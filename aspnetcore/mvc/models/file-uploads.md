---
title: Carregar arquivos no ASP.NET Core
author: rick-anderson
description: Como usar o model binding e o streaming para carregar arquivos no ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2020
uid: mvc/models/file-uploads
ms.openlocfilehash: e25da0b3867181a16a4636768f36c148a152dd23
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661731"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="cd535-103">Carregar arquivos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd535-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="cd535-104">Por [Steve Smith](https://ardalis.com/) e Rutger [Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="cd535-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd535-105">ASP.NET Core suporta o upload de um ou mais arquivos usando vinculação de modelo tampão para arquivos menores e streaming não tamponado para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="cd535-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="cd535-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cd535-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="cd535-107">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="cd535-107">Security considerations</span></span>

<span data-ttu-id="cd535-108">Tenha cuidado ao fornecer aos usuários a capacidade de carregar arquivos para um servidor.</span><span class="sxs-lookup"><span data-stu-id="cd535-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="cd535-109">Os atacantes podem tentar:</span><span class="sxs-lookup"><span data-stu-id="cd535-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="cd535-110">Execute ataques [de negação de serviço.](/windows-hardware/drivers/ifs/denial-of-service)</span><span class="sxs-lookup"><span data-stu-id="cd535-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="cd535-111">Envie vírus ou malware.</span><span class="sxs-lookup"><span data-stu-id="cd535-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="cd535-112">Comprometer redes e servidores de outras formas.</span><span class="sxs-lookup"><span data-stu-id="cd535-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="cd535-113">As etapas de segurança que reduzem a probabilidade de um ataque bem sucedido são:</span><span class="sxs-lookup"><span data-stu-id="cd535-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="cd535-114">Faça upload de arquivos para uma área de upload de arquivos dedicada, de preferência para uma unidade não-sistema.</span><span class="sxs-lookup"><span data-stu-id="cd535-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="cd535-115">Um local dedicado facilita a imposição de restrições de segurança em arquivos enviados.</span><span class="sxs-lookup"><span data-stu-id="cd535-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="cd535-116">Desativar as permissões de execução no local de upload do arquivo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cd535-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="cd535-117">**Não** persista arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cd535-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="cd535-118">Use um nome de arquivo seguro determinado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cd535-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="cd535-119">Não use um nome de arquivo fornecido pelo usuário ou o nome do arquivo não confiável do arquivo carregado. &dagger; HTML codificar o nome do arquivo não confiável ao exibi-lo.</span><span class="sxs-lookup"><span data-stu-id="cd535-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="cd535-120">Por exemplo, registrar o nome do arquivo ou exibir em UI (Razor automaticamente HTML codifica a saída).</span><span class="sxs-lookup"><span data-stu-id="cd535-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="cd535-121">Permitir apenas extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cd535-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="cd535-122">Verifique se as verificações do lado do cliente são realizadas no servidor. &dagger; Verificações do lado do cliente são fáceis de contornar.</span><span class="sxs-lookup"><span data-stu-id="cd535-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="cd535-123">Verifique o tamanho de um arquivo enviado.</span><span class="sxs-lookup"><span data-stu-id="cd535-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="cd535-124">Defina um limite máximo de tamanho para evitar grandes uploads.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cd535-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="cd535-125">Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="cd535-126">**Execute um scanner de vírus/malware no conteúdo carregado antes que o arquivo seja armazenado.**</span><span class="sxs-lookup"><span data-stu-id="cd535-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="cd535-127">&dagger;O aplicativo de amostra demonstra uma abordagem que atende aos critérios.</span><span class="sxs-lookup"><span data-stu-id="cd535-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="cd535-128">Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:</span><span class="sxs-lookup"><span data-stu-id="cd535-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="cd535-129">Ganhe completamente o controle de um sistema.</span><span class="sxs-lookup"><span data-stu-id="cd535-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="cd535-130">Sobrecarregue um sistema com o resultado que o sistema trava.</span><span class="sxs-lookup"><span data-stu-id="cd535-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="cd535-131">Comprometer dados do sistema ou de usuários.</span><span class="sxs-lookup"><span data-stu-id="cd535-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="cd535-132">Aplique pichações em uma ui pública.</span><span class="sxs-lookup"><span data-stu-id="cd535-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="cd535-133">Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="cd535-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="cd535-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="cd535-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="cd535-135">Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários</span><span class="sxs-lookup"><span data-stu-id="cd535-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="cd535-136">Para obter mais informações sobre a implementação de medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [Validação.](#validation)</span><span class="sxs-lookup"><span data-stu-id="cd535-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="cd535-137">Cenários de armazenamento</span><span class="sxs-lookup"><span data-stu-id="cd535-137">Storage scenarios</span></span>

<span data-ttu-id="cd535-138">As opções comuns de armazenamento para arquivos incluem:</span><span class="sxs-lookup"><span data-stu-id="cd535-138">Common storage options for files include:</span></span>

* <span data-ttu-id="cd535-139">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="cd535-139">Database</span></span>

  * <span data-ttu-id="cd535-140">Para pequenos uploads de arquivos, um banco de dados é muitas vezes mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).</span><span class="sxs-lookup"><span data-stu-id="cd535-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="cd535-141">Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, porque a recuperação de um registro de banco de dados para dados do usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de avatar).</span><span class="sxs-lookup"><span data-stu-id="cd535-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="cd535-142">Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="cd535-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="cd535-143">Armazenamento físico (sistema de arquivos ou compartilhamento de rede)</span><span class="sxs-lookup"><span data-stu-id="cd535-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="cd535-144">Para uploads de arquivos grandes:</span><span class="sxs-lookup"><span data-stu-id="cd535-144">For large file uploads:</span></span>
    * <span data-ttu-id="cd535-145">Os limites do banco de dados podem restringir o tamanho do upload.</span><span class="sxs-lookup"><span data-stu-id="cd535-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="cd535-146">O armazenamento físico é muitas vezes menos econômico do que o armazenamento em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cd535-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="cd535-147">O armazenamento físico é potencialmente mais barato do que o uso de um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="cd535-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="cd535-148">O processo do aplicativo deve ter permissões de leitura e gravação para o local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="cd535-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="cd535-149">**Nunca conceda permissão de execução.**</span><span class="sxs-lookup"><span data-stu-id="cd535-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="cd535-150">Serviço de armazenamento de dados (por exemplo, [Armazenamento Azure Blob)](https://azure.microsoft.com/services/storage/blobs/)</span><span class="sxs-lookup"><span data-stu-id="cd535-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="cd535-151">Os serviços geralmente oferecem maior escalabilidade e resiliência sobre soluções no local que geralmente estão sujeitas a pontos únicos de falha.</span><span class="sxs-lookup"><span data-stu-id="cd535-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="cd535-152">Os serviços são potencialmente mais baixos em grandes cenários de infra-estrutura de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="cd535-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="cd535-153">Para obter mais informações, consulte [Quickstart: Use .NET para criar uma bolha no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="cd535-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="cd535-154">Cenários de upload de arquivos</span><span class="sxs-lookup"><span data-stu-id="cd535-154">File upload scenarios</span></span>

<span data-ttu-id="cd535-155">Duas abordagens gerais para upload de arquivos são buffering e streaming.</span><span class="sxs-lookup"><span data-stu-id="cd535-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="cd535-156">**de resposta**</span><span class="sxs-lookup"><span data-stu-id="cd535-156">**Buffering**</span></span>

<span data-ttu-id="cd535-157">O arquivo inteiro é <xref:Microsoft.AspNetCore.Http.IFormFile>lido em uma representação C# do arquivo usado para processar ou salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="cd535-158">Os recursos (disco, memória) usados pelos uploads de arquivos dependem do número e tamanho dos uploads de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="cd535-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="cd535-159">Se um aplicativo tentar buffer de muitos uploads, o site falha quando fica sem memória ou espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="cd535-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="cd535-160">Se o tamanho ou frequência dos uploads de arquivos estiver esgotando os recursos do aplicativo, use o streaming.</span><span class="sxs-lookup"><span data-stu-id="cd535-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="cd535-161">Qualquer único arquivo tamponado superior a 64 KB é movido da memória para um arquivo temporário no disco.</span><span class="sxs-lookup"><span data-stu-id="cd535-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="cd535-162">O buffer de pequenos arquivos é coberto nas seguintes seções deste tópico:</span><span class="sxs-lookup"><span data-stu-id="cd535-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="cd535-163">Armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="cd535-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="cd535-164">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="cd535-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="cd535-165">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="cd535-165">**Streaming**</span></span>

<span data-ttu-id="cd535-166">O arquivo é recebido a partir de uma solicitação de várias partes e diretamente processado ou salvo pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cd535-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="cd535-167">O streaming não melhora significativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="cd535-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="cd535-168">O streaming reduz as demandas por memória ou espaço em disco ao carregar arquivos.</span><span class="sxs-lookup"><span data-stu-id="cd535-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="cd535-169">O streaming de arquivos grandes é coberto no Upload de arquivos grandes com a seção [de streaming.](#upload-large-files-with-streaming)</span><span class="sxs-lookup"><span data-stu-id="cd535-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="cd535-170">Carregar pequenos arquivos com modelo tampão vinculando ao armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="cd535-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="cd535-171">Para carregar pequenos arquivos, use um formulário multiparte ou construa uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cd535-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="cd535-172">O exemplo a seguir demonstra o uso de um formulário Razor Pages para carregar um único arquivo *(Páginas/BufferedSingleFileUploadPhysical.cshtml* no aplicativo de amostra):</span><span class="sxs-lookup"><span data-stu-id="cd535-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="cd535-173">O exemplo a seguir é análogo ao exemplo anterior, exceto que:</span><span class="sxs-lookup"><span data-stu-id="cd535-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="cd535-174">JavaScript's[(Fetch API)](https://developer.mozilla.org/docs/Web/API/Fetch_API)é usado para enviar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="cd535-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="cd535-175">Não há validação.</span><span class="sxs-lookup"><span data-stu-id="cd535-175">There's no validation.</span></span>

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

<span data-ttu-id="cd535-176">Para executar o formulário POST em JavaScript para clientes que [não suportam a API Fetch,](https://caniuse.com/#feat=fetch)use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="cd535-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="cd535-177">Use um Fetch Polyfill (por exemplo, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="cd535-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="cd535-178">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="cd535-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="cd535-179">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd535-179">For example:</span></span>

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

<span data-ttu-id="cd535-180">Para suportar uploads de arquivos, os formulários`enctype`HTML `multipart/form-data`devem especificar um tipo de codificação ( ) de .</span><span class="sxs-lookup"><span data-stu-id="cd535-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="cd535-181">Para `files` que um elemento de entrada `multiple` suporte o `<input>` upload de vários arquivos forneça o atributo no elemento:</span><span class="sxs-lookup"><span data-stu-id="cd535-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="cd535-182">Os arquivos individuais carregados para o servidor podem <xref:Microsoft.AspNetCore.Http.IFormFile>ser acessados através do Model [Binding](xref:mvc/models/model-binding) usando .</span><span class="sxs-lookup"><span data-stu-id="cd535-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="cd535-183">O aplicativo de exemplo demonstra vários uploads de arquivos tamponados para cenários de banco de dados e armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="cd535-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="cd535-184">**Não use** `FileName` a <xref:Microsoft.AspNetCore.Http.IFormFile> propriedade de outro além de exibição e registro.</span><span class="sxs-lookup"><span data-stu-id="cd535-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="cd535-185">Ao exibir ou registrar, HTML codifica rexa o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="cd535-186">Um invasor pode fornecer um nome de arquivo malicioso, incluindo caminhos completos ou caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="cd535-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="cd535-187">As aplicações devem:</span><span class="sxs-lookup"><span data-stu-id="cd535-187">Applications should:</span></span>
>
> * <span data-ttu-id="cd535-188">Remova o caminho do nome de arquivo fornecido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="cd535-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="cd535-189">Salve o nome de arquivo codificado por HTML e removido de caminho para UI ou registro.</span><span class="sxs-lookup"><span data-stu-id="cd535-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="cd535-190">Gerar um novo nome de arquivo aleatório para armazenamento.</span><span class="sxs-lookup"><span data-stu-id="cd535-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="cd535-191">O código a seguir remove o caminho do nome do arquivo:</span><span class="sxs-lookup"><span data-stu-id="cd535-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="cd535-192">Os exemplos fornecidos até agora não levam em conta considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="cd535-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="cd535-193">Informações adicionais são fornecidas pelas seguintes seções e pelo [aplicativo de amostra:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)</span><span class="sxs-lookup"><span data-stu-id="cd535-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="cd535-194">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="cd535-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="cd535-195">Validação</span><span class="sxs-lookup"><span data-stu-id="cd535-195">Validation</span></span>](#validation)

<span data-ttu-id="cd535-196">Ao carregar arquivos usando <xref:Microsoft.AspNetCore.Http.IFormFile>vinculação de modelo e, o método de ação pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="cd535-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="cd535-197">Um <xref:Microsoft.AspNetCore.Http.IFormFile>único.</span><span class="sxs-lookup"><span data-stu-id="cd535-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="cd535-198">Qualquer uma das seguintes coleções que representam vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="cd535-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="cd535-199">[Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="cd535-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="cd535-200">Correspondências de vinculação formam arquivos por nome.</span><span class="sxs-lookup"><span data-stu-id="cd535-200">Binding matches form files by name.</span></span> <span data-ttu-id="cd535-201">Por exemplo, `name` o `<input type="file" name="formFile">` valor HTML deve corresponder ao parâmetro`FormFile`C#/propriedade bound ( ).</span><span class="sxs-lookup"><span data-stu-id="cd535-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="cd535-202">Para obter mais informações, consulte o valor do [atributo nome match ao nome do parâmetro da](#match-name-attribute-value-to-parameter-name-of-post-method) seção do método POST.</span><span class="sxs-lookup"><span data-stu-id="cd535-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="cd535-203">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cd535-203">The following example:</span></span>

* <span data-ttu-id="cd535-204">Loops através de um ou mais arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="cd535-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="cd535-205">Usa [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="cd535-206">Salva os arquivos para o sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cd535-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="cd535-207">Retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="cd535-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="cd535-208">Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho.</span><span class="sxs-lookup"><span data-stu-id="cd535-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="cd535-209">No exemplo a seguir, o caminho é obtido a partir da configuração:</span><span class="sxs-lookup"><span data-stu-id="cd535-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="cd535-210">O caminho passado <xref:System.IO.FileStream> para o *deve* incluir o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="cd535-211">Se o nome do arquivo não <xref:System.UnauthorizedAccessException> for fornecido, um é jogado em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="cd535-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="cd535-212">Os arquivos carregados usando a <xref:Microsoft.AspNetCore.Http.IFormFile> técnica são protegidos na memória ou no disco no servidor antes do processamento.</span><span class="sxs-lookup"><span data-stu-id="cd535-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="cd535-213">Dentro do método <xref:Microsoft.AspNetCore.Http.IFormFile> de ação, <xref:System.IO.Stream>os conteúdos são acessíveis como um .</span><span class="sxs-lookup"><span data-stu-id="cd535-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="cd535-214">Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como [o armazenamento Azure Blob](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="cd535-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="cd535-215">Para outro exemplo que faz loops sobre vários arquivos para upload e usa nomes de arquivos seguros, consulte *Páginas/BufferedMultipleFileUploadPhysical.cshtml.cs* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="cd535-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="cd535-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) lança <xref:System.IO.IOException> um se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores.</span><span class="sxs-lookup"><span data-stu-id="cd535-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="cd535-217">O limite de 65.535 arquivos é um limite por servidor.</span><span class="sxs-lookup"><span data-stu-id="cd535-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="cd535-218">Para obter mais informações sobre esse limite no sistema operacional Windows, consulte as observações nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="cd535-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="cd535-219">Função GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="cd535-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="cd535-220">Carregar pequenos arquivos com modelo tampão vinculando a um banco de dados</span><span class="sxs-lookup"><span data-stu-id="cd535-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="cd535-221">Para armazenar dados de arquivos binários em <xref:System.Byte> um banco de dados usando o Entity [Framework,](/ef/core/index)defina uma propriedade de array na entidade:</span><span class="sxs-lookup"><span data-stu-id="cd535-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="cd535-222">Especifique uma propriedade de modelo <xref:Microsoft.AspNetCore.Http.IFormFile>de página para a classe que inclui um:</span><span class="sxs-lookup"><span data-stu-id="cd535-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="cd535-223"><xref:Microsoft.AspNetCore.Http.IFormFile>pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade modelo vinculada.</span><span class="sxs-lookup"><span data-stu-id="cd535-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="cd535-224">O exemplo anterior usa uma propriedade de modelo vinculado.</span><span class="sxs-lookup"><span data-stu-id="cd535-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="cd535-225">O `FileUpload` é usado no formulário Páginas de Navalha:</span><span class="sxs-lookup"><span data-stu-id="cd535-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="cd535-226">Quando o formulário for POSTed para <xref:Microsoft.AspNetCore.Http.IFormFile> o servidor, copie o para um fluxo e salve-o como uma matriz de bytes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cd535-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="cd535-227">No exemplo a `_dbContext` seguir, armazena o contexto de banco de dados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="cd535-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="cd535-228">O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de amostra:</span><span class="sxs-lookup"><span data-stu-id="cd535-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="cd535-229">*Páginas/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cd535-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="cd535-230">*Páginas/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="cd535-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="cd535-231">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="cd535-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="cd535-232">Não confie ou confie `FileName` na <xref:Microsoft.AspNetCore.Http.IFormFile> propriedade sem validação.</span><span class="sxs-lookup"><span data-stu-id="cd535-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="cd535-233">A `FileName` propriedade só deve ser usada para fins de exibição e somente após a codificação HTML.</span><span class="sxs-lookup"><span data-stu-id="cd535-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="cd535-234">Os exemplos fornecidos não levam em conta considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="cd535-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="cd535-235">Informações adicionais são fornecidas pelas seguintes seções e pelo [aplicativo de amostra:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)</span><span class="sxs-lookup"><span data-stu-id="cd535-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="cd535-236">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="cd535-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="cd535-237">Validação</span><span class="sxs-lookup"><span data-stu-id="cd535-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="cd535-238">Carregar arquivos grandes com streaming</span><span class="sxs-lookup"><span data-stu-id="cd535-238">Upload large files with streaming</span></span>

<span data-ttu-id="cd535-239">O exemplo a seguir demonstra como usar javaScript para transmitir um arquivo para uma ação controladora.</span><span class="sxs-lookup"><span data-stu-id="cd535-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="cd535-240">O token antiforgery do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo de solicitação.</span><span class="sxs-lookup"><span data-stu-id="cd535-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="cd535-241">Como o método de ação processa os dados enviados diretamente, a vinculação do modelo de formulário é desativada por outro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="cd535-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="cd535-242">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="cd535-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="cd535-243">Depois que as seções de várias partes são lidas, a ação executa sua própria vinculação de modelo.</span><span class="sxs-lookup"><span data-stu-id="cd535-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="cd535-244">A resposta inicial da página carrega o formulário e salva um token `GenerateAntiforgeryTokenCookieAttribute` antiforgery em um cookie (através do atributo).</span><span class="sxs-lookup"><span data-stu-id="cd535-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="cd535-245">O atributo usa o [suporte antiforgery](xref:security/anti-request-forgery) incorporado do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="cd535-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="cd535-246">O `DisableFormValueModelBindingAttribute` é usado para desativar a vinculação do modelo:</span><span class="sxs-lookup"><span data-stu-id="cd535-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="cd535-247">No aplicativo de `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` exemplo, e são aplicados como `/StreamedSingleFileUploadDb` filtros aos modelos de aplicação de página de e `/StreamedSingleFileUploadPhysical` em `Startup.ConfigureServices` usar convenções de Páginas de [Navalha](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="cd535-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="cd535-248">Como a vinculação do modelo não lê o formulário, os parâmetros vinculados ao formulário não se vinculam (consulta, rota e cabeçalho continuam a funcionar).</span><span class="sxs-lookup"><span data-stu-id="cd535-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="cd535-249">O método de ação `Request` funciona diretamente com a propriedade.</span><span class="sxs-lookup"><span data-stu-id="cd535-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="cd535-250">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="cd535-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="cd535-251">Os dados de chave/valor são armazenados em um `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="cd535-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="cd535-252">Depois que as seções de várias `KeyValueAccumulator` partes são lidas, o conteúdo do é usado para vincular os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="cd535-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="cd535-253">O `StreamingController.UploadDatabase` método completo para transmitir para um banco de dados com o EF Core:</span><span class="sxs-lookup"><span data-stu-id="cd535-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="cd535-254">`MultipartRequestHelper`(*Utilitários/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="cd535-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="cd535-255">O `StreamingController.UploadPhysical` método completo para streaming para um local físico:</span><span class="sxs-lookup"><span data-stu-id="cd535-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="cd535-256">No aplicativo de amostra, as `FileHelpers.ProcessStreamedFile`verificações de validação são tratadas por .</span><span class="sxs-lookup"><span data-stu-id="cd535-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="cd535-257">Validação</span><span class="sxs-lookup"><span data-stu-id="cd535-257">Validation</span></span>

<span data-ttu-id="cd535-258">A classe do `FileHelpers` aplicativo de exemplo demonstra <xref:Microsoft.AspNetCore.Http.IFormFile> várias verificações para uploads de arquivos tamponados e transmitidos.</span><span class="sxs-lookup"><span data-stu-id="cd535-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="cd535-259">Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> uploads de arquivos tamponados `ProcessFormFile` no aplicativo de amostra, consulte o método no arquivo *Utilities/FileHelpers.cs.*</span><span class="sxs-lookup"><span data-stu-id="cd535-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="cd535-260">Para processar arquivos transmitidos, `ProcessStreamedFile` consulte o método no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="cd535-261">Os métodos de processamento de validação demonstrados no aplicativo de amostra não digitalização do conteúdo dos arquivos enviados.</span><span class="sxs-lookup"><span data-stu-id="cd535-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="cd535-262">Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para usuários ou outros sistemas.</span><span class="sxs-lookup"><span data-stu-id="cd535-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="cd535-263">Embora a amostra de tópico forneça um exemplo de `FileHelpers` trabalho de técnicas de validação, não implemente a classe em um aplicativo de produção, a menos que você:</span><span class="sxs-lookup"><span data-stu-id="cd535-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="cd535-264">Entenda completamente a implementação.</span><span class="sxs-lookup"><span data-stu-id="cd535-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="cd535-265">Modifique a implementação conforme apropriado para o ambiente e especificações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cd535-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="cd535-266">**Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**</span><span class="sxs-lookup"><span data-stu-id="cd535-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="cd535-267">Validação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="cd535-267">Content validation</span></span>

<span data-ttu-id="cd535-268">**Use uma API de varredura de vírus/malware de terceiros no conteúdo carregado.**</span><span class="sxs-lookup"><span data-stu-id="cd535-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="cd535-269">A varredura de arquivos exige recursos do servidor em cenários de alto volume.</span><span class="sxs-lookup"><span data-stu-id="cd535-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="cd535-270">Se o desempenho do processamento de solicitações for diminuído devido à digitalização de arquivos, considere descarregar o trabalho de digitalização para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cd535-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="cd535-271">Normalmente, os arquivos enviados são mantidos em uma área de quarentena até que o scanner de vírus de fundo os verifique.</span><span class="sxs-lookup"><span data-stu-id="cd535-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="cd535-272">Quando um arquivo passa, o arquivo é movido para o local normal de armazenamento de arquivos.</span><span class="sxs-lookup"><span data-stu-id="cd535-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="cd535-273">Essas etapas geralmente são realizadas em conjunto com um registro de banco de dados que indica o status de digitalização de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="cd535-274">Ao usar essa abordagem, o aplicativo e o servidor do aplicativo permanecem focados em responder às solicitações.</span><span class="sxs-lookup"><span data-stu-id="cd535-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="cd535-275">Validação de extensão de arquivo</span><span class="sxs-lookup"><span data-stu-id="cd535-275">File extension validation</span></span>

<span data-ttu-id="cd535-276">A extensão do arquivo enviado deve ser verificada em uma lista de extensões permitidas.</span><span class="sxs-lookup"><span data-stu-id="cd535-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="cd535-277">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd535-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="cd535-278">Validação da assinatura do arquivo</span><span class="sxs-lookup"><span data-stu-id="cd535-278">File signature validation</span></span>

<span data-ttu-id="cd535-279">A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="cd535-280">Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="cd535-281">O aplicativo de amostra verifica assinaturas de arquivos para alguns tipos de arquivos comuns.</span><span class="sxs-lookup"><span data-stu-id="cd535-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="cd535-282">No exemplo a seguir, a assinatura do arquivo de uma imagem JPEG é verificada no arquivo:</span><span class="sxs-lookup"><span data-stu-id="cd535-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="cd535-283">Para obter assinaturas adicionais de arquivos, consulte o [Banco de Dados de Assinaturas de Arquivos](https://www.filesignatures.net/) e as especificações oficiais do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="cd535-284">Segurança do nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="cd535-284">File name security</span></span>

<span data-ttu-id="cd535-285">Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo para armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="cd535-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="cd535-286">Crie um nome de arquivo seguro para o arquivo usando [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.</span><span class="sxs-lookup"><span data-stu-id="cd535-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="cd535-287">Razor codifica automaticamente os valores de propriedade para exibição.</span><span class="sxs-lookup"><span data-stu-id="cd535-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="cd535-288">O seguinte código é seguro de usar:</span><span class="sxs-lookup"><span data-stu-id="cd535-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="cd535-289">Fora do Razor, sempre <xref:System.Net.WebUtility.HtmlEncode*> arquive o conteúdo do nome a partir da solicitação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="cd535-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="cd535-290">Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo é substituído por um arquivo de mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="cd535-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="cd535-291">Forneça lógica adicional para atender às especificações do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cd535-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="cd535-292">Validação de tamanho</span><span class="sxs-lookup"><span data-stu-id="cd535-292">Size validation</span></span>

<span data-ttu-id="cd535-293">Limitar o tamanho dos arquivos enviados.</span><span class="sxs-lookup"><span data-stu-id="cd535-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="cd535-294">No aplicativo de amostra, o tamanho do arquivo é limitado a 2 MB (indicado em bytes).</span><span class="sxs-lookup"><span data-stu-id="cd535-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="cd535-295">O limite é fornecido via [Configuração](xref:fundamentals/configuration/index) a partir do arquivo *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="cd535-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="cd535-296">O `FileSizeLimit` é injetado nas `PageModel` classes:</span><span class="sxs-lookup"><span data-stu-id="cd535-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="cd535-297">Quando um tamanho de arquivo excede o limite, o arquivo é rejeitado:</span><span class="sxs-lookup"><span data-stu-id="cd535-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="cd535-298">Valor de atributo do nome de correspondência ao nome do parâmetro do método POST</span><span class="sxs-lookup"><span data-stu-id="cd535-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="cd535-299">Em formulários não-Razor que formam dados `FormData` do POST ou usam diretamente do JavaScript, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="cd535-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="cd535-300">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cd535-300">In the following example:</span></span>

* <span data-ttu-id="cd535-301">Ao usar `<input>` um `name` elemento, o atributo é definido como valor: `battlePlans`</span><span class="sxs-lookup"><span data-stu-id="cd535-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="cd535-302">Ao `FormData` usar em JavaScript, o nome `battlePlans`é definido como o valor:</span><span class="sxs-lookup"><span data-stu-id="cd535-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="cd535-303">Use um nome correspondente para o parâmetro`battlePlans`do método C# ( ):</span><span class="sxs-lookup"><span data-stu-id="cd535-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="cd535-304">Para um método de `Upload`manipulador de páginas de lâminas chamado :</span><span class="sxs-lookup"><span data-stu-id="cd535-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="cd535-305">Para um método de ação do controlador MVC POST:</span><span class="sxs-lookup"><span data-stu-id="cd535-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="cd535-306">Configuração do servidor e do aplicativo</span><span class="sxs-lookup"><span data-stu-id="cd535-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="cd535-307">Limite de comprimento do corpo em várias partes</span><span class="sxs-lookup"><span data-stu-id="cd535-307">Multipart body length limit</span></span>

<span data-ttu-id="cd535-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>define o limite para o comprimento de cada corpo multipart.</span><span class="sxs-lookup"><span data-stu-id="cd535-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="cd535-309">As seções de formulário <xref:System.IO.InvalidDataException> que excedem esse limite lançam um quando analisados.</span><span class="sxs-lookup"><span data-stu-id="cd535-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="cd535-310">A inadimplência é 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="cd535-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="cd535-311">Personalize o limite <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> usando `Startup.ConfigureServices`a configuração em :</span><span class="sxs-lookup"><span data-stu-id="cd535-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="cd535-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>é usado para <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> definir o para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="cd535-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="cd535-313">Em um aplicativo Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) aplique `Startup.ConfigureServices`o filtro com uma convenção em :</span><span class="sxs-lookup"><span data-stu-id="cd535-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="cd535-314">Em um aplicativo Razor Pages ou um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="cd535-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="cd535-315">Kestrel tamanho máximo do corpo de solicitação</span><span class="sxs-lookup"><span data-stu-id="cd535-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="cd535-316">Para aplicativos hospedados pela Kestrel, o tamanho máximo do corpo de solicitação padrão é de 30.000.000 bytes, o que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="cd535-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="cd535-317">Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="cd535-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="cd535-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="cd535-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="cd535-319">Em um aplicativo Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) aplique `Startup.ConfigureServices`o filtro com uma convenção em :</span><span class="sxs-lookup"><span data-stu-id="cd535-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="cd535-320">Em um aplicativo de páginas de navalha ou em um aplicativo MVC, aplique o filtro na classe de manipulador de páginas ou no método de ação:</span><span class="sxs-lookup"><span data-stu-id="cd535-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="cd535-321">O `RequestSizeLimitAttribute` também pode ser [`@attribute`](xref:mvc/views/razor#attribute) aplicado usando a diretiva Razor:</span><span class="sxs-lookup"><span data-stu-id="cd535-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="cd535-322">Outros limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="cd535-322">Other Kestrel limits</span></span>

<span data-ttu-id="cd535-323">Outros limites de Kestrel podem se aplicar para aplicativos hospedados pela Kestrel:</span><span class="sxs-lookup"><span data-stu-id="cd535-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="cd535-324">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="cd535-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="cd535-325">Taxas de dados de solicitação e resposta</span><span class="sxs-lookup"><span data-stu-id="cd535-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="cd535-326">Limite de comprimento do conteúdo IIS</span><span class="sxs-lookup"><span data-stu-id="cd535-326">IIS content length limit</span></span>

<span data-ttu-id="cd535-327">O limite de`maxAllowedContentLength`solicitação padrão ( ) é de 30.000.000 bytes, que é de aproximadamente 28.6MB.</span><span class="sxs-lookup"><span data-stu-id="cd535-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="cd535-328">Personalize o limite no arquivo *Web.config:*</span><span class="sxs-lookup"><span data-stu-id="cd535-328">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="cd535-329">Essa configuração só se aplica ao IIS.</span><span class="sxs-lookup"><span data-stu-id="cd535-329">This setting only applies to IIS.</span></span> <span data-ttu-id="cd535-330">Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cd535-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="cd535-331">Para obter mais informações, consulte [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="cd535-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="cd535-332">Limitações no módulo de ASP.NET ou presença do Módulo de Filtragem de Solicitações iIS podem limitar os uploads a 2 ou 4 GB.</span><span class="sxs-lookup"><span data-stu-id="cd535-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="cd535-333">Para obter mais informações, consulte [Não é possível carregar um arquivo maior que 2GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="cd535-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="cd535-334">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="cd535-334">Troubleshoot</span></span>

<span data-ttu-id="cd535-335">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="cd535-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="cd535-336">Erro não encontrado quando implantado em um servidor IIS</span><span class="sxs-lookup"><span data-stu-id="cd535-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="cd535-337">O erro a seguir indica que o arquivo enviado excede o comprimento de conteúdo configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="cd535-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="cd535-338">Para obter mais informações sobre o aumento do limite, consulte a seção [limite de comprimento de conteúdo do IIS.](#iis-content-length-limit)</span><span class="sxs-lookup"><span data-stu-id="cd535-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="cd535-339">Falha de conexão</span><span class="sxs-lookup"><span data-stu-id="cd535-339">Connection failure</span></span>

<span data-ttu-id="cd535-340">Um erro de conexão e uma conexão de servidor de reset provavelmente indicam que o arquivo enviado excede o tamanho máximo do corpo de solicitação do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cd535-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="cd535-341">Para obter mais informações, consulte a seção de tamanho do corpo de solicitação máxima de [Kestrel.](#kestrel-maximum-request-body-size)</span><span class="sxs-lookup"><span data-stu-id="cd535-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="cd535-342">Os limites de conexão do cliente Kestrel também podem exigir ajustes.</span><span class="sxs-lookup"><span data-stu-id="cd535-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="cd535-343">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="cd535-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="cd535-344">Se o controlador estiver aceitando <xref:Microsoft.AspNetCore.Http.IFormFile> arquivos `null`carregados usando, mas o `enctype` valor `multipart/form-data`é, confirme se o formulário HTML está especificando um valor de .</span><span class="sxs-lookup"><span data-stu-id="cd535-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="cd535-345">Se esse atributo não `<form>` estiver definido no elemento, o upload <xref:Microsoft.AspNetCore.Http.IFormFile> do `null`arquivo não ocorrerá e quaisquer argumentos vinculados serão .</span><span class="sxs-lookup"><span data-stu-id="cd535-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="cd535-346">Também confirme que a [nomeação de upload em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="cd535-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="cd535-347">O fluxo era muito longo.</span><span class="sxs-lookup"><span data-stu-id="cd535-347">Stream was too long</span></span>

<span data-ttu-id="cd535-348">Os exemplos neste tópico <xref:System.IO.MemoryStream> dependem de manter o conteúdo do arquivo enviado.</span><span class="sxs-lookup"><span data-stu-id="cd535-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="cd535-349">O limite de `MemoryStream` `int.MaxValue`tamanho de a é .</span><span class="sxs-lookup"><span data-stu-id="cd535-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="cd535-350">Se o cenário de upload de arquivos do aplicativo requer manter conteúdo de arquivo maior `MemoryStream` que 50 MB, use uma abordagem alternativa que não dependa de um único para segurar o conteúdo de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="cd535-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cd535-351">ASP.NET Core suporta o upload de um ou mais arquivos usando vinculação de modelo tampão para arquivos menores e streaming não tamponado para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="cd535-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="cd535-352">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cd535-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="cd535-353">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="cd535-353">Security considerations</span></span>

<span data-ttu-id="cd535-354">Tenha cuidado ao fornecer aos usuários a capacidade de carregar arquivos para um servidor.</span><span class="sxs-lookup"><span data-stu-id="cd535-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="cd535-355">Os atacantes podem tentar:</span><span class="sxs-lookup"><span data-stu-id="cd535-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="cd535-356">Execute ataques [de negação de serviço.](/windows-hardware/drivers/ifs/denial-of-service)</span><span class="sxs-lookup"><span data-stu-id="cd535-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="cd535-357">Envie vírus ou malware.</span><span class="sxs-lookup"><span data-stu-id="cd535-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="cd535-358">Comprometer redes e servidores de outras formas.</span><span class="sxs-lookup"><span data-stu-id="cd535-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="cd535-359">As etapas de segurança que reduzem a probabilidade de um ataque bem sucedido são:</span><span class="sxs-lookup"><span data-stu-id="cd535-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="cd535-360">Faça upload de arquivos para uma área de upload de arquivos dedicada, de preferência para uma unidade não-sistema.</span><span class="sxs-lookup"><span data-stu-id="cd535-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="cd535-361">Um local dedicado facilita a imposição de restrições de segurança em arquivos enviados.</span><span class="sxs-lookup"><span data-stu-id="cd535-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="cd535-362">Desativar as permissões de execução no local de upload do arquivo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cd535-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="cd535-363">**Não** persista arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cd535-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="cd535-364">Use um nome de arquivo seguro determinado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cd535-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="cd535-365">Não use um nome de arquivo fornecido pelo usuário ou o nome do arquivo não confiável do arquivo carregado. &dagger; HTML codificar o nome do arquivo não confiável ao exibi-lo.</span><span class="sxs-lookup"><span data-stu-id="cd535-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="cd535-366">Por exemplo, registrar o nome do arquivo ou exibir em UI (Razor automaticamente HTML codifica a saída).</span><span class="sxs-lookup"><span data-stu-id="cd535-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="cd535-367">Permitir apenas extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cd535-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="cd535-368">Verifique se as verificações do lado do cliente são realizadas no servidor. &dagger; Verificações do lado do cliente são fáceis de contornar.</span><span class="sxs-lookup"><span data-stu-id="cd535-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="cd535-369">Verifique o tamanho de um arquivo enviado.</span><span class="sxs-lookup"><span data-stu-id="cd535-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="cd535-370">Defina um limite máximo de tamanho para evitar grandes uploads.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cd535-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="cd535-371">Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="cd535-372">**Execute um scanner de vírus/malware no conteúdo carregado antes que o arquivo seja armazenado.**</span><span class="sxs-lookup"><span data-stu-id="cd535-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="cd535-373">&dagger;O aplicativo de amostra demonstra uma abordagem que atende aos critérios.</span><span class="sxs-lookup"><span data-stu-id="cd535-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="cd535-374">Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:</span><span class="sxs-lookup"><span data-stu-id="cd535-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="cd535-375">Ganhe completamente o controle de um sistema.</span><span class="sxs-lookup"><span data-stu-id="cd535-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="cd535-376">Sobrecarregue um sistema com o resultado que o sistema trava.</span><span class="sxs-lookup"><span data-stu-id="cd535-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="cd535-377">Comprometer dados do sistema ou de usuários.</span><span class="sxs-lookup"><span data-stu-id="cd535-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="cd535-378">Aplique pichações em uma ui pública.</span><span class="sxs-lookup"><span data-stu-id="cd535-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="cd535-379">Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="cd535-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="cd535-380">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="cd535-380">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="cd535-381">Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários</span><span class="sxs-lookup"><span data-stu-id="cd535-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="cd535-382">Para obter mais informações sobre a implementação de medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [Validação.](#validation)</span><span class="sxs-lookup"><span data-stu-id="cd535-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="cd535-383">Cenários de armazenamento</span><span class="sxs-lookup"><span data-stu-id="cd535-383">Storage scenarios</span></span>

<span data-ttu-id="cd535-384">As opções comuns de armazenamento para arquivos incluem:</span><span class="sxs-lookup"><span data-stu-id="cd535-384">Common storage options for files include:</span></span>

* <span data-ttu-id="cd535-385">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="cd535-385">Database</span></span>

  * <span data-ttu-id="cd535-386">Para pequenos uploads de arquivos, um banco de dados é muitas vezes mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).</span><span class="sxs-lookup"><span data-stu-id="cd535-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="cd535-387">Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, porque a recuperação de um registro de banco de dados para dados do usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de avatar).</span><span class="sxs-lookup"><span data-stu-id="cd535-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="cd535-388">Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="cd535-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="cd535-389">Armazenamento físico (sistema de arquivos ou compartilhamento de rede)</span><span class="sxs-lookup"><span data-stu-id="cd535-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="cd535-390">Para uploads de arquivos grandes:</span><span class="sxs-lookup"><span data-stu-id="cd535-390">For large file uploads:</span></span>
    * <span data-ttu-id="cd535-391">Os limites do banco de dados podem restringir o tamanho do upload.</span><span class="sxs-lookup"><span data-stu-id="cd535-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="cd535-392">O armazenamento físico é muitas vezes menos econômico do que o armazenamento em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cd535-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="cd535-393">O armazenamento físico é potencialmente mais barato do que o uso de um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="cd535-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="cd535-394">O processo do aplicativo deve ter permissões de leitura e gravação para o local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="cd535-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="cd535-395">**Nunca conceda permissão de execução.**</span><span class="sxs-lookup"><span data-stu-id="cd535-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="cd535-396">Serviço de armazenamento de dados (por exemplo, [Armazenamento Azure Blob)](https://azure.microsoft.com/services/storage/blobs/)</span><span class="sxs-lookup"><span data-stu-id="cd535-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="cd535-397">Os serviços geralmente oferecem maior escalabilidade e resiliência sobre soluções no local que geralmente estão sujeitas a pontos únicos de falha.</span><span class="sxs-lookup"><span data-stu-id="cd535-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="cd535-398">Os serviços são potencialmente mais baixos em grandes cenários de infra-estrutura de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="cd535-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="cd535-399">Para obter mais informações, consulte [Quickstart: Use .NET para criar uma bolha no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="cd535-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="cd535-400">O tópico <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>demonstra, mas <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> pode <xref:System.IO.FileStream> ser usado para salvar <xref:System.IO.Stream>um armazenamento para blob ao trabalhar com um .</span><span class="sxs-lookup"><span data-stu-id="cd535-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="cd535-401">Cenários de upload de arquivos</span><span class="sxs-lookup"><span data-stu-id="cd535-401">File upload scenarios</span></span>

<span data-ttu-id="cd535-402">Duas abordagens gerais para upload de arquivos são buffering e streaming.</span><span class="sxs-lookup"><span data-stu-id="cd535-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="cd535-403">**de resposta**</span><span class="sxs-lookup"><span data-stu-id="cd535-403">**Buffering**</span></span>

<span data-ttu-id="cd535-404">O arquivo inteiro é <xref:Microsoft.AspNetCore.Http.IFormFile>lido em uma representação C# do arquivo usado para processar ou salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="cd535-405">Os recursos (disco, memória) usados pelos uploads de arquivos dependem do número e tamanho dos uploads de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="cd535-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="cd535-406">Se um aplicativo tentar buffer de muitos uploads, o site falha quando fica sem memória ou espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="cd535-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="cd535-407">Se o tamanho ou frequência dos uploads de arquivos estiver esgotando os recursos do aplicativo, use o streaming.</span><span class="sxs-lookup"><span data-stu-id="cd535-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="cd535-408">Qualquer único arquivo tamponado superior a 64 KB é movido da memória para um arquivo temporário no disco.</span><span class="sxs-lookup"><span data-stu-id="cd535-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="cd535-409">O buffer de pequenos arquivos é coberto nas seguintes seções deste tópico:</span><span class="sxs-lookup"><span data-stu-id="cd535-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="cd535-410">Armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="cd535-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="cd535-411">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="cd535-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="cd535-412">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="cd535-412">**Streaming**</span></span>

<span data-ttu-id="cd535-413">O arquivo é recebido a partir de uma solicitação de várias partes e diretamente processado ou salvo pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cd535-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="cd535-414">O streaming não melhora significativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="cd535-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="cd535-415">O streaming reduz as demandas por memória ou espaço em disco ao carregar arquivos.</span><span class="sxs-lookup"><span data-stu-id="cd535-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="cd535-416">O streaming de arquivos grandes é coberto no Upload de arquivos grandes com a seção [de streaming.](#upload-large-files-with-streaming)</span><span class="sxs-lookup"><span data-stu-id="cd535-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="cd535-417">Carregar pequenos arquivos com modelo tampão vinculando ao armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="cd535-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="cd535-418">Para carregar pequenos arquivos, use um formulário multiparte ou construa uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cd535-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="cd535-419">O exemplo a seguir demonstra o uso de um formulário Razor Pages para carregar um único arquivo *(Páginas/BufferedSingleFileUploadPhysical.cshtml* no aplicativo de amostra):</span><span class="sxs-lookup"><span data-stu-id="cd535-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="cd535-420">O exemplo a seguir é análogo ao exemplo anterior, exceto que:</span><span class="sxs-lookup"><span data-stu-id="cd535-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="cd535-421">JavaScript's[(Fetch API)](https://developer.mozilla.org/docs/Web/API/Fetch_API)é usado para enviar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="cd535-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="cd535-422">Não há validação.</span><span class="sxs-lookup"><span data-stu-id="cd535-422">There's no validation.</span></span>

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

<span data-ttu-id="cd535-423">Para executar o formulário POST em JavaScript para clientes que [não suportam a API Fetch,](https://caniuse.com/#feat=fetch)use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="cd535-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="cd535-424">Use um Fetch Polyfill (por exemplo, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="cd535-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="cd535-425">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="cd535-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="cd535-426">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd535-426">For example:</span></span>

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

<span data-ttu-id="cd535-427">Para suportar uploads de arquivos, os formulários`enctype`HTML `multipart/form-data`devem especificar um tipo de codificação ( ) de .</span><span class="sxs-lookup"><span data-stu-id="cd535-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="cd535-428">Para `files` que um elemento de entrada `multiple` suporte o `<input>` upload de vários arquivos forneça o atributo no elemento:</span><span class="sxs-lookup"><span data-stu-id="cd535-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="cd535-429">Os arquivos individuais carregados para o servidor podem <xref:Microsoft.AspNetCore.Http.IFormFile>ser acessados através do Model [Binding](xref:mvc/models/model-binding) usando .</span><span class="sxs-lookup"><span data-stu-id="cd535-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="cd535-430">O aplicativo de exemplo demonstra vários uploads de arquivos tamponados para cenários de banco de dados e armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="cd535-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="cd535-431">**Não use** `FileName` a <xref:Microsoft.AspNetCore.Http.IFormFile> propriedade de outro além de exibição e registro.</span><span class="sxs-lookup"><span data-stu-id="cd535-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="cd535-432">Ao exibir ou registrar, HTML codifica rexa o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="cd535-433">Um invasor pode fornecer um nome de arquivo malicioso, incluindo caminhos completos ou caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="cd535-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="cd535-434">As aplicações devem:</span><span class="sxs-lookup"><span data-stu-id="cd535-434">Applications should:</span></span>
>
> * <span data-ttu-id="cd535-435">Remova o caminho do nome de arquivo fornecido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="cd535-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="cd535-436">Salve o nome de arquivo codificado por HTML e removido de caminho para UI ou registro.</span><span class="sxs-lookup"><span data-stu-id="cd535-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="cd535-437">Gerar um novo nome de arquivo aleatório para armazenamento.</span><span class="sxs-lookup"><span data-stu-id="cd535-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="cd535-438">O código a seguir remove o caminho do nome do arquivo:</span><span class="sxs-lookup"><span data-stu-id="cd535-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="cd535-439">Os exemplos fornecidos até agora não levam em conta considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="cd535-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="cd535-440">Informações adicionais são fornecidas pelas seguintes seções e pelo [aplicativo de amostra:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)</span><span class="sxs-lookup"><span data-stu-id="cd535-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="cd535-441">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="cd535-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="cd535-442">Validação</span><span class="sxs-lookup"><span data-stu-id="cd535-442">Validation</span></span>](#validation)

<span data-ttu-id="cd535-443">Ao carregar arquivos usando <xref:Microsoft.AspNetCore.Http.IFormFile>vinculação de modelo e, o método de ação pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="cd535-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="cd535-444">Um <xref:Microsoft.AspNetCore.Http.IFormFile>único.</span><span class="sxs-lookup"><span data-stu-id="cd535-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="cd535-445">Qualquer uma das seguintes coleções que representam vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="cd535-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="cd535-446">[Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="cd535-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="cd535-447">Correspondências de vinculação formam arquivos por nome.</span><span class="sxs-lookup"><span data-stu-id="cd535-447">Binding matches form files by name.</span></span> <span data-ttu-id="cd535-448">Por exemplo, `name` o `<input type="file" name="formFile">` valor HTML deve corresponder ao parâmetro`FormFile`C#/propriedade bound ( ).</span><span class="sxs-lookup"><span data-stu-id="cd535-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="cd535-449">Para obter mais informações, consulte o valor do [atributo nome match ao nome do parâmetro da](#match-name-attribute-value-to-parameter-name-of-post-method) seção do método POST.</span><span class="sxs-lookup"><span data-stu-id="cd535-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="cd535-450">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cd535-450">The following example:</span></span>

* <span data-ttu-id="cd535-451">Loops através de um ou mais arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="cd535-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="cd535-452">Usa [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="cd535-453">Salva os arquivos para o sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cd535-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="cd535-454">Retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="cd535-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="cd535-455">Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho.</span><span class="sxs-lookup"><span data-stu-id="cd535-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="cd535-456">No exemplo a seguir, o caminho é obtido a partir da configuração:</span><span class="sxs-lookup"><span data-stu-id="cd535-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="cd535-457">O caminho passado <xref:System.IO.FileStream> para o *deve* incluir o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="cd535-458">Se o nome do arquivo não <xref:System.UnauthorizedAccessException> for fornecido, um é jogado em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="cd535-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="cd535-459">Os arquivos carregados usando a <xref:Microsoft.AspNetCore.Http.IFormFile> técnica são protegidos na memória ou no disco no servidor antes do processamento.</span><span class="sxs-lookup"><span data-stu-id="cd535-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="cd535-460">Dentro do método <xref:Microsoft.AspNetCore.Http.IFormFile> de ação, <xref:System.IO.Stream>os conteúdos são acessíveis como um .</span><span class="sxs-lookup"><span data-stu-id="cd535-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="cd535-461">Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como [o armazenamento Azure Blob](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="cd535-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="cd535-462">Para outro exemplo que faz loops sobre vários arquivos para upload e usa nomes de arquivos seguros, consulte *Páginas/BufferedMultipleFileUploadPhysical.cshtml.cs* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="cd535-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="cd535-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) lança <xref:System.IO.IOException> um se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores.</span><span class="sxs-lookup"><span data-stu-id="cd535-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="cd535-464">O limite de 65.535 arquivos é um limite por servidor.</span><span class="sxs-lookup"><span data-stu-id="cd535-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="cd535-465">Para obter mais informações sobre esse limite no sistema operacional Windows, consulte as observações nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="cd535-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="cd535-466">Função GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="cd535-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="cd535-467">Carregar pequenos arquivos com modelo tampão vinculando a um banco de dados</span><span class="sxs-lookup"><span data-stu-id="cd535-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="cd535-468">Para armazenar dados de arquivos binários em <xref:System.Byte> um banco de dados usando o Entity [Framework,](/ef/core/index)defina uma propriedade de array na entidade:</span><span class="sxs-lookup"><span data-stu-id="cd535-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="cd535-469">Especifique uma propriedade de modelo <xref:Microsoft.AspNetCore.Http.IFormFile>de página para a classe que inclui um:</span><span class="sxs-lookup"><span data-stu-id="cd535-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="cd535-470"><xref:Microsoft.AspNetCore.Http.IFormFile>pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade modelo vinculada.</span><span class="sxs-lookup"><span data-stu-id="cd535-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="cd535-471">O exemplo anterior usa uma propriedade de modelo vinculado.</span><span class="sxs-lookup"><span data-stu-id="cd535-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="cd535-472">O `FileUpload` é usado no formulário Páginas de Navalha:</span><span class="sxs-lookup"><span data-stu-id="cd535-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="cd535-473">Quando o formulário for POSTed para <xref:Microsoft.AspNetCore.Http.IFormFile> o servidor, copie o para um fluxo e salve-o como uma matriz de bytes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cd535-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="cd535-474">No exemplo a `_dbContext` seguir, armazena o contexto de banco de dados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="cd535-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="cd535-475">O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de amostra:</span><span class="sxs-lookup"><span data-stu-id="cd535-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="cd535-476">*Páginas/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cd535-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="cd535-477">*Páginas/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="cd535-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="cd535-478">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="cd535-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="cd535-479">Não confie ou confie `FileName` na <xref:Microsoft.AspNetCore.Http.IFormFile> propriedade sem validação.</span><span class="sxs-lookup"><span data-stu-id="cd535-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="cd535-480">A `FileName` propriedade só deve ser usada para fins de exibição e somente após a codificação HTML.</span><span class="sxs-lookup"><span data-stu-id="cd535-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="cd535-481">Os exemplos fornecidos não levam em conta considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="cd535-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="cd535-482">Informações adicionais são fornecidas pelas seguintes seções e pelo [aplicativo de amostra:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)</span><span class="sxs-lookup"><span data-stu-id="cd535-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="cd535-483">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="cd535-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="cd535-484">Validação</span><span class="sxs-lookup"><span data-stu-id="cd535-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="cd535-485">Carregar arquivos grandes com streaming</span><span class="sxs-lookup"><span data-stu-id="cd535-485">Upload large files with streaming</span></span>

<span data-ttu-id="cd535-486">O exemplo a seguir demonstra como usar javaScript para transmitir um arquivo para uma ação controladora.</span><span class="sxs-lookup"><span data-stu-id="cd535-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="cd535-487">O token antiforgery do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo de solicitação.</span><span class="sxs-lookup"><span data-stu-id="cd535-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="cd535-488">Como o método de ação processa os dados enviados diretamente, a vinculação do modelo de formulário é desativada por outro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="cd535-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="cd535-489">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="cd535-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="cd535-490">Depois que as seções de várias partes são lidas, a ação executa sua própria vinculação de modelo.</span><span class="sxs-lookup"><span data-stu-id="cd535-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="cd535-491">A resposta inicial da página carrega o formulário e salva um token `GenerateAntiforgeryTokenCookieAttribute` antiforgery em um cookie (através do atributo).</span><span class="sxs-lookup"><span data-stu-id="cd535-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="cd535-492">O atributo usa o [suporte antiforgery](xref:security/anti-request-forgery) incorporado do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="cd535-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="cd535-493">O `DisableFormValueModelBindingAttribute` é usado para desativar a vinculação do modelo:</span><span class="sxs-lookup"><span data-stu-id="cd535-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="cd535-494">No aplicativo de `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` exemplo, e são aplicados como `/StreamedSingleFileUploadDb` filtros aos modelos de aplicação de página de e `/StreamedSingleFileUploadPhysical` em `Startup.ConfigureServices` usar convenções de Páginas de [Navalha](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="cd535-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="cd535-495">Como a vinculação do modelo não lê o formulário, os parâmetros vinculados ao formulário não se vinculam (consulta, rota e cabeçalho continuam a funcionar).</span><span class="sxs-lookup"><span data-stu-id="cd535-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="cd535-496">O método de ação `Request` funciona diretamente com a propriedade.</span><span class="sxs-lookup"><span data-stu-id="cd535-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="cd535-497">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="cd535-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="cd535-498">Os dados de chave/valor são armazenados em um `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="cd535-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="cd535-499">Depois que as seções de várias `KeyValueAccumulator` partes são lidas, o conteúdo do é usado para vincular os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="cd535-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="cd535-500">O `StreamingController.UploadDatabase` método completo para transmitir para um banco de dados com o EF Core:</span><span class="sxs-lookup"><span data-stu-id="cd535-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="cd535-501">`MultipartRequestHelper`(*Utilitários/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="cd535-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="cd535-502">O `StreamingController.UploadPhysical` método completo para streaming para um local físico:</span><span class="sxs-lookup"><span data-stu-id="cd535-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="cd535-503">No aplicativo de amostra, as `FileHelpers.ProcessStreamedFile`verificações de validação são tratadas por .</span><span class="sxs-lookup"><span data-stu-id="cd535-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="cd535-504">Validação</span><span class="sxs-lookup"><span data-stu-id="cd535-504">Validation</span></span>

<span data-ttu-id="cd535-505">A classe do `FileHelpers` aplicativo de exemplo demonstra <xref:Microsoft.AspNetCore.Http.IFormFile> várias verificações para uploads de arquivos tamponados e transmitidos.</span><span class="sxs-lookup"><span data-stu-id="cd535-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="cd535-506">Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> uploads de arquivos tamponados `ProcessFormFile` no aplicativo de amostra, consulte o método no arquivo *Utilities/FileHelpers.cs.*</span><span class="sxs-lookup"><span data-stu-id="cd535-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="cd535-507">Para processar arquivos transmitidos, `ProcessStreamedFile` consulte o método no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="cd535-508">Os métodos de processamento de validação demonstrados no aplicativo de amostra não digitalização do conteúdo dos arquivos enviados.</span><span class="sxs-lookup"><span data-stu-id="cd535-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="cd535-509">Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para usuários ou outros sistemas.</span><span class="sxs-lookup"><span data-stu-id="cd535-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="cd535-510">Embora a amostra de tópico forneça um exemplo de `FileHelpers` trabalho de técnicas de validação, não implemente a classe em um aplicativo de produção, a menos que você:</span><span class="sxs-lookup"><span data-stu-id="cd535-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="cd535-511">Entenda completamente a implementação.</span><span class="sxs-lookup"><span data-stu-id="cd535-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="cd535-512">Modifique a implementação conforme apropriado para o ambiente e especificações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cd535-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="cd535-513">**Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**</span><span class="sxs-lookup"><span data-stu-id="cd535-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="cd535-514">Validação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="cd535-514">Content validation</span></span>

<span data-ttu-id="cd535-515">**Use uma API de varredura de vírus/malware de terceiros no conteúdo carregado.**</span><span class="sxs-lookup"><span data-stu-id="cd535-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="cd535-516">A varredura de arquivos exige recursos do servidor em cenários de alto volume.</span><span class="sxs-lookup"><span data-stu-id="cd535-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="cd535-517">Se o desempenho do processamento de solicitações for diminuído devido à digitalização de arquivos, considere descarregar o trabalho de digitalização para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cd535-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="cd535-518">Normalmente, os arquivos enviados são mantidos em uma área de quarentena até que o scanner de vírus de fundo os verifique.</span><span class="sxs-lookup"><span data-stu-id="cd535-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="cd535-519">Quando um arquivo passa, o arquivo é movido para o local normal de armazenamento de arquivos.</span><span class="sxs-lookup"><span data-stu-id="cd535-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="cd535-520">Essas etapas geralmente são realizadas em conjunto com um registro de banco de dados que indica o status de digitalização de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="cd535-521">Ao usar essa abordagem, o aplicativo e o servidor do aplicativo permanecem focados em responder às solicitações.</span><span class="sxs-lookup"><span data-stu-id="cd535-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="cd535-522">Validação de extensão de arquivo</span><span class="sxs-lookup"><span data-stu-id="cd535-522">File extension validation</span></span>

<span data-ttu-id="cd535-523">A extensão do arquivo enviado deve ser verificada em uma lista de extensões permitidas.</span><span class="sxs-lookup"><span data-stu-id="cd535-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="cd535-524">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cd535-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="cd535-525">Validação da assinatura do arquivo</span><span class="sxs-lookup"><span data-stu-id="cd535-525">File signature validation</span></span>

<span data-ttu-id="cd535-526">A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="cd535-527">Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="cd535-528">O aplicativo de amostra verifica assinaturas de arquivos para alguns tipos de arquivos comuns.</span><span class="sxs-lookup"><span data-stu-id="cd535-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="cd535-529">No exemplo a seguir, a assinatura do arquivo de uma imagem JPEG é verificada no arquivo:</span><span class="sxs-lookup"><span data-stu-id="cd535-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="cd535-530">Para obter assinaturas adicionais de arquivos, consulte o [Banco de Dados de Assinaturas de Arquivos](https://www.filesignatures.net/) e as especificações oficiais do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cd535-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="cd535-531">Segurança do nome do arquivo</span><span class="sxs-lookup"><span data-stu-id="cd535-531">File name security</span></span>

<span data-ttu-id="cd535-532">Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo para armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="cd535-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="cd535-533">Crie um nome de arquivo seguro para o arquivo usando [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.</span><span class="sxs-lookup"><span data-stu-id="cd535-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="cd535-534">Razor codifica automaticamente os valores de propriedade para exibição.</span><span class="sxs-lookup"><span data-stu-id="cd535-534">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="cd535-535">O seguinte código é seguro de usar:</span><span class="sxs-lookup"><span data-stu-id="cd535-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="cd535-536">Fora do Razor, sempre <xref:System.Net.WebUtility.HtmlEncode*> arquive o conteúdo do nome a partir da solicitação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="cd535-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="cd535-537">Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo é substituído por um arquivo de mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="cd535-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="cd535-538">Forneça lógica adicional para atender às especificações do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cd535-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="cd535-539">Validação de tamanho</span><span class="sxs-lookup"><span data-stu-id="cd535-539">Size validation</span></span>

<span data-ttu-id="cd535-540">Limitar o tamanho dos arquivos enviados.</span><span class="sxs-lookup"><span data-stu-id="cd535-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="cd535-541">No aplicativo de amostra, o tamanho do arquivo é limitado a 2 MB (indicado em bytes).</span><span class="sxs-lookup"><span data-stu-id="cd535-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="cd535-542">O limite é fornecido via [Configuração](xref:fundamentals/configuration/index) a partir do arquivo *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="cd535-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="cd535-543">O `FileSizeLimit` é injetado nas `PageModel` classes:</span><span class="sxs-lookup"><span data-stu-id="cd535-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="cd535-544">Quando um tamanho de arquivo excede o limite, o arquivo é rejeitado:</span><span class="sxs-lookup"><span data-stu-id="cd535-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="cd535-545">Valor de atributo do nome de correspondência ao nome do parâmetro do método POST</span><span class="sxs-lookup"><span data-stu-id="cd535-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="cd535-546">Em formulários não-Razor que formam dados `FormData` do POST ou usam diretamente do JavaScript, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="cd535-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="cd535-547">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cd535-547">In the following example:</span></span>

* <span data-ttu-id="cd535-548">Ao usar `<input>` um `name` elemento, o atributo é definido como valor: `battlePlans`</span><span class="sxs-lookup"><span data-stu-id="cd535-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="cd535-549">Ao `FormData` usar em JavaScript, o nome `battlePlans`é definido como o valor:</span><span class="sxs-lookup"><span data-stu-id="cd535-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="cd535-550">Use um nome correspondente para o parâmetro`battlePlans`do método C# ( ):</span><span class="sxs-lookup"><span data-stu-id="cd535-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="cd535-551">Para um método de `Upload`manipulador de páginas de lâminas chamado :</span><span class="sxs-lookup"><span data-stu-id="cd535-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="cd535-552">Para um método de ação do controlador MVC POST:</span><span class="sxs-lookup"><span data-stu-id="cd535-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="cd535-553">Configuração do servidor e do aplicativo</span><span class="sxs-lookup"><span data-stu-id="cd535-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="cd535-554">Limite de comprimento do corpo em várias partes</span><span class="sxs-lookup"><span data-stu-id="cd535-554">Multipart body length limit</span></span>

<span data-ttu-id="cd535-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>define o limite para o comprimento de cada corpo multipart.</span><span class="sxs-lookup"><span data-stu-id="cd535-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="cd535-556">As seções de formulário <xref:System.IO.InvalidDataException> que excedem esse limite lançam um quando analisados.</span><span class="sxs-lookup"><span data-stu-id="cd535-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="cd535-557">A inadimplência é 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="cd535-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="cd535-558">Personalize o limite <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> usando `Startup.ConfigureServices`a configuração em :</span><span class="sxs-lookup"><span data-stu-id="cd535-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="cd535-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>é usado para <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> definir o para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="cd535-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="cd535-560">Em um aplicativo Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) aplique `Startup.ConfigureServices`o filtro com uma convenção em :</span><span class="sxs-lookup"><span data-stu-id="cd535-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="cd535-561">Em um aplicativo Razor Pages ou um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="cd535-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="cd535-562">Kestrel tamanho máximo do corpo de solicitação</span><span class="sxs-lookup"><span data-stu-id="cd535-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="cd535-563">Para aplicativos hospedados pela Kestrel, o tamanho máximo do corpo de solicitação padrão é de 30.000.000 bytes, o que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="cd535-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="cd535-564">Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="cd535-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="cd535-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="cd535-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="cd535-566">Em um aplicativo Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) aplique `Startup.ConfigureServices`o filtro com uma convenção em :</span><span class="sxs-lookup"><span data-stu-id="cd535-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="cd535-567">Em um aplicativo de páginas de navalha ou em um aplicativo MVC, aplique o filtro na classe de manipulador de páginas ou no método de ação:</span><span class="sxs-lookup"><span data-stu-id="cd535-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="cd535-568">Outros limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="cd535-568">Other Kestrel limits</span></span>

<span data-ttu-id="cd535-569">Outros limites de Kestrel podem se aplicar para aplicativos hospedados pela Kestrel:</span><span class="sxs-lookup"><span data-stu-id="cd535-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="cd535-570">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="cd535-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="cd535-571">Taxas de dados de solicitação e resposta</span><span class="sxs-lookup"><span data-stu-id="cd535-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="cd535-572">Limite de comprimento do conteúdo IIS</span><span class="sxs-lookup"><span data-stu-id="cd535-572">IIS content length limit</span></span>

<span data-ttu-id="cd535-573">O limite de`maxAllowedContentLength`solicitação padrão ( ) é de 30.000.000 bytes, que é de aproximadamente 28.6MB.</span><span class="sxs-lookup"><span data-stu-id="cd535-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="cd535-574">Personalize o limite no arquivo *Web.config:*</span><span class="sxs-lookup"><span data-stu-id="cd535-574">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="cd535-575">Essa configuração só se aplica ao IIS.</span><span class="sxs-lookup"><span data-stu-id="cd535-575">This setting only applies to IIS.</span></span> <span data-ttu-id="cd535-576">Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cd535-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="cd535-577">Para obter mais informações, consulte [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="cd535-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="cd535-578">Limitações no módulo de ASP.NET ou presença do Módulo de Filtragem de Solicitações iIS podem limitar os uploads a 2 ou 4 GB.</span><span class="sxs-lookup"><span data-stu-id="cd535-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="cd535-579">Para obter mais informações, consulte [Não é possível carregar um arquivo maior que 2GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="cd535-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="cd535-580">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="cd535-580">Troubleshoot</span></span>

<span data-ttu-id="cd535-581">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="cd535-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="cd535-582">Erro não encontrado quando implantado em um servidor IIS</span><span class="sxs-lookup"><span data-stu-id="cd535-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="cd535-583">O erro a seguir indica que o arquivo enviado excede o comprimento de conteúdo configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="cd535-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="cd535-584">Para obter mais informações sobre o aumento do limite, consulte a seção [limite de comprimento de conteúdo do IIS.](#iis-content-length-limit)</span><span class="sxs-lookup"><span data-stu-id="cd535-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="cd535-585">Falha de conexão</span><span class="sxs-lookup"><span data-stu-id="cd535-585">Connection failure</span></span>

<span data-ttu-id="cd535-586">Um erro de conexão e uma conexão de servidor de reset provavelmente indicam que o arquivo enviado excede o tamanho máximo do corpo de solicitação do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cd535-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="cd535-587">Para obter mais informações, consulte a seção de tamanho do corpo de solicitação máxima de [Kestrel.](#kestrel-maximum-request-body-size)</span><span class="sxs-lookup"><span data-stu-id="cd535-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="cd535-588">Os limites de conexão do cliente Kestrel também podem exigir ajustes.</span><span class="sxs-lookup"><span data-stu-id="cd535-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="cd535-589">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="cd535-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="cd535-590">Se o controlador estiver aceitando <xref:Microsoft.AspNetCore.Http.IFormFile> arquivos `null`carregados usando, mas o `enctype` valor `multipart/form-data`é, confirme se o formulário HTML está especificando um valor de .</span><span class="sxs-lookup"><span data-stu-id="cd535-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="cd535-591">Se esse atributo não `<form>` estiver definido no elemento, o upload <xref:Microsoft.AspNetCore.Http.IFormFile> do `null`arquivo não ocorrerá e quaisquer argumentos vinculados serão .</span><span class="sxs-lookup"><span data-stu-id="cd535-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="cd535-592">Também confirme que a [nomeação de upload em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="cd535-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="cd535-593">O fluxo era muito longo.</span><span class="sxs-lookup"><span data-stu-id="cd535-593">Stream was too long</span></span>

<span data-ttu-id="cd535-594">Os exemplos neste tópico <xref:System.IO.MemoryStream> dependem de manter o conteúdo do arquivo enviado.</span><span class="sxs-lookup"><span data-stu-id="cd535-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="cd535-595">O limite de `MemoryStream` `int.MaxValue`tamanho de a é .</span><span class="sxs-lookup"><span data-stu-id="cd535-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="cd535-596">Se o cenário de upload de arquivos do aplicativo requer manter conteúdo de arquivo maior `MemoryStream` que 50 MB, use uma abordagem alternativa que não dependa de um único para segurar o conteúdo de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="cd535-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="cd535-597">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cd535-597">Additional resources</span></span>

* <span data-ttu-id="cd535-598">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="cd535-598">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="cd535-599">Segurança do Azure: Security Frame: Validação de entrada | Atenuações</span><span class="sxs-lookup"><span data-stu-id="cd535-599">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="cd535-600">Padrões de design de nuvem do Azure: padrão de chave do manobrista</span><span class="sxs-lookup"><span data-stu-id="cd535-600">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
