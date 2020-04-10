---
title: Compilação de arquivo do Razor no ASP.NET Core
author: rick-anderson
description: Saiba como a compilação de arquivos do Razor ocorre em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 7f329ffb4c63e8699663f49720145984bb8802fd
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994601"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Compilação de arquivo do Razor no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Os arquivos razor com uma extensão *.cshtml* são compilados em tempo de compilação e publicação usando o [Razor SDK](xref:razor-pages/sdk). A compilação do runtime pode ser opcionalmente habilitada através da configuração do aplicativo.

## <a name="razor-compilation"></a>Compilação do Razor

A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor. Quando habilitada, a compilação de runtime complementa a compilação de tempo de build, permitindo que os arquivos do Razor sejam atualizados se eles forem editados.

## <a name="runtime-compilation"></a>Compilação de runtime

Para habilitar a compilação em tempo de execução para todos os ambientes e modos de configuração:

1. Instale o pacote [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.

1. Atualize o `Startup.ConfigureServices` método do projeto `AddRazorRuntimeCompilation`para incluir uma chamada para . Por exemplo:

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

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

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