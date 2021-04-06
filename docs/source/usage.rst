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

If you do get a "Forbidden" message try using the following instead to check your permissions:

:|bash shell|_ example; test your kubectl config:

.. parsed-literal::

  kubectl --kubeconfig ``path/to/kube/config`` auth can-i get pods --as  ``USERNAME`` --namespace ``NAMESPACENAME``

This command will either tell you yes, or no. If it fails in giving you a yes or no then you aren't authenticating to the server at all, please check roles/ rolebindings/ certificates/ the kubectl config itself.

where:

- username is just what username the server expects I.E whatever it signed in the certificates, and has a role/ role-binding for
- namespacename is just what namespace you want to enact these commands in, for example you may only have permissions in a specific namespace. Try asking your administrator if you do not know what this is, but try using either your name or your teams name is a good place to start.

Making the Kubeconfig Permanent
+++++++++++++++++++++++++++++++

If this all works consider making your kubeconfig permanent, by putting it where kubectl looks for it by default on your OS. For example on Linux the default kubeconfig location is ``${HOME}/.kube/config`` if it is there then you have no need to keep issuing the --kubeconfig ``path/to/kube/config`` option each time you call kubectl.

see: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/
