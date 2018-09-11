---
title: Создайте свое первое приложение
author: guardrex
description: Build a Blazor app step-by-step and quickly learn the basic features of the Blazor framework.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/blazor/tutorials/first-app
---
# Создайте свое первое приложение

[!INCLUDE[](~/includes/blazor-preview-notice.md)]

Используя данную инструкцию, вы постепенно создаете приложение Blazor и быстро изуизучите основные функции фреймворка Blazor.

[Посмотрите или скачайте примеры кода](https://github.com/aspnet/Blazor.Docs/tree/master/docs/tutorials/build-your-first-blazor-app/samples/) ([как скачать](xref:client-side/blazor/index#просмотр-и-загрузка-примеров)). Смотрите [Начало работы с Blazor](xref:client-side/blazor/get-started).

Чтобы создать проект в Visual Studio:

1. Выберите **File** > **New** > **Project**. Выберите **Web** > **ASP.NET Core Web Application**. Запишите название проекта "BlazorApp1" в поле **Name**. Нажмите **OK**.

    ![Новый ASP.NET Core проект](https://raw.githubusercontent.com/aspnet/Blazor.Docs/gh-pages/docs/tutorials/build-your-first-blazor-app/_static/new-aspnet-core-project.png)

1. Появится диалог **New ASP.NET Core Web Application**. Убедимся что выбрано **.NET Core** в верху окна. Выберите либо **ASP.NET Core 2.0** либо **ASP.NET Core 2.1**. Выберите шаблон **Blazor** и нажмите **OK**.

    ![Диалог нового Blazor приложения](https://raw.githubusercontent.com/aspnet/Blazor.Docs/gh-pages/docs/tutorials/build-your-first-blazor-app/_static/new-blazor-app-dialog.png)

1. Когда проект будет создан, нажмите **Ctrl-F5** для запуска приложения *без отладчика*. Работа с отладчиком (**F5**) в настоящее время не поддерживается.

> [!NOTE]
> Если вы не используете Visual Studio, вы можете создавать Blazor приложения используя командную строку Windows, macOS или Linux:
>
> ```console
> dotnet new blazor -o BlazorApp1
> cd BlazorApp1
> dotnet run
> ```
>
> Перейдите в приложение используя адрес **localhost** и порт, который отобразится в окне консоли после выполнения команды `dotnet run`. Используйте сочетание клавиш **Ctrl-C** в окне консоли для завершения приложения.

Приложение Blazor запущено в браузере:

![Домашняя страница Blazor приложения](https://user-images.githubusercontent.com/1874516/39509497-5515c3ea-4d9b-11e8-887f-019ea4fdb3ee.png)

## Сборка компонентов

1. Посетите каждую из 3 доступных страниц: *Home*, *Counter* и *Fetch data*.

    Эти три страницы реализованы тремя файлами Razor в каталоге *Pages*: *Index.cshtml*, *Counter.cshtml* и *FetchData.cshtml*. Каждый из этих файлов реализует компонент Blazor, который компилируется и выполняется на стороне клиента в браузере.

1. Нажмите кнопку на странице *Counter*.

    ![Страница Counter приложения Blazor](https://user-images.githubusercontent.com/1874516/39509525-6e367c66-4d9b-11e8-9978-e52a9750c34b.png)

    Каждый раз, когда нажимается кнопка, счетчик увеличивается без обновления страницы. Обычно такое поведение на стороне клиента реализуется в JavaScript; но здесь это реализовано используя C# в компоненте `Counter`.
    
1. Взгляните на реализацию компонента `Counter` в файле *Counter.cshtml*:

    ```cshtml
    @page "/counter"

    <h1>Counter</h1>

    <p>Current count: @currentCount</p>

    <button class="btn btn-primary" onclick="@IncrementCount">Click me</button>

    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
    ```

    Пользовательский интерфейс для компонента `Counter` определяется обычным HTML. Динамическая логика визуализации (например, циклы, условия, выражения) добавляется с использованием синтаксиса C# [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor). 
    HTML-разметка и логика отображения C# преобразуются в класс компонента во время сборки. Имя сгенерированного .NET-класса соответствует имени файла.

    Компоненты класса определяются в блоке `@functions`. В блоке `@functions`, компоненты состояния (свойства, поля) и методы могут быть указаны для обработки событий или для определения другой логики компонента. Эти элементы затем могут использоваться как часть логики рендеринга компонента и для обработки событий.

    Когда нажимается кнопка, в компоненте `Counter` вызывается зарегистрированный обработчик `onclick` (метод `IncrementCount`) и конпонент `Counter` регенерирует дерево рендеринга. Blazor сравнивает новое дерево рендеринга с предыдущим и применяет все модификации в Document Object Model (DOM) браузера. Отображаемый счетчик обновляется.

1. Обновите разметку для компонента `Counter` чтобы сделать заголовок верхнего уровня более *захватывающим*.

    ```cshtml
    @page "/counter"
    <h1><em>Counter!!</em></h1>
    ```

1. Также измените логику C# компонента `Counter` так, чтобы значение увиличивалось на 2 за раз.

    ```cshtml
    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount += 2;
        }
    }
    ```

1. Перезагрузите страницу в браузере чтобы увидеть изменения.

    ![Захватывающий счетчик](https://user-images.githubusercontent.com/1874516/39509668-e8949a92-4d9b-11e8-91e9-b6a494695d92.png)

## Используйте компоненты

После того как компонент определён, он может быть использован для реализации других компонентов. Разметка для использования компонента выглядит как HTML тег, где имя тега является типом компонента.

1. Добавьте компонент `Counter` на главную страницу приложения (*Index.cshtml*).

    ```cshtml
    @page "/"

    <h1>Hello, world!</h1>

    Welcome to your new app.

    <SurveyPrompt Title="How is Blazor working for you?" />

    <Counter />
    ```

1. Перезагрузите домашнюю страницу в браузере. Обратите внимание на отдельный экземпляр компонента `Counter` на главной странице.

    ![Blazor домашняя страница с добавленным компонентом Counter](https://user-images.githubusercontent.com/1874516/39509718-224483f6-4d9c-11e8-9030-b4c7228d669d.png)

## Параметры компонента

Компоненты также могут иметь параметры, который задаются используя свойства класса компонента, которые помечены атрибутом `[Parameter]`. Используйте атрибуты для указания аргументов для компонента в разметке.

1. Обновите компонент `Counter` добавив параметр `IncrementAmount` со значением по умолчанию `1`.

    ```cshtml
    @functions {
        int currentCount = 0;

        [Parameter]
        private int IncrementAmount { get; set; } = 1;

        void IncrementCount()
        {
            currentCount += IncrementAmount;
        }
    }
    ```

    > [!NOTE]
    > Используя Visual Studio, вы можете быстро добавлять параметры компонента, используя сниппет `para`. Наберите `para` и нажмите `Tab` дважды.

1. На главной странице (*Index.cshtml*), узмените сумму увеличения до 10, задав значение для свойства `IncrementCount` компонента `Counter`.

    ```cshtml
    <Counter IncrementAmount="10" />
    ```

1. Перезагрузите страницу.

    Счетчик на главной странице сейчас увеличивается 10 за раз, тогда как счетчик на странице Counter по прежнему увеличивается только на 1.

    ![Blazor увеличение счетчика на 10](https://user-images.githubusercontent.com/1874516/39509798-618f0720-4d9c-11e8-9125-3d4c634dff46.png)

## Маршрутизация компонентов

Директива `@page` вверху файла *Counter.cshtml* определяет что данный файл является страницей, на которую могут быть отправлены запросы. Например, для страницы `Counter` обрабатываются запросы, отправленные по адресу `/Counter`. Без директивы `@page` компонент не будет обрабатывать запросы маршрутизации, но компонент по прежнему сможет использоваться в других компонентах.

## Внедрение зависимостей

Сервисы, зарегистрированные в приложении в провайдере сервисов, доступны в компонентах через [dependency injection (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Сервисы могут быть вставлены в компонент используя директиву `@inject`.

Взгляните на реализацию компонента `FetchData` в файле *FetchData.cshtml*. Директива `@inject` используется для внедрения экземпляра [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) в компонент.

```cshtml
@page "/fetchdata"
@inject HttpClient Http
```

Компонент `FetchData` использует внедрение `HttpClient` для получения JSON данных с сервера, когда компонент инициализирован. За кулисами `HttpClient` предоставляемый исполняемой средой Blazor, реализуется с использованием JavaScript-взаимодействия для вызова API-интерфейса браузера для отправки запроса (из C#, можно вызвать любую библиотеку JavaScript или API браузера). Полученные данные десериализуются в C# переменную `forecasts`, которая определена как массив `WeatherForecast` объектов.

```cshtml
@functions {
    WeatherForecast[] forecasts;

    protected override async Task OnInitAsync()
    {
        forecasts = await Http.GetJsonAsync<WeatherForecast[]>("/sample-data/weather.json");
    }

    class WeatherForecast
    {
        public DateTime Date { get; set; }
        public int TemperatureC { get; set; }
        public int TemperatureF { get; set; }
        public string Summary { get; set; }
    }
}
```

Цикл `@foreach` используется для отображения каждого экземпляра `forecast` в виде строки в таблице погоды.

```cshtml
<table class="table">
    <thead>
        <tr>
            <th>Date</th>
            <th>Temp. (C)</th>
            <th>Temp. (F)</th>
            <th>Summary</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var forecast in forecasts)
        {
            <tr>
                <td>@forecast.Date.ToShortDateString()</td>
                <td>@forecast.TemperatureC</td>
                <td>@forecast.TemperatureF</td>
                <td>@forecast.Summary</td>
            </tr>
        }
    </tbody>
</table>
```

## Создайте список задач

Добавьте новую страницу в приложение для реализации простого списка задач.

1. Добавьте пустой файл в каталог *Pages* назовите его *Todo.cshtml*.

1. Добавьте начальную разметку для страницы.

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>
    ```

1. Добавьте страницу задач в навигационное меню, обновив *Shared/NavMenu.cshtml*. Добавьте `NavLink` для страницы задач, добавив следующую разметку ниже существующего списка.

    ```cshtml
    <li class="nav-item px-3">
        <NavLink class="nav-link" href="todo">
            <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
        </NavLink>
    </li>
    ```

1. Перезагрузите страницу браузера. Посмотрите новую страницу задач.

    ![Blazor страница списка задач](https://user-images.githubusercontent.com/1874516/39509907-bb27e77a-4d9c-11e8-91e7-ea1e7c01729e.png)

1. Добавьте файл *TodoItem.cs* в корень проекта, в нём будут храниться отдельные задачи нашего списка задач.

1. Используйте следующий код для класса `ToDoItem`.

    ```csharp
    public class TodoItem
    {
        public string Title { get; set; }
        public bool IsDone { get; set; }
    }
    ```

1. Вернитесь в компонент `Todo` в *Todo.cshtml* и добавьте поле `todos` в блоке `@functions`. Компонент `Todo` использует данное поле для хранения состояния списка задач.

    ```cshtml
    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждой записи как элемента списка.

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>
    ```

1. Приложению требуютя элементы пользовательского интерфейса для добавления задач в список. Добавьте элемент для ввода текста и кнопку ниже списка.

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>

    <input class placeholder="Something todo" />
    <button>Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. Перезагрузите браузер.

    ![Добавлена кнопка добавления новых задач](https://user-images.githubusercontent.com/1874516/39512402-bc88ab46-4da5-11e8-9e3f-87b875b56383.png)

    Ничего не произойдёт, если нажать на кнопку **Add todo** так как сейчас нет соответствующего обработчика событий.

1. Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его использование при нажатии на кнопку, используя аттрибут `onclick`.

    ```cshtml
    <input placeholder="Something todo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();

        void AddTodo()
        {
            // Todo: Add the todo
        }
    }
    ```

    C# метод `AddTodo` вызывается каждый раз, когда нажимается кнопка.

1. Для полечения заголовка новой задачи добавьте новое текстовое поле `newTodo` и свяжите его значение с полем для ввода текста, используя аттрибут `bind`.

    ```csharp
    IList<TodoItem> todos = new List<TodoItem>();
    string newTodo;
    ```

    ```cshtml
    <input placeholder="Something todo" bind="@newTodo" />
    ```

1. Обновите метод `AddTodo` добавив в `TodoItem` запись заголовка новой задачи. Не забудьте очистить значение элемента ввода текста, записав в `newTodo` пустую строку.

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>

    <input placeholder="Something todo" bind="@newTodo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
        string newTodo;

        void AddTodo()
        {
            if (!string.IsNullOrWhiteSpace(newTodo))
            {
                todos.Add(new TodoItem { Title = newTodo });
                newTodo = string.Empty;
            }
        }
    }
    ```

1. Перезагрузите браузер. И добавьте несколько задач в список.

    ![Список задач](https://user-images.githubusercontent.com/1874516/39512531-2d2ff62e-4da6-11e8-8b83-291b0efc821b.png)

1. Наконец, что же это за список задач без флажков? Так же текст заголовка для каждой задачи должен быть доступен для редактирования. Добавим флажок и текстовое поле для каждой задачи, а их значения свяжем со свойствами `IsDone` и `Title` соответственно.

    ```cshtml
    <ul>
        @foreach (var todo in todos)
        {
            <li>
                <input type="checkbox" bind="@todo.IsDone" />
                <input bind="@todo.Title" />
            </li>
        }
    </ul>
    ```

1. Для проверки, что эти значения связаны, обновите `h1` заголовок, добавив отображение количества элементов списка задач, которые еще не выполнены (`IsDone` равно `false`).

    ```cshtml
    <h1>Todo (@todos.Where(todo => !todo.IsDone).Count())</h1>
    ```
    
1. Готовый компонент `Todo` должен выглядеть следующим образом:

    ```cshtml
    @page "/todo"

    <h1>Todo (@todos.Where(todo => !todo.IsDone).Count())</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>
                <input type="checkbox" bind="@todo.IsDone" />
                <input bind="@todo.Title" />
            </li>
        }
    </ul>

    <input placeholder="Something todo" bind="@newTodo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
        string newTodo;

        void AddTodo()
        {
            if (!string.IsNullOrWhiteSpace(newTodo))
            {
                todos.Add(new TodoItem { Title = newTodo });
                newTodo = string.Empty;
            }
        }
    }
    ```

Перезагрузите страницу браузера. Попробуйте добавить несколько новых задач.

![Завершённый список задач на Blazor](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

## Публикация и развёртывание

При использовании Visual Studio выполните следующие шаги, чтобы опубликовать приложение Blazor в Azure:

1. Нажмите правой кнопкой мыши на проекте в **Solution Explorer** и выберите **Publish**.

1. В диалоге **Pick a publish target** выберите **App Service** и **Create New**. Затем нажмите **Publish**.

    ![Выберите цель публикации](https://raw.githubusercontent.com/aspnet/Blazor.Docs/gh-pages/docs/tutorials/build-your-first-blazor-app/_static/blazor-publish-pick-target.png)

1. В диалоге **Create App Service** выберите имя проекта, укажите подписку, группу ресурсов и план размещения. Нажмите **Create** чтобы создать сервис и опубликовать приложение.

    ![Создание сервиса приложений](https://raw.githubusercontent.com/aspnet/Blazor.Docs/gh-pages/docs/tutorials/build-your-first-blazor-app/_static/blazor-publish-create-appservice2.png)

Подождите минуту, чтобы приложение было развернуто.

Теперь приложение должно работать в Azure. Можете отметить в вашем списке задач `Создать первое Blazor приложение` как *выполнено*. Отличная работа!

![Blazor on Azure](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

> [!NOTE]
> Если вы не используете Visual Studio, опубликовать Blazor приложение можно используя командную строку в Windows, macOS, или Linux:
>
> ```console
> dotnet publish -c Release
> ```
>
> Развертывание создается в */bin/Release/\<target-framework>/publish* каталоге. Переместите контент из каталога *publish* на сервер или сервис хостинга.
>
> Больше информации здесь [Хостинг и развёртывание](xref:client-side/blazor/host-and-deploy/index#публикация-приложения).

## Дополнительные ресурсы

В качестве более сложного примера Blazor приложения, ознакомьтесь с примером [Flight Finder](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor) на GitHub.

![Blazor Flight Finder](https://msdnshared.blob.core.windows.net/media/2018/03/blazor-flight-finder.png)
