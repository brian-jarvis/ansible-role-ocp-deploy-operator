Role Name
=========

Deploy an operator from the OpenShift OperatorHub by specifying the `PackageManifest` name.

Requirements
------------

Operator `PackageManifest` must be listed in the output of `oc get packagemanifest` command.

Role Variables
--------------

Required Vars:
  - op_namespace: The namespace to deploy the operator
  - pck_name: The name of the `PackageManifest` to deploy.  List of available `PackageManifests` can be found by running `oc get packagemanifest`
  - pck_channelName: [optional] Specify the channel version to deploy, defaults to the `defaultChannel` listed in the `PackageManifest`
  - op_ns_labels: [optional] Dictionary list of labels to apply to the namespace.
  - op_ns_annotations: [optional] Dictionary list of annotations to apply to the namespace.
  - rbac: [optional] Set Role and Rolebinding 
  - operatorgroup: [optional] Set spec on the `OperatorGroup`.
  - additional_resource_templates: [optional] List of template file to process to create `CustomResourceDefinitions`.  `Kind` in template is compared with listed types in `PackageManifest` for the operator being deployed.

Dependencies
------------

Need to have access to the KUBECONFIG to connect to the cluster, along with the k8s ansible module

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

- name: Cluster Logging Operator Deploy
  hosts: all
  gather_facts: true
  tasks:
  - name: Install Elastic Search Operator
    include_role:
      name: ocp-deploy-operator
    vars:
      op_namespace: "openshift-operators-redhat"
      pck_name: "elasticsearch-operator"
      op_ns_lables:
        openshift.io/cluster-logging: true
        openshift.io/cluster-monitoring: true
      rbac:
        set_role: true
        role: "{{ elastic_role }}"
        rolebinding_subject: 
          kind: "ServiceAccount"
          name: "{{ elastic_role.name }}"
      

  - name: Install Logging Operator
    include_role:
      name: ocp-deploy-operator
    vars:
      op_namespace: "openshift-logging"
      pck_name: "cluster-logging"
      op_ns_lables:
        openshift.io/cluster-logging: true
        openshift.io/cluster-monitoring: true
      operatorgroup:
        spec:
          targetNamespaces: 
            - openshift-logging
      additional_resource_templates:
        - crd_clusterlogging.yml.j2

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
