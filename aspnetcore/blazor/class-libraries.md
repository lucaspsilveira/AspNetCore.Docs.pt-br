---
title: bibliotecas de classe de componentes do ASP.NET Core Razor
author: guardrex
description: Descubra como os componentes Blazor podem ser incluídos em aplicativos de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218760"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="1f16c-103">bibliotecas de classe de componentes do ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="1f16c-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="1f16c-104">Por [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="1f16c-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="1f16c-105">Os componentes podem ser compartilhados em uma [biblioteca de classe Razor (RCL)](xref:razor-pages/ui-class) entre projetos.</span><span class="sxs-lookup"><span data-stu-id="1f16c-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="1f16c-106">Uma *biblioteca de classe de componentes razor* pode ser incluída a partir de:</span><span class="sxs-lookup"><span data-stu-id="1f16c-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="1f16c-107">Outro projeto na solução.</span><span class="sxs-lookup"><span data-stu-id="1f16c-107">Another project in the solution.</span></span>
* <span data-ttu-id="1f16c-108">Um pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="1f16c-108">A NuGet package.</span></span>
* <span data-ttu-id="1f16c-109">Uma biblioteca .NET referenciada.</span><span class="sxs-lookup"><span data-stu-id="1f16c-109">A referenced .NET library.</span></span>

<span data-ttu-id="1f16c-110">Assim como os componentes são tipos .NET regulares, os componentes fornecidos por uma RCL são conjuntos .NET normais.</span><span class="sxs-lookup"><span data-stu-id="1f16c-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="1f16c-111">Criar uma RCL</span><span class="sxs-lookup"><span data-stu-id="1f16c-111">Create an RCL</span></span>

<span data-ttu-id="1f16c-112">Siga a orientação no <xref:blazor/get-started> artigo para configurar seu ambiente para Blazor.</span><span class="sxs-lookup"><span data-stu-id="1f16c-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f16c-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f16c-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1f16c-114">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="1f16c-114">Create a new project.</span></span>
1. <span data-ttu-id="1f16c-115">Selecione **Biblioteca de Classe de Barbear**.</span><span class="sxs-lookup"><span data-stu-id="1f16c-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="1f16c-116">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="1f16c-116">Select **Next**.</span></span>
1. <span data-ttu-id="1f16c-117">No Criar uma nova sala de diálogo **de biblioteca de classe Razor,** selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="1f16c-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="1f16c-118">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="1f16c-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="1f16c-119">Os exemplos neste tópico usam `MyComponentLib1`o nome do projeto .</span><span class="sxs-lookup"><span data-stu-id="1f16c-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="1f16c-120">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="1f16c-120">Select **Create**.</span></span>
1. <span data-ttu-id="1f16c-121">Adicione a RCL a uma solução:</span><span class="sxs-lookup"><span data-stu-id="1f16c-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="1f16c-122">Clique com o botão direito do mouse na solução.</span><span class="sxs-lookup"><span data-stu-id="1f16c-122">Right-click the solution.</span></span> <span data-ttu-id="1f16c-123">Selecione **Adicionar** > **projeto existente**.</span><span class="sxs-lookup"><span data-stu-id="1f16c-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="1f16c-124">Navegue até o arquivo de projeto da RCL.</span><span class="sxs-lookup"><span data-stu-id="1f16c-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="1f16c-125">Selecione o arquivo de projeto da RCL *(.csproj).*</span><span class="sxs-lookup"><span data-stu-id="1f16c-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="1f16c-126">Adicione uma referência ao RCL do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="1f16c-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="1f16c-127">Clique com o botão direito do mouse no projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1f16c-127">Right-click the app project.</span></span> <span data-ttu-id="1f16c-128">Selecione **Adicionar** > **referência**.</span><span class="sxs-lookup"><span data-stu-id="1f16c-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="1f16c-129">Selecione o projeto RCL.</span><span class="sxs-lookup"><span data-stu-id="1f16c-129">Select the RCL project.</span></span> <span data-ttu-id="1f16c-130">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="1f16c-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="1f16c-131">Se as páginas de suporte e a caixa **de seleção de visualizações** forem selecionadas ao gerar o RCL a partir do modelo, adicione também um arquivo *_Imports.razor* à raiz do projeto gerado com os seguintes conteúdos para habilitar a autoria do componente Razor:</span><span class="sxs-lookup"><span data-stu-id="1f16c-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="1f16c-132">Adicione manualmente o arquivo a raiz do projeto gerado.</span><span class="sxs-lookup"><span data-stu-id="1f16c-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1f16c-133">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="1f16c-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="1f16c-134">Use o modelo razor`razorclasslib`class **library** ( ) com o novo comando [dotnet](/dotnet/core/tools/dotnet-new) em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="1f16c-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="1f16c-135">No exemplo a seguir, uma RCL é criada chamada `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="1f16c-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="1f16c-136">A pasta `MyComponentLib1` que é desmemoda é criada automaticamente quando o comando é executado:</span><span class="sxs-lookup"><span data-stu-id="1f16c-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="1f16c-137">Se `-s|--support-pages-and-views` o switch for usado ao gerar o RCL a partir do modelo, adicione também um arquivo *_Imports.razor* à raiz do projeto gerado com os seguintes conteúdos para habilitar a autoria do componente Razor:</span><span class="sxs-lookup"><span data-stu-id="1f16c-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="1f16c-138">Adicione manualmente o arquivo a raiz do projeto gerado.</span><span class="sxs-lookup"><span data-stu-id="1f16c-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="1f16c-139">Para adicionar a biblioteca a um projeto existente, use o comando [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="1f16c-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="1f16c-140">No exemplo a seguir, a RCL é adicionada ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1f16c-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="1f16c-141">Execute o seguinte comando da pasta de projeto do aplicativo com o caminho para a biblioteca:</span><span class="sxs-lookup"><span data-stu-id="1f16c-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="1f16c-142">Consumir um componente de biblioteca</span><span class="sxs-lookup"><span data-stu-id="1f16c-142">Consume a library component</span></span>

<span data-ttu-id="1f16c-143">Para consumir componentes definidos em uma biblioteca em outro projeto, use qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="1f16c-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="1f16c-144">Use o nome completo do tipo com o namespace.</span><span class="sxs-lookup"><span data-stu-id="1f16c-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="1f16c-145">Use razor's [ \@usando](xref:mvc/views/razor#using) diretiva.</span><span class="sxs-lookup"><span data-stu-id="1f16c-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="1f16c-146">Componentes individuais podem ser adicionados pelo nome.</span><span class="sxs-lookup"><span data-stu-id="1f16c-146">Individual components can be added by name.</span></span>

<span data-ttu-id="1f16c-147">Nos exemplos a `MyComponentLib1` seguir, está uma `SalesReport` biblioteca de componentes contendo um componente.</span><span class="sxs-lookup"><span data-stu-id="1f16c-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="1f16c-148">O `SalesReport` componente pode ser referenciado usando seu nome de tipo completo com namespace:</span><span class="sxs-lookup"><span data-stu-id="1f16c-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="1f16c-149">O componente também pode ser referenciado se a `@using` biblioteca for colocada em escopo com uma diretiva:</span><span class="sxs-lookup"><span data-stu-id="1f16c-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="1f16c-150">Inclua `@using MyComponentLib1` a diretiva no arquivo *_Import.razor* de nível superior para disponibilizar os componentes da biblioteca para um projeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="1f16c-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="1f16c-151">Adicione a diretiva a um arquivo *_Import.razor* em qualquer nível para aplicar o namespace a uma única página ou conjunto de páginas dentro de uma pasta.</span><span class="sxs-lookup"><span data-stu-id="1f16c-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="1f16c-152">Crie uma biblioteca de classes de componentes razor com ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="1f16c-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="1f16c-153">Uma RCL pode incluir ativos estáticos.</span><span class="sxs-lookup"><span data-stu-id="1f16c-153">An RCL can include static assets.</span></span> <span data-ttu-id="1f16c-154">Os ativos estáticos estão disponíveis para qualquer aplicativo que consuma a biblioteca.</span><span class="sxs-lookup"><span data-stu-id="1f16c-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="1f16c-155">Para obter mais informações, consulte <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="1f16c-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="1f16c-156">Construa, embale e embale para NuGet</span><span class="sxs-lookup"><span data-stu-id="1f16c-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="1f16c-157">Como as bibliotecas de componentes são bibliotecas padrão .NET, empacotá-las e enviá-las para o NuGet não é diferente de embalagem e envio de qualquer biblioteca para nuGet.</span><span class="sxs-lookup"><span data-stu-id="1f16c-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="1f16c-158">A embalagem é realizada usando o comando [dotnet pack](/dotnet/core/tools/dotnet-pack) em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="1f16c-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="1f16c-159">Faça upload do pacote para NuGet usando o comando [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="1f16c-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f16c-160">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="1f16c-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="1f16c-161">Adicione um arquivo de configuração do linker XML a uma biblioteca</span><span class="sxs-lookup"><span data-stu-id="1f16c-161">Add an XML linker configuration file to a library</span></span>](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
