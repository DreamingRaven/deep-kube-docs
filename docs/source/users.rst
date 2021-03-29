.. include:: substitutions

.. _csr-gen: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#x509-client-certs
.. |csr-gen| replace:: certificate signing request generation

User Authentication
###################

By default the Kube-api-server does not have user authentication. Instead you can use signed certificates to authenticate a user and the users group, as the kube-api-server will trust certificates signed by its own certificate-authority and has an internal mechanism known as certificate signing requests with which approval can be sought to sign certificates with the internal CA.cert.

Certificate as User Authentication Step-by-Step
===============================================

To have a certificate represent a user there are 3 requirements of the certificate:

- the CN (common name field) is populated with the username desired, or more specifically the username the administrator has/ expects for you with your roles/ role bindings.
- the O (organisation filed) is populated with the groups of the user that the administrator is expecting (can specify multiple).
- the certificate is signed by the kubernetes certificate authority (part of any kubernetes cluster)

To do this you must have completed the following |auth_prerequisites| -> |auth_private_key| ->  |auth_csr| -> |auth_kube_csr|.

.. |auth_prerequisites| replace:: :ref:`auth_prerequisites`

.. _auth_prerequisites:

Prerequisites
+++++++++++++


- OpenSSL

  - `Windows <https://stackoverflow.com/questions/50625283/how-to-install-openssl-in-windows-10/51757939#51757939>`_
  - `linux <https://wiki.archlinux.org/index.php/OpenSSL>`_
  - `osx <https://stackoverflow.com/questions/35129977/how-to-install-latest-version-of-openssl-mac-os-x-el-capitan>`_

.. |auth_private_key| replace:: :ref:`auth_private_key`

.. _auth_private_key:

Generate the Users Private Key
++++++++++++++++++++++++++++++

First to get this certificate to represent a user we need to generate a private key. This private key is a secret that only the user should have as for all intensive purposes it is the user when embedded in a certificate as far as the kube-api is concerned.

:|bash shell|_ example; Generating private key:

.. parsed-literal::

    openssl genrsa -out |username|.key 2048

Replace |username| with the desired |username|

.. |auth_csr| replace:: :ref:`auth_csr`

.. _auth_csr:

Generate a Certificate Signing Request
++++++++++++++++++++++++++++++++++++++

.. warning::

  The |username| and |group| (s) used here must match what the roles/ role bindings are expecting, or else they simply wont allow the user under RBAC to access anything even if they are accepted.

Now that we have the private key we want to use, we need to create a certificate signing request (CSR) to associate the private key with a certificate that contains all the additional information we want it to be related to like the common name, and organisation fields.

:|bash shell|_ example; |csr-gen|_:

.. parsed-literal::

    openssl req -new -key |username|.key -out |username|.csr -subj "/CN=username/O=group"

OR

:|bash shell|_ example; multi group |csr-gen|_:

.. parsed-literal::

    openssl req -new -key |username|.key -out |username|.csr -subj "/CN=username/O=group1/O=group2"

.. warning::

  Make sure you replace |username| and |group| with the correct/ desired |username| and |group| specified by your cluster administrator. (the last |username| and |group| are not highlighted as quoted)

.. note::
  It is advisable to check your now created CSR which should if you followed the above be called |username|.csr.
  You can check your CSR by:

  :|bash shell|_ example; inspect CSR:

  .. parsed-literal::

      openssl req --noout -text -in |username|.csr

  You should see something akin to the following towards the top:

  .. parsed-literal::

      Certificate Request:
        Data:
            Version: 1 (0x0)
            Subject: CN = |username|, O = |group|
            Subject Public Key Info:
                Public Key Algorithm: rsaEncryption
                    RSA Public-Key: (2048 bit)
                    Modulus:

  If these are not correct, create a new certificate now, otherwise you will be waiting for acceptance to only get rejected because they are incorrect later.

See also:

- |csr-gen|_

.. |auth_kube_csr| replace:: :ref:`auth_kube_csr`

.. _auth_kube_csr:

Use CSR in Kubernetes CSR.yaml to Request Signing by Administrator
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Now that we have a private-key, and a certificate signing request (.csr) file, we can now ask the kubernetes api to consider us for authentication, where an admin can accept/ decline us or a pre-defined rule can accept/ decline us.

To do so we need to define a short YAML file as seen below:

.. literalinclude:: ../../examples/username-csr.yaml
   :language: yaml
   :linenos:

We need to substitute our name or more specifically the name we used earlier in the CN field. If you have forgotten what it was or need to double check please refer to |auth_csr| which tells you how to inspect the certificate as well as generate it. We also need to insert our certificate in one line and Base64 encoded.

.. note::
   our CSR should be embedded in the above file next to request. This CSR should be embedded as Base64. If you are on linux you can use the following to get the properly formatted base64 string to insert:

  .. parsed-literal::

      cat |username|.csr | base64 | tr -d "\n"

Now that we have a CSR yaml file lets call it |csr|.yaml for consistency. We can submit this to kubernetes and await approval.
We can do this in two ways:

- Ask our administrator (or someone else with permissions) to submit the file to the api-server for approval
- Submit the file ourselves for approval to cut out the middleman
