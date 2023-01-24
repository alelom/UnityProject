# UnityProject-template

After cloning, remember to add the following to the `.git/config` file, **setting the right path to the YAML tool**:

```yaml
[merge]
	tool = unityyamlmerge

[mergetool "unityyamlmerge"]
	trustExitCode = false
	cmd = 'D:/Unity/Editor/2021.3.16f1/Editor/Data/Tools/UnityYAMLMerge.exe' merge -p "$BASE" "$REMOTE" "$LOCAL" "$MERGED"
```
