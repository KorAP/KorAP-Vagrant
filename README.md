# KorAP-Vagrant

A Vagrantfile for the KorAP Corpus Analysis Platform

## Description

A Vagrantfile provides all that is required to run KorAP
in a virtual machine.

## Setup

As a prerequisite, you will need [Vagrant](https://www.vagrantup.com/)
and [VirtualBox](https://www.virtualbox.org/).

To initially create the KorAP VM run

```
$ git clone https://github.com/KorAP/KorAP-Vagrant
$ cd KorAP-Vagrant
$ vagrant up
```

This will eventually download the VM image, start the VM and
initialize the provision.
Please be patient - the first provision will take quite some time.

Afterwards you will have the KorAP user-interface available at
```http://localhost:5555``` on your machine.
The KorAP API will be available at
```http://localhost:5556```.
An example corpus as provided by [Kustvakt](https://github.com/KorAP/Kustvakt/)
will be available as well.

To stop the service, run

```
$ vagrant halt
```

To restart the service, run

```
$ vagrant up
```

To update the software, run

```
$ git pull origin master
$ vagrant provision
```

To log into the machine, run

$ vagrant ssh

## Development and License

Copyright (c) 2018, [IDS Mannheim](http://ids-mannheim.de/), Germany

KorAP-Vagrant is developed as part of the [KorAP](http://korap.ids-mannheim.de/)
Corpus Analysis Platform at the Institute for German Language
([IDS](http://ids-mannheim.de/)),
funded by the
[Leibniz-Gemeinschaft](http://www.leibniz-gemeinschaft.de/en/about-us/leibniz-competition/projekte-2011/2011-funding-line-2/).

KorAP-Vagrant is published under the
[BSD-2 License](https://raw.githubusercontent.com/KorAP/KorAP-Vagrant/master/LICENSE).
