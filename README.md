# Rider Avalonia plugin in shared project
Sample repository showing the problem with Avalonia in a Shared Project.

* Rider 2023.2.1
* Avalonia 11.0.2
* .NET 7.0

## XAML syntax highlighting and UI preview are not working
Open the `SharedProject/Window1.axaml` in Rider and it will not highlight the syntax, all will be gray, and the preview will just say "Preview isn't supported for this project". 

Initially it worked, after adding a "Shared Project" and adding a Avalonia window there. But after closing the solution and re-opening it, it does not work anymore. 

## Workaround

The problem looks to be in `SharedProject/SharedProject.projitems` where the build action for the `Window1.axaml` is set. If it is `AvaloniaXAML`, the application can be compiled and run, but the plugin does not highlight the XAML syntax, nor does it show the preview:
```xml
  <ItemGroup>
    <AvaloniaXAML Include="$(MSBuildThisFileDirectory)Window1.axaml" />
  </ItemGroup>
```
BTW the `AvaloniaXAML` must be set by hand, because the Avalonia CLI adds it as `None` by default, in a "Shared Project".

After changing this to `None`, the syntax highlighting starts working and the preview will show-up, if the app was compiled before this change: 
```xml
  <ItemGroup>
    <None Include="$(MSBuildThisFileDirectory)Window1.axaml" />
  </ItemGroup>
```

But now the compilation does not work and it complains about the `InitializeComponent()` in `Window1.axaml.cs` file:
```
Error CS0103 : The name 'InitializeComponent' does not exist in the current context
```
