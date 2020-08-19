# SELinux Policy for Synapse
This policy module provides support for [Synapse](https://github.com/matrix-org/synapse), the Matrix reference homeserver.

This policy targets the Gentoo SELinux policy. Currently, systemd support is not available.

This policy should be considered in its infancy or in a testing stage and thus not recommended for production use. However, all typical functionality has been confirmed working to include sending and receiving events over federation.

This policy has not been tested against a Synapse instance running multiple workers.

## Installation
```
# Clone the repository
git clone https://github.com/0xC0ncord/synapse-selinux.git

cd synapse-selinux

# Compile the module for the desired policy type (i.e. strict)
make -f /usr/share/selinux/<policy_type>/include/Makefile

# Install the SELinux policy module
semodile -i synapse.pp

# Set up port (depending on your configuration)
setsebool -P synapse_tcp_allow_bind_http_port on
# OR
setsebool -P synapse_tcp_allow_bind_http_cache_port on
# OR
semanage port -a -t synapse_port_t -p tcp 8010

# Allow postgresql connections (if using psql as a backend)
setsebool -P synapse_postgresql_connect on

# Restore all correct context labels
restorecon -RvF /var/lib/synapse
restorecon -vF /etc/init.d/synapse
# Make sure to run this against the correct Python version(s)
restorecon -vF /usr/lib/python-exec/python3.6/synctl
restorecon -RvF /var/log/synapse
restorecon -RvF /run/synapse

# Start synapse
/etc/init.d/synapse start

# Ensure synapse is running in the correct confined context
ps -eZ | grep synapse
```
