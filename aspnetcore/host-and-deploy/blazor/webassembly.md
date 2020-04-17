---
title: Hospedar e implantar Blazor ASP.NET WebAssembly principal
author: guardrex
description: Saiba como hospedar e Blazor implantar um aplicativo usando ASP.NET Core, CDN (Content Delivery Networks, Redes de Entrega de Conteúdo), servidores de arquivos e páginas do GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f3508144f1e472ee906a35e427fc57f536008ab6
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488852"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a>Hospedar e implantar Blazor ASP.NET WebAssembly principal

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Com o [ Blazor modelo de hospedagem WebAssembly:](xref:blazor/hosting-models#blazor-webassembly)

* O Blazor aplicativo, suas dependências e o tempo de execução .NET são baixados para o navegador em paralelo.
* O aplicativo é executado diretamente no thread da interface do usuário do navegador.

As seguintes estratégias de implantação são suportadas:

* O Blazor aplicativo é servido por um aplicativo ASP.NET Core. Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).
* O Blazor aplicativo é colocado em um servidor ou serviço de hospedagem Blazor estática, onde o .NET não é usado para servir o aplicativo. Essa estratégia é abordada na seção [de implantação autônoma,](#standalone-deployment) que inclui informações sobre a hospedagem de um Blazor aplicativo WebAssembly como um subaplicativo IIS.

## <a name="brotli-precompression"></a>Precompressão de Brotli

Quando Blazor um aplicativo WebAssembly é publicado, a saída é pré-compactada usando o [algoritmo de compactação Brotli](https://tools.ietf.org/html/rfc7932) no nível mais alto para reduzir o tamanho do aplicativo e remover a necessidade de compactação em tempo de execução.

## <a name="rewrite-urls-for-correct-routing"></a>Reescrever as URLs para obter o roteamento correto

O roteamento de solicitações de componentes de página em Blazor um Blazor aplicativo WebAssembly não é tão simples quanto solicitações de roteamento em um aplicativo hospedado e de roteamento em um aplicativo hospedado. Considere Blazor um aplicativo WebAssembly com dois componentes:

* *Main.razor* &ndash; É carregado na raiz do aplicativo e contém um link para o componente `About` (`href="About"`).
* *About.Razor componente * &ndash; `About`.

Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):

1. O navegador faz uma solicitação.
1. A página padrão é retornada, que é geralmente é *index.html*.
1. A *index.html* inicia o aplicativo.
1. Blazoro roteador carrega, `Main` e o componente Razor é renderizado.

Na página Principal, selecionar o `About` link para o componente Blazor funciona no cliente porque o roteador impede o navegador de fazer uma solicitação na Internet `www.contoso.com` para `About` e serve o próprio componente renderizado. `About` Todas as solicitações de pontos finais internos *dentro do Blazor aplicativo WebAssembly* funcionam da mesma maneira: as solicitações não acionam solicitações baseadas no navegador para recursos hospedados em servidor na Internet. O roteador trata das solicitações internamente.

Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará. Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.

Como os navegadores fazem solicitações aos hosts baseados na Internet de páginas do lado do cliente, os servidores Web e os serviços de hospedagem precisam reescrever todas as solicitações de recursos que não estão fisicamente no servidor para a página *index.html*. Quando *o index.html* é devolvido, Blazor o roteador do aplicativo assume e responde com o recurso correto.

Ao implantar em um servidor IIS, você pode usar o Módulo de Regravação de URL com o arquivo *web.config* publicado do aplicativo. Para obter mais informações, consulte a seção [IIS.](#iis)

## <a name="hosted-deployment-with-aspnet-core"></a>Implantação hospedada com o ASP.NET Core

Uma *implantação hospedada* Blazor serve o aplicativo WebAssembly para navegadores de um [aplicativo ASP.NET Core](xref:index) que é executado em um servidor web.

O Blazor aplicativo WebAssembly do cliente é publicado na pasta */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* do aplicativo do servidor, juntamente com quaisquer outros ativos web estáticos do aplicativo do servidor. Os dois aplicativos são implantados juntos. É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core. Para uma implantação hospedada, o Visual Studio inclui`blazorwasm` o`-ho|--hosted` `dotnet new` ** Blazor ** modelo de projeto do WebAssembly App (modelo ao usar o novo comando [dotnet)](/dotnet/core/tools/dotnet-new) com a opção **Hosted** selecionada (ao usar o comando).

Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.

Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Implantação autônoma

Uma *implantação autônoma* Blazor serve o aplicativo WebAssembly como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes. Qualquer servidor de arquivos estático é capaz de servir o Blazor aplicativo.

Os ativos de implantação autônomo são publicados na pasta */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot.*

### <a name="iis"></a>IIS

O IIS é um Blazor servidor de arquivos estático capaz para aplicativos. Para configurar o IIS para hospedar, Blazorconsulte Construir um site [estático no IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Os ativos publicados são criados na pasta */bin/Release/{TARGET FRAMEWORK}/publish*. Hospede o conteúdo da pasta *publish* no servidor Web ou no serviço de hospedagem.

#### <a name="webconfig"></a>web.config

Quando Blazor um projeto é publicado, um arquivo *web.config* é criado com a seguinte configuração de IIS:

* Os tipos MIME são definidos para as seguintes extensões de arquivo:
  * *.dll* &ndash;`application/octet-stream`
  * *.json* &ndash;`application/json`
  * *.wasm* &ndash; `application/wasm`
  * *.woff* &ndash; `application/font-woff`
  * *.woff2* &ndash; `application/font-woff`
* A compactação HTTP está habilitada para os seguintes tipos MIME:
  * `application/octet-stream`
  * `application/wasm`
* As regras do Módulo de Reescrita de URL são estabelecidas:
  * Sirva o subdiretório onde residem os ativos estáticos do aplicativo *(wwwroot/{PATH REQUESTED}*).
  * Crie o roteamento de retorno do SPA para que as solicitações de ativos não arquivados sejam redirecionadas para o documento padrão do aplicativo em sua pasta de ativos estáticos *(wwwroot/index.html).*
  
#### <a name="use-a-custom-webconfig"></a>Use um web.config personalizado

Para usar um arquivo *web.config* personalizado:

1. Coloque o arquivo *web.config* personalizado na raiz da pasta do projeto.
1. Adicione o seguinte destino ao arquivo do projeto *(.csproj*):

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> O uso da propriedade `<IsWebConfigTransformDisabled>` MSBuild `true` não é Blazor suportado em aplicativos WebAssembly [como é para ASP.NET aplicativos Core implantados no IIS](xref:host-and-deploy/iis/index#webconfig-file). Para obter mais informações, consulte [O destino Copiar necessário para fornecer web.config PERSONALIZADO Blazor WASM (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).

#### <a name="install-the-url-rewrite-module"></a>Instalação do Módulo de Regeneração de URL

O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs. Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS). O módulo precisa ser baixado do site do IIS. Use o Web Platform Installer para instalar o módulo:

1. Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI. Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.
1. Copie o instalador para o servidor. Execute o instalador. Selecione o botão **Instalar** e aceite os termos de licença. Uma reinicialização do servidor não será necessária após a conclusão da instalação.

#### <a name="configure-the-website"></a>Configuração do site

Defina o **Caminho físico** do site como a pasta do aplicativo. A pasta contém:

* O arquivo *web.config* que o IIS usa para configurar o site, incluindo as regras de redirecionamento e os tipos de conteúdo do arquivo necessários.
* A pasta de ativos estática do aplicativo.

#### <a name="host-as-an-iis-sub-app"></a>Host como um subaplicativo IIS

Se um aplicativo autônomo estiver hospedado como um subaplicativo IIS, execute qualquer um dos seguintes:

* Desabilite o manipulador herdado do Módulo ASP.NET.

  Remova o manipulador Blazor no arquivo *web.config* publicado `<handlers>` do aplicativo adicionando uma seção ao arquivo:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Desativar `<system.webServer>` a herança da seção do aplicativo `<location>` raiz `inheritInChildApplications` (pai) usando um elemento com : `false`

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

A remoção do manipulador ou a desativação da herança é realizada, além de [configurar o caminho base do aplicativo](xref:host-and-deploy/blazor/index#app-base-path). Defina o caminho base do aplicativo no arquivo *index.html* do aplicativo do alias do IIS usado ao configurar o subaplicativo no IIS.

#### <a name="troubleshooting"></a>Solução de problemas

Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado. Quando o módulo não estiver instalado, o arquivo *web.config* não poderá ser analisado pelo IIS. Isso impede que o IIS Manager esteja carregando a Blazorconfiguração do site e o site de servir os arquivos estáticos.

Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Armazenamento do Azure

A hospedagem estática de Blazor arquivos [do Azure Storage](/azure/storage/) permite a hospedagem de aplicativos sem servidor. Nomes de domínio personalizados, CDN (Rede de Distribuição de Conteúdo) do Azure e HTTPS são compatíveis.

Quando o serviço de blob está habilitado para hospedagem de site estático em uma conta de armazenamento:

* Defina o **Nome do documento de índice** como `index.html`.
* Defina o **Caminho do documento de erro** como `index.html`. Os componentes do Razor e outros pontos de extremidade que não são arquivos não residem em caminhos físicos no conteúdo estático armazenado pelo serviço de blob. Quando uma solicitação de um desses Blazor recursos é recebida que o roteador deve lidar, o erro *404 - Não Encontrado* gerado pelo serviço blob encaminha a solicitação para o **caminho do documento de erro**. A *bolha index.html* é devolvida, Blazor e o roteador carrega e processa o caminho.

Para mais informações, confira [Hospedagem de site estático no Armazenamento do Azure](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

O seguinte arquivo *nginx.conf* é simplificado para mostrar como configurar o Nginx para enviar o arquivo *index.html* sempre que ele não pode encontrar um arquivo correspondente no disco.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).

### <a name="nginx-in-docker"></a>Nginx no Docker

Para Blazor hospedar no Docker usando o Nginx, configure o arquivo Docker para usar a imagem Nginx baseada em Alpine. Atualize o Dockerfile para copiar o arquivo *nginx.config* no contêiner.

Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Para implantar Blazor um aplicativo WebAssembly no CentOS 7 ou posterior:

1. Crie o arquivo de configuração Apache. O exemplo a seguir é um arquivo de configuração simplificado *(blazorapp.config*):

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Coloque o arquivo de `/etc/httpd/conf.d/` configuração Apache no diretório, que é o diretório de configuração Apache padrão no CentOS 7.

1. Coloque os arquivos do `/var/www/blazorapp` aplicativo no diretório (o `DocumentRoot` local especificado no arquivo de configuração).

1. Reinicie o serviço Apache.

Para obter mais informações, consulte [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>Páginas do GitHub

Para lidar com as reescritas de URL, adicione um arquivo *404.html* com um script que manipule o redirecionamento de solicitação para a página *index.html*. Para obter uma implementação de exemplo fornecida pela comunidade, confira [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Aplicativos de página única das Páginas do GitHub) ([rafrex/spa-github-pages no GitHub](https://github.com/rafrex/spa-github-pages#readme)). Um exemplo usando a abordagem da comunidade pode ser visto em [blazor-demo/blazor-demo.github.io no GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site dinâmico](https://blazor-demo.github.io/)).

Ao usar um site de projeto em vez de um site de empresa, adicione ou atualize a tag `<base>` no *index.html*. Defina o valor de atributo `href` com o nome do repositório GitHub com uma barra à direita (por exemplo, `my-repository/`).

## <a name="host-configuration-values"></a>Valores de configuração do host

Os aplicativos WebAssembly podem aceitar os seguintes valores de configuração do host como argumentos de linha de comando em tempo de execução no ambiente de desenvolvimento. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)

### <a name="content-root"></a>Raiz do conteúdo

O `--contentroot` argumento define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo (raiz de[conteúdo).](xref:fundamentals/index#content-root) Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**. Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* No Visual Studio, especifique o argumento em **argumentos** > de aplicativo**de depuração** > **de propriedades**. A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Caminho base

O `--pathbase` argumento define o caminho base do aplicativo para um aplicativo executado `<base>` `href` localmente com um `/` caminho de URL relativo não raiz (a tag está definida como um caminho diferente de preparação e produção). Nos exemplos a seguir, `/relative-URL-path` é o caminho base do aplicativo. Para obter mais informações, consulte [o caminho base do aplicativo](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`. Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**. Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* No Visual Studio, especifique o argumento em **argumentos** > de aplicativo**de depuração** > **de propriedades**. A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URLs

O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**. Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* No Visual Studio, especifique o argumento em **argumentos** > de aplicativo**de depuração** > **de propriedades**. A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Configurar o vinculador

Blazorexecuta a vinculação de Linguagem Intermediária (IL) em cada compilação de liberação para remover il desnecessário dos conjuntos de saída. Para obter mais informações, consulte <xref:host-and-deploy/blazor/configure-linker>.
