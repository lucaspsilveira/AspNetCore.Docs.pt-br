---
title: Use LibMan com ASP.NET Core no Visual Studio
author: scottaddie
description: Aprenda a usar o LibMan em um projeto ASP.NET Core com o Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658308"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="124cd-103">Use LibMan com ASP.NET Core no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="124cd-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="124cd-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="124cd-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="124cd-105">O Visual Studio tem suporte integrado para [o LibMan](xref:client-side/libman/index) em projetos ASP.NET Core, incluindo:</span><span class="sxs-lookup"><span data-stu-id="124cd-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="124cd-106">Suporte para configurar e executar operações de restauração libman na compilação.</span><span class="sxs-lookup"><span data-stu-id="124cd-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="124cd-107">Itens do menu para ativar as operações de restauração e limpeza do LibMan.</span><span class="sxs-lookup"><span data-stu-id="124cd-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="124cd-108">Pesquise a caixa de diálogo para encontrar bibliotecas e adicionar os arquivos a um projeto.</span><span class="sxs-lookup"><span data-stu-id="124cd-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="124cd-109">Suporte de edição para *libman.json*&mdash;o arquivo manifesto LibMan.</span><span class="sxs-lookup"><span data-stu-id="124cd-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="124cd-110">[Exibir ou baixar o código da amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="124cd-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="124cd-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="124cd-111">Prerequisites</span></span>

* <span data-ttu-id="124cd-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a **carga de trabalho de ASP.NET e desenvolvimento web**</span><span class="sxs-lookup"><span data-stu-id="124cd-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="124cd-113">Adicionar arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="124cd-113">Add library files</span></span>

<span data-ttu-id="124cd-114">Os arquivos da biblioteca podem ser adicionados a um projeto ASP.NET Core de duas maneiras diferentes:</span><span class="sxs-lookup"><span data-stu-id="124cd-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="124cd-115">Use a caixa de diálogo Adicionar biblioteca do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="124cd-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="124cd-116">Configurar manualmente entradas de arquivo manifesto LibMan</span><span class="sxs-lookup"><span data-stu-id="124cd-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="124cd-117">Use a caixa de diálogo Adicionar biblioteca do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="124cd-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="124cd-118">Siga estas etapas para instalar uma biblioteca do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="124cd-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="124cd-119">No **Solution Explorer,** clique com o botão direito do mouse na pasta de projeto na qual os arquivos devem ser adicionados.</span><span class="sxs-lookup"><span data-stu-id="124cd-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="124cd-120">Escolha **adicionar** > **biblioteca ao lado do cliente**.</span><span class="sxs-lookup"><span data-stu-id="124cd-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="124cd-121">A caixa de diálogo **Adicionar biblioteca do lado** do cliente é exibida:</span><span class="sxs-lookup"><span data-stu-id="124cd-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Adicionar diálogo da biblioteca do lado do cliente](_static/add-library-dialog.png)

* <span data-ttu-id="124cd-123">Selecione o provedor de biblioteca saqueado do **Provedor.**</span><span class="sxs-lookup"><span data-stu-id="124cd-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="124cd-124">O CDNJS é o provedor padrão.</span><span class="sxs-lookup"><span data-stu-id="124cd-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="124cd-125">Digite o nome da biblioteca para buscar na caixa de texto da **Biblioteca.**</span><span class="sxs-lookup"><span data-stu-id="124cd-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="124cd-126">O IntelliSense fornece uma lista de bibliotecas que começam com o texto fornecido.</span><span class="sxs-lookup"><span data-stu-id="124cd-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="124cd-127">Selecione a biblioteca na lista IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="124cd-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="124cd-128">Observe que o nome da `@` biblioteca está sufixo com o símbolo e a versão estável mais recente conhecida pelo provedor selecionado.</span><span class="sxs-lookup"><span data-stu-id="124cd-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="124cd-129">Decida quais arquivos incluir:</span><span class="sxs-lookup"><span data-stu-id="124cd-129">Decide which files to include:</span></span>
  * <span data-ttu-id="124cd-130">Selecione o botão **Incluir todos os arquivos** de biblioteca para incluir todos os arquivos da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="124cd-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="124cd-131">Selecione o botão **Desescolher arquivos específicos** para incluir um subconjunto dos arquivos da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="124cd-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="124cd-132">Quando o botão de rádio é selecionado, a árvore seletora de arquivos é ativada.</span><span class="sxs-lookup"><span data-stu-id="124cd-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="124cd-133">Verifique as caixas à esquerda dos nomes dos arquivos para baixar.</span><span class="sxs-lookup"><span data-stu-id="124cd-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="124cd-134">Especifique a pasta de projeto para armazenar os arquivos na caixa de texto **Local de** destino.</span><span class="sxs-lookup"><span data-stu-id="124cd-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="124cd-135">Como recomendação, armazene cada biblioteca em uma pasta separada.</span><span class="sxs-lookup"><span data-stu-id="124cd-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="124cd-136">A pasta **de localização de destino** sugerida é baseada no local a partir do qual a caixa de diálogo foi lançada:</span><span class="sxs-lookup"><span data-stu-id="124cd-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="124cd-137">Se lançado a partir da raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="124cd-137">If launched from the project root:</span></span>
    * <span data-ttu-id="124cd-138">*wwwroot/lib* é usado se *wwwroot* existir.</span><span class="sxs-lookup"><span data-stu-id="124cd-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="124cd-139">*lib* é usado se *wwwroot* não existe.</span><span class="sxs-lookup"><span data-stu-id="124cd-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="124cd-140">Se for lançado a partir de uma pasta de projeto, o nome da pasta correspondente será usado.</span><span class="sxs-lookup"><span data-stu-id="124cd-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="124cd-141">A sugestão da pasta é sufixada com o nome da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="124cd-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="124cd-142">A tabela a seguir ilustra sugestões de pastas ao instalar jQuery em um projeto Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="124cd-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="124cd-143">Local de lançamento</span><span class="sxs-lookup"><span data-stu-id="124cd-143">Launch location</span></span>                           |<span data-ttu-id="124cd-144">Pasta sugerida</span><span class="sxs-lookup"><span data-stu-id="124cd-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="124cd-145">raiz do projeto (se *wwwroot* existe)</span><span class="sxs-lookup"><span data-stu-id="124cd-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="124cd-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="124cd-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="124cd-147">raiz do projeto (se *wwwroot* não existe)</span><span class="sxs-lookup"><span data-stu-id="124cd-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="124cd-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="124cd-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="124cd-149">*Pasta páginas* no projeto</span><span class="sxs-lookup"><span data-stu-id="124cd-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="124cd-150">*Páginas/jquery/*</span><span class="sxs-lookup"><span data-stu-id="124cd-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="124cd-151">Clique no botão **Instalar** para baixar os arquivos, de acordo com a configuração em *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="124cd-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="124cd-152">Revise o feed do **Gerenciador** de bibliotecas da janela **Saída** para obter detalhes da instalação.</span><span class="sxs-lookup"><span data-stu-id="124cd-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="124cd-153">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="124cd-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="124cd-154">Configurar manualmente entradas de arquivo manifesto LibMan</span><span class="sxs-lookup"><span data-stu-id="124cd-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="124cd-155">Todas as operações da LibMan no Visual Studio são baseadas no conteúdo do manifesto LibMan da raiz do projeto *(libman.json).*</span><span class="sxs-lookup"><span data-stu-id="124cd-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="124cd-156">Você pode editar manualmente *libman.json* para configurar arquivos de biblioteca para o projeto.</span><span class="sxs-lookup"><span data-stu-id="124cd-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="124cd-157">O Visual Studio restaura todos os arquivos da biblioteca assim *que libman.json* for salvo.</span><span class="sxs-lookup"><span data-stu-id="124cd-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="124cd-158">Para abrir *libman.json* para edição, existem as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="124cd-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="124cd-159">Clique duas vezes no arquivo *libman.json* no **Solution Explorer**.</span><span class="sxs-lookup"><span data-stu-id="124cd-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="124cd-160">Clique com o botão direito do mouse no projeto no **Solution Explorer** e **selecione Gerenciar bibliotecas lado do cliente**.</span><span class="sxs-lookup"><span data-stu-id="124cd-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="124cd-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="124cd-161">**&#8224;**</span></span>
* <span data-ttu-id="124cd-162">Selecione **Gerenciar bibliotecas lado do cliente** no menu Visual Studio **Project.**</span><span class="sxs-lookup"><span data-stu-id="124cd-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="124cd-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="124cd-163">**&#8224;**</span></span>

<span data-ttu-id="124cd-164">**&#8224;&#8224;.** Se o arquivo *libman.json* ainda não existir na raiz do projeto, ele será criado com o conteúdo do modelo de item padrão.</span><span class="sxs-lookup"><span data-stu-id="124cd-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="124cd-165">O Visual Studio oferece suporte de edição JSON rico, como coloração, formatação, IntelliSense e validação de esquemas.</span><span class="sxs-lookup"><span data-stu-id="124cd-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="124cd-166">O esquema JSON do manifesto LibMan [https://json.schemastore.org/libman](https://json.schemastore.org/libman)é encontrado em .</span><span class="sxs-lookup"><span data-stu-id="124cd-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="124cd-167">Com o seguinte arquivo manifesto, libman recupera arquivos `libraries` de acordo com a configuração definida na propriedade.</span><span class="sxs-lookup"><span data-stu-id="124cd-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="124cd-168">Segue-se uma explicação dos `libraries` literais do objeto definidos a seguir:</span><span class="sxs-lookup"><span data-stu-id="124cd-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="124cd-169">Um subconjunto da versão 3.3.1 [do JQuery](https://jquery.com/) é recuperado do provedor CDNJS.</span><span class="sxs-lookup"><span data-stu-id="124cd-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="124cd-170">O subconjunto é `files` definido&mdash;na propriedade*jquery.min.js*, *jquery.js*e *jquery.min.map*.</span><span class="sxs-lookup"><span data-stu-id="124cd-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="124cd-171">Os arquivos são colocados na pasta *wwwroot/lib/jquery* do projeto.</span><span class="sxs-lookup"><span data-stu-id="124cd-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="124cd-172">A totalidade da versão 4.1.3 do [Bootstrap](https://getbootstrap.com/) é recuperada e colocada em uma pasta *wwwroot/lib/bootstrap.*</span><span class="sxs-lookup"><span data-stu-id="124cd-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="124cd-173">A propriedade do `provider` objeto literal `defaultProvider` substitui o valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="124cd-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="124cd-174">LibMan recupera os arquivos Bootstrap do provedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="124cd-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="124cd-175">Um subconjunto de [Lodash](https://lodash.com/) foi aprovado por um órgão governamental dentro da organização.</span><span class="sxs-lookup"><span data-stu-id="124cd-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="124cd-176">Os arquivos *lodash.js* e *lodash.min.js* são recuperados do sistema de arquivos local em *C:\\temp\\lodash\\*.</span><span class="sxs-lookup"><span data-stu-id="124cd-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="124cd-177">Os arquivos são copiados para a pasta *wwwroot/lib/lodash* do projeto.</span><span class="sxs-lookup"><span data-stu-id="124cd-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="124cd-178">LibMan suporta apenas uma versão de cada biblioteca de cada provedor.</span><span class="sxs-lookup"><span data-stu-id="124cd-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="124cd-179">O arquivo *libman.json* falha na validação do esquema se contiver duas bibliotecas com o mesmo nome de biblioteca para um determinado provedor.</span><span class="sxs-lookup"><span data-stu-id="124cd-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="124cd-180">Restaurar arquivos da biblioteca</span><span class="sxs-lookup"><span data-stu-id="124cd-180">Restore library files</span></span>

<span data-ttu-id="124cd-181">Para restaurar arquivos de biblioteca de dentro do Visual Studio, deve haver um arquivo *libman.json* válido na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="124cd-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="124cd-182">Os arquivos restaurados são colocados no projeto no local especificado para cada biblioteca.</span><span class="sxs-lookup"><span data-stu-id="124cd-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="124cd-183">Os arquivos da biblioteca podem ser restaurados em um projeto ASP.NET Core de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="124cd-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="124cd-184">Restaurar arquivos durante a compilação</span><span class="sxs-lookup"><span data-stu-id="124cd-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="124cd-185">Restaurar arquivos manualmente</span><span class="sxs-lookup"><span data-stu-id="124cd-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="124cd-186">Restaurar arquivos durante a compilação</span><span class="sxs-lookup"><span data-stu-id="124cd-186">Restore files during build</span></span>

<span data-ttu-id="124cd-187">LibMan pode restaurar os arquivos de biblioteca definidos como parte do processo de compilação.</span><span class="sxs-lookup"><span data-stu-id="124cd-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="124cd-188">Por padrão, o comportamento *de restauração na compilação* é desativado.</span><span class="sxs-lookup"><span data-stu-id="124cd-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="124cd-189">Para habilitar e testar o comportamento de restauração na compilação:</span><span class="sxs-lookup"><span data-stu-id="124cd-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="124cd-190">Clique com o botão direito do mouse *libman.json* no **Solution Explorer** e **selecione Ativar restaurar bibliotecas do lado do cliente na compilação** a partir do menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="124cd-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="124cd-191">Clique no botão **Sim** quando solicitado a instalar um pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="124cd-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="124cd-192">O pacote [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet é adicionado ao projeto:</span><span class="sxs-lookup"><span data-stu-id="124cd-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="124cd-193">Construa o projeto para confirmar a restauração do arquivo LibMan.</span><span class="sxs-lookup"><span data-stu-id="124cd-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="124cd-194">O `Microsoft.Web.LibraryManager.Build` pacote injeta um alvo MSBuild que executa o LibMan durante a operação de compilação do projeto.</span><span class="sxs-lookup"><span data-stu-id="124cd-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="124cd-195">Revise o feed **Build** da **janela Saída** para um registro de atividade libman:</span><span class="sxs-lookup"><span data-stu-id="124cd-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="124cd-196">Quando o comportamento de restauração na compilação é ativado, o menu de contexto *libman.json* exibe uma opção **Desativar Restaurar bibliotecas do lado do cliente na** opção Build.</span><span class="sxs-lookup"><span data-stu-id="124cd-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="124cd-197">A seleção desta `Microsoft.Web.LibraryManager.Build` opção remove a referência do pacote do arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="124cd-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="124cd-198">Consequentemente, as bibliotecas do lado do cliente não são mais restauradas em cada compilação.</span><span class="sxs-lookup"><span data-stu-id="124cd-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="124cd-199">Independentemente da configuração de restauração na compilação, você pode restaurar manualmente a qualquer momento a partir do menu de contexto *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="124cd-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="124cd-200">Para obter mais informações, consulte [Restaurar arquivos manualmente](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="124cd-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="124cd-201">Restaurar arquivos manualmente</span><span class="sxs-lookup"><span data-stu-id="124cd-201">Restore files manually</span></span>

<span data-ttu-id="124cd-202">Para restaurar manualmente arquivos da biblioteca:</span><span class="sxs-lookup"><span data-stu-id="124cd-202">To manually restore library files:</span></span>

* <span data-ttu-id="124cd-203">Para todos os projetos na solução:</span><span class="sxs-lookup"><span data-stu-id="124cd-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="124cd-204">Clique com o botão direito do mouse no nome da solução no **Solution Explorer**.</span><span class="sxs-lookup"><span data-stu-id="124cd-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="124cd-205">Selecione a opção **Restaurar bibliotecas do lado do cliente.**</span><span class="sxs-lookup"><span data-stu-id="124cd-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="124cd-206">Para um projeto específico:</span><span class="sxs-lookup"><span data-stu-id="124cd-206">For a specific project:</span></span>
  * <span data-ttu-id="124cd-207">Clique com o botão direito do mouse no arquivo *libman.json* no **Solution Explorer**.</span><span class="sxs-lookup"><span data-stu-id="124cd-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="124cd-208">Selecione a opção **Restaurar bibliotecas do lado do cliente.**</span><span class="sxs-lookup"><span data-stu-id="124cd-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="124cd-209">Enquanto a operação de restauração estiver em execução:</span><span class="sxs-lookup"><span data-stu-id="124cd-209">While the restore operation is running:</span></span>

* <span data-ttu-id="124cd-210">O ícone tsc (Task Status Center) na barra de status do Visual Studio será animado e lerá *a operação Restaurar iniciada*.</span><span class="sxs-lookup"><span data-stu-id="124cd-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="124cd-211">Clicar no ícone abre uma dica de ferramenta listando as tarefas em segundo plano conhecidas.</span><span class="sxs-lookup"><span data-stu-id="124cd-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="124cd-212">As mensagens serão enviadas para a barra de status e para o feed do Gerenciador de **bibliotecas** da janela **Saída.**</span><span class="sxs-lookup"><span data-stu-id="124cd-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="124cd-213">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="124cd-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="124cd-214">Excluir arquivos da biblioteca</span><span class="sxs-lookup"><span data-stu-id="124cd-214">Delete library files</span></span>

<span data-ttu-id="124cd-215">Para executar a operação *limpa,* que exclui arquivos de biblioteca previamente restaurados no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="124cd-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="124cd-216">Clique com o botão direito do mouse no arquivo *libman.json* no **Solution Explorer**.</span><span class="sxs-lookup"><span data-stu-id="124cd-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="124cd-217">Selecione a opção **Bibliotecas do Lado do Cliente Limpo.**</span><span class="sxs-lookup"><span data-stu-id="124cd-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="124cd-218">Para evitar a remoção não intencional de arquivos não-biblioteca, a operação limpa não exclui diretórios inteiros.</span><span class="sxs-lookup"><span data-stu-id="124cd-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="124cd-219">Ele só remove arquivos que foram incluídos na restauração anterior.</span><span class="sxs-lookup"><span data-stu-id="124cd-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="124cd-220">Enquanto a operação limpa estiver em execução:</span><span class="sxs-lookup"><span data-stu-id="124cd-220">While the clean operation is running:</span></span>

* <span data-ttu-id="124cd-221">O ícone TSC na barra de status do Visual Studio será animado e lerá *a operação bibliotecas clientes iniciada*.</span><span class="sxs-lookup"><span data-stu-id="124cd-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="124cd-222">Clicar no ícone abre uma dica de ferramenta listando as tarefas em segundo plano conhecidas.</span><span class="sxs-lookup"><span data-stu-id="124cd-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="124cd-223">As mensagens são enviadas para a barra de status e para o feed do **Gerenciador** de bibliotecas da janela **Saída.**</span><span class="sxs-lookup"><span data-stu-id="124cd-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="124cd-224">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="124cd-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="124cd-225">A operação limpa só exclui arquivos do projeto.</span><span class="sxs-lookup"><span data-stu-id="124cd-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="124cd-226">Os arquivos da biblioteca permanecem no cache para uma recuperação mais rápida em futuras operações de restauração.</span><span class="sxs-lookup"><span data-stu-id="124cd-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="124cd-227">Para gerenciar arquivos de biblioteca armazenados no cache da máquina local, use o [Cli LibMan](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="124cd-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="124cd-228">Desinstalar arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="124cd-228">Uninstall library files</span></span>

<span data-ttu-id="124cd-229">Para desinstalar arquivos de biblioteca:</span><span class="sxs-lookup"><span data-stu-id="124cd-229">To uninstall library files:</span></span>

* <span data-ttu-id="124cd-230">Abra *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="124cd-230">Open *libman.json*.</span></span>
* <span data-ttu-id="124cd-231">Posicione o cuidado `libraries` dentro do objeto correspondente literal.</span><span class="sxs-lookup"><span data-stu-id="124cd-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="124cd-232">Clique no ícone da lâmpada que aparece na margem esquerda e selecione **Desinstalar \<library_name>@\<library_version>**:</span><span class="sxs-lookup"><span data-stu-id="124cd-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Desinstale a opção menu de contexto da biblioteca](_static/uninstall-menu-option.png)

<span data-ttu-id="124cd-234">Alternativamente, você pode editar e salvar manualmente o manifesto LibMan *(libman.json*).</span><span class="sxs-lookup"><span data-stu-id="124cd-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="124cd-235">A [operação de restauração](#restore-library-files) é executada quando o arquivo é salvo.</span><span class="sxs-lookup"><span data-stu-id="124cd-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="124cd-236">Os arquivos da biblioteca que não estão mais definidos em *libman.json* são removidos do projeto.</span><span class="sxs-lookup"><span data-stu-id="124cd-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="124cd-237">Atualizar versão da biblioteca</span><span class="sxs-lookup"><span data-stu-id="124cd-237">Update library version</span></span>

<span data-ttu-id="124cd-238">Para verificar uma versão atualizada da biblioteca:</span><span class="sxs-lookup"><span data-stu-id="124cd-238">To check for an updated library version:</span></span>

* <span data-ttu-id="124cd-239">Abra *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="124cd-239">Open *libman.json*.</span></span>
* <span data-ttu-id="124cd-240">Posicione o cuidado `libraries` dentro do objeto correspondente literal.</span><span class="sxs-lookup"><span data-stu-id="124cd-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="124cd-241">Clique no ícone da lâmpada que aparece na margem esquerda.</span><span class="sxs-lookup"><span data-stu-id="124cd-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="124cd-242">Passar o tempo **verifique se há atualizações**.</span><span class="sxs-lookup"><span data-stu-id="124cd-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="124cd-243">LibMan verifica uma versão de biblioteca mais recente do que a versão instalada.</span><span class="sxs-lookup"><span data-stu-id="124cd-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="124cd-244">Os seguintes resultados podem ocorrer:</span><span class="sxs-lookup"><span data-stu-id="124cd-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="124cd-245">Uma mensagem **Sem atualizações encontradas** será exibida se a versão mais recente já estiver instalada.</span><span class="sxs-lookup"><span data-stu-id="124cd-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="124cd-246">A versão estável mais recente é exibida se não estiver instalada.</span><span class="sxs-lookup"><span data-stu-id="124cd-246">The latest stable version is displayed if not already installed.</span></span>

  ![Verifique se há atualizações opção de menu de contexto](_static/update-menu-option.png)

* <span data-ttu-id="124cd-248">Se uma versão mais recente do que a versão instalada estiver disponível, a pré-versão será exibida.</span><span class="sxs-lookup"><span data-stu-id="124cd-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="124cd-249">Para fazer o downgrade para uma versão de biblioteca mais antiga, edite manualmente o arquivo *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="124cd-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="124cd-250">Quando o arquivo é salvo, a [operação de restauração](#restore-library-files)LibMan:</span><span class="sxs-lookup"><span data-stu-id="124cd-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="124cd-251">Remove arquivos redundantes da versão anterior.</span><span class="sxs-lookup"><span data-stu-id="124cd-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="124cd-252">Adiciona arquivos novos e atualizados da nova versão.</span><span class="sxs-lookup"><span data-stu-id="124cd-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="124cd-253">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="124cd-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="124cd-254">Repositório do GitHub do LibMan</span><span class="sxs-lookup"><span data-stu-id="124cd-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
