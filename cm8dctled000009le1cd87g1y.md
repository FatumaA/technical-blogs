---
title: "How to Add SSR Auth to Astro with Supabase and Astro Actions"
seoDescription: "Learn how to integrate server-side rendered authentication in Astro projects using Supabase and Astro actions for secure, scalable web applications"
datePublished: Mon Mar 17 2025 17:44:25 GMT+0000 (Coordinated Universal Time)
cuid: cm8dctled000009le1cd87g1y
slug: how-to-add-ssr-auth-to-with-supabase-and-astro
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1742233380842/89bbcdbb-a215-4f28-ab91-92c6aa9eb2e5.png
tags: authentication, supabase, astro

---

This blog will go through how to add authentication to a server-side rendered Astro project using actions and the Supabase SSR package.

## Prerequisites

This blog assumes that you are familiar with the following concepts:

* Web development frameworks
    
* [Basic authentication flows](https://www.freecodecamp.org/news/set-up-authentication-in-apps-with-supabase/)
    
* Basic Backend-as-a-Service (BaaS)
    

## What is Supabase?

[Supabase](https://supabase.com/docs) is an open-source Backend-as-a-Service that builds upon [Postgres](https://www.postgresql.org/docs/). It provides common key features such as authentication, real-time, edge functions, storage, and more.

Supabase offers a hosted version that makes building and scaling production-ready apps easy and a self-hostable version that gives users full control.

## What is Astro?

[Astro](https://docs.astro.build/en/getting-started/) is a UI-agnostic web framework. By default, it renders [server-first](https://docs.astro.build/en/concepts/why-astro/#server-first), but it [can be used with any UI framework](https://docs.astro.build/en/guides/integrations-guide/#official-integrations), including [Astro client components](https://docs.astro.build/en/guides/client-side-scripts/).

### What are Astro Actions?

[Astro actions](https://docs.astro.build/en/guides/actions/) allow you to write server-side functions that can be called without explicitly setting up API routes. Out of the box, they provide many useful utilities that simplify the process of running server logic.

Astro actions can be called from both client and server environments.

## Understanding SSR Authentication

Server-side rendered (SSR) auth refers to handling authentication on the server. It is typically a [cookie-based authentication method](https://www.freecodecamp.org/news/set-up-authentication-in-apps-with-supabase/#how-does-authentication-work).

The server creates a session and stores a session ID in a cookie that is sent to the client.

The client (in this case, the browser) receives the cookie and automatically includes it in future requests so the server can use it to determine if the user is authenticated.

Since browsers cannot modify HTTP-only cookies and servers cannot access local storage, SSR authentication needs careful management to prevent security risks such as session hijacking and stale sessions.

### SSR vs. SPA Authentication

On the other hand, Single-Page Applications (SPAs), like traditional React apps, handle authentication on the client side because they don’t have direct access to a server.

SPAs typically use JWTs, which are stored in local storage, cookies, or session storage. When the application needs to communicate with a server, it sends these tokens in the HTTP headers.

%[https://www.youtube.com/watch?v=HdE3dk8VkRU] 

## Set Up Supabase Backend

To start, you will need [a Supabase account](https://supabase.com/dashboard/). Then, follow the prompts to create a project. Go to the Authentication tab in the sidebar, click the Sign In / Up tab under Configuration, and enable user sign-ups. Then scroll down to Auth Providers and enable email.

![The image shows a configuration interface for user authentication. It includes options for user signups, such as enabling new user signups, manual linking, and anonymous sign-ins, with toggle switches. There is also a section for authentication providers, showing options like Email (enabled), Phone, and SAML 2.0 (both disabled).](https://cdn.hashnode.com/res/hashnode/image/upload/v1742054137964/a379192b-4eaf-491f-bcf4-a0e1e0deef94.png align="center")

## Set Up the Frontend

Next, you will need to create an Astro project. Open your preferred IDE or Text editor’s integrated terminal and run the following command to scaffold an Astro project in a folder named “ssr-auth.” Feel free to use any name you like.

`npm create astro@latest ssr-auth`

Follow the provided prompts and choose a basic template to start with. When it’s done, change into the folder, then run `npm install` to install dependencies, followed by `npm run dev` to start the server, and your site will be available at `localhost:4321`.

Set Astro to run in SSR mode by adding `output: "server",` to the `defineConfig` function found in the `astro.config.mjs` file at the root of the folder.

Next, [add an adapter](https://docs.astro.build/en/guides/integrations-guide/node/) to create a server runtime. For this, use the node.js adapter by running this command in a terminal: `npx astro add node`. This will add it and automatically make all relevant changes.

Finally, add Tailwind for styling. Run this command in a terminal window: `npx astro add tailwind`. Follow the prompts, and it will make any changes necessary.

At this stage, your `astro.config.mjs` should look like this:

```typescript
// @ts-check
import { defineConfig } from "astro/config";

import node from "@astrojs/node";

import tailwindcss from "@tailwindcss/vite";

// https://astro.build/config
export default defineConfig({
  output: "server",

  adapter: node({
    mode: "standalone",
  }),

  vite: {
    plugins: [tailwindcss()],
  },
});
```

## Set Up Supabase SSR

Open an integrated terminal window and paste this command to add both the supabase-js client library and the SSR auth package to the project:

`npm install @supabase/supabase-js @supabase/ssr`

Create an `.env` file in the root of the project and paste this into it.

```bash
SUPABASE_URL=<YOUR_URL>
SUPABASE_ANON_KEY=<YOUR_ANON_KEY>
```

Get the correct values from the Supabase dashboard:

![Screenshot of a "Connect to your project" interface showing options to get connection strings and environment variables using the Supabase-js library with the Astro framework. It demonstrates how to import Supabase credentials in a JavaScript file.](https://cdn.hashnode.com/res/hashnode/image/upload/v1742054292788/8aeec326-259c-49bd-a6f8-b885e9a9e6ea.png align="center")

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Note that in Astro, all environment variables to be accessed on the client side need to be prefixed by ‘PUBLIC,’ but in this case, we will be using Astro actions that run on the server, so we won’t need the prefix.</div>
</div>

Then, create a lib folder in the `src` folder and add a `supabase.ts` file. Paste this code into it:

```typescript
import { createServerClient, parseCookieHeader } from "@supabase/ssr";
import type { AstroCookies } from "astro";

export function createClient({
	request,
	cookies,
}: {
	request: Request;
	cookies: AstroCookies;
}) {
	return createServerClient(
		import.meta.env.SUPABASE_URL,
		import.meta.env.SUPABASE_ANON_KEY,
		{
			cookies: {
				getAll() {
					return parseCookieHeader(request.headers.get("Cookie") ?? "");
				},
				setAll(cookiesToSet) {
					cookiesToSet.forEach(({ name, value, options }) =>
						cookies.set(name, value, options)
					);
				},
			},
		}
	);
}
```

This sets up Supabase to handle [cookies in a server-rendered application](https://supabase.com/docs/guides/auth/server-side/creating-a-client?queryGroups=framework&framework=astro&queryGroups=environment&environment=astro-browser) and exports a function that takes in the request and cookies object. The function is set up like this because Astro has three ways to access request and cookie information:

* Through Astro’s global object, which is only available on Astro pages.
    
* Through `AstroAPIContext` object, which is only available in Astro actions.
    
* Through `APIContext` which is a subset of the global object and is available through API routes and middleware.
    

So the `createClient` function accepts the`request` and `cookies` objects separately to make it flexible and applicable in the different contexts in which it may need to be used.

Next, create a `middleware.ts` file in the `src` folder and paste this into it:

```typescript
import { defineMiddleware } from "astro:middleware";
import { createClient } from "./lib/supabase";

export const onRequest = defineMiddleware(async (context, next) => {
	const { pathname } = context.url;

	console.log("Middleware executing for path:", pathname);

	const supabase = createClient({
		request: context.request,
		cookies: context.cookies,
	});

	if (pathname === "/protected") {
		console.log("Checking auth for protected route");

		const { data } = await supabase.auth.getUser();

		// If no sess, redirect to index
		if (!data.user) {
			return context.redirect("/");
		}
	}

	return next();
});
```

This file checks to see if there is an active user when we are on the protected route. If there is no user, it redirects to the index page, and if there is an active user, it allows the request to continue as is.

Thus guarding the protected route from unauthenticated access.

## Set up the UI

Open the `pages` folder in the `src` and replace the contents of `index.astro` with:

```typescript
---
import Layout from "../layouts/Layout.astro";
import { createClient } from "../lib/supabase";
import "../styles/global.css";

const supabase = createClient({
	request: Astro.request,
	cookies: Astro.cookies,
});

const { data } = await supabase.auth.getUser();

if (data.user) {
	return Astro.redirect("/protected");
}
---

<Layout>
	<section class="flex flex-col items-center justify-center m-30">
		<h1 class="text-4xl text-left font-bold mb-12">Sign In to Your Account</h1>
		<form id="signin-form" class="flex flex-col gap-2 w-1/2">
			<label for="email" class="">Enter your email</label>
			<input
				type="email"
				name="email"
				id="email"
				placeholder="youremail@example.com"
				class="border border-gray-500 rounded-md p-2"
				required
			/>
			<button
				type="submit"
				id="sign-in"
				class="bg-gray-600 hover:bg-gray-700 p-2 rounded-md text-white font-bold w-full cursor-pointer disabled:bg-gray-500 disabled:hover:bg-gray-500 disabled:cursor-not-allowed"
				>Sign In</button
			>
		</form>
	</section>
</Layout>
```

Here, the frontmatter creates a supabase server client and then uses it to check if we have an active user. It redirects based on this information. This works because the front matter runs on the server side, and the project is set to server output.

The template displays a simple form with an email input. To complete it, add this below the closing `</Layout>` tag:

```typescript
<script>
	import { actions } from "astro:actions";

	const signInForm = document.querySelector("#signin-form") as HTMLFormElement;
	const formSubmitBtn = document.getElementById("sign-in") as HTMLButtonElement;

	signInForm?.addEventListener("submit", async (e) => {
		e.preventDefault();
		formSubmitBtn!.disabled = true;
		formSubmitBtn!.textContent = "Signing in...";

		try {
			const formData = new FormData(signInForm);

			const results = await actions.signIn(formData);

			if (!results.data?.success) {
				formSubmitBtn.disabled = false;
				formSubmitBtn.textContent = "Sign In";
				return alert("Oops! Could not sign in. Please try again");
			}

			formSubmitBtn.textContent = "Sign In";
			return alert("Please check your email to sign in");
		} catch (error) {
			formSubmitBtn.disabled = false;
			formSubmitBtn.textContent = "Sign In";
			console.log(error);
			return alert("Something went wrong. Please try again");
		}
	});
</script>
```

This adds some vanilla JavaScript that calls the `SignIn` action on form submit, provides user feedback through the alerts, and manages the button’s text and disabled state. This effectively adds client-side interactivity to the page.

Next, create a new `protected.astro` file in the `pages` folder and paste this into it:

```typescript
---
import Layout from "../layouts/Layout.astro";
import { createClient } from "../lib/supabase";
import "../styles/global.css";

const supabase = createClient({
	request: Astro.request,
	cookies: Astro.cookies,
});

const { data } = await supabase.auth.getUser();
---

<Layout>
	<section class="flex flex-col items-center justify-center m-30">
		<h1 class="text-4xl text-left font-bold mb-12">You are logged in!</h1>
		<p class="mb-6">Your user Id: {data.user?.id}</p>
		<button
			id="sign-out"
			class="bg-gray-600 hover:bg-gray-700 px-4 py-2 rounded-md text-white font-bold cursor-pointer disabled:bg-gray-500 disabled:hover:bg-gray-500 disabled:cursor-not-allowed"
			>Sign Out</button
		>
	</section>
</Layout>

<script>
	import { actions } from "astro:actions";
	const signOutBtn = document.getElementById("sign-out") as HTMLButtonElement;

	signOutBtn?.addEventListener("click", async (e) => {
		e.preventDefault();
		signOutBtn!.disabled = true;
		signOutBtn!.textContent = "Signing out...";

		try {
			const results = await actions.signOut();

			if (!results.data?.success) {
				signOutBtn!.disabled = false;
				signOutBtn!.textContent = "Sign Out";
				return alert("Oops! Could not sign Out. Please try again");
			}
			return window.location.reload();
		} catch (error) {
			signOutBtn.disabled = false;
			signOutBtn.textContent = "Sign Out";
			console.log(error);
			return alert("Something went wrong. Please try again");
		}
	});
</script>
```

This page gets the user data server-side in the frontmatter and displays it in the template along with a sign-out button.

The JavaScript in the `script` tags handle calling the sign-out action, user feedback, and button stat,e as in the `index.astro` page.

## Set Up Astro Actions

Finally, add `actions` folder in the `src` folder and create an `index.ts` file to hold our logic. Paste the following into it:

```typescript
import { defineAction, type ActionAPIContext } from "astro:actions";
import { z } from "astro:schema";
import { createClient } from "../lib/supabase";

const emailSignUp = async (
	{
		email,
	}: {
		email: string;
	},
	context: ActionAPIContext
) => {
	console.log("Sign up action");
	try {
		const supabase = createClient({
			request: context.request,
			cookies: context.cookies,
		});

		const { data, error } = await supabase.auth.signInWithOtp({
			email,
			options: {
				emailRedirectTo: "http://localhost:4321/api/exchange",
			},
		});

		if (error) {
			console.error("Sign up error", error);
			return {
				success: false,
				message: error.message,
			};
		} else {
			console.log("Sign up success", data);
			return {
				success: true,
				message: "Successfully logged in",
			};
		}
	} catch (err) {
		console.error("SignUp action other error", err);
		return {
			success: false,
			message: "Unexpected error",
		};
	}
};

export const server = {
	signIn: defineAction({
		accept: "form",
		input: z.object({
			email: z.string().email(),
		}),
		handler: async (input, context) => {
			return emailSignUp(input, context);
		},
	}),
	signOut: defineAction({
		handler: async (_, context) => {
			const supabase = createClient({
				request: context.request,
				cookies: context.cookies,
			});
			const { error } = await supabase.auth.signOut();
			if (error) {
				console.error("Sign out error", error);
				return {
					success: false,
					message: error.message,
				};
			}
			return {
				success: true,
				message: "Successfully signed out",
			};
		},
	}),
};
```

This action takes care of both the sign-in and the sign-out methods. A supabase server instance is created in the sign-in method, and the magic link method is used to sign in. It passes a redirect URL, which we have yet to create, and handles errors.

The sign-out method calls Supabase’s sign-out method and handles any potential errors.

The redirect URL refers to an API route that exchanges the code from the email Supabase sends for a session that Supabase handles.

In the `pages` folder, add an `api` folder and create within it `exchange.ts` file. Paste this into it:

```typescript
import type { APIRoute } from "astro";
import { createClient } from "../../lib/supabase";

export const GET: APIRoute = async ({ request, cookies, redirect }) => {
	const url = new URL(request.url);
	const code = url.searchParams.get("code");

	if (!code) {
		return redirect("/");
	}

	const supabase = createClient({ request, cookies });
	const { error } = await supabase.auth.exchangeCodeForSession(code);

	if (error) {
		console.error("Error exchanging code for session:", error);
		return redirect("/404");
	}

	return redirect("/protected");
};
```

This grabs the code from the URL in the magic link sent, creates a server client, and calls the `exchangeCodeForSession` method with the code. It handles any error by redirecting to Astro’s built-in not-found page.

Otherwise, it will redirect to the protected page as Supabase handles the session implementation details.

## Running the App

In an integrated terminal, run `npm run dev` to start the app, you should see this UI:

![Sign-in page with a prompt to enter an email address and a "Sign In" button.](https://cdn.hashnode.com/res/hashnode/image/upload/v1742053357711/1d6134ae-be08-4f21-b710-a0a23c85a199.png align="center")

If you try to access the `/protected` page will redirect you back to this view until you sign in. Now, sign in, and you should get an email with a link that will redirect you to the `/protected` page. This is what you should see:

![Message display confirming user login with a user ID, featuring a "Sign Out" button.](https://cdn.hashnode.com/res/hashnode/image/upload/v1742053707320/baad6bb8-a357-427c-98e6-bac6a0c5abc8.png align="center")

With that, the project now uses Supabase SSR and is set.

## Notes and Resources

Here are some useful resources on SSR, Astro and Supabase:

* [Supabase’s advanced guide to SSR](https://supabase.com/docs/guides/auth/server-side/advanced-guide)
    
* [Supabase SSR package](https://github.com/supabase/ssr)
    
* [Docs on Astro Cookies](https://docs.astro.build/en/reference/api-reference/#cookies)
    
* [Supabase Docs on PKCE](https://supabase.com/docs/guides/auth/sessions/pkce-flow)
    
* [Astro Docs on Actions](https://docs.astro.build/en/guides/actions/)
    
* [Link to full repository](https://github.com/FatumaA/supa-ssr)