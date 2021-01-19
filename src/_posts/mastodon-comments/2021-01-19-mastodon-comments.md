---
title: افزودن بخش دیدگاه‌ها با مستادون
date: 2021-01-19 14:20:00 +03:30
tags: [foss, mastodon, fediverse]
description: افزودن بخش دیدگاه‌ها با مسنادون (یک شبکه اجتماعی آزاد و گسترده).
image:
mastodon:
  host: mas.to
  username: mz
  id: 105582586560918183
---


# مستادون

[از ویکی‌پدیا، دانشنامه آزاد](https://fa.wikipedia.org/wiki/%D9%85%D8%A7%D8%B3%D8%AA%D9%88%D8%AF%D9%88%D9%86_(%D9%86%D8%B1%D9%85%E2%80%8C%D8%A7%D9%81%D8%B2%D8%A7%D8%B1))

[مستادون (به انگلیسی: Mastodon)](https://joinmastodon.org/) یک نرم‌افزار آزاد و متن‌باز خودمیزبان (خدمات وب) برای ساخت شبکهٔ اجتماعی است که این امکان را به هر شخص می‌دهد تا گره سرور خود را در شبکه میزبانی کند و پایگاه‌های مختلف از کاربران آن در میان سروهای متفاوتی پخش هستند.

این سرورها به صورت شبکه اجتماعی فدرال به هم متصل هستند، که به کاربران خود اجازه می‌دهد با یکدیگر به شکل یکپارچه تعامل داشته باشند. مستادون عضوی از یک [فدراسیون بزرگ‌تر](https://fediverse.party/) است که به کاربران امکان می‌دهد با دیگر سکوهای آزاد مانند [پیرتیوب](https://joinpeertube.org/) و [فرندیکا](https://friendi.ca/) که قوانین یکسانی را پشتیبانی می‌کنند تعامل داشته باشند. 

# افزودن بخش دیدگاه‌ها با مستادون

یکی از دشواری‌های وبسایت‌ها استاتیک افزودن بخش دیدگاه‌هاست. گزینه‌های گوناگونی برای انجام این کار وجود دارد. یک راهکار ساده استفاده از Disqus است. چون Disqus  سنگین است و مقدار زیادی اسکریپت غیرضروری در صفحات بارگزاری می‌کند و مهم‌تر از همه آزاد نیست هرگز گزینه مطلوبی برای من نبود. برای دیدن راهکارهای بیشتر می‌توانید [این پست](https://mehdix.ir/static-comments.html) مهدی صادقی را ببینید.

با سپاس از [‪@carl‬](https://linuxrocks.online/@carl) برای نوشتن کد و گزارشی که در [این پست](https://carlschwan.eu/2020/12/29/adding-comments-to-your-static-blog-with-mastodon/) نوشته است، از این پس شما می‌توانید با داشتن یک اکانت مستادون دیدگاه‌های خودتون رو برای من بفرستید، و آنچه دیگران نوشته‌اند را هم ببینید.

## کد جاوا اسکریپت

چون کد اصلی برای [`#HUGO`](https://gohugo.io/) نوشته شده بود، من تغییرات اندکی در آن دادم تا با [`#JEKYLL`](https://jekyllrb.com/) سازگار شود و آن را در فایل [`mastodon.html`](https://raw.githubusercontent.com/mhdzli/zmim.ir/master/src/_includes/mastodon.html) در پوشه [`_includes`](https://github.com/mhdzli/zmim.ir/tree/master/src/_includes) قراد دادم:


<div class="code-block">
{% highlight javascript %}
{% raw %}
<div class="page-content">
  <h2>دیدگاه‌ها</h2>
  <p>می‌توانید دیدگاه‌های این پست را در مستادون و<a class="link" href="https://{{ page.mastodon.host }}/@{{ page.mastodon.username }}/{{ page.mastodon.id }}"> اینجا </a>ببینید. برای نوشتن دیدگاه خود روی لینک زیر کلیک کنید.</p>
  <p><a class="button" href="https://{{ page.mastodon.host }}/interact/{{ page.mastodon.id }}?type=reply">نوشتن دیدگاه‌</a></p>
  <p id="mastodon-comments-list"><button id="load-comment">دیدن دیدگاه‌ها</button></p>
  <noscript><p>برای این بخش می‌باید جاوا اسکریپت را فعال کنید.</p></noscript>
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

    document.getElementById("load-comment").addEventListener("click", function() {
      document.getElementById("load-comment").innerHTML = "دریافت دیدگاه‌ها";
      fetch('https://{{ page.mastodon.host }}/api/v1/statuses/{{ page.mastodon.id }}/context')
        .then(function(response) {
          return response.json();
        })
        .then(function(data) {
          if(data['descendants'] &&
             Array.isArray(data['descendants']) && 
            data['descendants'].length > 0) {
              document.getElementById('mastodon-comments-list').innerHTML = "";
              data['descendants'].forEach(function(reply) {
                reply.account.display_name = escapeHtml(reply.account.display_name);
                reply.account.emojis.forEach(emoji => {
                  reply.account.display_name = reply.account.display_name.replace(`:${emoji.shortcode}:`,
                    `<img src="${escapeHtml(emoji.static_url)}" alt="Emoji ${emoji.shortcode}" height="20" width="20" />`);
                });
                mastodonComment =
                  `<div class="mastodon-comment">
                     <div class="avatar">
                       <img src="${escapeHtml(reply.account.avatar_static)}" height=60 width=60 alt="">
                     </div>
                     <div class="content">
                       <div class="author">
                         <a href="${reply.account.url}" rel="nofollow">
                           <span>${reply.account.display_name}</span>
                           <span class="disabled">${escapeHtml(reply.account.acct)}</span>
                         </a>
                         <a class="date" href="${reply.uri}" rel="nofollow">
                           ${reply.created_at.substr(0, 10)}
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
      });
  </script>
</div>
{% endraw %}
{% endhighlight %}
</div>

سپس در [`layout` مربوط به پست‌ها]() یک شرط افزودم تا هر زمان که در [`frontmatter`](https://jekyllrb.com/docs/front-matter/) پست ‍`mastodon` وجود داشت، بخش دیدگاه‌ها افزوده شود:

<div class="code-block">
{% highlight liquid %}
{% raw %}
{% if page.mastodon %}
  {% include mastodon.html %}
{% endif %}
{% endraw %}
{% endhighlight %}
</div>

در پایان هم برای نمایش بهتر دیدگاه‌ها از [این چند خط `CSS`](https://github.com/mhdzli/zmim.ir/commit/78e351e5f809ead9eb4a77021026c5364c6e2081) استفاده کردم.

اینجا می‌توانید نتیجه نهایی را آزمایش کنید:
