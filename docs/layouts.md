---
title: Макеты Blazor
author: rstropek
description: Узнайте, как создавать повторно используемые макеты компонентов для приложений Blazor.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/blazor/layouts
---
# Макеты Blazor

От [Rainer Stropek](https://www.timecockpit.com)

[!INCLUDE[](~/includes/blazor-preview-notice.md)]

Приложения Blazor обычно содержат более одной страницы. Элементы макета, такие как меню, сообщения об авторских правах и логотипы, должны присутствовать на всех страницах. Копирование кода этих элементов на все страницы приложения не является эффективным решением. Такое дублирование трудно поддерживать и, вероятно, приводит к несогласованному контенту с течением времени. *Макеты* решают эту проблему.

## Что такое макеты?

Технически, макет - это еще один компонент Blazor. Макет определяется в шаблоне Razor или в коде C# и может содержать привязку данных, инъекцию зависимостей и другие обычные функции компонентов. Два дополнительных аспекта превращают *компонент* в *макет*:

* Компонент макета должен наследоваться от [BlazorLayoutComponent](/api/Microsoft.AspNetCore.Blazor.Layouts.BlazorLayoutComponent.html). `BlazorLayoutComponent` определяет свойство `Body` которое содержит содержимое, которое будет отображаться внутри макета.
* Компонент макета использует свойство `Body`, чтобы указать, где должно отображаться содержимое тела с использованием синтаксиса Razor `@Body`. Во время рендеринга `@Body` заменяется содержимым макета.

В следующем примере кода показан шаблон Razor компонента макета. Обратите внимание на использование `BlazorLayoutComponent` и `@Body`:

```csharp
@inherits BlazorLayoutComponent

<header>
    <h1>ERP Master 3000</h1>
</header>

<nav>
    <a href="master-data">Master Data Management</a>
    <a href="invoicing">Invoicing</a>
    <a href="accounting">Accounting</a>
</nav>

@Body

<footer>
    &copy; by @CopyrightMessage
</footer>

@functions {
    public string CopyrightMessage { get; set; }
    ...
}
```

## Использование макета в компоненте

Используйте директиву Razor `@layout` для применения макета к компоненту. Компилятор преобразует эту директиву в [LayoutAttribute](/api/Microsoft.AspNetCore.Blazor.Layouts.LayoutAttribute.html), который применяется к классу компонентов.

Следующий пример кода демонстрирует концепцию. Содержимое этого компонента вставляется в *MasterLayout* в позицию `@Body`:

```csharp
@layout MasterLayout

@page "/master-data"

<h2>Master Data Management</h2>
...
```

## Централизованный выбор макета

Каждая папка приложения Blazor может дополнительно содержать файл шаблона с именем *_ViewImports.cshtml*. Компилятор включает директивы, указанные в файле импорта, во всех шаблонах Razor в той же папке и рекурсивно во всех своих подпапках. Следовательно, файл *_ViewImports.cshtml*, содержащий `@layout MainLayout`, гарантирует, что все компоненты в папке используют макет *MainLayout*. Нет необходимости повторно добавлять `@layout` во все файлы *\*.cshtml*.

Обратите внимание, что шаблон по умолчанию для приложений Blazor использует механизм *_ViewImports.cshtml* для выбора макета. Новое созданное приложение содержит файл *_ViewImports.cshtml* в папке *Pages*.

## Вложенные макеты

Приложения Blazor могут состоять из вложенных макетов. Компонент может ссылаться на макет, который, в свою очередь, ссылается на другой макет. Например, макеты вложенности могут использоваться для отражения многоуровневой структуры меню.

В следующих примерах кода показано, как использовать вложенные макеты. Файл *CustomersComponent.cshtml* является отображаемым компонентом. Обратите внимание, что компонент ссылается на макет `MasterDataLayout`.

*CustomersComponent.cshtml*:

```csharp
@layout MasterDataLayout

@page "/master-data/customers"

<h1>Customer Maintenance</h1>
...
```

Файл *MasterDataLayout.cshtml* обеспечивает `MasterDataLayout`. Макет ссылается на другой макет `MainLayout`, там, где он будет встроен.

*MasterDataLayout.cshtml*:

```csharp
@layout MainLayout
@inherits BlazorLayoutComponent

<nav>
    <!-- Menu structure of master data module -->
    ...
</nav>

@Body
```

Наконец, `MainLayout` содержит элементы макета верхнего уровня, такие как верхний колонтитул, нижний колонтитул и главное меню.

*MainLayout.cshtml*:

```csharp
@inherits BlazorLayoutComponent

<header>...</header>
<nav>...</nav>

@Body
```
