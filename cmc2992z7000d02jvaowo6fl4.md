---
title: "How to Protect Auth Forms with Cloudflare, Supabase SSR, and Astro Actions"
seoDescription: "Secure your app's authentication forms from bots using Cloudflare Turnstile with Supabase SSR and Astro Actions"
datePublished: Wed Jun 18 2025 17:57:50 GMT+0000 (Coordinated Universal Time)
cuid: cmc2992z7000d02jvaowo6fl4
slug: how-to-protect-auth-forms-with-cloudflare-supabase-ssr-and-astro-actions
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1750269244668/ee3ae9e2-7feb-488d-b528-235e6f4f769e.png
tags: cloudflare, security, supabase, astro, turnstile, cloudflare-turnstile, supabase-auth, supabase-ssr, astro-actions

---

When building authentication into your app, consider how to secure it from bots and malicious actors. This blog discusses how to do that using [Cloudflare Turnstile](https://www.cloudflare.com/en-gb/application-services/products/turnstile/) in a [Supabase](https://supabase.com/) and [Astro](https://astro.build/) project.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">This blog builds upon this&nbsp;<a target="_blank" rel="noopener noreferrer nofollow" href="https://blog.hijabicoder.dev/how-to-add-ssr-auth-to-with-supabase-and-astro" style="pointer-events: none">tutorial, which covers the necessary setup and the auth form</a>. You can <a target="_self" rel="noopener noreferrer nofollow" href="https://github.com/FatumaA/supa-ssr" style="pointer-events: none">check the code here</a>, the new changes added will be available in the <code>add cloudflare</code> branch.</div>
</div>

## Prerequisites

This blog assumes that you:

* are following from [this tutorial](https://blog.hijabicoder.dev/how-to-add-ssr-auth-to-with-supabase-and-astro)
    
* are familiar with basic web development concepts
    
* understand basic [authentication concepts](https://www.freecodecamp.org/news/set-up-authentication-in-apps-with-supabase/)
    
* understand Backend-as-a-Service (BaaS) concepts
    

## What is Cloudflare Turnstile?

Turnstile is [Cloudflare’s replacement for CAPTCHAs](https://www.cloudflare.com/en-gb/application-services/products/turnstile/), which are visual puzzles you must solve to differentiate between genuine users and bots.

CAPTCHAs are visually clunky, annoying, and [sometimes really hard to solve](https://blog.cloudflare.com/turnstile-ga/). They reduce the quality of the user experience and are [generally inaccessible](https://www.a11y-collective.com/blog/accessible-captcha/).

In comparison, Turnstile does the same job of detecting malicious activity without requiring users to solve puzzles. And Turnstile looks prettier, in my opinion 😅.

## What is Supabase?

[Supabase](https://supabase.com/docs) is an open-source Backend-as-a-Service that builds upon Postgres. It provides common key features such as authentication, real-time, edge functions, storage, and more.

Supabase offers a hosted version that makes building and scaling production-ready apps supa easy and a self-hostable version that gives users full control.

## What is Astro?

[Astro](https://docs.astro.build/en/getting-started/) is a UI-agnostic web framework. It renders [server-first by default](https://docs.astro.build/en/concepts/why-astro/#server-first) and [can be used with any UI framework](https://docs.astro.build/en/guides/integrations-guide/#official-integrations), including [Astro client components](https://docs.astro.build/en/guides/client-side-scripts/).

## Why Should You Protect Auth Forms?

Authentication protects sensitive resources from unauthorized access, making them the primary target that bots and malicious actors attempt to breach. Therefore, you should take extra precautions to deter them from successfully compromising your application.

## Setting Up Supabase

To get started, you will need [a Supabase account](https://supabase.com/dashboard/). Then, follow the prompts to create a project. Go to the Authentication tab in the sidebar, click the Sign In / Up tab under Configuration, and enable user sign-ups.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Please use <a target="_self" rel="noopener noreferrer nofollow" href="https://github.com/FatumaA/supa-ssr" style="pointer-events: none">this code as the base</a>, as this blog assumes you already have the forms and the project set up with authentication.</div>
</div>

Next, go to the Auth Protection tab under the Configuration on the same page, turn on Captcha protection, and choose Cloudflare as the provider.

Next, you will need to set up a Cloudflare account to get Cloudflare credentials.

## Setting Up Cloudflare Turnstile

[Log in or register a Cloudflare account](https://dash.cloudflare.com/login), click the Turnstile tab in the sidebar, and then click the “Add widget” button.

Next, name your widget, add “localhost” as the hostname, and leave all other settings as is. Then, create it. Cloudflare will then show you your site key and secret key.

![This is a screenshot of the "Add Widget" page for Cloudflare's Turnstile feature. The interface allows users to name the widget, manage hostnames, and choose a widget mode from "Managed," "Non-interactive," or "Invisible." There is also an option to opt for pre-clearance for the site. A graphic of a document and code snippet is displayed in the center. Buttons for adding hostnames, canceling, and creating are located at the bottom.](https://cdn.hashnode.com/res/hashnode/image/upload/v1750260766060/95ec02e5-8ee7-4438-a66c-76866ec068c1.png align="center")

Copy the secret key and add it to the Supabase dashboard under the Auth protection section.

![The image shows a webpage interface for "Attack Protection" settings. It includes options for "Bot and Abuse Protection," with a toggle switch for enabling captcha protection, a dropdown to choose the captcha provider (set to "Turnstile by Cloudflare"), and a field for entering a captcha secret. A "Save changes" button is visible at the bottom.](https://cdn.hashnode.com/res/hashnode/image/upload/v1750260776990/56ef5fc1-3321-45f0-ab9a-878679a08e88.png align="center")

## Connect the Frontend

In your project’s `.env` file, add the cloudflare site key provided from the step above like so:

```bash
TURNSTILE_SITE_KEY=<YOUR_TURNSTILE_SITE_KEY>
```

Then in the `Layout.astro` file, add this script just above the closing `</head>` tag to initiate Cloudflare’s turnstile:

```typescript
	<script
			src="https://challenges.cloudflare.com/turnstile/v0/api.js"
			async
			defer></script>
```

You can read more [about Cloudflare Turnstile](https://developers.cloudflare.com/turnstile/get-started/) to understand the flow better.

### Updating the index page

Then update the `index.astro` page to include this div right above the submit button:

```typescript
<div class="cf-turnstile" data-sitekey={apiKey}></div>
```

Now add this line within the frontmatter at the top of the same page:

```typescript
const apiKey = import.meta.env.TURNSTILE_SITE_KEY;
```

Finally, adjust the script tag to account for the turnstile logic. It should look like this:

```typescript
<script>
	import { actions } from "astro:actions";

	declare global {
		interface Window {
			turnstile?: {
				reset: () => void;
			};
		}
	}

	const signInForm = document.querySelector("#signin-form") as HTMLFormElement;
	const formSubmitBtn = document.getElementById("sign-in") as HTMLButtonElement;

	signInForm?.addEventListener("submit", async (e) => {
		e.preventDefault();
		formSubmitBtn.disabled = true;
		formSubmitBtn.textContent = "Signing in...";

		try {
			const turnstileToken = (
				document.querySelector(
					"[name='cf-turnstile-response']"
				) as HTMLInputElement
			)?.value;

			if (!turnstileToken) {
				throw new Error("verification_missing");
			}

			const formData = new FormData(signInForm);
			formData.append("captchaToken", turnstileToken);

			const results = await actions.signIn(formData);

			if (!results.data?.success) {
				if (results.data?.message?.includes("captcha protection")) {
					alert("Verification failed. Please try again.");
					if (window.turnstile) {
						window.turnstile.reset();
					}
					formSubmitBtn.disabled = false;
					formSubmitBtn.textContent = "Sign In";
					return;
				} else {
					alert("Oops! Could not sign in. Please try again");
					formSubmitBtn.disabled = false;
					formSubmitBtn.textContent = "Sign In";
					return;
				}
			}

			formSubmitBtn.textContent = "Sign In";
			alert("Please check your email to sign in");
		} catch (error) {
			if (window.turnstile) {
				window.turnstile.reset();
			}
			formSubmitBtn.disabled = false;
			formSubmitBtn.textContent = "Sign In";
			console.log(error);
			alert("Something went wrong. Please try again");
		}
	});
</script>
```

After waiting for the token and handling potential errors, the most important part for this new flow to work is to then pass the token to the astro action that handles the sign-in. This allows Supabase to perform token verification on our behalf, eliminating the need to call [Cloudflare’s verify APIs](https://developers.cloudflare.com/turnstile/get-started/server-side-validation/) directly.  
We do that in this part of the code:

```typescript
const formData = new FormData(signInForm);
			formData.append("captchaToken", turnstileToken);

			const results = await actions.signIn(formData);
```

### Adjusting the Auth Astro Action

Now, we need to adjust the `emailSignUp` action in the `index.ts` file in the `actions` folder. The action now needs to accept the Turnstile token and pass it back to the Supabase method.

Replace the signIn function definition with this:

```typescript
	signIn: defineAction({
		accept: "form",
		input: z.object({
			email: z.string().email(),
			captchaToken: z.string(),
		}),
		handler: async (input, context) => {
			return emailSignUp(input, context);
		},
	}),
```

The only change here is adding the captchaToken to the expected input object. Then adjust the `emailSignUp` action to include the captcha. In the end, it should look like this:

```typescript
const emailSignUp = async (
	{
		email,
		captchaToken,
	}: {
		email: string;
		captchaToken: string;
	},
	context: ActionAPIContext
) => {
	console.log("Sign up action");
	try {
		const supabase = createClient({
			request: context.request,
			cookies: context.cookies,
		});

		console.log("Request cookies:", context.request.headers.get("Cookie"));

		const { data, error } = await supabase.auth.signInWithOtp({
			email,
			options: {
				captchaToken,
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
```

### Testing the Turnstile

All that is left is to verify that everything still works as expected and that the Turnstile works. Open an integrated terminal and run: `npm run dev`, then open the provided localhost URL.

You should see the [same screen as before](https://blog.hijabicoder.dev/how-to-add-ssr-auth-to-with-supabase-and-astro), but with the Turnstile addition:

![Sign in page with a field for email input. A success message with a checkmark is displayed below, followed by a "Sign In" button.](https://cdn.hashnode.com/res/hashnode/image/upload/v1750267075336/66ad5f39-67c6-458a-96ea-4dfe1123b015.png align="center")

You should see that all works by attempting to sign in. You should receive an email and be correctly redirected to the `protected` page.

![Text reads: "You are logged in!" with a field labeled "Your user Id" and a "Sign Out" button below.](https://cdn.hashnode.com/res/hashnode/image/upload/v1750335131827/f85cde2f-f9bb-46b0-a09e-6ae6456cd49f.png align="center")

With that, you have successfully protected your auth form in an Astro project using Supabase auth and Cloudflare’s Turnstile.

## Notes and Resources

Here are some useful resources on Cloudflare’s Turnstile, Astro actions, and Supabase auth:

* [Get started with Turnstile](https://developers.cloudflare.com/turnstile/get-started/)
    
* [Supabase SSR guide for Astro](https://supabase.com/docs/guides/auth/server-side/creating-a-client?queryGroups=framework&framework=astro&queryGroups=environment&environment=astro-server)
    
* [Astro actions docs](https://docs.astro.build/en/guides/actions/)
    
* [Updated full code branch](https://github.com/FatumaA/supa-ssr/tree/add-cloudflare)