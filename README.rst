.. _section-role-firewalled:

Role **firewalled**
================================================================================

* `Ansible Galaxy page <https://galaxy.ansible.com/honzamach/firewalled>`__
* `GitHub repository <https://github.com/honzamach/ansible-role-firewalled>`__
* `Travis CI page <https://travis-ci.org/honzamach/ansible-role-firewalled>`__

This roles takes care of configuring and managing system `UFW <https://en.wikipedia.org/wiki/Uncomplicated_Firewall>`__
firewal. Additionally following tasks are performed automagically:

* Access to SSH server will be configured for all user workstations.
* All hosts in inventory group **servers_monitored** will get the appropriate
  port opened for the appropriate list of monitoring nodes.

**Table of Contents:**

* :ref:`section-role-firewalled-installation`
* :ref:`section-role-firewalled-dependencies`
* :ref:`section-role-firewalled-usage`
* :ref:`section-role-firewalled-variables`
* :ref:`section-role-firewalled-files`
* :ref:`section-role-firewalled-author`

This role is part of the `MSMS <https://github.com/honzamach/msms>`__ package.
Some common features are documented in its :ref:`manual <section-manual>`.


.. _section-role-firewalled-installation:

Installation
--------------------------------------------------------------------------------

To install the role `honzamach.firewalled <https://galaxy.ansible.com/honzamach/firewalled>`__
from `Ansible Galaxy <https://galaxy.ansible.com/>`__ please use variation of
following command::

    ansible-galaxy install honzamach.firewalled

To install the role directly from `GitHub <https://github.com>`__ by cloning the
`ansible-role-firewalled <https://github.com/honzamach/ansible-role-firewalled>`__
repository please use variation of following command::

    git clone https://github.com/honzamach/ansible-role-firewalled.git honzamach.firewalled

Currently the advantage of using direct Git cloning is the ability to easily update
the role when new version comes out.


.. _section-role-firewalled-dependencies:

Dependencies
--------------------------------------------------------------------------------

This role dependent on following roles:

* :ref:`accounts <section-role-accounts>` *[SOFT]*
* :ref:`monitored <section-role-monitored>` *[SOFT]*

No other roles have dependency on this role.


.. _section-role-firewalled-usage:

Usage
--------------------------------------------------------------------------------

Example content of inventory file ``inventory``::

    [servers_firewalled]
    your-server

Example content of role playbook file ``role_playbook.yml``::

    - hosts: servers_firewalled
      remote_user: root
      roles:
        - role: honzamach.firewalled
      tags:
        - role-firewalled

Example usage::

    # Run everything:
    ansible-playbook --ask-vault-pass --inventory inventory role_playbook.yml

    # Disable firewall, but keep existing rules:
    ansible-playbook --ask-vault-pass --inventory inventory role_playbook.yml --extra-vars '{"hm_firewalled__enabled":false}'

    # Flush and reload firewall rules:
    ansible-playbook --ask-vault-pass --inventory inventory role_playbook.yml --extra-vars '{"hm_firewalled__flush_and_reload":true}'

It is recommended to follow these configuration principles:

* Create/edit file ``inventory/group_vars/all/vars.yml`` and within define some sensible
  defaults for all your managed servers. Example::

        # Open listed ports to whole world.
        hm_firewalled__open_ports:
            - 22
            - 443

* Use files ``inventory/host_vars/[your-server]/vars.yml`` to customize settings
  for particular servers. Please see section :ref:`section-role-firewalled-variables`
  for all available options. Example::

        # Open given ports for listed hosts
        hm_firewalled__open_port_hosts:
          5432:
            - 192.168.1.1


.. _section-role-firewalled-variables:

Configuration variables
--------------------------------------------------------------------------------


Internal role variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. envvar:: hm_firewalled__install_packages

    List of packages defined separately for each linux distribution and package manager,
    that MUST be present on target system. Any package on this list will be installed on
    target host. This role currently recognizes only ``apt`` for ``debian``.

    * *Datatype:* ``dict``
    * *Default:* (please see YAML file ``defaults/main.yml``)
    * *Example:*

    .. code-block:: yaml

        hm_firewalled__install_packages:
          debian:
            apt:
              - ufw
              - ...

.. envvar:: hm_firewalled__enabled

    Enable/disable firewall completely for particular host.

    * *Datatype:* ``boolean``
    * *Default:* ``true``

.. envvar:: hm_firewalled__ssh_restrict_to_host

    Restrict SSH to listed hosts instead of unrestricted access.

    * *Datatype:* ``boolean``
    * *Default:* ``false``

.. envvar:: hm_firewalled__ssh_port

    Number for SSH port.

    * *Datatype:* ``integer``
    * *Default:* ``22``

.. envvar:: hm_firewalled__open_ports

    Open custom ports to the whole world.

    * *Datatype:* ``list of integers``
    * *Default:* ``[]`` (empty list)

.. envvar:: hm_firewalled__allow_hosts

    Open all ports for listed hosts.

    * *Datatype:* ``list of strings``
    * *Default:* ``[]`` (empty list)

.. envvar:: hm_firewalled__allow_workstations

    Open all ports for all workstations of listed users. Identifiers must point
    to valid entry in :envvar:`site_users` secret configuration structure.

    * *Datatype:* ``list of strings``
    * *Default:* ``[]`` (empty list)

.. envvar:: hm_firewalled__open_port_hosts

    Open given ports for listed hosts.

    * *Datatype:* ``dict``
    * *Default:* ``{}`` (empty dictionary)
    * *Example:*

    .. code-block: yaml

        # Open given ports for listed hosts
        hm_firewalled__open_port_hosts:
            8888:
                - 192.168.1.1
                - 2001::1

.. envvar:: hm_firewalled__flush_and_reload

    Set this to true, when you need to completely flush and reload the whole firewall.
    Although there is no limitation in place, the recommended practice to use this
    feature is to give it only when really necesary via command line arguments::

       ansible-playbook ... --extra-vars '{"hm_firewalled__flush_and_reload":true}'

    * *Datatype:* ``boolean``
    * *Default:* ``false``

Additionally this role makes use of following built-in Ansible variables:

.. envvar:: group_names

    See section *Group memberships* below for details.


Foreign variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:envvar:`hm_accounts__admins`

    Open the SSH port for the appropriate list of administrator workstations.

:envvar:`hm_accounts__users`

    Open the SSH port for the appropriate list of user workstations.

:envvar:`hm_monitored__service_port`

    Open the appropriate port for the appropriate list of monitoring nodes.

:envvar:`hm_monitored__allowed_hosts`

    Open the appropriate port for the appropriate list of monitoring nodes.


Built-in Ansible variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:envvar:`group_names`

    List of group names current host is member of. This variable is used to resolve
    :ref:`soft role dependencies <section-overview-role-soft-dependencies>`.

:envvar:`ansible_lsb['codename']`

    Linux distribution codename. It is used for :ref:`template customizations <section-overview-role-customize-templates>`.


Group memberships
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* **servers_monitored**

  In case the target server is member of this group firewall is automatically
  opened for list of monitoring nodes.


.. _section-role-firewalled-files:

Managed files
--------------------------------------------------------------------------------

.. note::

    This role supports the :ref:`template customization <section-overview-role-customize-templates>` feature.

This role manages content of following files on target system:

* ``/etc/syslog-ng/syslog-ng.conf`` *[TEMPLATE]*
* ``/etc/logrotate.d/apt`` *[TEMPLATE]*
* ``/etc/logrotate.d/aptitude`` *[TEMPLATE]*
* ``/etc/logrotate.d/dpkg`` *[TEMPLATE]*
* ``/etc/logrotate.d/syslog-ng`` *[TEMPLATE]*


.. _section-role-firewalled-author:

Author and license
--------------------------------------------------------------------------------

| *Copyright:* (C) since 2019 Honza Mach <honza.mach.ml@gmail.com>
| *Author:* Honza Mach <honza.mach.ml@gmail.com>
| Use of this role is governed by the MIT license, see LICENSE file.
|
