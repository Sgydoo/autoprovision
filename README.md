# Puppet Lunch Autoprovisioning Scripts

These are the VMware autoprovisioning scripts as referred to by
[Puppet Lunch][puppet-lunch]. Further details and context may be found in
[Chapter 4][chapter-4].

These tools have been designed to provision Puppet-managed CentOS or
RHEL virtual machines on a VMware ESX platform.

## The Scripts

There are three scripts here, but the main one is called 'hatch'. It
does not *require* the other two, but optional reconfiguration of the
provisioned VM will not be possible without the 'vmspec' script.

The 'mkbrood' script is simply a looping wrapper for 'hatch' for
provisioning multiple nodes with one command.

'hatch' is based on [a script by Ger Apeldoorn][hatch-origin]. Ger has
kindly permitted me to publish this enhanced and embellished version
on GitHub.

## Script Requirements

Full details and context may be found in the aforementioned [Chapter 4][chapter-4]
of puppetlunch.com, but here's a quick overview...

### hatch

A bash shell script which does the following:

* Clones a VMWare template
* Installs a Puppet Agent on the new machine
* Configures the network
* Reconfigures the new VM (with help from vmspec)
* Reboots the machine

In order for it to function, it needs several things:

1. VMware credentials in the running user's ~/.fog file
2. An existing VMware virtual machine template
3. The Puppet Enterprise installation tarball

The VMware template (CentOS or RHEL) also needs to be correctly configured:

* The public SSH key from the CP control node in /root/.ssh/authorized_keys.
* VMware-tools installed (necessary for powering on/off).
* A known static IP address.
* No entries in /etc/udev/rules.d/70-persistent-net.rules (Remove this file each time the template is booted for updates/maintenance etc).

The hatch script will work best when run as root, but should be adaptable to run as any user.

### vmspec

This is a Perl script which requires the VMware SDK for Perl. You can
get this here:

http://pubs.vmware.com/vsphere-51/index.jsp?topic=%2Fcom.vmware.perlsdk.install.doc%2Fcli_install.3.5.html

Installation and setup of the Perl SDK on CentOS 6 has been documented
in [Appendix D][appendix-d] of Puppet Lunch.

The script also requires a valid Credential Store. See the docs for
how to create one.

### mkbrood

Another Perl script. This is a wrapper for hatch, which allows
provisioning of multiple nodes with one command. Really, it's just a
looping wrapper script with a fancy YAML configuration file :)

Requires the Perl module YAML::Tiny (available in CentOS / RHEL as the
'perl-YAML-Tiny' package).

## Puppet Requirements

The hatch script has been designed with a Hiera-based node
classification in mind. To this end, it populates the new node with a
couple of custom facter facts:

* role
* hiera_environment

These are simply little snippets of YAML written to role.yaml and
hiera_environment.yaml in /etc/puppetlabs/facter/facts.d.

Of course Puppet allows you to set things up in any way you please,
and I'm aware that this may not be the way you do things, but it
serves to illustrate the flexibility of Puppet and how other
automation tools can interact with it. So you can either use this
method, or have fun doing it another way!


[puppet-lunch]: http://puppetlunch.com
[chapter-4]: http://puppetlunch.com/puppet/autoprovisioning.html
[appendix-d]: http://puppetlunch.com/puppet/vmware-perl-sdk.html
[hatch-origin]: http://puppetspecialist.nl/2012/12/single-command-server-deployment/
