---
title: Blazor preview documentation
author: guardrex
description: Explore Blazor, a new experimental .NET web framework using C#/Razor and HTML that runs in the browser with WebAssembly.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/blazor/index
---
# Предварительная документация Blazor

[!INCLUDE[](~/includes/blazor-preview-notice.md)]

Blazor - это экспериментальный .NET веб фреймворк использующий C#/Razor и HTML который работает в браузере используя WebAssembly

## Начать

[Начать с Blazor](xref:client-side/blazor/get-started)

## Изучите предварительную документацию Blazor

[Создайте свое первое приложение Blazor](xref:client-side/blazor/tutorials/first-app)  
Постройте приложение Blazor шаг за шагом и быстро изучите основные функции Blazor-фреймворка.

[Часто задаваемые вопросы (FAQ)](xref:client-side/blazor/introduction/faq)  
Найдите ответы на часто задаваемые вопросы о Blazor.

[Введение в Blazor](xref:client-side/blazor/introduction/index)  
Узнайте, как Blazor запускается в браузере для выполнения кода C#/Razor с помощью WebAssembly и среды выполнения .NET.

[Компоненты](xref:client-side/blazor/components/index)  
Создавайте и используйте компоненты Blazor, основные строительные блоки приложений Blazor, предоставляемые скомпилированными файлами Razor или C#.

[Макеты](xref:client-side/blazor/layouts)  
Узнайте, как создавать многоразовые компоненты компоновки для приложений Blazor.

[Внедрение зависимости](xref:client-side/blazor/dependency-injection)  
Посмотрите, как приложения Blazor могут использовать встроенные сервисы, введя их в состав компонентов.

[Маршрутизация](xref:client-side/blazor/routing)  
Узнайте, как маршрутизировать запросы в клиентском приложении Blazor и о компоненте NavLink.

[JavaScript-взаимодействие](xref:client-side/blazor/javascript-interop)  
Узнайте, как вызывать функции JavaScript из .NET и .NET методы из JavaScript.

[Хостинг и развертывание](xref:client-side/blazor/host-and-deploy/index)  
Узнайте, как размещать и развертывать приложения Blazor с использованием моделей размещения и автономного развертывания.

## Дополнительные ресурсы

* [WebAssembly](http://webassembly.org/)
* [Руководство C#](https://docs.microsoft.com/dotnet/csharp/)
* [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor)
* [HTML](https://www.w3.org/html/)

## Предложить функцию или файл с сообщением об ошибке

Чтобы создавать предложения и отчеты об ошибках файлов, пожалуйста, [откройте вопрос](https://github.com/aspnet/Blazor/issues/new).Для получения общей помощи и получения ответов от сообщества, присоединитесь к разговору на [Gitter](https://gitter.im/aspnet/Blazor).

## Внести вклад в документацию Blazor

Предварительная документация Blazor предоставляется, чтобы помочь вам попробовать Blazor. Нам нужна ваша помощь для улучшения документации. Инструкции смотрите в разделе  [Внесение вклада в документацию Blazor](https://github.com/aspnet/Blazor.Docs/blob/master/CONTRIBUTING.md).

## Примечания к выпуску Blazor

* [Примечания к выпуску (0.4.0)](https://github.com/aspnet/Blazor/releases/tag/0.4.0)
* [Примечания к выпуску (0.3.0)](https://github.com/aspnet/Blazor/releases/tag/0.3.0)
* [Примечания к выпуску (0.2.0)](https://github.com/aspnet/Blazor/releases/tag/0.2.0)
* [Примечания к выпуску (0.1.0)](https://github.com/aspnet/Blazor/releases/tag/0.1.0)

## Просмотр и загрузка примеров

Several topics show source code and samples that are available to view or download from GitHub. To view a sample, follow the sample link. To download the code, follow these instructions:

1. Download the repository that contains the sample code by performing one of the following procedures:
   * Download a ZIP of the repository to your local system. Un-ZIP the compressed archive.
   * [Fork](https://help.github.com/articles/fork-a-repo/) the repository and [clone](https://help.github.com/articles/cloning-a-repository/) the fork to your local system. Forking and cloning permits you to make contributions to the documentation by committing changes to your fork and then creating a pull request for the docs repository. For more information, see the [.NET Documentation Contributing Guide](https://github.com/dotnet/docs/blob/master/CONTRIBUTING.md) and the [ASP.NET Docs Contributing Guide](https://github.com/aspnet/Docs/blob/master/CONTRIBUTING.md).
   * Clone the repository locally. If you clone a docs repository directly to your local system, you won't be able to make commits directly against the repository, so you won't be able to make documentation contributions later. Use the fork and clone procedure previously described if you want to preserve the opportunity to contribute to the documentation later.
1. Navigate within the repository's folders to the sample's location. The relative path to the sample's location appears in your browser's address bar when you follow the link to the sample in your browser.
1. To run a sample, you have several options:
   * Use the [.NET CLI tools](https://docs.microsoft.com/dotnet/core/tools): In a console window, navigate to the sample's folder and use dotnet CLI commands.
   * Use [Visual Studio](https://www.visualstudio.com/) or [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/): Open the sample by selecting **File** > **Open** > **Project/Solution** from the menu bar, navigate to the sample project folder, and select the project file (*\*.csproj* or *\*.fsproj*).
   * Use [Visual Studio Code](https://code.visualstudio.com/): Open the sample by selecting **File** > **Open Folder** from the menu bar and selecting the sample's project folder.
   * Use a different IDE that supports .NET Core projects.
