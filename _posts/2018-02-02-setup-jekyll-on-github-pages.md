---
title: How to setup a Jekyll powered GitHub Pages blog
categories: jekyll
tags: jekyll github github-pages
---

I finally got around to creating a Jekyll powered blog running on GitHub pages, and it was much 
easier than I thought. There are only a few steps involved:

1. Set up a GitHub repo called `<username>.github.io` (in my case `ndench.github.io`), this makes 
    github automatically serve the repo at `https://<username>.github.io`.<br />
    Note: Make sure you have set up an 
    [ssh key](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/) on 
    your github account.
2. [Install Ruby](https://www.ruby-lang.org/en/downloads/).
3. Install Jekyll and Bundler:
    ```bash
    $ gem install jekyll bundler
    ```
4. Create a Jekyll project locally:
    ```bash
    $ jekyll new <username>.github.io
    ```
5. Set up your local git project:
    ```bash
    $ cd <username>.github.io
    $ git init
    # Note: You can get this url from GitHub on your new repo page
    $ git remote add origin git@github.com:<username>/<username>.github.io.git
    ```
6. Build your blog locally then view it at [localhost:4000](http://localhost:4000):
    ```bash
    $ bundle exec jekyll serve
    ```
7. Commit and push your blog
    ```bash
    $ git add .
    $ git commit -m "Create my blog"
    $ git push -u origin master
    ```

8. View your blog at `https://<username.github.io>` (this might take 10 minutes or so to become 
    available)


## Understading Jekyll ##

Now that I had my blog live, I was confused by what to do next. All the tutorials I read said that 
I should have this sort of folder structure:

```
_includes/
_layouts/
_posts/
_sass/
assets/
script/
```

But all I could see was `_posts/`. It turns out that in a recent update Jekyll added gem powered 
themes. So you no longer have to manage your own includes, layouts and other assets. By default
Jekyll uses the `minima` theme, you can see those missing files on the 
[GitHub page for minima](https://github.com/jekyll/minima).

So literally the only thing left to do is create some posts. Create a file in the `_posts` 
directory with this naming convention `YYYY-MM-DD-<title>.md`, for instance, this post is called
`2018-02-01-setup-jekyll-on-github-pages.md`, add the following to the very start of the file:
```yaml
---
title: <title>
layout: post
categories: <category1> <category2>
---
```
Note: You can add as many space separate categories as you like, they appear at the start of the
URL for the post, for example, this post has the `jekyll` tag, which you can see in the URL 
`ndench.github.io/jekyll/setup-jekyll-on-github-pages`.


## Common configuration options ##

The `_config.yml` file specifies configuration for how Jekyll will build the site, plus also some
variable that the pages can use when they're being built. My `_config` looks like:
```yaml
title: My fully sick title
#email: your-email@example.com # Don't show my email address
description: >
  My fully sick description
baseurl: "" # Don't have any base path on my url. ie. /blog
url: "https://ndench.github.io" 
google_analytics: UA-000000000-0
timezone: Australia/Brisbane
show_excerpts: true # Show excerpts of posts on the home page
permalink: /:categories/:title # Don't show the date of the post in the URL

# Social
twitter_username: nathandench
github_username: ndench
linkedin_username: nathandench

# Build settings
markdown: kramdown
theme: minima
plugins:
  - jekyll-feed
  - jekyll-sitemap # I added this to automatiacally generate a sitemap.xml

# These are default variables for my pages
# Any file in the `_posts` directory gets a default layout of `post`, 
# so I don't have to specify it manually
defaults:
    - scope:
        path: _posts
      values:
        layout: post
```

## Update

Sometimes I want to be able to write a post that I can show someone for
proofreading, but not have it available "live". After looking around I found a
great way to implement drafs. The first way is to just add a new markdown file
under `/_drafts` as shown in the [jekyll docs](https://jekyllrb.com/docs/drafts/).
The problem with this is that it's only visible locally when you run 
`jekyll build` or `jekyll serve` with the `--drafts` option, so it's not 
visible live.

[This post](https://ben.balter.com/2015/02/20/jekyll-collections/) gives a 
great overviwe of Jekyll collections, which I modified to display drafts:

```yaml
# /_config.yml
...

defaults:
...
 - scope:
      path: _drafts
    values:
      layout: post
      comments: false

collections:
  drafts:
    output: true

...
```

This makes all markdown files under `/_drafts` be build without the `--drafts`
flag, gives them the default layout of `post`, and makes them accessible via 
the `site.drafts` variable. I then created a `/drafts.md` file, which is almost
a copy-paste of the minima theme's `/_layouts/home.html`:

{% raw %}
```html
---
layout: default
---

<div class="home">
  <h1 class="page-heading">Drafts</h1>

  {%- if site.drafts.size > 0 -%}
    <ul class="post-list">
      {%- for post in site.drafts -%}
      <li>
        <h3>
          <a class="post-link" href="{{ post.url | relative_url }}">
            {{ post.title | escape }}
          </a>
        </h3>
      </li>
      {%- endfor -%}
    </ul>
  {%- endif -%}

</div>
```
{% endraw %}

And now I have a `/drafts` page that lists all my drafts to make them easy to
find, but the `/drafts` page is not linked to from any other page, so readers
won't just stumble across it. Unless of course they've read this blog post ;).


## Update II

Some friends have been bugging me to add an email subscription to my blog.
After digging around it turns out to be super easy with Mailchimp, all
I needed to do was:

* Sign up for a free mailchimp account
* Create a 'list' (this will likely happen automatically during the sign up process)
* Go to my 'list' and click 'signup forms' -> 'embedded forms'
* Customise the form how I wanted
* Overwrite `/_includes/footer.html` from my theme and paste in the given HTML

I also customised the thank you page:
* Go to my 'list' and click 'signup forms' -> 'form builder'
* Choose 'confirmation thank you page'
* Customise however much you want


## Pages that helped me ##

* [Jekyll quickstart guide](https://jekyllrb.com/docs/quickstart/)
* [GitHub guide to setting up GitHub pages with Jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/)
* [Jekylly configuration options](https://jekyllrb.com/docs/configuration/)
* [Jekyll front matter configuration](https://jekyllrb.com/docs/frontmatter/)
* [Newbie guide to setting up Jekyll and GitHub pages](http://jmcglone.com/guides/github-pages/)
* [Jekyll themes on GitHub](https://help.github.com/articles/about-jekyll-themes-on-github/)
* [Jekyll drafts](https://jekyllrb.com/docs/drafts/)
* [Jekyll collections](https://ben.balter.com/2015/02/20/jekyll-collections/)
* [Explayn like I'm five: Jekyll collections](https://ben.balter.com/2015/02/20/jekyll-collections/)
* [Jekyll subscribe form](https://blog.webjeda.com/jekyll-subscribe-form/)
* [Add mailchimp subscribe form to Jekyll blog](http://www.controlfd.com/2016/05/16/add-a-mailchimp-subscriber-form-to-your-jekyll-blog.html)
* [Mailchimp custom thank you page](https://kb.mailchimp.com/lists/signup-forms/design-and-host-your-own-thank-you-pages)
