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
# <a name="upload-files-in-aspnet-core"></a>Carregar arquivos no ASP.NET Core

Por [Steve Smith](https://ardalis.com/) e Rutger [Storm](https://github.com/rutix)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core suporta o upload de um ou mais arquivos usando vinculação de modelo tampão para arquivos menores e streaming não tamponado para arquivos maiores.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considerações sobre segurança

Tenha cuidado ao fornecer aos usuários a capacidade de carregar arquivos para um servidor. Os atacantes podem tentar:

* Execute ataques [de negação de serviço.](/windows-hardware/drivers/ifs/denial-of-service)
* Envie vírus ou malware.
* Comprometer redes e servidores de outras formas.

As etapas de segurança que reduzem a probabilidade de um ataque bem sucedido são:

* Faça upload de arquivos para uma área de upload de arquivos dedicada, de preferência para uma unidade não-sistema. Um local dedicado facilita a imposição de restrições de segurança em arquivos enviados. Desativar as permissões de execução no local de upload do arquivo.&dagger;
* **Não** persista arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;
* Use um nome de arquivo seguro determinado pelo aplicativo. Não use um nome de arquivo fornecido pelo usuário ou o nome do arquivo não confiável do arquivo carregado. &dagger; HTML codificar o nome do arquivo não confiável ao exibi-lo. Por exemplo, registrar o nome do arquivo ou exibir em UI (Razor automaticamente HTML codifica a saída).
* Permitir apenas extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Verifique se as verificações do lado do cliente são realizadas no servidor. &dagger; Verificações do lado do cliente são fáceis de contornar.
* Verifique o tamanho de um arquivo enviado. Defina um limite máximo de tamanho para evitar grandes uploads.&dagger;
* Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.
* **Execute um scanner de vírus/malware no conteúdo carregado antes que o arquivo seja armazenado.**

&dagger;O aplicativo de amostra demonstra uma abordagem que atende aos critérios.

> [!WARNING]
> Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:
>
> * Ganhe completamente o controle de um sistema.
> * Sobrecarregue um sistema com o resultado que o sistema trava.
> * Comprometer dados do sistema ou de usuários.
> * Aplique pichações em uma ui pública.
>
> Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:
>
> * [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)
> * [Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Para obter mais informações sobre a implementação de medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [Validação.](#validation)

## <a name="storage-scenarios"></a>Cenários de armazenamento

As opções comuns de armazenamento para arquivos incluem:

* Banco de dados

  * Para pequenos uploads de arquivos, um banco de dados é muitas vezes mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).
  * Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, porque a recuperação de um registro de banco de dados para dados do usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de avatar).
  * Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dados.

* Armazenamento físico (sistema de arquivos ou compartilhamento de rede)

  * Para uploads de arquivos grandes:
    * Os limites do banco de dados podem restringir o tamanho do upload.
    * O armazenamento físico é muitas vezes menos econômico do que o armazenamento em um banco de dados.
  * O armazenamento físico é potencialmente mais barato do que o uso de um serviço de armazenamento de dados.
  * O processo do aplicativo deve ter permissões de leitura e gravação para o local de armazenamento. **Nunca conceda permissão de execução.**

* Serviço de armazenamento de dados (por exemplo, [Armazenamento Azure Blob)](https://azure.microsoft.com/services/storage/blobs/)

  * Os serviços geralmente oferecem maior escalabilidade e resiliência sobre soluções no local que geralmente estão sujeitas a pontos únicos de falha.
  * Os serviços são potencialmente mais baixos em grandes cenários de infra-estrutura de armazenamento.

  Para obter mais informações, consulte [Quickstart: Use .NET para criar uma bolha no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Cenários de upload de arquivos

Duas abordagens gerais para upload de arquivos são buffering e streaming.

**de resposta**

O arquivo inteiro é <xref:Microsoft.AspNetCore.Http.IFormFile>lido em uma representação C# do arquivo usado para processar ou salvar o arquivo.

Os recursos (disco, memória) usados pelos uploads de arquivos dependem do número e tamanho dos uploads de arquivos simultâneos. Se um aplicativo tentar buffer de muitos uploads, o site falha quando fica sem memória ou espaço em disco. Se o tamanho ou frequência dos uploads de arquivos estiver esgotando os recursos do aplicativo, use o streaming.

> [!NOTE]
> Qualquer único arquivo tamponado superior a 64 KB é movido da memória para um arquivo temporário no disco.

O buffer de pequenos arquivos é coberto nas seguintes seções deste tópico:

* [Armazenamento físico](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Banco de dados](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

O arquivo é recebido a partir de uma solicitação de várias partes e diretamente processado ou salvo pelo aplicativo. O streaming não melhora significativamente o desempenho. O streaming reduz as demandas por memória ou espaço em disco ao carregar arquivos.

O streaming de arquivos grandes é coberto no Upload de arquivos grandes com a seção [de streaming.](#upload-large-files-with-streaming)

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Carregar pequenos arquivos com modelo tampão vinculando ao armazenamento físico

Para carregar pequenos arquivos, use um formulário multiparte ou construa uma solicitação POST usando JavaScript.

O exemplo a seguir demonstra o uso de um formulário Razor Pages para carregar um único arquivo *(Páginas/BufferedSingleFileUploadPhysical.cshtml* no aplicativo de amostra):

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

O exemplo a seguir é análogo ao exemplo anterior, exceto que:

* JavaScript's[(Fetch API)](https://developer.mozilla.org/docs/Web/API/Fetch_API)é usado para enviar os dados do formulário.
* Não há validação.

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

Para executar o formulário POST em JavaScript para clientes que [não suportam a API Fetch,](https://caniuse.com/#feat=fetch)use uma das seguintes abordagens:

* Use um Fetch Polyfill (por exemplo, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Use `XMLHttpRequest`. Por exemplo:

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

Para suportar uploads de arquivos, os formulários`enctype`HTML `multipart/form-data`devem especificar um tipo de codificação ( ) de .

Para `files` que um elemento de entrada `multiple` suporte o `<input>` upload de vários arquivos forneça o atributo no elemento:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Os arquivos individuais carregados para o servidor podem <xref:Microsoft.AspNetCore.Http.IFormFile>ser acessados através do Model [Binding](xref:mvc/models/model-binding) usando . O aplicativo de exemplo demonstra vários uploads de arquivos tamponados para cenários de banco de dados e armazenamento físico.

<a name="filename"></a>

> [!WARNING]
> **Não use** `FileName` a <xref:Microsoft.AspNetCore.Http.IFormFile> propriedade de outro além de exibição e registro. Ao exibir ou registrar, HTML codifica rexa o nome do arquivo. Um invasor pode fornecer um nome de arquivo malicioso, incluindo caminhos completos ou caminhos relativos. As aplicações devem:
>
> * Remova o caminho do nome de arquivo fornecido pelo usuário.
> * Salve o nome de arquivo codificado por HTML e removido de caminho para UI ou registro.
> * Gerar um novo nome de arquivo aleatório para armazenamento.
>
> O código a seguir remove o caminho do nome do arquivo:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Os exemplos fornecidos até agora não levam em conta considerações de segurança. Informações adicionais são fornecidas pelas seguintes seções e pelo [aplicativo de amostra:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)
>
> * [Considerações sobre segurança](#security-considerations)
> * [Validação](#validation)

Ao carregar arquivos usando <xref:Microsoft.AspNetCore.Http.IFormFile>vinculação de modelo e, o método de ação pode aceitar:

* Um <xref:Microsoft.AspNetCore.Http.IFormFile>único.
* Qualquer uma das seguintes coleções que representam vários arquivos:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Correspondências de vinculação formam arquivos por nome. Por exemplo, `name` o `<input type="file" name="formFile">` valor HTML deve corresponder ao parâmetro`FormFile`C#/propriedade bound ( ). Para obter mais informações, consulte o valor do [atributo nome match ao nome do parâmetro da](#match-name-attribute-value-to-parameter-name-of-post-method) seção do método POST.

O exemplo a seguir:

* Loops através de um ou mais arquivos carregados.
* Usa [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo. 
* Salva os arquivos para o sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.
* Retorna o número total e o tamanho dos arquivos carregados.

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

Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho. No exemplo a seguir, o caminho é obtido a partir da configuração:

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

O caminho passado <xref:System.IO.FileStream> para o *deve* incluir o nome do arquivo. Se o nome do arquivo não <xref:System.UnauthorizedAccessException> for fornecido, um é jogado em tempo de execução.

Os arquivos carregados usando a <xref:Microsoft.AspNetCore.Http.IFormFile> técnica são protegidos na memória ou no disco no servidor antes do processamento. Dentro do método <xref:Microsoft.AspNetCore.Http.IFormFile> de ação, <xref:System.IO.Stream>os conteúdos são acessíveis como um . Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como [o armazenamento Azure Blob](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Para outro exemplo que faz loops sobre vários arquivos para upload e usa nomes de arquivos seguros, consulte *Páginas/BufferedMultipleFileUploadPhysical.cshtml.cs* no aplicativo de exemplo.

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) lança <xref:System.IO.IOException> um se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores. O limite de 65.535 arquivos é um limite por servidor. Para obter mais informações sobre esse limite no sistema operacional Windows, consulte as observações nos seguintes tópicos:
>
> * [Função GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Carregar pequenos arquivos com modelo tampão vinculando a um banco de dados

Para armazenar dados de arquivos binários em <xref:System.Byte> um banco de dados usando o Entity [Framework,](/ef/core/index)defina uma propriedade de array na entidade:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Especifique uma propriedade de modelo <xref:Microsoft.AspNetCore.Http.IFormFile>de página para a classe que inclui um:

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
> <xref:Microsoft.AspNetCore.Http.IFormFile>pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade modelo vinculada. O exemplo anterior usa uma propriedade de modelo vinculado.

O `FileUpload` é usado no formulário Páginas de Navalha:

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

Quando o formulário for POSTed para <xref:Microsoft.AspNetCore.Http.IFormFile> o servidor, copie o para um fluxo e salve-o como uma matriz de bytes no banco de dados. No exemplo a `_dbContext` seguir, armazena o contexto de banco de dados do aplicativo:

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

O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de amostra:

* *Páginas/BufferedSingleFileUploadDb.cshtml*
* *Páginas/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.
>
> Não confie ou confie `FileName` na <xref:Microsoft.AspNetCore.Http.IFormFile> propriedade sem validação. A `FileName` propriedade só deve ser usada para fins de exibição e somente após a codificação HTML.
>
> Os exemplos fornecidos não levam em conta considerações de segurança. Informações adicionais são fornecidas pelas seguintes seções e pelo [aplicativo de amostra:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)
>
> * [Considerações sobre segurança](#security-considerations)
> * [Validação](#validation)

### <a name="upload-large-files-with-streaming"></a>Carregar arquivos grandes com streaming

O exemplo a seguir demonstra como usar javaScript para transmitir um arquivo para uma ação controladora. O token antiforgery do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo de solicitação. Como o método de ação processa os dados enviados diretamente, a vinculação do modelo de formulário é desativada por outro filtro personalizado. Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado. Depois que as seções de várias partes são lidas, a ação executa sua própria vinculação de modelo.

A resposta inicial da página carrega o formulário e salva um token `GenerateAntiforgeryTokenCookieAttribute` antiforgery em um cookie (através do atributo). O atributo usa o [suporte antiforgery](xref:security/anti-request-forgery) incorporado do ASP.NET Core para definir um cookie com um token de solicitação:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

O `DisableFormValueModelBindingAttribute` é usado para desativar a vinculação do modelo:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

No aplicativo de `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` exemplo, e são aplicados como `/StreamedSingleFileUploadDb` filtros aos modelos de aplicação de página de e `/StreamedSingleFileUploadPhysical` em `Startup.ConfigureServices` usar convenções de Páginas de [Navalha](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

Como a vinculação do modelo não lê o formulário, os parâmetros vinculados ao formulário não se vinculam (consulta, rota e cabeçalho continuam a funcionar). O método de ação `Request` funciona diretamente com a propriedade. Um `MultipartReader` é usado para ler cada seção. Os dados de chave/valor são armazenados em um `KeyValueAccumulator`. Depois que as seções de várias `KeyValueAccumulator` partes são lidas, o conteúdo do é usado para vincular os dados do formulário a um tipo de modelo.

O `StreamingController.UploadDatabase` método completo para transmitir para um banco de dados com o EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Utilitários/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

O `StreamingController.UploadPhysical` método completo para streaming para um local físico:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

No aplicativo de amostra, as `FileHelpers.ProcessStreamedFile`verificações de validação são tratadas por .

## <a name="validation"></a>Validação

A classe do `FileHelpers` aplicativo de exemplo demonstra <xref:Microsoft.AspNetCore.Http.IFormFile> várias verificações para uploads de arquivos tamponados e transmitidos. Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> uploads de arquivos tamponados `ProcessFormFile` no aplicativo de amostra, consulte o método no arquivo *Utilities/FileHelpers.cs.* Para processar arquivos transmitidos, `ProcessStreamedFile` consulte o método no mesmo arquivo.

> [!WARNING]
> Os métodos de processamento de validação demonstrados no aplicativo de amostra não digitalização do conteúdo dos arquivos enviados. Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para usuários ou outros sistemas.
>
> Embora a amostra de tópico forneça um exemplo de `FileHelpers` trabalho de técnicas de validação, não implemente a classe em um aplicativo de produção, a menos que você:
>
> * Entenda completamente a implementação.
> * Modifique a implementação conforme apropriado para o ambiente e especificações do aplicativo.
>
> **Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**

### <a name="content-validation"></a>Validação de conteúdo

**Use uma API de varredura de vírus/malware de terceiros no conteúdo carregado.**

A varredura de arquivos exige recursos do servidor em cenários de alto volume. Se o desempenho do processamento de solicitações for diminuído devido à digitalização de arquivos, considere descarregar o trabalho de digitalização para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo. Normalmente, os arquivos enviados são mantidos em uma área de quarentena até que o scanner de vírus de fundo os verifique. Quando um arquivo passa, o arquivo é movido para o local normal de armazenamento de arquivos. Essas etapas geralmente são realizadas em conjunto com um registro de banco de dados que indica o status de digitalização de um arquivo. Ao usar essa abordagem, o aplicativo e o servidor do aplicativo permanecem focados em responder às solicitações.

### <a name="file-extension-validation"></a>Validação de extensão de arquivo

A extensão do arquivo enviado deve ser verificada em uma lista de extensões permitidas. Por exemplo:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validação da assinatura do arquivo

A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo. Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo. O aplicativo de amostra verifica assinaturas de arquivos para alguns tipos de arquivos comuns. No exemplo a seguir, a assinatura do arquivo de uma imagem JPEG é verificada no arquivo:

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

Para obter assinaturas adicionais de arquivos, consulte o [Banco de Dados de Assinaturas de Arquivos](https://www.filesignatures.net/) e as especificações oficiais do arquivo.

### <a name="file-name-security"></a>Segurança do nome do arquivo

Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo para armazenamento físico. Crie um nome de arquivo seguro para o arquivo usando [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.

Razor codifica automaticamente os valores de propriedade para exibição. O seguinte código é seguro de usar:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Fora do Razor, sempre <xref:System.Net.WebUtility.HtmlEncode*> arquive o conteúdo do nome a partir da solicitação de um usuário.

Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo é substituído por um arquivo de mesmo nome. Forneça lógica adicional para atender às especificações do seu aplicativo.

### <a name="size-validation"></a>Validação de tamanho

Limitar o tamanho dos arquivos enviados.

No aplicativo de amostra, o tamanho do arquivo é limitado a 2 MB (indicado em bytes). O limite é fornecido via [Configuração](xref:fundamentals/configuration/index) a partir do arquivo *appsettings.json:*

```json
{
  "FileSizeLimit": 2097152
}
```

O `FileSizeLimit` é injetado nas `PageModel` classes:

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

Quando um tamanho de arquivo excede o limite, o arquivo é rejeitado:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Valor de atributo do nome de correspondência ao nome do parâmetro do método POST

Em formulários não-Razor que formam dados `FormData` do POST ou usam diretamente do JavaScript, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.

No exemplo a seguir:

* Ao usar `<input>` um `name` elemento, o atributo é definido como valor: `battlePlans`

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Ao `FormData` usar em JavaScript, o nome `battlePlans`é definido como o valor:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Use um nome correspondente para o parâmetro`battlePlans`do método C# ( ):

* Para um método de `Upload`manipulador de páginas de lâminas chamado :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Para um método de ação do controlador MVC POST:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configuração do servidor e do aplicativo

### <a name="multipart-body-length-limit"></a>Limite de comprimento do corpo em várias partes

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>define o limite para o comprimento de cada corpo multipart. As seções de formulário <xref:System.IO.InvalidDataException> que excedem esse limite lançam um quando analisados. A inadimplência é 134.217.728 (128 MB). Personalize o limite <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> usando `Startup.ConfigureServices`a configuração em :

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>é usado para <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> definir o para uma única página ou ação.

Em um aplicativo Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) aplique `Startup.ConfigureServices`o filtro com uma convenção em :

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

Em um aplicativo Razor Pages ou um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel tamanho máximo do corpo de solicitação

Para aplicativos hospedados pela Kestrel, o tamanho máximo do corpo de solicitação padrão é de 30.000.000 bytes, o que é aproximadamente 28,6 MB. Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

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

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.

Em um aplicativo Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) aplique `Startup.ConfigureServices`o filtro com uma convenção em :

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

Em um aplicativo de páginas de navalha ou em um aplicativo MVC, aplique o filtro na classe de manipulador de páginas ou no método de ação:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

O `RequestSizeLimitAttribute` também pode ser [`@attribute`](xref:mvc/views/razor#attribute) aplicado usando a diretiva Razor:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Outros limites de Kestrel

Outros limites de Kestrel podem se aplicar para aplicativos hospedados pela Kestrel:

* [Número máximo de conexões de cliente](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Taxas de dados de solicitação e resposta](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>Limite de comprimento do conteúdo IIS

O limite de`maxAllowedContentLength`solicitação padrão ( ) é de 30.000.000 bytes, que é de aproximadamente 28.6MB. Personalize o limite no arquivo *Web.config:*

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

Essa configuração só se aplica ao IIS. Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel. Para obter mais informações, consulte [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Limitações no módulo de ASP.NET ou presença do Módulo de Filtragem de Solicitações iIS podem limitar os uploads a 2 ou 4 GB. Para obter mais informações, consulte [Não é possível carregar um arquivo maior que 2GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Solucionar problemas

Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Erro não encontrado quando implantado em um servidor IIS

O erro a seguir indica que o arquivo enviado excede o comprimento de conteúdo configurado do servidor:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Para obter mais informações sobre o aumento do limite, consulte a seção [limite de comprimento de conteúdo do IIS.](#iis-content-length-limit)

### <a name="connection-failure"></a>Falha de conexão

Um erro de conexão e uma conexão de servidor de reset provavelmente indicam que o arquivo enviado excede o tamanho máximo do corpo de solicitação do Kestrel. Para obter mais informações, consulte a seção de tamanho do corpo de solicitação máxima de [Kestrel.](#kestrel-maximum-request-body-size) Os limites de conexão do cliente Kestrel também podem exigir ajustes.

### <a name="null-reference-exception-with-iformfile"></a>Exceção de referência nula com IFormFile

Se o controlador estiver aceitando <xref:Microsoft.AspNetCore.Http.IFormFile> arquivos `null`carregados usando, mas o `enctype` valor `multipart/form-data`é, confirme se o formulário HTML está especificando um valor de . Se esse atributo não `<form>` estiver definido no elemento, o upload <xref:Microsoft.AspNetCore.Http.IFormFile> do `null`arquivo não ocorrerá e quaisquer argumentos vinculados serão . Também confirme que a [nomeação de upload em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>O fluxo era muito longo.

Os exemplos neste tópico <xref:System.IO.MemoryStream> dependem de manter o conteúdo do arquivo enviado. O limite de `MemoryStream` `int.MaxValue`tamanho de a é . Se o cenário de upload de arquivos do aplicativo requer manter conteúdo de arquivo maior `MemoryStream` que 50 MB, use uma abordagem alternativa que não dependa de um único para segurar o conteúdo de um arquivo carregado.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core suporta o upload de um ou mais arquivos usando vinculação de modelo tampão para arquivos menores e streaming não tamponado para arquivos maiores.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considerações sobre segurança

Tenha cuidado ao fornecer aos usuários a capacidade de carregar arquivos para um servidor. Os atacantes podem tentar:

* Execute ataques [de negação de serviço.](/windows-hardware/drivers/ifs/denial-of-service)
* Envie vírus ou malware.
* Comprometer redes e servidores de outras formas.

As etapas de segurança que reduzem a probabilidade de um ataque bem sucedido são:

* Faça upload de arquivos para uma área de upload de arquivos dedicada, de preferência para uma unidade não-sistema. Um local dedicado facilita a imposição de restrições de segurança em arquivos enviados. Desativar as permissões de execução no local de upload do arquivo.&dagger;
* **Não** persista arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;
* Use um nome de arquivo seguro determinado pelo aplicativo. Não use um nome de arquivo fornecido pelo usuário ou o nome do arquivo não confiável do arquivo carregado. &dagger; HTML codificar o nome do arquivo não confiável ao exibi-lo. Por exemplo, registrar o nome do arquivo ou exibir em UI (Razor automaticamente HTML codifica a saída).
* Permitir apenas extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Verifique se as verificações do lado do cliente são realizadas no servidor. &dagger; Verificações do lado do cliente são fáceis de contornar.
* Verifique o tamanho de um arquivo enviado. Defina um limite máximo de tamanho para evitar grandes uploads.&dagger;
* Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.
* **Execute um scanner de vírus/malware no conteúdo carregado antes que o arquivo seja armazenado.**

&dagger;O aplicativo de amostra demonstra uma abordagem que atende aos critérios.

> [!WARNING]
> Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:
>
> * Ganhe completamente o controle de um sistema.
> * Sobrecarregue um sistema com o resultado que o sistema trava.
> * Comprometer dados do sistema ou de usuários.
> * Aplique pichações em uma ui pública.
>
> Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:
>
> * [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)
> * [Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Para obter mais informações sobre a implementação de medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [Validação.](#validation)

## <a name="storage-scenarios"></a>Cenários de armazenamento

As opções comuns de armazenamento para arquivos incluem:

* Banco de dados

  * Para pequenos uploads de arquivos, um banco de dados é muitas vezes mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).
  * Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, porque a recuperação de um registro de banco de dados para dados do usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de avatar).
  * Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dados.

* Armazenamento físico (sistema de arquivos ou compartilhamento de rede)

  * Para uploads de arquivos grandes:
    * Os limites do banco de dados podem restringir o tamanho do upload.
    * O armazenamento físico é muitas vezes menos econômico do que o armazenamento em um banco de dados.
  * O armazenamento físico é potencialmente mais barato do que o uso de um serviço de armazenamento de dados.
  * O processo do aplicativo deve ter permissões de leitura e gravação para o local de armazenamento. **Nunca conceda permissão de execução.**

* Serviço de armazenamento de dados (por exemplo, [Armazenamento Azure Blob)](https://azure.microsoft.com/services/storage/blobs/)

  * Os serviços geralmente oferecem maior escalabilidade e resiliência sobre soluções no local que geralmente estão sujeitas a pontos únicos de falha.
  * Os serviços são potencialmente mais baixos em grandes cenários de infra-estrutura de armazenamento.

  Para obter mais informações, consulte [Quickstart: Use .NET para criar uma bolha no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet). O tópico <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>demonstra, mas <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> pode <xref:System.IO.FileStream> ser usado para salvar <xref:System.IO.Stream>um armazenamento para blob ao trabalhar com um .

## <a name="file-upload-scenarios"></a>Cenários de upload de arquivos

Duas abordagens gerais para upload de arquivos são buffering e streaming.

**de resposta**

O arquivo inteiro é <xref:Microsoft.AspNetCore.Http.IFormFile>lido em uma representação C# do arquivo usado para processar ou salvar o arquivo.

Os recursos (disco, memória) usados pelos uploads de arquivos dependem do número e tamanho dos uploads de arquivos simultâneos. Se um aplicativo tentar buffer de muitos uploads, o site falha quando fica sem memória ou espaço em disco. Se o tamanho ou frequência dos uploads de arquivos estiver esgotando os recursos do aplicativo, use o streaming.

> [!NOTE]
> Qualquer único arquivo tamponado superior a 64 KB é movido da memória para um arquivo temporário no disco.

O buffer de pequenos arquivos é coberto nas seguintes seções deste tópico:

* [Armazenamento físico](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Banco de dados](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

O arquivo é recebido a partir de uma solicitação de várias partes e diretamente processado ou salvo pelo aplicativo. O streaming não melhora significativamente o desempenho. O streaming reduz as demandas por memória ou espaço em disco ao carregar arquivos.

O streaming de arquivos grandes é coberto no Upload de arquivos grandes com a seção [de streaming.](#upload-large-files-with-streaming)

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Carregar pequenos arquivos com modelo tampão vinculando ao armazenamento físico

Para carregar pequenos arquivos, use um formulário multiparte ou construa uma solicitação POST usando JavaScript.

O exemplo a seguir demonstra o uso de um formulário Razor Pages para carregar um único arquivo *(Páginas/BufferedSingleFileUploadPhysical.cshtml* no aplicativo de amostra):

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

O exemplo a seguir é análogo ao exemplo anterior, exceto que:

* JavaScript's[(Fetch API)](https://developer.mozilla.org/docs/Web/API/Fetch_API)é usado para enviar os dados do formulário.
* Não há validação.

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

Para executar o formulário POST em JavaScript para clientes que [não suportam a API Fetch,](https://caniuse.com/#feat=fetch)use uma das seguintes abordagens:

* Use um Fetch Polyfill (por exemplo, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Use `XMLHttpRequest`. Por exemplo:

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

Para suportar uploads de arquivos, os formulários`enctype`HTML `multipart/form-data`devem especificar um tipo de codificação ( ) de .

Para `files` que um elemento de entrada `multiple` suporte o `<input>` upload de vários arquivos forneça o atributo no elemento:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Os arquivos individuais carregados para o servidor podem <xref:Microsoft.AspNetCore.Http.IFormFile>ser acessados através do Model [Binding](xref:mvc/models/model-binding) usando . O aplicativo de exemplo demonstra vários uploads de arquivos tamponados para cenários de banco de dados e armazenamento físico.

<a name="filename2"></a>

> [!WARNING]
> **Não use** `FileName` a <xref:Microsoft.AspNetCore.Http.IFormFile> propriedade de outro além de exibição e registro. Ao exibir ou registrar, HTML codifica rexa o nome do arquivo. Um invasor pode fornecer um nome de arquivo malicioso, incluindo caminhos completos ou caminhos relativos. As aplicações devem:
>
> * Remova o caminho do nome de arquivo fornecido pelo usuário.
> * Salve o nome de arquivo codificado por HTML e removido de caminho para UI ou registro.
> * Gerar um novo nome de arquivo aleatório para armazenamento.
>
> O código a seguir remove o caminho do nome do arquivo:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Os exemplos fornecidos até agora não levam em conta considerações de segurança. Informações adicionais são fornecidas pelas seguintes seções e pelo [aplicativo de amostra:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)
>
> * [Considerações sobre segurança](#security-considerations)
> * [Validação](#validation)

Ao carregar arquivos usando <xref:Microsoft.AspNetCore.Http.IFormFile>vinculação de modelo e, o método de ação pode aceitar:

* Um <xref:Microsoft.AspNetCore.Http.IFormFile>único.
* Qualquer uma das seguintes coleções que representam vários arquivos:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Correspondências de vinculação formam arquivos por nome. Por exemplo, `name` o `<input type="file" name="formFile">` valor HTML deve corresponder ao parâmetro`FormFile`C#/propriedade bound ( ). Para obter mais informações, consulte o valor do [atributo nome match ao nome do parâmetro da](#match-name-attribute-value-to-parameter-name-of-post-method) seção do método POST.

O exemplo a seguir:

* Loops através de um ou mais arquivos carregados.
* Usa [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo. 
* Salva os arquivos para o sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.
* Retorna o número total e o tamanho dos arquivos carregados.

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

Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho. No exemplo a seguir, o caminho é obtido a partir da configuração:

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

O caminho passado <xref:System.IO.FileStream> para o *deve* incluir o nome do arquivo. Se o nome do arquivo não <xref:System.UnauthorizedAccessException> for fornecido, um é jogado em tempo de execução.

Os arquivos carregados usando a <xref:Microsoft.AspNetCore.Http.IFormFile> técnica são protegidos na memória ou no disco no servidor antes do processamento. Dentro do método <xref:Microsoft.AspNetCore.Http.IFormFile> de ação, <xref:System.IO.Stream>os conteúdos são acessíveis como um . Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como [o armazenamento Azure Blob](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Para outro exemplo que faz loops sobre vários arquivos para upload e usa nomes de arquivos seguros, consulte *Páginas/BufferedMultipleFileUploadPhysical.cshtml.cs* no aplicativo de exemplo.

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) lança <xref:System.IO.IOException> um se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores. O limite de 65.535 arquivos é um limite por servidor. Para obter mais informações sobre esse limite no sistema operacional Windows, consulte as observações nos seguintes tópicos:
>
> * [Função GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Carregar pequenos arquivos com modelo tampão vinculando a um banco de dados

Para armazenar dados de arquivos binários em <xref:System.Byte> um banco de dados usando o Entity [Framework,](/ef/core/index)defina uma propriedade de array na entidade:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Especifique uma propriedade de modelo <xref:Microsoft.AspNetCore.Http.IFormFile>de página para a classe que inclui um:

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
> <xref:Microsoft.AspNetCore.Http.IFormFile>pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade modelo vinculada. O exemplo anterior usa uma propriedade de modelo vinculado.

O `FileUpload` é usado no formulário Páginas de Navalha:

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

Quando o formulário for POSTed para <xref:Microsoft.AspNetCore.Http.IFormFile> o servidor, copie o para um fluxo e salve-o como uma matriz de bytes no banco de dados. No exemplo a `_dbContext` seguir, armazena o contexto de banco de dados do aplicativo:

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

O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de amostra:

* *Páginas/BufferedSingleFileUploadDb.cshtml*
* *Páginas/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.
>
> Não confie ou confie `FileName` na <xref:Microsoft.AspNetCore.Http.IFormFile> propriedade sem validação. A `FileName` propriedade só deve ser usada para fins de exibição e somente após a codificação HTML.
>
> Os exemplos fornecidos não levam em conta considerações de segurança. Informações adicionais são fornecidas pelas seguintes seções e pelo [aplicativo de amostra:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)
>
> * [Considerações sobre segurança](#security-considerations)
> * [Validação](#validation)

### <a name="upload-large-files-with-streaming"></a>Carregar arquivos grandes com streaming

O exemplo a seguir demonstra como usar javaScript para transmitir um arquivo para uma ação controladora. O token antiforgery do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo de solicitação. Como o método de ação processa os dados enviados diretamente, a vinculação do modelo de formulário é desativada por outro filtro personalizado. Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado. Depois que as seções de várias partes são lidas, a ação executa sua própria vinculação de modelo.

A resposta inicial da página carrega o formulário e salva um token `GenerateAntiforgeryTokenCookieAttribute` antiforgery em um cookie (através do atributo). O atributo usa o [suporte antiforgery](xref:security/anti-request-forgery) incorporado do ASP.NET Core para definir um cookie com um token de solicitação:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

O `DisableFormValueModelBindingAttribute` é usado para desativar a vinculação do modelo:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

No aplicativo de `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` exemplo, e são aplicados como `/StreamedSingleFileUploadDb` filtros aos modelos de aplicação de página de e `/StreamedSingleFileUploadPhysical` em `Startup.ConfigureServices` usar convenções de Páginas de [Navalha](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Como a vinculação do modelo não lê o formulário, os parâmetros vinculados ao formulário não se vinculam (consulta, rota e cabeçalho continuam a funcionar). O método de ação `Request` funciona diretamente com a propriedade. Um `MultipartReader` é usado para ler cada seção. Os dados de chave/valor são armazenados em um `KeyValueAccumulator`. Depois que as seções de várias `KeyValueAccumulator` partes são lidas, o conteúdo do é usado para vincular os dados do formulário a um tipo de modelo.

O `StreamingController.UploadDatabase` método completo para transmitir para um banco de dados com o EF Core:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Utilitários/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

O `StreamingController.UploadPhysical` método completo para streaming para um local físico:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

No aplicativo de amostra, as `FileHelpers.ProcessStreamedFile`verificações de validação são tratadas por .

## <a name="validation"></a>Validação

A classe do `FileHelpers` aplicativo de exemplo demonstra <xref:Microsoft.AspNetCore.Http.IFormFile> várias verificações para uploads de arquivos tamponados e transmitidos. Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> uploads de arquivos tamponados `ProcessFormFile` no aplicativo de amostra, consulte o método no arquivo *Utilities/FileHelpers.cs.* Para processar arquivos transmitidos, `ProcessStreamedFile` consulte o método no mesmo arquivo.

> [!WARNING]
> Os métodos de processamento de validação demonstrados no aplicativo de amostra não digitalização do conteúdo dos arquivos enviados. Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para usuários ou outros sistemas.
>
> Embora a amostra de tópico forneça um exemplo de `FileHelpers` trabalho de técnicas de validação, não implemente a classe em um aplicativo de produção, a menos que você:
>
> * Entenda completamente a implementação.
> * Modifique a implementação conforme apropriado para o ambiente e especificações do aplicativo.
>
> **Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**

### <a name="content-validation"></a>Validação de conteúdo

**Use uma API de varredura de vírus/malware de terceiros no conteúdo carregado.**

A varredura de arquivos exige recursos do servidor em cenários de alto volume. Se o desempenho do processamento de solicitações for diminuído devido à digitalização de arquivos, considere descarregar o trabalho de digitalização para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo. Normalmente, os arquivos enviados são mantidos em uma área de quarentena até que o scanner de vírus de fundo os verifique. Quando um arquivo passa, o arquivo é movido para o local normal de armazenamento de arquivos. Essas etapas geralmente são realizadas em conjunto com um registro de banco de dados que indica o status de digitalização de um arquivo. Ao usar essa abordagem, o aplicativo e o servidor do aplicativo permanecem focados em responder às solicitações.

### <a name="file-extension-validation"></a>Validação de extensão de arquivo

A extensão do arquivo enviado deve ser verificada em uma lista de extensões permitidas. Por exemplo:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validação da assinatura do arquivo

A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo. Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo. O aplicativo de amostra verifica assinaturas de arquivos para alguns tipos de arquivos comuns. No exemplo a seguir, a assinatura do arquivo de uma imagem JPEG é verificada no arquivo:

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

Para obter assinaturas adicionais de arquivos, consulte o [Banco de Dados de Assinaturas de Arquivos](https://www.filesignatures.net/) e as especificações oficiais do arquivo.

### <a name="file-name-security"></a>Segurança do nome do arquivo

Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo para armazenamento físico. Crie um nome de arquivo seguro para o arquivo usando [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.

Razor codifica automaticamente os valores de propriedade para exibição. O seguinte código é seguro de usar:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Fora do Razor, sempre <xref:System.Net.WebUtility.HtmlEncode*> arquive o conteúdo do nome a partir da solicitação de um usuário.

Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo é substituído por um arquivo de mesmo nome. Forneça lógica adicional para atender às especificações do seu aplicativo.

### <a name="size-validation"></a>Validação de tamanho

Limitar o tamanho dos arquivos enviados.

No aplicativo de amostra, o tamanho do arquivo é limitado a 2 MB (indicado em bytes). O limite é fornecido via [Configuração](xref:fundamentals/configuration/index) a partir do arquivo *appsettings.json:*

```json
{
  "FileSizeLimit": 2097152
}
```

O `FileSizeLimit` é injetado nas `PageModel` classes:

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

Quando um tamanho de arquivo excede o limite, o arquivo é rejeitado:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Valor de atributo do nome de correspondência ao nome do parâmetro do método POST

Em formulários não-Razor que formam dados `FormData` do POST ou usam diretamente do JavaScript, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.

No exemplo a seguir:

* Ao usar `<input>` um `name` elemento, o atributo é definido como valor: `battlePlans`

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Ao `FormData` usar em JavaScript, o nome `battlePlans`é definido como o valor:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Use um nome correspondente para o parâmetro`battlePlans`do método C# ( ):

* Para um método de `Upload`manipulador de páginas de lâminas chamado :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Para um método de ação do controlador MVC POST:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configuração do servidor e do aplicativo

### <a name="multipart-body-length-limit"></a>Limite de comprimento do corpo em várias partes

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>define o limite para o comprimento de cada corpo multipart. As seções de formulário <xref:System.IO.InvalidDataException> que excedem esse limite lançam um quando analisados. A inadimplência é 134.217.728 (128 MB). Personalize o limite <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> usando `Startup.ConfigureServices`a configuração em :

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>é usado para <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> definir o para uma única página ou ação.

Em um aplicativo Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) aplique `Startup.ConfigureServices`o filtro com uma convenção em :

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

Em um aplicativo Razor Pages ou um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel tamanho máximo do corpo de solicitação

Para aplicativos hospedados pela Kestrel, o tamanho máximo do corpo de solicitação padrão é de 30.000.000 bytes, o que é aproximadamente 28,6 MB. Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

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

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.

Em um aplicativo Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) aplique `Startup.ConfigureServices`o filtro com uma convenção em :

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

Em um aplicativo de páginas de navalha ou em um aplicativo MVC, aplique o filtro na classe de manipulador de páginas ou no método de ação:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Outros limites de Kestrel

Outros limites de Kestrel podem se aplicar para aplicativos hospedados pela Kestrel:

* [Número máximo de conexões de cliente](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Taxas de dados de solicitação e resposta](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>Limite de comprimento do conteúdo IIS

O limite de`maxAllowedContentLength`solicitação padrão ( ) é de 30.000.000 bytes, que é de aproximadamente 28.6MB. Personalize o limite no arquivo *Web.config:*

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

Essa configuração só se aplica ao IIS. Esse comportamento não ocorre por padrão quando a hospedagem é feita no Kestrel. Para obter mais informações, consulte [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Limitações no módulo de ASP.NET ou presença do Módulo de Filtragem de Solicitações iIS podem limitar os uploads a 2 ou 4 GB. Para obter mais informações, consulte [Não é possível carregar um arquivo maior que 2GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Solucionar problemas

Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Erro não encontrado quando implantado em um servidor IIS

O erro a seguir indica que o arquivo enviado excede o comprimento de conteúdo configurado do servidor:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Para obter mais informações sobre o aumento do limite, consulte a seção [limite de comprimento de conteúdo do IIS.](#iis-content-length-limit)

### <a name="connection-failure"></a>Falha de conexão

Um erro de conexão e uma conexão de servidor de reset provavelmente indicam que o arquivo enviado excede o tamanho máximo do corpo de solicitação do Kestrel. Para obter mais informações, consulte a seção de tamanho do corpo de solicitação máxima de [Kestrel.](#kestrel-maximum-request-body-size) Os limites de conexão do cliente Kestrel também podem exigir ajustes.

### <a name="null-reference-exception-with-iformfile"></a>Exceção de referência nula com IFormFile

Se o controlador estiver aceitando <xref:Microsoft.AspNetCore.Http.IFormFile> arquivos `null`carregados usando, mas o `enctype` valor `multipart/form-data`é, confirme se o formulário HTML está especificando um valor de . Se esse atributo não `<form>` estiver definido no elemento, o upload <xref:Microsoft.AspNetCore.Http.IFormFile> do `null`arquivo não ocorrerá e quaisquer argumentos vinculados serão . Também confirme que a [nomeação de upload em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>O fluxo era muito longo.

Os exemplos neste tópico <xref:System.IO.MemoryStream> dependem de manter o conteúdo do arquivo enviado. O limite de `MemoryStream` `int.MaxValue`tamanho de a é . Se o cenário de upload de arquivos do aplicativo requer manter conteúdo de arquivo maior `MemoryStream` que 50 MB, use uma abordagem alternativa que não dependa de um único para segurar o conteúdo de um arquivo carregado.

::: moniker-end


## <a name="additional-resources"></a>Recursos adicionais

* [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)
* [Segurança do Azure: Security Frame: Validação de entrada | Atenuações](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Padrões de design de nuvem do Azure: padrão de chave do manobrista](/azure/architecture/patterns/valet-key)
