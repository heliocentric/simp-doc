Creating or updating your Control Repo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A Control Repo contains the modules, hieradata, and roles/profiles required for
an infrastructure, and keeping it all in a git repo creates a workflow for
updating and developing on your Puppet infrastructure.

Change into the directory you're using from above and run ``git init`` to create
a control repo. Puppet, Inc has some great `documentation of a control repo online`_.

In order to use :term:`r10k` or :term:`Code Manager` with SIMP, you must create 
or update your :term:`Control repo` with the following components:

+-------------------+------------------------------------------------------------+
| File/Directory    |  Description of changes                                    |
+===================+============================================================+
| Puppetfile        | Add SIMP Puppet Modules                                    |
+-------------------+------------------------------------------------------------+
| environment.conf  | Configures the alternate module path for rsync and pki     |
+-------------------+------------------------------------------------------------+
| hieradata/        | Directory that needs to contain SIMP top-scope hiera data  |
+-------------------+------------------------------------------------------------+
| hiera.yaml        | Configured with simp::scenario settings and compliance map |
+-------------------+------------------------------------------------------------+

Adding SIMP Modules to the Puppetfile
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We keep an up-to-date Puppetfile in the core of our repo, which you can
download using this snippet:

.. code-block:: bash

  $ curl -o Puppetfile https://raw.githubusercontent.com/simp/simp-core/6.0.0-0/Puppetfile.stable

The example Puppetfile is labeled *stable*, meaning that the versions of the
modules it contains are the ones contained in the last SIMP release. You can go
to any previous release and download a Puppetfile with references to older
modules from the git history of the ``simp-core`` repo.

Our Puppetfile pulls down every dependency SIMP needs, which is currently
contains more than just modules. Open up the ``Puppetfile`` that was just
downloaded and remove the lines from ``moduledir 'src'`` to
``moduledir 'src/puppet/modules'``. That should just leave the Puppet modules.
