# MSVCToolchain

MSVCToolchain extends the build process for building native C++ projects using the MSVC compiler. By default, the .NET CLI does not support C++ projects due to missing compiler paths and extensions, which are instead a part of the Visual Studio C++ toolset.

This package is designed to be imported in a `.csproj` file format to add support for building C++ projects on Windows through both the .NET CLI and MSBuild. This can be used to build standalone C++ projects or to build C++ projects as part of a larger .NET solution.

Below is a simple example of how to build a simple C++ executable using the MSVC toolchain:

```xml
<!-- ProjectA.csproj -->
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netstandard2.1</TargetFramework>
  </PropertyGroup>

  <ImportGroup>
    <PackageReference Include="MSVCToolchain"
                      Version="1.0.0"
                      PrivateAssets="All" />
  </ImportGroup>

  <!-- Properties for MSVCFindCompilerPaths -->
  <PropertyGroup>
    <MSVCPlatform>x86</MSVCPlatform>
    <MSVCOutputType>Exe</MSVCOutputType>
  </PropertyGroup>

  <!-- Properties for MSVCToolchain -->
  <ItemGroup>
    <CompilerArgs Include="/GS /sdl" />
    <PreprocessorDefines Include="/D WIN32" />
  </ItemGroup>

</Project>
```

Below is an example of how to build a native C++ project against the .NET SDK using the MSVC toolchain:

```xml
<!-- ProjectB.csproj -->
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netstandard2.1</TargetFramework>
  </PropertyGroup>

  <ImportGroup>
    <PackageReference Include="MSVCToolchain"
                      Version="1.0.0"
                      PrivateAssets="All" />
  </ImportGroup>

  <!--
    Reference the Microsoft .NET Framework (NETFX) SDK
    This is needed for building against Windows SDK 10.0.15063.0 and later.
  -->
  <PropertyGroup>
    <SDK40Version>4.8</SDK40Version>
    <!-- Get the .NET Framework SDK Kits path from registry -->
    <SDK40KitsPath>$([MSBuild]::GetRegistryValueFromView(
      'HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SDKs\NETFXSDK\$(SDK40Version)',
      'KitsInstallationFolder',
      null,
      RegistryView.Registry32
    ))</SDK40KitsPath>
  </PropertyGroup>

  <!-- Properties for MSVCFindCompilerPaths -->
  <PropertyGroup>
    <MSVCPlatform>x86</MSVCPlatform>
    <MSVCOutputType>Dll</MSVCOutputType>
  </PropertyGroup>

  <!-- Properties for MSVCToolchain -->
  <ItemGroup>
    <CompilerArgs Include="/FS" />
    <CompilerArgs Include="/D_WINDOWS" />
    <PreprocessorDefines Include="/D WIN32" />
    <!-- Include metahost.h and mscoree.lib from the .NET Framework SDK -->
    <IncludePaths Include="$(SDK40KitsPath)Include\um" />
    <LibPaths Include="$(SDK40KitsPath)Lib\um\$(MSVCPlatform)" />
  </ItemGroup>

</Project>
```

## License

This project is licensed under the [Apache-2.0 License](/LICENSE).
