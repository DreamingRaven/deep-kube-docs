.. include:: substitutions

NVIDIA-GPU
##########

Cluster Configuration
+++++++++++++++++++++

.. note::

  Clearly since this is cluster configuration this only applies to administrators. Normal users will have no need to follow this cluster configuration step.


To enable nvidia-container-toolking previously nvidia-docker, by editing /etc/docker/daemon.json since kubernetes does not support the docker --gpu option:

https://docs.nvidia.com/datacenter/cloud-native/kubernetes/dcgme2e.html#install-nvidia-container-toolkit-previously-nvidia-docker2

.. code-block:: json

  {
     "default-runtime": "nvidia",
     "runtimes": {
          "nvidia": {
              "path": "/usr/bin/nvidia-container-runtime",
              "runtimeArgs": []
        }
     }
  }

Then we need to install the nvidia operator:

.. code-block:: bash

  helm repo add nvdp https://nvidia.github.io/k8s-device-plugin \
   && helm repo update \
   && helm install --generate-name nvdp/nvidia-device-plugin

Pod Specification
+++++++++++++++++

Test Pods
+++++++++

Test pod for cuda functionality

:|manifest|_ example:

.. literalinclude:: ../../examples/cuda-add.yaml
   :language: yaml
   :linenos:

Test job for nvidia-smi

:|manifest|_ example:

.. literalinclude:: ../../examples/nvidia-smi-job.yaml
   :language: yaml
   :linenos:
