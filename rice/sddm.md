# SDDM & OpenGL madness  
Before the time of writing of this blog article, I have always used the autologin feature that comes with SDDM - my display manager. I had defined a section in my `/etc/sddm.conf` that said 
```
[Autologin]
User=<user>
Session=hyprland
```
Now that's fine and all, but after quite a while I had begun to reminisce the good ol' times where I was able to play with the beautiful SDDM themed interface I had configured before, but had ditched in the transition to `wlroots`-based `Hyprland` Desktop Environment.  
And so I decided to disable the autologin to re-enable the SDDM interface during power-on. And that revealed a significant problem. 
A part of `journalctl` had spat out an error about `sddm-greeter` (essentially the backend program for SDDM), complaining about an `Unrecognized OpenGL version`. 
OpenGL support for my laptop NVIDIA MX350 GPU had always been a bother, since for whatever reason OpenGL was shown to be supported by the GLX vendor tag `NVIDIA` by another program, `glxinfo`, 
but fails to be executed for - most confoundingly, the browser WebGL plugin for video acceleration and such. 
(Native applications that most certainly used OpenGL, for instance Minecraft w/ Prism Launcher, had not any problems with the Nvidia card - confusingly).  
This whole "Unrecognized OpenGL version" business was really frustrating. In an attempt to fix this issue, I had reconfigured kernel module parameters and `mkinitcpio` configuration for the `nvidia-dkms` package (*and suffering through the torturous dkms installation times whenever the linux kernel got an upgrade*). 
This did not solve the issue.  
At first I realized by performing `cat /proc/cmdline` that the grub configuration was not correctly loaded, as it had still contained the old version of the KMS parameters that was set last time. After correctly configuring and regenerating grub through the `grub-mkconfig -o /boot/grub/grub.cfg` command, the OpenGL error still persisted.  
Eventually, after other futile steps taken to solve the issue - including but not limited to: Re-installing `nvidia`; switching to `nvidia-dkms` for dynamic kernel mode setting; downgrading `nvidia` -- *didn't work because steam is dependent on* `lib32-vulkan-driver` *which depends on* `lib32-nvidia` *which depends on* `nvidia=54X.XX.X`; downgrading `nvidia-dkms` which lead to Hyprland not even booting; and etc.  
Eventually I had no choice but to choose a bodged solution. Some random guy on the internet mentioned that SDDM has experimental support for the Wayland display protocol - which is quite nice! Since my previous experience with X.org was not so well with Nvidia support (*contrary to popular opinion that X.org is more compatible with Nvidia*), switching SDDM to display on Wayland might resolve the issue...  
And what do you expect, but a beautifully working SDDM interface initiating each time the system boots!! After configuring some files in `/etc/sddm.conf.d/` including the addition of `10-wayland.conf` which set `DisplayServer=wayland` (*and also installing the weston compositor since it is apparently the default - and I hate change that might shatter my progress so far.*), `sddm` works fine and well again. Now every time I boot up, there is this beautiful interface (the theme of which is called Uzuri by the way and can be installed in the KDE store *which required some tinkering, especially installing* `plasma-framework5` *to provide relevant functions*) greeting me, and I no longer have to worry about random people just flipping open my laptop and stealing my confidential data.  
Because I guess I am important enough to be susceptible to these risks...  

> Last modified 2024-01-24
