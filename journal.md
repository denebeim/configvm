# Rebuilding deepthtot with ProxMox

## Current State

There was a hard drive failure and during recovery all of the VMs were lost on the SAN.  They may be recoverable, but that's unknown at this time.

Regardless I've been wanting to switch to ProxMox for a long time now, so I'm going to do this.

Converting to ProxMox

1. Get the HP up and running on kvm slot 1.
2. Watch ProxMox videos
3. Install ProxMox on HP
4. Get familiar with ProxMox
1. Set up backups
5. Move ssd VMs onto HP
    1. Move Services
    2. Move IPA
    3. Move other stuff, whatever exists
1. After everything seems to be working um, bite the bullet, maybe
    1. Install proxmox on sandisk ssd
    2. Run it on old machine
    3. Try joining it to cluster
    4. Migrate a test machine to it from the HP
    5. Dump an image of the current drive onto the SAN
    1. Clone services, IPA, etc onto supermicro
    1. shut down the VMs on the HP and ensure the supermicro works
    1. bring the HP VMs back up
    1. stop the SM with ssd
    1. install proxmox onto the SM
    1. repeat the migrations etc
    1. test everything
    1. if there's a failure recover the image from the SAN
5. Get Plex working
7. Get torrent working/rebuild torrent
6. Get Mastodon Working
8. Get Mail Server Ready
9. Create the dmz machines
1. Basic system is configured
    1. K8S
    1. DNS
    1. CertMonger
    1. S3 server
    1. gitlab
    1. migrate mail and mastodon to k8s

## ProxMox notes

ProxMox is really cool, I mean really.  It basically serves the same purpose as vsphere except it's all open source and brodcom can kiss my fat transgender ass.  This is why you never, ever trust closed source for anything useful.  (do you hear that plex???)

Anyway, this is what I've done, I watched [LearnLinuxTV's proxmox course on youtube](https://www.youtube.com/watch?v=5j0Zb6x_hOk&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo) It's pretty good, a little slow and repetitive, but it gave me a really good feel for how to proxmox.  I've been kinda working along with it to do my actual server.

The server is my old HP proliant, it has a 4 x AMD A12-9800 RADEON R7, 12 COMPUTE CORES 4C+8G, I'm not sure what that means, if it has 12 compute cores how is there only 4 cores?  Regardless, it's big enough for my purposes.  What my purposes are is set this thing up to be the minimal system, what is still surviving namely, services, ipa, awx, backup/and it looks like plex?

The system came up just peachy, I'm loving it.  I need to get into the habit of creating template disks, in fact I'll probably use ansible for it.  The idea is to have a 'golden' image, yeah, I know I always said that wasn't a good idea, however without a fast package cache (when let's face it, nexus is most certainly not) installing a system from bare metal is just too friggin slow.  That said, I think a big part of it is how slow my san is, that needs to be addressed, but when I have money.

I'm thinking it may be good to put an ssd on my main server and build the VMs there then migrate them to another drive.  Yes, I can do this with proxmox, it doesn't have all of ESXi's stupid money grubbing limitations.

So, as I was saying, after getting the proliant to boot, which was a very frustrating day followed by getting it done almost immediately the next day.  Installing proxmox was really easy, it probably doesn't need explanation, however basically you transfer it to a thumb drive (I had to track down software to do this, but I've forgotten what it was and don't feel like looking it up now), then booting the thumb drive, and use that to do the install, which as I said was super straight forward.

After that I started kicking the tires, I watched the aformentioned video series and worked along with it.  Create a VM, turn it into a template, spin up several more and that's it.

Now, one caveat, I really don't like how Jay's method of preparing a machine works.  When it comes time, this is what I want:

Using cloud-init, the machine's name should be available in a variable.  Use that, then get cloud-init to be set up to do it's whole thing on the next boot.  I think that just consists of blowing away everything (ssh keys, logs, machine-id, etc) and finally erasing a file that causes cloudinit to run.  I think that may be a u 13.10 thing though, I'm not finding it on my current machines.

TODO: Figure out how Ubuntu 22.04 and earlier cause cloud-init to only run completely on the first boot.

I need to know more about cloud init, it's the missing piece of cleanly bringing up a new vm autonomously.  Fabulous!  I've always wanted something like it.

This has gone on too long... My plan for today is:

1. Sometime during the day rsync hg2t2g volume1 to the external drives.  It'll probably take longer than a day, if that's the case play wow until it's done :-)
1. Move the VMs.  [docs](https://pve.proxmox.com/wiki/Migrate_to_Proxmox_VE) use ovftool on esx and qm importovf on pve.<br>
NOTE: a side note, it would be cool to have the proxmox machine id = the last tuple of the address
1. Do it again because I know I'll learn stuff while doing it the first time.<br>
NOTE: there's a cloud-init disk in the system, that may be useful for this.
1. If everything seems to be working, shut down the original machines and bring the new ones on-line.<br>
NOTE: track down how pve is defined, clearly it's from the last install see what it is and make the HP be pve.
1. Let it run overnight

Tomorrow:

1. Back up the old machine
1. Install proxmox on heartogold.  Maybe onto the sandisk if I'm squeemish.
1. Join it with the HP
1. Migrate the machines to it.

NOTE: yeah, I definately should do a dry run with the USBSSD I have.

Well, I fell into the youtube black hole... I'm trying to rememver who I saw with the command that allows you to take standard images and add packages to them.  (like emacs and ipa-client)
Still having issues.  I can't seem to find the application I ran across yesterday to add deb packages to an img file...  waste of time.

I've been unable to transfer IPA to proxmox, it just doesn't work.  The test machine worked fine, I'm suspecting it's the centos instead of ubuntu.  I'm ls

2/15/2 today I got services over.  IPA is a problem, I don't know if it's centos related or joy related.  Moving services involved editing /etc/netplan/01-network-manager-all.yaml which has the fixed ID and ethernet device in it.  I'm hoping this is because it has a fixed IP.  Which reminds me *always* use DHCP to assign addresses.  Use a MAC if you want static assignments.


