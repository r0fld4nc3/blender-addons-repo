# blender-addons-repo
The online repository for my personal Blender Extensions

# Installation - Remote Extension

* Copy the following script to automatically add the repository, name, access token and module name correctly to your Blender User Preferences

```python
import os
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
    try:
        bpy.ops.extensions.repo_sync_all(use_active_only=True)
    except RuntimeError as e:
        print(f"Error triggering repo sync all. Likely no internet connection or no online access allowed: {e}")
    
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

    try:
        # Get the filename of the current script
        filename = os.path.basename(__file__)

        # Find and remove the text with that name
        if filename in bpy.data.texts:
            bpy.data.texts.remove(bpy.data.texts[filename])
    except Exception as e:
        print(f"Error in removing/unlinking script text file: {e}")
    
    return True

main()
```

* Go to Blender
* Open Blender's Text Editor or go to Scripting tab
* Click `New`
* Paste the code
* Hit the run arrow
* It should open the `User Preferences` panel on the `Get Extensions` tab.
* The code and file will remove themselves automatically.
* The search parameter is filled automatically
* You now have the repository and version where all future addon updates will be added. Simply install your desired addon(s) from the list.

**Note:** If "nothing" happened, it is likely because the repository is already registered and so there is nothing to be done.
 

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
