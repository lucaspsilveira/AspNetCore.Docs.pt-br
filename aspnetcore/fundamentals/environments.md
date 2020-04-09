---
title: Usar vários ambientes no ASP.NET Core
author: rick-anderson
description: Aprenda a controlar o comportamento do aplicativo em vários ambientes em aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656215"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>Usar vários ambientes no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de runtime usando uma variável de ambiente.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ambientes

ASP.NET Core lê a `ASPNETCORE_ENVIRONMENT` variável ambiente na inicialização do aplicativo e armazena o valor no [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`pode ser definido como qualquer valor, mas três valores são fornecidos pelo framework:

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production> (padrão)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

O código anterior:

* Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.
* Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido com um dos seguintes:

  * `Staging`
  * `Production`
  * `Staging_2`

O [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) `IHostingEnvironment.EnvironmentName` usa o valor de incluir ou excluir marcação no elemento:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

No Windows e no macOS, valores e variáveis de ambiente não diferenciam maiúsculas de minúsculas. Valores e variáveis de ambiente do Linux **diferenciam maiúsculas de minúsculas** por padrão.

### <a name="development"></a>Desenvolvimento

O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção. Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.

O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto. Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.

O seguinte JSON mostra três perfis de um arquivo *launchSettings.json*:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> A propriedade `applicationUrl` no *launchSettings.json* pode especificar uma lista de URLs de servidores. Use um ponto e vírgula entre as URLs na lista:
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

Quando o aplicativo é inicializado com [dotnet run](/dotnet/core/tools/dotnet-run), o primeiro perfil com `"commandName": "Project"` é usado. O valor de `commandName` especifica o servidor Web a ser iniciado. `commandName` pode ser qualquer um dos seguintes:

* `IISExpress`
* `IIS`
* `Project` (que inicia o Kestrel)

Quando um aplicativo for iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):

* *launchSettings.json* é lido, se está disponível. As configurações de `environmentVariables` em *launchSettings.json* substituem as variáveis de ambiente.
* O ambiente de hospedagem é exibido.

A saída a seguir mostra um aplicativo iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

A guia **Depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o arquivo *launchSettings.json*:

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado. O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.

> [!WARNING]
> *launchSettings.json* não deve armazenar segredos. A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.

Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*. O exemplo a seguir define o ambiente como `Development`:

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

Um arquivo *.vscode/launch.json* do projeto não é lido ao iniciar o aplicativo com `dotnet run` da mesma maneira que *Properties/launchSettings.json*. Ao inicializar um aplicativo em desenvolvimento que não tem um arquivo *launchSettings.json*, defina o ambiente com uma variável de ambiente ou um argumento de linha de comando para o comando `dotnet run`.

### <a name="production"></a>Produção

O ambiente de produção deve ser configurado para maximizar a segurança, o desempenho e a robustez do aplicativo. Algumas configurações comuns que são diferentes do desenvolvimento incluem:

* Armazenamento em cache.
* Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.
* Páginas de erro de diagnóstico desabilitadas.
* Páginas de erro amigáveis habilitadas.
* Log de produção e monitoramento habilitados. Por exemplo, [Insights de aplicativos](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Definir o ambiente

Muitas vezes é útil definir um ambiente específico para testes com uma variável de ambiente ou configuração de plataforma. Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração. O método para configurar o ambiente depende do sistema operacional.

Quando o host é construído, a última configuração do ambiente lida pelo aplicativo determina o ambiente do aplicativo. O ambiente do aplicativo não pode ser alterado enquanto o aplicativo estiver em execução.

### <a name="environment-variable-or-platform-setting"></a>Configuração de ambiente ou plataforma

#### <a name="azure-app-service"></a>Serviço de aplicativo do Azure

Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:

1. Selecione o aplicativo na folha **Serviços de Aplicativos**.
1. No grupo **Configurações,** selecione a **lâmina Configuração.**
1. Na guia **Configurações** do aplicativo, selecione **Nova configuração do aplicativo**.
1. Na janela **de configuração de aplicativo Adicionar/Editar,** forneça `ASPNETCORE_ENVIRONMENT` o **Nome**. Para **valor,** forneça o ambiente `Staging`(por exemplo, ).
1. Selecione a caixa **de sote de configuração de implantação** se desejar que a configuração do ambiente permaneça com o slot atual quando os slots de implantação forem trocados. Para obter mais informações, consulte [Configurar ambientes de preparação no Azure App Service](/azure/app-service/web-sites-staged-publishing) na documentação do Azure.
1. Selecione **OK** para fechar a janela **de configuração do aplicativo Adicionar/Editar.**
1. Selecione **Salvar** na parte superior da **lâmina de configuração.**

O Serviço de Aplicativo do Azure reinicia automaticamente o aplicativo após uma configuração de aplicativo (variável de ambiente) ser adicionada, alterada ou excluída no portal do Azure.

#### <a name="windows"></a>Windows

Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:

**Prompt de comando**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Esses comandos somente têm efeito para a janela atual. Quando a janela é fechada, a configuração `ASPNETCORE_ENVIRONMENT` é revertida para a configuração padrão ou o valor de computador.

Para definir o valor globalmente no Windows, use uma das seguintes abordagens:

* Abra o **Painel de Controle** > **Sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:

  **Prompt de comando**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  O comutador `/M` indica para definir a variável de ambiente no nível do sistema. Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema. Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.

Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido.

**web.config**

Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Arquivo de projeto ou perfil de publicação**

**Para implantações do Windows IIS:** Inclua `<EnvironmentName>` a propriedade no [perfil de publicação (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou arquivo do projeto. Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Por pool de aplicativos do IIS**

Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.

> [!IMPORTANT]
> Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:
>
> * Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.
> * Reinicie o servidor.

#### <a name="macos"></a>macOS

A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*. Edite o arquivo usando qualquer editor de texto. Adicione a seguinte instrução:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Para distribuições Linux, use o comando `export` no prompt de comando para as configurações de variável baseadas na sessão e o arquivo *bash_profile* para as configurações de ambiente no nível do computador.

### <a name="set-the-environment-in-code"></a>Defina o ambiente em código

Ligue <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> quando construir o hospedeiro. Consulte <xref:fundamentals/host/generic-host#environmentname>.


### <a name="configuration-by-environment"></a>Configuração por ambiente

Para carregar a configuração por ambiente, recomendamos:

* *appsettings* arquivos *(appsettings.{ Ambiente}.json*). Consulte <xref:fundamentals/configuration/index#json-configuration-provider>.
* Variáveis de ambiente (definidas em cada sistema onde o aplicativo está hospedado). Veja <xref:fundamentals/host/generic-host#environmentname> e <xref:security/app-secrets#environment-variables>.
* Gerenciador de Segredo (somente no ambiente de desenvolvimento). Consulte <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Métodos e classe Startup baseados no ambiente

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a>Injete o IWebHostEnvironment em Startup.Configure

Injete <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> em `Startup.Configure`. Essa abordagem é útil quando `Startup.Configure` o aplicativo requer apenas ajustes para alguns ambientes com diferenças mínimas de código por ambiente.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Injete iWebHostEnvironment na classe Startup

<xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> Injete `Startup` na construtora. Essa abordagem é útil quando `Startup` o aplicativo requer configuração para apenas alguns ambientes com diferenças mínimas de código por ambiente.

No exemplo a seguir:

* O ambiente é `_env` mantido no campo.
* `_env`é usado `ConfigureServices` `Configure` e para aplicar a configuração de inicialização com base no ambiente do aplicativo.

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```
### <a name="startup-class-conventions"></a>Convenções da classe Startup

Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo. O aplicativo pode `Startup` definir classes separadas para `StartupDevelopment`diferentes ambientes (por exemplo, ). A `Startup` classe apropriada é selecionada em tempo de execução. A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada. Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada. Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.

Para implementar classes `Startup` com base em ambiente, crie uma classe `Startup{EnvironmentName}` para cada ambiente no uso e classe `Startup` de fallback:

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a>Convenções do método Startup

[Configurar](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigurarServiços](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) suportam versões `Configure<EnvironmentName>` `Configure<EnvironmentName>Services`específicas do ambiente do formulário e . Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de runtime usando uma variável de ambiente.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ambientes

O ASP.NET Core lê a variável de ambiente `ASPNETCORE_ENVIRONMENT` na inicialização do aplicativo e armazena o valor em [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`pode ser definido como qualquer valor, mas três valores são fornecidos pelo framework:

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (padrão)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

O código anterior:

* Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.
* Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido com um dos seguintes:

  * `Staging`
  * `Production`
  * `Staging_2`

O [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) `IHostingEnvironment.EnvironmentName` usa o valor de incluir ou excluir marcação no elemento:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

No Windows e no macOS, valores e variáveis de ambiente não diferenciam maiúsculas de minúsculas. Valores e variáveis de ambiente do Linux **diferenciam maiúsculas de minúsculas** por padrão.

### <a name="development"></a>Desenvolvimento

O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção. Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.

O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto. Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.

O seguinte JSON mostra três perfis de um arquivo *launchSettings.json*:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> A propriedade `applicationUrl` no *launchSettings.json* pode especificar uma lista de URLs de servidores. Use um ponto e vírgula entre as URLs na lista:
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

Quando o aplicativo é inicializado com [dotnet run](/dotnet/core/tools/dotnet-run), o primeiro perfil com `"commandName": "Project"` é usado. O valor de `commandName` especifica o servidor Web a ser iniciado. `commandName` pode ser qualquer um dos seguintes:

* `IISExpress`
* `IIS`
* `Project` (que inicia o Kestrel)

Quando um aplicativo for iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):

* *launchSettings.json* é lido, se está disponível. As configurações de `environmentVariables` em *launchSettings.json* substituem as variáveis de ambiente.
* O ambiente de hospedagem é exibido.

A saída a seguir mostra um aplicativo iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

A guia **Depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o arquivo *launchSettings.json*:

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado. O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.

> [!WARNING]
> *launchSettings.json* não deve armazenar segredos. A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.

Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*. O exemplo a seguir define o ambiente como `Development`:

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

Um arquivo *.vscode/launch.json* do projeto não é lido ao iniciar o aplicativo com `dotnet run` da mesma maneira que *Properties/launchSettings.json*. Ao inicializar um aplicativo em desenvolvimento que não tem um arquivo *launchSettings.json*, defina o ambiente com uma variável de ambiente ou um argumento de linha de comando para o comando `dotnet run`.

### <a name="production"></a>Produção

O ambiente de produção deve ser configurado para maximizar a segurança, o desempenho e a robustez do aplicativo. Algumas configurações comuns que são diferentes do desenvolvimento incluem:

* Armazenamento em cache.
* Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.
* Páginas de erro de diagnóstico desabilitadas.
* Páginas de erro amigáveis habilitadas.
* Log de produção e monitoramento habilitados. Por exemplo, [Insights de aplicativos](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Definir o ambiente

Muitas vezes é útil definir um ambiente específico para testes com uma variável de ambiente ou configuração de plataforma. Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração. O método para configurar o ambiente depende do sistema operacional.

Quando o host é construído, a última configuração do ambiente lida pelo aplicativo determina o ambiente do aplicativo. O ambiente do aplicativo não pode ser alterado enquanto o aplicativo estiver em execução.

### <a name="environment-variable-or-platform-setting"></a>Configuração de ambiente ou plataforma

#### <a name="azure-app-service"></a>Serviço de aplicativo do Azure

Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:

1. Selecione o aplicativo na folha **Serviços de Aplicativos**.
1. No grupo **Configurações,** selecione a **lâmina Configuração.**
1. Na guia **Configurações** do aplicativo, selecione **Nova configuração do aplicativo**.
1. Na janela **de configuração de aplicativo Adicionar/Editar,** forneça `ASPNETCORE_ENVIRONMENT` o **Nome**. Para **valor,** forneça o ambiente `Staging`(por exemplo, ).
1. Selecione a caixa **de sote de configuração de implantação** se desejar que a configuração do ambiente permaneça com o slot atual quando os slots de implantação forem trocados. Para obter mais informações, consulte [Configurar ambientes de preparação no Azure App Service](/azure/app-service/web-sites-staged-publishing) na documentação do Azure.
1. Selecione **OK** para fechar a janela **de configuração do aplicativo Adicionar/Editar.**
1. Selecione **Salvar** na parte superior da **lâmina de configuração.**

O Serviço de Aplicativo do Azure reinicia automaticamente o aplicativo após uma configuração de aplicativo (variável de ambiente) ser adicionada, alterada ou excluída no portal do Azure.

#### <a name="windows"></a>Windows

Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:

**Prompt de comando**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Esses comandos somente têm efeito para a janela atual. Quando a janela é fechada, a configuração `ASPNETCORE_ENVIRONMENT` é revertida para a configuração padrão ou o valor de computador.

Para definir o valor globalmente no Windows, use uma das seguintes abordagens:

* Abra o **Painel de Controle** > **Sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:

  **Prompt de comando**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  O comutador `/M` indica para definir a variável de ambiente no nível do sistema. Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema. Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.

Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido.

**web.config**

Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Arquivo de projeto ou perfil de publicação**

**Para implantações do Windows IIS:** Inclua `<EnvironmentName>` a propriedade no [perfil de publicação (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou arquivo do projeto. Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Por pool de aplicativos do IIS**

Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.

> [!IMPORTANT]
> Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:
>
> * Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.
> * Reinicie o servidor.

#### <a name="macos"></a>macOS

A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*. Edite o arquivo usando qualquer editor de texto. Adicione a seguinte instrução:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Para distribuições Linux, use o comando `export` no prompt de comando para as configurações de variável baseadas na sessão e o arquivo *bash_profile* para as configurações de ambiente no nível do computador.

### <a name="set-the-environment-in-code"></a>Defina o ambiente em código

Ligue <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> quando construir o hospedeiro. Consulte <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Configuração por ambiente

Para carregar a configuração por ambiente, recomendamos:

* *appsettings* arquivos *(appsettings.{ Ambiente}.json*). Consulte <xref:fundamentals/configuration/index#json-configuration-provider>.
* Variáveis de ambiente (definidas em cada sistema onde o aplicativo está hospedado). Veja <xref:fundamentals/host/web-host#environment> e <xref:security/app-secrets#environment-variables>.
* Gerenciador de Segredo (somente no ambiente de desenvolvimento). Consulte <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Métodos e classe Startup baseados no ambiente

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Injete o IHostingEnvironment na inicialização.Configure

Injete <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> em `Startup.Configure`. Essa abordagem é útil quando `Startup.Configure` o aplicativo requer apenas a configuração para apenas alguns ambientes com diferenças mínimas de código por ambiente.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>Injete iHostingEnvironment na classe Startup

<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> Injete `Startup` no construtor e atribua o serviço `Startup` a um campo para uso em toda a classe. Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para apenas alguns ambientes com diferenças mínimas de código por ambiente.

No exemplo a seguir:

* O ambiente é `_env` mantido no campo.
* `_env`é usado `ConfigureServices` `Configure` e para aplicar a configuração de inicialização com base no ambiente do aplicativo.

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a>Convenções da classe Startup

Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo. O aplicativo pode `Startup` definir classes separadas para `StartupDevelopment`diferentes ambientes (por exemplo, ). A `Startup` classe apropriada é selecionada em tempo de execução. A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada. Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada. Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.

Para implementar classes `Startup` com base em ambiente, crie uma classe `Startup{EnvironmentName}` para cada ambiente no uso e classe `Startup` de fallback:

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a>Convenções do método Startup

[Configurar](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigurarServiços](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) suportam versões `Configure<EnvironmentName>` `Configure<EnvironmentName>Services`específicas do ambiente do formulário e . Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
