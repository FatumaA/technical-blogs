---
title: "Build An Image Gallery With Flutter Web And Supabase Storage"
datePublished: Tue Apr 25 2023 19:57:10 GMT+0000 (Coordinated Universal Time)
cuid: clgwouufi000s09l29yd9cmmt
slug: build-an-image-gallery-with-flutter-web-and-supabase-storage
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1682516382970/b9e05ea5-6f3f-4033-a429-f30b88aea8d5.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1682452609256/06a983f1-e854-4670-800c-d3c2775bdea3.png
tags: flutter, storage, flutter-web, flutter-examples, supabase

---

# What is Supabase Storage?

[Supabase storage](https://supabase.com/docs/guides/storage) is a service offered by Supabase that allows for easy management of files. It wraps up Amazon's S3 service and integrates well with the other Supabase services even while remaining modular. You can access this service via the provided API, the client libraries and by self-hosting it directly.

Supabase storage has gotten a few major upgrades in the last Supabase [launch week](https://supabase.com/launch-week) including an increase in accepted file size and the introduction of [resumable uploads](https://supabase.com/blog/storage-v3-resumable-uploads). This makes it a great choice for most use cases.

In this blog post, we will be looking at how to use the service within a Flutter web application.

# Prerequisites

These are what you will need before we start:

* The Flutter [SDK](https://docs.flutter.dev/get-started/install) installed
    
* A [Supabase](https://app.supabase.com/sign-in) project set up
    

# Prepping Supabase

Once you have set up a Supabase project, you will need to click on the bucket icon to set up storage. You will be prompted to create a new bucket. Toggle it to public when creating it for this example. This means that we can read from our bucket without authorization.

![]( align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1682447796178/88ff7b9d-49ab-48a9-8253-b3c37f4a8062.png align="center")

Next, click on the Policies tab on the side of the Supabase dashboard. Click on the "new policy" button next to the text that reads "Other policies under storage.objects". Choose the pre-done policies. Enable insert and delete for authenticated users. Then enable the select policy for all users. This means we can read from the bucket and if we are logged in, we can also add and delete a file.

Once that is done, grab the project's URL and anon key under settings then project settings then the API tab on the side of the dashboard. We will need these to connect to our Supabase instance from our Flutter web application.

# Prepping Our Flutter Web Application

Open an IDE of your choice - I will be using VSCode. Open a directory where you are comfortable with having the application. For example, you can use open your desktop folder within the IDE.  
Open the integrated terminal and run the following command:  
`flutter create name_of_your_project`

This will create a folder on your Desktop with the specified name that will hold your Flutter app. Run the following commands to move into the folder and run your app. Then follow the prompts to run it in Chrome:  
`cd name_of_your_project`  
`flutter run`

Open your pubspec.yaml file then add the following lines under dependencies: Beware of YAML indentation errors.

```yaml
supabase_auth_ui: ^0.1.0+2
file_picker: ^5.2.9
```

We will need [supabase\_auth\_ui](https://pub.dev/packages/supabase_auth_ui) to spin up auth fast. It is a library that provides auth widgets for Supabase and Flutter projects out of the box. It depends on [supabase\_flutter](https://pub.dev/packages/supabase_flutter), so no need to add it separately. We will also need the [file\_picker](https://pub.dev/packages/file_picker) to enable us to get files from our system.

**Note:** You may have dependency issues if you use the latest file\_picker. In that case, downgrade to the version shown above.

# Setting Up Our Image Gallery

Replace the contents in the main function in your main.dart file with the following code:

```dart
void main() async {
  await Supabase.initialize(
    url: 'your_url',
    anonKey:
        'your_anonkey',
  );

  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.pink,
      ),
      home: const SplashScreen(),
    );
  }
}
```

This initializes Supabase, links it to our remote Supabase project and enables us to use Supabase services within our application.

Create a folder called screens within the lib folder, then create SplashScreen.dart file with the following code:

```dart
import 'package:flutter/material.dart';
import 'package:image_gallery/constants.dart';
import 'package:image_gallery/screens/HomeScreen.dart';
import 'package:image_gallery/screens/LoginScreen.dart';

class SplashScreen extends StatefulWidget {
  const SplashScreen({super.key});

  @override
  State<SplashScreen> createState() => _SplashScreenState();
}

class _SplashScreenState extends State<SplashScreen> {
  @override
  void initState() {
    super.initState();
    _redirect();
  }

  Future<void> _redirect() async {
    await Future.delayed(Duration.zero);

    final session = supaClient.auth.currentSession;
    if (session == null) {
      Navigator.of(context)
          .pushAndRemoveUntil(LogInScreen.route(), (route) => false);
    } else {
      Navigator.of(context)
          .pushAndRemoveUntil(HomeScreen.route(), (route) => false);
    }
  }

  @override
  Widget build(BuildContext context) {
    return const Center(
      child: CircularProgressIndicator(),
    );
  }
}
```

The splash screen checks if we have a session and are therefore logged in. It then redirects as necessary.  
Create another file in screens called LoginScreen.dart. Paste the following in that file:

```dart
import 'package:flutter/material.dart';
import 'package:image_gallery/screens/HomeScreen.dart';
import 'package:supabase_auth_ui/supabase_auth_ui.dart';

class LogInScreen extends StatefulWidget {
  const LogInScreen({super.key});

  static Route<void> route() {
    return MaterialPageRoute(
      builder: (context) => const LogInScreen(),
    );
  }

  @override
  State<LogInScreen> createState() => _LogInScreenState();
}

class _LogInScreenState extends State<LogInScreen> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        margin: const EdgeInsets.all(20.0),
        child: SupaMagicAuth(onSuccess: (session) {
          Navigator.of(context).pushAndRemoveUntil(
            HomeScreen.route(),
            (route) => false,
          );
        }),
      ),
    );
  }
}
```

In this screen, we are using SupaMagicAuth to sign in with Supabase magic link. This is a widget predefined in the supabase\_auth\_ui library.

Next, create HomeScreen.dart file in the screens folder and paste the following code:

```dart
import 'package:file_picker/_internal/file_picker_web.dart';
import 'package:file_picker/file_picker.dart';
import 'package:flutter/material.dart';
import 'package:image_gallery/constants.dart';
import 'package:image_gallery/screens/LoginScreen.dart';

class HomeScreen extends StatefulWidget {
  const HomeScreen({super.key});

  static Route<void> route() {
    return MaterialPageRoute(
      builder: (context) => const HomeScreen(),
    );
  }

  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  final List<PlatformFile> _attachments = [];

  getPics() async {
    final List<Object> supaPaths = [];
    final pics = await supaClient.storage.from('images').list();

    if (pics.isEmpty) return;

    Future.wait(
      pics.map(
        (p) async {
          final res = supaClient.storage.from('images').getPublicUrl(p.name);
          supaPaths.add({"path": res, "name": p.name});
        },
      ),
    );
    return supaPaths;
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Padding(
        padding: const EdgeInsets.fromLTRB(100.0, 40.0, 100.0, 20.0),
        child: Column(
          children: [
            const Text(
              'SupaImages',
              style: TextStyle(
                fontSize: 50,
                fontWeight: FontWeight.bold,
              ),
            ),
            const SizedBox(height: 20),
            const Text(
              'Manage your images with Supabase Storage',
              style: TextStyle(
                  fontSize: 20,
                  fontWeight: FontWeight.w700,
                  wordSpacing: 3,
                  letterSpacing: 3),
            ),
            const SizedBox(height: 60),
            ButtonBar(
              alignment: MainAxisAlignment.spaceBetween,
              children: [
                OutlinedButton.icon(
                  icon: const Icon(
                    Icons.image,
                    size: 15,
                  ),
                  onPressed: () async {
                    final result = await FilePickerWeb.platform.pickFiles(
                      allowMultiple: true,
                      type: FileType.custom,
                      allowedExtensions: ['jpg', 'png'],
                    );

                    if (result == null) {
                      print("No file selected");
                    } else {
                      try {
                        result.files.map(
                          (file) {
                            _attachments.add(file);
                          },
                        ).toList();

                        await Future.wait(
                          _attachments.map(
                            (attch) async {
                              final fileBytes = attch.bytes;
                              final fileName = attch.name;

                              await supaClient.storage
                                  .from('images')
                                  .uploadBinary(
                                    fileName,
                                    fileBytes!,
                                  );
                            },
                          ),
                        );
                        setState(() {});
                      } catch (e) {
                        print(e);
                        ScaffoldMessenger.of(context).showSnackBar(
                          SnackBar(
                            content:
                                Text('Error uploading file: ${e.toString()}'),
                          ),
                        );
                      }
                    }
                  },
                  style: ButtonStyle(
                    side: MaterialStateProperty.all(
                      const BorderSide(
                        color: Colors.pink,
                        width: 1,
                      ),
                    ),
                  ),
                  label: const Text(
                    "Upload Images",
                  ),
                ),
                ElevatedButton(
                    onPressed: () async {
                      await supaClient.auth.signOut();
                      Navigator.of(context).pushAndRemoveUntil(
                          LogInScreen.route(), (route) => false);
                    },
                    child: const Text('Logout'))
              ],
            ),
            const SizedBox(height: 10),
            Expanded(
              child: FutureBuilder(
                future: getPics(),
                builder: (BuildContext context, AsyncSnapshot snapshot) {
                  if (snapshot.hasData) {
                    final picInfo = snapshot.data! as List;
                    return GridView.count(
                      crossAxisCount: 4,
                      mainAxisSpacing: 5,
                      crossAxisSpacing: 5,
                      children: [
                        ...picInfo.map(
                          (pInfo) => Card(
                            child: Column(
                              children: [
                                Expanded(
                                  child: Padding(
                                    padding: const EdgeInsets.all(10),
                                    child: Image.network(
                                      pInfo['path'],
                                      width: 150,
                                      height: 150,
                                    ),
                                  ),
                                ),
                                Expanded(
                                  child: TextButton.icon(
                                      onPressed: () async {
                                        await supaClient.storage
                                            .from('images')
                                            .remove([pInfo['name']]);
                                        setState(() {});
                                      },
                                      icon: const Icon(
                                        Icons.delete,
                                        size: 15,
                                      ),
                                      label: const Text('Delete')),
                                )
                              ],
                            ),
                          ),
                        ),
                      ],
                    );
                  } else if (snapshot.hasError) {
                    return Center(child: Text('Error: ${snapshot.error}'));
                  } else if (!snapshot.hasData) {
                    return const Center(child: Text('No images uploaded yet'));
                  } else {
                    return const Center(child: CircularProgressIndicator());
                  }
                },
              ),
            )
          ],
        ),
      ),
    );
  }
}
```

This screen carries the logic of interacting with Supabase Storage and displaying images from our bucket on the page.

Finally, create constants.dart file in the root of the lib folder and paste this:

```dart
import 'package:supabase_auth_ui/supabase_auth_ui.dart';

final supaClient = Supabase.instance.client;
```

Now, when you run this project you should see this screen:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1682450146498/8cd183ad-2ab9-4d09-93a1-c5e02dae4d0b.png align="center")

Enter an email address then check your email for the magic link. Clicking it will direct you back to the application where you will see this screen:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1682450254148/4e5f8c31-6fe3-4052-85ae-03feda201721.png align="center")

Clicking on the upload image button will pop up the picker and you can choose multiple files to upload. The picker will only allow you to upload .png and .jpg files as specified in this part of the code:

```dart
onPressed: () async {
                    final result = await FilePickerWeb.platform.pickFiles(
                      allowMultiple: true,
                      type: FileType.custom,
                      allowedExtensions: ['jpg', 'png'],
                    );
```

To successfully upload files from the web using Flutter, you will need to use the uploadBinary() method exposed via Supabase SDK as shown here:

```dart
await supaClient.storage
                .from('images')
                .uploadBinary(fileName,fileBytes!);
```

To delete the uploaded images, simply click on the delete button below each image. And we have an application that reads, uploads and deletes from Supabase storage.

**Note:** When looping through results from the line of code below, we have to return from the function if it is empty as it will contain some internal placeholder that will display a placeholder widget by default.

```dart
final pics = await supaClient.storage.from('images').list();
```

The full code can be found [here](https://github.com/FatumaA/image_gallery).

# **Limitations:**

Responsiveness can be made better and the URL does not keep up state of the changing routes.