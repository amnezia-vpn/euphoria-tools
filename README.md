# [wireguard-tools](https://git.zx2c4.com/wireguard-tools/about/) &mdash; tools for configuring [WireGuard](https://www.wireguard.com/)

This supplies the main userspace tooling for using and configuring WireGuard
tunnels, including the
[`awg(8)`](https://git.zx2c4.com/wireguard-tools/about/src/man/wg.8) and
[`awg-quick(8)`](https://git.zx2c4.com/wireguard-tools/about/src/man/wg-quick.8)
utilities. This project supports Linux, OpenBSD, FreeBSD, macOS, Windows, and
Android.

**More information may be found at [WireGuard.com](https://www.wireguard.com/).**


## Euphoria Configuration
To implement Euphoria-specific configurations, follow these steps:

1. Prepare your Lua code containing two global functions with the following signatures:
    * `function d_gen(msg_type, data, counter)` returning the encoded data
    * `function d_parse(data)` returning the decoded data

2. Encode the entire Lua script using Base64 encoding.

3. Use the encoded string in the configuration as follows:

    * For the `set` command, use the `lua_codec` identifier
    * For the `setconf` command, use the `LuaCodec` identifier

For security reasons, the Base64 encoded string has a limit of 5MB. If you need to increase this you can pass the `MAX_AWG_LUA_CODEC_LEN` env variable when building like `make MAX_AWG_LUA_CODEC_LEN=1024`.

## Building

    $ cd src
    $ make

There are no dependencies other than a good C compiler and a sane libc.

## Installing

    # make install

This command takes into account several environment variables:

  * `PREFIX`               default: `/usr`
  * `DESTDIR`              default:
  * `BINDIR`               default: `$(PREFIX)/bin`
  * `LIBDIR`               default: `$(PREFIX)/lib`
  * `MANDIR`               default: `$(PREFIX)/share/man`
  * `BASHCOMPDIR`          default: `$(PREFIX)/share/bash-completion/completions`
  * `RUNSTATEDIR`          default: `/var/run`
  * `PKG_CONFIG`           default: `pkg-config`

  * `WITH_BASHCOMPLETION`  default: [auto-detect]
  * `WITH_WGQUICK`         default: [auto-detect]
  * `WITH_SYSTEMDUNITS`    default: [auto-detect]
  * `DEBUG`                default:

The first section is rather standard. The second section is not:

  * `WITH_BASHCOMPLETION` decides whether or not bash completion files for the
    tools are installed. This is just a nice thing for people who have bash.
    If you don't have bash, or don't want this, set the environment variable
    to `no`. If you'd like to force its use, even if bash-completion isn't
    detected in `DESTDIR`, then set it to `yes`.

  * `WITH_WGQUICK` decides whether or not the wg-quick(8) script is installed.
    This is a very quick and dirty bash script for reading a few extra
    variables from wg(8)-style configuration files, and automatically
    configures the interface. If you don't have bash, you probably don't want
    this at all. Likewise, if you already have a working network management
    tool or configuration, you probably want to integrate wg(8) or the direct
    WireGuard API into your network manager, rather than using wg-quick(8).
    But for folks who like simple quick and dirty scripts, this is nice. If you'd
    like to force its use, even if bash isn't detected in DESTDIR, then set it
    to `yes`.

  * `WITH_SYSTEMDUNITS` decides whether or not systemd units are installed for
    wg-quick(8). If you don't use systemd, you certainly don't want this, and
    should set it to `no`. If systemd isn't auto-detected, but you still would
    like to install it, set this to `yes`.

  * `DEBUG` decides whether to build with `-g`, when set to `yes`.

If you're a simple `make && make install` kind of user, you can get away with
not setting these variables and relying on the auto-detection. However, if
you're writing a package for a distro, you'll want to explicitly set these,
depending on what you want.

## `contrib/`

The `contrib/` subdirectory contains various scripts and examples. Most of these
are not immediately useful for production use, but should provide inspiration for
creating fully-featured tools. See the `README` in each directory.

## License

This project is released under the [GPLv2](COPYING).
