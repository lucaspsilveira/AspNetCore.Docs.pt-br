---
Título: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---
# <a name="aspnet-core-razor-sdk"></a>SDK do ASP.NET Core Razor

De [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Visão geral

O [!INCLUDE[](~/includes/2.1-SDK.md)] inclui o `Microsoft.NET.Sdk.Razor` SDK do MSBuild ( Razor SDK). O Razor SDK:

::: moniker range=">= aspnetcore-3.0"

* É necessário para compilar, empacotar e publicar projetos que contenham [Razor](xref:mvc/views/razor) arquivos para ASP.NET Core projetos ou baseados em MVC [Blazor](xref:blazor/index) .
* Inclui um conjunto de destinos, propriedades e itens predefinidos que permitem personalizar a compilação de Razor arquivos (*. cshtml* ou *. Razor*).

O Razor SDK inclui `Content` itens com `Include` atributos definidos para os `**\*.cshtml` `**\*.razor` padrões de mascaramento e. Os arquivos correspondentes são publicados.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Padroniza a experiência em relação à criação, ao empacotamento e à publicação de projetos que contêm [Razor](xref:mvc/views/razor) arquivos para ASP.NET Core projetos baseados em MVC.
* Inclui um conjunto de destinos, propriedades e itens predefinidos que permitem personalizar a compilação de Razor arquivos.

O Razor SDK inclui um `Content` item com um `Include` atributo definido para o `**\*.cshtml` padrão de mascaramento. Os arquivos correspondentes são publicados.

::: moniker-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Usar o Razor SDK

A maioria dos aplicativos Web não é necessária para referenciar explicitamente o Razor SDK.

::: moniker range=">= aspnetcore-3.0"

Para usar o Razor SDK para criar bibliotecas de classes que contenham Razor exibições ou Razor páginas, é recomendável começar com o Razor modelo de projeto RCL (biblioteca de classes). Um RCL que é usado para compilar Blazor arquivos (*. Razor*) minimamente requer uma referência ao pacote [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . Um RCL que é usado para criar Razor exibições ou páginas (arquivos *. cshtml* ) requer minimamente `netcoreapp3.0` o direcionamento ou posterior e tem um `FrameworkReference` para o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) em seu arquivo de projeto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Para usar o Razor SDK para criar bibliotecas de classes que contenham Razor exibições ou Razor páginas:

* Use `Microsoft.NET.Sdk.Razor` em vez de `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Normalmente, uma referência de pacote a `Microsoft.AspNetCore.Mvc` é necessária para receber dependências adicionais que são necessárias para criar e compilar Razor páginas e Razor exibições. No mínimo, o projeto deve adicionar referências de pacote a:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  O `Microsoft.AspNetCore.Razor.Design` pacote fornece as Razor tarefas de compilação e destinos para o projeto.

  Os pacotes anteriores são incluídos em `Microsoft.AspNetCore.Mvc`. A marcação a seguir mostra um arquivo de projeto que usa o Razor SDK para criar Razor arquivos para um aplicativo de ASP.NET Core Razor páginas:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Os `Microsoft.AspNetCore.Razor.Design` `Microsoft.AspNetCore.Mvc.Razor.Extensions` pacotes e estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app). No entanto, a referência de pacote sem versão `Microsoft.AspNetCore.App` fornece um metapacote para o aplicativo que não inclui a versão mais recente do `Microsoft.AspNetCore.Razor.Design` . Os projetos devem fazer referência a uma versão consistente do `Microsoft.AspNetCore.Razor.Design` (ou `Microsoft.AspNetCore.Mvc` ) para que as correções de tempo de compilação mais recentes para o Razor sejam incluídas. Para obter mais informações, consulte [este problema do GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Propriedades

As propriedades a seguir controlam o Razor comportamento do SDK como parte de uma compilação de projeto:

* `RazorCompileOnBuild`: Quando `true` , compila e emite o Razor assembly como parte da criação do projeto. O padrão é `true`.
* `RazorCompileOnPublish`: Quando `true` , compila e emite o Razor assembly como parte da publicação do projeto. O padrão é `true`.

As propriedades e os itens na tabela a seguir são usados para configurar entradas e saídas para o Razor SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> A partir do ASP.NET Core 3,0, as exibições ou Razor páginas do MVC não são servidas por padrão se as `RazorCompileOnBuild` Propriedades do ou do `RazorCompileOnPublish` MSBuild no arquivo de projeto estiverem desabilitadas. Os aplicativos devem adicionar uma referência explícita ao [Microsoft. AspNetCore. Mvc. Razor . Pacote RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) se o aplicativo depender da compilação de tempo de execução para processar arquivos *. cshtml* .

::: moniker-end

| Itens | Descrição |
| ----- | ---
Título: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------ | | `RazorGenerate` | Elementos de item (arquivos *. cshtml* ) que são entradas para geração de código. | | `RazorComponent` | Elementos de item (arquivos *. Razor* ) que são entradas para a Razor geração de código de componente. | | `RazorCompile` | Elementos de item (arquivos *. cs* ) que são entradas para Razor destinos de compilação. Use isso `ItemGroup` para especificar arquivos adicionais a serem compilados no Razor assembly. | | `RazorTargetAssemblyAttribute` | Elementos de item usados para gerar atributos de geração de código para o Razor assembly. Por exemplo:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">`| | `RazorEmbeddedResource` | Elementos de item adicionados como recursos incorporados ao Razor assembly gerado. |

::: moniker range=">= aspnetcore-3.0"

| Propriedade | Descrição |
| ---
Título: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---- | título do---: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------ | | `RazorTargetName` | Nome do arquivo (sem extensão) do assembly produzido por Razor . | | `RazorOutputPath` | O Razor diretório de saída. | | `RazorCompileToolset` | Usado para determinar o conjunto de ferramentas usado para compilar o Razor assembly. Os valores válidos são `Implicit`, `RazorSDK` e `PrecompilationTool`. | | [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | O padrão é `true` . Quando `true` , inclui arquivos *Web. config*, *. JSON*e *. cshtml* como conteúdo no projeto. Quando referenciado por meio `Microsoft.NET.Sdk.Web` de, os arquivos em arquivos *wwwroot* e config também são incluídos. | | `EnableDefaultRazorGenerateItems` | Quando `true` , inclui arquivos *. cshtml* de `Content` itens em `RazorGenerate` itens. | | `GenerateRazorTargetAssemblyInfo` | Quando `true` , o gera um arquivo *. cs* contendo atributos especificados por `RazorAssemblyAttribute` e inclui o arquivo na saída de compilação. | | `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando `true` , o adiciona um conjunto padrão de atributos de assembly a `RazorAssemblyAttribute` . | | `CopyRazorGenerateFilesToPublishDirectory` | Quando `true` , o copia os `RazorGenerate` arquivos de itens (*. cshtml*) para o diretório de publicação. Normalmente, Razor os arquivos não são necessários para um aplicativo publicado se participarem da compilação em tempo de compilação ou em tempo de publicação. O padrão é `false`. | | `PreserveCompilationReferences` | Quando `true` , copie itens de assembly de referência para o diretório de publicação. Normalmente, os assemblies de referência não são necessários para um aplicativo publicado se a Razor compilação ocorrer em tempo de compilação ou em tempo de publicação. Defina como `true` se seu aplicativo publicado exigir compilação em tempo de execução. Por exemplo, defina o valor como `true` se o aplicativo modifica arquivos *. cshtml* em tempo de execução ou usa exibições inseridas. O padrão é `false`. | | `IncludeRazorContentInPack` | Quando `true` , todos os Razor itens de conteúdo (arquivos *. cshtml* ) são marcados para inclusão no pacote NuGet gerado. O padrão é `false`. | | `EmbedRazorGenerateSources` | Quando `true` , o adiciona itens RazorGenerate (*. cshtml*) como arquivos incorporados ao Razor assembly gerado. O padrão é `false`. | | `UseRazorBuildServer` | Quando `true` , o usa um processo de servidor de compilação persistente para descarregar o trabalho de geração de código. Seu valor padrão é `UseSharedCompilation`. | | `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true` , o SDK gera atributos adicionais usados pelo MVC em tempo de execução para executar a descoberta de parte do aplicativo. | | `DefaultWebContentItemExcludes` | Um padrão de mascaramento para elementos de item que devem ser excluídos do `Content` grupo de itens em projetos direcionados à Web ou ao Razor SDK | | `ExcludeConfigFilesFromBuildOutput` | Quando os `true` arquivos *. config* e *. JSON* não são copiados para o diretório de saída da compilação. | | `AddRazorSupportForMvc` | Quando `true` , o configura o Razor SDK para adicionar suporte para a configuração do MVC que é necessária ao criar aplicativos que contenham exibições ou páginas do MVC Razor . Essa propriedade é definida implicitamente para projetos .NET Core 3,0 ou posterior direcionados para o SDK Web | | `RazorLangVersion` | A versão do Razor idioma para o destino. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Propriedade | Descrição |
| ---
Título: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---- | título do---: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

-
Título: ' ASP.NET Core Razor SDK ' autor: Descrição: ' saiba como as Razor páginas no ASP.NET Core tornam os cenários voltados para a página de codificação mais fáceis e produtivos do que usar o MVC. '
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

------ | | `RazorTargetName` | Nome do arquivo (sem extensão) do assembly produzido por Razor . | | `RazorOutputPath` | O Razor diretório de saída. | | `RazorCompileToolset` | Usado para determinar o conjunto de ferramentas usado para compilar o Razor assembly. Os valores válidos são `Implicit`, `RazorSDK` e `PrecompilationTool`. | | [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | O padrão é `true` . Quando `true` , inclui arquivos *Web. config*, *. JSON*e *. cshtml* como conteúdo no projeto. Quando referenciado por meio `Microsoft.NET.Sdk.Web` de, os arquivos em arquivos *wwwroot* e config também são incluídos. | | `EnableDefaultRazorGenerateItems` | Quando `true` , inclui arquivos *. cshtml* de `Content` itens em `RazorGenerate` itens. | | `GenerateRazorTargetAssemblyInfo` | Quando `true` , o gera um arquivo *. cs* contendo atributos especificados por `RazorAssemblyAttribute` e inclui o arquivo na saída de compilação. | | `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando `true` , o adiciona um conjunto padrão de atributos de assembly a `RazorAssemblyAttribute` . | | `CopyRazorGenerateFilesToPublishDirectory` | Quando `true` , o copia os `RazorGenerate` arquivos de itens (*. cshtml*) para o diretório de publicação. Normalmente, Razor os arquivos não são necessários para um aplicativo publicado se participarem da compilação em tempo de compilação ou em tempo de publicação. O padrão é `false`. | | `CopyRefAssembliesToPublishDirectory` | Quando `true` , copie itens de assembly de referência para o diretório de publicação. Normalmente, os assemblies de referência não são necessários para um aplicativo publicado se a Razor compilação ocorrer em tempo de compilação ou em tempo de publicação. Defina como `true` se seu aplicativo publicado exigir compilação em tempo de execução. Por exemplo, defina o valor como `true` se o aplicativo modifica arquivos *. cshtml* em tempo de execução ou usa exibições inseridas. O padrão é `false`. | | `IncludeRazorContentInPack` | Quando `true` , todos os Razor itens de conteúdo (arquivos *. cshtml* ) são marcados para inclusão no pacote NuGet gerado. O padrão é `false`. | | `EmbedRazorGenerateSources` | Quando `true` , o adiciona itens RazorGenerate (*. cshtml*) como arquivos incorporados ao Razor assembly gerado. O padrão é `false`. | | `UseRazorBuildServer` | Quando `true` , o usa um processo de servidor de compilação persistente para descarregar o trabalho de geração de código. Seu valor padrão é `UseSharedCompilation`. | | `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true` , o SDK gera atributos adicionais usados pelo MVC em tempo de execução para executar a descoberta de parte do aplicativo. | | `DefaultWebContentItemExcludes` | Um padrão de mascaramento para elementos de item que devem ser excluídos do `Content` grupo de itens em projetos direcionados à Web ou ao Razor SDK | | `ExcludeConfigFilesFromBuildOutput` | Quando os `true` arquivos *. config* e *. JSON* não são copiados para o diretório de saída da compilação. | | `AddRazorSupportForMvc` | Quando `true` , o configura o Razor SDK para adicionar suporte para a configuração do MVC que é necessária ao criar aplicativos que contenham exibições ou páginas do MVC Razor . Essa propriedade é definida implicitamente para projetos .NET Core 3,0 ou posterior direcionados para o SDK Web | | `RazorLangVersion` | A versão do Razor idioma para o destino. |

::: moniker-end

Para saber mais sobre as propriedades, confira [Propriedades do MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Destinos

O Razor SDK define dois destinos principais:

* `RazorGenerate`: O código gera arquivos *. cs* de `RazorGenerate` elementos de item. Use a `RazorGenerateDependsOn` propriedade para especificar destinos adicionais que podem ser executados antes ou depois desse destino.
* `RazorCompile`: Compila arquivos *. cs* gerados em um Razor assembly. Use o `RazorCompileDependsOn` para especificar destinos adicionais que podem ser executados antes ou depois desse destino.
* `RazorComponentGenerate`: O código gera arquivos *. cs* para `RazorComponent` elementos de item. Use a `RazorComponentGenerateDependsOn` propriedade para especificar destinos adicionais que podem ser executados antes ou depois desse destino.

### <a name="runtime-compilation-of-razor-views"></a>Compilação em tempo de execução de Razor exibições

* Por padrão, o Razor SDK não publica assemblies de referência que são necessários para executar a compilação em tempo de execução. Isso resulta em falhas de compilação quando o modelo de aplicativo se baseia na compilação em runtime&mdash;, por exemplo, o aplicativo usa exibições inseridas ou muda as exibições depois que o aplicativo é publicado. Defina `CopyRefAssembliesToPublishDirectory` como `true` para continuar publicando assemblies de referência.

* Para um aplicativo Web, verifique se seu aplicativo está direcionando o `Microsoft.NET.Sdk.Web` SDK.

## <a name="razor-language-version"></a>Razorversão do idioma

Ao direcionar o `Microsoft.NET.Sdk.Web` SDK, a Razor versão de idioma é inferida da versão da estrutura de destino do aplicativo. Para projetos direcionados ao `Microsoft.NET.Sdk.Razor` SDK ou, no caso raro, que o aplicativo exija uma Razor versão de idioma diferente do valor inferido, uma versão pode ser configurada definindo a `<RazorLangVersion>` propriedade no arquivo de projeto do aplicativo:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razora versão de idioma do é totalmente integrada à versão do tempo de execução para a qual foi criada. O direcionamento de uma versão de idioma que não é projetada para o tempo de execução não é suportado e provavelmente produz erros de compilação.

## <a name="additional-resources"></a>Recursos adicionais

* [Adições ao formato csproj para .NET Core](/dotnet/core/tools/csproj)
* [Itens de projeto comuns do MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
