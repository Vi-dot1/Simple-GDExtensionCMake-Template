This repo has been built using with the info I could understand by reading the [godot-cpp repository](https://github.com/godotengine/godot-cpp) and [Godot docs](https://docs.godotengine.org/en/stable/tutorials/scripting/gdextension/gdextension_cpp_example.html).

Also from what I could learn from visiting [asmaloney's GDExtension template](https://github.com/asmaloney/GDExtensionTemplate), *(which i'll admit, I took some code from to generate the .gdextension file along with some snippets)*.

Made mostly for personal use, but I'll be happy if it results useful to someone, a more simple implementation with just the bare minimum to get something running; if you want something more robust, I recommend asmaloney's template 1000 times over this.

# Before you start

## Select your godot version

The godot-cpp repo has been added as a git submodule, by default I set it on Godot 4.4 (latest stable release as I'm writing this), you can change the version by switching the branch on the submodule directly:

```
   cd godot-cpp 
   git checkout <branch-name>
```

## Generating Godot bindings

In case you cannot use the bindings bundled in godot-cpp you can get the extension api directly from your godot binary. Just try to open the executable from the terminal:

```
    godot --dump-extension-api
```

You will get a file called `extension_api.json` on the same place your executable is, just drop it at the root of this folder and it will be used instead of the default one on godot-cpp.


# Working on your library

All the source files inside src are listed and added to the library automatically, you can classify in subfolders if you like, as long as it is a `.cpp` and is inside src it will be compiled and added to the library.

So is just a matter adding your files there and building your project.

## Building your library

When you build your library, it will be saved in `./build/bin/`
Along with it a proper `.gdextension` file is generated

The file is placed in a folder according to what system is targeted to, e.g if you're on windows, the file will be on `bin/lib/Windows/yourLibrary.dll`. 

This build system only has targets for Mac, Windows and Linux.

## Testing

Use the install command, this will automatically move the files to INSTALL_DIR, which by default is set in folder called demo inside the project, if you want it to be elsewhere just change the path the variable, or just make a project inside of that folder if you prefer it.

I'm planning to maybe use something that isn't the install routine itself but as I said, quick and dirty.
