# rbenv installer & doctor scripts

## rbenv-installer

The `rbenv-installer` script idempotently installs or updates rbenv on your
system. If Homebrew is detected, installation will proceed using `brew
install/upgrade`. Otherwise, rbenv is installed under `~/.rbenv`.

Additionally, [ruby-build](https://github.com/rbenv/ruby-build#readme) is also
installed if `rbenv install` is not already available.

```sh
# with curl
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-installer | bash

# alternatively, with wget
wget -q https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-installer -O- | bash
```

Small caveat, for Ubuntu you are going to need to modify the PATH in your shell 
startup before running the installer. You can do this by configuring your `~/.bashrc`, `~/.zshrc`, or `~/.config/fish/config.fish`, with something like:

```
export PATH=$HOME/.rbenv/bin:$PATH
```

Also, after installing add this to your shell startup:

```
eval "$(rbenv init -)"
```

## rbenv-doctor

After the installation, a separate `rbenv-doctor` script is run to verify the
success of the installation and to detect common issues. You can run
`rbenv-doctor` on your machine separately to verify the state of your install:

```sh
# with curl
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor | bash

# alternatively, with wget
wget -q https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor -O- | bash
```
