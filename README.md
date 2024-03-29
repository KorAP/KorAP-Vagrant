# KorAP-Vagrant

A Vagrantfile for the KorAP Corpus Analysis Platform

## Description

A Vagrantfile provides all that is required to run KorAP
in a virtual machine, independent of your operating system.

## Setup

As a prerequisite, you will need [Vagrant](https://www.vagrantup.com/)
and [VirtualBox](https://www.virtualbox.org/).

Then, either create a directory ```KorAP-Vagrant``` and put the
[Vagrant file](https://raw.githubusercontent.com/KorAP/KorAP-Vagrant/master/Vagrantfile) in there or clone the git repository

```
$ git clone https://github.com/KorAP/KorAP-Vagrant
```

To initially create the KorAP VM, change into the
```KorAP-Vagrant``` directory and start vagrant.

```
$ cd KorAP-Vagrant
$ vagrant up
```

This will eventually download the VM image, start the VM and
initialize the provision.
Please be patient - the first provision will take quite some time.

Afterwards you will have the KorAP user-interface available at
```http://localhost:5555``` on your machine.
An example corpus as provided by [Kustvakt](https://github.com/KorAP/Kustvakt/)
will be available as well.

In case of problems installing any of the components
due to network issues, restart the provision with

```
$ vagrant provision
```

To stop the service, run

```
$ vagrant halt
```

To restart the service, run

```
$ vagrant up
```

To update the software (in case, you cloned the git repository),
run

```
$ git pull origin master
$ vagrant provision
```

To log into the machine, run

```
$ vagrant ssh
```

## Development and License

Copyright (c) 2018-2021, [IDS Mannheim](https://www.ids-mannheim.de/), Germany

KorAP-Vagrant is developed as part of the [KorAP](https://korap.ids-mannheim.de/)
Corpus Analysis Platform at the Leibniz Institute for the German Language
([IDS](https://www.ids-mannheim.de/)),
funded by the
[Leibniz-Gemeinschaft](https://www.leibniz-gemeinschaft.de/).

KorAP-Vagrant is published under the
[BSD-2 License](https://raw.githubusercontent.com/KorAP/KorAP-Vagrant/master/LICENSE).
