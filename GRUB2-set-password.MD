# Add GRUB2 password for non-default menu items

To add a password to GRUB2 on Debian Bookworm, while ensuring that the default booting doesn't require the password, you need to configure it so that the password is only required for certain actions, like entering the GRUB menu or editing boot options.

Follow these steps:

### 1. Generate a password hash
You will first need to generate a password hash for GRUB to use. Open a terminal and run the following command to generate the hash:

```bash
grub-mkpasswd-pbkdf2
```

You’ll be prompted to enter your desired password. After entering the password, it will generate a hash, which looks something like this:

```
PBKDF2 hash of your password is grub.pbkdf2.sha512.10000.123456789abcdef...
```

Copy this hash because you’ll need it later.

### 2. Edit the GRUB configuration
Now, you need to modify the GRUB configuration file to include the password.

Open the `/etc/grub.d/40_custom` file with a text editor:

```bash
sudo nano /etc/grub.d/40_custom
```

Add the following lines at the end of the file, replacing `grub.pbkdf2.sha512...` with the hash you copied earlier:

```bash
set superusers="root your_username"
password_pbkdf2 root grub.pbkdf2.sha512.10000.123456789abcdef...
password_pbkdf2 your_username grub.pbkdf2.sha512.10000.123456789abcdef...
```

Here, `your_username` can be any identifier you'd like to use (it doesn't have to be your actual Linux username).

### 3. Allow Grub to boot the default option without a password
To allow Grub to boot the default option without a password, but require a password for everything else, including editing the options, edit `/etc/grub.d/10_linux`, find the `menuentry` lines and add `--users ''` and `--unrestricted` like this:
```
if [ x$type != xsimple ] ; then
    # ...
    echo "menuentry '$(echo "$title" | grub_quote)' ${CLASS} --users '' \$menuentry_id_option 'gnulinux-$version-$type-$boot_device_id' {" | sed "s/^/$submenu_indentation/"
else
    echo "menuentry '$(echo "$os" | grub_quote)' ${CLASS} --unrestricted \$menuentry_id_option 'gnulinux-simple-$boot_device_id' {" | sed "s/^/$submenu_indentation/"
fi
```

### 4. Set GRUB to require the password only for certain actions
To ensure the password is only required when entering the GRUB menu or editing boot entries, you can modify the `/etc/default/grub` file.

Open the file:

```bash
sudo nano /etc/default/grub
```

Look for the line:

```bash
GRUB_TERMINAL=console
```

Make sure it's present and uncommented (if needed).

To ensure that the password is required only when entering the GRUB menu, set the following options:

```bash
GRUB_HIDDEN_TIMEOUT=0
GRUB_TIMEOUT=5
```

- `GRUB_HIDDEN_TIMEOUT=0` hides the GRUB menu by default (no password is asked for default boot).
- `GRUB_TIMEOUT=5` sets the timeout for GRUB to wait before booting the default kernel.

Save the file and exit the editor.


### 5. Update the GRUB configuration
Once the configuration is updated, regenerate the GRUB configuration to apply the changes. Run the following command:

```bash
sudo update-grub
```

### 6. Rebuild GRUB configuration again
Finally, run the following command again to update the GRUB configuration:

```bash
sudo update-grub
```
