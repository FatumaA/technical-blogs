---
title: "Create and Invite Users to your Admin App Using Supabase Edge Functions"
seoTitle: "RBAC Admin App User Invitations"
seoDescription: "Create and invite users efficiently with Supabase Edge Functions, assigning roles seamlessly in your admin app. Simplify user management"
datePublished: Sat May 27 2023 14:07:06 GMT+0000 (Coordinated Universal Time)
cuid: cli62fwx0000109l22hy0cynt
slug: create-and-invite-users-to-your-admin-app-using-supabase-edge-functions
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1713785076937/eb2ca41a-7ec9-45e9-a7a1-dc6ff4c1c17a.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1685197188081/1ce0dfc4-2e86-44ea-9c80-369072cfb49d.png
tags: postgres, flutter, supabase

---

In this blog, we will look at how to create and send invite emails to users while staying logged in to your application using [Supabase Edge Functions](https://supabase.com/docs/guides/functions). We will also see an approach to assigning roles to users in an admin application.

# Prerequisites

We will need a [Supabase](https://supabase.com/) project set up, our project keys and the [Supabase CLI](https://supabase.com/docs/guides/cli) installed. We will use Flutter for this and we will need the [Supabase-Flutter](https://pub.dev/packages/supabase_flutter) dependency installed.

# Prepare the Frontend

Run the following command in a directory of your choice to create a Flutter app and move into it:  
`flutter create name-of-your-app`

`cd name-of-your-app`

Add the supabase-flutter and [dotenv](https://pub.dev/packages/flutter_dotenv) dependencies in pubspec.yaml file. We also need the url\_strategy package for redirect to work properly.

Delete everything in main.dart file and paste the following:

```dart
import 'package:flutter/material.dart';
import 'package:flutter_dotenv/flutter_dotenv.dart';
import 'package:supabase_flutter/supabase_flutter.dart';

void main() async {
  await dotenv.load(fileName: ".env");
  await Supabase.initialize(
      url: dotenv.env['SUPABASE_URL']!,
      anonKey: dotenv.env['SUPABASE_ANON_KEY']!);
  runApp(const MyApp());
}

final supaClient = Supabase.instance.client;

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Example App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      routes: {
        '/': (context) => const MyHomePage(),
        '/new-user': (context) => const NewUser(),
      },
    );
  }
}
class MyHomePage extends StatefulWidget {
  const MyHomePage({super.key});

  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            if (supaClient.auth.currentSession == null)
              ElevatedButton(
                onPressed: () async {
                  await supaClient.auth.signUp(
                      email: 'your-mail@example.com'
                      password:'123456',
                  );
                  setState(() {});
                },
                child: const Text('Log In'),
              ),
            const SizedBox(
              height: 20,
            ),
            if (supaClient.auth.currentSession != null) ...[
              ElevatedButton(
                onPressed: () async {
                  await supaClient.auth.signOut();
                  setState(() {});
                },
                style: ElevatedButton.styleFrom(
                  backgroundColor: Colors.red,
                ),
                child: const Text('Log Out'),
              ),
              const SizedBox(
                height: 20,
              ),
              ElevatedButton(
                onPressed: () async {
                  final res = await supaClient.functions.invoke('createUser',
                      body: {'email': 'your-other-email@example.com'});
                },
                child: const Text('Invite Another User'),
              ),
            ]
          ],
        ),
      ),
    );
  }
}
```

Here we are simply creating a user via the application and only showing the logout and invite user buttons if we have a session from that user.

Create another file called new-user.dart and paste the following in it:

```dart
import 'package:flutter/material.dart';

class NewUser extends StatelessWidget {
  const NewUser({super.key});

  @override
  Widget build(BuildContext context) {
    return const Scaffold(
      body: Text(
        'hello',
        style: TextStyle(fontSize: 24),
        ),
    );
  }
}
```

This is the screen that your invited user will see when they click on the invite link in the email sent.

Type into the terminal `flutter run` and you should see the output on the screen.

# Create the Function

Open another terminal window and run the following commands sequentially to log in, initialize Supabase and link to your remote Supabase project:  
`supabase login`

`supabase init`

`supabase link --project-ref your-ref`

After which, run the following command to create a new function:  
`supabase functions new name-of-function`

The Supabase-specific variables such as the project URL and keys come already prepopulated and available.

Replace the contents in the functions/name-of-function/index.ts file with the following:

```typescript
// Follow this setup guide to integrate the Deno language server with your editor:
// https://deno.land/manual/getting_started/setup_your_environment
// This enables autocomplete, go to definition, etc.

import { serve } from "https://deno.land/std@0.131.0/http/server.ts";
import { createClient } from "https://esm.sh/@supabase/supabase-js";

console.log("Hello from Functions!");

const supaClient = createClient(
	Deno.env.get("SUPABASE_URL")!,
	Deno.env.get("SUPABASE_SERVICE_ROLE_KEY")!
);

const corsHeaders = {
	"Access-Control-Allow-Origin": "*",
	"Access-Control-Allow-Headers":
		"authorization, x-client-info, apikey, content-type",
	"Content-Type": "application/json",
};

serve(async (req) => {
	if (req.method === "OPTIONS") {
		return new Response("ok", { headers: corsHeaders });
	}

	// create a new user
	const { email } = await req.json();

	console.log(`Signing up new user init - ${JSON.stringify(req)}`);


	const { data, error } = await supaClient.auth.admin.createUser({
		email
	});

	console.log(`Response from sign up - ${JSON.stringify(data)}`);

	if (error) {
		return new Response(JSON.stringify(error), {
			headers: corsHeaders,
			status: 500,
		});
	}

	const { data: inviteUserData, error: inviteUserError } =
		await supaClient.auth.admin.inviteUserByEmail(email, {
			redirectTo: "http://localhost:3000/new-user",
		});

	if (inviteUserError) {
		return new Response(JSON.stringify(inviteUserError), {
			headers: corsHeaders,
			status: 500,
		});
	}

	return new Response(JSON.stringify(inviteUserData.user), {
		headers: corsHeaders,
		status: 200,
	});

});
```

In this code block, we are setting up our Supabase client with the service role key. This key should only be used in a server environment as it can ignore any [RLS policies](https://supabase.com/docs/guides/auth/row-level-security) you may have set up. It also allows us to use the admin auth functions that supabase-js provides.

We then handle CORS, extract the email from the incoming request, call the CreateUser method exposed by the Supabase SDK - passing the email, and handle the error case.

In the case that we successfully create the user, we call the invite user method and pass the email to it. This method sends the newly created user an invite email while handling the error case.

Deploy your function to the remote Supabase project by running the following command in the terminal:  
`supabase functions deploy name-of-function`

Run the flutter app, log in then call the function by clicking on the invite user button. You should see that the user is created in the Supabase Dashboard and is sent an email without logging you out of the application. When the user clicks the invite, they became confirmed and can access your application.

**Note:** If you need to use an external email service with edge functions you will need to either use an email API or use SMTP. If using SMTP, you have to use services that use ports other than 25, 465, & 587. I suggest [Postmark](https://postmarkapp.com/), they have both an API and a compatible SMTP service.

# Adding Roles to the User

What if you want to assign different roles to the users you are creating? For example, if your app has users who are customers, others who are agents and a couple of admins?

When you create users via Supabase auth, they get added to the auth.users table. This table is managed by Supabase and it is recommended that you create your own public tables for your users.

Ordinarily, you might create a public table called users and create a trigger on the auth.users table such that any new user in auth table gets copied over to your public users table. But in this case, we want different tables populated depending on what role the new user in the auth table has been assigned.

To achieve this, we will need to pass additional data about the user we are creating to the edge function we just deployed.

Edit the Invite User button in main.dart file to read like this:

```dart
ElevatedButton(
                onPressed: () async {
                  await supaClient.functions.invoke('createUser',
                      body: {'email': 'your-other-email@example.com', 'user_role': 'customer'});
                },
                child: const Text('Invite Another User'),
              ),
```

This adds the user\_role information we need. In the function file, extract the role as well the email like so:  
`const { email, user_role } = await req.json();`

At the point of creating the user, change the code to read like this:

```typescript
const { data, error } = await supaClient.auth.admin.createUser({
		email,
		user_metadata: {user_role}
	});
```

This will create a new user with their role as extra information tied to them.  
Deploy the function again with the changes.

Now in the Supabase dashboard, create the tables admin, customer and agent with the fields id and email. Follow the prompts to make the id field a foreign relation of the id field of the auth.users table. This means that every entry in the tables will have a corresponding entry in the auth table. We will then create a trigger that runs every time we create a new user. The trigger will check what the user\_role is and insert the userinfo into the correct table.

In the Supabase dashboard, go to the SQL editor, click on new query then paste the following into the editor:

```pgsql
CREATE OR REPLACE FUNCTION insert_user_data() RETURNS TRIGGER AS $$
BEGIN
    -- If user_role is 'agent', insert data into agent table
    IF NEW.raw_user_meta_data ->> 'user_role' = 'agent' THEN
        INSERT INTO public.agent (id, email)
        VALUES (NEW.id, NEW.email);
        
    -- If user_role is 'customer', insert data into customer table
    ELSIF NEW.raw_user_meta_data ->> 'user_role' = 'customer' THEN
        INSERT INTO public.customer (id, email)
        VALUES (NEW.id, NEW.email);
    
    -- If user_role is 'admin', insert data into admin table
    ELSIF NEW.raw_user_meta_data ->> 'user_role' = 'admin' THEN
        INSERT INTO public.admin (id, email)
        VALUES (NEW.id, NEW.email);
    END IF;
    
    RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

The code snippet above creates an insert\_user\_data function that returns a trigger that does the check based on user\_role every time a new record is inserted in the auth table.

Now, we can retest the application and we should have a record in the relevant table as well as the auth table. Our invited users should have also received an email that brings them back to our application.

# Notes:

If you need more security or to have the roles handled by the application layer instead of user metadata, this [resource](https://github.com/supabase-community/supabase-custom-claims) is very useful and detailed.