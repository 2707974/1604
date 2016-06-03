
## Before you begin

1.  Familiarize yourself with our [Getting Started](/docs/getting-started) guide and complete the steps for setting your Linode's hostname and timezone.

2.  This guide will use `sudo` wherever possible. Complete the sections of our [Securing Your Server](/docs/security/securing-your-server) to create a standard user account, harden SSH access and remove unnecessary network services. Do **not** follow the Configure a Firewall section yet--this guide includes firewall rules specifically for an OpenVPN server.

3.  Update your system.

        sudo apt-get update && sudo apt-get upgrade



>
>This guide is written for a non-root user. Commands that require elevated privileges are prefixed with `sudo`. If you’re not familiar with the `sudo` command, you can check our [Users and Groups](/docs/tools-reference/linux-users-and-groups) guide.

| Left-Aligned | Centered | Right-Aligned | 
| ---------------- |:-------------:| -----------------:| 
| Columns,     | both          | headers        | 
| and              | line items, | are aligned   | 
| by the hypens | and colons | above.     |
