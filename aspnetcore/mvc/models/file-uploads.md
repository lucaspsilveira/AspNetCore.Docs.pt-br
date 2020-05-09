---
title: Carregar arquivos no ASP.NET Core
author: rick-anderson
description: Como usar o model binding e o streaming para carregar arquivos no ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/file-uploads
ms.openlocfilehash: b613ccd8df65e41b86793466a0ed5dc7bf7e8772
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976747"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="4c2b6-103">Carregar arquivos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c2b6-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="4c2b6-104">Por [Steve Smith](https://ardalis.com/) e [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="4c2b6-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4c2b6-105">O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="4c2b6-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4c2b6-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="4c2b6-107">Considerações de segurança</span><span class="sxs-lookup"><span data-stu-id="4c2b6-107">Security considerations</span></span>

<span data-ttu-id="4c2b6-108">Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="4c2b6-109">Os invasores podem tentar:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="4c2b6-110">Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="4c2b6-111">Carregar vírus ou malware.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="4c2b6-112">Comprometa redes e servidores de outras maneiras.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="4c2b6-113">As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="4c2b6-114">Carregue arquivos em uma área de carregamento de arquivo dedicada, preferencialmente para uma unidade que não seja do sistema.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="4c2b6-115">Um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="4c2b6-116">Desabilite as permissões de execução no local de carregamento do arquivo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4c2b6-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="4c2b6-117">**Não** persiste arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4c2b6-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="4c2b6-118">Use um nome de arquivo seguro determinado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="4c2b6-119">Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado. &dagger; O HTML codifica o nome de arquivo não confiável ao exibi-lo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="4c2b6-120">Por exemplo, registrar em log o nome do arquivo ou exibindo na interface do usuário (Razor a saída de código HTML automaticamente).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="4c2b6-121">Permitir somente extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4c2b6-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="4c2b6-122">Verifique se as verificações do lado do cliente são executadas no servidor. &dagger; As verificações no lado do cliente são fáceis de evitar.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="4c2b6-123">Verifique o tamanho de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="4c2b6-124">Defina um limite de tamanho máximo para evitar carregamentos grandes.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4c2b6-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="4c2b6-125">Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="4c2b6-126">**Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**</span><span class="sxs-lookup"><span data-stu-id="4c2b6-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="4c2b6-127">&dagger;O aplicativo de exemplo demonstra uma abordagem que atende aos critérios.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="4c2b6-128">Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="4c2b6-129">Assuma completamente o controle de um sistema.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="4c2b6-130">Sobrecarregar um sistema com o resultado que o sistema falha.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="4c2b6-131">Comprometer dados do sistema ou de usuários.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="4c2b6-132">Aplique graffiti a uma interface do usuário pública.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="4c2b6-133">Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="4c2b6-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="4c2b6-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="4c2b6-135">Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários</span><span class="sxs-lookup"><span data-stu-id="4c2b6-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="4c2b6-136">Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="4c2b6-137">Cenários de armazenamento</span><span class="sxs-lookup"><span data-stu-id="4c2b6-137">Storage scenarios</span></span>

<span data-ttu-id="4c2b6-138">As opções de armazenamento comuns para arquivos incluem:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-138">Common storage options for files include:</span></span>

* <span data-ttu-id="4c2b6-139">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="4c2b6-139">Database</span></span>

  * <span data-ttu-id="4c2b6-140">Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="4c2b6-141">Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="4c2b6-142">Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="4c2b6-143">Armazenamento físico (sistema de arquivos ou compartilhamento de rede)</span><span class="sxs-lookup"><span data-stu-id="4c2b6-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="4c2b6-144">Para carregamentos de arquivos grandes:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-144">For large file uploads:</span></span>
    * <span data-ttu-id="4c2b6-145">Os limites de banco de dados podem restringir o tamanho do carregamento.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="4c2b6-146">O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="4c2b6-147">O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="4c2b6-148">O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="4c2b6-149">**Nunca conceda a permissão EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="4c2b6-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="4c2b6-150">Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="4c2b6-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="4c2b6-151">Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="4c2b6-152">Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="4c2b6-153">Para obter mais informações, consulte [início rápido: usar o .net para criar um blob no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="4c2b6-154">Cenários de upload de arquivo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-154">File upload scenarios</span></span>

<span data-ttu-id="4c2b6-155">Duas abordagens gerais para carregar arquivos são buffer e streaming.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="4c2b6-156">**de resposta**</span><span class="sxs-lookup"><span data-stu-id="4c2b6-156">**Buffering**</span></span>

<span data-ttu-id="4c2b6-157">O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile>, que é uma representação em C# do arquivo usado para processar ou salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="4c2b6-158">Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="4c2b6-159">Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="4c2b6-160">Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="4c2b6-161">Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="4c2b6-162">O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="4c2b6-163">Armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="4c2b6-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="4c2b6-164">Backup de banco de dados</span><span class="sxs-lookup"><span data-stu-id="4c2b6-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="4c2b6-165">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="4c2b6-165">**Streaming**</span></span>

<span data-ttu-id="4c2b6-166">O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="4c2b6-167">O streaming não melhora significativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="4c2b6-168">O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="4c2b6-169">O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="4c2b6-170">Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="4c2b6-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="4c2b6-171">Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="4c2b6-172">O exemplo a seguir demonstra o uso de Razor um formulário de páginas para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):</span><span class="sxs-lookup"><span data-stu-id="4c2b6-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="4c2b6-173">O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="4c2b6-174">O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="4c2b6-175">Não há validação.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-175">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="4c2b6-176">Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="4c2b6-177">Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="4c2b6-178">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="4c2b6-179">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="4c2b6-179">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="4c2b6-180">Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de`enctype`codificação ( `multipart/form-data`) de.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="4c2b6-181">Para um `files` elemento input oferecer suporte ao carregamento de vários arquivos, forneça `multiple` o atributo no `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="4c2b6-182">Os arquivos individuais carregados no servidor podem ser acessados por meio de <xref:Microsoft.AspNetCore.Http.IFormFile>Associação de [modelo](xref:mvc/models/model-binding) usando.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="4c2b6-183">O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="4c2b6-184">**Não** use a `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> outros para exibição e registro em log.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="4c2b6-185">Ao exibir ou registrar em log, o HTML codifica o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="4c2b6-186">Um invasor pode fornecer um nome de arquivo mal-intencionado, incluindo caminhos completos ou caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="4c2b6-187">Os aplicativos devem:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-187">Applications should:</span></span>
>
> * <span data-ttu-id="4c2b6-188">Remova o caminho do nome de arquivo fornecido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="4c2b6-189">Salve o nome de arquivo com codificação em HTML e removido por caminho para a interface do usuário ou log.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="4c2b6-190">Gere um novo nome de arquivo aleatório para armazenamento.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="4c2b6-191">O código a seguir remove o caminho do nome do arquivo:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="4c2b6-192">Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="4c2b6-193">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="4c2b6-194">Considerações de segurança</span><span class="sxs-lookup"><span data-stu-id="4c2b6-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="4c2b6-195">Validação</span><span class="sxs-lookup"><span data-stu-id="4c2b6-195">Validation</span></span>](#validation)

<span data-ttu-id="4c2b6-196">Ao carregar arquivos usando a associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile>o método de ação pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="4c2b6-197">Um único <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="4c2b6-198">Qualquer uma das seguintes coleções que representam vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="4c2b6-199">[Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="4c2b6-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="4c2b6-200">A Associação corresponde aos arquivos de formulário por nome.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-200">Binding matches form files by name.</span></span> <span data-ttu-id="4c2b6-201">Por exemplo, o valor `name` HTML in `<input type="file" name="formFile">` deve corresponder ao parâmetro/Propriedade do C# associado`FormFile`().</span><span class="sxs-lookup"><span data-stu-id="4c2b6-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="4c2b6-202">Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="4c2b6-203">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-203">The following example:</span></span>

* <span data-ttu-id="4c2b6-204">Executa um loop em um ou mais arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="4c2b6-205">Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="4c2b6-206">Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="4c2b6-207">Retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-207">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="4c2b6-208">Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="4c2b6-209">No exemplo a seguir, o caminho é obtido da configuração:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-209">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="4c2b6-210">O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="4c2b6-211">Se o nome do arquivo não for fornecido <xref:System.UnauthorizedAccessException> , um será lançado no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="4c2b6-212">Os arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile> a técnica são armazenados em buffer na memória ou no disco no servidor antes do processamento.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="4c2b6-213">Dentro do método de ação, <xref:Microsoft.AspNetCore.Http.IFormFile> o conteúdo é acessível como <xref:System.IO.Stream>um.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="4c2b6-214">Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="4c2b6-215">Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="4c2b6-216">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="4c2b6-217">O limite de 65.535 arquivos é um limite por servidor.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="4c2b6-218">Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="4c2b6-219">Função GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="4c2b6-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="4c2b6-220">Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados</span><span class="sxs-lookup"><span data-stu-id="4c2b6-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="4c2b6-221">Para armazenar dados de arquivo binário em um banco [Entity Framework](/ef/core/index)de dado usando Entity Framework <xref:System.Byte> , defina uma propriedade de matriz na entidade:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="4c2b6-222">Especifique uma propriedade de modelo de página para a classe que <xref:Microsoft.AspNetCore.Http.IFormFile>inclui um:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="4c2b6-223"><xref:Microsoft.AspNetCore.Http.IFormFile>pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="4c2b6-224">O exemplo anterior usa uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="4c2b6-225">O `FileUpload` é usado no formulário Razor páginas:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-225">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="4c2b6-226">Quando o formulário for Postado no servidor, copie o para <xref:Microsoft.AspNetCore.Http.IFormFile> um fluxo e salve-o como uma matriz de bytes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="4c2b6-227">No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-227">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="4c2b6-228">O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="4c2b6-229">*Páginas/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4c2b6-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="4c2b6-230">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="4c2b6-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="4c2b6-231">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="4c2b6-232">Não confie ou confie na `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="4c2b6-233">A `FileName` propriedade só deve ser usada para fins de exibição e somente após a codificação HTML.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="4c2b6-234">Os exemplos fornecidos não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="4c2b6-235">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="4c2b6-236">Considerações de segurança</span><span class="sxs-lookup"><span data-stu-id="4c2b6-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="4c2b6-237">Validação</span><span class="sxs-lookup"><span data-stu-id="4c2b6-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="4c2b6-238">Carregar arquivos grandes com streaming</span><span class="sxs-lookup"><span data-stu-id="4c2b6-238">Upload large files with streaming</span></span>

<span data-ttu-id="4c2b6-239">O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="4c2b6-240">O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="4c2b6-241">Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="4c2b6-242">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="4c2b6-243">Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="4c2b6-244">A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie ( `GenerateAntiforgeryTokenCookieAttribute` por meio do atributo).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="4c2b6-245">O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="4c2b6-246">O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="4c2b6-247">No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros aos modelos de aplicativo de página `/StreamedSingleFileUploadDb` do `/StreamedSingleFileUploadPhysical` e `Startup.ConfigureServices` no usando [ Razor convenções de páginas](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="4c2b6-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="4c2b6-248">Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="4c2b6-249">O método de ação funciona diretamente com `Request` a propriedade.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="4c2b6-250">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="4c2b6-251">Os dados de chave/valor são armazenados `KeyValueAccumulator`em um.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="4c2b6-252">Depois que as seções com várias partes forem lidas, `KeyValueAccumulator` o conteúdo do será usado para associar os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="4c2b6-253">O método `StreamingController.UploadDatabase` completo para streaming para um banco de dados com EF Core:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="4c2b6-254">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="4c2b6-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="4c2b6-255">O método `StreamingController.UploadPhysical` completo para streaming para um local físico:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="4c2b6-256">No aplicativo de exemplo, as verificações de validação são `FileHelpers.ProcessStreamedFile`tratadas pelo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="4c2b6-257">Validação</span><span class="sxs-lookup"><span data-stu-id="4c2b6-257">Validation</span></span>

<span data-ttu-id="4c2b6-258">A classe do `FileHelpers` aplicativo de exemplo demonstra várias verificações para carregamentos <xref:Microsoft.AspNetCore.Http.IFormFile> de arquivos em buffer e em fluxo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="4c2b6-259">Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivo em buffer no aplicativo de exemplo, consulte o `ProcessFormFile` método no arquivo *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="4c2b6-260">Para o processamento de arquivos transmitidos, `ProcessStreamedFile` consulte o método no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="4c2b6-261">Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="4c2b6-262">Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="4c2b6-263">Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não `FileHelpers` implemente a classe em um aplicativo de produção, a menos que você:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="4c2b6-264">Entenda totalmente a implementação.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="4c2b6-265">Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="4c2b6-266">**Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**</span><span class="sxs-lookup"><span data-stu-id="4c2b6-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="4c2b6-267">Validação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-267">Content validation</span></span>

<span data-ttu-id="4c2b6-268">**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**</span><span class="sxs-lookup"><span data-stu-id="4c2b6-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="4c2b6-269">A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="4c2b6-270">Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="4c2b6-271">Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="4c2b6-272">Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="4c2b6-273">Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="4c2b6-274">Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="4c2b6-275">Validação de extensão de arquivo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-275">File extension validation</span></span>

<span data-ttu-id="4c2b6-276">A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="4c2b6-277">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="4c2b6-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="4c2b6-278">Validação de assinatura de arquivo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-278">File signature validation</span></span>

<span data-ttu-id="4c2b6-279">A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="4c2b6-280">Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="4c2b6-281">O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="4c2b6-282">No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="4c2b6-283">Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="4c2b6-284">Segurança de nome de arquivo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-284">File name security</span></span>

<span data-ttu-id="4c2b6-285">Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="4c2b6-286">Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="4c2b6-287">o HTML automaticamente codifica valores de propriedade para exibição.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-287"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="4c2b6-288">O código a seguir é seguro para usar:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="4c2b6-289">Fora do Razor, sempre <xref:System.Net.WebUtility.HtmlEncode*> nome do arquivo o conteúdo da solicitação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="4c2b6-290">Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="4c2b6-291">Forneça lógica adicional para atender às especificações do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="4c2b6-292">Validação de tamanho</span><span class="sxs-lookup"><span data-stu-id="4c2b6-292">Size validation</span></span>

<span data-ttu-id="4c2b6-293">Limite o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="4c2b6-294">No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="4c2b6-295">O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="4c2b6-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="4c2b6-296">O `FileSizeLimit` é injetado em `PageModel` classes:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="4c2b6-297">Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="4c2b6-298">Coincidir valor do atributo de nome com o nome do parâmetro do método POST</span><span class="sxs-lookup"><span data-stu-id="4c2b6-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="4c2b6-299">Em nãoRazor formulários que postam dados de formulário ou usam o `FormData` JavaScript diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="4c2b6-300">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-300">In the following example:</span></span>

* <span data-ttu-id="4c2b6-301">Ao usar um `<input>` elemento, o `name` atributo é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="4c2b6-302">Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="4c2b6-303">Use um nome correspondente para o parâmetro do método C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="4c2b6-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="4c2b6-304">Para um Razor método de manipulador de página `Upload`de páginas chamado:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="4c2b6-305">Para um método de ação do controlador de POSTAgem MVC:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="4c2b6-306">Configuração de servidor e aplicativo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="4c2b6-307">Limite de comprimento de corpo com várias partes</span><span class="sxs-lookup"><span data-stu-id="4c2b6-307">Multipart body length limit</span></span>

<span data-ttu-id="4c2b6-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>define o limite para o comprimento de cada corpo com diversas partes.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="4c2b6-309">As seções de formulário que excedem esse <xref:System.IO.InvalidDataException> limite lançam um quando analisado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="4c2b6-310">O padrão é 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="4c2b6-311">Personalize o limite usando a <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> configuração em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="4c2b6-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="4c2b6-313">Em um Razor aplicativo de páginas, aplique o filtro com [convention](xref:razor-pages/razor-pages-conventions) uma convenção `Startup.ConfigureServices`em:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    });
```

<span data-ttu-id="4c2b6-314">Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="4c2b6-315">Tamanho máximo do corpo de solicitação do Kestrel</span><span class="sxs-lookup"><span data-stu-id="4c2b6-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="4c2b6-316">Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="4c2b6-317">Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="4c2b6-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="4c2b6-319">Em um Razor aplicativo de páginas, aplique o filtro com [convention](xref:razor-pages/razor-pages-conventions) uma convenção `Startup.ConfigureServices`em:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    });
```

<span data-ttu-id="4c2b6-320">Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="4c2b6-321">O `RequestSizeLimitAttribute` também pode ser aplicado usando a [`@attribute`](xref:mvc/views/razor#attribute) Razor diretiva:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="4c2b6-322">Outros limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="4c2b6-322">Other Kestrel limits</span></span>

<span data-ttu-id="4c2b6-323">Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="4c2b6-324">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="4c2b6-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="4c2b6-325">Taxas de dados de solicitação e resposta</span><span class="sxs-lookup"><span data-stu-id="4c2b6-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="4c2b6-326">Limite de comprimento de conteúdo do IIS</span><span class="sxs-lookup"><span data-stu-id="4c2b6-326">IIS content length limit</span></span>

<span data-ttu-id="4c2b6-327">O limite de solicitação padrão`maxAllowedContentLength`() é 30 milhões bytes, que é de aproximadamente 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="4c2b6-328">Personalize o limite no arquivo *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="4c2b6-328">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="4c2b6-329">Essa configuração só se aplica ao IIS.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-329">This setting only applies to IIS.</span></span> <span data-ttu-id="4c2b6-330">Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="4c2b6-331">Para obter mais informações, consulte [limites \<de solicitação>de requestLimits ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="4c2b6-332">As limitações no módulo ASP.NET Core ou na presença do módulo filtragem de solicitações do IIS podem limitar os carregamentos a dois ou 4 GB.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="4c2b6-333">Para obter mais informações, consulte [não é possível carregar o arquivo com mais de 2GB de tamanho (dotNet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="4c2b6-334">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="4c2b6-334">Troubleshoot</span></span>

<span data-ttu-id="4c2b6-335">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="4c2b6-336">Erro não encontrado quando implantado em um servidor IIS</span><span class="sxs-lookup"><span data-stu-id="4c2b6-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="4c2b6-337">O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="4c2b6-338">Para obter mais informações sobre como aumentar o limite, consulte a seção [limite de tamanho de conteúdo do IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="4c2b6-339">Falha de conexão</span><span class="sxs-lookup"><span data-stu-id="4c2b6-339">Connection failure</span></span>

<span data-ttu-id="4c2b6-340">Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="4c2b6-341">Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="4c2b6-342">Os limites de conexão de cliente Kestrel também podem exigir ajuste.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="4c2b6-343">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="4c2b6-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="4c2b6-344">Se o controlador estiver aceitando arquivos carregados <xref:Microsoft.AspNetCore.Http.IFormFile> usando, mas o `null`valor for, confirme se o formulário HTML está `enctype` especificando um `multipart/form-data`valor de.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="4c2b6-345">Se esse atributo não estiver definido no `<form>` elemento, o upload do arquivo não ocorrerá e <xref:Microsoft.AspNetCore.Http.IFormFile> todos os `null`argumentos associados serão.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="4c2b6-346">Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="4c2b6-347">O fluxo era muito longo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-347">Stream was too long</span></span>

<span data-ttu-id="4c2b6-348">Os exemplos neste tópico dependem <xref:System.IO.MemoryStream> de manter o conteúdo do arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="4c2b6-349">O limite de tamanho de `MemoryStream` um `int.MaxValue`é.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="4c2b6-350">Se o cenário de upload de arquivo do aplicativo exigir a retenção de conteúdo de arquivo com mais de 50 MB, use uma abordagem alternativa `MemoryStream` que não dependa de um único para manter o conteúdo de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4c2b6-351">O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="4c2b6-352">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4c2b6-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="4c2b6-353">Considerações de segurança</span><span class="sxs-lookup"><span data-stu-id="4c2b6-353">Security considerations</span></span>

<span data-ttu-id="4c2b6-354">Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="4c2b6-355">Os invasores podem tentar:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="4c2b6-356">Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="4c2b6-357">Carregar vírus ou malware.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="4c2b6-358">Comprometa redes e servidores de outras maneiras.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="4c2b6-359">As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="4c2b6-360">Carregue arquivos em uma área de carregamento de arquivo dedicada, preferencialmente para uma unidade que não seja do sistema.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="4c2b6-361">Um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="4c2b6-362">Desabilite as permissões de execução no local de carregamento do arquivo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4c2b6-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="4c2b6-363">**Não** persiste arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4c2b6-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="4c2b6-364">Use um nome de arquivo seguro determinado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="4c2b6-365">Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado. &dagger; O HTML codifica o nome de arquivo não confiável ao exibi-lo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="4c2b6-366">Por exemplo, registrar em log o nome do arquivo ou exibindo na interface do usuário (Razor a saída de código HTML automaticamente).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="4c2b6-367">Permitir somente extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4c2b6-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="4c2b6-368">Verifique se as verificações do lado do cliente são executadas no servidor. &dagger; As verificações no lado do cliente são fáceis de evitar.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="4c2b6-369">Verifique o tamanho de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="4c2b6-370">Defina um limite de tamanho máximo para evitar carregamentos grandes.&dagger;</span><span class="sxs-lookup"><span data-stu-id="4c2b6-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="4c2b6-371">Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="4c2b6-372">**Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**</span><span class="sxs-lookup"><span data-stu-id="4c2b6-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="4c2b6-373">&dagger;O aplicativo de exemplo demonstra uma abordagem que atende aos critérios.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="4c2b6-374">Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="4c2b6-375">Assuma completamente o controle de um sistema.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="4c2b6-376">Sobrecarregar um sistema com o resultado que o sistema falha.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="4c2b6-377">Comprometer dados do sistema ou de usuários.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="4c2b6-378">Aplique graffiti a uma interface do usuário pública.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="4c2b6-379">Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="4c2b6-380">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="4c2b6-380">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="4c2b6-381">Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários</span><span class="sxs-lookup"><span data-stu-id="4c2b6-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="4c2b6-382">Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="4c2b6-383">Cenários de armazenamento</span><span class="sxs-lookup"><span data-stu-id="4c2b6-383">Storage scenarios</span></span>

<span data-ttu-id="4c2b6-384">As opções de armazenamento comuns para arquivos incluem:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-384">Common storage options for files include:</span></span>

* <span data-ttu-id="4c2b6-385">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="4c2b6-385">Database</span></span>

  * <span data-ttu-id="4c2b6-386">Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="4c2b6-387">Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="4c2b6-388">Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="4c2b6-389">Armazenamento físico (sistema de arquivos ou compartilhamento de rede)</span><span class="sxs-lookup"><span data-stu-id="4c2b6-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="4c2b6-390">Para carregamentos de arquivos grandes:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-390">For large file uploads:</span></span>
    * <span data-ttu-id="4c2b6-391">Os limites de banco de dados podem restringir o tamanho do carregamento.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="4c2b6-392">O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="4c2b6-393">O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="4c2b6-394">O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="4c2b6-395">**Nunca conceda a permissão EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="4c2b6-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="4c2b6-396">Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="4c2b6-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="4c2b6-397">Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="4c2b6-398">Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="4c2b6-399">Para obter mais informações, consulte [início rápido: usar o .net para criar um blob no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="4c2b6-400">O tópico demonstra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, mas <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> pode ser usado para salvar um <xref:System.IO.FileStream> armazenamento de BLOBs ao trabalhar com <xref:System.IO.Stream>um.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="4c2b6-401">Cenários de upload de arquivo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-401">File upload scenarios</span></span>

<span data-ttu-id="4c2b6-402">Duas abordagens gerais para carregar arquivos são buffer e streaming.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="4c2b6-403">**de resposta**</span><span class="sxs-lookup"><span data-stu-id="4c2b6-403">**Buffering**</span></span>

<span data-ttu-id="4c2b6-404">O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile>, que é uma representação em C# do arquivo usado para processar ou salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="4c2b6-405">Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="4c2b6-406">Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="4c2b6-407">Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="4c2b6-408">Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="4c2b6-409">O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="4c2b6-410">Armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="4c2b6-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="4c2b6-411">Backup de banco de dados</span><span class="sxs-lookup"><span data-stu-id="4c2b6-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="4c2b6-412">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="4c2b6-412">**Streaming**</span></span>

<span data-ttu-id="4c2b6-413">O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="4c2b6-414">O streaming não melhora significativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="4c2b6-415">O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="4c2b6-416">O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="4c2b6-417">Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="4c2b6-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="4c2b6-418">Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="4c2b6-419">O exemplo a seguir demonstra o uso de Razor um formulário de páginas para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):</span><span class="sxs-lookup"><span data-stu-id="4c2b6-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="4c2b6-420">O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="4c2b6-421">O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="4c2b6-422">Não há validação.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-422">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="4c2b6-423">Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="4c2b6-424">Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="4c2b6-425">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="4c2b6-426">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="4c2b6-426">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="4c2b6-427">Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de`enctype`codificação ( `multipart/form-data`) de.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="4c2b6-428">Para um `files` elemento input oferecer suporte ao carregamento de vários arquivos, forneça `multiple` o atributo no `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="4c2b6-429">Os arquivos individuais carregados no servidor podem ser acessados por meio de <xref:Microsoft.AspNetCore.Http.IFormFile>Associação de [modelo](xref:mvc/models/model-binding) usando.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="4c2b6-430">O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="4c2b6-431">**Não** use a `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> outros para exibição e registro em log.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="4c2b6-432">Ao exibir ou registrar em log, o HTML codifica o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="4c2b6-433">Um invasor pode fornecer um nome de arquivo mal-intencionado, incluindo caminhos completos ou caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="4c2b6-434">Os aplicativos devem:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-434">Applications should:</span></span>
>
> * <span data-ttu-id="4c2b6-435">Remova o caminho do nome de arquivo fornecido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="4c2b6-436">Salve o nome de arquivo com codificação em HTML e removido por caminho para a interface do usuário ou log.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="4c2b6-437">Gere um novo nome de arquivo aleatório para armazenamento.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="4c2b6-438">O código a seguir remove o caminho do nome do arquivo:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="4c2b6-439">Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="4c2b6-440">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="4c2b6-441">Considerações de segurança</span><span class="sxs-lookup"><span data-stu-id="4c2b6-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="4c2b6-442">Validação</span><span class="sxs-lookup"><span data-stu-id="4c2b6-442">Validation</span></span>](#validation)

<span data-ttu-id="4c2b6-443">Ao carregar arquivos usando a associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile>o método de ação pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="4c2b6-444">Um único <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="4c2b6-445">Qualquer uma das seguintes coleções que representam vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="4c2b6-446">[Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="4c2b6-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="4c2b6-447">A Associação corresponde aos arquivos de formulário por nome.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-447">Binding matches form files by name.</span></span> <span data-ttu-id="4c2b6-448">Por exemplo, o valor `name` HTML in `<input type="file" name="formFile">` deve corresponder ao parâmetro/Propriedade do C# associado`FormFile`().</span><span class="sxs-lookup"><span data-stu-id="4c2b6-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="4c2b6-449">Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="4c2b6-450">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-450">The following example:</span></span>

* <span data-ttu-id="4c2b6-451">Executa um loop em um ou mais arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="4c2b6-452">Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="4c2b6-453">Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="4c2b6-454">Retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-454">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="4c2b6-455">Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="4c2b6-456">No exemplo a seguir, o caminho é obtido da configuração:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-456">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="4c2b6-457">O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="4c2b6-458">Se o nome do arquivo não for fornecido <xref:System.UnauthorizedAccessException> , um será lançado no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="4c2b6-459">Os arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile> a técnica são armazenados em buffer na memória ou no disco no servidor antes do processamento.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="4c2b6-460">Dentro do método de ação, <xref:Microsoft.AspNetCore.Http.IFormFile> o conteúdo é acessível como <xref:System.IO.Stream>um.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="4c2b6-461">Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="4c2b6-462">Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="4c2b6-463">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="4c2b6-464">O limite de 65.535 arquivos é um limite por servidor.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="4c2b6-465">Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="4c2b6-466">Função GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="4c2b6-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="4c2b6-467">Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados</span><span class="sxs-lookup"><span data-stu-id="4c2b6-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="4c2b6-468">Para armazenar dados de arquivo binário em um banco [Entity Framework](/ef/core/index)de dado usando Entity Framework <xref:System.Byte> , defina uma propriedade de matriz na entidade:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="4c2b6-469">Especifique uma propriedade de modelo de página para a classe que <xref:Microsoft.AspNetCore.Http.IFormFile>inclui um:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="4c2b6-470"><xref:Microsoft.AspNetCore.Http.IFormFile>pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="4c2b6-471">O exemplo anterior usa uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="4c2b6-472">O `FileUpload` é usado no formulário Razor páginas:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-472">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="4c2b6-473">Quando o formulário for Postado no servidor, copie o para <xref:Microsoft.AspNetCore.Http.IFormFile> um fluxo e salve-o como uma matriz de bytes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="4c2b6-474">No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-474">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="4c2b6-475">O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="4c2b6-476">*Páginas/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4c2b6-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="4c2b6-477">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="4c2b6-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="4c2b6-478">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="4c2b6-479">Não confie ou confie na `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="4c2b6-480">A `FileName` propriedade só deve ser usada para fins de exibição e somente após a codificação HTML.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="4c2b6-481">Os exemplos fornecidos não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="4c2b6-482">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="4c2b6-483">Considerações de segurança</span><span class="sxs-lookup"><span data-stu-id="4c2b6-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="4c2b6-484">Validação</span><span class="sxs-lookup"><span data-stu-id="4c2b6-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="4c2b6-485">Carregar arquivos grandes com streaming</span><span class="sxs-lookup"><span data-stu-id="4c2b6-485">Upload large files with streaming</span></span>

<span data-ttu-id="4c2b6-486">O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="4c2b6-487">O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="4c2b6-488">Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="4c2b6-489">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="4c2b6-490">Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="4c2b6-491">A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie ( `GenerateAntiforgeryTokenCookieAttribute` por meio do atributo).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="4c2b6-492">O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="4c2b6-493">O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="4c2b6-494">No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros aos modelos de aplicativo de página `/StreamedSingleFileUploadDb` do `/StreamedSingleFileUploadPhysical` e `Startup.ConfigureServices` no usando [ Razor convenções de páginas](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="4c2b6-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="4c2b6-495">Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="4c2b6-496">O método de ação funciona diretamente com `Request` a propriedade.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="4c2b6-497">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="4c2b6-498">Os dados de chave/valor são armazenados `KeyValueAccumulator`em um.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="4c2b6-499">Depois que as seções com várias partes forem lidas, `KeyValueAccumulator` o conteúdo do será usado para associar os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="4c2b6-500">O método `StreamingController.UploadDatabase` completo para streaming para um banco de dados com EF Core:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="4c2b6-501">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="4c2b6-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="4c2b6-502">O método `StreamingController.UploadPhysical` completo para streaming para um local físico:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="4c2b6-503">No aplicativo de exemplo, as verificações de validação são `FileHelpers.ProcessStreamedFile`tratadas pelo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="4c2b6-504">Validação</span><span class="sxs-lookup"><span data-stu-id="4c2b6-504">Validation</span></span>

<span data-ttu-id="4c2b6-505">A classe do `FileHelpers` aplicativo de exemplo demonstra várias verificações para carregamentos <xref:Microsoft.AspNetCore.Http.IFormFile> de arquivos em buffer e em fluxo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="4c2b6-506">Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivo em buffer no aplicativo de exemplo, consulte o `ProcessFormFile` método no arquivo *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="4c2b6-507">Para o processamento de arquivos transmitidos, `ProcessStreamedFile` consulte o método no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="4c2b6-508">Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="4c2b6-509">Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="4c2b6-510">Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não `FileHelpers` implemente a classe em um aplicativo de produção, a menos que você:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="4c2b6-511">Entenda totalmente a implementação.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="4c2b6-512">Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="4c2b6-513">**Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**</span><span class="sxs-lookup"><span data-stu-id="4c2b6-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="4c2b6-514">Validação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-514">Content validation</span></span>

<span data-ttu-id="4c2b6-515">**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**</span><span class="sxs-lookup"><span data-stu-id="4c2b6-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="4c2b6-516">A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="4c2b6-517">Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="4c2b6-518">Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="4c2b6-519">Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="4c2b6-520">Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="4c2b6-521">Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="4c2b6-522">Validação de extensão de arquivo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-522">File extension validation</span></span>

<span data-ttu-id="4c2b6-523">A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="4c2b6-524">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="4c2b6-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="4c2b6-525">Validação de assinatura de arquivo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-525">File signature validation</span></span>

<span data-ttu-id="4c2b6-526">A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="4c2b6-527">Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="4c2b6-528">O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="4c2b6-529">No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="4c2b6-530">Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="4c2b6-531">Segurança de nome de arquivo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-531">File name security</span></span>

<span data-ttu-id="4c2b6-532">Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="4c2b6-533">Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="4c2b6-534">o HTML automaticamente codifica valores de propriedade para exibição.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-534"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="4c2b6-535">O código a seguir é seguro para usar:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="4c2b6-536">Fora do Razor, sempre <xref:System.Net.WebUtility.HtmlEncode*> nome do arquivo o conteúdo da solicitação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="4c2b6-537">Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="4c2b6-538">Forneça lógica adicional para atender às especificações do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="4c2b6-539">Validação de tamanho</span><span class="sxs-lookup"><span data-stu-id="4c2b6-539">Size validation</span></span>

<span data-ttu-id="4c2b6-540">Limite o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="4c2b6-541">No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="4c2b6-542">O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="4c2b6-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="4c2b6-543">O `FileSizeLimit` é injetado em `PageModel` classes:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="4c2b6-544">Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="4c2b6-545">Coincidir valor do atributo de nome com o nome do parâmetro do método POST</span><span class="sxs-lookup"><span data-stu-id="4c2b6-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="4c2b6-546">Em nãoRazor formulários que postam dados de formulário ou usam o `FormData` JavaScript diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="4c2b6-547">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-547">In the following example:</span></span>

* <span data-ttu-id="4c2b6-548">Ao usar um `<input>` elemento, o `name` atributo é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="4c2b6-549">Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="4c2b6-550">Use um nome correspondente para o parâmetro do método C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="4c2b6-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="4c2b6-551">Para um Razor método de manipulador de página `Upload`de páginas chamado:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="4c2b6-552">Para um método de ação do controlador de POSTAgem MVC:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="4c2b6-553">Configuração de servidor e aplicativo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="4c2b6-554">Limite de comprimento de corpo com várias partes</span><span class="sxs-lookup"><span data-stu-id="4c2b6-554">Multipart body length limit</span></span>

<span data-ttu-id="4c2b6-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>define o limite para o comprimento de cada corpo com diversas partes.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="4c2b6-556">As seções de formulário que excedem esse <xref:System.IO.InvalidDataException> limite lançam um quando analisado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="4c2b6-557">O padrão é 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="4c2b6-558">Personalize o limite usando a <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> configuração em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="4c2b6-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="4c2b6-560">Em um Razor aplicativo de páginas, aplique o filtro com [convention](xref:razor-pages/razor-pages-conventions) uma convenção `Startup.ConfigureServices`em:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="4c2b6-561">Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="4c2b6-562">Tamanho máximo do corpo de solicitação do Kestrel</span><span class="sxs-lookup"><span data-stu-id="4c2b6-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="4c2b6-563">Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="4c2b6-564">Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="4c2b6-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="4c2b6-566">Em um Razor aplicativo de páginas, aplique o filtro com [convention](xref:razor-pages/razor-pages-conventions) uma convenção `Startup.ConfigureServices`em:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="4c2b6-567">Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="4c2b6-568">Outros limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="4c2b6-568">Other Kestrel limits</span></span>

<span data-ttu-id="4c2b6-569">Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="4c2b6-570">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="4c2b6-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="4c2b6-571">Taxas de dados de solicitação e resposta</span><span class="sxs-lookup"><span data-stu-id="4c2b6-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="4c2b6-572">Limite de comprimento de conteúdo do IIS</span><span class="sxs-lookup"><span data-stu-id="4c2b6-572">IIS content length limit</span></span>

<span data-ttu-id="4c2b6-573">O limite de solicitação padrão`maxAllowedContentLength`() é 30 milhões bytes, que é de aproximadamente 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="4c2b6-574">Personalize o limite no arquivo *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="4c2b6-574">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="4c2b6-575">Essa configuração só se aplica ao IIS.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-575">This setting only applies to IIS.</span></span> <span data-ttu-id="4c2b6-576">Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="4c2b6-577">Para obter mais informações, consulte [limites \<de solicitação>de requestLimits ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="4c2b6-578">As limitações no módulo ASP.NET Core ou na presença do módulo filtragem de solicitações do IIS podem limitar os carregamentos a dois ou 4 GB.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="4c2b6-579">Para obter mais informações, consulte [não é possível carregar o arquivo com mais de 2GB de tamanho (dotNet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="4c2b6-580">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="4c2b6-580">Troubleshoot</span></span>

<span data-ttu-id="4c2b6-581">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="4c2b6-582">Erro não encontrado quando implantado em um servidor IIS</span><span class="sxs-lookup"><span data-stu-id="4c2b6-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="4c2b6-583">O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="4c2b6-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="4c2b6-584">Para obter mais informações sobre como aumentar o limite, consulte a seção [limite de tamanho de conteúdo do IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="4c2b6-585">Falha de conexão</span><span class="sxs-lookup"><span data-stu-id="4c2b6-585">Connection failure</span></span>

<span data-ttu-id="4c2b6-586">Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="4c2b6-587">Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="4c2b6-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="4c2b6-588">Os limites de conexão de cliente Kestrel também podem exigir ajuste.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="4c2b6-589">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="4c2b6-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="4c2b6-590">Se o controlador estiver aceitando arquivos carregados <xref:Microsoft.AspNetCore.Http.IFormFile> usando, mas o `null`valor for, confirme se o formulário HTML está `enctype` especificando um `multipart/form-data`valor de.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="4c2b6-591">Se esse atributo não estiver definido no `<form>` elemento, o upload do arquivo não ocorrerá e <xref:Microsoft.AspNetCore.Http.IFormFile> todos os `null`argumentos associados serão.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="4c2b6-592">Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="4c2b6-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="4c2b6-593">O fluxo era muito longo</span><span class="sxs-lookup"><span data-stu-id="4c2b6-593">Stream was too long</span></span>

<span data-ttu-id="4c2b6-594">Os exemplos neste tópico dependem <xref:System.IO.MemoryStream> de manter o conteúdo do arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="4c2b6-595">O limite de tamanho de `MemoryStream` um `int.MaxValue`é.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="4c2b6-596">Se o cenário de upload de arquivo do aplicativo exigir a retenção de conteúdo de arquivo com mais de 50 MB, use uma abordagem alternativa `MemoryStream` que não dependa de um único para manter o conteúdo de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="4c2b6-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="4c2b6-597">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4c2b6-597">Additional resources</span></span>

* [<span data-ttu-id="4c2b6-598">Descarga de solicitação de conexão HTTP</span><span class="sxs-lookup"><span data-stu-id="4c2b6-598">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* <span data-ttu-id="4c2b6-599">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="4c2b6-599">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="4c2b6-600">Segurança do Azure: estrutura de segurança: validação de entrada | Mitigações</span><span class="sxs-lookup"><span data-stu-id="4c2b6-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="4c2b6-601">Padrões de design de nuvem do Azure: padrão valet Key</span><span class="sxs-lookup"><span data-stu-id="4c2b6-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
