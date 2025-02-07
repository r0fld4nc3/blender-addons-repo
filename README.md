# blender-addons-repo
The online repository for my personal Blender Extensions

# Installation - Remote Extension
* Copy the following script to automatically add the repository, name, access token and module name correctly to your Blender User Preferences
```python
import bpy


def add_remote_repo(name, remote_url: str, module: str = '', source = "USER", allow_duplicates=False) -> bpy.types.UserExtensionRepoCollection | None:
    print(f"Adding Remote Repository: {name}")
    
    repo = bpy.types.UserExtensionRepoCollection.new(name=name, module=module, source=source, remote_url=remote_url)
    
    if not allow_duplicates:
        name_split = repo.name.split('.')
        end = name_split[-1]
        try:
            int(end)
            print("This repository is a duplicate. Deleting.")
            bpy.types.UserExtensionRepoCollection.remove(repo)
            return None
        except ValueError as e:
            print("This repository is unique.")
    
    return repo
            

def main():
    repo = add_remote_repo(
        "r0Tools Extensions",
        "https://raw.githubusercontent.com/r0fld4nc3/blender-addons-repo/refs/heads/main/release/index.json",
        module = "r0tools_extensions"
    )
    if repo:
        repo.use_sync_on_startup = True
        
        bpy.ops.screen.userpref_show()
        bpy.data.window_managers["WinMan"].extension_search = "Simple Toolbox"
    
    return True

main()
```

* Go to Blender
* Open Blender's Text Editor or go to Scripting tab
* Click `New`
* Paste the code
* Hit the run arrow
* It should open the `User Preferences` panel. Go to `Get Extensions`
* Install the toolbox addon again
* Delete the install script from the text editor
* You now have the repository and version where all future addon updates will be added. So every time I push an update, all you have to do is go to extensions and hit "Update" and that's it 
 

# Installation - Local Addon
* Go to [<> Code](https://github.com/r0fld4nc3/blender-addons-repo/archive/refs/heads/main.zip)
* Download the .zip file
* Unzip the archive. It should produce a folder called `blender-addons-repo-main`
* Enter the `blender-addons-repo-main` folder
* Enter the `release` folder
* In Blender > User Settings > Addons > Top Left Downward Arrow > Install from Disk... > Pick the .zip file(s)

OR

* Extracting and copy-pasting the folder(s) to your Blender's configuration .../scripts/addons folder.
