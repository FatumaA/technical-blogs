---
title: "Add Analytics and Error Monitoring to Astro with PostHog"
datePublished: Thu Apr 10 2025 00:52:52 GMT+0000 (Coordinated Universal Time)
cuid: cm9an96br000409kz499tbmhj
slug: add-analytics-and-error-monitoring-to-astro-with-posthog
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1744245194071/0cad8634-3c35-4c43-87fd-be13dcd5b85b.png
tags: analytics, error-tracking, astro, web-analytics-tools, posthog

---

Nothing is quite as frustrating as finally releasing your app for testing or even into the wild and running into user complaints that you can’t quite replicate.

Maybe the issue was in the communication, or maybe it’s just a broken flow, but getting every detail out of a confused or even frustrated user is hard. That’s where analytics, error tracking, and even session replays come in.

Such services allow you to see what’s happening in your app and how your users interact, giving you valuable insights that can be used along with user reports to understand the situation.

I recently found myself in such a situation and started researching tools I could use. I was looking for easy-to-use options that were open-source and had a free tier. PostHog kept coming up, so I gave it a try.

Here is a quick write-up on PostHog, the services I used, and how to go about it.

## Pre-requisites

This blog will assume that you have a basic understanding of how the [Astro web framework](https://astro.build/) works.

## What is PostHog

[PostHog](https://posthog.com/) is an open-source product that offers observability, feature testing, and analytics tools. PostHog services cut across Product, Engineering, AI, Data, Marketing, and Support, and each vertical could be a product of its own.

I used Web Analytics and Error Reporting. Of course, as soon as you put in proper guardrails, there are no error events 😅.

PostHog has a [generous free tier](https://posthog.com/pricing) that offers up to a million free analytics events and 100,000 error incidents. Additionally, the product comes with [great docs and SDKs](https://posthog.com/docs/libraries) that cover many languages and JavaScript frameworks.

This makes it very simple to get started with. You can even add and use it in a local host environment.

## How to Add PostHog to Astro

To use PostHog in Astro, you need first to [sign up](https://us.posthog.com/) [and complete the onboarding process](https://us.posthog.com/) in PostHog.

Then, you need to install PostHog’s JavaScript library. You can do this by opening an Astro project and pasting this command into an integrated terminal: `npm i posthog-js`

Now, in the index file, paste the snippet that PostHog provided just above the closing `</head>` tag. It should look something like this, but with your specific data:

```xml
<script>
	import posthog from "posthog-js";

	posthog.init("<your_api_key>", {
		api_host: "<your_api_host_url>",
		person_profiles: "always",
	});
</script>
```

Ensure this snippet is in the page that wraps your whole app, including any protected and nested routes. For me, this was a main layout page.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">I set the person_profiles to always so that I can catch all activity on the site, not just logged-in sessions.</div>
</div>

The page should look something like this:

```xml
---
import Navbar from "../components/NavComponents/Navbar.astro";
import Footer from "../components/NavComponents/Footer.astro";
import { ViewTransitions } from "astro:transitions";

const { pageTitle = "bobi200 - Comics for life"} = Astro.props;

type Props = {
	pageTitle?: string;
};
---

<html lang="en">
	<head>
		<meta charset="utf-8" />
		<link rel="icon" type="image/svg+xml" href="/favicon.png" /><meta
			name="viewport"
			content="width=device-width"
		/>
		<meta name="generator" content={Astro.generator} />
		<title>
			{pageTitle}
		</title>
		<ViewTransitions />
		<script>
			import posthog from "posthog-js";

			posthog.init("<your_api_key>", {
				api_host: "<your_api_url>",
				person_profiles: "always",
			});
		</script>
	</head>

	<body class="bg-slate-950 text-white scroll-smooth">
		<Navbar />
		<slot />
		<Footer />
	</body>
</html>
```

Now run the app with: `npm run dev` and make some movements in your app. Click around, switch pages, and maybe even cause errors 😅.

Going back to your dashboard, you should see the analytics showing up. It contains information such as page views, how long you stayed on the page, and from which device you were accessing your site.

![Web analytics dashboard showing statistics for visitors, page views, and sessions over the last 24 hours. A line graph displays page views by hour. Metrics include session duration and bounce rate.](https://cdn.hashnode.com/res/hashnode/image/upload/v1744246082832/a226e62c-5d58-4d6d-a4ab-cbef1eabd8b0.gif align="center")

You can also see any error event in the Error Tracking tab.

That’s all it took to set up PostHog and start seeing some information immediately. I liked that it accepts localhost, so it’s easy to start testing it, and I can’t wait to see how much more useful it becomes over time.

## Notes and Resources

Here are some links to docs and resources I found useful:

* [What is the difference between web analytics and product analytics?](https://posthog.com/docs/web-analytics/faq)
    
* [How to set up Astro analytics, feature flags, and more](https://posthog.com/tutorials/astro-analytics)