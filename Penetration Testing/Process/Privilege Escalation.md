

## User Privileges

Another critical aspect to look for after gaining access to a server is the privileges available to the user we have access to. Suppose we are allowed to run specific commands as root (or as another user). In that case, we may be able to escalate our privileges to root/system users or gain access as a different user. Below are some common ways to exploit certain user privileges:

1. Sudo
2. SUID
3. Windows Token Privileges

The `sudo` command in Linux allows a user to execute commands as a different user. It is usually used to allow lower privileged users to execute commands as root without giving them access to the root user. This is generally done as specific commands can only be run as root 'like `tcpdump`' or allow the user to access certain root-only directories. We can check what `sudo` privileges we have with the `sudo -l` command:

  Privilege Escalation

```shell-session
0xscary@htb[/htb]$ sudo -l
```

The above output says that we can run all commands with `sudo`, which gives us complete access, and we can use the `su` command with `sudo` to switch to the root user:

  Privilege Escalation

```shell-session
0xscary@htb[/htb]$ sudo su -
```
## PrivEsc Checklists
excellent checklist for both [Linux](https://book.hacktricks.wiki/en/linux-hardening/linux-privilege-escalation-checklist.html) and [Windows](https://book.hacktricks.wiki/en/windows-hardening/checklist-windows-privilege-escalation.html) local privilege escalation


Once we find a particular application we can run with `sudo`, we can look for ways to exploit it to get a shell as the root user. [GTFOBins](https://gtfobins.github.io/) contains a list of commands and how they can be exploited through `sudo`. We can search for the application we have `sudo` privilege over, and if it exists, it may tell us the exact command we should execute to gain root access using the `sudo` privilege we have.

[LOLBAS](https://lolbas-project.github.io/#) also contains a list of Windows applications which we may be able to leverage to perform certain functions, like downloading files or executing commands in the context of a privileged user.

## Scheduled Tasks

In both Linux and Windows, there are methods to have scripts run at specific intervals to carry out a task. Some examples are having an anti-virus scan running every hour or a backup script that runs every 30 minutes. There are usually two ways to take advantage of scheduled tasks (Windows) or cron jobs (Linux) to escalate our privileges:

1. Add new scheduled tasks/cron jobs
2. Trick them to execute a malicious software

The easiest way is to check if we are allowed to add new scheduled tasks. In Linux, a common form of maintaining scheduled tasks is through `Cron Jobs`. There are specific directories that we may be able to utilize to add new cron jobs if we have the `write` permissions over them. These include:

1. `/etc/crontab`
2. `/etc/cron.d`
3. `/var/spool/cron/crontabs/root`

## Exposed Credentials

Next, we can look for files we can read and see if they contain any exposed credentials. This is very common with `configuration` files, `log` files, and user history files (`bash_history` in Linux and `PSReadLine` in Windows). The enumeration scripts we discussed at the beginning usually look for potential passwords in files and provide them to us, as below:

  Privilege Escalation

```shell-session
...SNIP...
[+] Searching passwords in config PHP files
[+] Finding passwords inside logs (limit 70)
...SNIP...
/var/www/html/config.php: $conn = new mysqli(localhost, 'db_user', 'password123');
```

As we can see, the database password '`password123`' is exposed, which would allow us to log in to the local `mysql` databases and look for interesting information. We may also check for `Password Reuse`, as the system user may have used their password for the databases, which may allow us to use the same password to switch to that user, as follows:

  Privilege Escalation

```shell-session
0xscary@htb[/htb]$ su -

Password: password123
whoami

root
```

We may also use the user credentials to `ssh` into the server as that user.

## SSH Keys

Finally, let us discuss SSH keys. If we have read access over the `.ssh` directory for a specific user, we may read their private ssh keys found in `/home/user/.ssh/id_rsa` or `/root/.ssh/id_rsa`, and use it to log in to the server. If we can read the `/root/.ssh/` directory and can read the `id_rsa` file, we can copy it to our machine and use the `-i` flag to log in with it:

  Privilege Escalation

```shell-session
0xscary@htb[/htb]$ vim id_rsa
0xscary@htb[/htb]$ chmod 600 id_rsa
0xscary@htb[/htb]$ ssh root@10.10.10.10 -i id_rsa

root@10.10.10.10#
```

**If we find ourselves with write access to a users`/.ssh/` directory**, we can place our public key in the user's ssh directory at `/home/user/.ssh/authorized_keys`. This technique is usually used to gain ssh access after gaining a shell as that user. The current SSH configuration will not accept keys written by other users, so it will only work if we have already gained control over that user. We must first create a new key with `ssh-keygen` and the `-f` flag to specify the output file:

  Privilege Escalation

```shell-session
0xscary@htb[/htb]$ ssh-keygen -f key
```

This will give us two files: `key` (which we will use with `ssh -i`) and `key.pub`, which we will copy to the remote machine. Let us copy `key.pub`, then on the remote machine, we will add it into `/root/.ssh/authorized_keys`:

  Privilege Escalation

```shell-session
user@remotehost$ echo "ssh-rsa AAAAB...SNIP...M= user@parrot" >> /root/.ssh/authorized_keys
```

Now, the remote server should allow us to log in as that user by using our private key:

  Privilege Escalation

```shell-session
0xscary@htb[/htb]$ ssh root@10.10.10.10 -i key
```


Yes — **absolutely.** Making it a habit to run commands like:

bash

CopyEdit

`find / -type f -perm -o=r 2>/dev/null`

is **one of the most reliable privilege escalation techniques** during post-exploitation. Here's why and how to use it properly:

---

## ✅ Why Make It a Habit?

### 🔓 1. **Sensitive Info Leaks**

This helps find:

- SSH private keys (`id_rsa`)
    
- Configuration files with credentials
    
- Backup files (`.bak`, `.old`)
    
- Scripts with hardcoded passwords or API keys
    
- Databases or secrets (e.g., `.env`, `config.php`)
    

### 🛠️ 2. **Misconfigured File Permissions**

Even on hardened systems, admins sometimes:

- Give lax permissions to logs or scripts
    
- Temporarily open a file for debugging and forget to fix it
    
- Copy sensitive files to insecure locations
    

---

## 🔍 Better Versions of the Command

### 🔹 Show only interesting files (e.g., keys or passwords):

bash

CopyEdit

`find / -type f -perm -o=r \( -iname "*id_rsa*" -o -iname "*pass*" -o -iname "*.sql" -o -iname "*.log" \) 2>/dev/null`

### 🔹 Search for world-writable files (more dangerous):

bash

CopyEdit

`find / -type f -perm -o=w 2>/dev/null`

---

## 🧠 Pro Tip

Run these in the background or limit by directory if `/` is too large or slow:

bash

CopyEdit

`find /home -type f -perm -o=r 2>/dev/null find /etc -type f -perm -o=r 2>/dev/null`

Or use tools like:

bash

CopyEdit

`linpeas.sh` (dhoka diya isne ek bar)

Which automates all of this and more.

