# rbenv installer & doctor scripts

## rbenv-installer

The `rbenv-installer` script idempotently installs or updates rbenv on your
system. If Homebrew is detected, installation will proceed using `brew
install/upgrade`. Otherwise, rbenv is installed under `~/.rbenv`.

Additionally, [ruby-build](https://github.com/rbenv/ruby-build#readme) is also
installed if `rbenv install` is not already available.

with curl:

  ```sh
  curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash
  ```

alternatively, with wget:

  ```sh
  wget -q https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer -O- | bash
  ```

> **Note**
The installer script is meant for casual use on your own development machine.
For automating installation across machines it's better to _avoid_ using this
script in favor of fine-tuning rbenv & ruby-build installation manually. Some
environments—such as container images meant for either Ruby development or
production—should not be switching between multiple Ruby versions at all, so if
you are installing rbenv there, you are likely doing something wrong.

## rbenv-doctor

You can verify the state of your rbenv installation with:

with curl:

  ```sh
  curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-doctor | bash
  ```

alternatively, with wget:

  ```sh
  wget -q https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-doctor -O- | bash
  ```
