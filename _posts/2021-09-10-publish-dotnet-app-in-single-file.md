---
title: Publish dotnet app in single file
tags:
  - Dotnet
  - C#
published: true
---

When publish dotnet app to release many file appear in **/Release** folder. But if you don't like it that way and love to compact those file in single file, it could be better. So define property like config below be useful to you.

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net5.0</TargetFramework>
    <PublishSingleFile>true</PublishSingleFile>
    <SelfContained>true</SelfContained>
    <RuntimeIdentifier>win-x64</RuntimeIdentifier>
    <PublishReadyToRun>true</PublishReadyToRun>
  </PropertyGroup>

</Project>

```