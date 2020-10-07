<div dir="rtl">

# سیمرغ

سیمرغ قرار است یک تم Jekyll برای فارسی نویسی باشد. یک تم مینیمال که با تغییر فایل کانفیگ به راحتی بتوانید آن را برای راه‌اندازی وبلاگ شخصی خودتان استفاده کنید.

برای شروع این پروژه یک فورک از تم [`klise`](https://klise.now.sh) استفاده شده است که [اینجا](https://github.com/piharpi/jekyll-klise) در دسترس است.

برای فارسی کردن تاریخ‌ها از پلاگین [`jekyll-jalali`](https://github.com/mehdisadeghi/jekyll-jalali) استفاده کردم که [مهدی صادقی](https://github.com/mehdisadeghi/) گرامی نوشته و کلی مطلب مفید هم در [وبلاگش](https://mehdix.ir/) دارد که اگر تصمیم دارید از Jekyll استفاده کنید حتما بهش سر بزنید.


در این تم از فونت‌های [`وزیر`](https://github.com/rastikerdar/vazir-font) و [`وزیر کد`](https://github.com/rastikerdar/vazir-code-font) استفاده شده است که از کارهای زیبای آقای [راستی‌کردار](https://github.com/rastikerdar) است که از ایشان بسیار ممنونم.

## قابلیت‌ها

-  تم تاریک و روشن
-  پشتیبانی از تاریخ فارسی
-  تغییر ظاهر با استفاده از scss
-  هماهنگ با دسکتاپ و گوشی
-  ساختار مناسب برای عکس‌ها ([`jekyll-postfiles`](https://github.com/nhoizey/jekyll-postfiles))
-  نقشه سایت ([`jekyll-sitemap`](https://github.com/jekyll/jekyll-sitemap))
-  خوراک rss ([`jekyll-feed`](https://github.com/jekyll/jekyll-feed))
-  هایلایت کدها ([`Vazir Code Font`](https://github.com/rastikerdar/vazir-code-font)) با تشکر مجدد از جناب [راستی کردار](https://github.com/rastikerdar/) 
-  صفحه بندی پست‌ها
-  آنالیز گوگل اگر دوست دارید استفاده کنید. (نکنید 😠)
-  استفاده از W3C **👽**
-  استفاده از فونت Awesome **👽**
-  سبک و سریع **🚄**


## نصب

راه انداختن سرور local روی سیستم شخصی:

</div>
<div dir=ltr >

```bash
$ git clone https://github.com/mhdzli/simorq.git
$ cd jekyll-klise
$ bundle install
$ bundle exec jekyll serve
```
</div>

<div dir=rtl>

با آدرس `localhost:4000` می‌توانید نتیجه را در مرورگرتان مشاهده کنید.

[![دیپلوی در Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/mhdzli/simorq) [![دیپلوی با Vercel](https://vercel.com/button)](https://vercel.com/import/project?template=https://github.com/mhdzli/simorq)

## محدودیت‌ها

- از آنجا که پلاگین  [`jekyll-postfiles`](https://github.com/nhoizey/jekyll-postfiles#compatibility) و  [`jekyll-jalali`](https://github.com/mehdisadeghi/jekyll-jalali)  در `github pages` پشتیبانی نمی‌شوند امکان استفاده  روی گیتهاب وجود ندارد اما میتوانید از  [`netlify.com`](https://netlify.com)، [`vercel.com`](https://vercel.com) یا [`surge.sh`](https://surge.sh) استفاده کنید.


## مجوز

یک پروژه آزاد با مجوز [MIT](LICENSE).

</div>
