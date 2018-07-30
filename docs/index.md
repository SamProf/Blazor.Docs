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

Ниже представлены исходный код и примеры, доступные для просмотра или загрузки с GitHub. Чтобы изучить пример, перейдите по ссылке. Чтобы загрузить код, выполните следующие действия:

1. Загрузите репозиторий, содержащий пример кода, выполнив одну из следующих процедур:
   * Загрузите ZIP-репозиторий к себе на компьютер. Разархивируйте сжатый архив.
   * Сделайте [Fork](https://help.github.com/articles/fork-a-repo/) репозитория [клонируйте](https://help.github.com/articles/cloning-a-repository/) его на вашу локальную систему. Ответвление(Forking) и клонирование позволяют вам вносить вклад в документацию, совершая изменения в своём репозитории, а затем создавая запрос на перенос в основной репозитория. Для получения дополнительной информации смотрите [.NET Руководство по составлению документации](https://github.com/dotnet/docs/blob/master/CONTRIBUTING.md) и [ASP.NET Руководство контрибуции документации](https://github.com/aspnet/Docs/blob/master/CONTRIBUTING.md).
   * Клонируйте репозиторий в локальную систему. Если вы клонируете репозиторий документов непосредственно в свою локальную систему, вы не сможете совершать коммиты непосредственно против репозитория, поэтому позже вы не сможете вносить документы. Используйте описанную выше процедуру fork и clone, если вы хотите сохранить возможность позже внести вклад в документацию.
1. Перейдите в каталог репозитория с примерами. Относительный путь к месту выборки отображается в адресной строке браузера, когда вы переходите по указанным ссылкам в своем браузере.
1. Чтобы запустить пример, у вас есть несколько вариантов:
   * Используйте [.NET CLI tools](https://docs.microsoft.com/dotnet/core/tools): В окне консоли перейдите к папку с примером и используйте команды dotnet.
   * Используйте [Visual Studio](https://www.visualstudio.com/) или [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/): Откройте пример, выбрав **File** > **Open** > **Project/Solution** из навигационного меню и выберите файл проекта (*\*.csproj* or *\*.fsproj*).
   * Используйте [Visual Studio Code](https://code.visualstudio.com/): Откройте пример, выбрав  **File** > **Open Folder** из навигационного меню и выберите каталог проекта.
   * Используйте любую другую IDE, которая поддерживает .NET Core проекты.
