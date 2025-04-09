#!/bin/bash
set -e

homebrew=
type -p brew >/dev/null && homebrew=1

if ! type -p git >/dev/null; then
  git() {
    echo "Error: git is required to proceed. Please install git and try again." >&2
    exit 1
  }
fi

rbenv="$(command -v rbenv ~/.rbenv/bin/rbenv | head -1)"

if [ -n "$rbenv" ]; then
  echo "rbenv already seems installed in \`$rbenv'."
  cd "${rbenv%/*}"

  if [ -x ./brew ]; then
    echo "Trying to update with Homebrew..."
    brew update >/dev/null
    if brew list rbenv | grep -q rbenv/HEAD; then
      brew reinstall rbenv
    else
      brew upgrade rbenv
    fi
  elif git remote -v 2>/dev/null | grep -q rbenv; then
    echo "Trying to update with git..."
    git pull --tags origin master
    cd ..
  fi
else
  if [ -n "$homebrew" ]; then
    echo "Installing rbenv with Homebrew..."
    brew update
    brew install rbenv
    rbenv="$(brew --prefix)/bin/rbenv"
  else
    echo "Installing rbenv with git..."
    mkdir -p ~/.rbenv
    cd ~/.rbenv
    git init
    git remote add -f -t master origin https://github.com/rbenv/rbenv.git
    git checkout -b master origin/master
    rbenv=~/.rbenv/bin/rbenv
  fi
fi

rbenv_root="$("$rbenv" root)"
ruby_build="$(command -v "$rbenv_root"/plugins/*/bin/rbenv-install rbenv-install | head -1)"

echo
if [ -n "$ruby_build" ]; then
  echo "\`rbenv install' command already available in \`$ruby_build'."
  cd "${ruby_build%/*}"

  if [ -x ./brew ]; then
    echo "Trying to update with Homebrew..."
    brew update >/dev/null
    brew upgrade ruby-build
  elif git remote -v 2>/dev/null | grep -q ruby-build; then
    echo "Trying to update with git..."
    git pull origin master
  fi
else
  if [ -n "$homebrew" ]; then
    echo "Installing ruby-build with Homebrew..."
    brew update
    brew install ruby-build
  else
    echo "Installing ruby-build with git..."
    mkdir -p "${rbenv_root}/plugins"
    git clone https://github.com/rbenv/ruby-build.git "${rbenv_root}/plugins/ruby-build"
  fi
fi

# Enable caching of rbenv-install downloads
mkdir -p "${rbenv_root}/cache"

# Detect the type of shell that invoked the installer:
shell="$(ps -p "$PPID" -o 'args=' 2>/dev/null || true)"
shell="${shell%% *}"
shell="${shell##-}"
shell="${shell:-$SHELL}"
shell="${shell##*/}"

case "$shell" in
bash | zsh| ksh | ksh93 | mksh | fish )
  # allow known shells to be configured
  ;;
* )
  # default to bash in case the parent process is not a known shell
  shell=bash
  ;;
esac

echo
echo "Setting up your shell with \`rbenv init $shell' ..."
"$rbenv" init "$shell"

echo
echo "All done! After reloading your terminal window, rbenv should be good to go."
