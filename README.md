# mdtohtml
*mdtohtml* is a simple tool that transform a mardowm file into a html file.  
For now, it only works on linux.  

*Behind the scene it uses pandoc*

## Installation

You need to install *pandoc* first.

```bash
# On Ubuntu (or any debian based distro) : 
sudo apt install pandoc

# On Archlinux :
sudo pacman -S pandoc

# On Fedora :
sudo dnf install pandoc
```

Then, just clone this git repo and exec the file *mdtohtml*.  
```bash
cd /where/you/want/to/install/this/tool
git clone https://github.com/Rrominet/mdtohtml.git
```
You might need to make executable depending of your filesystem.

If so : 
```bash
cd /where/you/cloned/this/repo
sudo chmod +x ./mdtohtml
```
> [!IMPORTANT]
> If you want to move the file `mdtohtml`, you need to move the file `md_template.html` with it. These files need to be in the same directory for the tool to correctly work.

> [!WARNING]
> Right now, `mdtohtml` use `/usr/local/bin/python3` to run.
> There are a lot of chances that your python version is different (often : `/usr/bin/python3`).
> If so change the first line of the file `mdtohtml` to `#!/usr/bin/python3` (or your python executable path)

## Basic Usage

The simplest way to use it :
```bash
mdtohtml your-md-file.md
```

This will create an html file named *your-md-file.html* with the HTML title *Your Md File*

You can also specify the title : 
```bash
mdtohtml your-md-file.md "Your Custom Title"
```

And lastly, you can also specify the output file :
```bash
mdtohtml your-md-file.md "Your Custom Title" your-custom-file.html
```

## Useful Usage

As it is, this tool is not very useful, you could do the same with pandoc before.  
It just has a simpler CLI interface.

It become interesting wen it's paired with the *save* event of your code editor.

What you can do is simply execute the command `mdtohtml you-current-file.md` on each save event to generate in real time you html version that you can preview in your browser.

> [!IMPORTANT]
> For the rest of the configuration to work you need to have added the path of the directory that contains *mdtohtml* to your PATH global environment variable.

### How setup *Visual Code* to do this : 

*Visual Code* does not support hooking a command on save by default but you can use the extention [Run on Save](https://marketplace.visualstudio.com/items?itemName=emeraldwalk.RunOnSave)

Once installed, add "emeraldwalk.runonsave" configuration to user or workspace settings.
Your config file should look like this:

```json 
{
...
  "emeraldwalk.runonsave": {
    "commands": [
      {
        "match": "your-md-file.md",
        "cmd": "mdtohtml '${file}'"
      }
    ]
  }
...
}
```

You can find more infos on the syntax of the matches [here](https://marketplace.visualstudio.com/items?itemName=emeraldwalk.RunOnSave).

### How setup *Sublime Text* to do this :

For *Sublime Text* you need to create a small plugin that will listen on the `save` event.  
To do this, create a .py file in the direcory `~/.config/sublime-text-3/Packages/User/`.  
The name don't matter. (be creative - lol)

Write in it: 
```python
import sublime, sublime_plugin

#This clearly could be place in a separate file for more flexibility
your_files = ["your-md-file.md", "your-other-md-file.md"] 

class AutoRunESLintOnSave(sublime_plugin.EventListener):
    def on_post_save_async(self, view):
        file_path = view.file_name()
        if not file_path:
            return
        NOT_FOUND = -1
        pos_dot = file_path.rfind(".")
        if pos_dot == NOT_FOUND:
            return
        for f in your_files:
            if f in file_path:
                view.window().run_command("mdtohtml " + file_path)
```

Should work. If not, let me know.

### How to setup *Neovim* to do this :

You can use my open source rplugin [hooks](https://github.com/Rrominet/nvim-hooks) for this.  
Once installed (the instructions for its installation are [here](https://github.com/Rrominet/nvim-hooks)), create the following file in `~/.config/nvim/hooks.json` :
```json
[
    {
        "event": "BufWritePost",
        "commands" : 
        [
            "mdtohtml $file"
        ], 

        "files" : 
        [
            "/path/to/your-md-file.md",
            "/path/to/your-md-file-2.md",
            "/path/to/your-md-file-3.md",
        ]
    }
]
```

Once your file is created/modified, you can reload it typing the command `:RealoadHooks`.
More infos on how the `hooks` rplugin works [here](https://github.com/Rrominet/nvim-hooks)
