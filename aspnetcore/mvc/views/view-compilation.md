---
title: Compilação de arquivo do Razor no ASP.NET Core
author: rick-anderson
description: Saiba como a compilação de arquivos do Razor ocorre em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277257"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Compilação de arquivo do Razor no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Os arquivos razor com uma extensão *.cshtml* são compilados em tempo de compilação e publicação usando o [Razor SDK](xref:razor-pages/sdk). A compilação em tempo de execução pode ser opcionalmente ativada pela configuração do seu projeto.

## <a name="razor-compilation"></a>Compilação do Razor

A compilação de tempo de compilação de arquivos Razor e tempo de compilação de tempo de compilação de arquivos Razor é habilitada por padrão pelo Razor SDK. Quando ativada, a compilação em tempo de execução complementa a compilação de tempo de compilação, permitindo que os arquivos Razor sejam atualizados se forem editados.

## <a name="enable-runtime-compilation-at-project-creation"></a>Habilite a compilação em tempo de execução na criação do projeto

Os modelos de projeto Razor Pages e MVC incluem uma opção para ativar a compilação em tempo de execução quando o projeto for criado. Esta opção é suportada em ASP.NET Core 3.1 e posterior.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

No Criar uma nova caixa de diálogo **de aplicativo web ASP.NET Core:**

1. Selecione o **aplicativo da Web** ou o modelo de projeto Do aplicativo da Web **(Model-View-Controller).**
1. Selecione a caixa **de seleção De seleção De seleção De habilitação Razor runtime.**

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Use `-rrc` a `--razor-runtime-compilation` opção ou modelo. Por exemplo, o comando a seguir cria um novo projeto Razor Pages com compilação em tempo de execução ativada:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Habilite a compilação em tempo de execução em um projeto existente

Para habilitar a compilação em tempo de execução para todos os ambientes de um projeto existente:

1. Instale o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.
1. Atualize o `Startup.ConfigureServices` método do projeto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>para incluir uma chamada para . Por exemplo:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Habilitar condicionalmente a compilação em tempo de execução em um projeto existente

A compilação em tempo de execução pode ser ativada de tal forma que esteja disponível apenas para o desenvolvimento local. Habilitar condicionalmente dessa forma garante que a saída publicada:

* Usa visualizações compiladas.
* Não permite observadores de arquivos em produção.

Para habilitar a compilação em tempo de execução apenas no ambiente desenvolvimento:

1. Instale o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.
1. Modifique a `environmentVariables` seção de perfil de lançamento em *launchSettings.json*:
    * Verificar `ASPNETCORE_ENVIRONMENT` está `"Development"`definido como .
    * Defina `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` como `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.

No exemplo a seguir, a compilação em tempo `IIS Express` `RazorPagesApp` de execução é habilitada no ambiente Desenvolvimento para os perfis e de lançamento:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

Não são necessárias mudanças `Startup` de código na classe do projeto. Em tempo de execução, ASP.NET Core procura um [atributo HostingStartup em nível de montagem](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) em `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`. O `HostingStartup` atributo especifica o código de inicialização do aplicativo para ser executado. Esse código de inicialização permite a compilação em tempo de execução.

## <a name="additional-resources"></a>Recursos adicionais

* [Propriedades RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Consulte a [amostra de compilação em tempo de execução no GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obter uma amostra que mostra fazer a compilação em tempo de execução funcionar entre projetos.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Os arquivos razor com uma extensão *.cshtml* são compilados em tempo de compilação e publicação usando o [Razor SDK](xref:razor-pages/sdk). A compilação do runtime pode ser opcionalmente habilitada através da configuração do aplicativo.

## <a name="razor-compilation"></a>Compilação do Razor

A compilação de tempo de compilação de arquivos Razor e tempo de compilação de tempo de compilação de arquivos Razor é habilitada por padrão pelo Razor SDK. Quando ativada, a compilação em tempo de execução complementa a compilação de tempo de compilação, permitindo que os arquivos Razor sejam atualizados se forem editados.

## <a name="runtime-compilation"></a>Compilação de runtime

Para habilitar a compilação em tempo de execução para todos os ambientes e modos de configuração:

1. Instale o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.

1. Atualize o `Startup.ConfigureServices` método do projeto <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>para incluir uma chamada para . Por exemplo:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Habilitar condicionalmente a compilação em tempo de execução

A compilação em tempo de execução pode ser ativada de tal forma que esteja disponível apenas para o desenvolvimento local. Habilitar condicionalmente dessa forma garante que a saída publicada:

* Usa visualizações compiladas.
* É menor em tamanho.
* Não permite observadores de arquivos em produção.

Para habilitar a compilação em tempo de execução com base no ambiente e no modo de configuração:

1. Consulte condicionalmente o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) com base no valor ativo: `Configuration`

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Atualize o `Startup.ConfigureServices` método do projeto `AddRazorRuntimeCompilation`para incluir uma chamada para . Executar condicionalmente `AddRazorRuntimeCompilation` de modo que ele só `ASPNETCORE_ENVIRONMENT` é executado `Development`no modo Debug quando a variável é definida como :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionais

* [Propriedades RazorCompileOnBuild e RazorCompileOnPublish.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Consulte a [amostra de compilação em tempo de execução no GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obter uma amostra que mostra fazer a compilação em tempo de execução funcionar entre projetos.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Um arquivo do Razor é compilado em runtime, quando o modo de exibição do MVC ou da Página do Razor associada é chamado. Os arquivos do Razor são compilados em tempo de build e de publicação usando o [SDK do Razor](xref:razor-pages/sdk).

## <a name="razor-compilation"></a>Compilação do Razor

A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor. Há suporte para edição de arquivos do Razor depois que eles são atualizados em tempo de build. Por padrão, somente os arquivos *Views.dll* compilados, mas nenhum arquivo *.cshtml* ou assembly de referência necessário para compilar arquivos do Razor, são implantados com seu aplicativo.

> [!IMPORTANT]
> A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0. É recomendado migrar para o [SDK do Razor](xref:razor-pages/sdk).
>
> O SDK do Razor é eficaz somente quando não há propriedades específicas de pré-compilação definidas no arquivo de projeto. Por exemplo, definir a propriedade `MvcRazorCompileOnPublish` do arquivo *.csproj* como `true` desabilita o SDK do Razor.

## <a name="runtime-compilation"></a>Compilação de runtime

A compilação de tempo de build é complementada pela compilação de runtime de arquivos do Razor. O ASP.NET Core MVC recompilará arquivos do Razor quando o conteúdo de um arquivo *.cshtml* for alterado.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
