# blender-addons-repo
The online repository for my personal Blender Extensions

# Installation - Remote Extension

* Copy the following script to automatically add the repository, name, access token and module name correctly to your Blender User Preferences

```python
import bpy

def add_remote_repo(name, remote_url: str, module: str = '', source = "USER", sync_on_startup=False, allow_duplicates=False) -> tuple[bpy.types.UserExtensionRepoCollection, int] | None:
    print(f"Adding Remote Repository: {name}")
    
    prefs_extensions = bpy.context.preferences.extensions

    repo = prefs_extensions.repos.new(name=name, module=module, source=source, remote_url=remote_url)
    
    if not allow_duplicates:
        name_split = repo.name.split('.')
        end = name_split[-1]
        # Try to convert to int, meaning 001 = 1.
        # If it fails, then it's likely that it doesn't exist, since it didn't break it up to end in '.00x'
        try:
            int(end)
            print("This repository is a duplicate. Deleting.")
            bpy.types.UserExtensionRepoCollection.remove(repo)
            return None
        except ValueError as e:
            print("This repository is unique.")
            
    if sync_on_startup:
        print(f"Setting repo {name} to sync on startup.")
        repo.use_sync_on_startup = True
        
    repo_index = len(prefs_extensions.repos) - 1

    print(f"{name} index: {repo_index}")
    
    # Set the newly added repo as the active (selected) repo
    prefs_extensions.active_repo = repo_index
    
    # Sync repo to remove to retrieve data
    bpy.ops.extensions.repo_sync_all(use_active_only=True)
    
    return repo, repo_index
            

def main():
    ret = add_remote_repo(
        "r0Tools Extensions",
        "https://raw.githubusercontent.com/r0fld4nc3/blender-addons-repo/refs/heads/main/release/index.json",
        module = "r0tools_extensions",
        sync_on_startup=True
    )

    if ret:
        repo, repo_index = ret[0], ret[1]

        # Bring up the User Preferences window and pre-search the addon name
        bpy.ops.screen.userpref_show()
        
        bpy.context.preferences.active_section = 'EXTENSIONS'
        
        bpy.data.window_managers["WinMan"].extension_search = "r0tools"
        
        # Instal the package from the repository
        bpy.ops.extensions.package_install(repo_index=repo_index, pkg_id="r0tools_simple_toolbox")
    
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

# Active Repositories
* [r0Tools - Simple Toolbox](https://github.com/r0fld4nc3/blender-r0-simple-toolbox)
