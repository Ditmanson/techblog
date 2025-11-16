---
title: "Tech Stack"
date: 2025-11-15
description: "My Tech Stack Nov 2025"
draft: false
tags:
  - k8s
  - linux
---

# Techstack

## OS
- omarchy
- http://omarchy.org/

It feels different than a debian or ubuntu distro on the fact that it's super vim focused. It comes equipped with [hyperland](https://wiki.archlinux.org/title/Hyprland), and a preconfigured arch setup. There's no complicated set-up and bluetooth/wifi work out of the box. It has a bunch of pre-configured software installed, some of it needs changing and some I'm still checking out. 

---

#### Omarchy OS 
The keybindings are pretty sick, and easy to change in the $HOME/.config/hypr/bindings.conf. The software is pretty opinionated, but we can change the ones we don't like. The default password manager is 1password, which we currently use. I'm planning to swap over to selfhosted just haven't goettn around to it. The browser installed is some sort of stripped down chromium, there's stuff in the config files about brave so maybe it's related. I took that out and installed qute browser instead. Neovim is the default code editor. I'm used to plain old vim that comes with every dnf install; however, I'm giving it a try. The Neovim distro, if that's what we call it, is lazyvim. My first impressions is, I mostly hate it. But I think that's just cause I haven't figured out how to turn off all the extra garbage. Spotify is pre-installed which is what I use; but again, you can swap out the hyperland shortcuts, so if you want to switch it up, it's super easy. 
#### Qutebrowser
[Qutebrowser](https://qutebrowser.org/) is super sick as far as browser's go. It's open source python browser. You can call it from the command line. You can edit the configs from a vim like command. There's a basic pi-hole style ad-blocker. You can update the links with a command, adblock-update. They have all the code opensource so you know they aren't sending your info back to google or mozilla or anyone else. The keybindings takes some getting used to; **BUT** they are sweet, check them out [here](qute://help/img/cheatsheet-big.png). You can't add plugins, so there's not a good way of blocking youtube adds. I saw a cool way around that on [distrotube](https://www.youtube.com/watch?v=rMYMfnOpxP0&t=392s) .You open the youtube links with MPV. To name things easier, in the .config/qutebrowser/config.py you can add this line `config.bind('M', 'hint links spawn mpv {hint-url}')`
#### Nvim
I'm still getting used to lazyvim. I need to turn a bunch of this stuff off. Things like the distracting autocomplete suggestions. Don't like that. I'm used to the commands being at the bottom, the command box seems like a bit much for vim, if you ask me. If you accidently open it in a directory, it looks nicer than netrw;however, I don't know how to make it dispear. I kind of feel like if you think you need a code tree open all the time, then maybe you belong on vscode. But it's not all bad. Omarchy has built in theme changing, which changes the color and look of the desktop, terminal, and nvim editor. That's pretty sweet. I think that's the only reason I haven't switched back yet. All the keybindings are [here](https://www.lazyvim.org/keymaps). So far they have mostly disapointed me, probably because I'm not used to them. But the only one I wanted to try was the markdown-preview <leader>cp . However, apparently that plugin didn't make the cut. So I'll have to figure out the plugin manager.
#### Kubernetes
I've got 2 nodes running off [k3s](https://docs.k3s.io/). I don't use any cloud software. Everything is set up on [metallb](https://metallb.io/). One thing to note, if you want to copy me you'll need to disable k3s default loadbalancer, see [here](https://metallb.io/configuration/k3s/). You can do it when you bring up your first node, but if you don't. Then just add `ExecStart=/usr/local/bin/k3s server --disable servicelb \` to the end of /etc/systemd/system/k3s.service.

For ingress I'm using istio for public routing. Private routing is done through metallb loadbalancers. Since I have 2 nodes, I need shared disk storage. For that I use [nfs-csi-driver](https://github.com/kubernetes-csi/csi-driver-nfs). It's a bit tricky to set up the nfs kernel on the arch system, see [here](https://wiki.archlinux.org/title/NFS). The big change from all the debian instructions is restarting your nfs server is you need to use [nfs-utils](https://www.linuxfromscratch.org/blfs/view/cvs/basicnet/nfs-utils.html). I ended up just feeding a chat bot the debain docs and asket it to translate it to arch. It fixed it up for me.

Using nginx to host Webpage from a hugo site. 

I have cnpg and pgadmin hooked up and routed on the private network. The work I did on them got erased, but it wasn't much anyways. I've decided I'm going to learn fast api instead of learning sql. So nothing's really lost. 

Speaking of losing things. Thankfully I keep all my manifests in [ArgoCD](https://argo-cd.readthedocs.io/en/stable/). Bringing up a cluster from scartch only required kicking the cnpg operator when I needed to redownload the crd's. You can grab my [manifests](https://github.com/Ditmanson/argocluster) [bootstrap app](https://github.com/Ditmanson/argobase) if you want. Probably the next app is going to be a fastapi app.  

#### Router
Speaking of losing everything. The reason I lost everything, is when the power went out my IP's all changed. While trying to fix it, I ended up getting my cluster stuck to a low ip address and I somehow locked it. I didn't have much to lose, so I uninstalled and reinstalled, but before that; I configured static ip's on my pfsense router. PFsense is a pretty cool little hobby project if you want to learn network stuff. I used it for [snort](https://www.snort.org/documents) and I used to configure network wide vpn's with openvpn. However, coding network vpn's are a bit static, and I like switching every device to a different IP every day, so we use it on each device now. For VPN's we switch between a few, but we always stick wtih something from Europe since they have the GDPR.

#### OTHER
- [keyboard voyager](https://www.zsa.io/voyager) Pretty much the sickest keyboard. If bluetooth is important then you need something zmk flashed running with a bluetooth chip. If you go wired, you get [qmk](https://configure.zsa.io/voyager/layouts/default/latest/0/intro) which means you can configure with zsa. Which is what makes them so sweet. 
- [Bambu 3D Printer](https://duckduckgo.com/?q=bambu+printer&ia=web)
- [NAS](https://wiki.archlinux.org/title/RAID) 
- [Static Site Generater](https://gohugo.io/), This thing's pretty sweet for making quick websites with markdown
