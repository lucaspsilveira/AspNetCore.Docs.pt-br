---
title: RazorBibliotecas de classes de componentes ASP.NET Core
author: guardrex
description: Descubra como os componentes podem ser incluídos em Blazor aplicativos de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: b172059407f9a08dacc0fadd804864c7aee7fb90
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944501"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="3274d-103">RazorBibliotecas de classes de componentes ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3274d-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="3274d-104">Por [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="3274d-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="3274d-105">Os componentes podem ser compartilhados em uma [ Razor biblioteca de classes (RCL)](xref:razor-pages/ui-class) em projetos.</span><span class="sxs-lookup"><span data-stu-id="3274d-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="3274d-106">Uma \* Razor biblioteca de classes de componentes\* pode ser incluída em:</span><span class="sxs-lookup"><span data-stu-id="3274d-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="3274d-107">Outro projeto na solução.</span><span class="sxs-lookup"><span data-stu-id="3274d-107">Another project in the solution.</span></span>
* <span data-ttu-id="3274d-108">Um pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="3274d-108">A NuGet package.</span></span>
* <span data-ttu-id="3274d-109">Uma biblioteca .NET referenciada.</span><span class="sxs-lookup"><span data-stu-id="3274d-109">A referenced .NET library.</span></span>

<span data-ttu-id="3274d-110">Assim como os componentes são tipos .NET regulares, os componentes fornecidos por um RCL são assemblies normais do .NET.</span><span class="sxs-lookup"><span data-stu-id="3274d-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="3274d-111">Criar um RCL</span><span class="sxs-lookup"><span data-stu-id="3274d-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3274d-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3274d-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3274d-113">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="3274d-113">Create a new project.</span></span>
1. <span data-ttu-id="3274d-114">Selecione \*\* Razor biblioteca de classes\*\*.</span><span class="sxs-lookup"><span data-stu-id="3274d-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="3274d-115">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3274d-115">Select **Next**.</span></span>
1. <span data-ttu-id="3274d-116">Na caixa de diálogo **criar uma nova Razor biblioteca de classes** , selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="3274d-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="3274d-117">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="3274d-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3274d-118">Os exemplos neste tópico usam o nome do projeto `MyComponentLib1` .</span><span class="sxs-lookup"><span data-stu-id="3274d-118">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="3274d-119">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3274d-119">Select **Create**.</span></span>
1. <span data-ttu-id="3274d-120">Adicione o RCL a uma solução:</span><span class="sxs-lookup"><span data-stu-id="3274d-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="3274d-121">Clique com o botão direito do mouse na solução.</span><span class="sxs-lookup"><span data-stu-id="3274d-121">Right-click the solution.</span></span> <span data-ttu-id="3274d-122">Selecione **Adicionar**  >  **projeto existente**.</span><span class="sxs-lookup"><span data-stu-id="3274d-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="3274d-123">Navegue até o arquivo de projeto do RCL.</span><span class="sxs-lookup"><span data-stu-id="3274d-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="3274d-124">Selecione o arquivo de projeto do RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="3274d-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="3274d-125">Adicione uma referência ao RCL do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="3274d-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="3274d-126">Clique com o botão direito do mouse no projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3274d-126">Right-click the app project.</span></span> <span data-ttu-id="3274d-127">Selecione **Adicionar**  >  **referência**.</span><span class="sxs-lookup"><span data-stu-id="3274d-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="3274d-128">Selecione o projeto RCL.</span><span class="sxs-lookup"><span data-stu-id="3274d-128">Select the RCL project.</span></span> <span data-ttu-id="3274d-129">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="3274d-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="3274d-130">Se a caixa de seleção **páginas de suporte e exibições** estiver marcada ao gerar o RCL do modelo, adicione também um `_Imports.razor` arquivo à raiz do projeto gerado com o seguinte conteúdo para habilitar a Razor criação de componentes:</span><span class="sxs-lookup"><span data-stu-id="3274d-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="3274d-131">Adicione manualmente o arquivo a raiz do projeto gerado.</span><span class="sxs-lookup"><span data-stu-id="3274d-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3274d-132">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="3274d-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="3274d-133">Use o modelo de \*\* Razor biblioteca de classes\*\* ( `razorclasslib` ) com o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3274d-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="3274d-134">No exemplo a seguir, um RCL é criado com o nome `MyComponentLib1` .</span><span class="sxs-lookup"><span data-stu-id="3274d-134">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="3274d-135">A pasta que contém `MyComponentLib1` é criada automaticamente quando o comando é executado:</span><span class="sxs-lookup"><span data-stu-id="3274d-135">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="3274d-136">Se a `-s|--support-pages-and-views` opção for usada ao gerar o RCL do modelo, adicione também um `_Imports.razor` arquivo à raiz do projeto gerado com o seguinte conteúdo para habilitar a criação de Razor componentes:</span><span class="sxs-lookup"><span data-stu-id="3274d-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="3274d-137">Adicione manualmente o arquivo a raiz do projeto gerado.</span><span class="sxs-lookup"><span data-stu-id="3274d-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="3274d-138">Para adicionar a biblioteca a um projeto existente, use o [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) comando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3274d-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="3274d-139">No exemplo a seguir, o RCL é adicionado ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3274d-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="3274d-140">Execute o seguinte comando na pasta do projeto do aplicativo com o caminho para a biblioteca:</span><span class="sxs-lookup"><span data-stu-id="3274d-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="3274d-141">Consumir um componente de biblioteca</span><span class="sxs-lookup"><span data-stu-id="3274d-141">Consume a library component</span></span>

<span data-ttu-id="3274d-142">Para consumir os componentes definidos em uma biblioteca em outro projeto, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="3274d-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="3274d-143">Use o nome completo do tipo com o namespace.</span><span class="sxs-lookup"><span data-stu-id="3274d-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="3274d-144">Use Razor a [`@using`](xref:mvc/views/razor#using) diretiva do.</span><span class="sxs-lookup"><span data-stu-id="3274d-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="3274d-145">Componentes individuais podem ser adicionados por nome.</span><span class="sxs-lookup"><span data-stu-id="3274d-145">Individual components can be added by name.</span></span>

<span data-ttu-id="3274d-146">Nos exemplos a seguir, `MyComponentLib1` é uma biblioteca de componentes que contém um `SalesReport` componente.</span><span class="sxs-lookup"><span data-stu-id="3274d-146">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="3274d-147">O `SalesReport` componente pode ser referenciado usando seu nome de tipo completo com namespace:</span><span class="sxs-lookup"><span data-stu-id="3274d-147">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="3274d-148">O componente também poderá ser referenciado se a biblioteca for colocada no escopo com uma `@using` diretiva:</span><span class="sxs-lookup"><span data-stu-id="3274d-148">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="3274d-149">Inclua a `@using MyComponentLib1` diretiva no arquivo de nível superior `_Import.razor` para disponibilizar os componentes da biblioteca para um projeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="3274d-149">Include the `@using MyComponentLib1` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="3274d-150">Adicione a diretiva a um `_Import.razor` arquivo em qualquer nível para aplicar o namespace a uma única página ou a um conjunto de páginas dentro de uma pasta.</span><span class="sxs-lookup"><span data-stu-id="3274d-150">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="3274d-151">Criar uma Razor biblioteca de classes de componentes com ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="3274d-151">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="3274d-152">Um RCL pode incluir ativos estáticos.</span><span class="sxs-lookup"><span data-stu-id="3274d-152">An RCL can include static assets.</span></span> <span data-ttu-id="3274d-153">Os ativos estáticos estão disponíveis para qualquer aplicativo que consuma a biblioteca.</span><span class="sxs-lookup"><span data-stu-id="3274d-153">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="3274d-154">Para obter mais informações, consulte <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="3274d-154">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="3274d-155">Compilar, empacotar e enviar para o NuGet</span><span class="sxs-lookup"><span data-stu-id="3274d-155">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="3274d-156">Como as bibliotecas de componentes são bibliotecas padrão do .NET, o empacotamento e o envio deles para o NuGet não é diferente do empacotamento e do envio de nenhuma biblioteca para o NuGet.</span><span class="sxs-lookup"><span data-stu-id="3274d-156">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="3274d-157">O empacotamento é executado usando o [`dotnet pack`](/dotnet/core/tools/dotnet-pack) comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3274d-157">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="3274d-158">Carregue o pacote no NuGet usando o [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) comando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3274d-158">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3274d-159">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3274d-159">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="3274d-160">Adicionar um arquivo de configuração do vinculador XML a uma biblioteca</span><span class="sxs-lookup"><span data-stu-id="3274d-160">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
