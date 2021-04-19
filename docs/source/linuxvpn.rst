.. include:: substitutions

Remote VPN Connections With Linux
#################################

This section will guide you through connecting to the university VPN using AnyConnect on Linux. Windows users do not need to follow these instructions, but Mac users might.

Downloading Certificates
========================

In order to view the required certificates (on Firefox):

#. Open the university's `WebVPN service <https://remote.lincoln.ac.uk>`_ in your browser.
#. Click the padlock icon to the left of the URL.
#. Click the arrow to the right of "Connection Secure".
#. Click "More Information".
#. On the Security tab, click "View Certificate".

There should be three tabs at the top. We only need to concern ourselves with the two QuoVadis ones. For each, scroll down to a section called "Miscellaneous", and next to "Download", click "PEM (cert)". This will save each certificate to your system. The filename and save location don't matter.

Installing Certificates
=======================

You then need to open your terminal and :code:`cd` to where you saved your PEM files. For each PEM file, run the following command:

.. code ::

    sudo openssl x509 -in <CERTNAME>.pem -inform PEM -out <CERTNAME>.crt

Here :code:`<CERTNAME>` should be replaced with the name of the certificate. Again, the filenames do not matter, and you can create the CRT files with different names to the PEM files if you wish.

Next, make a new directory for your certificates and copy the CRT files into it (make sure you're only copying the two CRT files we just created to this directory):

.. code ::

    sudo mkdir /usr/share/ca-certificates/extra
    sudo cp *.crt /usr/share/ca-certificates/extra

Now we can install the certificates:

.. code ::

    sudo dpkg-reconfigure ca-certificates

On the screen that appears, select "ask". You should then be prompted with a list of certificates. The ones you copied over will not have asterisks next to them, so you need to navigate to them using the arrow keys, and press SPACE to select it. After you've selected them, press TAB, then RETURN to confirm your choice. If you see :code:`2 added, 0 removed; done.` anywhere in the subsequent output, it was successful.

You can now go to AnyConnect and connect as you would normally! It might take a few tries for it to work (normally 2, sometimes 3). If you successfully connect, but are then disconnected, try again -- it should work the second time. If you simply cannot connect at all, you may need to consult the IT department.

Testing your permissions
========================

If you are a professor, PhD student, or network administrator, you should be good to go. If you are an undergraduate student, you need to have an ad-hoc account created for you. You may also need to do this if you are a Masters student.

To test whether you have proper access run the following command while connected to the VPN:

.. code ::

    ping ausers03

If the output reads :code:`Temporary failure in name resolution`, you are not connected. Make sure you connected successfully before. If the output reads :code:`Destination port unreachable`, you need an ad-hoc account; see the section below. If the output seems normal, you should be good to go!

Creating an ad-hoc account
++++++++++++++++++++++++++

An ad-hoc account is one created for a student and verified by a professor in order to provide elevated access to the remote internals. You should contact the IT department, and forward the response to your project supervisor (if you are currently doing a dissertation) or your personal tutor. You should then have the correct permissions, but will need to connect to the VPN using this account rather than your student one.
