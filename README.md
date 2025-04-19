# Monolithic vs plugin-based file conversion command-line tools

Many of the most popular file conversion utilities with a command-line
interface (CLI) take the shape of one "monolithic" tool, with a great number of
supported input and output formats baked in but no possibility to add other
formats via plugins.

I'd been puzzled by this, considering that such tools seem like "obvious"
candidates for a plugin-based architecture. Here are some findings after
actually trying to make a plugin-based tool.

## Comparison table

<table>
  <tr>
    <th>Topic</th>
    <th>Monolithic</th>
    <th>Plugin-based</th>
  </tr>
  <tr>
    <th>Examples</th>
    <td>Pandoc, ImageMagick convert, ffmpeg<sup>1</sup></td>
    <td>?</td>
  </tr>
  <tr>
    <th>Ease of contributing new formats</th>
    <td>
      ❌ High barrier: Must go through maintainer of main repo, may take a
      while to review, may be rejected for niche or in-development formats
    </td>
    <td>
      ✅ Low barrier: Can publish plugins completely independent of main repo
    </td>
  </tr>
  <tr>
    <th>Download size / installed size on user system</th>
    <td>
      ❌ Potentially large to cover all possible formats
    </td>
    <td>
      ✅ Entirely controllable by user, who may choose to only install needed
      plugins or opt for a "full" installation containing all/most of them
    </td>
  </tr>
  <tr>
    <th>CLI option collisions</th>
    <td>
      ✅ Not possible because all options are known & maintained in main repo
    </td>
    <td>
      ❌ Possible unless plugin authors cooperate (e.g. noting down option
      names they use in central registry) or unwieldy "fully-qualified"
      option names with unique prefixes are used (e.g. using centrally
      registered names: PyPI package names, GitHub <code>user/repo</code>
      strings, etc.)
    </td>
  </tr>
  <tr>
    <th>CLI options used by more than one format</th>
    <td>✅ Easily introduced (same reason as above)</td>
    <td>
      ❌ What is the "source of truth" for their CLI help text etc.? Main repo
      means we're back to monolithic architecture, format-specific plugin repos
      seems arbitrary & may introduce conflicts. Only "clean" place is separate
      plugin that <em>only</em> exists to introduce the option; quite unwieldy
    </td>
  </tr>
  <tr>
    <th>Intermediate representation</th>
    <td>✅ Can be altered at will if required by certain formats</td>
    <td>
      ❌ Must either be very carefully chosen to cover all possible plugin
      needs, contain complicated extension mechanisms, or suffer from the usual
      choice of working around a subpar format vs breaking
      backwards-compatibility
    </td>
  </tr>
  <tr>
    <th>Reproducibility for CLI users</th>
    <td>
      ✅ Relatively simple for them to ensure: pin version of executable
    </td>
    <td>
      ❌ Must pin version of tool itself <em>plus</em> all installed plugins;
      depending on exact plugin mechanism, mere presence of another plugin
      may alter behavior, so reproduction must ensure no <em>additional</em>
      plugins are present
    </td>
  </tr>
  <tr>
    <th>User effort to install new format plugin</th>
    <td>
      ✅ Not applicable since not plugin-based
    </td>
    <td>
      ❌ Some effort required if "minimal" installation chosen;
      <br>
      0️⃣ Usually small, especially when using language with plugin mechanism
      built into packaging ecosystem (e.g. Python entrypoints, making plugins
      installed via Pip etc. automatically discoverable)
    </td>
  </tr>
</table>

<sup>1 ffmpeg does seem to have support for
[external libraries](https://ffmpeg.org/general.html#External-libraries)
that add formats, but they must be baked in at compile-time.</sup>
