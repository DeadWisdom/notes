## Create the VSStudio Project:

https://www.reddit.com/r/unrealengine/comments/nn2n7h/visual_studio_code_with_ue5/

`D:\Dev\UE_5.4\Engine\Binaries\DotNET\UnrealBuildTool\UnrealBuildTool.exe -projectfiles -project="D:\Dev\Solace\Solace.uproject" -game -rocket -progress -engine -VSCode`

## Gameplay Tags + Linker Errors

The GameplayTags module needs to be added to the Build.cs like:

`PublicDependencyModuleNames.AddRange(new string[] { "Core", "CoreUObject", "Engine", "InputCore", "EnhancedInput", "GameplayTags" });`

Create a data table with a GamePlayTagsRow schema named GameplayTags, add a JSON file with the same name "GameplayTags.json":

```json
[
  {"Name": "mechanics.thing.t", "Tag": "mechanics.thing.t", "DevComment": "Yep"}
]
```

Go into the project settings, Gameplay Tags, and add a the data table in "Gameplay Tag Table List"

## Live Coding

Set it so the panel always comes up (Edit->Editor Preferences->Live Coding) -- Then you can press CTL-ALT-F11 to recompile
