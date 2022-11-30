---
title: Kali inside UTM (Guest VM)
description:
icon:
weight: 216
author: ["gamb1t",]
---

{{% notice info %}}
It should be noted that if another hypervisor is available, it would be best to use that. UTM is quite slow compared to other Mac hypervisors.
{{% /notice %}}

To install and set up a UTM VM it is pretty straightforward. We first download [UTM](https://mac.getutm.app/) and then launch the app. After this we can click create VM and start the short process:

![](utm-setup-2.png)

We will select Virtualize, as we will be using a [Kali Apple M1 installer](/get-kali/#kali-installer-images) iso which will bring us to the next page:

![](utm-setup-3.png)

From here, we will select "Other" which will again bring us to the next page:

![](utm-setup-4.png)

Now we are only going to change one item, which is selecting our "Boot ISO Image" and choosing the ISO we downloaded previously. Once that is done we can hit continue:

![](utm-setup-5.png)

We won't change any of the settings here, and will just continue forward:

![](utm-setup-6.png)

Here we will be sure to lower or raise the size of the drive to what we will need. In our case, we will be lowering it to 20GB and continue:

![](utm-setup-7.png)

We again will skip changing anything on this page, however if you want a shared directory feel free to add it:

![](utm-setup-8.png)

Now we are at the summary and we can re-name our VM to be "Kali Linux". Once complete, we will hit save and then press the settings button. Due to a bug in the current UTM version, we will have to install Kali in console only mode.

![](utm-setup-9.png)

From here we can add the device "Serial" under "Devices":

![](utm-setup-10.png)
![](utm-setup-10.5.png)

Now we can save and press play on the VM. Once prompted, we select "Install":

![](utm-setup-11.png)

Once the install is finished we can go back to the settings and remove the "Serial" device by right-clicking and selecting "Remove":

![](utm-setup-12.png)

We can now use our VM like normal.
