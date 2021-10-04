# plugins load failures demo

This reproduces the plugins load failures by following https://docs.microsoft.com/en-us/dotnet/core/tutorials/creating-app-with-plugin-support

only this changed:
1. ImplicitUsings has been commented out in the csproj files
2. pluginsPaths were made relative to the working directory (but as in the example, they are turned into full paths before being handed to PluginLoadContext and thus the assembly dependency resolver
3. changed to multi target so it is easy to compare 5.0 vs. 6.0 targets (reproduced first by manually setting the target in the csproj files, so it is not due to multi target)

To reproduce, with dotnet 6 rc1 sdk installed (change win-x64 to match your environment and \ to / if you are not running on windows):
```
dotnet publish .\HelloPlugin\ -c Release -f net6.0
dotnet publish AppWithPlugin -c Release -r win-x64 --self-contained -p:PublishSingleFile=true -f net6.0
mkdir test
cp HelloPlugin\bin\Release\net6.0\publish\* .\test\
cp \AppWithPlugin\bin\Release\net6.0\win-x64\publish\* .\test\
cd test
./AppWithPlugin.exe
```

To confirm this is a regression i.e. works on .net 5:
```
dotnet publish .\HelloPlugin\ -c Release -f net5.0
dotnet publish AppWithPlugin -c Release -r win-x64 --self-contained -p:PublishSingleFile=true -f net5.0
mkdir test
cp HelloPlugin\bin\Release\net5.0\publish\* .\test\
cp \AppWithPlugin\bin\Release\net5.0\win-x64\publish\* .\test\
cd test
./AppWithPlugin.exe
```