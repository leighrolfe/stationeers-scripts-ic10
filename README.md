# Stationeers IC10 Scripts

IC10 scripts for programmable chips in [Stationeers](https://store.steampowered.com/app/544550/Stationeers/). Each script is a single `.ic10` file with its device pins, registers and tunable constants declared at the top.

## Usage

1. Open the script and read the header comment and the `alias` / `define` blocks.
2. Wire your devices to the pins listed under `# Device Aliases` (`d0`–`d5`), or edit the aliases to match your build.
3. Adjust the `define` constants (temperatures, pressures, offsets, device hashes).
4. Paste the script into the chip's editor in game.

## Large scripts and minifying

The in-game chip limits both the number of lines and the characters per script. These scripts favour readability (descriptive aliases, labels and comments), so a large script or one with long names may not fit. When that happens, minify it before pasting it in. [IC10 Minify](https://github.com/leighrolfe/vscodium-ic10-minify) is a VS Code / VSCodium extension that strips comments and whitespace, inlines register and device aliases, and shortens define and label names, without touching your original file.

## Scripts

| Script | Purpose |
| --- | --- |
| [`gas-generator-room-control.ic10`](gas-generator-room-control.ic10) | Keeps a gas generator room between 20 and 40 °C using fans and a heater, ramps fuel pressure to match temperature, and runs the generator only when the room is warm enough and batteries are below a set charge. Optional LED displays and proximity-activated power saving. |
| [`solar-panel-tracker-generic.ic10`](solar-panel-tracker-generic.ic10) | Points every solar panel type (standard and heavy, including dual and 1x5) at the sun using a daylight sensor. |
| [`printer-logic-sorter-setup.ic10`](printer-logic-sorter-setup.ic10) | One-off setup for a five-printer chain: configures five logic sorters to pass ingots to the next printer and send everything else to the stacker. Run once, then turn the chip off. |
| [`template.ic10`](template.ic10) | Starting skeleton following the conventions below. |
| [`ide-styles.ic10`](ide-styles.ic10) | Sample file for checking editor syntax-highlighting colours. Not a real script. |

### gas-generator-room-control

| Pin | Device |
| --- | --- |
| `d0` | Temperature sensor |
| `d1` | Gas pressure regulator |
| `d2` | Gas generator |
| `d3` / `d4` | Intake / exhaust vents |
| `d5` | Room heater |

- Batteries are read by type hash (`BatteriesNetwork`); change it to your battery type. With no batteries on the network, remove the battery check.
- LED displays and the proximity sensor are looked up by name hash. Use a labeller to name them `display-temp`, `display-pressure`, `display-charge`, `display-power` and `fuel-room-sensor-player`, or edit the defines.
- Lights of type `StructureLightLongAngled` and `StructureWallLight` on the same network follow the proximity sensor.

### solar-panel-tracker-generic

- Place a daylight sensor on `d0`.
- Set `HorizontalOffset` and `VerticalOffset` to match how your sensor is oriented.

## Using with an AI assistant

The repo includes an agent skill, [`stationeers-ic10-developer`](.agents/skills/stationeers-ic10-developer/SKILL.md), so you can have your preferred AI write, review or debug IC10 scripts in the same style as the ones here.

- **Tools that support agent skills:** open the repo (or copy the `stationeers-ic10-developer` folder into your tool's skills directory) and ask for an IC10 script. The skill is picked up when the task matches its description.
- **Anything else:** paste the contents of `SKILL.md` into the chat or your custom instructions, and attach `template.ic10` as an example.

Always test generated scripts in the in-game IC editor before relying on them.

## Editor setup

`.vscode/settings.json` holds syntax colours for the [Stationeers VS Code extension](https://marketplace.visualstudio.com/search?term=stationeers&target=VSCode).

## License

[MIT](LICENSE)
