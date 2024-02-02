# Development Notes
## Current State
I've forgotten where I was in the install which is why I'm writing this.  There is a partition on k8s-control that appears to have ubuntu loaded on it and nothing else.  However the ssh keys are wrong on it.

I took a snapshot before starting all of this, and the state it was in could be sshed into.  It had containerd on it, but not k8s.  I don't remember what I was doing.

## Tasks
- [X] Remove the machine from IPA
- [X] Go back to the fresh install of ubuntu
- [X] Ansible playbook for common (i.e. updated and registered)
- [X] Snapshot
- [X] Try the ansible playbook to install k8s one more time, I'm not sure this is going to work because I don't think geerlinguy's ansible supports current k8s.
- [ ] check it out, see if it works.
no it doesn't, but 
- [X] Snapshot
anyway

*NOTE:* Recall that the guy I interviewed with told me that weave isn't supported anymore.  Flux hasn't worked for me at all lately.  So, there's a third one that's common, try to install with that instead.  *calico* is the network layer.

## Notes
vscode is flagging my requirements .yaml file bad.  It was an array of package names.  Now you can't just use the default.  You need to put the name: explicity into the array.  I should probably add versions as well, but that's for later.

I was just thinking my nexus repo is fucking slow.  I refactored the updating of the apt sources into another file.  I'm going to try a scratch install with no proxy, because this is too friggin painful.  Back when I had Artifactory, even the free version, upgrades of RPM repositories were lightning fast.  I'm not sure debian repos can be static, so I haven't tried it.

It's still taking forever to install.  WTH installing packages used to be lightning fast.  Of course this is a VM on a SAN.  Maybe I should try it with the onboard ssd.
Never mind, this was dumb.

OK, this is where I stopped before.  geerlinguy's ansible was using the deprecated google repository.  I'm trying to fix that assuming I'm passing my variables correctly.

Yeah switching to the new key format worked.

TODO: fork geerlinguy's repo and submit a pull request if someone hasn't done it already.

And it installed without a hitch.  However kubectl is giving me:

```shell
root@k8s-control:~# hostname -i
192.168.42.242
root@k8s-control:~# kubectl get nodes
The connection to the server 192.168.42.242:6443 was refused - did you specify the right host or port?
root@k8s-control:~# 
```
There was this in the ansible logs:
```
TASK [geerlingguy.kubernetes : Configure Calico networking.] ******************************************************************************************************************************************************************************************
FAILED - RETRYING: Configure Calico networking. (12 retries left).
FAILED - RETRYING: Configure Calico networking. (11 retries left).
FAILED - RETRYING: Configure Calico networking. (10 retries left).
FAILED - RETRYING: Configure Calico networking. (9 retries left).
FAILED - RETRYING: Configure Calico networking. (8 retries left).
changed: [k8s-control]
```
However it looks like the 7th try didn't fail.  I'm at a loss what's going on.  I'll look maybe something is wrong with calico?


