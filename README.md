# Phonetic XKB Keyboard Layout for Persian

This is an [XKB](https://wiki.archlinux.org/title/X_keyboard_extension) layout
for typing Persian (Farsi) phonetically on Linux.

The existing XKB layouts assume a Persian keyboard, which is impractical for
people typing with US (or similar) keyboards.

## Setup

- Download and copy the `ir_phonetic` file into
  `/usr/share/X11/xkb/symbols`.
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

### IBus

To make the layout work with IBus, copy the following to the `<engines>` section
of `/usr/share/ibus/component/simple.xml`:

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
