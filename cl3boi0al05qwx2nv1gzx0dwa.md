---
title: "How To Contribute To Open Source - Supabase Edition"
datePublished: Wed May 18 2022 14:27:51 GMT+0000 (Coordinated Universal Time)
cuid: cl3boi0al05qwx2nv1gzx0dwa
slug: how-to-contribute-to-open-source-supabase-edition
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1652692254704/V9yEwqOjp.png
tags: opensource

---

# What is Open Source 
[Open Source](https://opensource.org/osd) refers to software that is available to the public. This software can be used, modified and extended as needed. This software normally runs under permissive licenses such as MIT and Apache among others. 
Popular software projects that are open source include [Flutter](https://github.com/flutter#:~:text=Welcome%20to%20the%20Flutter%20organization,from%20all%20around%20the%20world.) and of course [Supabase](https://github.com/supabase).

# Why Contribute To Open Source
Open source software allow for developers to practise their skill set while helping out with projects they find interesting. Open source contributions also show up in your public Github activity and can be a fun way to keep things green while learning. 

Open source contributions are a great indicator to potential clients or employers that you are proactive, willing to put yourself in uncomfortable situations in order to learn and that you understand how to collaborate on a non trivial project. 

All this means that contributing to open source is worth your time and will add to your perceived value as a developer. 

# What Is Supabase
[Supabase](https://supabase.com/) is a Backend as a Service that is often considered to be the open source [Firebase](https://firebase.google.com/) alternative. Supabase is a service that is built on top of [Postgres](https://www.postgresql.org/). Postgres is a SQL based database that is open source, secure and has been around for decades. 

Supabase also provides authentication, storage, realtime capabilities, edge functions, an auto generating api and [client libraries](https://github.com/supabase-community) for your favourite language. This means that you can access your Supabase instance either via the api directly or via the client libraries.

# Why Contribute to Supabase
Supabase is open source, is built around open source tooling and actively contributes to the open source community. This means that you can help build Supabase!

Supabase has detailed [documentation](https://supabase.com/docs) that helps make the process simpler. The Supabase team and [community]( http://discord.supabase.com) is also very helpful and welcoming. 

# How To Set Up For Contributing
Supabase's [github](https://github.com/supabase) has quite a number of repositories as it is a service that wraps around other open source tools and services. For this purpose, we will focus on the [main](https://github.com/supabase/supabase) Supabase repository. 

Within this repository, there are three main applications. These are: 

- The documentation website
- The landing page website
- The dashboard that exposes a visual interface for the services Supabase offers

We will first focus on how to get the studio up and running as it is what is exposed via self hosting. Then we will look at how to get the documentation set up. You may notice an apps and a packages folder in the repository, this is because Supabase is currently being migrated to a Monorepo architecture. 

##  Supabase Dashboard

We will need [Docker](https://www.docker.com/) installed as well the [Supabase CLI](https://github.com/supabase/cli). Supabase has a managed platform that you can log into via the CLI that allows for local development while connected to the remote instance. However, we will work with a local instance of Supabase instead.


### 1. Get Supabase Code
Get the code by running: 

```
git clone --depth 1 https://github.com/supabase/supabase
```

### 2. Get Supabase Services Up
Have Docker desktop running then open your cloned Supabase project in a code editor. Open the integrated terminal window, change directory into the docker folder and run the following commands to copy the .env file and to start the services via docker compose:

```
cp .env.example .env
docker-compose up
``` 
**Note:** The env files have example secrets to start off with. This is fine only for local development and contributing. [Read this guide](https://supabase.com/docs/guides/hosting/docker#securing-your-setup) on how to prepare your setup for deployment.

This will serve the studio on localhost:3000. You can interact with this instance via client applications, api or the UI now available on localhost:3000, just as you could with the hosted version. 

**Note:** The self hosted version does not have all the functionality that is available on the hosted platform.

### 3. Get Studio Up
Open another terminal window and change directory into studio. Then run the following commands to get all dependencies and run a server:


```
npm i
npm run dev
``` 
This should spin up the studio website on localhost:8082. This website is interacting with the services you spun up in step 2 above, essentially acting as the client application. Any changes you make in the code within the studio folder will reflect on localhost:8082. 

##  Supabase Documentation
We will need [JQ](https://stedolan.github.io/jq/), a tool that makes manipulating JSON data easy, installed in order to be able to modify the documentation. If using a mac, simply open a terminal window and paste the following in order to install jq:

```
brew install jq
``` 
Otherwise, the website linked above has the necessary installation instructions. 


### 1. Set The Documentation Up
Open the Supabase project you cloned in a code editor and open the integrated terminal. Change directory to the spec folder in the web folder. Then run the following commands in the terminal window:

```
npm install //to get dependencies
make //to construct the documentation
``` 
Now you can edit the .yml files in the spec directory to edit the documentation. When ready, run the following command (while in the spec folder in the terminal) to generate the relevant pages:

```
npm run gen:supabase
```
You can now check for the changes you've made by running this in the terminal:

```
npm run serve
``` 
Once you are done with your changes, be sure to check if your changes build by running this in the terminal before submitting a PR with your changes:

```
npm run build
``` 
And now we are ready to start contributing to both Supabase studio and the documentation 🎉. Or just build a fun project with Supabase and submit it to the examples folder 🙃.

# How To Navigate And Find Beginner Issues
The next part is to look for issues that are beginner friendly. On Supabase's github, you can search in the issues tab for issues that are tagged as 'good first issue', 'dashboard' or 'documentation' as we have gone through the process of setting them up. 

You can also go to the projects tab and click on the Contributors project. There are several issues organised in an easy to follow manner that the Supabase team is leaving for contributors to help out with.

# Conclusion
That is how you can set up and contribute to the Supabase project. Feel free to reach out in the [community]( http://discord.supabase.com) if you need any help. As always PRs are welcome 😌.


