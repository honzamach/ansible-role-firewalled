.. _section-role-firewalled:

Role **firewalled**
================================================================================

Ansible role for managing UFW firewall on all target systems.

* `Ansible Galaxy page <https://galaxy.ansible.com/honzamach/firewalled>`__
* `GitHub repository <https://github.com/honzamach/ansible-role-firewalled>`__
* `Travis CI page <https://travis-ci.org/honzamach/ansible-role-firewalled>`__


Description
--------------------------------------------------------------------------------

This roles takes care of configuring and managing system UFW firewal. Additionally
following tasks are performed automagically:

* Access to SSH server will be configured for all user workstations.
* All hosts in inventory group **servers-monitored** will get the appropriate
  port opened for the appropriate list of monitoring nodes.

.. note::

    This role optionally requires the :ref:`secure registry <section-overview-secure-registry>`
    feature.

Requirements
--------------------------------------------------------------------------------

This role does not have any special requirements.


Dependencies
--------------------------------------------------------------------------------

This role dependent on following roles:

* :ref:`accounts <section-role-accounts>`

  Soft dependency.

* :ref:`monitored <section-role-monitored>`

  Soft dependency.

No other roles have direct dependency on this role.


Managed files
--------------------------------------------------------------------------------

This role directly manages content of following files on target system:

* ``/etc/syslog-ng/syslog-ng.conf``
* ``/etc/logrotate.d/apt``
* ``/etc/logrotate.d/aptitude``
* ``/etc/logrotate.d/dpkg``
* ``/etc/logrotate.d/syslog-ng``


Role variables
--------------------------------------------------------------------------------

There are following internal role variables defined in ``defaults/main.yml`` file,
that can be overriden and adjusted as needed:

.. envvar:: hm_firewalled__ssh_restrict_to_host

    Restrict SSH to listed hosts instead of unrestricted access

    * *Datatype:* ``boolean``
    * *Default value:* ``false``

hm_firewalled__ssh_port: 22

    Number of SSH port.

    * *Datatype:* ``integer``
    * *Default value:* ``22``

.. envvar:: hm_firewalled__open_ports

    Open custom ports to the whole world.

    * *Datatype:* ``list of integers``
    * *Default value:* ``[]`` (empty list)

.. envvar:: hm_firewalled__allow_hosts

    Open all ports for listed hosts.

    * *Datatype:* ``list of strings``
    * *Default value:* ``[]`` (empty list)

hm_firewalled__allow_workstations: []

    Open all ports for all workstations of listed users. Identifiers must point
    to valid entry in :envvar:`site_users` secret configuration structure.

    * *Datatype:* ``list of strings``
    * *Default value:* ``[]`` (empty list)

hm_firewalled__open_port_hosts: {}

    * *Datatype:* ``dict``
    * *Default value:* ``{}`` (empty dictionary)
    * *Example:*

    .. code-block: yaml

        # Open given ports for listed hosts
        hm_firewalled__open_port_hosts:
            8888:
                - 192.168.1.1
                - 2001::1

hm_firewalled__flush_and_reload

    Set this to true, when you need to completely flush and reload the whole firewall.
    Although there is no limitation in place, the recommended practice to use this
    feature is to give it only when really necesary via command line arguments::

       ansible-playbook ... --extra-vars '{"hm_firewalled__flush_and_reload":true}'

    * *Datatype:* ``boolean``
    * *Default value:* ``false``

Foreign variables
--------------------------------------------------------------------------------

This role uses following foreign variables defined in other roles:

:envvar:`hm_accounts__admins`

    Open the SSH port for the appropriate list of administrator workstations.

    * *Occurence:* **optional**

:envvar:`rb_accounts__users`

    Open the SSH port for the appropriate list of user workstations.

    * *Occurence:* **optional**

:envvar:`hm_monitored__service_port`

    Open the appropriate port for the appropriate list of monitoring nodes.

    * *Occurence:* **optional**

:envvar:`hm_monitored__allowed_hosts`

    Open the appropriate port for the appropriate list of monitoring nodes.

    * *Occurence:* **optional**


Usage and customization
--------------------------------------------------------------------------------

This role is (attempted to be) written according to the `Ansible best practices <https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html>`__. The default implementation should fit most users,
however you may customize it by tweaking default variables and providing custom
templates.


Variable customizations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Most of the usefull variables are defined in ``defaults/main.yml`` file, so they
can be easily overridden almost from `anywhere <https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable>`__.


Template customizations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This roles uses *with_first_found* mechanism for all of its templates. If you do
not like anything about built-in template files you may provide your own custom
templates. For now please see the role tasks for list of all checked paths for
each of the template files.


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


Example Playbook
--------------------------------------------------------------------------------

Example content of inventory file ``inventory``::

    [servers-firewalled]
    localhost

Example content of role playbook file ``playbook.yml``::

    - hosts: servers-firewalled
      remote_user: root
      roles:
        - role: honzamach.firewalled
      tags:
        - role-firewalled

Example usage::

    ansible-playbook -i inventory playbook.yml


License
--------------------------------------------------------------------------------

MIT


Author Information
--------------------------------------------------------------------------------

Jan Mach <honza.mach.ml@gmail.com>
