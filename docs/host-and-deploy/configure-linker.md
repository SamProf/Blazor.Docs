---
title: Настройка компоновщика
author: guardrex
description: Узнайте, как управлять компоновщиком промежуточного языка (IL) при создании приложения Blazor.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/blazor/host-and-deploy/configure-linker
---
# Настройка компоновщика

От [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazor-preview-notice.md)]

Blazor выполняет [Intermediate Language (IL)](https://docs.microsoft.com/dotnet/standard/managed-code#intermediate-language--execution) связывание во время каждой релизной сборки, чтобы удалить ненужный IL из выходных сборок.

Вы можете контролировать сборку ссылок с помощью одного из следующих подходов:

* Отключить глобальную привязку с помощью свойства MSBuild.
* Управление привязкой для каждой сборки в конфигурационном файле.

## Отключить привязку к свойству MSBuild

Связывание включено по умолчанию в режиме Release при создании приложения, которое включает публикацию. Чтобы отключить привязку для всех сборок, в файле проекта установите свойство `<BlazorLinkOnBuild>` как `false`:

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## Управление связыванием в конфигурационном файле

Связывание можно контролировать на основе сборки, предоставляя файл конфигурации XML и указывая файл как элемент MSBuild в файле проекта.

Ниже приведен пример файла конфигурации (*Linker.xml*):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/aspnet/Blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

Чтобы узнать больше о формате файла для конфигурации, смотрите [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/linker/README.md#syntax-of-xml-descriptor).

Укажите файл конфигурации в файле проекта с помощью `BlazorLinkerDescriptor`:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```
