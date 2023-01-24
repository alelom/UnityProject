# Using Git with Unity

### **What are the problems?**
* **Noise**: Unity editor has lots of temporary files that we don’t want git to track
* **Broken object references**: Unity keeps track of objects created by using random GUIDs, and if they aren’t tracked using .meta files then there can be conflicts that really break your project
* **Unresolvable merge conflicts**: files like scene files that are written in confusing languages (like YAML) that are supposed to be translations of Unity editor actions into code. Most likely, you cannot resolve using Git, and the only way to resolve merge conflicts is to open it in a text editor and resolve them manually while hoping you don't mess anything up because these files are confusing and not meant to be manipulated directly.
* **Large files**: Sometimes assets are large and take up a lot of storage space

## **💻 Project Setup**

A few things we can do to help prevent some of these problems:
1. Add Unity specific `.gitignore`
2. Configure Unity for version control
3. Use GitHub For Unity
4. Use Git Large File Storage

**Before Starting:** Make sure that your Unity project has a `.git` folder. If not, use Terminal or Command Prompt to go into the Unity project folder and run `git init`. Use `git remote add origin <git url>` to add the repo URL as a remote connection.


### **1. Adding a Unity Specific `.gitignore`**
* This is a nice `.gitignore` for unity: https://github.com/github/gitignore/blob/master/Unity.gitignore
* Add MacOS files that should be ignored if you are developing on a Mac: https://github.com/github/gitignore/blob/master/Global/macOS.gitignore
* Add Windows files that should be ignored if you are developing using Windows: https://github.com/github/gitignore/blob/master/Global/Windows.gitignore

### **2. Configuring Unity for Version Control**
1. Open the editor settings window.
    * `Edit > Project Settings > Editor`
2. Make .meta files visible to avoid broken object references.
   * `Version Control / Mode: “Visible Meta Files”`
3. Use plain text serialization to avoid unresolvable merge conflicts.
    * `Asset Serialization / Mode: “Force Text”`
4. Save your changes.
    * `File > Save Project`
    


### **3. Configuring Git to use Unity Smart Merge for merging**
Unity's Smart Merge (also called UnityYAMLMerge) is a tool shipped with the Unity editor. It is a pretty decent tool to use for resolving merge conflicts that would be really hard to merge maually (e.g. scene files). You can read more about it at the following links.

* https://docs.unity3d.com/Manual/SmartMerge.html
* https://www.reddit.com/r/Unity3D/comments/39bdq5/how_to_solve_scene_conflicts_with_unitys_smart/

Assuming Unity is installed in the standard location, the path to UnityYAMLMerge will be...

**on Windows:**
```
C:\Program Files\Unity\Editor\Data\Tools\UnityYAMLMerge.exe

or

C:\Program Files (x86)\Unity\Editor\Data\Tools\UnityYAMLMerge.exe
```

**on Mac OSX:**
```
/Applications/Unity/Unity.app/Contents/Tools/UnityYAMLMerge
```


🚀 **Important:** Add the following to your `.git` or `.gitconfig` file:
```
[merge]
tool = unityyamlmerge

[mergetool "unityyamlmerge"]
trustExitCode = false
cmd = '<path to UnityYAMLMerge>' merge -p "$BASE" "$REMOTE" "$LOCAL" "$MERGED"
```

Make sure that in your `.gitattributes` file, the following is present:

```
# For line ending normalization between Unix and Windows
* text=auto

# Unity 
*.cs diff=csharp text
*.cginc text
*.shader text

*.mat -text merge=unityyamlmerge diff
*.anim -text merge=unityyamlmerge diff
*.unity -text merge=unityyamlmerge diff
*.prefab -text merge=unityyamlmerge diff
*.physicsMaterial2D -text merge=unityyamlmerge diff
*.physicMaterial -text merge=unityyamlmerge diff
*.asset -text merge=unityyamlmerge diff
*.meta -text merge=unityyamlmerge diff
*.controller -text merge=unityyamlmerge diff
```

This tells git to use UnityYAMLMerge when resolving conflicts for meta files, scenes, prefabs, etc. This is based off of https://gist.github.com/nemotoo/b8a1c3a0f1225bb9231979f389fd4f3f.


### **4. Using Git Large File Storage System (Git LFS)**

Git LFS uses `.gitattributes` to track large files with git.

Instructions to install and use are [here](https://git-lfs.github.com/).

A good `.gitattributes` to use:

```
# 3D models
*.3dm filter=lfs diff=lfs merge=lfs -text
*.3ds filter=lfs diff=lfs merge=lfs -text
*.blend filter=lfs diff=lfs merge=lfs -text
*.c4d filter=lfs diff=lfs merge=lfs -text
*.collada filter=lfs diff=lfs merge=lfs -text
*.dae filter=lfs diff=lfs merge=lfs -text
*.dxf filter=lfs diff=lfs merge=lfs -text
*.fbx filter=lfs diff=lfs merge=lfs -text
*.jas filter=lfs diff=lfs merge=lfs -text
*.lws filter=lfs diff=lfs merge=lfs -text
*.lxo filter=lfs diff=lfs merge=lfs -text
*.ma filter=lfs diff=lfs merge=lfs -text
*.max filter=lfs diff=lfs merge=lfs -text
*.mb filter=lfs diff=lfs merge=lfs -text
*.obj filter=lfs diff=lfs merge=lfs -text
*.ply filter=lfs diff=lfs merge=lfs -text
*.skp filter=lfs diff=lfs merge=lfs -text
*.stl filter=lfs diff=lfs merge=lfs -text
*.ztl filter=lfs diff=lfs merge=lfs -text
# Audio
*.aif filter=lfs diff=lfs merge=lfs -text
*.aiff filter=lfs diff=lfs merge=lfs -text
*.it filter=lfs diff=lfs merge=lfs -text
*.mod filter=lfs diff=lfs merge=lfs -text
*.mp3 filter=lfs diff=lfs merge=lfs -text
*.ogg filter=lfs diff=lfs merge=lfs -text
*.s3m filter=lfs diff=lfs merge=lfs -text
*.wav filter=lfs diff=lfs merge=lfs -text
*.xm filter=lfs diff=lfs merge=lfs -text
# Fonts
*.otf filter=lfs diff=lfs merge=lfs -text
*.ttf filter=lfs diff=lfs merge=lfs -text
# Images
*.bmp filter=lfs diff=lfs merge=lfs -text
*.exr filter=lfs diff=lfs merge=lfs -text
*.gif filter=lfs diff=lfs merge=lfs -text
*.hdr filter=lfs diff=lfs merge=lfs -text
*.iff filter=lfs diff=lfs merge=lfs -text
*.jpeg filter=lfs diff=lfs merge=lfs -text
*.jpg filter=lfs diff=lfs merge=lfs -text
*.pict filter=lfs diff=lfs merge=lfs -text
*.png filter=lfs diff=lfs merge=lfs -text
*.psd filter=lfs diff=lfs merge=lfs -text
*.tga filter=lfs diff=lfs merge=lfs -text
*.tif filter=lfs diff=lfs merge=lfs -text
*.tiff filter=lfs diff=lfs merge=lfs -text
```


To reduce clutter on git when you are reviewing files on git (as YAML files are often not changeable or actionable on git), add this to the `.gitattributes` file:

```
# Collapse Unity-generated files on GitHub
*.asset linguist-generated
*.mat linguist-generated
*.meta linguist-generated
*.prefab linguist-generated
*.unity linguist-generated
```


🚀And so at the end of project setup, your entire `.gitattributes` should look like:

```
# For line ending normalization between Unix and Windows
* text=auto

# Unity 
*.cs diff=csharp text
*.cginc text
*.shader text

*.mat -text merge=unityyamlmerge diff
*.anim -text merge=unityyamlmerge diff
*.unity -text merge=unityyamlmerge diff
*.prefab -text merge=unityyamlmerge diff
*.physicsMaterial2D -text merge=unityyamlmerge diff
*.physicMaterial -text merge=unityyamlmerge diff
*.asset -text merge=unityyamlmerge diff
*.meta -text merge=unityyamlmerge diff
*.controller -text merge=unityyamlmerge diff

# 3D models
*.3dm filter=lfs diff=lfs merge=lfs -text
*.3ds filter=lfs diff=lfs merge=lfs -text
*.blend filter=lfs diff=lfs merge=lfs -text
*.c4d filter=lfs diff=lfs merge=lfs -text
*.collada filter=lfs diff=lfs merge=lfs -text
*.dae filter=lfs diff=lfs merge=lfs -text
*.dxf filter=lfs diff=lfs merge=lfs -text
*.fbx filter=lfs diff=lfs merge=lfs -text
*.jas filter=lfs diff=lfs merge=lfs -text
*.lws filter=lfs diff=lfs merge=lfs -text
*.lxo filter=lfs diff=lfs merge=lfs -text
*.ma filter=lfs diff=lfs merge=lfs -text
*.max filter=lfs diff=lfs merge=lfs -text
*.mb filter=lfs diff=lfs merge=lfs -text
*.obj filter=lfs diff=lfs merge=lfs -text
*.ply filter=lfs diff=lfs merge=lfs -text
*.skp filter=lfs diff=lfs merge=lfs -text
*.stl filter=lfs diff=lfs merge=lfs -text
*.ztl filter=lfs diff=lfs merge=lfs -text
# Audio
*.aif filter=lfs diff=lfs merge=lfs -text
*.aiff filter=lfs diff=lfs merge=lfs -text
*.it filter=lfs diff=lfs merge=lfs -text
*.mod filter=lfs diff=lfs merge=lfs -text
*.mp3 filter=lfs diff=lfs merge=lfs -text
*.ogg filter=lfs diff=lfs merge=lfs -text
*.s3m filter=lfs diff=lfs merge=lfs -text
*.wav filter=lfs diff=lfs merge=lfs -text
*.xm filter=lfs diff=lfs merge=lfs -text
# Fonts
*.otf filter=lfs diff=lfs merge=lfs -text
*.ttf filter=lfs diff=lfs merge=lfs -text
# Images
*.bmp filter=lfs diff=lfs merge=lfs -text
*.exr filter=lfs diff=lfs merge=lfs -text
*.gif filter=lfs diff=lfs merge=lfs -text
*.hdr filter=lfs diff=lfs merge=lfs -text
*.iff filter=lfs diff=lfs merge=lfs -text
*.jpeg filter=lfs diff=lfs merge=lfs -text
*.jpg filter=lfs diff=lfs merge=lfs -text
*.pict filter=lfs diff=lfs merge=lfs -text
*.png filter=lfs diff=lfs merge=lfs -text
*.psd filter=lfs diff=lfs merge=lfs -text
*.tga filter=lfs diff=lfs merge=lfs -text
*.tif filter=lfs diff=lfs merge=lfs -text
*.tiff filter=lfs diff=lfs merge=lfs -text

# Collapse Unity-generated files on GitHub
*.asset linguist-generated
*.mat linguist-generated
*.meta linguist-generated
*.prefab linguist-generated
*.unity linguist-generated
```

### **5. Using GitHub For Unity**

If you are uncomfortable with using git on the commandline/terminal, there is an asset called GitHub For Unity that can be downloaded and installed using Unity's Asset store.

A cool feature of GitHub For Unity that can help with Git Workflow is that you no longer need to use the command line. There is a nice visual editor inside Unity that will allow you to commit, push, pull, etc. Furthermore, with GitHub For Unity, you can lock specific files that you are working on (like scene or prefab files) so other people can't change them when you are working. (Wow the power!)

**To install GitHub For Unity:**

1. Make sure you are in Unity
2. Go to `Window -> Asset Store`
3. Search for GitHub for unity, install it, and import it into your project.

GitHub For Unity Guide: https://github.com/github-for-unity/Unity/blob/master/docs/using/quick-guide.md

🚀 You can bring up GitHub For Unity in the Unity editor by going to `Window->GitHub` in the Unity editor.

  
## **🤝 Git Workflow**

The best way to merge scene files is to **avoid merge conflicts in the first place**. Merge conflicts in YAML files are pretty much impossible to read and thus very, very difficult to manually resolve those conflicts. There are generally two ways we can avoid merge conflicts in scene files:

1. Break up large scenes into prefabs
2. Use Additive Scene Loading

### **1. Breaking up large scenes into prefabs**

We can break up scenes into many smaller areas and make these areas prefabs. Then as long as there is only one person working on an area prefab at a time, we can have multiple people working on the scene at once.

A video on this approach: https://www.youtube.com/watch?v=YgoCp2tzRh0

### **2. Using additive scene loading**

This approach is similar to the prefabs approach but instead of breaking up the scenes into different prefabs, we break up the scene into mini scenes and use additive scene loading to put them together at runtime. Scene loading may cause a dip in framerate temporarily though.

In this gist, there contains two scripts that will help you accomplish additive scene loading. `sceneLoader.cs` relies on `SceneReference.cs`, so you will need to put both scripts in your project. If you put an empty game object in your main scene and add `sceneLoader.cs` to the game object, you will be able to drag in the scenes you want to load into a list in the inspector for the script.

Note: `SceneReference.cs` is taken from this gist: https://gist.github.com/JohannesMP/ec7d3f0bcf167dab3d0d3bb480e0e07b.

### **GitHub Locking**
A cool feature of GitHub For Unity is that you can lock files right inside the Unity editor. If you choose to have a workflow where only one person can edit a scene at a time, you can lock the scene. 

🚀 Here is a guide on locks in GitHub: https://github.com/github-for-unity/Unity/blob/master/docs/using/locking-files.md. 

But if you didn't want to go to a separate webpage, here are the instructions:

* To view which scenes are locked, make sure the GitHub window is opened in Unity, then press the Locks tab in the GitHub window.
* To lock a scene, right click on the scene and click `Request Lock`
* To release a lock, there are 3 ways:  
    * Right click on the locked file and click `Release Lock`
    * From the GitHub tab under the Locks view, right-click to open the context menu and select to Release Lock
    * Select the file to unlock and go to select the menu option `Assets -> Release Lock`

**🚀 Note**: There are 2 options to release locks: `Release Lock` and `Release Lock (Forced)`. Always choose `Release Lock` first, as `Release Lock (Forced)` can release someone else's lock.

Make sure you release your locks after you are done working on them so other people can work on them too!

### ✏️ **Tips for Workflow**
1. **Communication**: As with all collaborative projects, communication is important, but with development in Unity, **communication is especially important**. Tools like UnityYAMLMerge and GitHub For Unity should make collaboration much easier, but it is still really important to communicate which scenes each person is working on. 
2. **Only Stage Actual Changes**: When you are doing `git add`, only add the files that you actually changed!
3. **Making New Branches**: When starting new features, to make sure that you are as up-to-date with master as possible, make new branches from the master branch and not other branches
4. **Smaller PRs**: General rule is that each PR should only have one new feature. Features in Unity often already have so many different changes and assets added, so to make it easier to review and have less potential for merge conflicts, avoid giant PRs.


## **⚔️ Resolving Merge Conflicts**

Hopefully with tools like UnityYAMLMerge and GitHub For Unity, manual merge conflict resolution will no longer be needed. But if things come to this, you can always open the file in Visual Studio or another text editor and manually resolve merge conflicts that way. This is **highly not recommended** because this method is error prone, as YAML files are difficult to understand and not meant to be manipulated directly.


### **Sources and More Information:**
* https://thoughtbot.com/blog/how-to-git-with-unity
* Video on Git with Unity for Game Development: https://www.youtube.com/watch?v=GmRPCl4MzCA
* How to Avoid Merge Conflicts in Unity: https://www.youtube.com/watch?v=YgoCp2tzRh0
* Unity Smart Merge (aka UnityYAMLMerge) manual: https://docs.unity3d.com/Manual/SmartMerge.html
* Reddit Post on UnityYAMLMerge: https://www.reddit.com/r/Unity3D/comments/39bdq5/how_to_solve_scene_conflicts_with_unitys_smart/
* GitHub For Unity Guide: https://github.com/github-for-unity/Unity/blob/master/docs/using/quick-guide.md