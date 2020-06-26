---
title: Perfis de publicação do Visual Studio (. pubxml) para implantação de aplicativo ASP.NET Core
author: rick-anderson
description: Saiba como criar perfis de publicação no Visual Studio e usá-los para gerenciar implantações de aplicativo ASP.NET Core para vários destinos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: a386066f8d780c5e71c3634065c4e06b74e83c8c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403853"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="bfe9f-103">Perfis de publicação do Visual Studio (. pubxml) para implantação de aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bfe9f-103">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="bfe9f-104">Por [Sayed Hashimi de Ibrahim](https://github.com/sayedihashimi) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bfe9f-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bfe9f-105">Este documento se concentra no uso do Visual Studio 2019 ou posterior para criar e usar perfis de publicação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="bfe9f-106">Os perfis de publicação criados com o Visual Studio podem ser usados com o MSBuild e o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="bfe9f-107">Para obter instruções sobre a publicação no Azure, confira <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="bfe9f-108">O `dotnet new mvc` comando produz um arquivo de projeto que contém o seguinte [ \<Project> elemento](/visualstudio/msbuild/project-element-msbuild)de nível raiz:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="bfe9f-109">O atributo `Sdk` do elemento `<Project>` anterior importa as [propriedades](/visualstudio/msbuild/msbuild-properties) e os [destinos](/visualstudio/msbuild/msbuild-targets) do MSBuild de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* e *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="bfe9f-110">O local padrão para `$(MSBuildSDKsPath)` (com o Visual Studio 2019 Enterprise) é a pasta *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks*.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="bfe9f-111">`Microsoft.NET.Sdk.Web`([SDK da Web](xref:razor-pages/web-sdk)) depende de outros SDKs, incluindo `Microsoft.NET.Sdk` ([SDK do .NET Core](/dotnet/core/project-sdk/msbuild-props)) e `Microsoft.NET.Sdk.Razor` ([ Razor SDK](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-111">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="bfe9f-112">As propriedades e os destinos do MSBuild associados a cada SDK dependente são importados.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="bfe9f-113">Os destinos de publicação importam o conjunto apropriado de destinos com base no método de publicação usado.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="bfe9f-114">Quando o MSBuild ou o Visual Studio carrega um projeto, as seguintes ações de nível alto ocorrem:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="bfe9f-115">Compilar projeto</span><span class="sxs-lookup"><span data-stu-id="bfe9f-115">Build project</span></span>
* <span data-ttu-id="bfe9f-116">Computar arquivos a publicar</span><span class="sxs-lookup"><span data-stu-id="bfe9f-116">Compute files to publish</span></span>
* <span data-ttu-id="bfe9f-117">Publicar arquivos para o destino</span><span class="sxs-lookup"><span data-stu-id="bfe9f-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="bfe9f-118">Itens de projeto de computação</span><span class="sxs-lookup"><span data-stu-id="bfe9f-118">Compute project items</span></span>

<span data-ttu-id="bfe9f-119">Quando o projeto é carregado, os [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (arquivos) são computados.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="bfe9f-120">O tipo de item determina como o arquivo é processado.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="bfe9f-121">Por padrão, os arquivos *.cs* são incluídos na lista de itens `Compile`.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="bfe9f-122">Os arquivos na lista de itens `Compile` são compilados.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="bfe9f-123">A lista de itens `Content` contém arquivos que são publicados juntamente com as saídas de build.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="bfe9f-124">Por padrão, os arquivos que correspondem aos padrões `wwwroot\**`, `**\*.config` e `**\*.json` são incluídos na lista de itens `Content`.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="bfe9f-125">Por exemplo, o  [padrão de recurso de curinga](https://gruntjs.com/configuring-tasks#globbing-patterns)`wwwroot\**` corresponde a todos os arquivos na pasta *wwwroot* e suas subpastas.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bfe9f-126">O [SDK da Web](xref:razor-pages/web-sdk) importa o [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-126">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="bfe9f-127">Como resultado, os arquivos que correspondem aos padrões `**\*.cshtml` e `**\*.razor` também são incluídos na lista de itens `Content`.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="bfe9f-128">O [SDK da Web](xref:razor-pages/web-sdk) importa o [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-128">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="bfe9f-129">Como resultado, os arquivos que correspondem ao padrão `**\*.cshtml` também são incluídos na lista de itens `Content`.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="bfe9f-130">Para adicionar explicitamente um arquivo à lista de publicação, adicione o arquivo diretamente no arquivo *.csproj*, conforme mostrado na seção [Incluir Arquivos](#include-files).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="bfe9f-131">Ao selecionar o botão **Publicar** no Visual Studio ou ao publicar da linha de comando:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="bfe9f-132">Os itens/propriedades são calculados (os arquivos necessários para compilar).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="bfe9f-133">**Somente Visual Studio**: os pacotes NuGet são restaurados.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="bfe9f-134">(A restauração precisa ser explícita pelo usuário na CLI.)</span><span class="sxs-lookup"><span data-stu-id="bfe9f-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="bfe9f-135">O projeto é compilado.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-135">The project builds.</span></span>
* <span data-ttu-id="bfe9f-136">Os itens de publicação são computados (os arquivos necessários para a publicação).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="bfe9f-137">O projeto é publicado (os arquivos computados são copiados para o destino de publicação).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="bfe9f-138">Quando um projeto do ASP.NET Core faz referência a `Microsoft.NET.Sdk.Web` no arquivo de projeto, um arquivo *app_offline.htm* é colocado na raiz do diretório do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="bfe9f-139">Quando o arquivo estiver presente, o módulo do ASP.NET Core apenas desligará o aplicativo e servirá o arquivo *app_offline.htm* durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="bfe9f-140">Para obter mais informações, consulte [Referência de configuração do módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="bfe9f-141">Publicação de linha de comando básica</span><span class="sxs-lookup"><span data-stu-id="bfe9f-141">Basic command-line publishing</span></span>

<span data-ttu-id="bfe9f-142">A publicação de linha de comando funciona em todas as plataformas compatíveis com o .NET Core e não requer o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="bfe9f-143">Nos exemplos a seguir, o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) da CLI do .NET Core é executado no diretório do projeto (que contém o arquivo *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="bfe9f-144">Se a pasta do projeto não for o diretório de trabalho atual, passe explicitamente no caminho do arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="bfe9f-145">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-145">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="bfe9f-146">Execute os comandos a seguir para criar e publicar um aplicativo Web:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-146">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="bfe9f-147">O comando `dotnet publish` produz uma variação da saída a seguir:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="bfe9f-148">O formato da pasta de publicação padrão é *bin\Debug\\{MONIKER DA ESTRUTURA DE DESTINO}\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="bfe9f-149">Por exemplo, *bin\Debug\netcoreapp2.2\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-149">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="bfe9f-150">O comando a seguir especifica um build de `Release` e o diretório de publicação:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="bfe9f-151">O comando `dotnet publish` chama MSBuild, que invoca o destino `Publish`.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="bfe9f-152">Quaisquer parâmetros passados para `dotnet publish` são passados para o MSBuild.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="bfe9f-153">Os parâmetros `-c` e `-o` mapeiam as propriedades `Configuration` e `OutputPath` do MSBuild, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="bfe9f-154">Propriedades do MSBuild podem ser passadas usando qualquer um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="bfe9f-155">Por exemplo, o comando a seguir publica um build de `Release` para um compartilhamento de rede.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="bfe9f-156">O compartilhamento de rede é especificado com barras "/" (*//r8/*) e funciona em todas as plataformas com suporte do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-156">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="bfe9f-157">Confirme que o aplicativo publicado para implantação não está em execução.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="bfe9f-158">Os arquivos da pasta *publish* são bloqueados quando o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="bfe9f-159">A implantação não pode ocorrer porque os arquivos bloqueados não podem ser copiados.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="bfe9f-160">Perfis de publicação</span><span class="sxs-lookup"><span data-stu-id="bfe9f-160">Publish profiles</span></span>

<span data-ttu-id="bfe9f-161">Esta seção usa o Visual Studio 2019 ou posterior para criar um perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="bfe9f-162">Uma vez criado, é possível publicar do Visual Studio ou da linha de comando.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="bfe9f-163">Perfis de publicação podem simplificar o processo de publicação e podem existir em qualquer número.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="bfe9f-164">Crie um perfil de publicação no Visual Studio escolhendo um dos seguintes caminhos:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="bfe9f-165">No Visual Studio, clique com o botão direito do mouse no nome do projeto no **Gerenciador de Soluções** e selecione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-165">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="bfe9f-166">Selecione **Publicar {NOME DO PROJETO}** no menu **Build**.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="bfe9f-167">A guia **Publicar** da página de capacidades do aplicativo é exibida.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="bfe9f-168">Se o projeto não tiver um perfil de publicação, a página **Escolher um destino de publicação** é exibida.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="bfe9f-169">Você deverá selecionar um dos seguintes destinos de publicação:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="bfe9f-170">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="bfe9f-170">Azure App Service</span></span>
* <span data-ttu-id="bfe9f-171">Serviço de Aplicativo do Azure no Linux</span><span class="sxs-lookup"><span data-stu-id="bfe9f-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="bfe9f-172">Máquinas Virtuais do Azure</span><span class="sxs-lookup"><span data-stu-id="bfe9f-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="bfe9f-173">Pasta</span><span class="sxs-lookup"><span data-stu-id="bfe9f-173">Folder</span></span>
* <span data-ttu-id="bfe9f-174">Implantação da Web, IIS, FTP (para qualquer servidor web)</span><span class="sxs-lookup"><span data-stu-id="bfe9f-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="bfe9f-175">Importar Perfil</span><span class="sxs-lookup"><span data-stu-id="bfe9f-175">Import Profile</span></span>

<span data-ttu-id="bfe9f-176">Para determinar o destino de publicação mais apropriado, confira [Quais opções de publicação são adequadas para mim](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="bfe9f-177">Quando o destino de publicação **Pasta** é selecionado, especifique um caminho de pasta para armazenar os ativos publicados.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="bfe9f-178">O caminho de pasta padrão é *bin\\{CONFIGURAÇÃO DO PROJETO}\\{MONIKER DA ESTRUTURA DE DESTINO}\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="bfe9f-179">Por exemplo, *bin\Release\netcoreapp2.2\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-179">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="bfe9f-180">Selecione o botão **Criar Perfil** para concluir.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="bfe9f-181">Depois de um perfil de publicação ser criado, o conteúdo da guia **Publicar** é alterado.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="bfe9f-182">O perfil recém-criado é exibido em uma lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="bfe9f-183">Abaixo da lista suspensa, selecione **Criar novo perfil** para criar outro novo perfil.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="bfe9f-184">A ferramenta de publicação do Visual Studio cria um arquivo MSBuild *Properties/PublishProfiles/{NOME DO PERFIL}.pubxml* que descreve o perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="bfe9f-185">O arquivo *.pubxml*:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="bfe9f-186">Contém as definições de configuração de publicação e é consumido pelo processo de publicação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="bfe9f-187">Pode ser modificado para personalizar o processo de compilação e publicação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="bfe9f-188">Ao publicar em um destino do Azure, o arquivo *.pubxml* contém o identificador de assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="bfe9f-189">Com esse tipo de destino, não é recomendável adicionar esse arquivo ao controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="bfe9f-190">Ao publicar em um destino não Azure, é seguro fazer check-in do arquivo *.pubxml*.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="bfe9f-191">Informações confidenciais (como a senha de publicação) são criptografadas em um nível por usuário/computador.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="bfe9f-192">Elas são armazenadas no arquivo *Properties/PublishProfiles/{NOME DO PERFIL}.pubxml.user*.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="bfe9f-193">Já que esse arquivo pode armazenar informações confidenciais, o check-in dele não deve ser realizado no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="bfe9f-194">Para obter uma visão geral de como publicar um aplicativo web ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="bfe9f-195">As tarefas e os destinos do MSBuild necessários para publicar um aplicativo Web ASP.NET Core são de código-fonte aberto no [repositório ASPNET/WebSDK](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="bfe9f-196">Os comandos a seguir podem usar os perfis de publicação Folder, MSDeploy e [kudu](https://github.com/projectkudu/kudu/wiki) .</span><span class="sxs-lookup"><span data-stu-id="bfe9f-196">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="bfe9f-197">Porque o MSDeploy não oferece suporte em plataforma cruzada, as seguintes opções de MSDeploy têm suporte apenas no Windows.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="bfe9f-198">**Pasta (funciona em plataforma cruzada):**</span><span class="sxs-lookup"><span data-stu-id="bfe9f-198">**Folder (works cross-platform):**</span></span>

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="bfe9f-199">**MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="bfe9f-199">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="bfe9f-200">**Pacote do MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="bfe9f-200">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="bfe9f-201">Nos exemplos anteriores:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-201">In the preceding examples:</span></span>

* <span data-ttu-id="bfe9f-202">`dotnet publish`e `dotnet build` dar suporte a APIs kudu para publicar no Azure de qualquer plataforma.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-202">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="bfe9f-203">A publicação do Visual Studio dá suporte às APIs do Kudu, mas ela é compatível com o WebSDK para publicação multiplataforma para o Azure.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-203">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="bfe9f-204">Não passe `DeployOnBuild` para o `dotnet publish` comando.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-204">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="bfe9f-205">Para obter mais informações, confira [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-205">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="bfe9f-206">Adicione um perfil de publicação à pasta *Properties/PublishProfiles* do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-206">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a><span data-ttu-id="bfe9f-207">Exemplo de publicação de pasta</span><span class="sxs-lookup"><span data-stu-id="bfe9f-207">Folder publish example</span></span>

<span data-ttu-id="bfe9f-208">Ao publicar com um perfil chamado *FolderProfile*, use um dos seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-208">When publishing with a profile named *FolderProfile*, use either of the following commands:</span></span>

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="bfe9f-209">O comando [dotnet build](/dotnet/core/tools/dotnet-build) da CLI do .NET Core chama `msbuild` para executar a compilação e o processo de publicação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="bfe9f-210">Os comandos `dotnet build` e `msbuild` são equivalentes ao passar em um perfil de pasta.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="bfe9f-211">Ao chamar o `msbuild` diretamente no Windows, a versão do .NET Framework do MSBuild é usada.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="bfe9f-212">Chamar `dotnet build` em um perfil não de pasta:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="bfe9f-213">Invoca `msbuild`, que usa MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="bfe9f-214">Resulta em uma falha (mesmo quando em execução no Windows).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="bfe9f-215">Para publicar com um perfil não de pasta, chame o `msbuild` diretamente.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="bfe9f-216">A pasta de perfil de publicação a seguir foi criada com o Visual Studio e publica em um compartilhamento de rede:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="bfe9f-217">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-217">In the preceding example:</span></span>

* <span data-ttu-id="bfe9f-218">A propriedade `<ExcludeApp_Data>` está presente, simplesmente para satisfazer um requisito de esquema XML.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="bfe9f-219">A propriedade `<ExcludeApp_Data>` não tem efeito sobre o processo de publicação, mesmo se houver uma pasta *App_Data* na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="bfe9f-220">A pasta *App_Data* não recebe tratamento especial, como faz em projetos do ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* <span data-ttu-id="bfe9f-221">A propriedade `<LastUsedBuildConfiguration>` é definida como `Release`.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="bfe9f-222">Ao publicar no Visual Studio, o valor de `<LastUsedBuildConfiguration>` é definido usando o valor quando o processo de publicação é iniciado.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="bfe9f-223">`<LastUsedBuildConfiguration>` é especial e não deve ser substituída em um arquivo do MSBuild importado.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="bfe9f-224">Essa propriedade pode, no entanto, ser substituída na linha de comando usando uma das abordagens a seguir.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="bfe9f-225">Usando a CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-225">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="bfe9f-226">Usando o MSBuild:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-226">Using MSBuild:</span></span>

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="bfe9f-227">Para saber mais, confira [MSBuild: como definir a propriedade de configuração](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="bfe9f-228">Publicar em um ponto de extremidade do MSDeploy da linha de comando</span><span class="sxs-lookup"><span data-stu-id="bfe9f-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="bfe9f-229">O exemplo a seguir usa um aplicativo Web do ASP.NET Core, criado pelo Visual Studio, denominada *AzureWebApp*.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="bfe9f-230">Um perfil de publicação dos aplicativos do Azure é adicionado ao Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="bfe9f-231">Para obter mais informações sobre como criar um perfil, consulte a seção [Perfis de publicação](#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="bfe9f-232">Para implantar o aplicativo usando um perfil de publicação, execute o comando `msbuild` de um **Prompt de Comando do Desenvolvedor** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="bfe9f-233">O prompt de comando está disponível na pasta do *Visual Studio* do menu **Página Inicial** na barra de tarefas do Windows.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="bfe9f-234">Para facilitar o acesso, você pode adicionar o prompt de comando ao menu **Ferramentas** no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="bfe9f-235">Para saber mais, confira [Prompt de comando do desenvolvedor para Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="bfe9f-236">O MSBuild usa a sintaxe de comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-236">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="bfe9f-237">{CAMINHO}: caminho para o arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-237">{PATH}: Path to the app's project file.</span></span>
* <span data-ttu-id="bfe9f-238">{PROFILE}: nome do perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-238">{PROFILE}: Name of the publish profile.</span></span>
* <span data-ttu-id="bfe9f-239">{USERNAME}: nome de usuário do MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-239">{USERNAME}: MSDeploy username.</span></span> <span data-ttu-id="bfe9f-240">O {NOME DE USUÁRIO} pode ser encontrado no perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-240">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="bfe9f-241">{SENHA}: senha do MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-241">{PASSWORD}: MSDeploy password.</span></span> <span data-ttu-id="bfe9f-242">Obtenha a {SENHA} do arquivo *{PERFIL}.PublishSettings*.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-242">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="bfe9f-243">Baixe o arquivo *.PublishSettings* de uma das seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="bfe9f-244">**Gerenciador de soluções**: selecione **Exibir**  >  **Gerenciador de nuvem**.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-244">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="bfe9f-245">Conecte-se à sua assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="bfe9f-246">Abra **Serviços de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-246">Open **App Services**.</span></span> <span data-ttu-id="bfe9f-247">Clique com o botão direito do mouse no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-247">Right-click the app.</span></span> <span data-ttu-id="bfe9f-248">Selecione **Baixar Perfil de Publicação**.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-248">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="bfe9f-249">Portal do Azure: selecione **obter perfil de publicação** no painel **visão geral** do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="bfe9f-250">O exemplo a seguir usa um perfil de publicação denominado *AzureWebApp – Implantação da Web*:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-250">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="bfe9f-251">Um perfil de publicação também pode ser usado com o comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) da CLI do .NET Core em um shell de comando do Windows:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="bfe9f-252">O comando `dotnet msbuild` é um comando de plataforma cruzada e pode compilar os aplicativos ASP.NET Core no macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="bfe9f-253">No entanto, o MSBuild no macOS e Linux não é capaz de implantar um aplicativo no Azure ou em outros pontos de extremidade do MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="bfe9f-254">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="bfe9f-254">Set the environment</span></span>

<span data-ttu-id="bfe9f-255">Inclua a propriedade `<EnvironmentName>` no perfil de publicação (*.pubxml*) ou no arquivo de projeto para definir o [ambiente](xref:fundamentals/environments) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="bfe9f-256">Se você precisar de transformações do *web.config* (por exemplo, definir variáveis ​​de ambiente com base na configuração, no perfil ou no ambiente), confira <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="bfe9f-257">Excluir arquivos</span><span class="sxs-lookup"><span data-stu-id="bfe9f-257">Exclude files</span></span>

<span data-ttu-id="bfe9f-258">Ao publicar aplicativos Web ASP.NET Core, os seguintes ativos são incluídos:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="bfe9f-259">Artefatos de compilação</span><span class="sxs-lookup"><span data-stu-id="bfe9f-259">Build artifacts</span></span>
* <span data-ttu-id="bfe9f-260">As pastas e os arquivos que correspondem aos seguintes padrões de recurso de curinga:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="bfe9f-261">`**\*.config` (por exemplo, *web.config*)</span><span class="sxs-lookup"><span data-stu-id="bfe9f-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="bfe9f-262">`**\*.json` (por exemplo, *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="bfe9f-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="bfe9f-263">O MSBuild dá suporte aos [padrões de recurso de curinga](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="bfe9f-264">Por exemplo, o elemento `<Content>` a seguir suprime a cópia dos arquivos de texto (*.txt*) na pasta *wwwroot\content* e suas subpastas:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="bfe9f-265">A marcação anterior pode ser adicionada a um perfil de publicação ou ao arquivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="bfe9f-266">Quando adicionada ao arquivo *.csproj*, a regra será adicionada a todos os perfis de publicação no projeto.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="bfe9f-267">O elemento a seguir `<MsDeploySkipRules>` exclui todos os arquivos da pasta *wwwroot\content* :</span><span class="sxs-lookup"><span data-stu-id="bfe9f-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="bfe9f-268">`<MsDeploySkipRules>` não exclui os destinos de *ignorados* do site de implantação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="bfe9f-269">Pastas e arquivos de destino de `<Content>` são excluídos do site de implantação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="bfe9f-270">Por exemplo, suponha que um aplicativo Web implantado tinha os seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="bfe9f-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bfe9f-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="bfe9f-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bfe9f-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="bfe9f-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="bfe9f-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="bfe9f-274">Nos elementos `<MsDeploySkipRules>` a seguir, esses arquivos não seriam excluídos no site de implantação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="bfe9f-275">Os elementos `<MsDeploySkipRules>` anteriores impedem que os arquivos *ignorados* sejam implantados.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="bfe9f-276">Ele não excluirá esses arquivos depois que eles forem implantados.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="bfe9f-277">O elemento `<Content>` a seguir exclui os arquivos de destino no site de implantação:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="bfe9f-278">O uso da implantação de linha de comando com o elemento `<Content>` anterior produz uma variação da seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="bfe9f-279">Incluir arquivos</span><span class="sxs-lookup"><span data-stu-id="bfe9f-279">Include files</span></span>

<span data-ttu-id="bfe9f-280">As seções a seguir descrevem diferentes abordagens para inclusão de arquivos no momento da publicação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="bfe9f-281">A seção de [inclusão de arquivo geral](#general-file-inclusion) usa o `DotNetPublishFiles` Item, que é fornecido por um arquivo de destino de publicação no [SDK da Web](xref:razor-pages/web-sdk).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="bfe9f-282">A seção [inclusão de arquivo seletivo](#selective-file-inclusion) usa o `ResolvedFileToPublish` Item, que é fornecido por um arquivo de destino de publicação no [SDK do .NET Core](/dotnet/core/project-sdk/msbuild-props).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="bfe9f-283">Como o SDK para Web depende do SDK do .NET Core, qualquer item pode ser usado em um projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="bfe9f-284">Inclusão de arquivos gerais</span><span class="sxs-lookup"><span data-stu-id="bfe9f-284">General file inclusion</span></span>

<span data-ttu-id="bfe9f-285">O elemento `<ItemGroup>` do exemplo a seguir demonstra como copiar uma pasta localizada fora do diretório do projeto para uma pasta do site publicado.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="bfe9f-286">Qualquer arquivo adicionado ao `<ItemGroup>` da marcação a seguir são incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="bfe9f-287">A marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-287">The preceding markup:</span></span>

* <span data-ttu-id="bfe9f-288">Pode ser adicionada ao arquivo *.csproj* ou ao perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="bfe9f-289">Se ela for adicionada ao arquivo *.csproj*, ela será incluída em cada perfil de publicação no projeto.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="bfe9f-290">Declara um item `_CustomFiles` para armazenar os arquivos correspondendo ao padrão de recurso de curinga do atributo `Include`.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="bfe9f-291">A pasta *imagens* referenciada no padrão está localizada fora do diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="bfe9f-292">Uma [propriedade reservada](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), denominada `$(MSBuildProjectDirectory)`, resolve o caminho absoluto do arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="bfe9f-293">Fornece uma lista de arquivos para o item `DotNetPublishFiles`.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="bfe9f-294">Por padrão, o elemento `<DestinationRelativePath>` do item está vazio.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="bfe9f-295">O valor padrão é substituído na marcação e usa [metadados de item bem conhecidos](/visualstudio/msbuild/msbuild-well-known-item-metadata), como `%(RecursiveDir)`.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="bfe9f-296">O texto interno representa a pasta *wwwroot/images* do site publicado.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="bfe9f-297">Inclusão de arquivo seletivo</span><span class="sxs-lookup"><span data-stu-id="bfe9f-297">Selective file inclusion</span></span>

<span data-ttu-id="bfe9f-298">A marcação realçada no exemplo a seguir demonstra:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="bfe9f-299">Copiar um arquivo localizado fora do projeto para a pasta *wwwroot* do site publicado.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="bfe9f-300">O nome de arquivo *ReadMe2.md* é mantido.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="bfe9f-301">Excluir a pasta *wwwroot\Content*.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="bfe9f-302">Excluir *Views\Home\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-302">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="bfe9f-303">O exemplo anterior usa o item `ResolvedFileToPublish`, cujo comportamento padrão é sempre copiar os arquivos fornecidos no atributo `Include` para o site publicado.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="bfe9f-304">Substituir o comportamento padrão, incluindo um elemento filho `<CopyToPublishDirectory>` com o texto interno de um `Never` ou `PreserveNewest`.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="bfe9f-305">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="bfe9f-306">Para obter mais amostras de implantação, confira o [Leiame do repositório do SDK da Web](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-306">For more deployment samples, see the [Web SDK repository Readme](https://github.com/aspnet/websdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="bfe9f-307">Executar um destino antes ou depois da publicação</span><span class="sxs-lookup"><span data-stu-id="bfe9f-307">Run a target before or after publishing</span></span>

<span data-ttu-id="bfe9f-308">Os destinos `BeforePublish` e `AfterPublish` internos executam um destino antes ou após o destino de publicação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="bfe9f-309">Adicione os elementos a seguir ao perfil de publicação para registrar em log as mensagens de console antes e após a publicação:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="bfe9f-310">Publicar em um servidor usando um certificado não confiável</span><span class="sxs-lookup"><span data-stu-id="bfe9f-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="bfe9f-311">Adicione a propriedade `<AllowUntrustedCertificate>` com um valor `True` ao perfil de publicação:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="bfe9f-312">O serviço Kudu</span><span class="sxs-lookup"><span data-stu-id="bfe9f-312">The Kudu service</span></span>

<span data-ttu-id="bfe9f-313">Para exibir os arquivos em uma implantação de aplicativo Web do Serviço de Aplicativo do Azure, use o [serviço Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="bfe9f-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="bfe9f-314">Acrescente o token `scm` ao nome do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="bfe9f-315">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="bfe9f-315">For example:</span></span>

| <span data-ttu-id="bfe9f-316">URL</span><span class="sxs-lookup"><span data-stu-id="bfe9f-316">URL</span></span>                                    | <span data-ttu-id="bfe9f-317">Result</span><span class="sxs-lookup"><span data-stu-id="bfe9f-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="bfe9f-318">Aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="bfe9f-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="bfe9f-319">Serviço Kudu</span><span class="sxs-lookup"><span data-stu-id="bfe9f-319">Kudu service</span></span> |

<span data-ttu-id="bfe9f-320">Selecione o item de menu [Console de Depuração](https://github.com/projectkudu/kudu/wiki/Kudu-console) para exibir, editar, excluir ou adicionar arquivos.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bfe9f-321">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bfe9f-321">Additional resources</span></span>

* <span data-ttu-id="bfe9f-322">A [Implantação da Web](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifica a implantação de aplicativos Web e sites da Web em servidores IIS.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="bfe9f-323">[Repositório GitHub do SDK da Web](https://github.com/aspnet/websdk/issues): problemas de arquivo e recursos de solicitação para implantação.</span><span class="sxs-lookup"><span data-stu-id="bfe9f-323">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="bfe9f-324">Publicar um aplicativo Web ASP.NET para uma VM do Azure a partir do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bfe9f-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
