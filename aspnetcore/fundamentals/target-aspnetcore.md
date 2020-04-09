---
title: Use ASP.NET PRINCIPAIS APIs em uma biblioteca de classes
author: scottaddie
description: Aprenda a usar ASP.NET APIs principais em uma biblioteca de classes.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 5374d7eec4334223a4bba7ee26cb6e2f208ed20b
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977191"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Use ASP.NET PRINCIPAIS APIs em uma biblioteca de classes

Por [Scott Addie](https://github.com/scottaddie)

Este documento fornece orientação para o uso de ASP.NET APIs principais em uma biblioteca de classes. Para obter todas as outras orientações da biblioteca, consulte [orientação de biblioteca de código aberto](/dotnet/standard/library-guidance/).

## <a name="determine-which-aspnet-core-versions-to-support"></a>Determine quais ASP.NET versões core para suportar

ASP.NET Core adere à [política de suporte do Núcleo .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Consulte a política de suporte ao determinar quais ASP.NET versões do Core para suportar em uma biblioteca. Uma biblioteca deve:

* Faça um esforço para suportar todas as ASP.NET versões Core classificadas como *Suporte de Longo Prazo* (LTS).
* Não se sinta obrigado a suportar ASP.NET versões Core classificadas como *End of Life* (EOL).

À medida que as versões de pré-visualização do ASP.NET Core são disponibilizadas, as alterações de quebra são postadas no repositório [Aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub. Os testes de compatibilidade das bibliotecas podem ser realizados à medida que os recursos da estrutura estão sendo desenvolvidos.

## <a name="use-the-aspnet-core-shared-framework"></a>Use a estrutura compartilhada ASP.NET Core

Com o lançamento do .NET Core 3.0, muitos conjuntos ASP.NET Core não são mais publicados no NuGet como pacotes. Em vez disso, os `Microsoft.AspNetCore.App` conjuntos estão incluídos na estrutura compartilhada, que é instalada com o .NET Core SDK e os instaladores de tempo de execução. Para obter uma lista de pacotes que não serão mais publicados, consulte [Remover referências obsoletas de pacotes](xref:migration/22-to-30#remove-obsolete-package-references).

A partir do .NET Core 3.0, projetos que usam o `Microsoft.NET.Sdk.Web` MSBuild SDK fazem referência implícita à estrutura compartilhada. Os projetos `Microsoft.NET.Sdk` `Microsoft.NET.Sdk.Razor` que usam o SDK devem fazer referência ao ASP.NET Core para usar ASP.NET APIs core na estrutura compartilhada.

Para referenciar ASP.NET Core, adicione o seguinte `<FrameworkReference>` elemento ao arquivo do projeto:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

A referência ASP.NET Core dessa forma só é suportada para projetos direcionados ao .NET Core 3.x.

## <a name="include-blazor-extensibility"></a>Inclua a extensibilidade de Blazor

Blazor suporta modelos de [hospedagem](xref:blazor/hosting-models)webassembly (WASM) e server . A menos que haja uma razão específica para não fazê-lo, uma biblioteca [de componentes razor](xref:blazor/components) deve suportar ambos os modelos de hospedagem. Uma biblioteca de componentes Razor deve usar o [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).

### <a name="support-both-hosting-models"></a>Suporte a ambos os modelos de hospedagem

Para suportar o consumo de componentes Razor de projetos [Blazor Server](xref:blazor/hosting-models#blazor-server) e [Blazor WASM,](xref:blazor/hosting-models#blazor-webassembly) use as seguintes instruções para o seu editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Use o modelo de projeto **da Biblioteca de Classe de Barbear.** As páginas de suporte e a caixa de **seleção** de visualizações do modelo devem ser desmarcadas.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o seguinte comando no terminal integrado:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Use o modelo de projeto **da Biblioteca de Classe de Barbear.**

---

O projeto gerado a partir do modelo faz as seguintes coisas:

* Alvos .NET Padrão 2.0.
* Define a propriedade `RazorLangVersion` como `3.0`. `3.0`é o valor padrão do .NET Core 3.x.
* Adiciona as seguintes referências de pacote:
  * [Microsoft.AspNetCore.Componentes](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Suporte a um modelo específico de hospedagem

É muito menos comum apoiar um único modelo de hospedagem Blazor. Como exemplo, para suportar o consumo de componentes Razor apenas de projetos [do Blazor Server:](xref:blazor/hosting-models#blazor-server)

* Alvo .NET Core 3.x.
* Adicione `<FrameworkReference>` um elemento para a estrutura compartilhada.

Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Para obter mais informações sobre bibliotecas que contenham componentes da Lâmina, consulte [ASP.NET bibliotecas de classe de componentes do Core Razor](xref:blazor/class-libraries).

## <a name="include-mvc-extensibility"></a>Incluir extensibilidade MVC

Esta seção descreve recomendações para bibliotecas que incluem:

* [Vistas de navalha ou páginas de navalha](#razor-views-or-razor-pages)
* [Tag Helpers](#tag-helpers)
* [Exibir componentes](#view-components)

Esta seção não discute multi-segmentação para suportar várias versões do MVC. Para obter orientação sobre como suportar várias versões ASP.NET Core, consulte [Suporte a várias versões ASP.NET Core](#support-multiple-aspnet-core-versions).

### <a name="razor-views-or-razor-pages"></a>Vistas de navalha ou páginas de navalha

Um projeto que inclua [visualizações de navalha](xref:mvc/views/overview) ou [páginas de barbear](xref:razor-pages/index) deve usar o [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).

Se o projeto tiver como alvo o .NET Core 3.x, ele requer:

* Uma `AddRazorSupportForMvc` propriedade MSBuild `true`definida como .
* Um `<FrameworkReference>` elemento para a estrutura compartilhada.

O modelo de projeto **razor class library** satisfaz os requisitos precedentes para projetos direcionados ao .NET Core 3.x. Use as seguintes instruções para o seu editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Use o modelo de projeto **da Biblioteca de Classe de Barbear.** As páginas de suporte e a caixa de **seleção** de visualizações do modelo devem ser selecionadas.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o seguinte comando no terminal integrado:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Nenhum suporte ao modelo de projeto no momento.

---

Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Se o projeto tiver como alvo o .NET Standard, uma referência de pacote [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) é necessária. O `Microsoft.AspNetCore.Mvc` pacote mudou-se para a estrutura compartilhada em ASP.NET Núcleo 3.0 e, portanto, não é mais publicado. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Auxiliares de Marca

Um projeto que inclua [tag helpers](xref:mvc/views/tag-helpers/intro) deve usar o `Microsoft.NET.Sdk` SDK. Se tiver como alvo o .NET `<FrameworkReference>` Core 3.x, adicione um elemento para a estrutura compartilhada. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Se tiver como alvo o .NET Standard (para suportar versões anteriores ao ASP.NET Core 3.x), adicione uma referência de pacote ao [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor). O `Microsoft.AspNetCore.Mvc.Razor` pacote mudou-se para o quadro compartilhado e, portanto, não é mais publicado. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Componentes da exibição

Um projeto que inclua [componentes de exibição](xref:mvc/views/view-components) deve usar o `Microsoft.NET.Sdk` SDK. Se tiver como alvo o .NET `<FrameworkReference>` Core 3.x, adicione um elemento para a estrutura compartilhada. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Se tiver como alvo o .NET Standard (para suportar versões anteriores ao ASP.NET Core 3.x), adicione uma referência de pacote ao [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). O `Microsoft.AspNetCore.Mvc.ViewFeatures` pacote mudou-se para o quadro compartilhado e, portanto, não é mais publicado. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Suporte várias ASP.NET versões core

O multi-targeting é necessário para criar uma biblioteca que suporte várias variantes do ASP.NET Core. Considere um cenário no qual uma biblioteca de Ajudantes de Marcação deve suportar as seguintes variantes ASP.NET Core:

* ASP.NET Núcleo 2.1 visando .NET Framework 4.6.1
* ASP.NET Core 2.x mirando .NET Core 2.x
* ASP.NET Core 3.x mirando .NET Core 3.x

O arquivo de projeto a seguir `TargetFrameworks` suporta essas variantes através da propriedade:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

Com o arquivo de projeto anterior:

* O `Markdig` pacote é adicionado para todos os consumidores.
* Uma referência ao [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) é adicionada para consumidores que visam o .NET Framework 4.6.1 ou posterior ou .NET Core 2.x. A versão 2.1.0 do pacote funciona com ASP.NET Core 2.2 devido à retrocompatibilidade.
* A estrutura compartilhada é referenciada para consumidores que visam o .NET Core 3.x. O `Microsoft.AspNetCore.Mvc.Razor` pacote está incluído no quadro compartilhado.

Alternativamente, o .NET Standard 2.0 poderia ser direcionado em vez de direcionar tanto o .NET Core 2.1 quanto o .NET Framework 4.6.1:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

Com o arquivo de projeto anterior, existem as seguintes ressalvas:

* Uma vez que a biblioteca contém apenas Ajudadores de marcação, é mais simples direcionar as plataformas específicas nas quais ASP.NET Core é executado: .NET Core e .NET Framework. Os ''Ajudadores de marcação' não podem ser usados por outros frameworks de destino compatíveis com o .NET Standard 2.0, como Unity, UWP e Xamarin.
* Usar o .NET Standard 2.0 do .NET Framework tem alguns problemas que foram resolvidos no .NET Framework 4.7.2. Você pode melhorar a experiência para os consumidores usando o .NET Framework 4.6.1 a 4.7.1 mirando o .NET Framework 4.6.1.

Se sua biblioteca precisar chamar APIs específicas da plataforma, direcione implementações .NET específicas em vez de .NET Standard. Para obter mais informações, consulte [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).

## <a name="use-an-api-that-hasnt-changed"></a>Use uma API que não mudou

Imagine um cenário em que você está atualizando uma biblioteca de middleware do .NET Core 2.2 para o 3.0. As APIs de middleware ASP.NET Que estão sendo usadas na biblioteca não mudaram entre ASP.NET Core 2.2 e 3.0. Para continuar a suportar a biblioteca de middleware no .NET Core 3.0, tome as seguintes etapas:

* Siga a [orientação padrão](/dotnet/standard/library-guidance/)da biblioteca.
* Adicione uma referência de pacote para o pacote NuGet de cada API se o conjunto correspondente não existir na estrutura compartilhada.

## <a name="use-an-api-that-changed"></a>Use uma API que mudou

Imagine um cenário em que você está atualizando uma biblioteca de .NET Core 2.2 para .NET Core 3.0. Uma API ASP.NET Core que está sendo usada na biblioteca tem uma [mudança de ruptura](/dotnet/core/compatibility/breaking-changes) no ASP.NET Core 3.0. Considere se a biblioteca pode ser reescrita para não usar a API quebrada em todas as versões.

Se você puder reescrever a biblioteca, faça-o e continue a direcionar uma estrutura de destino anterior (por exemplo, .NET Standard 2.0 ou .NET Framework 4.6.1) com referências de pacotes.

Se você não puder reescrever a biblioteca, tome as seguintes etapas:

* Adicione um alvo para .NET Core 3.0.
* Adicione `<FrameworkReference>` um elemento para a estrutura compartilhada.
* Use a [diretiva de pré-processador #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) com o símbolo de estrutura de destino apropriado para compilar condicionalmente o código.

Por exemplo, leituras síncronas e gravações em fluxos de solicitação e resposta HTTP são desativados por padrão a partir de ASP.NET Core 3.0. ASP.NET Core 2.2 suporta o comportamento síncrono por padrão. Considere uma biblioteca de middleware na qual leituras e gravações síncronas devem ser ativadas onde a I/O está ocorrendo. A biblioteca deve envolver o código para habilitar recursos síncronos na diretiva de pré-processador apropriada. Por exemplo:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Use uma API introduzida em 3.0

Imagine que você deseja usar uma API ASP.NET Core que foi introduzida no ASP.NET Core 3.0. Considere as perguntas a seguir:

1. A biblioteca requer funcionalmente a nova API?
1. A biblioteca pode implementar esse recurso de uma forma diferente?

Se a biblioteca requer funcionalmente a API e não há como implementá-la no nível de baixa:

* Somente alvo .NET Core 3.x.
* Adicione `<FrameworkReference>` um elemento para a estrutura compartilhada.

Se a biblioteca puder implementar o recurso de uma maneira diferente:

* Adicione o .NET Core 3.x como uma estrutura de destino.
* Adicione `<FrameworkReference>` um elemento para a estrutura compartilhada.
* Use a [diretiva de pré-processador #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) com o símbolo de estrutura de destino apropriado para compilar condicionalmente o código.

Por exemplo, o Tag Helper a seguir usa a <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduzida no ASP.NET Core 3.0. Os consumidores que visam o .NET Core 3.0 executam o caminho de código definido pelo símbolo da `NETCOREAPP3_0` estrutura de destino. O tipo de parâmetro de construtor do <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> Tag Helper muda para os consumidores .NET Core 2.1 e .NET Framework 4.6.1. Essa mudança foi necessária porque ASP.NET `IHostingEnvironment` Núcleo 3.0 marcado como obsoleto e recomendado `IWebHostEnvironment` como a substituição.

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

O seguinte arquivo de projeto multi-segmentado suporta este cenário do Tag Helper:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Use uma API removida da estrutura compartilhada

Para usar um conjunto ASP.NET Core que foi removido da estrutura compartilhada, adicione a referência de pacote apropriada. Para obter uma lista de pacotes removidos da estrutura compartilhada em ASP.NET Núcleo 3.0, consulte [Remover referências obsoletas de pacotes](xref:migration/22-to-30#remove-obsolete-package-references).

Por exemplo, para adicionar o cliente de API web:

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [Suporte à implementação do .NET](/dotnet/standard/net-standard#net-implementation-support)
* [Políticas de suporte .NET](https://dotnet.microsoft.com/platform/support/policy)
