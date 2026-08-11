# flimkit-demo-plugin

A worked example of every [FLIMKit](https://github.com/FLIMKit/FLIMKit) add-on hook, in one file.

It is not useful on its own. It exists so you can see what an add-on looks like, check that add-ons load on your machine, and copy the shape of one when writing your own.

## What it registers

| Hook | What it adds |
|---|---|
| `@tool` | `Tools > Add-on Self Test...`, a window listing everything currently registered |
| `@tool` | `Tools > Add-on Demo > Nested Entry...`, showing that menu paths nest |
| `@tool` | `Tools > Add-on Demo > Break On Purpose...`, which raises so you can see the failure handling |
| `@file_format` | a `.demoflim` reader, so the extension is recognised everywhere a path is accepted |
| `@format_sniffer` | magic-byte detection of `DEMOFLIM`, for files without a known extension |
| `@phasor_filter` | a no-op filter, selectable wherever `gaussian` and `median` are |

The self-test window also counts how many times it has been opened into its own `plugin:demo_plugin` section of `~/.flimkit/config.json`, which is the per-add-on settings namespace.

## Installing

Requires FLIMKit 0.10.0 or newer, which is the release that added the add-on system.

As a package, which registers through the `flimkit.plugins` entry point:

```bash
pip install git+https://github.com/FLIMKit/flimkit-demo-plugin
```

Or as a single file, which needs no packaging at all:

```bash
mkdir -p ~/.flimkit/plugins
curl -o ~/.flimkit/plugins/demo_plugin.py \
  https://raw.githubusercontent.com/FLIMKit/flimkit-demo-plugin/main/flimkit_demo_plugin/__init__.py
```

The folder is off by default. Start FLIMKit and answer the prompt, or tick "Load from ~/.flimkit/plugins" in `File > Preferences > Plugins`, then restart. Installed packages are not gated that way, since `pip install` is already a deliberate act.

`Help > Plugins...` lists what loaded and what failed, and turns individual add-ons off.

## Writing your own

The whole contract is a decorated function:

```python
from flimkit.plugins import tool

FLIMKIT_PLUGIN_API = 1

@tool(id='my_tool', label='My Tool...', menu='Tools', order=500)
def open_my_tool(app):
    from tkinter import messagebox
    messagebox.showinfo('My Tool', 'Hello from an add-on.')
```

`id` has to be unique across everything loaded, `menu` is a slash path, and the function is called with the GUI object whose `app.root` is the Tk parent. Keep the tkinter import inside the body so the module still imports on a headless machine.

The v1 hooks are frozen: new ones get added, these keep their arguments and their meaning. The [Plugins section of the FLIMKit documentation](https://github.com/FLIMKit/FLIMKit/wiki/Plugins) is the reference.

## Licence

MIT, same as FLIMKit.
