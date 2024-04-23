---
title: "How to Add Search to Your Headless Hashnode Blog with Supabase Text Search and Next.js 14"
seoTitle: "Add Search functionality using Supabase"
seoDescription: "Learn how to integrate search functionality to your Hashnode blog using Supabase Text Search and Next.js. Full tutorial provided"
datePublished: Tue Apr 23 2024 20:50:18 GMT+0000 (Coordinated Universal Time)
cuid: clvcv18bm000408mjb3dt1v8m
slug: how-to-add-search-to-your-headless-hashnode-blog-with-supabase-text-search-and-nextjs-14
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1713912927437/b2189f85-e5dd-40d9-a3ba-b80ed161c84c.png

---

In this blog, you are going to get your blog posts from the Hashnode API (Application Programming Interface), pass them to a Supabase database then add the ability to do full text search against your saved posts.

## Prerequisites

You will need the following to better follow along with this blog:

* Basic programming skills
    
* [Basic understanding of browser and server](https://developer.mozilla.org/en-US/docs/Learn/Server-side/First_steps/Client-Server_overview)
    
* [Basic understanding of React and React components](https://react.dev/learn)
    
* [Basic understanding of APIs](https://www.freecodecamp.org/news/apis-for-beginners/)
    
* [A hashnode blog](https://hashnode.com/)
    
* [A supabase account](https://supabase.com/)
    
* A text editor like [VSCode](https://code.visualstudio.com/) or an IDE (Integrated Development Environment)
    

This blog assumes you have a blog set up and starts from there.

## Set up GraphQL Apollo Client and Hashnode Endpoint

In your `src` folder, create a folder called `queries` and create within it a file `index.ts`. Paste the following within it:

```typescript
import { ApolloClient, gql, HttpLink, InMemoryCache } from "@apollo/client";
import { registerApolloClient } from "@apollo/experimental-nextjs-app-support/rsc";

export const { getClient } = registerApolloClient(() => {
	return new ApolloClient({
		cache: new InMemoryCache(),
		link: new HttpLink({
			uri: "https://gql.hashnode.com",
		}),
	});
});

export const getBlogsQuery = gql`
	{
		publication(host: "your-url") {
			posts(first: 20) {
				edges {
					node {
						coverImage {
							url
						}
						title
						subtitle
						brief
						slug
						content {
							html
							markdown
						}
					}
				}
			}
		}
	}
`;
```

This sets up Apollo client to work with Next.js app router, defines the query to make and exports both.

Open an integrated terminal and run `npm install` to install the dependencies needed.

## Set up Supabase Config and Database

Create a Supabase project if you don't have one. Create a new database table give it a name. Add columns corresponding to the information gotten from hashnode API. In this case, add title and markdown as columns, they are all type text and leave Row Level Security off.

In your Next.js app, create a folder in `src` called `lib`, create two files within it to export a Supabase server and client instance. This is because Next.js app router is by default server-first but the search will be implemented client side.

The server instance file will look as below:

```typescript
import { createClient } from "@supabase/supabase-js";

export const supabaseServerClient = createClient(
	process.env.NEXT_PUBLIC_SUPABASE_URL!,
	process.env.SUPABASE_SERVICE_ROLE_KEY!
);
```

And the client instance will look as below:

```typescript
import { createClient } from "@supabase/supabase-js";

export const supabaseClient = createClient(
	process.env.NEXT_PUBLIC_SUPABASE_URL!,
	process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
);
```

The difference between them being that service key is exposed in the `SupabaseServerClient` instance and hidden in the client one. You will need to run `npm install` in an integrated terminal again to get the Supabase dependency.

Create an `.env` file at the root of the application and paste your credentials in it. You can get the values from the Supabase dashboard. Your `.env` file should look like this:

```bash
NEXT_PUBLIC_SUPABASE_URL=<your-supabase-project-public-url>
NEXT_PUBLIC_SUPABASE_ANON_KEY=<your-supabase-project-anon-key>
SUPABASE_SERVICE_ROLE_KEY=<your-supabase-project-service-key>
```

## Get and Display the Blogs

In the `app` folder, create a `page.tsx` file and scaffold a react component. This is what mine looks like:

```typescript
const Page = async () => {
	const {
		data: {
			publication: {
				posts: { edges },
			},
		},
	} = await getClient().query({ query: getBlogsQuery });

	console.log(edges[4].node.title);

	return (
		<div className="top-styles my-14">
			<h5 className="tagline"> BLOG POSTS </h5>
			<h1 className="headline mb-8">
				Thoughts on the tech I&apos;m using, learning and loving{" "}
			</h1>
			<div className="card-section w-8/12">
				{edges.map(({ node }: { node: IBlog }, index: number) => (
					<Card key={index} cardInfo={node} />
				))}
			</div>
		</div>
	);
};

export default Page;
```

This makes a request to the Hashnode endpoint, returns the data then the data is displayed in a Card component. Here is what my Card component looks:

```typescript
export interface IBlog {
	title: string;
	brief: string;
	slug: string;
	subtitle: string;
	coverImage: {
		url: string;
	};
	content: {
		html: string;
		markdown: string;
	};
}


function Card({
	cardInfo,
	isInDialog,
}: {
	cardInfo: IBlog;
	isInDialog?: boolean;
}) {
	return (
		<Link
			className="card flex flex-col mb-6 justify-between"
			href={`https://<Your-site-url-or-subdomain>/${cardInfo.slug}`}
			target="_blank"
		>
			{!isInDialog ? (
				<div className="h-48">
					<Image
						src={isInDialog ? "" : cardInfo!.coverImage?.url}
						alt="screenshot"
						width="200"
						height="200"
						className="h-full w-full object-cover rounded-md"
					/>
				</div>
			) : null}

			<h2 className="text-2xl my-4 text-gray-200 font-extrabold">
				{cardInfo!.title}
			</h2>
			<p className="mt-2 mb-4 overflow-auto">
				{cardInfo!.brief?.substring(0, 120) + "..."}
			</p>
			<span className="text-yellow-400 font-semibold hover:scale-110 self-end">
				Read More
			</span>
		</Link>
	);
}

export default Card;
```

Going back to the `page.tsx` file, paste this code below the getClient query, it will run on page load and populate the Supabase table.

```typescript
	} = await getClient().query({ query: getBlogsQuery });

    //paste here
	edges.forEach(async ({ node }: ) => {
		const {
			title,
			markdown
		} = node;

		const { data, error } = await supabaseServerClient
			.from("name of your table")
			.insert({ title, markdown });

		if (error) {
			console.error("Error inserting blog to supabase", error.hint);
			return;
		}

		console.log("Blog inserted to supabase", data);
		return data;
	});

    //code below remains the same
	console.log(edges[4].node.title);
```

This is a hacky way so remove it once the table is populated. You can also download your Hashnode blogs and upload them to Supabase directly, but they will need to be transformed into a csv or excel file first.

## Add Search Functionality

Now that the blogs are available in the Supabase table, create a components folder in the `src` folder and add a file called `search.tsx` to hold the search logic. Mark this file as a client component and paste the following into it:

```typescript
"use client";
import React, { FormEvent, useState } from "react";
import Card, { IBlog } from "./Card";
import Dialog from "./Dialog";
import { supabaseClient } from "@/lib/supabaseClient";

const Search = () => {
	const [searchTerm, setSearchTerm] = useState("");
	const [isSearching, setIsSearching] = useState(false);
	const [searchResults, setSearchResults] = useState<IBlog[]>([]);
	const [error, setError] = useState("");

	const handleSearch = async (event: FormEvent<HTMLFormElement>) => {
		event.preventDefault();

		if (!searchTerm) {
			setError("No search term entered");
			setTimeout(() => {
				setError("");
			}, 3000);
			return;
		}
		setIsSearching(true);

		// coz 2 or more words seem to be treated as an exact match
		const preparedDBSearchString = searchTerm.split(" ").join(" or ");

		console.log(
			"searching for ",
			searchTerm,
			"prepped search term",
			preparedDBSearchString
		);

		const { data, error } = await supabaseClient
			.from("hashnode_blogs")
			.select("*")
			.textSearch("title", preparedDBSearchString, {
				type: "websearch",
				config: "english",
			});

		const { data: mdData, error: mdError } = await supabaseClient
			.from("your sb table")
			.select("*")
			.textSearch("markdown", preparedDBSearchString, {
				type: "websearch",
				config: "english",
			});

		if (data?.length === 0 && mdData?.length === 0) {
			setSearchTerm("");
			setIsSearching(false);
			setError("No search results found");

			setTimeout(() => {
				setError("");
			}, 3000);
			return;
		}

		setSearchTerm("");

		console.log("COMBINED SEARCHH", [data, mdData]);
		setIsSearching(false);
		setSearchResults([...data!, ...mdData!]);
	};
	return (
		<div className="flex flex-col mb-8 ">
			<form onSubmit={handleSearch} className="flex gap-3">
				{searchResults.length > 0 && (
					<Dialog setSearchResults={setSearchResults}>
						{searchResults.map((res: IBlog, index: number) => (
							<Card key={index} cardInfo={res} isInDialog={true} />
						))}
					</Dialog>
				)}
				<input
					type="text"
					placeholder="search..."
					name="search"
					value={searchTerm}
					onChange={(e) => setSearchTerm(e.target.value)}
					className="rounded-sm p-2 bg-inherit w-96 border focus:border-brand-accent focus:outline-brand-accent focus:outline-none"
				/>
				<button type="submit" className="btn-primary my-0 w-32">
					{isSearching ? "Searching..." : "Search"}
				</button>
			</form>
			<span className=" text-red-300 font-medium mt-1">{error}</span>
		</div>
	);
};

export default Search; 
```

This creates a form with a search input, some local state to hold input value and gives the user some feedback. It also performs a full text search on the title and markdown columns against the user-generated search term. Finally, it merges the two results and sets the `searchResults` state.

Taking a closer look at the logic here, `const preparedDBSearchString = searchTerm.split(" ").join(" or ");`, the searchTerm was not sent directly to the `textSearch` method. It had to be prepared.

This is because many times users will insert multiple words and the text search default is to treat search strings as exact matches. This is not ideal, it is better if the text search treats the search string as a rough match. In this case, this is achieved by adding "or" between each word of the search string then passing the result of this to the text search function.

```typescript
		// coz 2 or more words seem to be treated as an exact match
		const preparedDBSearchString = searchTerm.split(" ").join(" or ");

		const { data, error } = await supabaseClient
			.from("hashnode_blogs")
			.select("*")
			.textSearch("title", preparedDBSearchString, {
				type: "websearch",
				config: "english",
			});

		const { data: mdData, error: mdError } = await supabaseClient
			.from("your sb table")
			.select("*")
			.textSearch("markdown", preparedDBSearchString, {
				type: "websearch",
				config: "english",
			});
```

The search component opens a dialog when it has search results, you will need to add a new file in components called `dialog.tsx` and paste the following in it:

```typescript
import { AiFillCloseCircle } from "react-icons/ai";
import { ReactNode, useState } from "react";
import { IBlog } from "./Card";

interface DialogProps {
	setSearchResults?: (blogs: IBlog[]) => void;
	children: ReactNode;
}

function Dialog({ setSearchResults, children }: DialogProps) {
	const [isOpen, setIsOpen] = useState(true);

	const closeModal = () => {
		if (setSearchResults) setSearchResults([]);
		setIsOpen(false);
	};
	return (
		<dialog
			open={isOpen}
			id="modal"
			className={`${
				isOpen ? "opacity-100" : "opacity-0 pointer-events-none"
			}  flex flex-col items-center justify-center bg-inherit transition-opacity duration-300 ease-in-out fixed inset-0 backdrop-filter backdrop-blur-md backdrop-brightness-50 w-4/6 border border-container rounded-md max-h-[80vh] text-white p-4`}
		>
			<button
				onClick={closeModal}
				className="flex flex-row gap-2 items-center justify-center ml-auto font-medium hover:text-red-300 my-4"
			>
				<span>Close</span>
				<AiFillCloseCircle height={25} />
			</button>
			<div className="max-h-[80vh] overflow-auto w-11/12 mb-6 ">{children}</div>
		</dialog>
	);
}

export default Dialog;
```

This dialog takes a setter function and children as props, handles its open/close state and displays some markup.

Going back to the `page.tsx` filein the app folder, add the `<Search/>` component right below the `h1` and above the `div` like so:

```typescript
	Thoughts on the tech I&apos;m using, learning and loving{" "}
			</h1>
			<Search />

			<div className="card-section w-8/12">
				{edges.map(({ node }: { node: IBlog }, index: number) => (
```

You now ready to test out the search functionality. This is how the implementation looks on [my site](https://hijabicoder.dev/):

![A screenshot showing a small text "Blog posts" and a heading about the blog. And a search bar with a button](https://cdn.hashnode.com/res/hashnode/image/upload/v1713910560413/d033aee5-740c-49bd-9ba4-004bc91ea6a2.png align="center")

![A screenshot of a dialog with some cards. The cards have blog titles, briefs and a Read More text](https://cdn.hashnode.com/res/hashnode/image/upload/v1713910584813/d4b45946-aa29-410d-9293-08ddb43629b7.png align="center")

## Notes on Semantic Search and Hybrid Search

While this blog went through the process of adding search functionality using [Supabase full text search](https://supabase.com/docs/guides/ai/keyword-search), there are two other types of search that can be implemented using Supabase.

The first is semantic search, the quickest way to get started with this is via AI (Artificial Intelligence) models. This has been simplified in Supabase launch week 11 and you can learn more about it [here](https://www.youtube.com/watch?v=w4Rr_1whU-U).

The second way is through hybrid search. Essentially, this leverages both full text search and semantic search. It offers the best of both worlds and you can read more about it here.

## Additional Resources

Here are some resources that are useful:

* [Supabase docs on search](https://supabase.com/docs/guides/ai/semantic-search)
    
* [Hashnode guide on headless hashnode](https://support.hashnode.com/en/articles/8478295-getting-started-with-headless-hashnode-starter-kit-guide)
    
* [Next.js Learn - App router](https://nextjs.org/docs/app)
    

**Disclaimer:** *I'm yet to completely move to headless hashnode. At the time of writing, clicking on my blog posts will redirect back to a subdomain.*