---
title: Размещение и развертывание Blazor
author: guardrex
description: Узнайте, как размещать и развертывать приложения Blazor с использованием ядра ASP.NET, сетей доставки контента (CDN), файловых серверов и страниц GitHub.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/blazor/host-and-deploy/index
---
# Размещение и развертывание Blazor

От [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) и [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazor-preview-notice.md)]

## Публикация приложения

Приложения Blazor публикуются для развертывания в конфигурации Release с помощью команды [dotnet publish](https://docs.microsoft.com/dotnet/core/tools/dotnet-publish). IDE может обрабатывать выполнение команды `dotnet publish` автоматически, используя встроенные функции публикации, поэтому может не потребоваться вручную выполнить команду из командной строки в зависимости от используемых инструментов разработки. 

```console
dotnet publish -c Release
```

`dotnet publish` запускает [восстановление](https://docs.microsoft.com/dotnet/core/tools/dotnet-restore) зависимостей проекта и [builds](https://docs.microsoft.com/dotnet/core/tools/dotnet-build) перед созданием файлов для развертывания. Как часть процесса сборки, неиспользуемые методы и сборки удаляются, чтобы уменьшить размер загрузки приложения и время загрузки. Развертывание создается в каталоге */bin/Release/&lt;target-framework&gt;/publish*.

Содержимое в каталоге *publish* развертываются на веб-сервере. Развертывание может быть ручным или автоматизированным процессом в зависимости от используемых инструментов разработки.

## Настройка компоновщика

Blazor выполняет Intermediate Language (IL), связывающий каждую сборку, чтобы удалить ненужный IL из выходных сборок. Вы можете контролировать сборку сборки при сборке. Для получения дополнительной информации смотрите <xref:client-side/blazor/host-and-deploy/configure-linker>.

## Переписать URL-адреса для правильной маршрутизации

Запросы маршрутизации для компонентов страницы в клиентском приложении не так просты, как запросы на маршрутизацию на серверное, размещенное приложение. Рассмотрим клиентское приложение с двумя страницами:

* **_Main.cshtml_** &ndash; Загружает корневое приложение и содержит ссылку на страницу О сайте (`href="About"`).
* **_About.cshtml_** &ndash; Страница О сайте.

Когда запрашивается документ по умолчанию приложения, используя адресную строку браузера (например, `https://www.contoso.com/`):

1. Браузер делает запрос.
1. Возвращается страница по умолчанию, которая обычно *index.html*.
1. *index.html* загружает приложение.
1. Маршрутизатор Blazor загружается и Razor страница `Main` (*Main.cshtml*) отображается.

На главной странице, выбрав ссылку на страницу `О сайте`, загружается страница `О сайте`. Выбор ссылки на странице `О сайте` работает на клиенте, потому что маршрутизатор Blazor запрещает браузеру делать запрос в Интернете на `www.contoso.com` для `О сайте` и обслуживает страницу внутри себя. Все запросы для внутренних страниц *в клиентском приложении* работают одинаково: запросы не вызывают запросы на основе браузера на серверные ресурсы в Интернете. Маршрутизатор обрабатывает запросы внутренне.

Если запрос выполняется с помощью адресной строки браузера для `www.contoso.com/About`, запрос не выполняется. Такой ресурс не существует на интернет-хосте приложения, поэтому возвращается ответ *404 Не найдено*.

Поскольку браузеры запрашивают интернет-хосты для клиентских страниц, веб-серверы и службы хостинга должны переписывать все запросы на ресурсы, не физически находящиеся на сервере, на страницу *index.html*. Когда возвращается *index.html*, клиентский маршрутизатор приложения берет обработку на себя и возвращает правильный ресурс.

## Базовый путь приложения

The app base path is the virtual app root path on the server. For example, an app that resides on the Contoso server in a virtual folder at `/CoolBlazorApp/` is reached at `https://www.contoso.com/CoolBlazorApp` and has a virtual base path of `/CoolBlazorApp/`. By setting the app base path to `CoolBlazorApp/`, the app is made aware of where it virtually resides on the server. The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory. This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app. The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.

In many hosting scenarios, the server's virtual path to the app is the root of the app. In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app. In other hosting scenarios, such as GitHub Pages and IIS virtual directories, the app base path must be set to the server's virtual path to the app. To set the Blazor app's base path, add or update the `<base>` tag in *index.html* found within the `<head>` tag elements. Set the `href` attribute value to `<virtual-path>/` (the trailing slash is required), where `<virtual-path>/` is the full virtual app root path on the server for the app. In the preceding example, the virutal path is set to `CoolBlazorApp/`: `<base href="CoolBlazorApp/" />`.

For an app with a non-root virtual path configured (for example, `<base href="CoolBlazorApp/" />`), the app fails to find its resources *when run locally*. To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.

To pass the path base argument with the root path (`/`) when running the app locally, execute the following command from the Blazor app's directory:

```console
dotnet run --pathbase=/CoolBlazorApp
```

The app responds locally at `http://localhost:port/CoolBlazorApp`.

For more information, see the [path base host configuration value](#path-base) section.

> [!IMPORTANT]
> If a Blazor app uses the [client-side hosting model](xref:client-side/blazor/host-and-deploy/hosting-models#client-side-hosting-model) (based on the **Blazor** project template) and is hosted as an IIS sub-application in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler. Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:
>
> ```xml
> <handlers>
>   <remove name="aspNetCore" />
> </handlers>
> ```
>
> Removing the handler is performed in addition to configuring the app's base path as described in this section. Set the app base path in the Blazor app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.

## Host configuration values

Blazor apps that use the [server-side hosting model](xref:client-side/blazor/host-and-deploy/hosting-models#server-side-hosting-model) can accept [Web Host configuration values](https://docs.microsoft.com/aspnet/core/fundamentals/host/web-host#host-configuration-values).

Blazor apps that use the [client-side hosting model](xref:client-side/blazor/host-and-deploy/hosting-models#client-side-hosting-model) can accept the following host configuration values as command-line arguments at runtime in the development environment.

### Content Root

The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.

* Pass the argument when running the app locally at a command prompt. From the app's directory, execute:

  ```console
  dotnet run --contentroot=/<content-root>
  ```
* Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile. This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/<content-root>"
  ```
* In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**. Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.

  ```console
  --contentroot=/<content-root>
  ```

### Path base

The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production). For more information, see the [App base path](#app-base-path) section.

> [!IMPORTANT]
> Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value. If the app base path is provided in the `<base>` tag as `<base href="/CoolBlazorApp/" />` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolBlazorApp` (no trailing slash).

* Pass the argument when running the app locally at a command prompt. From the app's directory, execute:

  ```console
  dotnet run --pathbase=/<virtual-path>
  ```
* Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile. This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/<virtual-path>"
  ```
* In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**. Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.

  ```console
  --pathbase=/<virtual-path>
  ```

### URLs

The `--urls` argument indicates the IP addresses or host addresses with ports and protocols to listen on for requests.

* Pass the argument when running the app locally at a command prompt. From the app's directory, execute:

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```
* Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile. This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```
* In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**. Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.

  ```console
  --urls=http://127.0.0.1:0
  ```
Во многих сценариях хоста виртуальный путь сервера к приложению является корнем приложения. В этих случаях базовый путь к приложению - пустая строка, которая является стандартной конфигурацией для приложения Blazor. В других сценариях хостинга, таких как виртуальные каталоги GitHub Pages и IIS, базовый путь приложения должен быть установлен на виртуальный путь сервера к приложению. Чтобы установить базовый путь приложения Blazor, добавьте или обновите тег **&lt;base&gt;** в *index.html* в теге **&lt;head&gt;**. Установите значение атрибута `href` в `<virtual-path>/` (требуется конечная косая черта), где `<virtual-path>/` является полным корневым корнем виртуального приложения на сервере для приложения.

## Модели развертывания

Существуют две модели развертывания для приложений Blazor:

* [Развёртывание с помощью ASP.NET Core](#развертывание-с-помощью-aspnet-core) &ndash; Развертывание использует приложение ASP.NET Core на сервере для размещения приложения Blazor.
* [Автономное развёртывание](#автономное-развертывание) &ndash; Автономное развертывание размещает приложение Blazor на статичном веб-сервере или службе, где .NET не используется для обслуживания приложения Blazor.

### Развертывание с помощью ASP.NET Core

В развертывании приложение ASP.NET Core обрабатывает одностраничную маршрутизацию приложений и хостинг приложений Blazor. Опубликованное приложение ASP.NET Core вместе с одним или несколькими приложениями Blazor, которые он размещает, развертывается на веб-сервере или в сервисе хостинга.

Чтобы разместить приложение Blazor, приложение ASP.NET Core должно:

* Включать Blazor приложение.
* Содержать пакет [Microsoft.AspNetCore.Blazor.Server](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Server/) в файлах проекта.
* Иметь настроеный хостинг приложений Blazor с помощью марширения метода `UseBlazor` в [IApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) в `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseBlazor<Client.Program>();
}
```

Метод расширения `UseBlazor` выполняет следующую задачу:

* Конфигурирует [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files) для обслуживания статических активов Blazor из папки *dist*. В среде разработки хранятся файлы в папке *wwwroot*.
* Настраивает одностраничную маршрутизацию приложений для запросов ресурсов, которые не предназначены для реальных файлов, существующих на диске. Приложение обслуживает документ по умолчанию (*wwwroot/index.html*) для любого запроса, который не был обработан предыдущим экземпляром промежуточного статического файла. Например, запрос на получение страницы из приложения, который должен обрабатываться маршрутизатором Blazor на клиенте, переписывается в запрос на странице *wwwroot/index.html*.

Когда опубликовано приложение ASP.NET Core, приложение Blazor входит в опубликованный каталог, поэтому приложение ASP.NET Core и приложение Blazor могут быть развернуты вместе. Для получения дополнительной информации о хостинге и развертывании основного приложения ASP.NET смотрите [Host and deploy ASP.NET Core](https://docs.microsoft.com/aspnet/core/host-and-deploy).

Информацию о развертывании в Azure App Service смотрите в следующих разделах:

[Publish to Azure with Visual Studio](https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs)  
Узнайте, как опубликовать приложение Blazor с поддержкой Core для Azure App Service с помощью Visual Studio.

[Publish to Azure with CLI tools](https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-cli)  
Узнайте, как публиковать приложение ASP.NET Core в Azure App Service, используя клиент командной строки Git.

### Автономное развертывание

При автономном развертывании на сервер или службу хостинга развертывается только клиентское приложение Blazor. Серверное приложение ASP.NET Core не используется для размещения приложения Blazor. Статические файлы приложения Blazor запрашиваются браузером непосредственно с веб-сервера или службы статического файла.

При развертывании автономного приложения Blazor из опубликованной *dist* папки любой веб-сервер или служба хостинга, которая обслуживает статические файлы, может работать с приложенями Blazor.

#### IIS

IIS - это статический файловый сервер для приложений Blazor. Чтобы настроить IIS на размещение Blazor, смотрите [Создайте статический веб-сайт в IIS](https://docs.microsoft.com/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Опубликованные активы создаются в каталоге *\\bin\\Release\\&lt;target-framework&gt;\\publish*. Содержимое каталога *publish* размещается на веб-сервере или хостинге.

**web.config**

Когда проект Blazor публикуется, создается файл *web.config* со следующей конфигурацией IIS:

* Типы MIME установлены для следующих расширений файлов:
  - *\*.dll*: `application/octet-stream`
  - *\*.json*: `application/json`
  - *\*.wasm*: `application/wasm`
  - *\*.woff*: `application/font-woff`
  - *\*.woff2*: `application/font-woff`
* HTTP-сжатие включено для следующих типов MIME:
  - `application/octet-stream`
  - `application/wasm`
* Правила перезаписи URL-адреса установлены:
  - Подавать подкаталог, где расположены статические активы приложения (*&lt;assembly_name&gt;\\dist\\&lt;path_requested&gt;*).
  - Создавать резервную маршрутизацию SPA, чтобы запросы на нефайловые активы перенаправлялись к документу по умолчанию приложения в папке статических ресурсов (*&lt;assembly_name&gt;\\dist\\index.html*).

**Установка модуля перезаписи URL-адресов**

[Модуль перезаписи URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite) требуется чтобы переписывать URL-адреса. Модуль не установлен по умолчанию, и он недоступен для установки в качестве службы роли веб-сервера (IIS). Модуль должен быть загружен с веб-сайта IIS. Используйте установщик веб-платформы для установки модуля:

1. Перейдите к [Страница загрузки модуля перезаписи URL-адреса](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Для английской версии выберите **WebPI** для загрузки установщика WebPI. Для других языков выберите соответствующую архитектуру для сервера (x86/x64) для загрузки установщика.
1. Скопируйте установщик на сервер. Запустите программу установки. Выберите кнопку **Install** и примите условия лицензии. После завершения установки перезагрузка сервера не требуется.

**Настройка веб-сайта**

Установите  **физический путь** веб-сайта в папку приложения Blazor. Папка содержит:

* Файл *web.config*, который IIS использует для настройки веб-сайта, включая необходимые правила переадресации и типы файлов.
* Папка статических файлов приложения.

**Исправление проблем**

Если получена *500 Internal Server Error* и диспетчер IIS выдает ошибки при попытке получить доступ к настройке веб-сайта, убедитесь, что установлен модуль перезаписи URL-адресов. Когда модуль не установлен, файл *web.config* не может быть проанализирован IIS. Это не позволяет диспетчеру IIS загружать конфигурацию веб-сайта и веб-сайт из службы статических файлов Blazor.

Дополнительные сведения об устранении неполадок при развертывании в IIS смотрите [Устранение неполадок в ASP.NET Core на IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/troubleshoot).

#### Nginx

Следующий файл *nginx.conf* упрощен, чтобы показать, как настроить Nginx для отправки файла *Index.html* всякий раз, когда он не может найти соответствующий файл на диске.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /Index.html =404;
        }
    }
}
```

Для получения дополнительной информации о конфигурации веб-сервера Nginx смотрите [Создание файлов конфигурации NGINX Plus и NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

#### Nginx в Docker

Чтобы разместить Blazor в Docker с помощью Nginx, настройте файл Docker для использования образа Nginx на основе Alpine. Обновите файл Docker, чтобы скопировать файл *nginx.config* в контейнер.

Добавьте строки в файл Docker, как показано в следующем примере:

```
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

#### GitHub Pages

Чтобы обрабатывать перезапись URL, добавьте файл *404.html* со сценарием, который обрабатывает перенаправление запроса на страницу *index.html*. Для примера реализации, представленной сообществом, смотрите [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)). Пример использования сообществом можно увидеть на [blazor-demo/blazor-demo.github.io в GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([живой сайт](https://blazor-demo.github.io/)).

При использовании сайта проекта вместо сайта организации добавьте или обновите **&lt;base&gt;** тег в файле *index.html*. установите значение атрибута `href` как `<repository-name>/`, где `<repository-name>/` это имя репозитория GitHub.
