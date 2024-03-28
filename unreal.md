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

## Style Guide

This is the standard style guide: https://github.com/Allar/ue5-style-guide/tree/v2

I'm okay with it generally except for the following things:

- Variables/properties should be lower_snake_case. I don't understand the weirdness of PascalCase for everything. So dumb.
- Asset names SHOULD NOT start with a prefix like SK_ and BP_ and all this nonsense. It's an out of date practice. UE5 is much better with telling you what sort of file it is and filtering.
- All project-content needs to be in a top-level directory named "_". This is because all of the packs and plugins and what not want to be in the Content folder, so we need to separate out the custom project code. Until unreal lets us properly separate content packs / plugins / modules / whatever.
- The projects and plugins should have a 1-4 letter initialism that all classes are prefixed with, along with an underscore like SOL_PlayerCharacter. This is because C++ sucks and Unreal expects all classes to have a unique name, wtf. Until unreal properly supports C++20 modules or namespaces.
- If there's a well established example like in a staff tutorial on https://dev.epicgames.com/community/ -- do it that way. Not because it's better but because it's much easier to package cognatively. But like, adjust the style.

## LLM Usage

ChatGPT has been kind of useful, but it's made a lot of mistakes. I don't know. I still have to run around looking for examples of which there never are enough.

## Can We Do Better than C++?

No. Thanks for asking.

I mean, clearly there's a lot of things we could do:

- Use Zig modules
- Connect to Rust somehow
- Generate code with Python
- Use a transpiler

Sure but the problem is Unreal's massive library so auto-complete and intellisense is hard. And example code we find would be useless.

## Blueprints

I have some issues with blueprints, my main one is they don't serialize well, but I just found this website that saves them -- https://blueprintue.com/blueprint/6na3dkeq/ -- Super cool. We need more snippet sites. Snippets are the future.

We still don't have a good way to serialize the blueprints as they go. This means it's hard to use blueprints between projects.

The best strategy is to create C++ scripts that expose blueprint functions to do the things we want. We know this.
