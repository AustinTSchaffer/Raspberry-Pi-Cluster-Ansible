# Local Pi Cluster

This repository contains an Ansible playbook for setting up and managing
a cluster of Raspberry Pis 4s.

The Raspberry Pis were flashed using the Raspberry Pi Imager tool, using
"Ubuntu 23.10" as the base OS. Some of the configuration weirdness is to
account for (undo) some of the work that Cloud Init is doing.

## TODO and Ideas

- [ ] Can linux expose prometheus metrics?
- [ ] DNS on controller node instead of editing hosts files?
- [ ] Set up one of the nodes as a bridged wifi router. Use it to sniff traffic from wifi-enabled devices.

## Mistakes

This section contains a list of individual mistakes that I made while messing
around with this Raspberry Pi cluster. Each mistake generally required that I
reflash the cluster, because generally I've considered that to be faster compared
top 

- I'm using MacOS as my daily driver, which makes it hard (impossible) to
  mount the cluster's SD cards and edit any files that I messed with.
- I skipped the image verification step once. This didn't immediately require
  a reflash, but some of the SD cards are already showing signs of wear and tear.
- One time I didn't set a password for my user account, which meant I wasted
  a ton of time trying to log in to an individual node to fix a broken network
  configuration.
- On one iteration I selected the wrong architecture for the Ubuntu Server image
  (32-bit vs 64-bit).
- One one iteration I selected the latest version of Ubuntu Server instead of the
  latest LTS version. I spent 2 hours trying to fix the PoE+ hat fans, which were
  running full blast at 40C.
- Setting up the ansible scripts and deployment environment such that it was
  not possible to easily fix common mistakes.

### Networking Mistakes

The most common category of mistake that I made while setting up this cluster
was networking mistakes. These resulted in me being unable to connect to the
hosts remotely.

- Setting configurations which were being overwritten by Cloud Init.
- Malformed netplan config file (missing a `:`).
- Remotely disconnecting the wifi interface before setting up the ethernet
  interface and wifi interface, resulting in no network connectivity across
  all hosts.
- Setting a default route in the netplan config file which was outside of
  the cluster's subnet. I assumed that I could use a `/29` subnet mask for
  my cluster without a piece of hardware which lives at the first address in
  that range.
- Using netplan apply instead of netplan try assumes that you got the networking
  configuration right on the first try.
