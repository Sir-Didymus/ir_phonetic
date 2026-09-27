# Phonetic XKB Keyboard Layout for Persian

This is an [XKB](https://wiki.archlinux.org/title/X_keyboard_extension) layout
for typing Persian (Farsi) phonetically on Linux.

The existing XKB layouts assume a Persian keyboard, which is impractical for
people typing with US (or similar) keyboards.

## Setup (Wayland)

On Wayland the compositor builds the keymap itself, using `libxkbcommon`, which
searches `~/.config/xkb` before the system directory. So no root is needed, and
package updates to `xkeyboard-config` cannot wipe the layout:

```shell
mkdir -p ~/.config/xkb/symbols
cp ir_phonetic ~/.config/xkb/symbols/
```

Verify it compiles:

```shell
xkbcli compile-keymap --layout ir_phonetic > /dev/null && echo ok
```

Then tell your compositor to use it. No input method (IBus, fcitx5) is involved
— a plain XKB layout is handled entirely by the compositor.

### Hyprland

In the `input` section of your Hyprland config, list the layout alongside your
primary one and pick a switching shortcut:

```ini
input {
    kb_layout = de,ir_phonetic
    kb_variant = ,basic
    kb_options = grp:alt_shift_toggle
}
```

You can also switch imperatively with
`hyprctl switchxkblayout <device> next`.

### Other compositors

Sway uses `input * xkb_layout "de,ir_phonetic"`. GNOME and KDE read the layout
registry rather than accepting arbitrary names, so they additionally need the
`evdev.xml` entry described under [Setup (X11)](#setup-x11).

## Setup (X11)

Under X11 the keymap is built by the X server, which only ever reads
`/usr/share/X11/xkb`. The layout therefore has to be installed system-wide, and
will need reinstalling after `xkeyboard-config` updates.

- Copy the `ir_phonetic` file into `/usr/share/X11/xkb/symbols`.
- Add the following to `/usr/share/X11/xkb/rules/evdev.xml` to end of the
  `<LayoutList>` section:

  ```xml
    <layout>
      <configItem>
        <name>ir_phonetic</name>
        <shortDescription>ir_ph</shortDescription>
        <description>Persian (Phonetic)</description>
        <languageList>
          <iso639Id>fas</iso639Id>
        </languageList>
      </configItem>
      <variantList/>
    </layout>
  ```

This `evdev.xml` entry is a registry read by graphical layout pickers. Wayland
compositors that take a layout name directly, such as Hyprland and Sway, do not
need it.

### IBus

Only relevant on X11. IBus `xkb:*` entries are not input methods; they are
shims that ask the X server to switch layout. They do nothing on Wayland,
because IBus has no way to change the compositor's keymap.

Copy the following to the `<engines>` section of
`/usr/share/ibus/component/simple.xml`:

```xml
<engine>
    <name>xkb:ir_phonetic:basic:fas</name>
    <language>fa</language>
    <license>GPL</license>
    <author></author>
    <layout>ir_phonetic</layout>
    <layout_variant>basic</layout_variant>
    <longname>Persian (Phonetic)</longname>
    <description>Persian (Phonetic)</description>
    <icon>ibus-keyboard</icon>
    <rank>1</rank>
</engine>
```

Make sure to turn off `Use system keyboard layout` in the
IBus settings (`ibus-setup`).

Then, clear the cache and restart IBus:

```shell
rm -rf ~/.cache/ibus/bus
ibus restart
```

## Credit

This layout was copied from
[jahanshiri.ir](https://www.jahanshiri.ir/keyboard/phonetic/en/), and is mostly
the same as the one described there.
The only difference is that I omitted mappings that emit multiple Unicode
characters for a single key stroke, because that is impossible with XKB.
