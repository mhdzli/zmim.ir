---
title: Installing Ubuntu 20.04 on a Lenovo ThinkPad 10 Tablet
date: 2021-02-13 10:20:00 +03:30
tags: [foss, ubuntu, touch]
description: Booting a device with 32-bit UEFI from USB
image:
mastodon:
  host: mas.to
  username: mz
  id: 105723338614674634
lang: en
---

# Background

A friend of mine has a Lenovo ThinkPad 10 tablet and, wanting to install GNU/Linux on it, asked for my help. I was also curious to see how well different desktop environments work with touchscreens.

To avoid having to reformat a USB drive every time you want to boot a live ISO, you can use [ventoy](https://github.com/ventoy/Ventoy){:target="_blank"}{:rel="noopener noreferrer"}.

After testing several options, we decided to install Ubuntu. Since it was my friend's first time using GNU/Linux, we decided to go step by step and I would explain the process at each stage. But right at the start, things got stuck. I tried several times using `Rufus` to create a bootable USB from the `ISO` file. Even though I had set USB as the first boot priority in `BIOS` settings (accessible by pressing the power button and volume-up simultaneously, where you can also change boot settings), disabled `Secure Boot` and `Fast Boot` — the system kept booting from the hard drive and wouldn't recognize the `USB drive` as `bootable`.

I also tried the familiar method of writing the `ISO` to `USB` using `dd`, but while it booted fine on a laptop, it had problems on the tablet.

<div class="code-block">
{% highlight bash %}
dd if=<PATH-TO-A-LIVE-ISO> of=/dev/sdx status="progress"
{% endhighlight %}
</div>

Here `sdx` is the block device corresponding to your `USB drive`, which you can find using `lsblk`.

After a bit of searching and checking the tablet's specs, I realized this system uses a 32-bit `UEFI`, which is exactly why the `USB` was not booting.

# What to Do?

The answer is simple:

- Format the `USB drive` as `fat32`.
- Manually copy the contents of the `ISO` file onto it.

To `mount` an `ISO` file on GNU/Linux, use the following command:
<div class="code-block">
{% highlight bash %}
sudo mount -o loop <PATH-TO-A-LIVE-ISO> <PATH-TO-THE-FOLDER-YOU-WANT-TO-MOUNT-THE-ISO>
{% endhighlight %}
</div>

- Download the [`bootia32.efi`](https://github.com/hirotakaster/baytail-bootia32.efi){:target="_blank"}{:rel="noopener noreferrer"} file from the GitHub repository or from [here](/ubuntu-on-tablet/bootia32.efi) and copy it to the `/EFI/BOOT` folder.
- Copy the `grub.cfg` file from the `/boot/grub` folder and place it in the root of the `USB` (`/`).
- Disable `Fast Boot` and `Secure Boot`, then boot the system from the `USB`.
