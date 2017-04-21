Configuring Hiera
^^^^^^^^^^^^^^^^^

In order to configure a working SIMP environment in a control-repo, you will need to add some settings to hiera. If you do not have a hiera.yaml in the root of your control-repo, you can use Puppet’s environment layer feature and just create a hiera.yaml file with the following contents:

.. code-block:: yaml

  ---
  version: 4
  datadir: hieradata
  hierarchy:
    - name: 'Common'
      backend: yaml
      path: 'common'
    - name: "Compliance"
      backend: yaml
      path: 'compliance_profiles/%{::compliance_profile}'
    - name: "Scenario"
      backend: yaml
      path: 'scenarios/%{::simp_scenario}'
