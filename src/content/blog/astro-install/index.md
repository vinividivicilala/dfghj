---
title: 'Building With Astro: Part 1'
description: 'New Tech, new site'
pubDate: 'Jun 01 2024'
# heroImage: '/blog-placeholder-3.jpg'
---

Install to writing in less than 5 minutes. I'm sitting with my 4 year-old while she watches Across The Spiderverse on a Saturday morning, so I'm sure it could have been faster.

I decided to use the blog template option Astro offers out of the box to have more to see and play with. The README.md looks nicely setup for jumping into the sandbox but should be replaced. I want to keep the content as is to be able to reference back to some of the claims and expectations it has set. [The content can be found here](./original-readme).

This was the first "task" I tackled since install, and I have to say it was quite easy to understand what to do and how to do it. The task at hand was having a blog post with its own set of sub-paths for supporting content. Astro's directory structure made this an easy process.

From the blog template, the configuration allowed for me to understand I can:

1. Create a directory as the blog post holder
2. Add `index.md` for the content of the main post
3. Create supporting file `original-readme.md` to create the path `/astro-install/original-readme`
    > Note: This showed me the content still needs to be preceeded by front-matter, even if the content of the page is "utility" in nature. Makes sense to me.
4. Create a link to this content with markdown pointed to `(./original-readme)`
5. It works!

Let's deploy as is. I already have a [Netlify](https://www.netlify.com/) account, so that's where I'm going to send this thing. For the sake of building in public, this first deploy will only have this article and a few updates of personal information attached to it. Feedback on the deploy process and any gotchas from the out-of-the-box state to follow.

### After First Deploy

Well, that was pretty seamless. Point Netlify to the correct branch of the project on GitHub, and you're essentially done. 

Things I noticed afterwards on Astro:

- /blog defaults to earliest post
- /nested/directories/ in blogs show on the main blog listing page

These are not too surprising, but good to be aware of.

For the first, in `pages/blog/index.astro`, it's a simple swap of the sort function they've already provided:

```js
const posts = (await getCollection('blog')).sort(
	(a, b) =>  b.data.pubDate.valueOf() - a.data.pubDate.valueOf()
);
```

Originally, this was `a - b`, a swap to `b - a` changes the rendering order.

For the second part, this is most likely related to Astro's `collections`, so I'll start [looking for this in the docs](https://docs.astro.build/en/guides/content-collections/#organizing-with-subdirectories).

After reading this, I thought my original excitement for organizing posts with subdirectories may have been misplaced. However, digging into this a bit more led me down a path of updating the blog frontmatter with an optional prop I've called `onIndex` set as a boolean. Back to the sort function, I've added a filter for this prop:

```js
const posts = (await getCollection('blog', ({ data }) => {
  return data.onIndex !== false;
})).sort(
	(a, b) =>  b.data.pubDate.valueOf() - a.data.pubDate.valueOf()
);
```

As long as `onIndex` is not explicitly set to `false`, then the correct level of blog posts are rendered. I'd like a solution that does not involve a manual task such as this, but problem solved for the time being.

> I suspect there's likely a way to have one collection reference another, likely filter a collection based on a piece of front-matter that connects the two. I'll revisit this. If this requires a manual effort, I'd rather it be a forced part of the process to reduce the likelihood of mistakenly missing it vs a *prettier* solution with a higher-probability of forgetting.

### Get To The Point - Content On The Homepage

I want blog posts on the homepage - this should be straight-forward and a good way to test out the component feature of Astro. The current `pages/blog/index.astro` already has some markup for post layouts, let's grab that and build a component.

*Moments later*
Well, that was pretty easy. A new component is created, `<BlogLink />`, and the content found within each list-item was moved here. The final product being this, after a decent copy+paste:

```html
---
import '../styles/global.css';
import FormattedDate from './FormattedDate.astro';

interface Props {
	title: string;
    slug: string;
	date: Date;
	image?: string;
}

const { title, description, slug, date, image = '/blog-placeholder-1.jpg' } = Astro.props;
---
<li>
    <a href={`/blog/${slug}/`}>
        <img width={720} height={360} src={image} alt="" />
        <h4 class="title">{title}</h4>
        <p class="date">
            <FormattedDate date={date} />
        </p>
    </a>
</li>

<style>
    li {
        width: calc(50% - 1rem);
    }
    li * {
        text-decoration: none;
        transition: 0.2s ease;
    }
    li:first-child {
        width: 100%;
        margin-bottom: 1rem;
        text-align: center;
    }
    li:first-child img {
        width: 100%;
    }
    li:first-child .title {
        font-size: 2.369rem;
    }
    li img {
        margin-bottom: 0.5rem;
        border-radius: 12px;
    }
    li a {
        display: block;
    }
    .title {
        margin: 0;
        color: rgb(var(--black));
        line-height: 1;
    }
    .date {
        margin: 0;
        color: rgb(var(--gray));
    }
    li a:hover h4,
    li a:hover .date {
        color: rgb(var(--accent));
    }
    a:hover img {
        box-shadow: var(--box-shadow);
    }
    @media (max-width: 720px) {
        li {
            width: 100%;
            text-align: center;
        }
        li:first-child {
            margin-bottom: 0;
        }
        li:first-child .title {
            font-size: 1.563em;
        }
    }
</style>
```
It's going to take some getting used to, but I do like the concept of a foundational, global set of `css` rules combined with local scoped rules. This feels like it can have some strong potential in managing conflicts, but I wonder how this paradigm will fit with mental models of `@layer` rules that are shipping in most major browsers now. Feel like we're about to see a lot of circa-2010 css articles coming out to help people wrap their heads around the practice of organizing stylesheets.

## Conclusion: Use The Basics

For getting up and running fast, Astro is fantastic. Feels like this is an indicator of the maturity the web industry has reached. If I want to roll some complex, dynamic component system, I can. If I want to keep other pieces simple and core web technology, I can. Having the ability to utilize the pieces of the spectrum from simple to complex, together, is a wonderful experience to jump into.

This will be a series of articles talking about my early experience with Astro. I do not intend for this website to always focus on its skeletal makeup, but for right now this is helpful and hopefully interesting for some of you.

Next up, I will either experiment with the API abilities of Astro or jump into styling and seeing how we can break our brains with the control (or restrictions) of scoped styles.