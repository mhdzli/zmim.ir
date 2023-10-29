---
title: افزودن بخش دیدگاه‌ها با ماستودون
date: 2021-01-19 14:20:00 +03:30
modified: 2023-10-30 00:28:19 +03:30
tags: [foss, mastodon, fediverse]
description: افزودن بخش دیدگاه‌ها با مسنادون (یک شبکه اجتماعی آزاد و گسترده).
image:
mastodon:
  host: mas.to
  username: mz
  id: 105582586560918183
---


# ماستودون

[از ویکی‌پدیا، دانشنامه آزاد](https://fa.wikipedia.org/wiki/%D9%85%D8%A7%D8%B3%D8%AA%D9%88%D8%AF%D9%88%D9%86_(%D9%86%D8%B1%D9%85%E2%80%8C%D8%A7%D9%81%D8%B2%D8%A7%D8%B1)){:target="_blank"}{:rel="noopener noreferrer"}

[ماستودون (به انگلیسی: Mastodon)](https://joinmastodon.org/){:target="_blank"}{:rel="noopener noreferrer"} یک نرم‌افزار آزاد و متن‌باز خودمیزبان (خدمات وب) برای ساخت شبکهٔ اجتماعی است که این امکان را به هر شخص می‌دهد تا گره سرور خود را در شبکه میزبانی کند و پایگاه‌های مختلف از کاربران آن در میان سروهای متفاوتی پخش هستند.

این سرورها به صورت شبکه اجتماعی فدرال به هم متصل هستند، که به کاربران خود اجازه می‌دهد با یکدیگر به شکل یکپارچه تعامل داشته باشند. ماستودون عضوی از یک [فدراسیون بزرگ‌تر](https://fediverse.party/){:target="_blank"}{:rel="noopener noreferrer"} است که به کاربران امکان می‌دهد با دیگر سکوهای آزاد مانند [پیرتیوب](https://joinpeertube.org/){:target="_blank"}{:rel="noopener noreferrer"} و [فرندیکا](https://friendi.ca/){:target="_blank"}{:rel="noopener noreferrer"} که قوانین یکسانی را پشتیبانی می‌کنند تعامل داشته باشند. 

# افزودن بخش دیدگاه‌ها با ماستودون

یکی از دشواری‌های وبسایت‌ها استاتیک افزودن بخش دیدگاه‌هاست. گزینه‌های گوناگونی برای انجام این کار وجود دارد. یک راهکار ساده استفاده از Disqus است. چون Disqus  سنگین است و مقدار زیادی اسکریپت غیرضروری در صفحات بارگزاری می‌کند و مهم‌تر از همه آزاد نیست هرگز گزینه مطلوبی برای من نبود. برای دیدن راهکارهای بیشتر می‌توانید [این پست](https://mehdix.ir/static-comments.html){:target="_blank"}{:rel="noopener noreferrer"} مهدی صادقی را ببینید.

با سپاس از [‪@carl‬](https://linuxrocks.online/@carl){:target="_blank"}{:rel="noopener noreferrer"} برای نوشتن کد و گزارشی که در [این پست](https://carlschwan.eu/2020/12/29/adding-comments-to-your-static-blog-with-mastodon/){:target="_blank"}{:rel="noopener noreferrer"} نوشته است، از این پس شما می‌توانید با داشتن یک اکانت ماستودون دیدگاه‌های خودتان را برای من بفرستید، و آنچه دیگران نوشته‌اند را هم ببینید.

## کد جاوا اسکریپت

چون کد اصلی برای [`#HUGO`](https://gohugo.io/){:target="_blank"}{:rel="noopener noreferrer"} نوشته شده بود، من تغییرات اندکی در آن دادم تا با [`#JEKYLL`](https://jekyllrb.com/){:target="_blank"}{:rel="noopener noreferrer"} سازگار شود و آن را در فایل [`mastodon.html`](https://raw.githubusercontent.com/mhdzli/zmim.ir/master/src/_includes/mastodon.html){:target="_blank"}{:rel="noopener noreferrer"} در پوشه [`_includes`](https://github.com/mhdzli/zmim.ir/tree/master/src/_includes){:target="_blank"}{:rel="noopener noreferrer"} قراد دادم:


<div class="code-block">
{% highlight html %}
{% raw %}
<div class="page-content">
  <h2>دیدگاه‌ها</h2>
  <p>می‌توانید دیدگاه‌های این پست را در ماستودون و<a class="link" href="https://{{ page.mastodon.host }}/@{{ page.mastodon.username }}/{{ page.mastodon.id }}" target="_blank" rel="noopener noreferrer"> اینجا </a>ببینید. برای نوشتن دیدگاه خود روی لینک زیر کلیک کنید.</p>
  <p><a class="button" href="https://{{ page.mastodon.host }}/@{{ page.mastodon.username }}/{{ page.mastodon.id }}" target="_blank" rel="noopener noreferrer">نوشتن دیدگاه‌</a></p>
  <p></p>
  <p id="mastodon-comments-list"><a id="load-comment" class="button">دیدن دیدگاه‌ها</a></p>
  <div id="comments-wrapper">
    <noscript><p>برای این بخش می‌باید جاوا اسکریپت را فعال کنید، یا <a href="https://{{ page.mastodon.host }}/@{{ page.mastodon.username }}/{{ page.mastodon.id }}" target="_blank" rel="noopener noreferrer">پست اصلی</a> را در ماستودون ببینید</p></noscript>
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
      document.getElementById("load-comment").innerHTML = "دریافت دیدگاه‌ها";
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

              reply.created_at = new Date( reply.created_at ).toLocaleString('fa-IR', {
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
                <div title="دیدن دیدگاه در ${ instance }">
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
            document.getElementById('mastodon-comments-list').innerHTML = "<p>بدون دیدگاه</p>";
          }
        });
    }
    document.getElementById("load-comment").addEventListener("click", loadcomments);
  </script>
</div>
{% endraw %}
{% endhighlight %}
</div>

سپس در [`layout` مربوط به پست‌ها](https://raw.githubusercontent.com/mhdzli/zmim.ir/master/src/_layouts/post.html){:target="_blank"}{:rel="noopener noreferrer"} یک شرط افزودم تا هر زمان که در [`frontmatter`](https://jekyllrb.com/docs/front-matter/){:target="_blank"}{:rel="noopener noreferrer"} پست ‍`mastodon` وجود داشت، بخش دیدگاه‌ها افزوده شود:

<div class="code-block">
{% highlight liquid %}
{% raw %}
{% if page.mastodon %}
  {% include mastodon.html %}
{% endif %}
{% endraw %}
{% endhighlight %}
</div>

و در `frontmatter` باید `mastodon` با این ساختار افزوده شود:

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

که باید این موارد در آن مشخص گردد:

- host: آدرس سرور ماستودون
- username: نام کاربری در ماستودون
- id: شماره یکتای بوق مربوط به پست

در پایان هم برای نمایش بهتر دیدگاه‌ها از [این چند خط `CSS`](https://github.com/mhdzli/zmim.ir/commit/78e351e5f809ead9eb4a77021026c5364c6e2081){:target="_blank"}{:rel="noopener noreferrer"} استفاده کردم.

اینجا می‌توانید نتیجه نهایی را آزمایش کنید:
