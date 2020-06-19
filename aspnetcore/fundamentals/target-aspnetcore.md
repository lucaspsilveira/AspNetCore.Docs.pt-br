---
title: Usar ASP.NET Core APIs em uma biblioteca de classes
author: scottaddie
description: Saiba como usar ASP.NET Core APIs em uma biblioteca de classes.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 70a445d109a1a9553178e94d79df87cd373e6b06
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103026"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Usar ASP.NET Core APIs em uma biblioteca de classes

Por [Scott Addie](https://github.com/scottaddie)

Este documento fornece diretrizes para usar ASP.NET Core APIs em uma biblioteca de classes. Para todas as outras diretrizes de biblioteca, consulte [diretrizes de biblioteca](/dotnet/standard/library-guidance/)de software livre.

## <a name="determine-which-aspnet-core-versions-to-support"></a>Determinar quais versões de ASP.NET Core para dar suporte

ASP.NET Core adere à política de [suporte do .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Consulte a política de suporte ao determinar a quais ASP.NET Core versões dar suporte em uma biblioteca. Uma biblioteca deve:

* Tome um esforço para dar suporte a todas as versões ASP.NET Core classificadas como *suporte a longo prazo* (LTS).
* Não se sinta obrigado a dar suporte a ASP.NET Core versões classificadas como *fim da vida útil* (EOL).

À medida que as versões de visualização de ASP.NET Core são disponibilizadas, as alterações significativas são postadas no repositório GitHub [ASPNET/comunicados](https://github.com/aspnet/Announcements/issues) . O teste de compatibilidade das bibliotecas pode ser conduzido conforme os recursos da estrutura estão sendo desenvolvidos.

## <a name="use-the-aspnet-core-shared-framework"></a>Usar a estrutura compartilhada ASP.NET Core

Com o lançamento do .NET Core 3,0, muitos ASP.NET Core assemblies não são mais publicados no NuGet como pacotes. Em vez disso, os assemblies são incluídos na `Microsoft.AspNetCore.App` estrutura compartilhada, que é instalada com os instaladores de SDK do .NET Core e tempo de execução. Para obter uma lista de pacotes que não estão mais sendo publicados, consulte [remover referências de pacotes obsoletos](xref:migration/22-to-30#remove-obsolete-package-references).

A partir do .NET Core 3,0, os projetos que usam o `Microsoft.NET.Sdk.Web` SDK do MSBuild referenciam implicitamente a estrutura compartilhada. Os projetos que usam o `Microsoft.NET.Sdk` SDK do ou do `Microsoft.NET.Sdk.Razor` devem referenciar ASP.NET Core usar ASP.NET Core APIs na estrutura compartilhada.

Para fazer referência a ASP.NET Core, adicione o seguinte `<FrameworkReference>` elemento ao seu arquivo de projeto:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

A referência a ASP.NET Core dessa maneira só tem suporte em projetos destinados ao .NET Core 3. x.

## <a name="include-blazor-extensibility"></a>Incluir Blazor extensibilidade

Blazordá suporte ao Webassembly (WASM) e [modelos de hospedagem](xref:blazor/hosting-models)de servidor. A menos que haja um motivo específico para não, uma biblioteca de [ Razor componentes](xref:blazor/components/index) deve dar suporte a ambos os modelos de hospedagem. Uma Razor biblioteca de componentes deve usar o [Microsoft. net. Sdk. Razor SDK](xref:razor-pages/sdk).

### <a name="support-both-hosting-models"></a>Suporte para ambos os modelos de hospedagem

Para dar suporte ao Razor consumo de componentes de projetos de [ Blazor servidor](xref:blazor/hosting-models#blazor-server) e [ Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) , use as instruções a seguir para o seu editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Use o modelo de projeto de ** Razor biblioteca de classes** . A caixa de seleção páginas de suporte do modelo **e exibições** deve ser desmarcada.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o seguinte comando no terminal integrado:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Use o modelo de projeto de ** Razor biblioteca de classes** .

---

O projeto gerado a partir do modelo faz o seguinte:

* Destinos .NET Standard 2,0.
* Define a propriedade `RazorLangVersion` como `3.0`. `3.0`é o valor padrão para o .NET Core 3. x.
* Adiciona as seguintes referências de pacote:
  * [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft. AspNetCore. Components. Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Suporte a um modelo de hospedagem específico

É muito menos comum oferecer suporte a um único Blazor modelo de hospedagem. Por exemplo, para dar suporte ao Razor consumo de componentes somente de projetos de [ Blazor servidor](xref:blazor/hosting-models#blazor-server) :

* Direcione o .NET Core 3. x.
* Adicione um `<FrameworkReference>` elemento para a estrutura compartilhada.

Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Para obter mais informações sobre bibliotecas que contêm Razor componentes, consulte [bibliotecas de classes de Razor componentes de ASP.NET Core](xref:blazor/components/class-libraries).

## <a name="include-mvc-extensibility"></a>Incluir extensibilidade MVC

Esta seção descreve as recomendações para bibliotecas que incluem:

* [Razorexibições ou Razor páginas](#razor-views-or-razor-pages)
* [Auxiliares de Marca](#tag-helpers)
* [Componentes da exibição](#view-components)

Esta seção não aborda vários destinos para dar suporte a várias versões do MVC. Para obter orientação sobre como dar suporte a várias versões de ASP.NET Core, consulte [suporte para várias versões de ASP.NET Core](#support-multiple-aspnet-core-versions).

### <a name="razor-views-or-razor-pages"></a>Razorexibições ou Razor páginas

Um projeto que inclui [ Razor exibições](xref:mvc/views/overview) ou [ Razor páginas](xref:razor-pages/index) deve usar o [Microsoft. net. Sdk. Razor SDK](xref:razor-pages/sdk).

Se o projeto for destinado ao .NET Core 3. x, ele exigirá:

* Uma `AddRazorSupportForMvc` Propriedade do MSBuild definida como `true` .
* Um `<FrameworkReference>` elemento para a estrutura compartilhada.

O modelo de projeto de ** Razor biblioteca de classes** atende aos requisitos anteriores para projetos que visam o .NET Core 3. x. Use as instruções a seguir para o seu editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Use o modelo de projeto de ** Razor biblioteca de classes** . A caixa de seleção páginas de suporte do modelo **e exibições** deve ser selecionada.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o seguinte comando no terminal integrado:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Não há suporte ao modelo de projeto no momento.

---

Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Se o projeto for direcionado .NET Standard em vez disso, uma referência de pacote [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) será necessária. O `Microsoft.AspNetCore.Mvc` pacote foi movido para a estrutura compartilhada no ASP.NET Core 3,0 e, portanto, não é mais publicado. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Auxiliares de Marca

Um projeto que inclui [auxiliares de marca](xref:mvc/views/tag-helpers/intro) deve usar o `Microsoft.NET.Sdk` SDK. Se estiver direcionando o .NET Core 3. x, adicione um `<FrameworkReference>` elemento para a estrutura compartilhada. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Se estiver direcionando .NET Standard (para dar suporte a versões anteriores a ASP.NET Core 3. x), adicione uma referência de pacote a [Microsoft Razor . AspNetCore. Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) O `Microsoft.AspNetCore.Mvc.Razor` pacote foi movido para a estrutura compartilhada e, portanto, não é mais publicado. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Componentes da exibição

Um projeto que inclui [componentes de exibição](xref:mvc/views/view-components) deve usar o `Microsoft.NET.Sdk` SDK. Se estiver direcionando o .NET Core 3. x, adicione um `<FrameworkReference>` elemento para a estrutura compartilhada. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Se estiver direcionando .NET Standard (para dar suporte a versões anteriores a ASP.NET Core 3. x), adicione uma referência de pacote a [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). O `Microsoft.AspNetCore.Mvc.ViewFeatures` pacote foi movido para a estrutura compartilhada e, portanto, não é mais publicado. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Suporte a várias versões de ASP.NET Core

O direcionamento múltiplo é necessário para criar uma biblioteca que dê suporte a várias variantes de ASP.NET Core. Considere um cenário no qual uma biblioteca de auxiliares de marcas deve dar suporte às seguintes ASP.NET Core variantes:

* ASP.NET Core 2,1 direcionando .NET Framework 4.6.1
* ASP.NET Core 2. x com direcionamento para .NET Core 2. x
* ASP.NET Core 3. x com direcionamento para o .NET Core 3. x

O arquivo de projeto a seguir dá suporte a essas variantes por meio da `TargetFrameworks` Propriedade:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

Com o arquivo de projeto anterior:

* O `Markdig` pacote é adicionado para todos os consumidores.
* Uma referência a [Microsoft. AspNetCore. Mvc. Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) é adicionado para consumidores que visam .NET Framework 4.6.1 ou posterior ou o .NET Core 2. x. A versão 2.1.0 do pacote funciona com o ASP.NET Core 2,2 devido à compatibilidade com versões anteriores.
* A estrutura compartilhada é referenciada para os consumidores que visam o .NET Core 3. x. O `Microsoft.AspNetCore.Mvc.Razor` pacote está incluído na estrutura compartilhada.

Como alternativa, .NET Standard 2,0 poderia ser direcionado em vez de direcionar o .NET Core 2,1 e .NET Framework 4.6.1:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

Com o arquivo de projeto anterior, existem as seguintes limitações:

* Como a biblioteca contém apenas auxiliares de marca, é mais simples direcionar para as plataformas específicas em que ASP.NET Core é executado: .NET Core e .NET Framework. Os auxiliares de marca não podem ser usados por outras estruturas de destino compatíveis com .NET Standard 2,0, como Unity, UWP e Xamarin.
* Usar o .NET Standard 2.0 do .NET Framework tem alguns problemas que foram resolvidos no .NET Framework 4.7.2. Você pode melhorar a experiência dos consumidores usando o .NET Framework 4.6.1 até o 4.7.1 direcionando .NET Framework 4.6.1.

Se sua biblioteca precisar chamar APIs específicas da plataforma, direcione as implementações específicas do .NET em vez de .NET Standard. Para obter mais informações, consulte [multi-Targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).

## <a name="use-an-api-that-hasnt-changed"></a>Usar uma API que não foi alterada

Imagine um cenário no qual você está atualizando uma biblioteca de middleware do .NET Core 2,2 para 3,0. As APIs de middleware ASP.NET Core que estão sendo usadas na biblioteca não foram alteradas entre ASP.NET Core 2,2 e 3,0. Para continuar a oferecer suporte à biblioteca de middleware no .NET Core 3,0, execute as seguintes etapas:

* Siga as [diretrizes da biblioteca padrão](/dotnet/standard/library-guidance/).
* Adicione uma referência de pacote para cada pacote NuGet de API se o assembly correspondente não existir na estrutura compartilhada.

## <a name="use-an-api-that-changed"></a>Usar uma API que foi alterada

Imagine um cenário no qual você está atualizando uma biblioteca do .NET Core 2,2 para o .NET Core 3,0. Uma API de ASP.NET Core que está sendo usada na biblioteca tem uma [alteração significativa](/dotnet/core/compatibility/breaking-changes) no ASP.NET Core 3,0. Considere se a biblioteca pode ser reescrita para não usar a API quebrada em todas as versões.

Se você puder reescrever a biblioteca, faça-a e continue a direcionar uma estrutura de destino anterior (por exemplo, .NET Standard 2,0 ou .NET Framework 4.6.1) com referências de pacote.

Se você não puder reescrever a biblioteca, execute as seguintes etapas:

* Adicione um destino para o .NET Core 3,0.
* Adicione um `<FrameworkReference>` elemento para a estrutura compartilhada.
* Use a [diretiva de pré-processador #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) com o símbolo de estrutura de destino apropriado para compilar o código condicionalmente.

Por exemplo, leituras e gravações síncronas em fluxos de solicitação e resposta HTTP são desabilitadas por padrão a partir de ASP.NET Core 3,0. O ASP.NET Core 2,2 dá suporte ao comportamento síncrono por padrão. Considere uma biblioteca de middleware na qual leituras e gravações síncronas devem ser habilitadas onde e/s está ocorrendo. A biblioteca deve incluir o código para habilitar recursos síncronos na diretiva de pré-processador apropriada. Por exemplo:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Usar uma API introduzida em 3,0

Imagine que você deseja usar uma API de ASP.NET Core que foi introduzida no ASP.NET Core 3,0. Considere as perguntas a seguir:

1. A biblioteca requer a nova API?
1. A biblioteca pode implementar esse recurso de forma diferente?

Se a biblioteca exigir a API e não houver como implementá-la em nível inferior:

* Somente o .NET Core 3. x de destino.
* Adicione um `<FrameworkReference>` elemento para a estrutura compartilhada.

Se a biblioteca puder implementar o recurso de uma maneira diferente:

* Adicione o .NET Core 3. x como uma estrutura de destino.
* Adicione um `<FrameworkReference>` elemento para a estrutura compartilhada.
* Use a [diretiva de pré-processador #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) com o símbolo de estrutura de destino apropriado para compilar o código condicionalmente.

Por exemplo, o auxiliar de marca a seguir usa a <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduzida no ASP.NET Core 3,0. Os consumidores que visam o .NET Core 3,0 executam o caminho do código definido pelo `NETCOREAPP3_0` símbolo da estrutura de destino. O tipo de parâmetro do construtor do auxiliar de marca é alterado para <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> para .NET Core 2,1 e .NET Framework consumidores do 4.6.1. Essa alteração foi necessária porque ASP.NET Core 3,0 marcado `IHostingEnvironment` como obsoleto e recomendado `IWebHostEnvironment` como substituição.

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

O seguinte arquivo de projeto multiplataforma dá suporte a este cenário auxiliar de marca:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Usar uma API removida da estrutura compartilhada

Para usar um assembly ASP.NET Core que foi removido da estrutura compartilhada, adicione a referência de pacote apropriada. Para obter uma lista de pacotes removidos da estrutura compartilhada no ASP.NET Core 3,0, consulte [remover referências de pacote obsoletos](xref:migration/22-to-30#remove-obsolete-package-references).

Por exemplo, para adicionar o cliente de API Web:

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
* <xref:blazor/components/class-libraries>
* [Suporte à implementação do .NET](/dotnet/standard/net-standard#net-implementation-support)
* [Políticas de suporte do .NET](https://dotnet.microsoft.com/platform/support/policy)
