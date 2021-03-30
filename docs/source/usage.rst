.. include:: substitutions

.. |auth_kube_config| replace:: :ref:`auth_kube_config`

Usage
#####

This section will briefly introduce you to how you can use a kubernetes cluster for some basic tasks.

prerequisites
+++++++++++++

- You have a user with authentication to the cluster and have created your kubectl config from |auth_kube_config|.
- You have `kubectl <https://kubernetes.io/docs/tasks/tools/>`_ installed and accessible.
- You have a cluster to connect to.

Testing your kube-config
++++++++++++++++++++++++

Before worrying about making your generated config file permenant you can test it works by making a simple request with it by using the --kubeconfig flag like:

:|bash shell|_ example; test your kubectl config:

.. parsed-literal::

  kubectl --kubeconfig ``path/to/kube/config`` get nodes

Where you should see an output like:

.. parsed-literal::

  NAME     STATUS   ROLES                  AGE   VERSION
  ``host``    Ready    control-plane,master   28d   v1.20.2

If you get this output you are all set to continue if not you will need to check:

- that the kube-api server is actually alive and listening
- that the kube-api server is accessible from your machine
- that you are connecting to the correct kube-api server which you have your certificate signed by previously
- that your kubectl config is correct/ free of errors, and actually contains the base 64 encoded strings of your certificates or if not atleast paths to where those certificates can be found
- that you have the correct permissions to access what you are trying to access e.g the correct namespace
