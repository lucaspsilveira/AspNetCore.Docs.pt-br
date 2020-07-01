---
title: Arquivos estáticos no ASP.NET Core
author: rick-anderson
description: Saiba como fornecer e proteger arquivos estáticos e configurar comportamentos do middleware de hospedagem de arquivos estáticos em um aplicativo Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 39c5c5d4875e1d59abaa6d998a09dbffd723214d
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793409"
---
# <a name="static-files-in-aspnet-core"></a>Arquivos estáticos no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)

Arquivos estáticos, como HTML, CSS, imagens e JavaScript, são ativos que um aplicativo ASP.NET Core atende diretamente aos clientes por padrão.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples/3x) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Fornecer arquivos estáticos

Os arquivos estáticos são armazenados no diretório [raiz da Web](xref:fundamentals/index#web-root) do projeto. O diretório padrão é `{content root}/wwwroot` , mas pode ser alterado com o método [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) . Para obter mais informações, consulte [raiz de conteúdo](xref:fundamentals/index#content-root) e [raiz da Web](xref:fundamentals/index#web-root).

O método <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> define a raiz do conteúdo como o diretório atual:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/Program2.cs?name=snippet_Main)]

O código anterior foi criado com o modelo de aplicativo Web.

Os arquivos estáticos podem ser acessados por meio de um caminho relativo à [raiz da Web](xref:fundamentals/index#web-root). Por exemplo, os modelos de projeto de **aplicativo Web** contêm várias pastas dentro da `wwwroot` pasta:

* `wwwroot`
  * `css`
  * `js`
  * `lib`

Considere criar a pasta *wwwroot/images* e adicionar o arquivo *wwwroot/images/MyImage.jpg* . O formato do URI para acessar um arquivo na `images` pasta é `https://<hostname>/images/<image_file_name>` . Por exemplo, `https://localhost:5001/images/MyImage.jpg`

### <a name="serve-files-in-web-root"></a>Fornecer arquivos na raiz da Web

Os modelos de aplicativo Web padrão chamam o <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> método no `Startup.Configure` , que permite que os arquivos estáticos sejam atendidos:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/Startup.cs?name=snippet&highlight=14)]

A sobrecarga do `UseStaticFiles` método sem parâmetros marca os arquivos na [raiz da Web](xref:fundamentals/index#web-root) como servable. A marcação a seguir referencia *wwwroot/images/MyImage.jpg*:

```html
<img src="~/images/MyImage.jpg"  class="img" alt="My image." />
```

No código anterior, o caractere til `~/` aponta para a [raiz da Web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Fornecer arquivos fora do diretório base

Considere uma hierarquia de diretório na qual os arquivos estáticos a serem servidos residem fora da [raiz da Web](xref:fundamentals/index#web-root):

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

Uma solicitação pode acessar o `red-rose.jpg` arquivo Configurando o middleware de arquivo estático da seguinte maneira:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupRose.cs?name=snippet&highlight=14-21)]

No código anterior, a hierarquia de diretórios *MyStaticFiles* é exposta publicamente por meio do segmento do URI *StaticFiles*. Uma solicitação para `https://<hostname>/StaticFiles/images/red-rose.jpg` servir o arquivo de *red-rose.jpg* .

A marcação a seguir faz referência a *MyStaticFiles/images/red-rose.jpg*:

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose." />
```

### <a name="set-http-response-headers"></a>Definir cabeçalhos de resposta HTTP

Um <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> objeto pode ser usado para definir cabeçalhos de resposta http. Além de configurar o serviço de arquivos estáticos na [raiz da Web](xref:fundamentals/index#web-root), o código a seguir define o `Cache-Control` cabeçalho:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupAddHeader.cs?name=snippet&highlight=14-23)]

Os arquivos estáticos são publicamente armazenáveis em cache por 600 segundos:

![Cabeçalho de resposta mostrando que o cabeçalho Cache-Control foi adicionado](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorização de arquivo estático

O middleware de arquivos estáticos não fornece verificações de autorização. Todos os arquivos servidos por ele, incluindo aqueles em `wwwroot` , estão publicamente acessíveis. Para fornecer arquivos com base na autorização:

* Armazene-os fora do `wwwroot` e de qualquer diretório acessível ao middleware de arquivo estático.
* Sirva-os por meio de um método de ação ao qual a autorização é aplicada e retorna um objeto [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) :

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="directory-browsing"></a>Pesquisa no diretório

A pesquisa no diretório permite a listagem de diretório em diretórios especificados.

A pesquisa no diretório é desabilitada por padrão por motivos de segurança. Para obter mais informações, consulte [considerações](#sc).

Habilitar pesquisa no diretório com:

* <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>em `Startup.ConfigureServices` .
* [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) em `Startup.Configure` .

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupBrowse.cs?name=snippet&highlight=4,20-34)]

O código anterior permite a navegação no diretório da pasta *wwwroot/images* usando a URL `https://<hostname>/MyImages` , com links para cada arquivo e pasta:

![navegação no diretório](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a>Fornecer documentos padrão

A definição de uma página padrão fornece aos visitantes um ponto de partida em um site. Para servir uma página padrão de `wwwroot` sem um URI totalmente qualificado, chame o <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> método:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty.cs?name=snippet&highlight=14)]

`UseDefaultFiles` deve ser chamado antes de `UseStaticFiles` para fornecer o arquivo padrão. `UseDefaultFiles`é um regravador de URL que não atende ao arquivo.

Com `UseDefaultFiles` o, solicitações para uma pasta na `wwwroot` pesquisa por:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

O primeiro arquivo encontrado na lista é fornecido como se a solicitação fosse o URI totalmente qualificado. A URL do navegador continua refletindo o URI solicitado.

O seguinte código altera o nome de arquivo padrão para *mydefault.html*:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupDefault.cs?name=snippet2)]

O código a seguir mostra `Startup.Configure` o código anterior:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupDefault.cs?name=snippet)]

### <a name="usefileserver-for-default-documents"></a>UseFileServer para documentos padrão

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>combina a funcionalidade de `UseStaticFiles` , `UseDefaultFiles` e opcionalmente `UseDirectoryBrowser` .

Chame `app.UseFileServer` para habilitar o serviço de arquivos estáticos e o arquivo padrão. A navegação no diretório não está habilitada. O código a seguir `Startup.Configure` mostra `UseFileServer` :

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty2.cs?name=snippet&highlight=14)]

O código a seguir permite o serviço de arquivos estáticos, o arquivo padrão e a pesquisa no diretório:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

O código a seguir mostra `Startup.Configure` o código anterior:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty3.cs?name=snippet&highlight=14-18)]

Considere a seguinte hierarquia de diretórios:

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

O código a seguir permite o serviço de arquivos estáticos, o arquivo padrão e a pesquisa no diretório `MyStaticFiles` :

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupUseFileServer.cs?name=snippet&highlight=4,20-30)]

<xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>deve ser chamado quando o `EnableDirectoryBrowsing` valor da propriedade é `true` .

Com o uso da hierarquia de arquivos e do código anterior, as URLs são resolvidas da seguinte maneira:

| URI            |      Resposta  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | *MyStaticFiles/imagens/MyImage.jpg* |
| `https://<hostname>/StaticFiles` | *MyStaticFiles/default.html* |

Se nenhum arquivo nomeado padrão existir no diretório *MyStaticFiles* , `https://<hostname>/StaticFiles` o retornará a listagem de diretório com links clicáveis:

![Lista de arquivos estáticos](static-files/_static/db2.png)

<xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>e <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> Execute um redirecionamento do lado do cliente a partir do URI de destino sem uma trilha `/` para o URI de destino com uma à direita `/` . Por exemplo, de `https://<hostname>/StaticFiles` para `https://<hostname>/StaticFiles/` . As URLs relativas no diretório *StaticFiles* são inválidas sem uma barra à direita ( `/` ).

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

A classe [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) contém uma `Mappings` propriedade que serve como um mapeamento de extensões de arquivo para tipos de conteúdo MIME. No exemplo a seguir, várias extensões de arquivo são mapeadas para tipos MIME conhecidos. A extensão *. rtf* é substituída e *. mp4* é removido:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupFileExtensionContentTypeProvider.cs?name=snippet2)]

O código a seguir mostra `Startup.Configure` o código anterior:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupFileExtensionContentTypeProvider.cs?name=snippet&highlight=14-42)]

Consulte [Tipos de conteúdo MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Tipos de conteúdo não padrão

O middleware de arquivo estático compreende quase 400 tipos de conteúdo de arquivo conhecidos. Se o usuário solicitar um arquivo com um tipo de arquivo desconhecido, o middleware de arquivo estático passará a solicitação para o próximo middleware no pipeline. Se nenhum middleware manipular a solicitação, uma resposta *404 Não Encontrado* será retornada. Se a navegação no diretório estiver habilitada, um link para o arquivo será exibido na lista do diretório.

O seguinte código habilita o fornecimento de tipos desconhecidos e renderiza o arquivo desconhecido como uma imagem:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupServeUnknownFileTypes.cs?name=snippet2)]

O código a seguir mostra `Startup.Configure` o código anterior:

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupServeUnknownFileTypes.cs?name=snippet&highlight=14-18)]

Com o código anterior, uma solicitação para um arquivo com um tipo de conteúdo desconhecido é retornada como uma imagem.

> [!WARNING]
> A habilitação de [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) é um risco de segurança. Ela está desabilitada por padrão, e seu uso não é recomendado. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fornece uma alternativa mais segura para o fornecimento de arquivos com extensões não padrão.

## <a name="serve-files-from-multiple-locations"></a>Fornecer arquivos de vários locais

`UseStaticFiles`e `UseFileServer` o padrão para o provedor de arquivos que aponta para `wwwroot` . Instâncias adicionais do `UseStaticFiles` e do `UseFileServer` podem ser fornecidas com outros provedores de arquivos para fornecer arquivos de outros locais. Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a>Considerações de segurança para arquivos estáticos

> [!WARNING]
> `UseDirectoryBrowser` e `UseStaticFiles` podem causar a perda de segredos. A desabilitação da navegação no diretório em produção é altamente recomendada. Examine com atenção os diretórios que são habilitados por meio de `UseStaticFiles` ou `UseDirectoryBrowser`. Todo o diretório e seus subdiretórios se tornam publicamente acessíveis. Armazene arquivos adequados para servir ao público em um diretório dedicado, como `<content_root>/wwwroot` . Separe esses arquivos de exibições do MVC, Razor páginas, arquivos de configuração, etc.

* As URLs para o conteúdo exposto com `UseDirectoryBrowser` e `UseStaticFiles` estão sujeitas à diferenciação de maiúsculas e minúsculas e a restrições de caracteres do sistema de arquivos subjacente. Por exemplo, o Windows não diferencia maiúsculas de minúsculas, mas o macOS e o Linux não são.

* Os aplicativos ASP.NET Core hospedados no IIS usam o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para encaminhar todas as solicitações ao aplicativo, inclusive as solicitações de arquivo estático. O manipulador de arquivos estáticos do IIS não é usado e não tem a chance de lidar com solicitações.

* Conclua as etapas seguinte no Gerenciador do IIS para remover o manipulador de arquivos estáticos no IIS no nível do servidor ou do site:
    1. Navegue para o recurso **Módulos**.
    1. Selecione **StaticFileModule** na lista.
    1. Clique em **Remover** na barra lateral **Ações**.

> [!WARNING]
> Se o manipulador de arquivo estático do IIS estiver habilitado **e** o Módulo do ASP.NET Core não estiver configurado corretamente, os arquivos estáticos serão atendidos. Isso acontece, por exemplo, se o arquivo *web.config* não foi implantado.

* Coloque os arquivos de código, incluindo *. cs* e *. cshtml*, fora da [raiz da Web](xref:fundamentals/index#web-root)do projeto de aplicativo. Portanto, uma separação lógica é criada entre o conteúdo do lado do cliente do aplicativo e o código baseado em servidor. Isso impede a perda de código do lado do servidor.

## <a name="additional-resources"></a>Recursos adicionais

* [Middleware](xref:fundamentals/middleware/index)
* [Introdução ao ASP.NET Core](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Scott Addie](https://twitter.com/Scott_Addie)

Arquivos estáticos, como HTML, CSS, imagens e JavaScript, são ativos que um aplicativo ASP.NET Core fornece diretamente para os clientes. Algumas etapas de configuração são necessárias para habilitar o fornecimento desses arquivos.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Fornecer arquivos estáticos

Os arquivos estáticos são armazenados no diretório [raiz da Web](xref:fundamentals/index#web-root) do projeto. O diretório padrão é *{Content root}/wwwroot*, mas pode ser alterado por meio do método [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) . Consulte [Raiz de conteúdo](xref:fundamentals/index#content-root) e [Diretório base](xref:fundamentals/index#web-root) para obter mais informações.

O host Web do aplicativo deve ser informado do diretório raiz do conteúdo.

O método `WebHost.CreateDefaultBuilder` define a raiz do conteúdo como o diretório atual:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

Os arquivos estáticos podem ser acessados por meio de um caminho relativo à [raiz da Web](xref:fundamentals/index#web-root). Por exemplo, o modelo de projeto de **aplicativo Web** contém várias pastas dentro da `wwwroot` pasta:

* `wwwroot`
  * `css`
  * `images`
  * `js`

O formato do URI para acessar um arquivo na *images* subpasta imagens *é \<server_address> http:// \<image_file_name> /images/*. Por exemplo, *http://localhost:9189/images/banner3.svg* .

Se estiver direcionando o .NET Framework, adicione o pacote [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto. Se você estiver direcionando para o .NET Core, o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluirá esse pacote.

Configure o [middleware](xref:fundamentals/middleware/index), que permite o serviço de arquivos estáticos.

### <a name="serve-files-inside-of-web-root"></a>Fornecer arquivos dentro do diretório base

Invoque o método [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dentro de `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

A sobrecarga do `UseStaticFiles` método sem parâmetros marca os arquivos na [raiz da Web](xref:fundamentals/index#web-root) como servable. A seguinte marcação referencia *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

No código anterior, o caractere til `~/` aponta para a [raiz da Web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Fornecer arquivos fora do diretório base

Considere uma hierarquia de diretório na qual os arquivos estáticos a serem servidos residem fora da [raiz da Web](xref:fundamentals/index#web-root):

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

Uma solicitação pode acessar o arquivo *banner1.svg* configurando o middleware de arquivos estáticos da seguinte maneira:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

No código anterior, a hierarquia de diretórios *MyStaticFiles* é exposta publicamente por meio do segmento do URI *StaticFiles*. Uma solicitação para *http:// \<server_address> /StaticFiles/images/banner1.svg* serve o arquivo *banner1. svg* .

A seguinte marcação referencia *MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Definir cabeçalhos de resposta HTTP

Um objeto [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) pode ser usado para definir cabeçalhos de resposta HTTP. Além de configurar o serviço de arquivos estáticos na [raiz da Web](xref:fundamentals/index#web-root), o código a seguir define o `Cache-Control` cabeçalho:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

O método [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) existe no pacote [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).

Os arquivos se tornaram armazenáveis em cache publicamente por 10 minutos (600 segundos) no ambiente de desenvolvimento:

![Cabeçalho de resposta mostrando que o cabeçalho Cache-Control foi adicionado](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorização de arquivo estático

O middleware de arquivos estáticos não fornece verificações de autorização. Todos os arquivos atendidos, incluindo aqueles em *wwwroot*, estão acessíveis publicamente. Para fornecer arquivos com base na autorização:

* Armazene-os fora do *wwwroot* e de qualquer diretório acessível ao middleware de arquivos estáticos.
* Forneça-os por meio de um método de ação ao qual a autorização é aplicada. Retorne um objeto [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult):

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Habilitar navegação no diretório

A navegação no diretório permite que os usuários do aplicativo Web vejam uma listagem de diretório e arquivos em um diretório especificado. A navegação no diretório está desabilitada por padrão por motivos de segurança (consulte [Considerações](#sc)). Habilite a navegação no diretório invocando o método [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) em `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Adicione os serviços necessários invocando o <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> método de `Startup.ConfigureServices` :

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

O código anterior permite a navegação no diretório da pasta *wwwroot/images* usando a URL *http:// \<server_address> /myImages*, com links para cada arquivo e pasta:

![navegação no diretório](static-files/_static/dir-browse.png)

Consulte [Considerações](#considerations) para saber mais sobre os riscos de segurança ao habilitar a navegação.

Observe as duas chamadas `UseStaticFiles` no exemplo a seguir. A primeira chamada permite o fornecimento de arquivos estáticos na pasta *wwwroot*. A segunda chamada habilita a navegação no diretório da pasta *wwwroot/images* usando a URL *http:// \<server_address> /myImages*:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Fornecer um documento padrão

Definir uma home page padrão fornece ao visitantes um ponto de partida lógico de ao visitar o site. Para fornecer uma página padrão sem qualificar totalmente o URI do usuário, chame o [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) método de `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles` deve ser chamado antes de `UseStaticFiles` para fornecer o arquivo padrão. `UseDefaultFiles` é um rewriter de URL que, na verdade, não fornece o arquivo. Habilite o middleware de arquivos estáticos por meio de `UseStaticFiles` para fornecer o arquivo.

Com `UseDefaultFiles`, as solicitações para uma pasta pesquisam:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

O primeiro arquivo encontrado na lista é fornecido como se a solicitação fosse o URI totalmente qualificado. A URL do navegador continua refletindo o URI solicitado.

O seguinte código altera o nome de arquivo padrão para *mydefault.html*:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>combina a funcionalidade de `UseStaticFiles` , `UseDefaultFiles` e opcionalmente `UseDirectoryBrowser` .

O código a seguir permite o fornecimento de arquivos estáticos e do arquivo padrão. A navegação no diretório não está habilitada.

```csharp
app.UseFileServer();
```

O seguinte código baseia-se na sobrecarga sem parâmetros, habilitando a navegação no diretório:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Considere a seguinte hierarquia de diretórios:

* **wwwroot**
  * **CSS**
  * **images**
  * **JS**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

O seguinte código habilita arquivos estáticos, arquivos padrão e a navegação no diretório de `MyStaticFiles`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser` precisa ser chamado quando o valor da propriedade `EnableDirectoryBrowsing` é `true`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Com o uso da hierarquia de arquivos e do código anterior, as URLs são resolvidas da seguinte maneira:

| URI            |                             Resposta  |
| ------- | ------|
| *http:// \<server_address> /StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http:// \<server_address> /StaticFiles*             |     MyStaticFiles/default.html |

Se nenhum arquivo nomeado padrão existir no diretório *MyStaticFiles* , *http:// \<server_address> /StaticFiles* retornará a listagem de diretório com links clicáveis:

![Lista de arquivos estáticos](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> e <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> executam um redirecionamento do lado do cliente de `http://{SERVER ADDRESS}/StaticFiles` (sem uma barra à direita) para `http://{SERVER ADDRESS}/StaticFiles/` (com uma barra à direita). As URLs relativas dentro do diretório *StaticFiles* são inválidas sem uma barra "\" à direita.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

A classe [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) contém uma propriedade `Mappings` que serve como um mapeamento de extensões de arquivo para tipos de conteúdo MIME. Na amostra a seguir, várias extensões de arquivo são registradas para tipos MIME conhecidos. A extensão *.rtf* é substituída, e *.mp4* é removida.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Consulte [Tipos de conteúdo MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Tipos de conteúdo não padrão

O middleware de arquivo estático compreende quase 400 tipos de conteúdo de arquivo conhecidos. Se o usuário solicitar um arquivo com um tipo desconhecido, o middleware de arquivo estático passará a solicitação para o próximo middleware no pipeline. Se nenhum middleware manipular a solicitação, uma resposta *404 Não Encontrado* será retornada. Se a navegação no diretório estiver habilitada, um link para o arquivo será exibido na lista do diretório.

O seguinte código habilita o fornecimento de tipos desconhecidos e renderiza o arquivo desconhecido como uma imagem:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Com o código anterior, uma solicitação para um arquivo com um tipo de conteúdo desconhecido é retornada como uma imagem.

> [!WARNING]
> A habilitação de [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) é um risco de segurança. Ela está desabilitada por padrão, e seu uso não é recomendado. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fornece uma alternativa mais segura para o fornecimento de arquivos com extensões não padrão.

## <a name="serve-files-from-multiple-locations"></a>Fornecer arquivos de vários locais

`UseStaticFiles`e `UseFileServer` usa como padrão o provedor de arquivos que aponta em *wwwroot*. Você pode fornecer instâncias adicionais do `UseStaticFiles` e `UseFileServer` com outros provedores de arquivos para fornecer arquivos de outros locais. Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Considerações

> [!WARNING]
> `UseDirectoryBrowser` e `UseStaticFiles` podem causar a perda de segredos. A desabilitação da navegação no diretório em produção é altamente recomendada. Examine com atenção os diretórios que são habilitados por meio de `UseStaticFiles` ou `UseDirectoryBrowser`. Todo o diretório e seus subdiretórios se tornam publicamente acessíveis. Armazene arquivos adequados para servir ao público em um diretório dedicado, como * \<content_root> /wwwroot*. Separe esses arquivos de exibições do MVC, Razor páginas (somente 2. x), arquivos de configuração, etc.

* As URLs para o conteúdo exposto com `UseDirectoryBrowser` e `UseStaticFiles` estão sujeitas à diferenciação de maiúsculas e minúsculas e a restrições de caracteres do sistema de arquivos subjacente. Por exemplo, o Windows diferencia maiúsculas de minúsculas, o macOS e o Linux não.

* Os aplicativos ASP.NET Core hospedados no IIS usam o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para encaminhar todas as solicitações ao aplicativo, inclusive as solicitações de arquivo estático. O manipulador de arquivos estáticos do IIS não é usado. Ele não tem nenhuma possibilidade de manipular as solicitações antes que elas sejam manipuladas pelo módulo.

* Conclua as etapas seguinte no Gerenciador do IIS para remover o manipulador de arquivos estáticos no IIS no nível do servidor ou do site:
    1. Navegue para o recurso **Módulos**.
    1. Selecione **StaticFileModule** na lista.
    1. Clique em **Remover** na barra lateral **Ações**.

> [!WARNING]
> Se o manipulador de arquivo estático do IIS estiver habilitado **e** o Módulo do ASP.NET Core não estiver configurado corretamente, os arquivos estáticos serão atendidos. Isso acontece, por exemplo, se o arquivo *web.config* não foi implantado.

* Coloque os arquivos de código (incluindo *. cs* e *. cshtml*) fora da [raiz da Web](xref:fundamentals/index#web-root)do projeto de aplicativo. Portanto, uma separação lógica é criada entre o conteúdo do lado do cliente do aplicativo e o código baseado em servidor. Isso impede a perda de código do lado do servidor.

## <a name="additional-resources"></a>Recursos adicionais

* [Middleware](xref:fundamentals/middleware/index)
* [Introdução ao ASP.NET Core](xref:index)

::: moniker-end
