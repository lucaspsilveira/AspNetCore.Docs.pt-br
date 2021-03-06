---
title: Hospedar e implantar ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 2a2b0dabc26c14624144ce7eceb5861fe56f1054
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445132"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>Hospedar e implantar ASP.NET CoreBlazor WebAssembly

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)e [Safia Abdalla](https://safia.rocks)

Com o [ Blazor WebAssembly modelo de hospedagem](xref:blazor/hosting-models#blazor-webassembly):

* O Blazor aplicativo, suas dependências e o tempo de execução do .NET são baixados para o navegador em paralelo.
* O aplicativo é executado diretamente no thread da interface do usuário do navegador.

Há suporte para as seguintes estratégias de implantação:

* O Blazor aplicativo é servido por um aplicativo ASP.NET Core. Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).
* O Blazor aplicativo é colocado em um servidor Web ou serviço de hospedagem estática, em que o .net não é usado para servir o Blazor aplicativo. Essa estratégia é abordada na seção [implantação autônoma](#standalone-deployment) , que inclui informações sobre como hospedar um Blazor WebAssembly aplicativo como um subaplicativo do IIS.

## <a name="compression"></a>Compactação

Quando um Blazor WebAssembly aplicativo é publicado, a saída é compactada estaticamente durante a publicação para reduzir o tamanho do aplicativo e remover a sobrecarga de compactação de tempo de execução. Os seguintes algoritmos de compactação são usados:

* [Brotli](https://tools.ietf.org/html/rfc7932) (nível mais alto)
* [Gzip](https://tools.ietf.org/html/rfc1952)

Blazoro se baseia no host para o fornecer os arquivos compactados apropriados. Ao usar um projeto ASP.NET Core hospedado, o projeto host é capaz de executar a negociação de conteúdo e fornecer os arquivos compactados estaticamente. Ao hospedar um Blazor WebAssembly aplicativo autônomo, um trabalho adicional pode ser necessário para garantir que arquivos compactados estaticamente sejam atendidos:

* Para `web.config` a configuração de compactação do IIS, consulte a seção [IIS: Brotli e a compactação Gzip](#brotli-and-gzip-compression) . 
* Ao hospedar soluções de hospedagem estática que não dão suporte à negociação de conteúdo de arquivo compactado estaticamente, como páginas do GitHub, considere configurar o aplicativo para buscar e decodificar arquivos compactados Brotli:

  * Referencie o decodificador Brotli do [repositório GitHub do Google/Brotli](https://github.com/google/brotli/) no aplicativo.
  * Atualize o aplicativo para usar o decodificador. Altere a marcação dentro da marca de fechamento `<body>` `wwwroot/index.html` para o seguinte:
  
    ```html
    <script src="brotli.decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
          return (async function () {
            const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
            if (!response.ok) {
              throw new Error(response.statusText);
            }
            const originalResponseBuffer = await response.arrayBuffer();
            const originalResponseArray = new Int8Array(originalResponseBuffer);
            const decompressedResponseArray = BrotliDecode(originalResponseArray);
            const contentType = type === 
              'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
            return new Response(decompressedResponseArray, 
              { headers: { 'content-type': contentType } });
          })();
        }
      }
    });
    </script>
    ```
 
Para desabilitar a compactação, adicione a `BlazorEnableCompression` Propriedade MSBuild ao arquivo de projeto do aplicativo e defina o valor como `false` :

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a>Reescrever as URLs para obter o roteamento correto

O roteamento de solicitações para componentes de página em um Blazor WebAssembly aplicativo não é tão simples quanto o roteamento de solicitações em um Blazor Server aplicativo hospedado. Considere um Blazor WebAssembly aplicativo com dois componentes:

* `Main.razor`: Carrega na raiz do aplicativo e contém um link para o `About` componente ( `href="About"` ).
* `About.razor`: `About` componente.

Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):

1. O navegador faz uma solicitação.
1. A página padrão é retornada, o que geralmente é `index.html` .
1. `index.html`Inicializa o aplicativo.
1. Blazoro roteador do é carregado e o Razor `Main` componente é renderizado.

Na página principal, selecionar o link para o `About` componente funciona no cliente, pois o Blazor roteador interrompe o navegador de fazer uma solicitação na Internet para `www.contoso.com` `About` e serve o componente renderizado em `About` si. Todas as solicitações de pontos de extremidade internos *no Blazor WebAssembly aplicativo* funcionam da mesma maneira: as solicitações não disparam solicitações baseadas em navegador para recursos hospedados no servidor na Internet. O roteador trata das solicitações internamente.

Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará. Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.

Como os navegadores fazem solicitações para hosts baseados na Internet para páginas do lado do cliente, servidores Web e serviços de hospedagem devem regravar todas as solicitações de recursos não fisicamente no servidor para a `index.html` página. Quando `index.html` é retornado, o roteador do aplicativo Blazor assume o failover e responde com o recurso correto.

Ao implantar em um servidor IIS, você pode usar o módulo de reescrita de URL com o arquivo publicado do aplicativo `web.config` . Para obter mais informações, consulte a seção [IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Implantação hospedada com o ASP.NET Core

Uma *implantação hospedada* serve o Blazor WebAssembly aplicativo para os navegadores de um [aplicativo ASP.NET Core](xref:index) executado em um servidor Web.

O Blazor WebAssembly aplicativo cliente é publicado na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta do aplicativo de servidor, juntamente com quaisquer outros ativos da Web estáticos do aplicativo de servidor. Os dois aplicativos são implantados juntos. É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core. Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto de ** Blazor WebAssembly aplicativo** ( `blazorwasm` modelo ao usar o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando) com a **`Hosted`** opção selecionada ( `-ho|--hosted` ao usar o `dotnet new` comando).

Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.

Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Implantação autônoma

Uma *implantação autônoma* serve o Blazor WebAssembly aplicativo como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes. Qualquer servidor de arquivos estático é capaz de atender ao Blazor aplicativo.

Os ativos de implantação autônomo são publicados na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta.

### <a name="azure-app-service"></a>Serviço de Aplicativo do Azure

Blazor WebAssemblyos aplicativos podem ser implantados em serviços Azure App no Windows, que hospedam o aplicativo no [IIS](#iis).

Blazor WebAssemblyAtualmente, não há suporte para a implantação de um aplicativo autônomo no serviço Azure app para Linux. Uma imagem do servidor Linux para hospedar o aplicativo não está disponível no momento. O trabalho está em andamento para habilitar esse cenário.

### <a name="iis"></a>IIS

O IIS é um servidor de arquivos estático com capacidade para Blazor aplicativos. Para configurar o IIS para hospedar Blazor , consulte [criar um site estático no IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Os ativos publicados são criados na `/bin/Release/{TARGET FRAMEWORK}/publish` pasta. Hospede o conteúdo da `publish` pasta no servidor Web ou no serviço de hospedagem.

#### <a name="webconfig"></a>web.config

Quando um Blazor projeto é publicado, um `web.config` arquivo é criado com a seguinte configuração do IIS:

* Os tipos MIME são definidos para as seguintes extensões de arquivo:
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* A compactação HTTP está habilitada para os seguintes tipos MIME:
  * `application/octet-stream`
  * `application/wasm`
* As regras do Módulo de Reescrita de URL são estabelecidas:
  * Serve o subdiretório onde residem os ativos estáticos do aplicativo ( `wwwroot/{PATH REQUESTED}` ).
  * Crie o roteamento de fallback de SPA para que as solicitações de ativos que não sejam de arquivo sejam redirecionadas para o documento padrão do aplicativo em sua pasta de ativos estáticos ( `wwwroot/index.html` ).
  
#### <a name="use-a-custom-webconfig"></a>Usar um web.config personalizado

Para usar um `web.config` arquivo personalizado, coloque o `web.config` arquivo personalizado na raiz da pasta do projeto e publique o projeto.

#### <a name="install-the-url-rewrite-module"></a>Instalação do Módulo de Regeneração de URL

O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs. Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS). O módulo precisa ser baixado do site do IIS. Use o Web Platform Installer para instalar o módulo:

1. Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI. Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.
1. Copie o instalador para o servidor. Execute o instalador. Selecione o botão **Instalar** e aceite os termos de licença. Uma reinicialização do servidor não será necessária após a conclusão da instalação.

#### <a name="configure-the-website"></a>Configuração do site

Defina o **Caminho físico** do site como a pasta do aplicativo. A pasta contém:

* O `web.config` arquivo que o IIS usa para configurar o site, incluindo as regras de redirecionamento necessárias e os tipos de conteúdo do arquivo.
* A pasta de ativos estática do aplicativo.

#### <a name="host-as-an-iis-sub-app"></a>Hospedar como um subaplicativo do IIS

Se um aplicativo autônomo for hospedado como um subaplicativo do IIS, execute um dos seguintes procedimentos:

* Desabilite o manipulador do módulo ASP.NET Core herdado.

  Remova o manipulador no Blazor arquivo publicado do aplicativo `web.config` adicionando uma `<handlers>` seção ao arquivo:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Desabilite a herança da seção do aplicativo raiz (pai) `<system.webServer>` usando um `<location>` elemento com `inheritInChildApplications` definido como `false` :

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

A remoção do manipulador ou a desabilitação da herança é executada além da [configuração do caminho base do aplicativo](xref:blazor/host-and-deploy/index#app-base-path). Defina o caminho base do aplicativo no arquivo do aplicativo `index.html` como o alias do IIS usado ao configurar o subaplicativo no IIS.

#### <a name="brotli-and-gzip-compression"></a>Compactação Brotli e gzip

O IIS pode ser configurado via `web.config` para servir ativos compactados Brotli ou gzip Blazor . Para obter um exemplo de configuração, consulte [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .

#### <a name="troubleshooting"></a>Solução de problemas

Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado. Quando o módulo não está instalado, o `web.config` arquivo não pode ser analisado pelo IIS. Isso impede que o Gerenciador do IIS carregue a configuração do site e o site de serviços de Blazor arquivos estáticos.

Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Armazenamento do Azure

A hospedagem de arquivos estáticos de [armazenamento do Azure](/azure/storage/) permite Hospedagem de aplicativos sem servidor Blazor . Nomes de domínio personalizados, CDN (Rede de Distribuição de Conteúdo) do Azure e HTTPS são compatíveis.

Quando o serviço de blob está habilitado para hospedagem de site estático em uma conta de armazenamento:

* Defina o **Nome do documento de índice** como `index.html`.
* Defina o **Caminho do documento de erro** como `index.html`. Razoros componentes e outros pontos de extremidade que não são de arquivo não residem em caminhos físicos no conteúdo estático armazenado pelo serviço BLOB. Quando uma solicitação para um desses recursos é recebida e o Blazor roteador deve lidar, o erro *404-não encontrado* gerado pelo serviço blob roteia a solicitação para o caminho do **documento de erro**. O `index.html` blob é retornado e o Blazor roteador carrega e processa o caminho.

Se os arquivos não forem carregados no tempo de execução devido a tipos MIME inadequados nos cabeçalhos dos arquivos `Content-Type` , execute uma das seguintes ações:

* Configure suas ferramentas para definir os tipos MIME corretos ( `Content-Type` cabeçalhos) quando os arquivos são implantados.
* Altere os tipos MIME ( `Content-Type` cabeçalhos) para os arquivos depois que o aplicativo for implantado.

  Em Gerenciador de Armazenamento (portal do Azure) para cada arquivo:
  
  1. Clique com o botão direito do mouse no arquivo e selecione **Propriedades**.
  1. Defina o **ContentType** e selecione o botão **salvar** .

Para mais informações, confira [Hospedagem de site estático no Armazenamento do Azure](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

O arquivo a seguir `nginx.conf` é simplificado para mostrar como configurar o Nginx para enviar o `index.html` arquivo sempre que ele não encontrar um arquivo correspondente no disco.

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Ao definir o [limite de taxa de intermitência Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) com [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , os Blazor WebAssembly aplicativos podem exigir um grande `burst` valor de parâmetro para acomodar o número relativamente grande de solicitações feitas por um aplicativo. Inicialmente, defina o valor para pelo menos 60:

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

Aumente o valor se as ferramentas de desenvolvedor do navegador ou uma ferramenta de tráfego de rede indicar que as solicitações estão recebendo um código de status de *503-Serviço indisponível* .

Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).

### <a name="nginx-in-docker"></a>Nginx no Docker

Para hospedar Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem Nginx baseada em Alpine. Atualize o Dockerfile para copiar o `nginx.config` arquivo para o contêiner.

Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Para implantar um Blazor WebAssembly aplicativo no CentOS 7 ou posterior:

1. Crie o arquivo de configuração do Apache. O exemplo a seguir é um arquivo de configuração simplificado ( `blazorapp.config` ):

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

1. Coloque o arquivo de configuração do Apache no `/etc/httpd/conf.d/` diretório, que é o diretório de configuração padrão do Apache no CentOS 7.

1. Coloque os arquivos do aplicativo no `/var/www/blazorapp` diretório (o local especificado para `DocumentRoot` o no arquivo de configuração).

1. Reinicie o serviço apache.

Para obter mais informações, consulte [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .

### <a name="github-pages"></a>GitHub Pages

Para tratar as regravações de URL, adicione um `404.html` arquivo com um script que manipula o redirecionamento da solicitação para a `index.html` página. Para obter uma implementação de exemplo fornecida pela comunidade, confira [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Aplicativos de página única das Páginas do GitHub) ([rafrex/spa-github-pages no GitHub](https://github.com/rafrex/spa-github-pages#readme)). Um exemplo usando a abordagem da comunidade pode ser visto em [blazor-demo/blazor-demo.github.io no GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site dinâmico](https://blazor-demo.github.io/)).

Ao usar um site do projeto em vez de um site da organização, adicione ou atualize a `<base>` marca no `index.html` . Defina o valor de atributo `href` com o nome do repositório GitHub com uma barra à direita (por exemplo, `my-repository/`).

## <a name="host-configuration-values"></a>Valores de configuração do host

os [ Blazor WebAssembly aplicativos](xref:blazor/hosting-models#blazor-webassembly) podem aceitar os seguintes valores de configuração de host como argumentos de linha de comando em tempo de execução no ambiente de desenvolvimento.

### <a name="content-root"></a>Raiz do conteúdo

O `--contentroot` argumento define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo ([raiz de conteúdo](xref:fundamentals/index#content-root)). Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** . Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**. Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Caminho base

O `--pathbase` argumento define o caminho base do aplicativo para um aplicativo executado localmente com um caminho de URL relativo não raiz (a `<base>` marca `href` é definida para um caminho diferente `/` de para preparo e produção). Nos exemplos a seguir, `/relative-URL-path` é o caminho base do aplicativo. Para obter mais informações, consulte [caminho base do aplicativo](xref:blazor/host-and-deploy/index#app-base-path).

> [!IMPORTANT]
> Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`. Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** . Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**. Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URLs

O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** . Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**. Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Configurar o vinculador

Blazorexecuta a vinculação de IL (linguagem intermediária) em cada Build de versão para remover o IL desnecessário dos assemblies de saída. Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-linker>.

## <a name="custom-boot-resource-loading"></a>Carregamento de recurso de inicialização personalizada

Um Blazor WebAssembly aplicativo pode ser inicializado com a `loadBootResource` função para substituir o mecanismo de carregamento do recurso de inicialização interno. Use `loadBootResource` para os seguintes cenários:

* Permitir que os usuários carreguem recursos estáticos, como dados de fuso horário ou `dotnet.wasm` de uma CDN.
* Carregue assemblies compactados usando uma solicitação HTTP e descompacte-os no cliente para hosts que não dão suporte à busca de conteúdo compactado do servidor.
* Recursos de alias para um nome diferente redirecionando cada `fetch` solicitação para um novo nome.

`loadBootResource`os parâmetros aparecem na tabela a seguir.

| Parâmetro    | Descrição |
| ------------ | ----------- |
| `type`       | Tipo do recurso. Tipos permissiváveis: `assembly` ,, `pdb` , `dotnetjs` `dotnetwasm` ,`timezonedata` |
| `name`       | O nome do recurso. |
| `defaultUri` | O URI relativo ou absoluto do recurso. |
| `integrity`  | A cadeia de caracteres de integridade que representa o conteúdo esperado na resposta. |

`loadBootResource`retorna qualquer um dos seguintes para substituir o processo de carregamento:

* Cadeia de caracteres de URI. No exemplo a seguir ( `wwwroot/index.html` ), os seguintes arquivos são atendidos de uma CDN em `https://my-awesome-cdn.com/` :

  * `dotnet.*.js`
  * `dotnet.wasm`
  * Dados de fuso horário

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. Passe o `integrity` parâmetro em um cabeçalho para manter o comportamento de verificação de integridade padrão.

  O exemplo a seguir ( `wwwroot/index.html` ) adiciona um cabeçalho HTTP personalizado às solicitações de saída e passa o `integrity` parâmetro para a `fetch` chamada:
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`, o que resulta no comportamento de carregamento padrão.

As fontes externas devem retornar os cabeçalhos CORS necessários para que os navegadores permitam o carregamento de recursos entre origens. O CDNs geralmente fornece os cabeçalhos necessários por padrão.

Você só precisa especificar tipos para comportamentos personalizados. Tipos não especificados para `loadBootResource` são carregados pela estrutura de acordo com seus comportamentos de carregamento padrão.

## <a name="change-the-filename-extension-of-dll-files"></a>Alterar a extensão do nome de arquivo de arquivos DLL

Caso você tenha a necessidade de alterar as extensões de nome de arquivo dos arquivos publicados do aplicativo `.dll` , siga as orientações nesta seção.

Depois de publicar o aplicativo, use um script de shell ou o pipeline de compilação DevOps para renomear `.dll` arquivos para usar uma extensão de arquivo diferente. Direcione os `.dll` arquivos no `wwwroot` diretório da saída publicada do aplicativo (por exemplo, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).

Nos exemplos a seguir, `.dll` os arquivos são renomeados para usar a `.bin` extensão de arquivo.

No Windows:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

No Linux ou macOS:

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
Para usar uma extensão de arquivo diferente da `.bin` , substitua `.bin` os comandos anteriores.

Para resolver os arquivos compactados `blazor.boot.json.gz` `blazor.boot.json.br` , adote uma das seguintes abordagens:

* Remova os arquivos compactados `blazor.boot.json.gz` `blazor.boot.json.br` . A compactação está desabilitada com essa abordagem.
* Recompacte o `blazor.boot.json` arquivo atualizado.

As diretrizes anteriores também se aplicam quando os ativos de trabalho do serviço estão em uso. Remova ou recompacte `wwwroot/service-worker-assets.js.br` e `wwwroot/service-worker-assets.js.gz` . Caso contrário, as verificações de integridade de arquivo falharão no navegador.

O exemplo de Windows a seguir usa um script do PowerShell colocado na raiz do projeto.

`ChangeDLLExtensions.ps1:`:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

No arquivo de projeto, o script é executado após a publicação do aplicativo:

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

Para fornecer comentários, visite [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).
 
