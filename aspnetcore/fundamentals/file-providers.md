---
<span data-ttu-id="42d93-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-102">'Blazor'</span></span>
- <span data-ttu-id="42d93-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-103">'Identity'</span></span>
- <span data-ttu-id="42d93-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-105">'Razor'</span></span>
- <span data-ttu-id="42d93-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-106">'SignalR' uid:</span></span> 

---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="42d93-107">Provedores de arquivos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42d93-107">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="42d93-108">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="42d93-108">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42d93-109">O ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.</span><span class="sxs-lookup"><span data-stu-id="42d93-109">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="42d93-110">Os provedores de arquivos são usados em todo o ASP.NET Core Framework.</span><span class="sxs-lookup"><span data-stu-id="42d93-110">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="42d93-111">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="42d93-111">For example:</span></span>

* <span data-ttu-id="42d93-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>expõe a raiz do [conteúdo](xref:fundamentals/index#content-root) do aplicativo e a [raiz da Web](xref:fundamentals/index#web-root) como `IFileProvider` tipos.</span><span class="sxs-lookup"><span data-stu-id="42d93-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="42d93-113">O [middleware de arquivos estáticos](xref:fundamentals/static-files) usa provedores de arquivos para localizar arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="42d93-113">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="42d93-114">[Razor](xref:mvc/views/razor)usa provedores de arquivos para localizar páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="42d93-114">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="42d93-115">As ferramentas do .NET Core usam provedores de arquivos e padrões glob para especificar quais arquivos devem ser publicados.</span><span class="sxs-lookup"><span data-stu-id="42d93-115">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="42d93-116">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42d93-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="42d93-117">Interfaces de provedor de arquivo</span><span class="sxs-lookup"><span data-stu-id="42d93-117">File Provider interfaces</span></span>

<span data-ttu-id="42d93-118">A interface principal é <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="42d93-118">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="42d93-119">`IFileProvider` expõe métodos para:</span><span class="sxs-lookup"><span data-stu-id="42d93-119">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="42d93-120">Obter informações do arquivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="42d93-120">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="42d93-121">Obter informações do diretório (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="42d93-121">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="42d93-122">Configurar notificações de alteração (usando um <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="42d93-122">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="42d93-123">`IFileInfo` fornece métodos e propriedades para trabalhar com arquivos:</span><span class="sxs-lookup"><span data-stu-id="42d93-123">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="42d93-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (em bytes)</span><span class="sxs-lookup"><span data-stu-id="42d93-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="42d93-125">Data de <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="42d93-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="42d93-126">Você pode ler o arquivo usando o <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> método.</span><span class="sxs-lookup"><span data-stu-id="42d93-126">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="42d93-127">O aplicativo de exemplo *FileProviderSample* demonstra como configurar um provedor de arquivos no `Startup.ConfigureServices` para uso em todo o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="42d93-127">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="42d93-128">Implementações do provedor de arquivos</span><span class="sxs-lookup"><span data-stu-id="42d93-128">File Provider implementations</span></span>

<span data-ttu-id="42d93-129">A tabela a seguir lista as implementações do `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="42d93-129">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="42d93-130">Implementação</span><span class="sxs-lookup"><span data-stu-id="42d93-130">Implementation</span></span> | <span data-ttu-id="42d93-131">Descrição</span><span class="sxs-lookup"><span data-stu-id="42d93-131">Description</span></span> |
| ---
<span data-ttu-id="42d93-132">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-133">'Blazor'</span></span>
- <span data-ttu-id="42d93-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-134">'Identity'</span></span>
- <span data-ttu-id="42d93-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-136">'Razor'</span></span>
- <span data-ttu-id="42d93-137">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-138">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-139">'Blazor'</span></span>
- <span data-ttu-id="42d93-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-140">'Identity'</span></span>
- <span data-ttu-id="42d93-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-142">'Razor'</span></span>
- <span data-ttu-id="42d93-143">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-144">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-145">'Blazor'</span></span>
- <span data-ttu-id="42d93-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-146">'Identity'</span></span>
- <span data-ttu-id="42d93-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-148">'Razor'</span></span>
- <span data-ttu-id="42d93-149">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-150">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-151">'Blazor'</span></span>
- <span data-ttu-id="42d93-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-152">'Identity'</span></span>
- <span data-ttu-id="42d93-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-154">'Razor'</span></span>
- <span data-ttu-id="42d93-155">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-156">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-157">'Blazor'</span></span>
- <span data-ttu-id="42d93-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-158">'Identity'</span></span>
- <span data-ttu-id="42d93-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-160">'Razor'</span></span>
- <span data-ttu-id="42d93-161">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-161">'SignalR' uid:</span></span> 

<span data-ttu-id="42d93-162">------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-163">'Blazor'</span></span>
- <span data-ttu-id="42d93-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-164">'Identity'</span></span>
- <span data-ttu-id="42d93-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-166">'Razor'</span></span>
- <span data-ttu-id="42d93-167">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-168">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-169">'Blazor'</span></span>
- <span data-ttu-id="42d93-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-170">'Identity'</span></span>
- <span data-ttu-id="42d93-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-172">'Razor'</span></span>
- <span data-ttu-id="42d93-173">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-174">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-175">'Blazor'</span></span>
- <span data-ttu-id="42d93-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-176">'Identity'</span></span>
- <span data-ttu-id="42d93-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-178">'Razor'</span></span>
- <span data-ttu-id="42d93-179">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-179">'SignalR' uid:</span></span> 

<span data-ttu-id="42d93-180">------ | | [CompositeFileProvider](#compositefileprovider) | Usado para fornecer acesso combinado a arquivos e diretórios de um ou mais outros provedores.</span><span class="sxs-lookup"><span data-stu-id="42d93-180">------ | | [CompositeFileProvider](#compositefileprovider) | Used to provide combined access to files and directories from one or more other providers.</span></span> <span data-ttu-id="42d93-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="42d93-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Used to access files embedded in assemblies.</span></span> <span data-ttu-id="42d93-182">| | [PhysicalFileProvider](#physicalfileprovider) | Usado para acessar os arquivos físicos do sistema.</span><span class="sxs-lookup"><span data-stu-id="42d93-182">| | [PhysicalFileProvider](#physicalfileprovider) | Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="42d93-183">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="42d93-183">PhysicalFileProvider</span></span>

<span data-ttu-id="42d93-184">O <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fornece acesso ao sistema de arquivos físico.</span><span class="sxs-lookup"><span data-stu-id="42d93-184">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="42d93-185">O `PhysicalFileProvider` usa o tipo <xref:System.IO.File?displayProperty=fullName> (para o provedor físico) e define o escopo de todos os caminhos para um diretório e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="42d93-185">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="42d93-186">Esse escopo impede o acesso ao sistema de arquivos fora do diretório especificado e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="42d93-186">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="42d93-187">O cenário mais comum para criar e usar um `PhysicalFileProvider` é solicitar um `IFileProvider` em um construtor por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="42d93-187">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="42d93-188">Ao instanciar esse provedor diretamente, um caminho de diretório absoluto é necessário e serve como o caminho base para todas as solicitações feitas usando o provedor.</span><span class="sxs-lookup"><span data-stu-id="42d93-188">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="42d93-189">Não há suporte para padrões glob no caminho do diretório.</span><span class="sxs-lookup"><span data-stu-id="42d93-189">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="42d93-190">O código a seguir mostra como usar o `PhysicalFileProvider` para obter o conteúdo do diretório e as informações do arquivo:</span><span class="sxs-lookup"><span data-stu-id="42d93-190">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="42d93-191">Tipos no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="42d93-191">Types in the preceding example:</span></span>

* <span data-ttu-id="42d93-192">`provider` é um `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="42d93-192">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="42d93-193">`contents` é um `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="42d93-193">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="42d93-194">`fileInfo` é um `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="42d93-194">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="42d93-195">O provedor de arquivos pode ser usado para percorrer o diretório especificado por `applicationRoot` ou chamar `GetFileInfo` para obter as informações de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="42d93-195">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="42d93-196">Padrões de glob não podem ser passados para o `GetFileInfo` método.</span><span class="sxs-lookup"><span data-stu-id="42d93-196">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="42d93-197">O provedor de arquivos não tem acesso fora do diretório `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="42d93-197">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="42d93-198">O aplicativo de exemplo *FileProviderSample* cria o provedor no `Startup.ConfigureServices` método usando <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="42d93-198">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="42d93-199">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="42d93-199">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="42d93-200">O <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> é usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="42d93-200">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="42d93-201">O `ManifestEmbeddedFileProvider` usa um manifesto compilado no assembly para reconstruir os caminhos originais dos arquivos inseridos.</span><span class="sxs-lookup"><span data-stu-id="42d93-201">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="42d93-202">Para gerar um manifesto dos arquivos inseridos:</span><span class="sxs-lookup"><span data-stu-id="42d93-202">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="42d93-203">Adicione o pacote NuGet [Microsoft. Extensions.](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) . Embedded ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="42d93-203">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="42d93-204">Defina a propriedade `<GenerateEmbeddedFilesManifest>` como `true`.</span><span class="sxs-lookup"><span data-stu-id="42d93-204">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="42d93-205">Especifique os arquivos a serem inseridos [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :</span><span class="sxs-lookup"><span data-stu-id="42d93-205">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="42d93-206">Use [padrões glob](#glob-patterns) para especificar um ou mais arquivos a serem inseridos no assembly.</span><span class="sxs-lookup"><span data-stu-id="42d93-206">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="42d93-207">O aplicativo de exemplo *FileProviderSample* cria um `ManifestEmbeddedFileProvider` e passa o assembly atualmente em execução para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="42d93-207">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="42d93-208">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="42d93-208">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="42d93-209">Sobrecargas adicionais permitem:</span><span class="sxs-lookup"><span data-stu-id="42d93-209">Additional overloads allow you to:</span></span>

* <span data-ttu-id="42d93-210">Especificar um caminho de arquivo relativo.</span><span class="sxs-lookup"><span data-stu-id="42d93-210">Specify a relative file path.</span></span>
* <span data-ttu-id="42d93-211">Delimitar os arquivos segundo a data da última modificação.</span><span class="sxs-lookup"><span data-stu-id="42d93-211">Scope files to a last modified date.</span></span>
* <span data-ttu-id="42d93-212">Nomear o recurso inserido que contém o manifesto do arquivo inserido.</span><span class="sxs-lookup"><span data-stu-id="42d93-212">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="42d93-213">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="42d93-213">Overload</span></span> | <span data-ttu-id="42d93-214">Descrição</span><span class="sxs-lookup"><span data-stu-id="42d93-214">Description</span></span> |
| ---
<span data-ttu-id="42d93-215">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-216">'Blazor'</span></span>
- <span data-ttu-id="42d93-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-217">'Identity'</span></span>
- <span data-ttu-id="42d93-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-219">'Razor'</span></span>
- <span data-ttu-id="42d93-220">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-221">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-222">'Blazor'</span></span>
- <span data-ttu-id="42d93-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-223">'Identity'</span></span>
- <span data-ttu-id="42d93-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-225">'Razor'</span></span>
- <span data-ttu-id="42d93-226">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-226">'SignalR' uid:</span></span> 

<span data-ttu-id="42d93-227">---- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-228">'Blazor'</span></span>
- <span data-ttu-id="42d93-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-229">'Identity'</span></span>
- <span data-ttu-id="42d93-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-231">'Razor'</span></span>
- <span data-ttu-id="42d93-232">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-233">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-234">'Blazor'</span></span>
- <span data-ttu-id="42d93-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-235">'Identity'</span></span>
- <span data-ttu-id="42d93-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-237">'Razor'</span></span>
- <span data-ttu-id="42d93-238">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-239">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-240">'Blazor'</span></span>
- <span data-ttu-id="42d93-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-241">'Identity'</span></span>
- <span data-ttu-id="42d93-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-243">'Razor'</span></span>
- <span data-ttu-id="42d93-244">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-244">'SignalR' uid:</span></span> 

<span data-ttu-id="42d93-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Aceita um `root` parâmetro de caminho relativo opcional.</span><span class="sxs-lookup"><span data-stu-id="42d93-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="42d93-246">Especifique o `root` para definir o escopo das chamadas de <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> para esses recursos no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="42d93-246">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="42d93-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Aceita um `root` parâmetro de caminho relativo opcional e `lastModified` um parâmetro date ( <xref:System.DateTimeOffset> ).</span><span class="sxs-lookup"><span data-stu-id="42d93-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="42d93-248">A data `lastModified` tem como escopo a data da última modificação das instâncias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> retornadas por <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="42d93-248">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="42d93-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Aceita um `root` caminho relativo opcional, uma `lastModified` data e `manifestName` parâmetros.</span><span class="sxs-lookup"><span data-stu-id="42d93-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="42d93-250">O `manifestName` representa o nome do recurso inserido que contém o manifesto.</span><span class="sxs-lookup"><span data-stu-id="42d93-250">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="42d93-251">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="42d93-251">CompositeFileProvider</span></span>

<span data-ttu-id="42d93-252">O <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instâncias de `IFileProvider`, expondo uma interface única para trabalhar com arquivos de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="42d93-252">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="42d93-253">Ao criar o `CompositeFileProvider`, passe uma ou mais instâncias de `IFileProvider` para o construtor.</span><span class="sxs-lookup"><span data-stu-id="42d93-253">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="42d93-254">No aplicativo de exemplo *FileProviderSample* , um `PhysicalFileProvider` e um `ManifestEmbeddedFileProvider` fornecem arquivos para um `CompositeFileProvider` registrado no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="42d93-254">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="42d93-255">O código a seguir é encontrado no método do projeto `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="42d93-255">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="42d93-256">Monitorar as alterações</span><span class="sxs-lookup"><span data-stu-id="42d93-256">Watch for changes</span></span>

<span data-ttu-id="42d93-257">O <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> método fornece um cenário para assistir a um ou mais arquivos ou diretórios para alterações.</span><span class="sxs-lookup"><span data-stu-id="42d93-257">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="42d93-258">O método `Watch`:</span><span class="sxs-lookup"><span data-stu-id="42d93-258">The `Watch` method:</span></span>

* <span data-ttu-id="42d93-259">Aceita uma cadeia de caracteres de caminho de arquivo, que pode usar [padrões glob](#glob-patterns) para especificar vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="42d93-259">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="42d93-260">Retorna um <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="42d93-260">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="42d93-261">O token de alteração resultante expõe:</span><span class="sxs-lookup"><span data-stu-id="42d93-261">The resulting change token exposes:</span></span>

* <span data-ttu-id="42d93-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Uma propriedade que pode ser inspecionada para determinar se ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="42d93-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="42d93-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Chamado quando são detectadas alterações na cadeia de caracteres do caminho especificado.</span><span class="sxs-lookup"><span data-stu-id="42d93-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="42d93-264">Cada token de alteração chama apenas seu retorno de chamada associado em resposta a uma única alteração.</span><span class="sxs-lookup"><span data-stu-id="42d93-264">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="42d93-265">Para permitir o monitoramento constante, use um <xref:System.Threading.Tasks.TaskCompletionSource`1> (mostrado abaixo) ou recrie instâncias de `IChangeToken` em resposta a alterações.</span><span class="sxs-lookup"><span data-stu-id="42d93-265">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="42d93-266">O aplicativo de exemplo *WatchConsole* grava uma mensagem sempre que um arquivo *. txt* no diretório *textfiles* é modificado:</span><span class="sxs-lookup"><span data-stu-id="42d93-266">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="42d93-267">Alguns sistemas de arquivos, como contêineres do Docker e compartilhamentos de rede, podem não enviar notificações de alteração de forma confiável.</span><span class="sxs-lookup"><span data-stu-id="42d93-267">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="42d93-268">Defina a variável de ambiente `DOTNET_USE_POLLING_FILE_WATCHER` como `1` ou `true` para sondar o sistema de arquivos a cada quatro segundos em relação a alterações.</span><span class="sxs-lookup"><span data-stu-id="42d93-268">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="42d93-269">Padrões glob</span><span class="sxs-lookup"><span data-stu-id="42d93-269">Glob patterns</span></span>

<span data-ttu-id="42d93-270">Os caminhos do sistema de arquivos usam padrões curinga chamados *padrões glob (ou globbing)*.</span><span class="sxs-lookup"><span data-stu-id="42d93-270">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="42d93-271">Especifique grupos de arquivos com esses padrões.</span><span class="sxs-lookup"><span data-stu-id="42d93-271">Specify groups of files with these patterns.</span></span> <span data-ttu-id="42d93-272">Os dois caracteres curinga são `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="42d93-272">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="42d93-273">Corresponde a qualquer coisa no nível da pasta atual, qualquer nome de arquivo ou qualquer extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="42d93-273">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="42d93-274">As correspondências são terminadas pelos caracteres `/` e `.` no caminho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="42d93-274">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="42d93-275">Coincide a qualquer coisa em vários níveis de diretório.</span><span class="sxs-lookup"><span data-stu-id="42d93-275">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="42d93-276">Pode ser usada para fazer a correspondência recursiva com vários arquivos em uma hierarquia de diretórios.</span><span class="sxs-lookup"><span data-stu-id="42d93-276">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="42d93-277">A tabela a seguir fornece exemplos comuns de padrões de glob.</span><span class="sxs-lookup"><span data-stu-id="42d93-277">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="42d93-278">Padrão</span><span class="sxs-lookup"><span data-stu-id="42d93-278">Pattern</span></span>  |<span data-ttu-id="42d93-279">Descrição</span><span class="sxs-lookup"><span data-stu-id="42d93-279">Description</span></span>  |
|---
<span data-ttu-id="42d93-280">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-281">'Blazor'</span></span>
- <span data-ttu-id="42d93-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-282">'Identity'</span></span>
- <span data-ttu-id="42d93-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-284">'Razor'</span></span>
- <span data-ttu-id="42d93-285">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-286">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-287">'Blazor'</span></span>
- <span data-ttu-id="42d93-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-288">'Identity'</span></span>
- <span data-ttu-id="42d93-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-290">'Razor'</span></span>
- <span data-ttu-id="42d93-291">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-291">'SignalR' uid:</span></span> 

<span data-ttu-id="42d93-292">-----|---
Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-293">'Blazor'</span></span>
- <span data-ttu-id="42d93-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-294">'Identity'</span></span>
- <span data-ttu-id="42d93-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-296">'Razor'</span></span>
- <span data-ttu-id="42d93-297">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-298">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-299">'Blazor'</span></span>
- <span data-ttu-id="42d93-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-300">'Identity'</span></span>
- <span data-ttu-id="42d93-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-302">'Razor'</span></span>
- <span data-ttu-id="42d93-303">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-303">'SignalR' uid:</span></span> 

<span data-ttu-id="42d93-304">-----|
|`directory/file.txt`| Corresponde a um arquivo específico em um diretório específico. | |`directory/*.txt`| Corresponde a todos os arquivos com a extensão *. txt* em um diretório específico. | |`directory/*/appsettings.json`| Corresponde a todos os arquivos *appSettings. JSON* em diretórios exatamente um nível abaixo da pasta do *diretório* . | |`directory/**/*.txt`| Corresponde a todos os arquivos com uma extensão *. txt* encontrado em qualquer lugar na pasta do *diretório* . |</span><span class="sxs-lookup"><span data-stu-id="42d93-304">-----|
|`directory/file.txt`|Matches a specific file in a specific directory.| |`directory/*.txt`|Matches all files with *.txt* extension in a specific directory.| |`directory/*/appsettings.json`|Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.| |`directory/**/*.txt`|Matches all files with a *.txt* extension found anywhere under the *directory* folder.|</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="42d93-305">O ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.</span><span class="sxs-lookup"><span data-stu-id="42d93-305">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="42d93-306">Os provedores de arquivos são usados ​​em toda a estrutura do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="42d93-306">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="42d93-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>expõe a raiz do [conteúdo](xref:fundamentals/index#content-root) do aplicativo e a [raiz da Web](xref:fundamentals/index#web-root) como `IFileProvider` tipos.</span><span class="sxs-lookup"><span data-stu-id="42d93-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="42d93-308">O [middleware de arquivos estáticos](xref:fundamentals/static-files) usa provedores de arquivos para localizar arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="42d93-308">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="42d93-309">[Razor](xref:mvc/views/razor)usa provedores de arquivos para localizar páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="42d93-309">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="42d93-310">As ferramentas do .NET Core usam provedores de arquivos e padrões glob para especificar quais arquivos devem ser publicados.</span><span class="sxs-lookup"><span data-stu-id="42d93-310">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="42d93-311">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42d93-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="42d93-312">Interfaces de provedor de arquivo</span><span class="sxs-lookup"><span data-stu-id="42d93-312">File Provider interfaces</span></span>

<span data-ttu-id="42d93-313">A interface principal é <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="42d93-313">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="42d93-314">`IFileProvider` expõe métodos para:</span><span class="sxs-lookup"><span data-stu-id="42d93-314">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="42d93-315">Obter informações do arquivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="42d93-315">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="42d93-316">Obter informações do diretório (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="42d93-316">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="42d93-317">Configurar notificações de alteração (usando um <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="42d93-317">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="42d93-318">`IFileInfo` fornece métodos e propriedades para trabalhar com arquivos:</span><span class="sxs-lookup"><span data-stu-id="42d93-318">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="42d93-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (em bytes)</span><span class="sxs-lookup"><span data-stu-id="42d93-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="42d93-320">Data de <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="42d93-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="42d93-321">Você pode ler o arquivo usando o método [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="42d93-321">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="42d93-322">O aplicativo de amostra demonstra como configurar um provedor de arquivos em `Startup.ConfigureServices` para uso em todo o aplicativo por meio da [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="42d93-322">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="42d93-323">Implementações do provedor de arquivos</span><span class="sxs-lookup"><span data-stu-id="42d93-323">File Provider implementations</span></span>

<span data-ttu-id="42d93-324">Três implementações de `IFileProvider` estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="42d93-324">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="42d93-325">Implementação</span><span class="sxs-lookup"><span data-stu-id="42d93-325">Implementation</span></span> | <span data-ttu-id="42d93-326">Descrição</span><span class="sxs-lookup"><span data-stu-id="42d93-326">Description</span></span> |
| ---
<span data-ttu-id="42d93-327">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-328">'Blazor'</span></span>
- <span data-ttu-id="42d93-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-329">'Identity'</span></span>
- <span data-ttu-id="42d93-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-331">'Razor'</span></span>
- <span data-ttu-id="42d93-332">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-333">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-334">'Blazor'</span></span>
- <span data-ttu-id="42d93-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-335">'Identity'</span></span>
- <span data-ttu-id="42d93-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-337">'Razor'</span></span>
- <span data-ttu-id="42d93-338">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-339">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-340">'Blazor'</span></span>
- <span data-ttu-id="42d93-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-341">'Identity'</span></span>
- <span data-ttu-id="42d93-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-343">'Razor'</span></span>
- <span data-ttu-id="42d93-344">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-345">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-346">'Blazor'</span></span>
- <span data-ttu-id="42d93-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-347">'Identity'</span></span>
- <span data-ttu-id="42d93-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-349">'Razor'</span></span>
- <span data-ttu-id="42d93-350">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-351">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-352">'Blazor'</span></span>
- <span data-ttu-id="42d93-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-353">'Identity'</span></span>
- <span data-ttu-id="42d93-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-355">'Razor'</span></span>
- <span data-ttu-id="42d93-356">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-356">'SignalR' uid:</span></span> 

<span data-ttu-id="42d93-357">------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-358">'Blazor'</span></span>
- <span data-ttu-id="42d93-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-359">'Identity'</span></span>
- <span data-ttu-id="42d93-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-361">'Razor'</span></span>
- <span data-ttu-id="42d93-362">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-363">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-364">'Blazor'</span></span>
- <span data-ttu-id="42d93-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-365">'Identity'</span></span>
- <span data-ttu-id="42d93-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-367">'Razor'</span></span>
- <span data-ttu-id="42d93-368">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-369">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-370">'Blazor'</span></span>
- <span data-ttu-id="42d93-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-371">'Identity'</span></span>
- <span data-ttu-id="42d93-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-373">'Razor'</span></span>
- <span data-ttu-id="42d93-374">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-374">'SignalR' uid:</span></span> 

<span data-ttu-id="42d93-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | O provedor físico é usado para acessar os arquivos físicos do sistema.</span><span class="sxs-lookup"><span data-stu-id="42d93-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files.</span></span> <span data-ttu-id="42d93-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | O provedor inserido do manifesto é usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="42d93-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies.</span></span> <span data-ttu-id="42d93-377">| | [CompositeFileProvider](#compositefileprovider) | O provedor composto é usado para fornecer acesso combinado a arquivos e diretórios de um ou mais outros provedores.</span><span class="sxs-lookup"><span data-stu-id="42d93-377">| | [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="42d93-378">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="42d93-378">PhysicalFileProvider</span></span>

<span data-ttu-id="42d93-379">O <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fornece acesso ao sistema de arquivos físico.</span><span class="sxs-lookup"><span data-stu-id="42d93-379">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="42d93-380">O `PhysicalFileProvider` usa o tipo <xref:System.IO.File?displayProperty=fullName> (para o provedor físico) e define o escopo de todos os caminhos para um diretório e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="42d93-380">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="42d93-381">Esse escopo impede o acesso ao sistema de arquivos fora do diretório especificado e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="42d93-381">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="42d93-382">O cenário mais comum para criar e usar um `PhysicalFileProvider` é solicitar um `IFileProvider` em um construtor por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="42d93-382">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="42d93-383">Ao criar uma instância para esse provedor, um caminho de diretório é necessário e serve como o caminho base para todas as solicitações feitas usando o provedor.</span><span class="sxs-lookup"><span data-stu-id="42d93-383">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="42d93-384">O código a seguir mostra como criar um `PhysicalFileProvider` e usá-lo para obter o conteúdo do diretório e as informações do arquivo:</span><span class="sxs-lookup"><span data-stu-id="42d93-384">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="42d93-385">Tipos no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="42d93-385">Types in the preceding example:</span></span>

* <span data-ttu-id="42d93-386">`provider` é um `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="42d93-386">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="42d93-387">`contents` é um `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="42d93-387">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="42d93-388">`fileInfo` é um `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="42d93-388">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="42d93-389">O provedor de arquivos pode ser usado para percorrer o diretório especificado por `applicationRoot` ou chamar `GetFileInfo` para obter as informações de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="42d93-389">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="42d93-390">O provedor de arquivos não tem acesso fora do diretório `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="42d93-390">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="42d93-391">O aplicativo de amostra cria o provedor na classe `Startup.ConfigureServices` do aplicativo usando [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="42d93-391">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="42d93-392">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="42d93-392">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="42d93-393">O <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> é usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="42d93-393">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="42d93-394">O `ManifestEmbeddedFileProvider` usa um manifesto compilado no assembly para reconstruir os caminhos originais dos arquivos inseridos.</span><span class="sxs-lookup"><span data-stu-id="42d93-394">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="42d93-395">Para gerar um manifesto dos arquivos inseridos, defina a propriedade `<GenerateEmbeddedFilesManifest>` como `true`.</span><span class="sxs-lookup"><span data-stu-id="42d93-395">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="42d93-396">Especifique os arquivos a serem inseridos com [ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="42d93-396">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="42d93-397">Use [padrões glob](#glob-patterns) para especificar um ou mais arquivos a serem inseridos no assembly.</span><span class="sxs-lookup"><span data-stu-id="42d93-397">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="42d93-398">O aplicativo de amostra cria um `ManifestEmbeddedFileProvider` e passa o assembly atualmente em execução para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="42d93-398">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="42d93-399">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="42d93-399">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="42d93-400">Sobrecargas adicionais permitem:</span><span class="sxs-lookup"><span data-stu-id="42d93-400">Additional overloads allow you to:</span></span>

* <span data-ttu-id="42d93-401">Especificar um caminho de arquivo relativo.</span><span class="sxs-lookup"><span data-stu-id="42d93-401">Specify a relative file path.</span></span>
* <span data-ttu-id="42d93-402">Delimitar os arquivos segundo a data da última modificação.</span><span class="sxs-lookup"><span data-stu-id="42d93-402">Scope files to a last modified date.</span></span>
* <span data-ttu-id="42d93-403">Nomear o recurso inserido que contém o manifesto do arquivo inserido.</span><span class="sxs-lookup"><span data-stu-id="42d93-403">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="42d93-404">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="42d93-404">Overload</span></span> | <span data-ttu-id="42d93-405">Descrição</span><span class="sxs-lookup"><span data-stu-id="42d93-405">Description</span></span> |
| ---
<span data-ttu-id="42d93-406">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-407">'Blazor'</span></span>
- <span data-ttu-id="42d93-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-408">'Identity'</span></span>
- <span data-ttu-id="42d93-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-410">'Razor'</span></span>
- <span data-ttu-id="42d93-411">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-412">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-413">'Blazor'</span></span>
- <span data-ttu-id="42d93-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-414">'Identity'</span></span>
- <span data-ttu-id="42d93-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-416">'Razor'</span></span>
- <span data-ttu-id="42d93-417">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-417">'SignalR' uid:</span></span> 

<span data-ttu-id="42d93-418">---- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-419">'Blazor'</span></span>
- <span data-ttu-id="42d93-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-420">'Identity'</span></span>
- <span data-ttu-id="42d93-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-422">'Razor'</span></span>
- <span data-ttu-id="42d93-423">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-424">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-425">'Blazor'</span></span>
- <span data-ttu-id="42d93-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-426">'Identity'</span></span>
- <span data-ttu-id="42d93-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-428">'Razor'</span></span>
- <span data-ttu-id="42d93-429">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42d93-430">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="42d93-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42d93-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42d93-431">'Blazor'</span></span>
- <span data-ttu-id="42d93-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42d93-432">'Identity'</span></span>
- <span data-ttu-id="42d93-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42d93-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="42d93-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42d93-434">'Razor'</span></span>
- <span data-ttu-id="42d93-435">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="42d93-435">'SignalR' uid:</span></span> 

<span data-ttu-id="42d93-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Aceita um `root` parâmetro de caminho relativo opcional.</span><span class="sxs-lookup"><span data-stu-id="42d93-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="42d93-437">Especifique o `root` para definir o escopo das chamadas de <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> para esses recursos no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="42d93-437">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="42d93-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Aceita um `root` parâmetro de caminho relativo opcional e `lastModified` um parâmetro date ( <xref:System.DateTimeOffset> ).</span><span class="sxs-lookup"><span data-stu-id="42d93-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="42d93-439">A data `lastModified` tem como escopo a data da última modificação das instâncias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> retornadas por <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="42d93-439">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="42d93-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Aceita um `root` caminho relativo opcional, uma `lastModified` data e `manifestName` parâmetros.</span><span class="sxs-lookup"><span data-stu-id="42d93-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="42d93-441">O `manifestName` representa o nome do recurso inserido que contém o manifesto.</span><span class="sxs-lookup"><span data-stu-id="42d93-441">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="42d93-442">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="42d93-442">CompositeFileProvider</span></span>

<span data-ttu-id="42d93-443">O <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instâncias de `IFileProvider`, expondo uma interface única para trabalhar com arquivos de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="42d93-443">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="42d93-444">Ao criar o `CompositeFileProvider`, passe uma ou mais instâncias de `IFileProvider` para o construtor.</span><span class="sxs-lookup"><span data-stu-id="42d93-444">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="42d93-445">No aplicativo de amostra, um `PhysicalFileProvider` e um `ManifestEmbeddedFileProvider` fornecem arquivos para um `CompositeFileProvider` registrado no contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="42d93-445">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="42d93-446">Monitorar as alterações</span><span class="sxs-lookup"><span data-stu-id="42d93-446">Watch for changes</span></span>

<span data-ttu-id="42d93-447">O método [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) proporciona um cenário para monitorar um ou mais arquivos ou diretórios quanto a alterações.</span><span class="sxs-lookup"><span data-stu-id="42d93-447">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="42d93-448">`Watch` aceita uma cadeia de caracteres de caminho, que pode usar [padrões glob](#glob-patterns) para especificar vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="42d93-448">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="42d93-449">`Watch` retorna um <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="42d93-449">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="42d93-450">O token de alteração expõe:</span><span class="sxs-lookup"><span data-stu-id="42d93-450">The change token exposes:</span></span>

* <span data-ttu-id="42d93-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Uma propriedade que pode ser inspecionada para determinar se ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="42d93-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="42d93-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Chamado quando são detectadas alterações na cadeia de caracteres do caminho especificado.</span><span class="sxs-lookup"><span data-stu-id="42d93-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="42d93-453">Cada token de alteração chama apenas seu retorno de chamada associado em resposta a uma única alteração.</span><span class="sxs-lookup"><span data-stu-id="42d93-453">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="42d93-454">Para permitir o monitoramento constante, use um <xref:System.Threading.Tasks.TaskCompletionSource`1> (mostrado abaixo) ou recrie instâncias de `IChangeToken` em resposta a alterações.</span><span class="sxs-lookup"><span data-stu-id="42d93-454">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="42d93-455">No aplicativo de amostra, o aplicativo de console *WatchConsole* é configurado para exibir uma mensagem sempre que um arquivo de texto é modificado:</span><span class="sxs-lookup"><span data-stu-id="42d93-455">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="42d93-456">Alguns sistemas de arquivos, como contêineres do Docker e compartilhamentos de rede, podem não enviar notificações de alteração de forma confiável.</span><span class="sxs-lookup"><span data-stu-id="42d93-456">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="42d93-457">Defina a variável de ambiente `DOTNET_USE_POLLING_FILE_WATCHER` como `1` ou `true` para sondar o sistema de arquivos a cada quatro segundos em relação a alterações.</span><span class="sxs-lookup"><span data-stu-id="42d93-457">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="42d93-458">Padrões glob</span><span class="sxs-lookup"><span data-stu-id="42d93-458">Glob patterns</span></span>

<span data-ttu-id="42d93-459">Os caminhos do sistema de arquivos usam padrões curinga chamados *padrões glob (ou globbing)*.</span><span class="sxs-lookup"><span data-stu-id="42d93-459">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="42d93-460">Especifique grupos de arquivos com esses padrões.</span><span class="sxs-lookup"><span data-stu-id="42d93-460">Specify groups of files with these patterns.</span></span> <span data-ttu-id="42d93-461">Os dois caracteres curinga são `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="42d93-461">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="42d93-462">Corresponde a qualquer coisa no nível da pasta atual, qualquer nome de arquivo ou qualquer extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="42d93-462">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="42d93-463">As correspondências são terminadas pelos caracteres `/` e `.` no caminho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="42d93-463">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="42d93-464">Coincide a qualquer coisa em vários níveis de diretório.</span><span class="sxs-lookup"><span data-stu-id="42d93-464">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="42d93-465">Pode ser usada para fazer a correspondência recursiva com vários arquivos em uma hierarquia de diretórios.</span><span class="sxs-lookup"><span data-stu-id="42d93-465">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="42d93-466">**Exemplos de padrão glob**</span><span class="sxs-lookup"><span data-stu-id="42d93-466">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="42d93-467">Corresponde a um arquivo específico em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="42d93-467">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="42d93-468">Corresponde a todos os arquivos com a extensão *.txt* em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="42d93-468">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="42d93-469">Corresponde a todos os arquivos `appsettings.json` em diretórios que estão exatamente um nível abaixo da pasta *diretório*.</span><span class="sxs-lookup"><span data-stu-id="42d93-469">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="42d93-470">Corresponde a todos os arquivos com a extensão *.txt* encontrados em qualquer lugar abaixo da pasta *diretório*.</span><span class="sxs-lookup"><span data-stu-id="42d93-470">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
