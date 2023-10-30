---
title: افزودن terminfo به سرور
date: 2022-03-24 20:42:36 +04:30
tags: [foss, termux, ssh, linux]
description: گاهی بسته به ترمینالی که استفاده می‌کنید، برخی برنامه‌های تحت ترمینال روی سروری که به آن `ssh` کرده‌اید به درستی اجرا نمی‌شوند و نیاز است که `terminfo` را به سرور بیافزایید.
image:
mastodon:
  host: mas.to
  username: mz
  id: 108012802697185849
---

# جابجایی فایل بین گوشی اندروید و سیستم گنو/لینوکس و مشکل `terminfo` برای ترمینال `foot`

من روی سیستم از تریمینال امولاتور [`foot`](https://codeberg.org/dnkl/foot){:target="_blank"}{:rel="noopener noreferrer"} استقاده می‌کنم که روی `wayland` به درستی اجرا می‌شود و بسیار ساده، سریع و مینیمال است.
برای جابجایی فایل‌ها بین گوشی اندروید و سیستم گنو/لینوکس هم از `scp` یا `rsync` روی `ssh` استفاده می‌کنم. اگر دوست دارید، می‌توانید ویدیوی آموزشی که در این باره ساختم را ببینید.

<div class="video">
<iframe id="odysee-iframe" src="https://odysee.com/$/embed/ssh-on-termux/2c1fb60299e057dad14d1ffdd70f5bdbee16df88?r=CTpZDCJuEb8cZyCyCCpUEdw5D4LFZTkn" allowfullscreen></iframe>
</div>

هنگامی که برای اولین بار از ترمینال `foot` به گوشی اندرویدم `ssh` کردم، رنگ‌های ترمینال به درستی نمایش داده نمی‌شد و هر کاراکتری که تایپ می‌کردم دو بار نوشته می‌شد.
پس از کمی جستجو فهمیدم که مشکل مربوط به داده‌های `terminfo` و نبود آنها روی `termux` است. اینجا می‌توانید نبود رنگ‌ها و همچنین تکرار چند باره داده‌های `zsh prompt` را روی ترماکس بعد از `ssh` ببینید و آن را با گوشی مقایسه کنید:

<div style="text-align: center;">
    <img src="no-terminfo.png" style="max-width: 80%; margin: 10px;" alt="نبود `terminfo`">
</div>

# نبود داده‌های `terminfo` روی سرور

ممکن است برای شما هم بارها پیش آمده باشد که هنگام اجرای یک برنامه تحت ترمینال روی سروری که به آن `ssh` کرده‌اید این خطاها را دریافت کرده باشید:

<div class="code-block">
{% highlight bash %}
Error opening terminal: xterm-kitty.
# OR
WARNING: terminal is not fully functional
/etc/hosts  (press RETURN)
{% endhighlight %}
</div>

این مشکلات به همان دلیلی که گفتم پیش می‌آیند.

# چه باید کرد؟

- روی سرور با `export TERM=xterm`  متغیر محیطی ‪`$TERM`‬ را تغییر دهید و آزمایش کنید که آیا مشکل برطرف می‌شود یا خیر.
- ترمینال امولاتوری را که استفاده می‌کنید روی سرور هم نصب کنید. (برای من این کار نشدنی بود چرا که روی ترماکس می‌خواستم `ssh` بزنم.)
- داده‌های `terminfo` را به سرور بیافزایید.


## افزودن داده‌های `terminfo` به سرور

- اگر داده‌های `terminfo` را در یک فایل دارید می‌توانید آنها را به سرور منتقل کرده و با استفاده از `tic` آنها را نصب کنید.

<div class="code-block">
{% highlight bash %}
rsync <PATH-TO-YOUR.TERMINFO> <REMOTE-MACHINE>:/tmp
ssh <REMOTE-MACHINE>
sudo tic /tmp/emu.terminfo
{% endhighlight %}
</div>

اگر این داده‌ها را ندارید می‌توانید با استفاده از ‪`infocmp > foot.terminfo`‬ آنها را ببینید و در یک فایل ذخیره کنید.
برای `foot` این داده‌ها به این صورت هستند:

<div class="code-block">
{% highlight bash %}
infocmp
#	Reconstructed via infocmp from file: /usr/share/terminfo/f/foot
foot|foot terminal emulator,
	am, bce, bw, ccc, hs, mir, msgr, npc, xenl,
	colors#0x100, cols#80, it#8, lines#24, pairs#0x10000,
	acsc=``aaffggiijjkkllmmnnooppqqrrssttuuvvwwxxyyzz{% raw %}{{||}}{% endraw %}~~,
	bel=^G, blink=\E[5m, bold=\E[1m, cbt=\E[Z, civis=\E[?25l,
	clear=\E[H\E[2J, cnorm=\E[?12l\E[?25h, cr=\r,
	csr=\E[%i%p1%d;%p2%dr, cub=\E[%p1%dD, cub1=^H,
	cud=\E[%p1%dB, cud1=\n, cuf=\E[%p1%dC, cuf1=\E[C,
	cup=\E[%i%p1%d;%p2%dH, cuu=\E[%p1%dA, cuu1=\E[A,
	cvvis=\E[?12;25h, dch=\E[%p1%dP, dch1=\E[P, dim=\E[2m,
	dl=\E[%p1%dM, dl1=\E[M, ech=\E[%p1%dX, ed=\E[J, el=\E[K,
	el1=\E[1K, flash=\E]555\E\\, home=\E[H, hpa=\E[%i%p1%dG,
	ht=^I, hts=\EH, ich=\E[%p1%d@, ich1=\E[@, il=\E[%p1%dL,
	il1=\E[L, ind=\n, indn=\E[%p1%dS,
	initc=\E]4;%p1%d;rgb:%p2%{255}%*%{1000}%/%2.2X/%p3%{255}%*%{1000}%/%2.2X/%p4%{255}%*%{1000}%/%2.2X\E\\,
	invis=\E[8m, is2=\E[!p\E[?3;4l\E[4l\E>, kDC=\E[3;2~,
	kEND=\E[1;2F, kHOM=\E[1;2H, kIC=\E[2;2~, kLFT=\E[1;2D,
	kNXT=\E[6;2~, kPRV=\E[5;2~, kRIT=\E[1;2C, kbs=^?,
	kcbt=\E[Z, kcub1=\EOD, kcud1=\EOB, kcuf1=\EOC, kcuu1=\EOA,
	kdch1=\E[3~, kend=\EOF, kf1=\EOP, kf10=\E[21~, kf11=\E[23~,
	kf12=\E[24~, kf13=\E[1;2P, kf14=\E[1;2Q, kf15=\E[1;2R,
	kf16=\E[1;2S, kf17=\E[15;2~, kf18=\E[17;2~,
	kf19=\E[18;2~, kf2=\EOQ, kf20=\E[19;2~, kf21=\E[20;2~,
	kf22=\E[21;2~, kf23=\E[23;2~, kf24=\E[24;2~,
	kf25=\E[1;5P, kf26=\E[1;5Q, kf27=\E[1;5R, kf28=\E[1;5S,
	kf29=\E[15;5~, kf3=\EOR, kf30=\E[17;5~, kf31=\E[18;5~,
	kf32=\E[19;5~, kf33=\E[20;5~, kf34=\E[21;5~,
	kf35=\E[23;5~, kf36=\E[24;5~, kf37=\E[1;6P, kf38=\E[1;6Q,
	kf39=\E[1;6R, kf4=\EOS, kf40=\E[1;6S, kf41=\E[15;6~,
	kf42=\E[17;6~, kf43=\E[18;6~, kf44=\E[19;6~,
	kf45=\E[20;6~, kf46=\E[21;6~, kf47=\E[23;6~,
	kf48=\E[24;6~, kf49=\E[1;3P, kf5=\E[15~, kf50=\E[1;3Q,
	kf51=\E[1;3R, kf52=\E[1;3S, kf53=\E[15;3~, kf54=\E[17;3~,
	kf55=\E[18;3~, kf56=\E[19;3~, kf57=\E[20;3~,
	kf58=\E[21;3~, kf59=\E[23;3~, kf6=\E[17~, kf60=\E[24;3~,
	kf61=\E[1;4P, kf62=\E[1;4Q, kf63=\E[1;4R, kf7=\E[18~,
	kf8=\E[19~, kf9=\E[20~, khome=\EOH, kich1=\E[2~,
	kind=\E[1;2B, kmous=\E[<, knp=\E[6~, kpp=\E[5~,
	kri=\E[1;2A, oc=\E]104\E\\, op=\E[39;49m, rc=\E8,
	rep=%p1%c\E[%p2%{1}%-%db, rev=\E[7m, ri=\EM,
	rin=\E[%p1%dT, ritm=\E[23m, rmacs=\E(B, rmam=\E[?7l,
	rmcup=\E[?1049l\E[23;0;0t, rmir=\E[4l, rmkx=\E[?1l\E>,
	rmso=\E[27m, rmul=\E[24m, rs1=\Ec,
	rs2=\E[!p\E[?3;4l\E[4l\E>, sc=\E7,
	setab=\E[%?%p1%{8}%<%t4%p1%d%e%p1%{16}%<%t10%p1%{8}%-%d%e48:5:%p1%d%;m,
	setaf=\E[%?%p1%{8}%<%t3%p1%d%e%p1%{16}%<%t9%p1%{8}%-%d%e38:5:%p1%d%;m,
	sgr=%?%p9%t\E(0%e\E(B%;\E[0%?%p6%t;1%;%?%p5%t;2%;%?%p2%t;4%;%?%p1%p3%|%t;7%;%?%p4%t;5%;%?%p7%t;8%;m,
	sgr0=\E(B\E[m, sitm=\E[3m, smacs=\E(0, smam=\E[?7h,
	smcup=\E[?1049h\E[22;0;0t, smir=\E[4h, smkx=\E[?1h\E=,
	smso=\E[7m, smul=\E[4m, tbc=\E[3g, u6=\E[%i%d;%dR,
	u7=\E[6n, u8=\E[?%[;0123456789]c, u9=\E[c,
	vpa=\E[%i%p1%dd,
{% endhighlight %}
</div>

- راه ساده‌تر این است که پس از تولید این داده‌ها مستقیم آنها را به سرور فرستاده و نصب کنید:

<div class="code-block">
{% highlight bash %}
infocmp | ssh <REMOTE-MACHINE> "tic -x /dev/stdin"
{% endhighlight %}
</div>

- اما اگر سرور شما قدیمی است و یا به هر دلیلی `tic` امکان دسترسی به `/dev/stdin` را ندارد، می‌توانید داده‌ها را مستقیم روی فایلی در سرور بریزید و بعد نصب کنید:

<div class="code-block">
{% highlight bash %}
infocmp | ssh <REMOTE-MACHINE> "cat > /tmp/terminfo && tic -x /tmp/terminfo; rm /tmp/terminfo"
{% endhighlight %}
</div>

پس از افزودن داده‌های `foot` به `termux` مشکل من حل شد.

<div style="text-align: center;">
    <img src="with-terminfo.png" style="max-width: 80%; margin: 10px;" alt="نمایش درست رنگ‌ها پس از افزودن داده‌های `terminfo`">
</div>

# منابع:

- [kitty FAQ](https://sw.kovidgoyal.net/kitty/faq/#i-get-errors-about-the-terminal-being-unknown-or-opening-the-terminal-failing-when-sshing-into-a-different-computer){:target="_blank"}{:rel="noopener noreferrer"}
- [foot repository](https://codeberg.org/dnkl/foot){:target="_blank"}{:rel="noopener noreferrer"}
