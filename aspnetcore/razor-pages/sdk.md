---
title: SDK Razor do ASP.NET Core
author: Rick-Anderson
description: Saiba como Razor as páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/sdk
ms.openlocfilehash: a49f183c4c037a1654e79bdb672b758684137cbe
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776728"
---
# <a name="aspnet-core-razor-sdk"></a>SDK do Razor do ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Visão geral

O [!INCLUDE[](~/includes/2.1-SDK.md)] inclui o SDK do MSBuild `Microsoft.NET.Sdk.Razor` (SDK do Razor). O SDK do Razor:

::: moniker range=">= aspnetcore-3.0"

* É necessário para compilar, empacotar e publicar projetos que contenham arquivos [Razor](xref:mvc/views/razor) [para projetos do](xref:blazor/index) ASP.NET Core com base em MVC ou mais.
* Inclui um conjunto de destinos, propriedades e itens predefinidos que permitem personalizar a compilação de arquivos Razor (*. cshtml* ou *. Razor*).

O SDK do Razor `Content` inclui itens `Include` com atributos definidos para `**\*.cshtml` os `**\*.razor` padrões de mascaramento e. Os arquivos correspondentes são publicados.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Padroniza a experiência de criação, empacotamento e publicação de projetos que contêm arquivos [Razor](xref:mvc/views/razor) para projetos baseados no ASP.NET Core MVC.
* Inclui um conjunto de destinos, propriedades e itens predefinidos que permitem personalizar a compilação de arquivos Razor.

O SDK do Razor inclui `Content` um item com `Include` um atributo definido para `**\*.cshtml` o padrão de mascaramento. Os arquivos correspondentes são publicados.

::: moniker-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Usar o SDK do Razor

A maioria dos aplicativos Web não é necessária para referenciar explicitamente o SDK do Razor.

::: moniker range=">= aspnetcore-3.0"

Para usar o SDK do Razor para criar bibliotecas de classes que contêm exibições do Razor ou Razor Pages, é recomendável começar com o modelo de projeto RCL (biblioteca de classes Razor). Um RCL que é usado para criar arquivos de mais e (*. Razor*) requer minimamente uma referência ao pacote [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . Um RCL que é usado para criar exibições ou páginas do Razor (arquivos *. cshtml* ) requer minimamente o direcionamento `netcoreapp3.0` ou `FrameworkReference` posterior e tem um para o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) em seu arquivo de projeto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Para usar o SDK do Razor para criar bibliotecas de classe contendo exibições Razor ou Páginas Razor:

* Use `Microsoft.NET.Sdk.Razor` em vez de `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Normalmente, uma referência de pacote `Microsoft.AspNetCore.Mvc` a é necessária para receber dependências adicionais que são necessárias para compilar e compilar Razor Pages e exibições do Razor. No mínimo, o projeto deve adicionar referências de pacote a:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  O `Microsoft.AspNetCore.Razor.Design` pacote fornece as tarefas de compilação do Razor e os destinos para o projeto.

  Os pacotes anteriores são incluídos em `Microsoft.AspNetCore.Mvc`. A marcação a seguir mostra um arquivo de projeto que usa o SDK do Razor para criar arquivos Razor para um aplicativo ASP.NET Core Razor Pages:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Os `Microsoft.AspNetCore.Razor.Design` pacotes `Microsoft.AspNetCore.Mvc.Razor.Extensions` e estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app). No entanto, a referência `Microsoft.AspNetCore.App` de pacote sem versão fornece um metapacote para o aplicativo que não inclui a versão `Microsoft.AspNetCore.Razor.Design`mais recente do. Os projetos devem fazer referência a uma `Microsoft.AspNetCore.Razor.Design` versão consistente `Microsoft.AspNetCore.Mvc`do (ou) para que as correções de tempo de compilação mais recentes para o Razor sejam incluídas. Para obter mais informações, consulte [este problema do GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Propriedades

As seguintes propriedades controlam o comportamento do SDK do Razor como parte de um build de projeto:

* `RazorCompileOnBuild`&ndash; Quando `true`, o compila e emite o assembly Razor como parte da criação do projeto. O padrão é `true`.
* `RazorCompileOnPublish`&ndash; Quando `true`, o compila e emite o assembly Razor como parte da publicação do projeto. O padrão é `true`.

As propriedades e os itens na tabela a seguir são usados para configurar entradas e saídas para o SDK do Razor.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> A partir do ASP.NET Core 3,0, as exibições do MVC ou Razor Pages não serão `RazorCompileOnBuild` servidas por padrão se as propriedades do ou `RazorCompileOnPublish` do MSBuild no arquivo de projeto estiverem desabilitadas. Os aplicativos devem adicionar uma referência explícita ao pacote [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) se o aplicativo depender da compilação de tempo de execução para processar arquivos *. cshtml* .

::: moniker-end

| Itens | Descrição |
| ----- | ----------- |
| `RazorGenerate` | Elementos de item (arquivos *. cshtml* ) que são entradas para geração de código. |
| `RazorComponent` | Elementos de item (arquivos *. Razor* ) que são entradas para a geração de código de componente Razor. |
| `RazorCompile` | Elementos de item (arquivos *. cs* ) que são entradas para destinos de compilação do Razor. Use isso `ItemGroup` para especificar arquivos adicionais a serem compilados no assembly Razor. |
| `RazorTargetAssemblyAttribute` | Os elementos de item usados para a codificação geram atributos para o assembly Razor. Por exemplo:   <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elementos de item adicionados como recursos incorporados ao assembly do Razor gerado. |

::: moniker range=">= aspnetcore-3.0"

| Propriedade | Descrição |
| -------- | ----------- |
| `RazorTargetName` | Nome do arquivo (sem extensão) do assembly produzido pelo Razor. |
| `RazorOutputPath` | O diretório de saída do Razor. |
| `RazorCompileToolset` | Usado para determinar o conjunto de ferramentas usado para criar o assembly do Razor. Os valores válidos são `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | O padrão é `true`. Quando `true`, inclui arquivos *Web. config*, *. JSON*e *. cshtml* como conteúdo no projeto. Quando referenciado `Microsoft.NET.Sdk.Web`por meio de, os arquivos em arquivos *wwwroot* e config também são incluídos. |
| `EnableDefaultRazorGenerateItems` | Quando `true`, inclui arquivos *.cshtml* de itens de `Content` em itens de `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true`, o gera um arquivo *. cs* contendo atributos especificados `RazorAssemblyAttribute` por e inclui o arquivo na saída de compilação. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando `true`, adiciona um conjunto padrão de atributos de assembly em `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true`, o `RazorGenerate` copia os arquivos de itens (*. cshtml*) para o diretório de publicação. Normalmente, os arquivos do Razor não são necessários para um aplicativo publicado se participarem da compilação em tempo de compilação ou em tempo de publicação. O padrão é `false`. |
| `PreserveCompilationReferences` | Quando `true`, copia os itens do assembly de referência no diretório de publicação. Normalmente, os assemblies de referência não são necessários para um aplicativo publicado se a compilação do Razor ocorrer em tempo de compilação ou em tempo de publicação. Defina como `true` se seu aplicativo publicado exigir compilação em tempo de execução. Por exemplo, defina o valor como `true` se o aplicativo modifica arquivos *. cshtml* em tempo de execução ou usa exibições inseridas. O padrão é `false`. |
| `IncludeRazorContentInPack` | Quando `true`, todos os itens de conteúdo do Razor (arquivos *. cshtml* ) são marcados para inclusão no pacote NuGet gerado. O padrão é `false`. |
| `EmbedRazorGenerateSources` | Quando `true`, adiciona itens de RazorGenerate (*.cshtml*) como arquivos incorporados ao assembly Razor gerado. O padrão é `false`. |
| `UseRazorBuildServer` | Quando `true`, usa um processo de servidor de build persistente para descarregar o trabalho de geração de código. Seu valor padrão é `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true`, o SDK gera atributos adicionais usados pelo MVC em tempo de execução para executar a descoberta de parte do aplicativo. |
| `DefaultWebContentItemExcludes` | Um padrão de mascaramento para elementos de item que devem ser excluídos `Content` do grupo de itens em projetos direcionados para o SDK Web ou Razor |
| `ExcludeConfigFilesFromBuildOutput` | Quando `true`os arquivos *. config* e *. JSON* não são copiados para o diretório de saída da compilação. |
| `AddRazorSupportForMvc` | Quando `true`, o configura o SDK do Razor para adicionar suporte para a configuração do MVC que é necessária ao criar aplicativos que contenham exibições ou Razor pages do MVC. Essa propriedade é definida implicitamente para projetos .NET Core 3,0 ou posterior direcionados para o SDK da Web |
| `RazorLangVersion` | A versão do idioma do Razor para o destino. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Propriedade | Descrição |
| -------- | ----------- |
| `RazorTargetName` | Nome do arquivo (sem extensão) do assembly produzido por Razor. |
| `RazorOutputPath` | O Razor diretório de saída. |
| `RazorCompileToolset` | Usado para determinar o conjunto de ferramentas usado para Razor compilar o assembly. Os valores válidos são `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | O padrão é `true`. Quando `true`, inclui arquivos *Web. config*, *. JSON*e *. cshtml* como conteúdo no projeto. Quando referenciado `Microsoft.NET.Sdk.Web`por meio de, os arquivos em arquivos *wwwroot* e config também são incluídos. |
| `EnableDefaultRazorGenerateItems` | Quando `true`, inclui arquivos *.cshtml* de itens de `Content` em itens de `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true`, o gera um arquivo *. cs* contendo atributos especificados `RazorAssemblyAttribute` por e inclui o arquivo na saída de compilação. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando `true`, adiciona um conjunto padrão de atributos de assembly em `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true`, o `RazorGenerate` copia os arquivos de itens (*. cshtml*) para o diretório de publicação. Normalmente, Razor os arquivos não são necessários para um aplicativo publicado se participarem da compilação em tempo de compilação ou em tempo de publicação. O padrão é `false`. |
| `CopyRefAssembliesToPublishDirectory` | Quando `true`, copia os itens do assembly de referência no diretório de publicação. Normalmente, os assemblies de referência não são necessários para um Razor aplicativo publicado se a compilação ocorrer em tempo de compilação ou em tempo de publicação. Defina como `true` se seu aplicativo publicado exigir compilação em tempo de execução. Por exemplo, defina o valor como `true` se o aplicativo modifica arquivos *. cshtml* em tempo de execução ou usa exibições inseridas. O padrão é `false`. |
| `IncludeRazorContentInPack` | Quando `true`, todos Razor os itens de conteúdo (arquivos *. cshtml* ) são marcados para inclusão no pacote NuGet gerado. O padrão é `false`. |
| `EmbedRazorGenerateSources` | Quando `true`, o adiciona itens RazorGenerate (*. cshtml*) como arquivos incorporados ao Razor assembly gerado. O padrão é `false`. |
| `UseRazorBuildServer` | Quando `true`, usa um processo de servidor de build persistente para descarregar o trabalho de geração de código. Seu valor padrão é `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true`, o SDK gera atributos adicionais usados pelo MVC em tempo de execução para executar a descoberta de parte do aplicativo. |
| `DefaultWebContentItemExcludes` | Um padrão de mascaramento para elementos de item que devem ser excluídos `Content` do grupo de itens em projetos direcionados Razor à Web ou ao SDK |
| `ExcludeConfigFilesFromBuildOutput` | Quando `true`os arquivos *. config* e *. JSON* não são copiados para o diretório de saída da compilação. |
| `AddRazorSupportForMvc` | Quando `true`, o configura o Razor SDK para adicionar suporte para a configuração do MVC que é necessária ao criar aplicativos que contenham Razor exibições ou páginas do MVC. Essa propriedade é definida implicitamente para projetos .NET Core 3,0 ou posterior direcionados para o SDK da Web |
| `RazorLangVersion` | A versão do Razor idioma para o destino. |

::: moniker-end

Para saber mais sobre as propriedades, confira [Propriedades do MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Destinos

O Razor SDK define dois destinos principais:

* `RazorGenerate`&ndash; O código gera arquivos *. cs* de elementos de `RazorGenerate` item. Use a `RazorGenerateDependsOn` propriedade para especificar destinos adicionais que podem ser executados antes ou depois desse destino.
* `RazorCompile`&ndash; Compila arquivos *. cs* gerados em um Razor assembly. Use o `RazorCompileDependsOn` para especificar destinos adicionais que podem ser executados antes ou depois desse destino.
* `RazorComponentGenerate`&ndash; O código gera arquivos *. cs* para elementos de `RazorComponent` item. Use a `RazorComponentGenerateDependsOn` propriedade para especificar destinos adicionais que podem ser executados antes ou depois desse destino.

### <a name="runtime-compilation-of-razor-views"></a>Compilação em tempo Razor de execução de exibições

* Por padrão, o Razor SDK não publica assemblies de referência que são necessários para executar a compilação em tempo de execução. Isso resulta em falhas de compilação quando o modelo de aplicativo se baseia na compilação em runtime&mdash;, por exemplo, o aplicativo usa exibições inseridas ou muda as exibições depois que o aplicativo é publicado. Defina `CopyRefAssembliesToPublishDirectory` como `true` para continuar publicando assemblies de referência.

* Para um aplicativo Web, verifique se seu aplicativo está direcionando o `Microsoft.NET.Sdk.Web` SDK.

## <a name="razor-language-version"></a>Razorversão do idioma

Ao direcionar `Microsoft.NET.Sdk.Web` o SDK, Razor a versão de idioma é inferida da versão da estrutura de destino do aplicativo. Para projetos direcionados `Microsoft.NET.Sdk.Razor` ao SDK ou, no caso raro, que o aplicativo exija Razor uma versão de idioma diferente do valor inferido, uma versão pode ser configurada definindo a `<RazorLangVersion>` Propriedade no arquivo de projeto do aplicativo:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razora versão de idioma do é totalmente integrada à versão do tempo de execução para a qual foi criada. O direcionamento de uma versão de idioma que não é projetada para o tempo de execução não é suportado e provavelmente produz erros de compilação.

## <a name="additional-resources"></a>Recursos adicionais

* [Adições ao formato csproj para .NET Core](/dotnet/core/tools/csproj)
* [Itens de projeto comuns do MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
