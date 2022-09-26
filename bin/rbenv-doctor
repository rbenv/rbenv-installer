#!/bin/bash
# Usage: rbenv doctor
# Summary: Detects common problems in rbenv installation

set -e
[ -n "$RBENV_DEBUG" ] && set -x

count-lines() {
  printf "%d" "$(wc -l)"
}

command-list() {
  # shellcheck disable=SC2230
  which -a "$1"
}

indent() {
  sed 's/^/  /'
}

# for ubuntu /bin is a symlink to /usr/bin
# which -a doesn't know the binaries are the same
fix-usr-bin() {
  sed 's:^/usr/bin/:/bin/:'
}

printc() {
  local color_name="color_$1"
  local msg="$2"
  printf "%s%s%s" "${!color_name}" "$msg" "$color_reset"
}

if [ -t 1 ]; then
  # shellcheck disable=SC2034
  color_red=$'\e[31m'
  # shellcheck disable=SC2034
  color_green=$'\e[32m'
  # shellcheck disable=SC2034
  color_yellow=$'\e[1;33m'
  # shellcheck disable=SC2034
  color_bright=$'\e[1;37m'
  color_reset=$'\e[0m'
else
  # shellcheck disable=SC2034
  color_red=""
  # shellcheck disable=SC2034
  color_green=""
  # shellcheck disable=SC2034
  color_yellow=""
  # shellcheck disable=SC2034
  color_bright=""
  color_reset=""
fi

warnings=0

echo -n "Checking for \`rbenv' in PATH: "
num_locations="$(command-list rbenv | fix-usr-bin | uniq | count-lines)"
if [ "$num_locations" -eq 0 ]; then
  printc red "not found"
  echo
  { if [ -x ~/.rbenv/bin/rbenv ]; then
      echo "You seem to have rbenv installed in \`$HOME/.rbenv/bin', but that"
      echo "directory is not present in PATH. Please add it to PATH by configuring"
      echo "your shell initialization files."
    else
      echo "Please refer to https://github.com/rbenv/rbenv#installation"
    fi
  } | indent
  exit 1
elif [ "$num_locations" -eq 1 ]; then
  printc green "$(command -v rbenv)"
  echo
else
  printc yellow "multiple"
  echo
  { echo "You seem to have multiple rbenv installs in the following locations."
    echo "Please pick just one installation and remove the others."
    echo
    command-list rbenv
  } | indent
  echo
  : $((warnings++))
fi

RBENV_ROOT="${RBENV_ROOT:-$(rbenv root)}"

echo -n "Checking for rbenv shims in PATH: "
shims_dir="${RBENV_ROOT}/shims"
found=""
precedence_problem=""
IFS=: read -r -a path <<<"$PATH" || true
for dir in "${path[@]}"; do
  if [ "$dir" = "$shims_dir" ]; then
    found=true
  elif [ -x "$dir"/ruby ] && [ -z "$found" ]; then
    precedence_problem="$dir"
  fi
done
if [ -n "$found" ]; then
  if [ -n "$precedence_problem" ]; then
    printc yellow "found at wrong position"
    echo
    { echo "The directory \`$shims_dir' is present in PATH, but is listed too late."
      echo "The Ruby version found in \`$precedence_problem' will have precedence. Please reorder your PATH."
    } | indent
    echo
    : $((warnings++))
  else
    printc green "OK"
    echo
  fi
else
  printc red "not found"
  echo
  { echo "The directory \`$shims_dir' must be present in PATH for rbenv to work."
    echo "Please run \`rbenv init' and follow the instructions."
  } | indent
  echo
  : $((warnings++))
fi

echo -n "Checking \`rbenv install' support: "
rbenv_installs="$({ ls "$RBENV_ROOT"/plugins/*/bin/rbenv-install 2>/dev/null || true
                    command-list rbenv-install 2>/dev/null || true
                  } | uniq)"
num_installs="$(count-lines <<<"$rbenv_installs")"
if [ -z "$rbenv_installs" ]; then
  printc red "not found"
  echo
  { echo "Unless you plan to add Ruby versions manually, you should install ruby-build."
    echo "Please refer to https://github.com/rbenv/ruby-build#installation"
  }
  echo
  : $((warnings++))
elif [ "$num_installs" -eq 1 ]; then
  printc green "$rbenv_installs"
  if [[ $rbenv_installs == "$RBENV_ROOT"/plugins/* ]]; then
    rbenv_install_cmd="${rbenv_installs##*/}"
    rbenv_install_version="$(rbenv "${rbenv_install_cmd#rbenv-}" --version || true)"
  else
    rbenv_install_version="$("$rbenv_installs" --version || true)"
  fi
  printf " (%s)\n" "$rbenv_install_version"
else
  printc yellow "multiple"
  echo
  { echo "You seem to have multiple \`rbenv-install' in the following locations."
    echo "Please pick just one installation and remove the others."
    echo
    echo "$rbenv_installs"
  } | indent
  echo
  : $((warnings++))
fi

echo -n "Counting installed Ruby versions: "
num_rubies="$(rbenv versions --bare | count-lines)"
if [ "$num_rubies" -eq 0 ]; then
  printc yellow "none"
  echo
  echo "There aren't any Ruby versions installed under \`$RBENV_ROOT/versions'." | indent
  [ "$num_installs" -eq 0 ] || {
    latest_version="$(rbenv install -l 2>/dev/null | grep -vFe - | tail -1)"
    [ -n "$latest_version" ] || latest_version="<version>"
    echo -n "You can install Ruby versions like so: "
    printc bright "rbenv install $latest_version"
    echo
  } | indent
else
  printc green "$num_rubies versions"
  echo
fi

echo -n "Auditing installed plugins: "
hooks_list="$(rbenv hooks exec || true)"
IFS=$'\n' read -r -d '' -a hooks <<<"$hooks_list" || true
plugin_broken=0
for hook in "${hooks[@]}"; do
  plugin_name=
  message=
  case "$hook" in
  */"~gem-rehash.bash" )
    plugin_name=rbenv-gem-rehash
    message="functionality is now included in rbenv core. Please remove the plugin."
    ;;
  */"bundler.bash" )
    plugin_name=rbenv-bundler
    message="is considered harmful. Please remove the plugin and \`rm -rf \$(rbenv root)/shims && rbenv rehash'."
    ;;
  esac

  if [ -n "$plugin_name" ]; then
    if [ "$((plugin_broken++))" -eq 0 ]; then
      printc yellow "warning"
      echo
    fi
    { printc bright "$plugin_name"
      echo " $message"
      echo "  (found in \`${hook}')"
    } | indent
    : $((warnings++))
  fi
done
if [ "$plugin_broken" -eq 0 ]; then
  printc green "OK"
  echo
fi

if [ "$warnings" -gt 0 ]; then
  exit 1
fi
