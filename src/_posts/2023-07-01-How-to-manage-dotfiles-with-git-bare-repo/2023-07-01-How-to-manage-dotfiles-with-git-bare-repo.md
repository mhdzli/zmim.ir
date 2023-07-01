---
title: مدیریت فایل‌های تنظیمات با استفاده از Git bare
date: 2023-07-01 22:10:00 +04:30
tags: [git, dotfiles, linux]
description: یکی از بزرگترین مزایای استفاده از گنو/لینوکس قابلیت شخصی سازی آن است. بسیاری از کاربران شخصی سازی را با تغییر فایل‌های تنظیمات انجام می‌دهند. یکی از بهترین روش‌ها برای مدیریت این فایل‌ها استفاده از `Git` است. 
image:
toc: true
mastodon:
  host: mas.to
  username: mz
  id: 110642412995764976
---

# دات‌فایل‌ها

 یکی از بزرگترین مزایای استفاده از گنو/لینوکس قابلیت شخصی سازی آن است. بسیاری از کاربران شخصی سازی را با تغییر فایل‌های تنظیمات انجام می‌دهند.
 به مرور که از سیستم استفاده می‌کنیم، این فایل‌ها پیوسته تغییر می‌کنند و وقتی تعداد آنها زیاد شود و شما روی سیستم‌های مختلف از آنها استفاده کنید، مدیریت این فایل‌ها دشوارتر می‌شود.
 یکی از بهترین روش‌ها برای مدیریت این فایل‌ها استفاده از `Git` است.

# ویندو منیجرها و بزرگترین مزیتشان: قابلیت شخصی سازی

 من مدت‌هاست که از گنو/لینوکس روی سیستم‌های شخصی استفاده می‌کنم و سال‌هاست که استفاده از ویندو منیجر‌ّهای مینیمال را به محیط‌های دسکتاپ بزرگ مثل گنوم و کی‌دی‌ای ترجیح می‌دهم.
استفاده از ویندو منیجرها سبب شده که سیستم من سبک‌تر باشد و برای استفاده من کاملا شخصی سازی شده باشد.

هنگامی که شما به جای دسکتاپ‌های بزرگ از ویندو منیجرها استفاده می‌کنید، بسیاری از تنظیمات سیستم و ابزارها را خودتان باید انجام دهید.

این کار سبب می‌شود که شما سیستمی که از آن استفاده می‌کنید را بهتر بشناسید و بسته به نیازتان آن را تغییر بدهید.

مدیریت فایل‌های تنظیمات برای استفاده از آنها روی سیستم‌های مختلف با سخت‌افزار و سیستم عامل‌های متفاوت ممکن است دشوار به نظر برسد. استفاده از `Git bare repository` یکی از بهترین و هوشمندانه‌ترین راه‌ها برای این کار است. از آنجا که بیشتر ما برای مدیریت پروژه‌هامان از `Git` استفاده می‌کنیم، این کار نیاز به نصب ابزار تازه و یاد گرفتن آن ندارد.

# سایر ابزارها

ابزارهای بسیاری برای مدیریت دات‌فایل‌ها وجود دارد. معروفترین آنها شاید [`GNU Stow`](https://www.gnu.org/software/stow/){:target="_blank"}{:rel="noopener noreferrer"} باشد. لیستی از این ابزارها را می‌توانید [اینجا](https://dotfiles.github.io/utilities){:target="_blank"}{:rel="noopener noreferrer"} یا در [ویکی آرچ](https://wiki.archlinux.org/title/Dotfiles#Tools){:target="_blank"}{:rel="noopener noreferrer"} ببینید.

# روش `Git bare repo`

اولین جایی که من این روش بسیار هوشمندانه را پیدا کردم در [این گفتگوی هکر نیوز](https://news.ycombinator.com/item?id=11070797) بود. پس از آن جاهای مختلفی درباره آن خواندم و تصمیم گرفتم از این روش برای مدیریت دات‌فایل‌ها استفاده کنم.

## مزایا

- بدون نیاز به نصب ابزار اضافی
- از symlink استفاده نمی‌کنید.
- فایل‌ها در یک ریپوزیتوری `Git` مدیریت می‌شوند.
- می‌توانید از شاخه‌های مختلف برای سیستم‌های‌ مختلف استفاده کنید.
- می‌توانید پیکربندی خود را به راحتی هنگام نصب سیستم جدید تکرار کنید.

## چطور کار می‌کند؟

در این روش مخزن `Git bare` در یک پوشه جانبی (مانند ‪$HOME/.cfg‬ یا ‪$HOME/.myconfig‬) ذخیره می‌شود. با استفاده از یک `alias` ویژه دستورهای `Git` روی این پوشه جانبی اجرا می‌شوند و تداخلی با دات‌فایل‌ها و بقیه مخزن‌های `Git` نخواهند داشت.

## آغاز از صفر

اگر پیش از این پیکربندی‌های خود را در یک مخزن `Git` ردیابی نکرده‌اید، می‌توانید با این فرمان‌ها به راحتی از این روش استفاده کنید:

<div class="code-block">
{% highlight bash %}
git init --bare $HOME/.cfg
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
config config --local status.showUntrackedFiles no
echo "alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'" >> $HOME/.config/shell/.aliasrc
{% endhighlight %}
</div>

فرمان نخست یک پوشه در مسیر `‪~/.cfg‬` ایجاد می کند که یک مخزن [`Git bare`](http://www.saintsjd.com/2011/01/what-is-a-bare-git-repository/) است که فایل‌های ما را ردیابی می‌کند.

سپس یک `alias` می‌سازیم تا هر گاه می‌خواهیم با مخزن دات‌فایل‌ها تعامل داشته باشیم به جای `git` معمولی از آن استفاده کنیم.

ما یک فلگ محلی برای مخفی کردن فایل‌هایی که هنوز آنها را ردیابی نمی‌کنیم تنظیم می‌کنیم. چون پس از این وقتی وضعیت پیکربندی و سایر فرمان‌ها را تایپ می‌کنید، فایل‌هایی که علاقه‌ای به ردیابی آنها ندارید به‌عنوان ردیابی نشده نشان داده نشوند.
من ترجیح می‌دهم به جای این کار از فرمان `echo "*" > ~/.gitignore` استفاده کنم. در این صورت برای اضافه کردن فایل‌ها باید از فلگ `‪-f‬` در فرمان‌های `config` استفاده کنیم مثلا: `config add -f .dotfile`. این کمک می‌کند که ناخواسته با فرمان `‪config add .‬` فایل‌های اضافی افزوده و کامیت نشوند.

من از `zsh` استفاده می‌کنم و فایل `‪~/.config/shell/aliasrc‬` را در `‪.zshrc‬` سورس کرده‌ام. دقت کنید که اگر شما از شل دیگری استفاده می‌کنید در خط چهارم `alias` را برای آن شل اضافه کنید:

<div class="code-block">
{% highlight bash %}
# for bash
echo "alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'" >> $HOME/.bashrc

# for zsh 
echo "alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'" >> $HOME/.config/zsh/.zshrc
{% endhighlight %}
</div>

برای `fish` می‌توانید از این تابع استفاده کنید:

<div class="code-block">
{% highlight bash %}
function config -w git -d "Manages dotfiles"
    git --git-dir=$HOME/.dot --work-tree=$HOME $argv
end
{% endhighlight %}
</div>

## نصب فایل‌ها روی سیستم جدید و یا استفاده از این روش برای فایل‌های فعلی

اگر پیش از این پیکربندی‌های خود را در یک مخزن `Git` ذخیره کرده‌اید، در یک سیستم جدید می‌توانید با مراحل زیر به این روش مهاجرت کنید:

پیش از نصب مطمئن شوید که `alias` را در ریپوزیتوریتان به `‪.bashrc‬` یا `‪.zshrc‬` اضافه کردید. 

<div class="code-block">
{% highlight bash %}
echo "alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'" >> $HOME/.config/shell/.aliasrc
{% endhighlight %}
</div>


و اینکه مخزن منبع شما پوشه‌ای را که به عنوان `git bare`  استفاده می‌کنید نادیده می‌گیرد تا مشکلات عجیب و غریب برایتان پیش نیاید: `echo ".cfg" >> ~/.gitignore` یا آنطور که در بالا اشاره کردم: `echo "*" >> ~/.gitignore`

اکنون ریپوزیتوری پیکربندی‌هایتان را در یک مخزن `bare` در پوشه‌ای مخفی در مسیر `‪$HOME/.cfg‬` کلون کنید:

<div class="code-block">
{% highlight bash %}
git clone --bare <git-repo-url> $HOME/.cfg
{% endhighlight %}
</div>

حال `alias` را در شلی که باز کرده‌این تعریف کنید تا بتوانید از آن استفاده کنید:

<div class="code-block">
{% highlight bash %}
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
{% endhighlight %}
</div>

فایل‌های اصلی را با استفاده از فرمان `config checkout` جایگذاری کنید. من از شاخه `home` در [ریپوزیتوری خودم](https://github.com/mhdzli/dotfiles/tree/home){:target="_blank"}{:rel="noopener noreferrer"} برای ذخیره این فایل‌ها استفاده می‌کنم بنابر این برای من این فرمان به این شکل خواهد بود: `config checkout home`. اگر شما برای سیستم‌های مختلف از شاخه‌های مختلف استفاده می‌کنید دقت کنید که فایل‌های درست را جایگذاری کنید.

در صورتی که پیشتر فایل‌های پیکربندی مشابه در پوشه خانه سیستم داشته باشید دستور بالا اجرا نشده و خطا می‌دهد.
باید این فایل‌ها را از پوشه خانه پاک یا جابجا کنید. لیست کل فایل‌هایی که در ریپوزیتوری دارید را می‌توانید با این دستور ببینید: 

<div class="code-block">
{% highlight bash %}
config ls-tree --full-tree --name-only -r <YOUR BRANCH> 

#for me
config ls-tree --full-tree --name-only -r home
{% endhighlight %}
</div>

سپس برای دیده نشدن فایل‌های ردیابی نشده این تنظیم را انجام دهید: `config config --local status.showUntrackedFiles no`

تبریک می‌گم. پس از این می‌توانید فایل‌هایتان را این گونه مدیریت کنید:

<div class="code-block">
{% highlight bash %}
config status
config add .vimrc
config commit -m "Add vimrc"
config add .bashrc
config commit -m "Add bashrc"
config push
{% endhighlight %}
</div>

اگر به جای تنظیم `status.showUntrackedFiles` از `*` در `‪.gitignore‬` استفاده کردید این فرمان‌ها به این صورت خواهند بود:

<div class="code-block">
{% highlight bash %}
config status
config add -f .vimrc
config commit -m "Add vimrc"
config add -f .bashrc
config commit -m "Add bashrc"
config push
{% endhighlight %}
</div>

# منابع:

- [Hacker News](https://news.ycombinator.com/item?id=11070797){:target="_blank"}{:rel="noopener noreferrer"}
- [Atlassian's Blog](https://www.atlassian.com/git/tutorials/dotfiles){:target="_blank"}{:rel="noopener noreferrer"}
- [Coffee Addict](https://coffeeaddict.dev/how-to-manage-dotfiles-with-git-bare-repo){:target="_blank"}{:rel="noopener noreferrer"}

# [دات‌فایل‌های من](https://github.com/mhdzli/dotfiles){:target="_blank"}{:rel="noopener noreferrer"}
