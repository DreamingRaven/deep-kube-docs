.. include:: substitutions

Users
#####

By default the Kube-api-server does not have user authentication. Instead you can use signed certificates to authenticate a user and the users group, as the kube-api-server will trust certificates signed by its own certificate-authority and has an interman mechanism known as certificate signing requests with which approval can be sought to sign certificates with the internal CA.cert.

Certificate as User
===================

To have a certificate represent a user there are 3 requirements of the certificate:

- the CN (common name field) is populated with the username desired.
- the O (organisation filed) is populated with the groups of the user (can specify multiple).
- the certificate is signed by the kubernetes certificate authority (part of any kubernetes cluster)

Generate the Users certificate
++++++++++++++++++++++++++++++

:|bash shell|_ example; Generating private key\::

  .. parsed-literal::

      openssl genrsa -out |username|.key 2048


Generate a Certificate Signing Request
++++++++++++++++++++++++++++++++++++++

|bash shell|_ example; |csr-gen|_\:

  .. parsed-literal::

      openssl genrsa -out |username|.key 2048

See also:

- |csr-gen|_

Use CSR in Kubernetes CSR.yaml for Signing by CA
++++++++++++++++++++++++++++++++++++++++++++++++

:creation of database example:

  .. parsed-literal::

      mongod --config ./examples/configs/basic_mongo_config.yaml
