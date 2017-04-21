Installing SIMP using r10k or Code Manager
==========================================

.. contents:: Contents:
  :local:

:term:`r10k` and :term:`Code Manager` are products that automate the development
and deployment of a :term:`Puppet` infrastructure. SIMP supports the usage of these
tools, with a little tweaking.

Read the introduction documentation on whichever of these technologies that is
being used:

  * Code Manager: https://docs.puppet.com/pe/latest/code_mgr.html
  * r10k: https://github.com/puppetlabs/r10k/blob/master/README.mkd

.. NOTE::
  r10k will be used to reference both r10k itself and Code Manager throughout
  this document. If you are using Code Manager, skip to `Setting up your Control Repo`_

.. IMPORTANT::
  This document will assumes the SIMP server has internet access.
  If your system does not have Internet access, you will need to adjust any paths
  to point to your internal mirrors.

.. NOTE::
   This method does *not* modify your system's partitioning scheme or
   encryption scheme to meet any regulatory policies. If you want an example of
   what that should look like either see the :ref:`simp-installation-guide` or
   check out the `Kickstart`_ files in the `simp-core Git repository`_.

System Requirements
^^^^^^^^^^^^^^^^^^^
.. include:: Yum_Repos.rst

Installation of r10k
~~~~~~~~~~~~~~~~~~~~

On the system intended to be the Puppet server, run the following command to
install the ``r10k`` ruby gem into the vendor ruby that comes with the
```puppet-agent`` AIO package:

.. code-block:: bash

  $ /opt/puppetlabs/puppet/bin/gem install r10k

``r10k`` can be used by calling the absolute path of the executable (unless
added to ``$PATH``):

.. code-block:: bash

  $ /opt/puppetlabs/puppet/bin/r10k help

.. include:: Creating_A_SIMP_Control_Repo.rst

.. include:: Alternate_Module_Path.rst

Minimum classes for classification
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Server
------
.. WARNING::
  These instructions are for SIMP users who are not using Puppet Enterprise. Please refer to :ref:`install-simp-on-pe` for detailed documentation

Because SIMP will manage the puppetserver, just include the following classes:

* ``simp``
* ``simp::server``
* ``pupmod::master``

Agents
------

Other agent will require the ``simp`` class or profile at a minimum. Other
classes or profiles may be included on top of this baseline for the desired
functionality. Check the ``simp`` scenario for a full list of classes.


Running Puppet for the first time
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

SIMP doesn't configure the puppetserver to listen on the typical port and CA
port, so the first time the puppet agent is run, you may have to specify the
``ca_port`` and ``server``. An example:

.. code-block:: bash

  $ puppet agent -t --ca_port 8141 --server puppet.your.domain

SIMP also provides a provisioning script called `runpuppet`_. Run this script
during provisioning and it will (provided autosign is configured) attempt to
connect to your puppetserver as defined in ``simp_options`` and run puppet a few
times in order to get the new system in order.

.. WARNING::
  SIMP, by default, implements ``tcpwrappers`` and PAM access restrictions.
  The root user should always be able to log in at a console, but if there is no
  console, like in `AWS`_, be sure to add a user to the PAM whitelist and give
  it sudo powers:

    .. code-block:: puppet

      pam::access::rule { 'ec2user':
        origins    => ['ALL'],
        permission => '+',
        users      => ['ec2user']
      }
      sudo::user_specification { 'ec2user':
        user_list => ['ec2user'],
        cmnd      => ['ALL']
      }

  SIMP also moves the location of the ssh authorized_keys file to
  ``/etc/ssh/local_keys/%u``, so copy it there bofore logging out.


Notes about SIMP Infrastructure
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

SIMP, when installed from the ISO, moves packages into ``/var/www/yum`` and
creates a ``yum`` repo in itself. SIMP modules, notably the ``simp::yum`` class,
assumes this. You will have to set ``simp::yum::os_update_url`` to a CentOS
Updates URL.


.. _AWS: https://aws.amazon.com/
.. _documentation of a control repo online: https://docs.puppet.com/pe/latest/cmgmt_control_repo.html
.. _Kickstart: http://pykickstart.readthedocs.io/en/latest
.. _runpuppet: https://github.com/simp/pupmod-simp-simp/blob/master/manifests/server/kickstart/runpuppet.pp
.. _simp-core Git repository: https://github.com/simp/simp-core/tree/master/build/distributions/CentOS/7/x86_64/DVD/ks
.. _simp-core: https://github.com/simp/simp-core/
.. _documentation of a control repo online: https://docs.puppet.com/pe/latest/cmgmt_control_repo.html
