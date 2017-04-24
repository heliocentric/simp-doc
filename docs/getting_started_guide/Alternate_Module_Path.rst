Adding the Alternate Module Path to environment.conf
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

SIMP uses an alternative module path, located at ``/var/simp/environments/<environment>``
and set in each environment's ``environment.conf``.

Change or add a modulepath directive like so:

.. code:: bash

  modulepath=modules:/var/simp/environments/<environment>/site_files:$basemodulepath

Creating Alternate Module Path
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Currently, it contains the rsync assets and PKI data, custom to each host. Here
is an example from a fresh install:

.. code-block:: bash

  $ tree -L 1 /var/simp/environments/production
  /var/simp/environments/production
  ├── rsync
  └── site_files

* ``rsync`` is a tree that stores data that will be copied over to modules. We
  have chosen rsync for these applications because of how it handles large files
  and large amounts of files. See :ref:`rsync_justification`.
* ``site_files`` is a place to store private files that may not belong in the
  control repo or another data source. And example of this would be host-based
  x509 certificates (which are used heavily by SIMP).

Create a puppet group to own the directories:
  groupadd puppet

Each of these directories need to be created manually, except rsync, which
requires a bit more work:

.. code-block:: bash

  export environment=production
  mkdir -p /var/simp/environments/${environment}/{site_files/modules/pki_files,files/keydist}
  chown root.puppet /var/simp/environments/${environment}/site_files
  chmod -R g+rX /var/simp/environments/${environment}/site_files

The rsync folder has a few tricks. When installed from an ISO, this data and
folder structure is laid out by the ``simp-rsync`` rpm. When installing using the
method described in this document, the git repo will have to be cloned and
manipulated:

.. code-block:: bash

  git clone https://github.com/simp/simp-rsync.git /tmp/simp-rsync
  mv -f /tmp/simp-rsync/environments/simp/rsync /var/simp/environments/${environment}/
  ln -s /var/simp/environments/${environment}/rsync/RedHat /var/simp/environments/${environment}/rsync/CentOS
  chmod u+rwx,g+rX,o+rX /var/simp/{environments,environments/${environment}}

Rsync's primary use case in a base SIMP infrastructure is to distribute ClamAV
databases. If ``simp_options::clamav`` is set to true, rsync and this step are
required.

Install ``clamav-update`` and download the latest database using the following
config and commands, replacing <environment> with your environment.

.. code-block:: bash

  $ cat << EOF > /tmp/freshclam.conf
  DatabaseDirectory /var/simp/environments/<environment>/rsync/Global/clamav
  DatabaseMirror database.clamav.net
  Bytecode yes
  EOF


.. code-block:: bash

  $ yum install -y clamav-update
  $ freshclam -u root --config-file=/tmp/freshclam.conf

Be careful when copying the first rsync environment around. There are hidden
files in each folder that represents and rsync share called ``.shares``. There
is a fact in the ``simp`` modules that checks for that files. The fact is then
ingested by ``simp::server::rsync_shares`` and rsync shares are created on the
Puppet server.
.. _simp-core Git repository: https://github.com/simp/simp-core/tree/master/build/distributions/CentOS/7/x86_64/DVD/ks
.. _simp-core: https://github.com/simp/simp-core/
.. _documentation of a control repo online: https://docs.puppet.com/pe/latest/cmgmt_control_repo.html
