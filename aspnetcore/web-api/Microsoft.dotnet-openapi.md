---
title: Desenvolver ASP.NET Core aplicativos usando o OpenAPI
author: ryanbrandenburg
description: Demonstra como usar a ferramenta ' Microsoft. dotnet-openapi ' para adicionar referências a arquivos OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 1924fb8ee5ac1ba8dc31d2175a336c8333c81fb2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775707"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="880d2-103">Desenvolver ASP.NET Core aplicativos usando ferramentas OpenAPI</span><span class="sxs-lookup"><span data-stu-id="880d2-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="880d2-104">Por Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="880d2-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="880d2-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) é uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools) para gerenciar referências do [openapi](https://github.com/OAI/OpenAPI-Specification) em um projeto.</span><span class="sxs-lookup"><span data-stu-id="880d2-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="880d2-106">Instalação</span><span class="sxs-lookup"><span data-stu-id="880d2-106">Installation</span></span>

<span data-ttu-id="880d2-107">Para instalar `Microsoft.dotnet-openapi`o, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="880d2-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="880d2-108">Adicionar</span><span class="sxs-lookup"><span data-stu-id="880d2-108">Add</span></span>

<span data-ttu-id="880d2-109">Adicionar uma referência de OpenAPI usando qualquer um dos comandos nessa página adiciona um `<OpenApiReference />` elemento semelhante ao seguinte ao arquivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="880d2-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="880d2-110">A referência anterior é necessária para que o aplicativo chame o código do cliente gerado.</span><span class="sxs-lookup"><span data-stu-id="880d2-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="880d2-111">Adicionar Arquivo</span><span class="sxs-lookup"><span data-stu-id="880d2-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="880d2-112">Opções</span><span class="sxs-lookup"><span data-stu-id="880d2-112">Options</span></span>

| <span data-ttu-id="880d2-113">Opção curta</span><span class="sxs-lookup"><span data-stu-id="880d2-113">Short option</span></span>| <span data-ttu-id="880d2-114">Opção Long</span><span class="sxs-lookup"><span data-stu-id="880d2-114">Long option</span></span>| <span data-ttu-id="880d2-115">Descrição</span><span class="sxs-lookup"><span data-stu-id="880d2-115">Description</span></span> | <span data-ttu-id="880d2-116">Exemplo</span><span class="sxs-lookup"><span data-stu-id="880d2-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="880d2-117">-p</span><span class="sxs-lookup"><span data-stu-id="880d2-117">-p</span></span>|<span data-ttu-id="880d2-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="880d2-118">--updateProject</span></span> | <span data-ttu-id="880d2-119">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="880d2-119">The project to operate on.</span></span> |<span data-ttu-id="880d2-120">dotnet openapi Adicionar arquivo *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="880d2-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="880d2-121">-c</span><span class="sxs-lookup"><span data-stu-id="880d2-121">-c</span></span>|<span data-ttu-id="880d2-122">--gerador de código</span><span class="sxs-lookup"><span data-stu-id="880d2-122">--code-generator</span></span>| <span data-ttu-id="880d2-123">O gerador de código a ser aplicado à referência.</span><span class="sxs-lookup"><span data-stu-id="880d2-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="880d2-124">As opções `NSwagCSharp` são `NSwagTypeScript`e.</span><span class="sxs-lookup"><span data-stu-id="880d2-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="880d2-125">Se `--code-generator` não for especificado, as ferramentas padrão serão `NSwagCSharp`.</span><span class="sxs-lookup"><span data-stu-id="880d2-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="880d2-126">dotnet openapi Adicionar arquivo .\OpenApi.json--gerador de código</span><span class="sxs-lookup"><span data-stu-id="880d2-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="880d2-127">-H</span><span class="sxs-lookup"><span data-stu-id="880d2-127">-h</span></span>|<span data-ttu-id="880d2-128">--help</span><span class="sxs-lookup"><span data-stu-id="880d2-128">--help</span></span>|<span data-ttu-id="880d2-129">Mostra informações da Ajuda</span><span class="sxs-lookup"><span data-stu-id="880d2-129">Show help information</span></span>|<span data-ttu-id="880d2-130">dotnet openapi Adicionar arquivo--ajuda</span><span class="sxs-lookup"><span data-stu-id="880d2-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="880d2-131">Argumentos</span><span class="sxs-lookup"><span data-stu-id="880d2-131">Arguments</span></span>

|  <span data-ttu-id="880d2-132">Argumento</span><span class="sxs-lookup"><span data-stu-id="880d2-132">Argument</span></span>  | <span data-ttu-id="880d2-133">Descrição</span><span class="sxs-lookup"><span data-stu-id="880d2-133">Description</span></span> | <span data-ttu-id="880d2-134">Exemplo</span><span class="sxs-lookup"><span data-stu-id="880d2-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="880d2-135">arquivo de origem</span><span class="sxs-lookup"><span data-stu-id="880d2-135">source-file</span></span> | <span data-ttu-id="880d2-136">A origem da qual criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="880d2-136">The source to create a reference from.</span></span> <span data-ttu-id="880d2-137">Deve ser um arquivo OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="880d2-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="880d2-138">dotnet openapi Adicionar arquivo *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="880d2-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="880d2-139">Adicionar URL</span><span class="sxs-lookup"><span data-stu-id="880d2-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="880d2-140">Opções</span><span class="sxs-lookup"><span data-stu-id="880d2-140">Options</span></span>

| <span data-ttu-id="880d2-141">Opção curta</span><span class="sxs-lookup"><span data-stu-id="880d2-141">Short option</span></span>| <span data-ttu-id="880d2-142">Opção Long</span><span class="sxs-lookup"><span data-stu-id="880d2-142">Long option</span></span>| <span data-ttu-id="880d2-143">Descrição</span><span class="sxs-lookup"><span data-stu-id="880d2-143">Description</span></span> | <span data-ttu-id="880d2-144">Exemplo</span><span class="sxs-lookup"><span data-stu-id="880d2-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="880d2-145">-p</span><span class="sxs-lookup"><span data-stu-id="880d2-145">-p</span></span>|<span data-ttu-id="880d2-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="880d2-146">--updateProject</span></span> | <span data-ttu-id="880d2-147">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="880d2-147">The project to operate on.</span></span> |<span data-ttu-id="880d2-148">dotnet openapi Adicionar URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="880d2-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="880d2-149">-o</span><span class="sxs-lookup"><span data-stu-id="880d2-149">-o</span></span>|<span data-ttu-id="880d2-150">--arquivo de saída</span><span class="sxs-lookup"><span data-stu-id="880d2-150">--output-file</span></span> | <span data-ttu-id="880d2-151">Onde posicionar a cópia local do arquivo OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="880d2-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="880d2-152">dotnet openapi Adicionar URL `https://contoso.com/openapi.json` *--saída-arquivo myclient. JSON*</span><span class="sxs-lookup"><span data-stu-id="880d2-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="880d2-153">-c</span><span class="sxs-lookup"><span data-stu-id="880d2-153">-c</span></span>|<span data-ttu-id="880d2-154">--gerador de código</span><span class="sxs-lookup"><span data-stu-id="880d2-154">--code-generator</span></span>| <span data-ttu-id="880d2-155">O gerador de código a ser aplicado à referência.</span><span class="sxs-lookup"><span data-stu-id="880d2-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="880d2-156">As opções `NSwagCSharp` são `NSwagTypeScript`e.</span><span class="sxs-lookup"><span data-stu-id="880d2-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="880d2-157">dotnet openapi Adicionar arquivo .\OpenApi.json--gerador de código</span><span class="sxs-lookup"><span data-stu-id="880d2-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="880d2-158">-H</span><span class="sxs-lookup"><span data-stu-id="880d2-158">-h</span></span>|<span data-ttu-id="880d2-159">--help</span><span class="sxs-lookup"><span data-stu-id="880d2-159">--help</span></span>|<span data-ttu-id="880d2-160">Mostra informações da Ajuda</span><span class="sxs-lookup"><span data-stu-id="880d2-160">Show help information</span></span>|<span data-ttu-id="880d2-161">dotnet openapi Adicionar URL--ajuda</span><span class="sxs-lookup"><span data-stu-id="880d2-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="880d2-162">Argumentos</span><span class="sxs-lookup"><span data-stu-id="880d2-162">Arguments</span></span>

|  <span data-ttu-id="880d2-163">Argumento</span><span class="sxs-lookup"><span data-stu-id="880d2-163">Argument</span></span>  | <span data-ttu-id="880d2-164">Descrição</span><span class="sxs-lookup"><span data-stu-id="880d2-164">Description</span></span> | <span data-ttu-id="880d2-165">Exemplo</span><span class="sxs-lookup"><span data-stu-id="880d2-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="880d2-166">origem-URL</span><span class="sxs-lookup"><span data-stu-id="880d2-166">source-URL</span></span> | <span data-ttu-id="880d2-167">A origem da qual criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="880d2-167">The source to create a reference from.</span></span> <span data-ttu-id="880d2-168">Deve ser uma URL.</span><span class="sxs-lookup"><span data-stu-id="880d2-168">Must be a URL.</span></span> |<span data-ttu-id="880d2-169">dotnet openapi Adicionar URL`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="880d2-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="880d2-170">Remover</span><span class="sxs-lookup"><span data-stu-id="880d2-170">Remove</span></span>

<span data-ttu-id="880d2-171">Remove a referência OpenAPI correspondente ao nome do arquivo *. csproj* fornecido.</span><span class="sxs-lookup"><span data-stu-id="880d2-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="880d2-172">Quando a referência de OpenAPI for removida, os clientes não serão gerados.</span><span class="sxs-lookup"><span data-stu-id="880d2-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="880d2-173">Os arquivos local *. JSON* e *. YAML* são excluídos.</span><span class="sxs-lookup"><span data-stu-id="880d2-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="880d2-174">Opções</span><span class="sxs-lookup"><span data-stu-id="880d2-174">Options</span></span>

| <span data-ttu-id="880d2-175">Opção curta</span><span class="sxs-lookup"><span data-stu-id="880d2-175">Short option</span></span>| <span data-ttu-id="880d2-176">Opção Long</span><span class="sxs-lookup"><span data-stu-id="880d2-176">Long option</span></span>| <span data-ttu-id="880d2-177">Descrição</span><span class="sxs-lookup"><span data-stu-id="880d2-177">Description</span></span>| <span data-ttu-id="880d2-178">Exemplo</span><span class="sxs-lookup"><span data-stu-id="880d2-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="880d2-179">-p</span><span class="sxs-lookup"><span data-stu-id="880d2-179">-p</span></span>|<span data-ttu-id="880d2-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="880d2-180">--updateProject</span></span> | <span data-ttu-id="880d2-181">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="880d2-181">The project to operate on.</span></span> |<span data-ttu-id="880d2-182">dotnet openapi remover *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="880d2-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="880d2-183">-H</span><span class="sxs-lookup"><span data-stu-id="880d2-183">-h</span></span>|<span data-ttu-id="880d2-184">--help</span><span class="sxs-lookup"><span data-stu-id="880d2-184">--help</span></span>|<span data-ttu-id="880d2-185">Mostra informações da Ajuda</span><span class="sxs-lookup"><span data-stu-id="880d2-185">Show help information</span></span>|<span data-ttu-id="880d2-186">dotnet openapi remover--ajuda</span><span class="sxs-lookup"><span data-stu-id="880d2-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="880d2-187">Argumentos</span><span class="sxs-lookup"><span data-stu-id="880d2-187">Arguments</span></span>

|  <span data-ttu-id="880d2-188">Argumento</span><span class="sxs-lookup"><span data-stu-id="880d2-188">Argument</span></span>  | <span data-ttu-id="880d2-189">Descrição</span><span class="sxs-lookup"><span data-stu-id="880d2-189">Description</span></span>| <span data-ttu-id="880d2-190">Exemplo</span><span class="sxs-lookup"><span data-stu-id="880d2-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="880d2-191">arquivo de origem</span><span class="sxs-lookup"><span data-stu-id="880d2-191">source-file</span></span> | <span data-ttu-id="880d2-192">A origem para a qual remover a referência.</span><span class="sxs-lookup"><span data-stu-id="880d2-192">The source to remove the reference to.</span></span> |<span data-ttu-id="880d2-193">dotnet openapi remover *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="880d2-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="880d2-194">Atualizar</span><span class="sxs-lookup"><span data-stu-id="880d2-194">Refresh</span></span>

<span data-ttu-id="880d2-195">Atualiza a versão local de um arquivo que foi baixado usando o conteúdo mais recente da URL de download.</span><span class="sxs-lookup"><span data-stu-id="880d2-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="880d2-196">Opções</span><span class="sxs-lookup"><span data-stu-id="880d2-196">Options</span></span>

| <span data-ttu-id="880d2-197">Opção curta</span><span class="sxs-lookup"><span data-stu-id="880d2-197">Short option</span></span>| <span data-ttu-id="880d2-198">Opção Long</span><span class="sxs-lookup"><span data-stu-id="880d2-198">Long option</span></span>| <span data-ttu-id="880d2-199">Descrição</span><span class="sxs-lookup"><span data-stu-id="880d2-199">Description</span></span> | <span data-ttu-id="880d2-200">Exemplo</span><span class="sxs-lookup"><span data-stu-id="880d2-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="880d2-201">-p</span><span class="sxs-lookup"><span data-stu-id="880d2-201">-p</span></span>|<span data-ttu-id="880d2-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="880d2-202">--updateProject</span></span> | <span data-ttu-id="880d2-203">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="880d2-203">The project to operate on.</span></span> | <span data-ttu-id="880d2-204">dotnet openapi atualização *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="880d2-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="880d2-205">-H</span><span class="sxs-lookup"><span data-stu-id="880d2-205">-h</span></span>|<span data-ttu-id="880d2-206">--help</span><span class="sxs-lookup"><span data-stu-id="880d2-206">--help</span></span>|<span data-ttu-id="880d2-207">Mostra informações da Ajuda</span><span class="sxs-lookup"><span data-stu-id="880d2-207">Show help information</span></span>|<span data-ttu-id="880d2-208">dotnet openapi atualização--ajuda</span><span class="sxs-lookup"><span data-stu-id="880d2-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="880d2-209">Argumentos</span><span class="sxs-lookup"><span data-stu-id="880d2-209">Arguments</span></span>

|  <span data-ttu-id="880d2-210">Argumento</span><span class="sxs-lookup"><span data-stu-id="880d2-210">Argument</span></span>  | <span data-ttu-id="880d2-211">Descrição</span><span class="sxs-lookup"><span data-stu-id="880d2-211">Description</span></span> | <span data-ttu-id="880d2-212">Exemplo</span><span class="sxs-lookup"><span data-stu-id="880d2-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="880d2-213">origem-URL</span><span class="sxs-lookup"><span data-stu-id="880d2-213">source-URL</span></span> | <span data-ttu-id="880d2-214">A URL da qual atualizar a referência.</span><span class="sxs-lookup"><span data-stu-id="880d2-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="880d2-215">atualização do dotnet openapi`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="880d2-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
