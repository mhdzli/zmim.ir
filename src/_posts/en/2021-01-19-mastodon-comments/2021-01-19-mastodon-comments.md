---
title: Adding a Comments Section with Mastodon
date: 2021-01-19 14:20:00 +03:30
modified: 2023-10-30 00:28:19 +03:30
tags: [foss, mastodon, fediverse]
description: Adding a comments section using Mastodon (a free and decentralized social network).
image:
mastodon:
  host: mas.to
  username: mz
  id: 105582586560918183
lang: en
---


# Mastodon

[From Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Mastodon_(social_network)){:target="_blank"}{:rel="noopener noreferrer"}

[Mastodon](https://joinmastodon.org/){:target="_blank"}{:rel="noopener noreferrer"} is a free and open-source self-hosted social networking service that allows anyone to host their own server node in the network, with user bases distributed across different servers.

These servers are interconnected as a federated social network, allowing their users to interact seamlessly with one another. Mastodon is part of a [larger federation](https://fediverse.party/){:target="_blank"}{:rel="noopener noreferrer"} that enables users to interact with other free platforms such as [PeerTube](https://joinpeertube.org/){:target="_blank"}{:rel="noopener noreferrer"} and [Friendica](https://friendi.ca/){:target="_blank"}{:rel="noopener noreferrer"} that support the same protocols.

# Adding a Comments Section with Mastodon

One of the challenges with static websites is adding a comments section. There are various options for doing this. One simple solution is Disqus. However, Disqus was never an appealing option for me because it is heavy, loads a lot of unnecessary scripts on pages, and most importantly is not free software. For more alternatives, you can see [this post](https://mehdix.ir/static-comments.html){:target="_blank"}{:rel="noopener noreferrer"} by Mehdi Sadeghi.

Thanks to [‪@carl‬](https://linuxrocks.online/@carl){:target="_blank"}{:rel="noopener noreferrer"} for writing the code and the report in [this post](https://carlschwan.eu/2020/12/29/adding-comments-to-your-static-blog-with-mastodon/){:target="_blank"}{:rel="noopener noreferrer"}, you can now send me your comments using a Mastodon account, and view what others have written.

## JavaScript Code

Since the original code was written for [`#HUGO`](https://gohugo.io/){:target="_blank"}{:rel="noopener noreferrer"}, I made minor modifications to make it compatible with [`#JEKYLL`](https://jekyllrb.com/){:target="_blank"}{:rel="noopener noreferrer"} and placed it in the [`mastodon.html`](https://raw.githubusercontent.com/mhdzli/zmim.ir/master/src/_includes/mastodon.html){:target="_blank"}{:rel="noopener noreferrer"} file inside the [`_includes`](https://github.com/mhdzli/zmim.ir/tree/master/src/_includes){:target="_blank"}{:rel="noopener noreferrer"} folder:


<div class="code-block">
{% highlight html %}
{% raw %}
<div class="page-content">
  <h2>Comments</h2>
  <p>You can view the comments for this post on Mastodon and <a class="link" href="https://{{ page.mastodon.host }}/@{{ page.mastodon.username }}/{{ page.mastodon.id }}" target="_blank" rel="noopener noreferrer"> here</a>. To write your own comment, click the link below.</p>
  <p><a class="button" href="https://{{ page.mastodon.host }}/@{{ page.mastodon.username }}/{{ page.mastodon.id }}" target="_blank" rel="noopener noreferrer">Write a comment</a></p>
  <p></p>
  <p id="mastodon-comments-list"><a id="load-comment" class="button">View comments</a></p>
  <div id="comments-wrapper">
    <noscript><p>JavaScript must be enabled for this section, or view the <a href="https://{{ page.mastodon.host }}/@{{ page.mastodon.username }}/{{ page.mastodon.id }}" target="_blank" rel="noopener noreferrer">original post</a> on Mastodon</p></noscript>
  </div>
  <script src="/assets/js/purify.min.js"></script>
  <script type="text/javascript">
    function escapeHtml(unsafe) {
      return unsafe
        .replace(/&/g, "&amp;")
        .replace(/</g, "&lt;")
        .replace(/>/g, "&gt;")
        .replace(/"/g, "&quot;")
        .replace(/'/g, "&#039;");
    }

    function emojify(input, emojis) {
      let output = input;

      emojis.forEach(emoji => {
        let picture = document.createElement("picture");

        let source = document.createElement("source");
        source.setAttribute("srcset", escapeHtml(emoji.url));
        source.setAttribute("media", "(prefers-reduced-motion: no-preference)");

        let img = document.createElement("img");
        img.className = "emoji";
        img.setAttribute("src", escapeHtml(emoji.static_url));
        img.setAttribute("alt", `:${ emoji.shortcode }:`);
        img.setAttribute("title", `:${ emoji.shortcode }:`);
        img.setAttribute("width", "20");
        img.setAttribute("height", "20");

        picture.appendChild(source);
        picture.appendChild(img);

        output = output.replace(`:${ emoji.shortcode }:`, picture.outerHTML);
      });

      return output;
    }

    function loadcomments() {
      document.getElementById("load-comment").innerHTML = "Loading comments";
      fetch('https://{{ page.mastodon.host }}/api/v1/statuses/{{ page.mastodon.id }}/context')
        .then(function(response) {
          return response.json();
        })
        .then(function(data) {
          let descendants = data['descendants'];
          if(
            descendants &&
            Array.isArray(descendants) &&
            descendants.length > 0
          ) {
            document.getElementById('mastodon-comments-list').innerHTML = "";
            descendants.forEach(function(reply) {
              if( reply.account.display_name.length > 0 ) {
                reply.account.display_name = escapeHtml(reply.account.display_name);
                reply.account.display_name = emojify(reply.account.display_name, reply.account.emojis);
              } else {
                reply.account.display_name = reply.account.username;
              };

              reply.content = emojify(reply.content, reply.emojis);

              reply.created_at = new Date( reply.created_at ).toLocaleString('en-US', {
                dateStyle: "long",
                timeStyle: "short",
              });

              let instance = "";
              if( reply.account.acct.includes("@") ) {
                instance = reply.account.acct.split("@")[1];
              } else {
                instance = "{{ page.mastodon.host }}";
              }

              mastodonComment =
                `<div class="mastodon-comment">
                <div class="avatar">
                <img src="${escapeHtml(reply.account.avatar_static)}" height=60 width=60 alt="">
                </div>
                <div class="content">
                <div class="author" title="profile: @${ reply.account.username }@${ instance }">
                <a href="${reply.account.url}" target="_blank" rel="external nofollow">
                <span>${reply.account.display_name}</span>
                <span class="instance">${instance}</span>
                </a>
                </div>
                <div title="View comment on ${ instance }">
                <a class="date" href="${reply.uri}" target="_blank" rel="external nofollow">
                ${reply.created_at}
                </a>
                </div>
                <div class="mastodon-comment-content">${reply.content}</div>
                </div>
                </div>`;
              document.getElementById('mastodon-comments-list').appendChild(DOMPurify.sanitize(mastodonComment, {'RETURN_DOM_FRAGMENT': true}));
            });
          } else {
            document.getElementById('mastodon-comments-list').innerHTML = "<p>No comments yet</p>";
          }
        });
    }
    document.getElementById("load-comment").addEventListener("click", loadcomments);
  </script>
</div>
{% endraw %}
{% endhighlight %}
</div>

Then in the [post layout](https://raw.githubusercontent.com/mhdzli/zmim.ir/master/src/_layouts/post.html){:target="_blank"}{:rel="noopener noreferrer"}, I added a condition so that whenever `mastodon` is present in the post's [`frontmatter`](https://jekyllrb.com/docs/front-matter/){:target="_blank"}{:rel="noopener noreferrer"}, the comments section is included:

<div class="code-block">
{% highlight liquid %}
{% raw %}
{% if page.mastodon %}
  {% include mastodon.html %}
{% endif %}
{% endraw %}
{% endhighlight %}
</div>

And in the `frontmatter`, `mastodon` should be added with this structure:

<div class="code-block">
{% highlight yaml %}
{% raw %}
mastodon:
  host: mas.to
  username: mz
  id: 105582586560918183
{% endraw %}
{% endhighlight %}
</div>

Where the following must be specified:

- host: the Mastodon server address
- username: the Mastodon username
- id: the unique ID of the toot related to the post

Finally, I used [these few lines of `CSS`](https://github.com/mhdzli/zmim.ir/commit/78e351e5f809ead9eb4a77021026c5364c6e2081){:target="_blank"}{:rel="noopener noreferrer"} for better comment display.

You can try the final result here:
