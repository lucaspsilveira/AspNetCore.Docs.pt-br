---
title: Razorcompilação de arquivo no ASP.NET Core
author: rick-anderson
description: Saiba como a compilação de Razor arquivos ocorre em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-compilation
ms.openlocfilehash: 71487ff2d5d7d7cf96835778f386e5f30fa32254
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405439"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Razorcompilação de arquivo no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Razorarquivos com uma extensão *. cshtml* são compilados em tempo de compilação e publicação usando o [ Razor SDK](xref:razor-pages/sdk). A compilação em tempo de execução pode ser habilitada opcionalmente Configurando seu projeto.

## <a name="razor-compilation"></a>Razorocorrida

A compilação de arquivos em tempo de compilação e em tempo de publicação Razor é habilitada por padrão pelo Razor SDK. Quando habilitada, a compilação de tempo de execução complementa a compilação em tempo de compilação, permitindo que Razor os arquivos sejam atualizados se eles forem editados.

## <a name="enable-runtime-compilation-at-project-creation"></a>Habilitar a compilação em tempo de execução na criação do projeto

As Razor páginas e os modelos de projeto MVC incluem uma opção para habilitar a compilação em tempo de execução quando o projeto é criado. Essa opção tem suporte no ASP.NET Core 3,1 e posterior.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Na caixa de diálogo **criar um novo aplicativo web ASP.NET Core** :

1. Selecione o modelo de **projeto do aplicativo Web ou do** **aplicativo Web (Model-View-Controller)** .
1. Marque a caixa de seleção **habilitar Razor compilação de tempo de execução** .

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Use a `-rrc` `--razor-runtime-compilation` opção de modelo ou. Por exemplo, o comando a seguir cria um novo Razor projeto de páginas com compilação em tempo de execução habilitada:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Habilitar a compilação em tempo de execução em um projeto existente

Para habilitar a compilação em tempo de execução para todos os ambientes em um projeto existente:

1. Instale o [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Pacote NuGet do RuntimeCompilation.
1. Atualize o método do projeto `Startup.ConfigureServices` para incluir uma chamada para <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> . Por exemplo:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Habilitar condicionalmente a compilação em tempo de execução em um projeto existente

A compilação em tempo de execução pode ser habilitada de modo que esteja disponível apenas para desenvolvimento local. Habilitar condicionalmente dessa maneira garante que a saída publicada:

* Usa exibições compiladas.
* Não habilita os inspetores de arquivo na produção.

Para habilitar a compilação em tempo de execução somente no ambiente de desenvolvimento:

1. Instale o [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Pacote NuGet do RuntimeCompilation.
1. Modifique a seção de perfil de inicialização `environmentVariables` no *launchSettings.jsem*:
    * Verifique se `ASPNETCORE_ENVIRONMENT` está definido como `"Development"` .
    * Defina `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` como `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.

No exemplo a seguir, a compilação em tempo de execução está habilitada no ambiente de desenvolvimento para os `IIS Express` `RazorPagesApp` perfis de inicialização e:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

Nenhuma alteração de código é necessária na classe do projeto `Startup` . Em tempo de execução, ASP.NET Core procura um [atributo HostingStartup no nível de assembly](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) no `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` . O `HostingStartup` atributo especifica o código de inicialização do aplicativo a ser executado. Esse código de inicialização habilita a compilação em tempo de execução.

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a>Habilitar a compilação em tempo de execução para uma Razor biblioteca de classes

Considere um cenário no qual um Razor projeto de páginas referencia uma [ Razor biblioteca de classes (RCL)](xref:razor-pages/ui-class) chamada *MyClassLib*. O RCL contém um arquivo *_Layout. cshtml* que todos os projetos de página e MVC da sua equipe Razor consomem. Você deseja habilitar a compilação em tempo de execução para o arquivo *_Layout. cshtml* nesse RCL. Faça as seguintes alterações no Razor projeto de páginas:

1. Habilite a compilação em tempo de execução com as instruções em [habilitar condicionalmente a compilação em tempo de execução em um projeto existente](#conditionally-enable-runtime-compilation-in-an-existing-project).
1. Configure as opções de compilação de tempo de execução em `Startup.ConfigureServices` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    No código anterior, um caminho absoluto para o *MyClassLib* RCL é construído. A [API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) é usada para localizar diretórios e arquivos nesse caminho absoluto. Por fim, a `PhysicalFileProvider` instância é adicionada a uma coleção de provedores de arquivos, que permite o acesso aos arquivos *. cshtml* do RCL.

## <a name="additional-resources"></a>Recursos adicionais

* Propriedades [RazorCompileOnBuild e RazorCompileOnPublish](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razorarquivos com uma extensão *. cshtml* são compilados em tempo de compilação e publicação usando o [ Razor SDK](xref:razor-pages/sdk). A compilação do runtime pode ser opcionalmente habilitada através da configuração do aplicativo.

## <a name="razor-compilation"></a>Razorocorrida

A compilação de arquivos em tempo de compilação e em tempo de publicação Razor é habilitada por padrão pelo Razor SDK. Quando habilitada, a compilação de tempo de execução complementa a compilação em tempo de compilação, permitindo que Razor os arquivos sejam atualizados se eles forem editados.

## <a name="runtime-compilation"></a>Compilação de runtime

Para habilitar a compilação em tempo de execução para todos os ambientes e modos de configuração:

1. Instale o [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Pacote NuGet do RuntimeCompilation.

1. Atualize o método do projeto `Startup.ConfigureServices` para incluir uma chamada para <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> . Por exemplo:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Habilitar condicionalmente a compilação em tempo de execução

A compilação em tempo de execução pode ser habilitada de modo que esteja disponível apenas para desenvolvimento local. Habilitar condicionalmente dessa maneira garante que a saída publicada:

* Usa exibições compiladas.
* É menor em tamanho.
* Não habilita os inspetores de arquivo na produção.

Para habilitar a compilação em tempo de execução com base no ambiente e no modo de configuração:

1. Referenciar condicionalmente o [Microsoft. AspNetCore. Mvc. Razor . Pacote RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) com base no `Configuration` valor ativo:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Atualize o método do projeto `Startup.ConfigureServices` para incluir uma chamada para `AddRazorRuntimeCompilation` . Execute condicionalmente `AddRazorRuntimeCompilation` para que ele seja executado somente no modo de depuração quando a `ASPNETCORE_ENVIRONMENT` variável for definida como `Development` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionais

* Propriedades [RazorCompileOnBuild e RazorCompileOnPublish](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Consulte o [exemplo de compilação em tempo de execução no GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obter um exemplo que mostra como fazer trabalhos de compilação de tempo de execução entre projetos.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Um Razor arquivo é compilado em tempo de execução, quando a Razor página associada ou a exibição MVC é invocada. Razoros arquivos são compilados em tempo de compilação e publicação usando o [ Razor SDK](xref:razor-pages/sdk).

## <a name="razor-compilation"></a>Razorocorrida

A compilação de arquivos em tempo de compilação e publicação Razor é habilitada por padrão pelo Razor SDK. RazorA edição de arquivos depois que eles são atualizados tem suporte no momento da compilação. Por padrão, somente os *Views.dll* compilados e nenhum arquivo *. cshtml* ou fazem referência a assemblies necessários para compilar Razor arquivos são implantados com seu aplicativo.

> [!IMPORTANT]
> A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0. É recomendável migrar para o [ Razor SDK](xref:razor-pages/sdk).
>
> O Razor SDK só será eficaz quando nenhuma propriedade específica de pré-compilação for definida no arquivo de projeto. Por exemplo, definir a propriedade do arquivo *. csproj* `MvcRazorCompileOnPublish` para `true` desabilitar o Razor SDK.

## <a name="runtime-compilation"></a>Compilação de runtime

A compilação em tempo de compilação é complementada pela compilação de arquivos em tempo de execução Razor . ASP.NET Core MVC recompilará Razor os arquivos quando o conteúdo de um arquivo *. cshtml* for alterado.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
