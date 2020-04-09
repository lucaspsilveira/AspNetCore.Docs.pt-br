---
title: Interface do usuário do Razor reutilizável em bibliotecas de classes com o ASP.NET Core
author: Rick-Anderson
description: Explica como criar a ID navalha reutilizável usando visualizações parciais em uma biblioteca de classes em ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667562"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="0cb0d-103">Crie iU reutilizável usando o projeto de biblioteca da classe Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0cb0d-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="0cb0d-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0cb0d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0cb0d-105">Visualizações de navalha, páginas, controladores, modelos de página, [componentes de navalha,](xref:blazor/class-libraries) [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser incorporados em uma biblioteca de classe Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="0cb0d-106">A RCL pode ser e empacotada e reutilizada.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="0cb0d-107">Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="0cb0d-108">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="0cb0d-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0cb0d-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="0cb0d-110">Criar uma biblioteca de classes contendo a interface do usuário do Razor</span><span class="sxs-lookup"><span data-stu-id="0cb0d-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cb0d-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cb0d-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0cb0d-112">Do Visual Studio selecione **Criar um novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="0cb0d-113">Selecione **Razor Class Library** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="0cb0d-114">Nomeie a biblioteca (por exemplo, "RazorClassLib"), > **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="0cb0d-115">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="0cb0d-116">Selecione **Páginas de suporte e visualizações** se você precisar apoiar exibições.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="0cb0d-117">Por padrão, apenas páginas de barbear são suportadas.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="0cb0d-118">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-118">Select **Create**.</span></span>

<span data-ttu-id="0cb0d-119">O modelo de biblioteca de classe Razor (RCL) é padrão para o desenvolvimento de componentes Razor por padrão.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="0cb0d-120">A opção **''''''''Opções de exibição'** suporta páginas e visualizações.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0cb0d-121">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="0cb0d-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0cb0d-122">Da linha de comando, execute `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="0cb0d-123">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="0cb0d-124">O modelo de biblioteca de classe Razor (RCL) é padrão para o desenvolvimento de componentes Razor por padrão.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="0cb0d-125">Passe `--support-pages-and-views` a`dotnet new razorclasslib --support-pages-and-views`opção ( ) para fornecer suporte para páginas e visualizações.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="0cb0d-126">Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="0cb0d-127">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="0cb0d-128">Adicione arquivos Razor na RCL.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="0cb0d-129">Os modelos ASP.NET Core assumem que o conteúdo RCL está na pasta *Áreas.*</span><span class="sxs-lookup"><span data-stu-id="0cb0d-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="0cb0d-130">Consulte [o layout rcl pages](#rcl-pages-layout) para criar `~/Pages` uma `~/Areas/Pages`RCL que exponha o conteúdo em vez de .</span><span class="sxs-lookup"><span data-stu-id="0cb0d-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="0cb0d-131">Conteúdo RCL de referência</span><span class="sxs-lookup"><span data-stu-id="0cb0d-131">Reference RCL content</span></span>

<span data-ttu-id="0cb0d-132">A RCL pode ser referenciada por:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="0cb0d-133">Pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-133">NuGet package.</span></span> <span data-ttu-id="0cb0d-134">Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="0cb0d-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="0cb0d-136">Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="0cb0d-137">Substituir exibições, exibições parciais e páginas</span><span class="sxs-lookup"><span data-stu-id="0cb0d-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="0cb0d-138">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="0cb0d-139">Por exemplo, adicione *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* ao WebApp1 e a Página1 no WebApp1 terá precedência sobre a Page1 na RCL.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="0cb0d-140">No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="0cb0d-141">Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="0cb0d-142">Atualize a marcação para indicar o novo local.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="0cb0d-143">Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="0cb0d-144">Layout de páginas RCL</span><span class="sxs-lookup"><span data-stu-id="0cb0d-144">RCL Pages layout</span></span>

<span data-ttu-id="0cb0d-145">Para referenciar o conteúdo rcl como se fosse parte da pasta *Páginas* do aplicativo web, crie o projeto RCL com a seguinte estrutura de arquivo:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="0cb0d-146">*RazoruiClassLib/Páginas*</span><span class="sxs-lookup"><span data-stu-id="0cb0d-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="0cb0d-147">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="0cb0d-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="0cb0d-148">Suponha que *RazorUIClassLib/Pages/Shared* contém dois arquivos *parciais: _Header.cshtml* e *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="0cb0d-149">As `<partial>` tags podem ser adicionadas ao arquivo *_Layout.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="0cb0d-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="0cb0d-150">Crie uma RCL com ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="0cb0d-150">Create an RCL with static assets</span></span>

<span data-ttu-id="0cb0d-151">Uma RCL pode exigir ativos estáticos companheiros que podem ser referenciados pela RCL ou pelo aplicativo de consumo da RCL.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="0cb0d-152">ASP.NET Core permite criar RCLs que incluem ativos estáticos que estão disponíveis para um aplicativo consumidor.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="0cb0d-153">Para incluir ativos complementares como parte de uma RCL, crie uma pasta *wwwroot* na biblioteca de classes e inclua quaisquer arquivos necessários nessa pasta.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="0cb0d-154">Ao embalar uma RCL, todos os ativos complementares na pasta *wwwroot* são automaticamente incluídos no pacote.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="0cb0d-155">Excluir ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="0cb0d-155">Exclude static assets</span></span>

<span data-ttu-id="0cb0d-156">Para excluir ativos estáticos, adicione `$(DefaultItemExcludes)` o caminho de exclusão desejado ao grupo de propriedades no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="0cb0d-157">Entradas separadas com`;`ponto e vírgula ( ).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="0cb0d-158">No exemplo a seguir, a folha de estilos *lib.css* na pasta *wwwroot* não é considerada um ativo estático e não está incluída na RCL publicada:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="0cb0d-159">Integração de scripts de máquinas de escrever</span><span class="sxs-lookup"><span data-stu-id="0cb0d-159">Typescript integration</span></span>

<span data-ttu-id="0cb0d-160">Para incluir arquivos TypeScript em uma RCL:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="0cb0d-161">Coloque os arquivos TypeScript *(.ts)* fora da pasta *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="0cb0d-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="0cb0d-162">Por exemplo, coloque os arquivos em uma pasta *Cliente.*</span><span class="sxs-lookup"><span data-stu-id="0cb0d-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="0cb0d-163">Configure a saída de compilação TypeScript para a pasta *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="0cb0d-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="0cb0d-164">Defina `TypescriptOutDir` a propriedade `PropertyGroup` dentro de um no arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="0cb0d-165">Inclua o destino TypeScript como `ResolveCurrentProjectStaticWebAssets` uma dependência do destino adicionando o seguinte destino dentro de um `PropertyGroup` arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="0cb0d-166">Consumir conteúdo de uma RCL referenciada</span><span class="sxs-lookup"><span data-stu-id="0cb0d-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="0cb0d-167">Os arquivos incluídos na pasta *wwwroot* da RCL são expostos à RCL `_content/{LIBRARY NAME}/`ou ao aplicativo de consumo sob o prefixo .</span><span class="sxs-lookup"><span data-stu-id="0cb0d-167">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="0cb0d-168">Por exemplo, uma biblioteca chamada *Razor.Class.Lib* resulta `_content/Razor.Class.Lib/`em um caminho para conteúdo estático em .</span><span class="sxs-lookup"><span data-stu-id="0cb0d-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="0cb0d-169">Ao produzir um pacote NuGet e o nome de montagem não é o `{LIBRARY NAME}`mesmo que o ID do pacote, use o ID do pacote para .</span><span class="sxs-lookup"><span data-stu-id="0cb0d-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="0cb0d-170">O aplicativo de consumo faz referência aos `<script>`ativos `<style>` `<img>`estáticos fornecidos pela biblioteca com , e outras tags HTML.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="0cb0d-171">O aplicativo consumidor deve ter suporte `Startup.Configure`a arquivos [estáticos](xref:fundamentals/static-files) ativado em:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="0cb0d-172">Ao executar o aplicativo de`dotnet run`consumo a partir da saída de compilação (), os ativos web estáticos são ativados por padrão no ambiente Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="0cb0d-173">Para suportar ativos em outros ambientes ao `UseStaticWebAssets` executar a partir da saída de compilação, chame o construtor de host em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="0cb0d-174">A `UseStaticWebAssets` chamada não é necessária ao executar`dotnet publish`um aplicativo a partir da saída publicada ( ).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="0cb0d-175">Fluxo de desenvolvimento de vários projetos</span><span class="sxs-lookup"><span data-stu-id="0cb0d-175">Multi-project development flow</span></span>

<span data-ttu-id="0cb0d-176">Quando o aplicativo de consumo é executado:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-176">When the consuming app runs:</span></span>

* <span data-ttu-id="0cb0d-177">Os ativos da RCL permanecem em suas pastas originais.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="0cb0d-178">Os ativos não são movidos para o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="0cb0d-179">Qualquer alteração dentro da pasta *wwwroot* da RCL é refletida no aplicativo de consumo depois que a RCL é reconstruída e sem reconstruir o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="0cb0d-180">Quando a RCL é construída, um manifesto é produzido que descreve os locais estáticos do ativo web.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="0cb0d-181">O aplicativo de consumo lê o manifesto em tempo de execução para consumir os ativos de projetos e pacotes referenciados.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="0cb0d-182">Quando um novo ativo é adicionado a uma RCL, a RCL deve ser reconstruída para atualizar seu manifesto antes que um aplicativo consumidor possa acessar o novo ativo.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="0cb0d-183">Publicar</span><span class="sxs-lookup"><span data-stu-id="0cb0d-183">Publish</span></span>

<span data-ttu-id="0cb0d-184">Quando o aplicativo é publicado, os ativos complementares de todos os projetos e pacotes `_content/{LIBRARY NAME}/`referenciados são copiados para a pasta *wwwroot* do aplicativo publicado em .</span><span class="sxs-lookup"><span data-stu-id="0cb0d-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0cb0d-185">Visualizações de navalha, páginas, controladores, modelos de página, [componentes de navalha,](xref:blazor/class-libraries) [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser incorporados em uma biblioteca de classe Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="0cb0d-186">A RCL pode ser e empacotada e reutilizada.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="0cb0d-187">Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="0cb0d-188">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="0cb0d-189">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0cb0d-189">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="0cb0d-190">Criar uma biblioteca de classes contendo a interface do usuário do Razor</span><span class="sxs-lookup"><span data-stu-id="0cb0d-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cb0d-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cb0d-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0cb0d-192">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0cb0d-193">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0cb0d-194">Dê um nome à biblioteca (por exemplo, "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="0cb0d-195">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="0cb0d-196">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="0cb0d-197">Selecione **Biblioteca de Classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="0cb0d-198">Uma RCL tem o seguinte arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="0cb0d-199">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="0cb0d-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0cb0d-200">Da linha de comando, execute `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="0cb0d-201">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="0cb0d-202">Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="0cb0d-203">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="0cb0d-204">Adicione arquivos Razor na RCL.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="0cb0d-205">Os modelos ASP.NET Core assumem que o conteúdo RCL está na pasta *Áreas.*</span><span class="sxs-lookup"><span data-stu-id="0cb0d-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="0cb0d-206">Consulte [o layout rcl pages](#rcl-pages-layout) para criar `~/Pages` uma `~/Areas/Pages`RCL que exponha o conteúdo em vez de .</span><span class="sxs-lookup"><span data-stu-id="0cb0d-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="0cb0d-207">Conteúdo RCL de referência</span><span class="sxs-lookup"><span data-stu-id="0cb0d-207">Reference RCL content</span></span>

<span data-ttu-id="0cb0d-208">A RCL pode ser referenciada por:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="0cb0d-209">Pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-209">NuGet package.</span></span> <span data-ttu-id="0cb0d-210">Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="0cb0d-211">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="0cb0d-212">Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="0cb0d-213">Passo a passo: Crie um projeto de RCL e use a partir de um projeto Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0cb0d-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="0cb0d-214">Você pode baixar o [projeto completo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testá-lo em vez de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-214">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="0cb0d-215">O download de exemplo contém um código adicional e links que facilitam o teste do projeto.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="0cb0d-216">Você pode deixar comentários [nesse problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098) com suas opiniões sobre os exemplos de download em relação às instruções passo a passo.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-216">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="0cb0d-217">Testar o aplicativo de download</span><span class="sxs-lookup"><span data-stu-id="0cb0d-217">Test the download app</span></span>

<span data-ttu-id="0cb0d-218">Se você não tiver baixado o aplicativo concluído e preferir criar o projeto do passo a passo, vá para a [próxima seção](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cb0d-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cb0d-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cb0d-220">Abra o arquivo *.sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="0cb0d-221">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-221">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0cb0d-222">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="0cb0d-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0cb0d-223">Em um prompt de comando no diretório *cli*, crie a RCL e o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="0cb0d-224">Acesse o diretório *WebApp1* e execute o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="0cb0d-225">Siga as instruções em [Testar WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="0cb0d-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="0cb0d-226">Criar uma RCL</span><span class="sxs-lookup"><span data-stu-id="0cb0d-226">Create an RCL</span></span>

<span data-ttu-id="0cb0d-227">Nesta seção, uma RCL é criada.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-227">In this section, an RCL is created.</span></span> <span data-ttu-id="0cb0d-228">Arquivos Razor são adicionados à RCL.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cb0d-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cb0d-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cb0d-230">Crie o projeto da RCL:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-230">Create the RCL project:</span></span>

* <span data-ttu-id="0cb0d-231">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0cb0d-232">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0cb0d-233">Nomeie o aplicativo **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="0cb0d-234">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="0cb0d-235">Selecione **Biblioteca de Classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="0cb0d-236">Adicione um arquivo Razor de exibição parcial chamado *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0cb0d-237">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="0cb0d-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0cb0d-238">Na linha de comando, execute o seguinte:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="0cb0d-239">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-239">The preceding commands:</span></span>

* <span data-ttu-id="0cb0d-240">Cria `RazorUIClassLib` a RCL.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="0cb0d-241">Criam uma página Razor _Message e a adicionam à RCL.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="0cb0d-242">O parâmetro `-np` cria a página sem um `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="0cb0d-243">Cria um arquivo [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) e adiciona-o à RCL.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="0cb0d-244">O arquivo *_ViewStart.cshtml* é necessário para usar o layout do projeto Páginas de Barbear (que é adicionado na próxima seção).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="0cb0d-245">Adicionar arquivos e pastas razor ao projeto</span><span class="sxs-lookup"><span data-stu-id="0cb0d-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="0cb0d-246">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="0cb0d-247">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="0cb0d-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` é necessário para usar a exibição parcial (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="0cb0d-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="0cb0d-249">Em vez de incluir a diretiva `@addTagHelper`, você pode adicionar um arquivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="0cb0d-250">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="0cb0d-251">Para obter mais informações sobre *_ViewImports.cshtml*, consulte [Importar Diretivas Compartilhadas](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="0cb0d-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="0cb0d-252">Crie a biblioteca de classes para verificar se não há nenhum erro de compilador:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="0cb0d-253">A saída do build contém *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="0cb0d-254">*RazorUIClassLib.Views.dll* contém o conteúdo Razor compilado.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="0cb0d-255">Use a biblioteca da interface do usuário do Razor de um projeto Páginas Razor</span><span class="sxs-lookup"><span data-stu-id="0cb0d-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0cb0d-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0cb0d-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0cb0d-257">Crie aplicativo Web Páginas Razor:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="0cb0d-258">No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução > **Adicionar** >  **Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="0cb0d-259">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0cb0d-260">Nomeie o aplicativo como **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="0cb0d-261">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="0cb0d-262">Selecione **Aplicativo Web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="0cb0d-263">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Definir como projeto de inicialização**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="0cb0d-264">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Dependências de Build** > **Dependências do Projeto**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="0cb0d-265">Marque **RazorUIClassLib** como uma dependência de **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="0cb0d-266">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Adicionar** > **Referência**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="0cb0d-267">Na caixa de diálogo **Gerenciador de Referências**, marque **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="0cb0d-268">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-268">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0cb0d-269">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="0cb0d-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0cb0d-270">Crie um aplicativo web Razor Pages e um arquivo de solução contendo o aplicativo Razor Pages e o RCL:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="0cb0d-271">Crie e execute o aplicativo Web:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="0cb0d-272">Testar o WebApp1</span><span class="sxs-lookup"><span data-stu-id="0cb0d-272">Test WebApp1</span></span>

<span data-ttu-id="0cb0d-273">Navegue `/MyFeature/Page1` para verificar se a biblioteca de classe Razor UI está em uso.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="0cb0d-274">Substituir exibições, exibições parciais e páginas</span><span class="sxs-lookup"><span data-stu-id="0cb0d-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="0cb0d-275">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="0cb0d-276">Por exemplo, adicione *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* ao WebApp1 e a Página1 no WebApp1 terá precedência sobre a Page1 na RCL.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="0cb0d-277">No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="0cb0d-278">Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="0cb0d-279">Atualize a marcação para indicar o novo local.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="0cb0d-280">Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="0cb0d-281">Layout de páginas RCL</span><span class="sxs-lookup"><span data-stu-id="0cb0d-281">RCL Pages layout</span></span>

<span data-ttu-id="0cb0d-282">Para referenciar o conteúdo rcl como se fosse parte da pasta *Páginas* do aplicativo web, crie o projeto RCL com a seguinte estrutura de arquivo:</span><span class="sxs-lookup"><span data-stu-id="0cb0d-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="0cb0d-283">*RazoruiClassLib/Páginas*</span><span class="sxs-lookup"><span data-stu-id="0cb0d-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="0cb0d-284">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="0cb0d-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="0cb0d-285">Suponha que *RazorUIClassLib/Pages/Shared* contém dois arquivos *parciais: _Header.cshtml* e *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0cb0d-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="0cb0d-286">As `<partial>` tags podem ser adicionadas ao arquivo *_Layout.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="0cb0d-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0cb0d-287">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0cb0d-287">Additional resources</span></span>

* <xref:blazor/class-libraries>
