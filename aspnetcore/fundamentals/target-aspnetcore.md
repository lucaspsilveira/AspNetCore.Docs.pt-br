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
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="20af5-103">Use ASP.NET PRINCIPAIS APIs em uma biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="20af5-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="20af5-104">Por [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="20af5-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="20af5-105">Este documento fornece orientação para o uso de ASP.NET APIs principais em uma biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="20af5-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="20af5-106">Para obter todas as outras orientações da biblioteca, consulte [orientação de biblioteca de código aberto](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="20af5-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="20af5-107">Determine quais ASP.NET versões core para suportar</span><span class="sxs-lookup"><span data-stu-id="20af5-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="20af5-108">ASP.NET Core adere à [política de suporte do Núcleo .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="20af5-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="20af5-109">Consulte a política de suporte ao determinar quais ASP.NET versões do Core para suportar em uma biblioteca.</span><span class="sxs-lookup"><span data-stu-id="20af5-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="20af5-110">Uma biblioteca deve:</span><span class="sxs-lookup"><span data-stu-id="20af5-110">A library should:</span></span>

* <span data-ttu-id="20af5-111">Faça um esforço para suportar todas as ASP.NET versões Core classificadas como *Suporte de Longo Prazo* (LTS).</span><span class="sxs-lookup"><span data-stu-id="20af5-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="20af5-112">Não se sinta obrigado a suportar ASP.NET versões Core classificadas como *End of Life* (EOL).</span><span class="sxs-lookup"><span data-stu-id="20af5-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="20af5-113">À medida que as versões de pré-visualização do ASP.NET Core são disponibilizadas, as alterações de quebra são postadas no repositório [Aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub.</span><span class="sxs-lookup"><span data-stu-id="20af5-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="20af5-114">Os testes de compatibilidade das bibliotecas podem ser realizados à medida que os recursos da estrutura estão sendo desenvolvidos.</span><span class="sxs-lookup"><span data-stu-id="20af5-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="20af5-115">Use a estrutura compartilhada ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20af5-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="20af5-116">Com o lançamento do .NET Core 3.0, muitos conjuntos ASP.NET Core não são mais publicados no NuGet como pacotes.</span><span class="sxs-lookup"><span data-stu-id="20af5-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="20af5-117">Em vez disso, os `Microsoft.AspNetCore.App` conjuntos estão incluídos na estrutura compartilhada, que é instalada com o .NET Core SDK e os instaladores de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="20af5-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="20af5-118">Para obter uma lista de pacotes que não serão mais publicados, consulte [Remover referências obsoletas de pacotes](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="20af5-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="20af5-119">A partir do .NET Core 3.0, projetos que usam o `Microsoft.NET.Sdk.Web` MSBuild SDK fazem referência implícita à estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="20af5-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="20af5-120">Os projetos `Microsoft.NET.Sdk` `Microsoft.NET.Sdk.Razor` que usam o SDK devem fazer referência ao ASP.NET Core para usar ASP.NET APIs core na estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="20af5-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="20af5-121">Para referenciar ASP.NET Core, adicione o seguinte `<FrameworkReference>` elemento ao arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="20af5-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="20af5-122">A referência ASP.NET Core dessa forma só é suportada para projetos direcionados ao .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="20af5-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-blazor-extensibility"></a><span data-ttu-id="20af5-123">Inclua a extensibilidade de Blazor</span><span class="sxs-lookup"><span data-stu-id="20af5-123">Include Blazor extensibility</span></span>

<span data-ttu-id="20af5-124">Blazor suporta modelos de [hospedagem](xref:blazor/hosting-models)webassembly (WASM) e server .</span><span class="sxs-lookup"><span data-stu-id="20af5-124">Blazor supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="20af5-125">A menos que haja uma razão específica para não fazê-lo, uma biblioteca [de componentes razor](xref:blazor/components) deve suportar ambos os modelos de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="20af5-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components) library should support both hosting models.</span></span> <span data-ttu-id="20af5-126">Uma biblioteca de componentes Razor deve usar o [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="20af5-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="20af5-127">Suporte a ambos os modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="20af5-127">Support both hosting models</span></span>

<span data-ttu-id="20af5-128">Para suportar o consumo de componentes Razor de projetos [Blazor Server](xref:blazor/hosting-models#blazor-server) e [Blazor WASM,](xref:blazor/hosting-models#blazor-webassembly) use as seguintes instruções para o seu editor.</span><span class="sxs-lookup"><span data-stu-id="20af5-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20af5-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20af5-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20af5-130">Use o modelo de projeto **da Biblioteca de Classe de Barbear.**</span><span class="sxs-lookup"><span data-stu-id="20af5-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="20af5-131">As páginas de suporte e a caixa de **seleção** de visualizações do modelo devem ser desmarcadas.</span><span class="sxs-lookup"><span data-stu-id="20af5-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="20af5-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="20af5-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="20af5-133">Execute o seguinte comando no terminal integrado:</span><span class="sxs-lookup"><span data-stu-id="20af5-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="20af5-134">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="20af5-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="20af5-135">Use o modelo de projeto **da Biblioteca de Classe de Barbear.**</span><span class="sxs-lookup"><span data-stu-id="20af5-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="20af5-136">O projeto gerado a partir do modelo faz as seguintes coisas:</span><span class="sxs-lookup"><span data-stu-id="20af5-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="20af5-137">Alvos .NET Padrão 2.0.</span><span class="sxs-lookup"><span data-stu-id="20af5-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="20af5-138">Define a propriedade `RazorLangVersion` como `3.0`.</span><span class="sxs-lookup"><span data-stu-id="20af5-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="20af5-139">`3.0`é o valor padrão do .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="20af5-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="20af5-140">Adiciona as seguintes referências de pacote:</span><span class="sxs-lookup"><span data-stu-id="20af5-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="20af5-141">Microsoft.AspNetCore.Componentes</span><span class="sxs-lookup"><span data-stu-id="20af5-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="20af5-142">Microsoft.AspNetCore.Components.Web</span><span class="sxs-lookup"><span data-stu-id="20af5-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="20af5-143">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="20af5-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="20af5-144">Suporte a um modelo específico de hospedagem</span><span class="sxs-lookup"><span data-stu-id="20af5-144">Support a specific hosting model</span></span>

<span data-ttu-id="20af5-145">É muito menos comum apoiar um único modelo de hospedagem Blazor.</span><span class="sxs-lookup"><span data-stu-id="20af5-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="20af5-146">Como exemplo, para suportar o consumo de componentes Razor apenas de projetos [do Blazor Server:](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="20af5-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="20af5-147">Alvo .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="20af5-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="20af5-148">Adicione `<FrameworkReference>` um elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="20af5-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="20af5-149">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="20af5-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="20af5-150">Para obter mais informações sobre bibliotecas que contenham componentes da Lâmina, consulte [ASP.NET bibliotecas de classe de componentes do Core Razor](xref:blazor/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="20af5-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="20af5-151">Incluir extensibilidade MVC</span><span class="sxs-lookup"><span data-stu-id="20af5-151">Include MVC extensibility</span></span>

<span data-ttu-id="20af5-152">Esta seção descreve recomendações para bibliotecas que incluem:</span><span class="sxs-lookup"><span data-stu-id="20af5-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="20af5-153">Vistas de navalha ou páginas de navalha</span><span class="sxs-lookup"><span data-stu-id="20af5-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="20af5-154">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="20af5-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="20af5-155">Exibir componentes</span><span class="sxs-lookup"><span data-stu-id="20af5-155">View components</span></span>](#view-components)

<span data-ttu-id="20af5-156">Esta seção não discute multi-segmentação para suportar várias versões do MVC.</span><span class="sxs-lookup"><span data-stu-id="20af5-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="20af5-157">Para obter orientação sobre como suportar várias versões ASP.NET Core, consulte [Suporte a várias versões ASP.NET Core](#support-multiple-aspnet-core-versions).</span><span class="sxs-lookup"><span data-stu-id="20af5-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="20af5-158">Vistas de navalha ou páginas de navalha</span><span class="sxs-lookup"><span data-stu-id="20af5-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="20af5-159">Um projeto que inclua [visualizações de navalha](xref:mvc/views/overview) ou [páginas de barbear](xref:razor-pages/index) deve usar o [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="20af5-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="20af5-160">Se o projeto tiver como alvo o .NET Core 3.x, ele requer:</span><span class="sxs-lookup"><span data-stu-id="20af5-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="20af5-161">Uma `AddRazorSupportForMvc` propriedade MSBuild `true`definida como .</span><span class="sxs-lookup"><span data-stu-id="20af5-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="20af5-162">Um `<FrameworkReference>` elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="20af5-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="20af5-163">O modelo de projeto **razor class library** satisfaz os requisitos precedentes para projetos direcionados ao .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="20af5-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="20af5-164">Use as seguintes instruções para o seu editor.</span><span class="sxs-lookup"><span data-stu-id="20af5-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20af5-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20af5-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20af5-166">Use o modelo de projeto **da Biblioteca de Classe de Barbear.**</span><span class="sxs-lookup"><span data-stu-id="20af5-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="20af5-167">As páginas de suporte e a caixa de **seleção** de visualizações do modelo devem ser selecionadas.</span><span class="sxs-lookup"><span data-stu-id="20af5-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="20af5-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="20af5-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="20af5-169">Execute o seguinte comando no terminal integrado:</span><span class="sxs-lookup"><span data-stu-id="20af5-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="20af5-170">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="20af5-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="20af5-171">Nenhum suporte ao modelo de projeto no momento.</span><span class="sxs-lookup"><span data-stu-id="20af5-171">No project template support at this time.</span></span>

---

<span data-ttu-id="20af5-172">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="20af5-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="20af5-173">Se o projeto tiver como alvo o .NET Standard, uma referência de pacote [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) é necessária.</span><span class="sxs-lookup"><span data-stu-id="20af5-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="20af5-174">O `Microsoft.AspNetCore.Mvc` pacote mudou-se para a estrutura compartilhada em ASP.NET Núcleo 3.0 e, portanto, não é mais publicado.</span><span class="sxs-lookup"><span data-stu-id="20af5-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="20af5-175">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="20af5-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="20af5-176">Auxiliares de Marca</span><span class="sxs-lookup"><span data-stu-id="20af5-176">Tag Helpers</span></span>

<span data-ttu-id="20af5-177">Um projeto que inclua [tag helpers](xref:mvc/views/tag-helpers/intro) deve usar o `Microsoft.NET.Sdk` SDK.</span><span class="sxs-lookup"><span data-stu-id="20af5-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="20af5-178">Se tiver como alvo o .NET `<FrameworkReference>` Core 3.x, adicione um elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="20af5-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="20af5-179">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="20af5-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="20af5-180">Se tiver como alvo o .NET Standard (para suportar versões anteriores ao ASP.NET Core 3.x), adicione uma referência de pacote ao [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span><span class="sxs-lookup"><span data-stu-id="20af5-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="20af5-181">O `Microsoft.AspNetCore.Mvc.Razor` pacote mudou-se para o quadro compartilhado e, portanto, não é mais publicado.</span><span class="sxs-lookup"><span data-stu-id="20af5-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="20af5-182">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="20af5-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="20af5-183">Componentes da exibição</span><span class="sxs-lookup"><span data-stu-id="20af5-183">View components</span></span>

<span data-ttu-id="20af5-184">Um projeto que inclua [componentes de exibição](xref:mvc/views/view-components) deve usar o `Microsoft.NET.Sdk` SDK.</span><span class="sxs-lookup"><span data-stu-id="20af5-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="20af5-185">Se tiver como alvo o .NET `<FrameworkReference>` Core 3.x, adicione um elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="20af5-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="20af5-186">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="20af5-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="20af5-187">Se tiver como alvo o .NET Standard (para suportar versões anteriores ao ASP.NET Core 3.x), adicione uma referência de pacote ao [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="20af5-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="20af5-188">O `Microsoft.AspNetCore.Mvc.ViewFeatures` pacote mudou-se para o quadro compartilhado e, portanto, não é mais publicado.</span><span class="sxs-lookup"><span data-stu-id="20af5-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="20af5-189">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="20af5-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="20af5-190">Suporte várias ASP.NET versões core</span><span class="sxs-lookup"><span data-stu-id="20af5-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="20af5-191">O multi-targeting é necessário para criar uma biblioteca que suporte várias variantes do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20af5-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="20af5-192">Considere um cenário no qual uma biblioteca de Ajudantes de Marcação deve suportar as seguintes variantes ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="20af5-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="20af5-193">ASP.NET Núcleo 2.1 visando .NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="20af5-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="20af5-194">ASP.NET Core 2.x mirando .NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="20af5-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="20af5-195">ASP.NET Core 3.x mirando .NET Core 3.x</span><span class="sxs-lookup"><span data-stu-id="20af5-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="20af5-196">O arquivo de projeto a seguir `TargetFrameworks` suporta essas variantes através da propriedade:</span><span class="sxs-lookup"><span data-stu-id="20af5-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="20af5-197">Com o arquivo de projeto anterior:</span><span class="sxs-lookup"><span data-stu-id="20af5-197">With the preceding project file:</span></span>

* <span data-ttu-id="20af5-198">O `Markdig` pacote é adicionado para todos os consumidores.</span><span class="sxs-lookup"><span data-stu-id="20af5-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="20af5-199">Uma referência ao [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) é adicionada para consumidores que visam o .NET Framework 4.6.1 ou posterior ou .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="20af5-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="20af5-200">A versão 2.1.0 do pacote funciona com ASP.NET Core 2.2 devido à retrocompatibilidade.</span><span class="sxs-lookup"><span data-stu-id="20af5-200">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="20af5-201">A estrutura compartilhada é referenciada para consumidores que visam o .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="20af5-201">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="20af5-202">O `Microsoft.AspNetCore.Mvc.Razor` pacote está incluído no quadro compartilhado.</span><span class="sxs-lookup"><span data-stu-id="20af5-202">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="20af5-203">Alternativamente, o .NET Standard 2.0 poderia ser direcionado em vez de direcionar tanto o .NET Core 2.1 quanto o .NET Framework 4.6.1:</span><span class="sxs-lookup"><span data-stu-id="20af5-203">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="20af5-204">Com o arquivo de projeto anterior, existem as seguintes ressalvas:</span><span class="sxs-lookup"><span data-stu-id="20af5-204">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="20af5-205">Uma vez que a biblioteca contém apenas Ajudadores de marcação, é mais simples direcionar as plataformas específicas nas quais ASP.NET Core é executado: .NET Core e .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="20af5-205">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="20af5-206">Os ''Ajudadores de marcação' não podem ser usados por outros frameworks de destino compatíveis com o .NET Standard 2.0, como Unity, UWP e Xamarin.</span><span class="sxs-lookup"><span data-stu-id="20af5-206">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="20af5-207">Usar o .NET Standard 2.0 do .NET Framework tem alguns problemas que foram resolvidos no .NET Framework 4.7.2.</span><span class="sxs-lookup"><span data-stu-id="20af5-207">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="20af5-208">Você pode melhorar a experiência para os consumidores usando o .NET Framework 4.6.1 a 4.7.1 mirando o .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="20af5-208">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="20af5-209">Se sua biblioteca precisar chamar APIs específicas da plataforma, direcione implementações .NET específicas em vez de .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="20af5-209">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="20af5-210">Para obter mais informações, consulte [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span><span class="sxs-lookup"><span data-stu-id="20af5-210">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="20af5-211">Use uma API que não mudou</span><span class="sxs-lookup"><span data-stu-id="20af5-211">Use an API that hasn't changed</span></span>

<span data-ttu-id="20af5-212">Imagine um cenário em que você está atualizando uma biblioteca de middleware do .NET Core 2.2 para o 3.0.</span><span class="sxs-lookup"><span data-stu-id="20af5-212">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="20af5-213">As APIs de middleware ASP.NET Que estão sendo usadas na biblioteca não mudaram entre ASP.NET Core 2.2 e 3.0.</span><span class="sxs-lookup"><span data-stu-id="20af5-213">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="20af5-214">Para continuar a suportar a biblioteca de middleware no .NET Core 3.0, tome as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="20af5-214">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="20af5-215">Siga a [orientação padrão](/dotnet/standard/library-guidance/)da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="20af5-215">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="20af5-216">Adicione uma referência de pacote para o pacote NuGet de cada API se o conjunto correspondente não existir na estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="20af5-216">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="20af5-217">Use uma API que mudou</span><span class="sxs-lookup"><span data-stu-id="20af5-217">Use an API that changed</span></span>

<span data-ttu-id="20af5-218">Imagine um cenário em que você está atualizando uma biblioteca de .NET Core 2.2 para .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="20af5-218">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="20af5-219">Uma API ASP.NET Core que está sendo usada na biblioteca tem uma [mudança de ruptura](/dotnet/core/compatibility/breaking-changes) no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="20af5-219">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="20af5-220">Considere se a biblioteca pode ser reescrita para não usar a API quebrada em todas as versões.</span><span class="sxs-lookup"><span data-stu-id="20af5-220">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="20af5-221">Se você puder reescrever a biblioteca, faça-o e continue a direcionar uma estrutura de destino anterior (por exemplo, .NET Standard 2.0 ou .NET Framework 4.6.1) com referências de pacotes.</span><span class="sxs-lookup"><span data-stu-id="20af5-221">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="20af5-222">Se você não puder reescrever a biblioteca, tome as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="20af5-222">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="20af5-223">Adicione um alvo para .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="20af5-223">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="20af5-224">Adicione `<FrameworkReference>` um elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="20af5-224">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="20af5-225">Use a [diretiva de pré-processador #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) com o símbolo de estrutura de destino apropriado para compilar condicionalmente o código.</span><span class="sxs-lookup"><span data-stu-id="20af5-225">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="20af5-226">Por exemplo, leituras síncronas e gravações em fluxos de solicitação e resposta HTTP são desativados por padrão a partir de ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="20af5-226">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="20af5-227">ASP.NET Core 2.2 suporta o comportamento síncrono por padrão.</span><span class="sxs-lookup"><span data-stu-id="20af5-227">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="20af5-228">Considere uma biblioteca de middleware na qual leituras e gravações síncronas devem ser ativadas onde a I/O está ocorrendo.</span><span class="sxs-lookup"><span data-stu-id="20af5-228">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="20af5-229">A biblioteca deve envolver o código para habilitar recursos síncronos na diretiva de pré-processador apropriada.</span><span class="sxs-lookup"><span data-stu-id="20af5-229">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="20af5-230">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="20af5-230">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="20af5-231">Use uma API introduzida em 3.0</span><span class="sxs-lookup"><span data-stu-id="20af5-231">Use an API introduced in 3.0</span></span>

<span data-ttu-id="20af5-232">Imagine que você deseja usar uma API ASP.NET Core que foi introduzida no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="20af5-232">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="20af5-233">Considere as perguntas a seguir:</span><span class="sxs-lookup"><span data-stu-id="20af5-233">Consider the following questions:</span></span>

1. <span data-ttu-id="20af5-234">A biblioteca requer funcionalmente a nova API?</span><span class="sxs-lookup"><span data-stu-id="20af5-234">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="20af5-235">A biblioteca pode implementar esse recurso de uma forma diferente?</span><span class="sxs-lookup"><span data-stu-id="20af5-235">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="20af5-236">Se a biblioteca requer funcionalmente a API e não há como implementá-la no nível de baixa:</span><span class="sxs-lookup"><span data-stu-id="20af5-236">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="20af5-237">Somente alvo .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="20af5-237">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="20af5-238">Adicione `<FrameworkReference>` um elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="20af5-238">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="20af5-239">Se a biblioteca puder implementar o recurso de uma maneira diferente:</span><span class="sxs-lookup"><span data-stu-id="20af5-239">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="20af5-240">Adicione o .NET Core 3.x como uma estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="20af5-240">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="20af5-241">Adicione `<FrameworkReference>` um elemento para a estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="20af5-241">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="20af5-242">Use a [diretiva de pré-processador #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) com o símbolo de estrutura de destino apropriado para compilar condicionalmente o código.</span><span class="sxs-lookup"><span data-stu-id="20af5-242">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="20af5-243">Por exemplo, o Tag Helper a seguir usa a <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduzida no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="20af5-243">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="20af5-244">Os consumidores que visam o .NET Core 3.0 executam o caminho de código definido pelo símbolo da `NETCOREAPP3_0` estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="20af5-244">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="20af5-245">O tipo de parâmetro de construtor do <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> Tag Helper muda para os consumidores .NET Core 2.1 e .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="20af5-245">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="20af5-246">Essa mudança foi necessária porque ASP.NET `IHostingEnvironment` Núcleo 3.0 marcado como obsoleto e recomendado `IWebHostEnvironment` como a substituição.</span><span class="sxs-lookup"><span data-stu-id="20af5-246">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

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

<span data-ttu-id="20af5-247">O seguinte arquivo de projeto multi-segmentado suporta este cenário do Tag Helper:</span><span class="sxs-lookup"><span data-stu-id="20af5-247">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="20af5-248">Use uma API removida da estrutura compartilhada</span><span class="sxs-lookup"><span data-stu-id="20af5-248">Use an API removed from the shared framework</span></span>

<span data-ttu-id="20af5-249">Para usar um conjunto ASP.NET Core que foi removido da estrutura compartilhada, adicione a referência de pacote apropriada.</span><span class="sxs-lookup"><span data-stu-id="20af5-249">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="20af5-250">Para obter uma lista de pacotes removidos da estrutura compartilhada em ASP.NET Núcleo 3.0, consulte [Remover referências obsoletas de pacotes](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="20af5-250">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="20af5-251">Por exemplo, para adicionar o cliente de API web:</span><span class="sxs-lookup"><span data-stu-id="20af5-251">For example, to add the web API client:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="20af5-252">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="20af5-252">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [<span data-ttu-id="20af5-253">Suporte à implementação do .NET</span><span class="sxs-lookup"><span data-stu-id="20af5-253">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="20af5-254">Políticas de suporte .NET</span><span class="sxs-lookup"><span data-stu-id="20af5-254">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
