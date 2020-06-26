---
title: Provedores de arquivos no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/file-providers
ms.openlocfilehash: 9c679f6cb56397632eb99708bd2edd83c55ecf50
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408260"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="58c4d-103">Provedores de arquivos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58c4d-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="58c4d-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="58c4d-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="58c4d-105">O ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="58c4d-106">Os provedores de arquivos são usados em todo o ASP.NET Core Framework.</span><span class="sxs-lookup"><span data-stu-id="58c4d-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="58c4d-107">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="58c4d-107">For example:</span></span>

* <span data-ttu-id="58c4d-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>expõe a raiz do [conteúdo](xref:fundamentals/index#content-root) do aplicativo e a [raiz da Web](xref:fundamentals/index#web-root) como `IFileProvider` tipos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="58c4d-109">O [middleware de arquivos estáticos](xref:fundamentals/static-files) usa provedores de arquivos para localizar arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="58c4d-110">[Razor](xref:mvc/views/razor)usa provedores de arquivos para localizar páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="58c4d-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="58c4d-111">As ferramentas do .NET Core usam provedores de arquivos e padrões glob para especificar quais arquivos devem ser publicados.</span><span class="sxs-lookup"><span data-stu-id="58c4d-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="58c4d-112">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="58c4d-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="58c4d-113">Interfaces de provedor de arquivo</span><span class="sxs-lookup"><span data-stu-id="58c4d-113">File Provider interfaces</span></span>

<span data-ttu-id="58c4d-114">A interface principal é <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="58c4d-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="58c4d-115">`IFileProvider` expõe métodos para:</span><span class="sxs-lookup"><span data-stu-id="58c4d-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="58c4d-116">Obter informações do arquivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="58c4d-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="58c4d-117">Obter informações do diretório (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="58c4d-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="58c4d-118">Configurar notificações de alteração (usando um <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="58c4d-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="58c4d-119">`IFileInfo` fornece métodos e propriedades para trabalhar com arquivos:</span><span class="sxs-lookup"><span data-stu-id="58c4d-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="58c4d-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (em bytes)</span><span class="sxs-lookup"><span data-stu-id="58c4d-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="58c4d-121">Data de <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="58c4d-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="58c4d-122">Você pode ler o arquivo usando o <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> método.</span><span class="sxs-lookup"><span data-stu-id="58c4d-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="58c4d-123">O aplicativo de exemplo *FileProviderSample* demonstra como configurar um provedor de arquivos no `Startup.ConfigureServices` para uso em todo o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58c4d-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="58c4d-124">Implementações do provedor de arquivos</span><span class="sxs-lookup"><span data-stu-id="58c4d-124">File Provider implementations</span></span>

<span data-ttu-id="58c4d-125">A tabela a seguir lista as implementações do `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="58c4d-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="58c4d-126">Implementação</span><span class="sxs-lookup"><span data-stu-id="58c4d-126">Implementation</span></span> | <span data-ttu-id="58c4d-127">Descrição</span><span class="sxs-lookup"><span data-stu-id="58c4d-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="58c4d-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="58c4d-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="58c4d-129">Usado para fornecer acesso combinado a arquivos e diretórios de um ou mais outros provedores.</span><span class="sxs-lookup"><span data-stu-id="58c4d-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="58c4d-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="58c4d-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="58c4d-131">Usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="58c4d-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="58c4d-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="58c4d-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="58c4d-133">Usado para acessar os arquivos físicos do sistema.</span><span class="sxs-lookup"><span data-stu-id="58c4d-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="58c4d-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="58c4d-134">PhysicalFileProvider</span></span>

<span data-ttu-id="58c4d-135">O <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fornece acesso ao sistema de arquivos físico.</span><span class="sxs-lookup"><span data-stu-id="58c4d-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="58c4d-136">O `PhysicalFileProvider` usa o tipo <xref:System.IO.File?displayProperty=fullName> (para o provedor físico) e define o escopo de todos os caminhos para um diretório e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="58c4d-137">Esse escopo impede o acesso ao sistema de arquivos fora do diretório especificado e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="58c4d-138">O cenário mais comum para criar e usar um `PhysicalFileProvider` é solicitar um `IFileProvider` em um construtor por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58c4d-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="58c4d-139">Ao instanciar esse provedor diretamente, um caminho de diretório absoluto é necessário e serve como o caminho base para todas as solicitações feitas usando o provedor.</span><span class="sxs-lookup"><span data-stu-id="58c4d-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="58c4d-140">Não há suporte para padrões glob no caminho do diretório.</span><span class="sxs-lookup"><span data-stu-id="58c4d-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="58c4d-141">O código a seguir mostra como usar o `PhysicalFileProvider` para obter o conteúdo do diretório e as informações do arquivo:</span><span class="sxs-lookup"><span data-stu-id="58c4d-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="58c4d-142">Tipos no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="58c4d-142">Types in the preceding example:</span></span>

* <span data-ttu-id="58c4d-143">`provider` é um `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="58c4d-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="58c4d-144">`contents` é um `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="58c4d-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="58c4d-145">`fileInfo` é um `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="58c4d-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="58c4d-146">O provedor de arquivos pode ser usado para percorrer o diretório especificado por `applicationRoot` ou chamar `GetFileInfo` para obter as informações de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="58c4d-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="58c4d-147">Padrões de glob não podem ser passados para o `GetFileInfo` método.</span><span class="sxs-lookup"><span data-stu-id="58c4d-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="58c4d-148">O provedor de arquivos não tem acesso fora do diretório `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="58c4d-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="58c4d-149">O aplicativo de exemplo *FileProviderSample* cria o provedor no `Startup.ConfigureServices` método usando <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="58c4d-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="58c4d-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="58c4d-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="58c4d-151">O <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> é usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="58c4d-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="58c4d-152">O `ManifestEmbeddedFileProvider` usa um manifesto compilado no assembly para reconstruir os caminhos originais dos arquivos inseridos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="58c4d-153">Para gerar um manifesto dos arquivos inseridos:</span><span class="sxs-lookup"><span data-stu-id="58c4d-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="58c4d-154">Adicione o pacote NuGet [Microsoft. Extensions.](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) . Embedded ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="58c4d-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="58c4d-155">Defina a propriedade `<GenerateEmbeddedFilesManifest>` como `true`.</span><span class="sxs-lookup"><span data-stu-id="58c4d-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="58c4d-156">Especifique os arquivos a serem inseridos [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :</span><span class="sxs-lookup"><span data-stu-id="58c4d-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="58c4d-157">Use [padrões glob](#glob-patterns) para especificar um ou mais arquivos a serem inseridos no assembly.</span><span class="sxs-lookup"><span data-stu-id="58c4d-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="58c4d-158">O aplicativo de exemplo *FileProviderSample* cria um `ManifestEmbeddedFileProvider` e passa o assembly atualmente em execução para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="58c4d-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="58c4d-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="58c4d-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="58c4d-160">Sobrecargas adicionais permitem:</span><span class="sxs-lookup"><span data-stu-id="58c4d-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="58c4d-161">Especificar um caminho de arquivo relativo.</span><span class="sxs-lookup"><span data-stu-id="58c4d-161">Specify a relative file path.</span></span>
* <span data-ttu-id="58c4d-162">Delimitar os arquivos segundo a data da última modificação.</span><span class="sxs-lookup"><span data-stu-id="58c4d-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="58c4d-163">Nomear o recurso inserido que contém o manifesto do arquivo inserido.</span><span class="sxs-lookup"><span data-stu-id="58c4d-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="58c4d-164">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="58c4d-164">Overload</span></span> | <span data-ttu-id="58c4d-165">Descrição</span><span class="sxs-lookup"><span data-stu-id="58c4d-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="58c4d-166">Aceita um parâmetro de caminho relativo `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="58c4d-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="58c4d-167">Especifique o `root` para definir o escopo das chamadas de <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> para esses recursos no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="58c4d-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="58c4d-168">Aceita um parâmetro de caminho relativo `root` opcional e um parâmetro de data `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="58c4d-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="58c4d-169">A data `lastModified` tem como escopo a data da última modificação das instâncias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> retornadas por <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="58c4d-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="58c4d-170">Aceita um caminho relativo `root` opcional, data de `lastModified` e parâmetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="58c4d-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="58c4d-171">O `manifestName` representa o nome do recurso inserido que contém o manifesto.</span><span class="sxs-lookup"><span data-stu-id="58c4d-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="58c4d-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="58c4d-172">CompositeFileProvider</span></span>

<span data-ttu-id="58c4d-173">O <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instâncias de `IFileProvider`, expondo uma interface única para trabalhar com arquivos de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="58c4d-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="58c4d-174">Ao criar o `CompositeFileProvider`, passe uma ou mais instâncias de `IFileProvider` para o construtor.</span><span class="sxs-lookup"><span data-stu-id="58c4d-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="58c4d-175">No aplicativo de exemplo *FileProviderSample* , um `PhysicalFileProvider` e um `ManifestEmbeddedFileProvider` fornecem arquivos para um `CompositeFileProvider` registrado no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="58c4d-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="58c4d-176">O código a seguir é encontrado no método do projeto `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="58c4d-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="58c4d-177">Monitorar as alterações</span><span class="sxs-lookup"><span data-stu-id="58c4d-177">Watch for changes</span></span>

<span data-ttu-id="58c4d-178">O <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> método fornece um cenário para assistir a um ou mais arquivos ou diretórios para alterações.</span><span class="sxs-lookup"><span data-stu-id="58c4d-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="58c4d-179">O método `Watch`:</span><span class="sxs-lookup"><span data-stu-id="58c4d-179">The `Watch` method:</span></span>

* <span data-ttu-id="58c4d-180">Aceita uma cadeia de caracteres de caminho de arquivo, que pode usar [padrões glob](#glob-patterns) para especificar vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="58c4d-181">Retorna um <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="58c4d-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="58c4d-182">O token de alteração resultante expõe:</span><span class="sxs-lookup"><span data-stu-id="58c4d-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="58c4d-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Uma propriedade que pode ser inspecionada para determinar se ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="58c4d-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="58c4d-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Chamado quando são detectadas alterações na cadeia de caracteres do caminho especificado.</span><span class="sxs-lookup"><span data-stu-id="58c4d-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="58c4d-185">Cada token de alteração chama apenas seu retorno de chamada associado em resposta a uma única alteração.</span><span class="sxs-lookup"><span data-stu-id="58c4d-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="58c4d-186">Para permitir o monitoramento constante, use um <xref:System.Threading.Tasks.TaskCompletionSource`1> (mostrado abaixo) ou recrie instâncias de `IChangeToken` em resposta a alterações.</span><span class="sxs-lookup"><span data-stu-id="58c4d-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="58c4d-187">O aplicativo de exemplo *WatchConsole* grava uma mensagem sempre que um arquivo *. txt* no diretório *textfiles* é modificado:</span><span class="sxs-lookup"><span data-stu-id="58c4d-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="58c4d-188">Alguns sistemas de arquivos, como contêineres do Docker e compartilhamentos de rede, podem não enviar notificações de alteração de forma confiável.</span><span class="sxs-lookup"><span data-stu-id="58c4d-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="58c4d-189">Defina a variável de ambiente `DOTNET_USE_POLLING_FILE_WATCHER` como `1` ou `true` para sondar o sistema de arquivos a cada quatro segundos em relação a alterações.</span><span class="sxs-lookup"><span data-stu-id="58c4d-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="58c4d-190">Padrões glob</span><span class="sxs-lookup"><span data-stu-id="58c4d-190">Glob patterns</span></span>

<span data-ttu-id="58c4d-191">Os caminhos do sistema de arquivos usam padrões curinga chamados *padrões glob (ou globbing)*.</span><span class="sxs-lookup"><span data-stu-id="58c4d-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="58c4d-192">Especifique grupos de arquivos com esses padrões.</span><span class="sxs-lookup"><span data-stu-id="58c4d-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="58c4d-193">Os dois caracteres curinga são `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="58c4d-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="58c4d-194">Corresponde a qualquer coisa no nível da pasta atual, qualquer nome de arquivo ou qualquer extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="58c4d-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="58c4d-195">As correspondências são terminadas pelos caracteres `/` e `.` no caminho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="58c4d-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="58c4d-196">Coincide a qualquer coisa em vários níveis de diretório.</span><span class="sxs-lookup"><span data-stu-id="58c4d-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="58c4d-197">Pode ser usada para fazer a correspondência recursiva com vários arquivos em uma hierarquia de diretórios.</span><span class="sxs-lookup"><span data-stu-id="58c4d-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="58c4d-198">A tabela a seguir fornece exemplos comuns de padrões de glob.</span><span class="sxs-lookup"><span data-stu-id="58c4d-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="58c4d-199">Padrão</span><span class="sxs-lookup"><span data-stu-id="58c4d-199">Pattern</span></span>  |<span data-ttu-id="58c4d-200">Descrição</span><span class="sxs-lookup"><span data-stu-id="58c4d-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="58c4d-201">Corresponde a um arquivo específico em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="58c4d-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="58c4d-202">Corresponde a todos os arquivos com a extensão *.txt* em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="58c4d-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="58c4d-203">Corresponde a todos os *appsettings.jsem* arquivos em diretórios exatamente um nível abaixo da pasta do *diretório* .</span><span class="sxs-lookup"><span data-stu-id="58c4d-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="58c4d-204">Corresponde a todos os arquivos com uma extensão *. txt* encontrado em qualquer lugar na pasta do *diretório* .</span><span class="sxs-lookup"><span data-stu-id="58c4d-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="58c4d-205">O ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="58c4d-206">Os provedores de arquivos são usados ​​em toda a estrutura do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="58c4d-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="58c4d-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>expõe a raiz do [conteúdo](xref:fundamentals/index#content-root) do aplicativo e a [raiz da Web](xref:fundamentals/index#web-root) como `IFileProvider` tipos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="58c4d-208">O [middleware de arquivos estáticos](xref:fundamentals/static-files) usa provedores de arquivos para localizar arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="58c4d-209">[Razor](xref:mvc/views/razor)usa provedores de arquivos para localizar páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="58c4d-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="58c4d-210">As ferramentas do .NET Core usam provedores de arquivos e padrões glob para especificar quais arquivos devem ser publicados.</span><span class="sxs-lookup"><span data-stu-id="58c4d-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="58c4d-211">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="58c4d-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="58c4d-212">Interfaces de provedor de arquivo</span><span class="sxs-lookup"><span data-stu-id="58c4d-212">File Provider interfaces</span></span>

<span data-ttu-id="58c4d-213">A interface principal é <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="58c4d-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="58c4d-214">`IFileProvider` expõe métodos para:</span><span class="sxs-lookup"><span data-stu-id="58c4d-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="58c4d-215">Obter informações do arquivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="58c4d-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="58c4d-216">Obter informações do diretório (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="58c4d-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="58c4d-217">Configurar notificações de alteração (usando um <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="58c4d-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="58c4d-218">`IFileInfo` fornece métodos e propriedades para trabalhar com arquivos:</span><span class="sxs-lookup"><span data-stu-id="58c4d-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="58c4d-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (em bytes)</span><span class="sxs-lookup"><span data-stu-id="58c4d-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="58c4d-220">Data de <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="58c4d-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="58c4d-221">Você pode ler o arquivo usando o método [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="58c4d-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="58c4d-222">O aplicativo de amostra demonstra como configurar um provedor de arquivos em `Startup.ConfigureServices` para uso em todo o aplicativo por meio da [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58c4d-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="58c4d-223">Implementações do provedor de arquivos</span><span class="sxs-lookup"><span data-stu-id="58c4d-223">File Provider implementations</span></span>

<span data-ttu-id="58c4d-224">Três implementações de `IFileProvider` estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="58c4d-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="58c4d-225">Implementação</span><span class="sxs-lookup"><span data-stu-id="58c4d-225">Implementation</span></span> | <span data-ttu-id="58c4d-226">Descrição</span><span class="sxs-lookup"><span data-stu-id="58c4d-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="58c4d-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="58c4d-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="58c4d-228">O provedor físico é usado para acessar os arquivos físicos do sistema.</span><span class="sxs-lookup"><span data-stu-id="58c4d-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="58c4d-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="58c4d-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="58c4d-230">O provedor inserido de manifesto é usado para acessar arquivos incorporados em assemblies.</span><span class="sxs-lookup"><span data-stu-id="58c4d-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="58c4d-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="58c4d-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="58c4d-232">O provedor composto é usado para fornecer acesso combinado a arquivos e diretórios de um ou mais provedores.</span><span class="sxs-lookup"><span data-stu-id="58c4d-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="58c4d-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="58c4d-233">PhysicalFileProvider</span></span>

<span data-ttu-id="58c4d-234">O <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fornece acesso ao sistema de arquivos físico.</span><span class="sxs-lookup"><span data-stu-id="58c4d-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="58c4d-235">O `PhysicalFileProvider` usa o tipo <xref:System.IO.File?displayProperty=fullName> (para o provedor físico) e define o escopo de todos os caminhos para um diretório e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="58c4d-236">Esse escopo impede o acesso ao sistema de arquivos fora do diretório especificado e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="58c4d-237">O cenário mais comum para criar e usar um `PhysicalFileProvider` é solicitar um `IFileProvider` em um construtor por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58c4d-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="58c4d-238">Ao criar uma instância para esse provedor, um caminho de diretório é necessário e serve como o caminho base para todas as solicitações feitas usando o provedor.</span><span class="sxs-lookup"><span data-stu-id="58c4d-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="58c4d-239">O código a seguir mostra como criar um `PhysicalFileProvider` e usá-lo para obter o conteúdo do diretório e as informações do arquivo:</span><span class="sxs-lookup"><span data-stu-id="58c4d-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="58c4d-240">Tipos no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="58c4d-240">Types in the preceding example:</span></span>

* <span data-ttu-id="58c4d-241">`provider` é um `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="58c4d-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="58c4d-242">`contents` é um `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="58c4d-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="58c4d-243">`fileInfo` é um `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="58c4d-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="58c4d-244">O provedor de arquivos pode ser usado para percorrer o diretório especificado por `applicationRoot` ou chamar `GetFileInfo` para obter as informações de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="58c4d-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="58c4d-245">O provedor de arquivos não tem acesso fora do diretório `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="58c4d-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="58c4d-246">O aplicativo de amostra cria o provedor na classe `Startup.ConfigureServices` do aplicativo usando [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="58c4d-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="58c4d-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="58c4d-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="58c4d-248">O <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> é usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="58c4d-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="58c4d-249">O `ManifestEmbeddedFileProvider` usa um manifesto compilado no assembly para reconstruir os caminhos originais dos arquivos inseridos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="58c4d-250">Para gerar um manifesto dos arquivos inseridos, defina a propriedade `<GenerateEmbeddedFilesManifest>` como `true`.</span><span class="sxs-lookup"><span data-stu-id="58c4d-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="58c4d-251">Especifique os arquivos a serem inseridos com [ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="58c4d-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="58c4d-252">Use [padrões glob](#glob-patterns) para especificar um ou mais arquivos a serem inseridos no assembly.</span><span class="sxs-lookup"><span data-stu-id="58c4d-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="58c4d-253">O aplicativo de amostra cria um `ManifestEmbeddedFileProvider` e passa o assembly atualmente em execução para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="58c4d-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="58c4d-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="58c4d-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="58c4d-255">Sobrecargas adicionais permitem:</span><span class="sxs-lookup"><span data-stu-id="58c4d-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="58c4d-256">Especificar um caminho de arquivo relativo.</span><span class="sxs-lookup"><span data-stu-id="58c4d-256">Specify a relative file path.</span></span>
* <span data-ttu-id="58c4d-257">Delimitar os arquivos segundo a data da última modificação.</span><span class="sxs-lookup"><span data-stu-id="58c4d-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="58c4d-258">Nomear o recurso inserido que contém o manifesto do arquivo inserido.</span><span class="sxs-lookup"><span data-stu-id="58c4d-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="58c4d-259">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="58c4d-259">Overload</span></span> | <span data-ttu-id="58c4d-260">Descrição</span><span class="sxs-lookup"><span data-stu-id="58c4d-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="58c4d-261">Aceita um parâmetro de caminho relativo `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="58c4d-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="58c4d-262">Especifique o `root` para definir o escopo das chamadas de <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> para esses recursos no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="58c4d-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="58c4d-263">Aceita um parâmetro de caminho relativo `root` opcional e um parâmetro de data `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="58c4d-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="58c4d-264">A data `lastModified` tem como escopo a data da última modificação das instâncias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> retornadas por <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="58c4d-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="58c4d-265">Aceita um caminho relativo `root` opcional, data de `lastModified` e parâmetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="58c4d-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="58c4d-266">O `manifestName` representa o nome do recurso inserido que contém o manifesto.</span><span class="sxs-lookup"><span data-stu-id="58c4d-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="58c4d-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="58c4d-267">CompositeFileProvider</span></span>

<span data-ttu-id="58c4d-268">O <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instâncias de `IFileProvider`, expondo uma interface única para trabalhar com arquivos de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="58c4d-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="58c4d-269">Ao criar o `CompositeFileProvider`, passe uma ou mais instâncias de `IFileProvider` para o construtor.</span><span class="sxs-lookup"><span data-stu-id="58c4d-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="58c4d-270">No aplicativo de amostra, um `PhysicalFileProvider` e um `ManifestEmbeddedFileProvider` fornecem arquivos para um `CompositeFileProvider` registrado no contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="58c4d-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="58c4d-271">Monitorar as alterações</span><span class="sxs-lookup"><span data-stu-id="58c4d-271">Watch for changes</span></span>

<span data-ttu-id="58c4d-272">O método [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) proporciona um cenário para monitorar um ou mais arquivos ou diretórios quanto a alterações.</span><span class="sxs-lookup"><span data-stu-id="58c4d-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="58c4d-273">`Watch` aceita uma cadeia de caracteres de caminho, que pode usar [padrões glob](#glob-patterns) para especificar vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="58c4d-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="58c4d-274">`Watch` retorna um <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="58c4d-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="58c4d-275">O token de alteração expõe:</span><span class="sxs-lookup"><span data-stu-id="58c4d-275">The change token exposes:</span></span>

* <span data-ttu-id="58c4d-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Uma propriedade que pode ser inspecionada para determinar se ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="58c4d-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="58c4d-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Chamado quando são detectadas alterações na cadeia de caracteres do caminho especificado.</span><span class="sxs-lookup"><span data-stu-id="58c4d-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="58c4d-278">Cada token de alteração chama apenas seu retorno de chamada associado em resposta a uma única alteração.</span><span class="sxs-lookup"><span data-stu-id="58c4d-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="58c4d-279">Para permitir o monitoramento constante, use um <xref:System.Threading.Tasks.TaskCompletionSource`1> (mostrado abaixo) ou recrie instâncias de `IChangeToken` em resposta a alterações.</span><span class="sxs-lookup"><span data-stu-id="58c4d-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="58c4d-280">No aplicativo de amostra, o aplicativo de console *WatchConsole* é configurado para exibir uma mensagem sempre que um arquivo de texto é modificado:</span><span class="sxs-lookup"><span data-stu-id="58c4d-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="58c4d-281">Alguns sistemas de arquivos, como contêineres do Docker e compartilhamentos de rede, podem não enviar notificações de alteração de forma confiável.</span><span class="sxs-lookup"><span data-stu-id="58c4d-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="58c4d-282">Defina a variável de ambiente `DOTNET_USE_POLLING_FILE_WATCHER` como `1` ou `true` para sondar o sistema de arquivos a cada quatro segundos em relação a alterações.</span><span class="sxs-lookup"><span data-stu-id="58c4d-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="58c4d-283">Padrões glob</span><span class="sxs-lookup"><span data-stu-id="58c4d-283">Glob patterns</span></span>

<span data-ttu-id="58c4d-284">Os caminhos do sistema de arquivos usam padrões curinga chamados *padrões glob (ou globbing)*.</span><span class="sxs-lookup"><span data-stu-id="58c4d-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="58c4d-285">Especifique grupos de arquivos com esses padrões.</span><span class="sxs-lookup"><span data-stu-id="58c4d-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="58c4d-286">Os dois caracteres curinga são `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="58c4d-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="58c4d-287">Corresponde a qualquer coisa no nível da pasta atual, qualquer nome de arquivo ou qualquer extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="58c4d-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="58c4d-288">As correspondências são terminadas pelos caracteres `/` e `.` no caminho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="58c4d-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="58c4d-289">Coincide a qualquer coisa em vários níveis de diretório.</span><span class="sxs-lookup"><span data-stu-id="58c4d-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="58c4d-290">Pode ser usada para fazer a correspondência recursiva com vários arquivos em uma hierarquia de diretórios.</span><span class="sxs-lookup"><span data-stu-id="58c4d-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="58c4d-291">**Exemplos de padrão glob**</span><span class="sxs-lookup"><span data-stu-id="58c4d-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="58c4d-292">Corresponde a um arquivo específico em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="58c4d-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="58c4d-293">Corresponde a todos os arquivos com a extensão *.txt* em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="58c4d-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="58c4d-294">Corresponde a todos os arquivos `appsettings.json` em diretórios que estão exatamente um nível abaixo da pasta *diretório*.</span><span class="sxs-lookup"><span data-stu-id="58c4d-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="58c4d-295">Corresponde a todos os arquivos com a extensão *.txt* encontrados em qualquer lugar abaixo da pasta *diretório*.</span><span class="sxs-lookup"><span data-stu-id="58c4d-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
