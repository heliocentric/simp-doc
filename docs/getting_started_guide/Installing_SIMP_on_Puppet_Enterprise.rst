.. _install-simp-on-pe:

Installing SIMP on Puppet Enterprise
====================================

.. contents:: Contents:
  :local:

Overview
^^^^^^^^^^^^^

As of 6.0, SIMP can now be installed on top of an existing
Puppet Enterprise installation. Once installed SIMP will 
configure itself to add the necessary firewall rules, and service
level exclusions based only on changes made to the PE Console.

Installing SIMP on top of Puppet Enterprise is a multi-step process, involving changes at the following places in your environment:

* control repo
* global hiera
* console classifier
* Some manual shell commands on the Master of Masters (MoM).

It is highly recommended to perform these steps in sequence to prevent loss of connectivity.

At this time multiple compile masters and PE HA is not supported out of the box in SIMP. We are currently working on a Technology Preview in succeeding 6.x versions to support HA configurations out of the box. At this time HA support requires manual configuration and only supports manual failover.

System Requirements
^^^^^^^^^^^^^^^^^^^

Puppet Enterprise
-----------------

The following versions of Puppet Enterprise are currently supported:

============  ==============  =============================================
 PE Version    SIMP Version    Notes
============  ==============  =============================================
 2016.4        6.0
 2016.5        6.0             SIMP on PE HA requires manual configuration
 2017.1        6.0             SIMP on PE HA requires manual configuration
============  ==============  =============================================

recommended to configure the replica master before installing SIMP. The replica provision process makes a lot of single time changes to the system to configure the replication, and SIMP’s security restrictions may prevent it from working properly.

.. include:: Yum_Repos.rst

Disclaimer
^^^^^^^^^^

SIMP will attempt to make minor compliance changes to Puppet
Enterprise on certain versions. However we have not made major changes
to bring it in-line with FIPS 140-2 to ensure that SIMP users who have
a valid support license do not invalidate their support by using SIMP.

XXX TODO Insert further information relevant to the disclaimer

Pre-install tasks
^^^^^^^^^^^^^^^^^

.. include:: Creating_A_SIMP_Control_Repo.rst

.. include:: Alternate_Module_Path.rst

.. include:: Configuring_Hiera.rst

XXX TODO Node Classification recommendations


