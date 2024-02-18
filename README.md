# MSVCToolchain

MSVCToolchain is a set of MSBuild targets and props files that can be used to build C++ projects using the MSVC compiler. By default, the .NET CLI does not support building C++ projects due to missing compiler paths, which are instead a part of the Visual Studio C++ toolset.

This module is designed to be imported in a `.csproj` file formatto add support for building C++ projects on Windows through both the .NET CLI and MSBuild. This can be used to build standalone C++ projects or to build C++ projects as part of a larger .NET solution.

Below is a simple example of how to build a simple C++ executable using the MSVC toolchain:
```xml
<!-- ProjectA.csproj -->
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>

    <!-- Properties for MSVCFindCompilerPaths -->
    <MSVCPlatform>x86</MSVCPlatform>
    <MSVCOutputType>Exe</MSVCOutputType>

     <!-- Properties for MSVCToolchain -->
    <CompilerArgs>/GS /sdl</CompilerArgs>
    <PreprocessorDefines>/D WIN32</PreprocessorDefines>
  </PropertyGroup>

  <Import Project="< ... >\MSVCToolchain.props" />
  <Import Project="< ... >\MSVCToolchain.targets" />
</Project>
```

Below is an example of how to build a native C++ project against the .NET SDK using the MSVC toolchain:

```xml
<!-- ProjectB.csproj -->
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>

    <!-- Properties for MSVCFindCompilerPaths -->
    <MSVCPlatform>x86</MSVCPlatform>
    <MSVCOutputType>Dll</MSVCOutputType>

     <!-- Properties for MSVCToolchain -->
    <CompilerArgs>/GS /sdl</CompilerArgs>
    <PreprocessorDefines>/D WIN32</PreprocessorDefines>
  </PropertyGroup>

  <!--
    Reference the Microsoft .NET Framework (NETFX) SDK
    This is needed for building against Windows SDK 10.0.15063.0 and later.
  -->
  <PropertyGroup>
    <!-- Get the .NET Framework SDK Kits path from registry -->
    <SDK40Version>4.8</SDK40Version>
    <SDK40KitsPath>$([MSBuild]::GetRegistryValueFromView(
      'HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SDKs\NETFXSDK\$(SDK40Version)',
      'KitsInstallationFolder',
      null,
      RegistryView.Registry32
    ))</SDK40KitsPath>

    <!-- Include metahost.h and mscoree.lib from the .NET Framework SDK -->
    <IncPaths>/I &quot;$(SDK40KitsPath)Include\um&quot;</IncPaths>
    <LibPaths>/LIBPATH:&quot;$(SDK40KitsPath)Lib\um\$(MSVCPlatform)&quot;</LibPaths>
  </PropertyGroup>

  <Import Project="< ... >\MSVCToolchain.props" />
  <Import Project="< ... >\MSVCToolchain.targets" />
</Project>
```

## License

This project is licensed under the [Apache-2.0 License](/LICENSE).
