---
title: Desenvolver ASP.NET Core aplicativos usando o OpenAPI
author: ryanbrandenburg
description: Demonstra como usar a ferramenta ' Microsoft. dotnet-openapi ' para adicionar referências a arquivos OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: eb8d6a1dc70b2aabf495bdb359e243c91e94289f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404789"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="706b4-103">Desenvolver ASP.NET Core aplicativos usando ferramentas OpenAPI</span><span class="sxs-lookup"><span data-stu-id="706b4-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="706b4-104">Por Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="706b4-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="706b4-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) é uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools) para gerenciar referências do [openapi](https://github.com/OAI/OpenAPI-Specification) em um projeto.</span><span class="sxs-lookup"><span data-stu-id="706b4-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="706b4-106">Instalação</span><span class="sxs-lookup"><span data-stu-id="706b4-106">Installation</span></span>

<span data-ttu-id="706b4-107">Para instalar `Microsoft.dotnet-openapi` o, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="706b4-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="706b4-108">Adicionar</span><span class="sxs-lookup"><span data-stu-id="706b4-108">Add</span></span>

<span data-ttu-id="706b4-109">Adicionar uma referência de OpenAPI usando qualquer um dos comandos nessa página adiciona um `<OpenApiReference />` elemento semelhante ao seguinte ao arquivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="706b4-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="706b4-110">A referência anterior é necessária para que o aplicativo chame o código do cliente gerado.</span><span class="sxs-lookup"><span data-stu-id="706b4-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="706b4-111">Adicionar Arquivo</span><span class="sxs-lookup"><span data-stu-id="706b4-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="706b4-112">Opções</span><span class="sxs-lookup"><span data-stu-id="706b4-112">Options</span></span>

| <span data-ttu-id="706b4-113">Opção curta</span><span class="sxs-lookup"><span data-stu-id="706b4-113">Short option</span></span>| <span data-ttu-id="706b4-114">Opção Long</span><span class="sxs-lookup"><span data-stu-id="706b4-114">Long option</span></span>| <span data-ttu-id="706b4-115">Descrição</span><span class="sxs-lookup"><span data-stu-id="706b4-115">Description</span></span> | <span data-ttu-id="706b4-116">Exemplo</span><span class="sxs-lookup"><span data-stu-id="706b4-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="706b4-117">-p</span><span class="sxs-lookup"><span data-stu-id="706b4-117">-p</span></span>|<span data-ttu-id="706b4-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="706b4-118">--updateProject</span></span> | <span data-ttu-id="706b4-119">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="706b4-119">The project to operate on.</span></span> |<span data-ttu-id="706b4-120">dotnet openapi Adicionar arquivo *--updateProject .\Ref.csproj* .\OpenAPI.js</span><span class="sxs-lookup"><span data-stu-id="706b4-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="706b4-121">-c</span><span class="sxs-lookup"><span data-stu-id="706b4-121">-c</span></span>|<span data-ttu-id="706b4-122">--gerador de código</span><span class="sxs-lookup"><span data-stu-id="706b4-122">--code-generator</span></span>| <span data-ttu-id="706b4-123">O gerador de código a ser aplicado à referência.</span><span class="sxs-lookup"><span data-stu-id="706b4-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="706b4-124">As opções são `NSwagCSharp` e `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="706b4-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="706b4-125">Se `--code-generator` não for especificado, as ferramentas padrão serão `NSwagCSharp` .</span><span class="sxs-lookup"><span data-stu-id="706b4-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="706b4-126">dotnet openapi Adicionar arquivo .\OpenApi.jsno gerador-de-código</span><span class="sxs-lookup"><span data-stu-id="706b4-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="706b4-127">-H</span><span class="sxs-lookup"><span data-stu-id="706b4-127">-h</span></span>|<span data-ttu-id="706b4-128">--help</span><span class="sxs-lookup"><span data-stu-id="706b4-128">--help</span></span>|<span data-ttu-id="706b4-129">Mostra informações da Ajuda</span><span class="sxs-lookup"><span data-stu-id="706b4-129">Show help information</span></span>|<span data-ttu-id="706b4-130">dotnet openapi Adicionar arquivo--ajuda</span><span class="sxs-lookup"><span data-stu-id="706b4-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="706b4-131">Argumentos</span><span class="sxs-lookup"><span data-stu-id="706b4-131">Arguments</span></span>

|  <span data-ttu-id="706b4-132">Argumento</span><span class="sxs-lookup"><span data-stu-id="706b4-132">Argument</span></span>  | <span data-ttu-id="706b4-133">Descrição</span><span class="sxs-lookup"><span data-stu-id="706b4-133">Description</span></span> | <span data-ttu-id="706b4-134">Exemplo</span><span class="sxs-lookup"><span data-stu-id="706b4-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="706b4-135">arquivo de origem</span><span class="sxs-lookup"><span data-stu-id="706b4-135">source-file</span></span> | <span data-ttu-id="706b4-136">A origem da qual criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="706b4-136">The source to create a reference from.</span></span> <span data-ttu-id="706b4-137">Deve ser um arquivo OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="706b4-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="706b4-138">dotnet openapi Adicionar arquivo *.\OpenAPI.jsem*</span><span class="sxs-lookup"><span data-stu-id="706b4-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="706b4-139">Adicionar URL</span><span class="sxs-lookup"><span data-stu-id="706b4-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="706b4-140">Opções</span><span class="sxs-lookup"><span data-stu-id="706b4-140">Options</span></span>

| <span data-ttu-id="706b4-141">Opção curta</span><span class="sxs-lookup"><span data-stu-id="706b4-141">Short option</span></span>| <span data-ttu-id="706b4-142">Opção Long</span><span class="sxs-lookup"><span data-stu-id="706b4-142">Long option</span></span>| <span data-ttu-id="706b4-143">Descrição</span><span class="sxs-lookup"><span data-stu-id="706b4-143">Description</span></span> | <span data-ttu-id="706b4-144">Exemplo</span><span class="sxs-lookup"><span data-stu-id="706b4-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="706b4-145">-p</span><span class="sxs-lookup"><span data-stu-id="706b4-145">-p</span></span>|<span data-ttu-id="706b4-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="706b4-146">--updateProject</span></span> | <span data-ttu-id="706b4-147">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="706b4-147">The project to operate on.</span></span> |<span data-ttu-id="706b4-148">dotnet openapi Adicionar URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="706b4-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="706b4-149">-o</span><span class="sxs-lookup"><span data-stu-id="706b4-149">-o</span></span>|<span data-ttu-id="706b4-150">--arquivo de saída</span><span class="sxs-lookup"><span data-stu-id="706b4-150">--output-file</span></span> | <span data-ttu-id="706b4-151">Onde posicionar a cópia local do arquivo OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="706b4-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="706b4-152">dotnet openapi Adicionar URL `https://contoso.com/openapi.json` *--arquivo de saída myclient.jsem*</span><span class="sxs-lookup"><span data-stu-id="706b4-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="706b4-153">-c</span><span class="sxs-lookup"><span data-stu-id="706b4-153">-c</span></span>|<span data-ttu-id="706b4-154">--gerador de código</span><span class="sxs-lookup"><span data-stu-id="706b4-154">--code-generator</span></span>| <span data-ttu-id="706b4-155">O gerador de código a ser aplicado à referência.</span><span class="sxs-lookup"><span data-stu-id="706b4-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="706b4-156">As opções são `NSwagCSharp` e `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="706b4-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="706b4-157">dotnet openapi Adicionar arquivo .\OpenApi.jsno gerador-de-código</span><span class="sxs-lookup"><span data-stu-id="706b4-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="706b4-158">-H</span><span class="sxs-lookup"><span data-stu-id="706b4-158">-h</span></span>|<span data-ttu-id="706b4-159">--help</span><span class="sxs-lookup"><span data-stu-id="706b4-159">--help</span></span>|<span data-ttu-id="706b4-160">Mostra informações da Ajuda</span><span class="sxs-lookup"><span data-stu-id="706b4-160">Show help information</span></span>|<span data-ttu-id="706b4-161">dotnet openapi Adicionar URL--ajuda</span><span class="sxs-lookup"><span data-stu-id="706b4-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="706b4-162">Argumentos</span><span class="sxs-lookup"><span data-stu-id="706b4-162">Arguments</span></span>

|  <span data-ttu-id="706b4-163">Argumento</span><span class="sxs-lookup"><span data-stu-id="706b4-163">Argument</span></span>  | <span data-ttu-id="706b4-164">Descrição</span><span class="sxs-lookup"><span data-stu-id="706b4-164">Description</span></span> | <span data-ttu-id="706b4-165">Exemplo</span><span class="sxs-lookup"><span data-stu-id="706b4-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="706b4-166">origem-URL</span><span class="sxs-lookup"><span data-stu-id="706b4-166">source-URL</span></span> | <span data-ttu-id="706b4-167">A origem da qual criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="706b4-167">The source to create a reference from.</span></span> <span data-ttu-id="706b4-168">Deve ser uma URL.</span><span class="sxs-lookup"><span data-stu-id="706b4-168">Must be a URL.</span></span> |<span data-ttu-id="706b4-169">dotnet openapi Adicionar URL`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="706b4-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="706b4-170">Remover</span><span class="sxs-lookup"><span data-stu-id="706b4-170">Remove</span></span>

<span data-ttu-id="706b4-171">Remove a referência OpenAPI correspondente ao nome do arquivo *. csproj* fornecido.</span><span class="sxs-lookup"><span data-stu-id="706b4-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="706b4-172">Quando a referência de OpenAPI for removida, os clientes não serão gerados.</span><span class="sxs-lookup"><span data-stu-id="706b4-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="706b4-173">Os arquivos local *. JSON* e *. YAML* são excluídos.</span><span class="sxs-lookup"><span data-stu-id="706b4-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="706b4-174">Opções</span><span class="sxs-lookup"><span data-stu-id="706b4-174">Options</span></span>

| <span data-ttu-id="706b4-175">Opção curta</span><span class="sxs-lookup"><span data-stu-id="706b4-175">Short option</span></span>| <span data-ttu-id="706b4-176">Opção Long</span><span class="sxs-lookup"><span data-stu-id="706b4-176">Long option</span></span>| <span data-ttu-id="706b4-177">Descrição</span><span class="sxs-lookup"><span data-stu-id="706b4-177">Description</span></span>| <span data-ttu-id="706b4-178">Exemplo</span><span class="sxs-lookup"><span data-stu-id="706b4-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="706b4-179">-p</span><span class="sxs-lookup"><span data-stu-id="706b4-179">-p</span></span>|<span data-ttu-id="706b4-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="706b4-180">--updateProject</span></span> | <span data-ttu-id="706b4-181">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="706b4-181">The project to operate on.</span></span> |<span data-ttu-id="706b4-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.js</span><span class="sxs-lookup"><span data-stu-id="706b4-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="706b4-183">-H</span><span class="sxs-lookup"><span data-stu-id="706b4-183">-h</span></span>|<span data-ttu-id="706b4-184">--help</span><span class="sxs-lookup"><span data-stu-id="706b4-184">--help</span></span>|<span data-ttu-id="706b4-185">Mostra informações da Ajuda</span><span class="sxs-lookup"><span data-stu-id="706b4-185">Show help information</span></span>|<span data-ttu-id="706b4-186">dotnet openapi remover--ajuda</span><span class="sxs-lookup"><span data-stu-id="706b4-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="706b4-187">Argumentos</span><span class="sxs-lookup"><span data-stu-id="706b4-187">Arguments</span></span>

|  <span data-ttu-id="706b4-188">Argumento</span><span class="sxs-lookup"><span data-stu-id="706b4-188">Argument</span></span>  | <span data-ttu-id="706b4-189">Descrição</span><span class="sxs-lookup"><span data-stu-id="706b4-189">Description</span></span>| <span data-ttu-id="706b4-190">Exemplo</span><span class="sxs-lookup"><span data-stu-id="706b4-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="706b4-191">arquivo de origem</span><span class="sxs-lookup"><span data-stu-id="706b4-191">source-file</span></span> | <span data-ttu-id="706b4-192">A origem para a qual remover a referência.</span><span class="sxs-lookup"><span data-stu-id="706b4-192">The source to remove the reference to.</span></span> |<span data-ttu-id="706b4-193">dotnet openapi remover *.\OpenAPI.js*</span><span class="sxs-lookup"><span data-stu-id="706b4-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="706b4-194">Atualizar</span><span class="sxs-lookup"><span data-stu-id="706b4-194">Refresh</span></span>

<span data-ttu-id="706b4-195">Atualiza a versão local de um arquivo que foi baixado usando o conteúdo mais recente da URL de download.</span><span class="sxs-lookup"><span data-stu-id="706b4-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="706b4-196">Opções</span><span class="sxs-lookup"><span data-stu-id="706b4-196">Options</span></span>

| <span data-ttu-id="706b4-197">Opção curta</span><span class="sxs-lookup"><span data-stu-id="706b4-197">Short option</span></span>| <span data-ttu-id="706b4-198">Opção Long</span><span class="sxs-lookup"><span data-stu-id="706b4-198">Long option</span></span>| <span data-ttu-id="706b4-199">Descrição</span><span class="sxs-lookup"><span data-stu-id="706b4-199">Description</span></span> | <span data-ttu-id="706b4-200">Exemplo</span><span class="sxs-lookup"><span data-stu-id="706b4-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="706b4-201">-p</span><span class="sxs-lookup"><span data-stu-id="706b4-201">-p</span></span>|<span data-ttu-id="706b4-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="706b4-202">--updateProject</span></span> | <span data-ttu-id="706b4-203">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="706b4-203">The project to operate on.</span></span> | <span data-ttu-id="706b4-204">dotnet openapi atualização *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="706b4-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="706b4-205">-H</span><span class="sxs-lookup"><span data-stu-id="706b4-205">-h</span></span>|<span data-ttu-id="706b4-206">--help</span><span class="sxs-lookup"><span data-stu-id="706b4-206">--help</span></span>|<span data-ttu-id="706b4-207">Mostra informações da Ajuda</span><span class="sxs-lookup"><span data-stu-id="706b4-207">Show help information</span></span>|<span data-ttu-id="706b4-208">dotnet openapi atualização--ajuda</span><span class="sxs-lookup"><span data-stu-id="706b4-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="706b4-209">Argumentos</span><span class="sxs-lookup"><span data-stu-id="706b4-209">Arguments</span></span>

|  <span data-ttu-id="706b4-210">Argumento</span><span class="sxs-lookup"><span data-stu-id="706b4-210">Argument</span></span>  | <span data-ttu-id="706b4-211">Descrição</span><span class="sxs-lookup"><span data-stu-id="706b4-211">Description</span></span> | <span data-ttu-id="706b4-212">Exemplo</span><span class="sxs-lookup"><span data-stu-id="706b4-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="706b4-213">origem-URL</span><span class="sxs-lookup"><span data-stu-id="706b4-213">source-URL</span></span> | <span data-ttu-id="706b4-214">A URL da qual atualizar a referência.</span><span class="sxs-lookup"><span data-stu-id="706b4-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="706b4-215">atualização do dotnet openapi`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="706b4-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
