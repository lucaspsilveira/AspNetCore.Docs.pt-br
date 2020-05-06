---
title: Conceitos básicos do ASP.NET Core
author: rick-anderson
description: Aprenda os conceitos fundamentais para a criação de aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/index
ms.openlocfilehash: 0f0e97246b6e1381b85866bd831ee9b4b150650d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774321"
---
# <a name="aspnet-core-fundamentals"></a>Conceitos básicos do ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Este artigo fornece uma visão geral dos principais tópicos para entender como desenvolver ASP.NET Core aplicativos.

## <a name="the-startup-class"></a>A classe Startup

A classe `Startup` é o local em que:

* Os serviços exigidos pelo aplicativo são configurados.
* O pipeline de tratamento de solicitação do aplicativo é definido como uma série de componentes de middleware.

Aqui está um exemplo de classe `Startup`:

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Injeção de dependência (serviços)

ASP.NET Core inclui uma estrutura de injeção de dependência interna (DI) que torna os serviços configurados disponíveis em todo o aplicativo. Por exemplo, um componente de registro em log é um serviço.

Código para configurar (ou *registrar*) serviços é adicionado ao método `Startup.ConfigureServices`. Por exemplo: 

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

Normalmente, os serviços são resolvidos de DI usando injeção de construtor. Com a injeção de construtor, uma classe declara um parâmetro de construtor do tipo necessário ou de uma interface. A estrutura DI fornece uma instância desse serviço em tempo de execução.

O exemplo a seguir usa injeção de construtor para `RazorPagesMovieContext` resolver um de di:

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

Se o contêiner interno de inversão de controle (IoC) não atender a todas as necessidades de um aplicativo, um contêiner IoC de terceiros poderá ser usado em vez disso.

Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

O pipeline de tratamento de solicitação é composto como uma série de componentes de middleware. Cada componente executa operações em um `HttpContext` e invoca o próximo middleware no pipeline ou encerra a solicitação.

Por convenção, um componente de middleware é adicionado ao pipeline invocando um `Use...` método de extensão no `Startup.Configure` método. Por exemplo, para habilitar o processamento de arquivos estáticos, chame `UseStaticFiles`.

O exemplo a seguir configura um pipeline de tratamento de solicitação:

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

O ASP.NET Core inclui um conjunto avançado de middleware interno. Os componentes de middleware personalizados também podem ser escritos.

Para obter mais informações, consulte <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Na inicialização, um aplicativo ASP.NET Core cria um *host*. O host encapsula todos os recursos do aplicativo, como:

* Uma implementação do servidor HTTP
* Componentes de middleware
* Registrando em log
* Serviços de injeção de dependência (DI)
* Configuração

Há dois hosts diferentes: 

* Host Genérico .NET
* Host da Web do ASP.NET Core

O host genérico .NET é recomendado. O host Web do ASP.NET Core está disponível apenas para compatibilidade com versões anteriores.

O exemplo a seguir cria um host genérico .NET:

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

Os `CreateDefaultBuilder` métodos `ConfigureWebHostDefaults` e configuram um host com um conjunto de opções padrão, como:

* Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.
* Configuração de carregamento de *appsettings.json*, *appsettings.{EnvironmentName}.json*, de variáveis de ambiente, de argumentos de linha de comando e outras fontes de configuração.
* Envio da saída de log para os provedores de console e de depuração.

Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.

### <a name="non-web-scenarios"></a>Cenários não Web

O Host Genérico permite que outros tipos de aplicativos usem extensões de estruturas abrangentes como registro em log, DI (Injeção de Dependência), configuração e gerenciamento do tempo de vida dos aplicativos. Para obter mais informações, consulte <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Servidores

Um aplicativo ASP.NET Core usa uma implementação do servidor HTTP para ouvir solicitações HTTP. O servidor descobre solicitações ao aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um `HttpContext`.

# <a name="windows"></a>[Windows](#tab/windows)

O ASP.NET Core vem com as seguintes implementações de servidor:

* *Kestrel* é um servidor Web multiplataforma. O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/). No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.
* O *servidor http do IIS* é um servidor para Windows que usa o IIS. Com esse servidor, o aplicativo ASP.NET Core e o IIS são executados no mesmo processo.
* *HTTP.sys* é um servidor para Windows que não é usado com IIS.

# <a name="macos"></a>[macOS](#tab/macos)

O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*. No ASP.NET Core 2,0 ou posterior, o Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet. O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*. No ASP.NET Core 2,0 ou posterior, o Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet. O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

---

Para obter mais informações, consulte <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Configuração

O ASP.NET Core fornece uma estrutura de configuração que obtém as configurações como pares nome-valor de um conjunto ordenado de provedores de configuração. Os provedores de configuração internos estão disponíveis para uma variedade de fontes, como arquivos *. JSON* , arquivos *. xml* , variáveis de ambiente e argumentos de linha de comando. Escreva provedores de configuração personalizados para dar suporte a outras fontes.

Por [padrão](xref:fundamentals/configuration/index#default), os aplicativos ASP.NET Core são configurados para ler de *appSettings. JSON*, variáveis de ambiente, a linha de comando e muito mais. Quando a configuração do aplicativo é carregada, os valores das variáveis de ambiente substituem valores de *appSettings. JSON*.

A maneira preferida de ler valores de configuração relacionados é usar o [padrão de opções](xref:fundamentals/configuration/options). Para obter mais informações, consulte [associar dados de configuração hierárquica usando o padrão de opções](xref:fundamentals/configuration/index#optpat).

Para gerenciar dados de configuração confidenciais, como senhas, ASP.NET Core fornece o [Gerenciador de segredo](xref:security/app-secrets#secret-manager). Para segredos de produção, recomendamos o [Azure Key Vault](xref:security/key-vault-configuration).

Para obter mais informações, consulte <xref:fundamentals/configuration/index>.

## <a name="environments"></a>Ambientes

Ambientes de execução, `Development` `Staging`como, e `Production`, são uma noção de primeira classe no ASP.NET Core. Especifique o ambiente em que um aplicativo está sendo executado definindo `ASPNETCORE_ENVIRONMENT` a variável de ambiente. O ASP.NET Core lê a variável de ambiente na inicialização do aplicativo e armazena o valor em uma implementação `IWebHostEnvironment`. Essa implementação está disponível em qualquer lugar em um aplicativo por meio de injeção de dependência (DI).

O exemplo a seguir configura o aplicativo para fornecer informações de erro detalhadas durante a `Development` execução no ambiente:

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

Para obter mais informações, consulte <xref:fundamentals/environments>.

## <a name="logging"></a>Registrando em log

O ASP.NET Core dá suporte a uma API de registro em log que funciona com uma série de provedores de registro em log internos e de terceiros. Os provedores disponíveis incluem:

* Console
* Depurar
* Rastreamento de Eventos no Windows
* Log de eventos do Windows
* TraceSource
* Serviço de aplicativo do Azure
* Azure Application Insights

Para criar logs, resolva um <xref:Microsoft.Extensions.Logging.ILogger%601> serviço de injeção de dependência (di) e métodos de registro em <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>log de chamada, como. Por exemplo: 

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

Métodos de registro em `LogInformation` log, como suporte a qualquer número de campos. Esses campos são comumente usados para construir uma mensagem `string`, mas alguns provedores de log os enviam para um armazenamento de dados como campos separados. Esse recurso torna possível para provedores de log implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Para obter mais informações, consulte <xref:fundamentals/logging/index>.

## <a name="routing"></a>Roteamento

Um *rota* é um padrão de URL mapeado para um manipulador. O manipulador normalmente é um Razor Page, um método de ação em um controlador MVC ou um middleware. O roteamento do ASP.NET Core lhe dá controle sobre as URLs usadas pelo seu aplicativo.

Para obter mais informações, consulte <xref:fundamentals/routing>.

## <a name="error-handling"></a>Tratamento de erros

O ASP.NET Core tem recursos internos para tratamento de erros, como:

* Uma página de exceção do desenvolvedor
* Páginas de erro personalizadas
* Páginas de código de status estático
* Tratamento de exceção na inicialização

Para obter mais informações, consulte <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Fazer solicitações HTTP

Uma implementação de `IHttpClientFactory` está disponível para a criação de instâncias do `HttpClient`. O alocador:

* Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`. Por exemplo, registre e configure um cliente *GitHub* para acessar o github. Registre e configure um cliente padrão para outras finalidades.
* Dá suporte ao registro e ao encadeamento de vários manipuladores de delegação para criar um pipeline do middleware de solicitação saída. Esse padrão é semelhante ao pipeline de middleware de entrada de ASP.NET Core. O padrão fornece um mecanismo para gerenciar preocupações abrangentes para solicitações HTTP, incluindo cache, tratamento de erros, serialização e registro em log.
* Integra-se com a *Polly*, uma biblioteca de terceiros popular para tratamento de falhas transitórias.
* Gerencia o pooling e o tempo de vida `HttpClientHandler` de instâncias subjacentes para evitar problemas comuns de DNS `HttpClient` que ocorrem durante o gerenciamento manual de tempos de vida.
* Adiciona uma experiência de registro configurável <xref:Microsoft.Extensions.Logging.ILogger> por meio de todas as solicitações enviadas por clientes criados pela fábrica.

Para obter mais informações, consulte <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Raiz do conteúdo

A raiz do conteúdo é o caminho base para:

* O executável que hospeda o aplicativo (*. exe*).
* Assemblies compilados que compõem o aplicativo (*. dll*).
* Arquivos de conteúdo usados pelo aplicativo, como:
  * Arquivos Razor (*. cshtml*, *. Razor*)
  * Arquivos de configuração (*. JSON*, *. xml*)
  * Arquivos de dados (*. db*)
* A [raiz da Web](#web-root), normalmente a pasta *wwwroot* .

Durante o desenvolvimento, a raiz do conteúdo assume como padrão o diretório raiz do projeto. Esse diretório também é o caminho base para os arquivos de conteúdo do aplicativo e a [raiz da Web](#web-root). Especifique uma raiz de conteúdo diferente definindo seu caminho ao [compilar o host](#host). Para obter mais informações, veja [Raiz de conteúdo](xref:fundamentals/host/generic-host#contentroot).

## <a name="web-root"></a>Raiz da Web

A raiz da Web é o caminho base para arquivos de recursos públicos e estáticos, como:

* Folhas de estilo (*. css*)
* JavaScript (*. js*)
* Imagens (*. png*, *. jpg*)

Por padrão, os arquivos estáticos são servidos somente do diretório raiz da Web e de seus subdiretórios. O caminho raiz da Web tem como padrão *{Content root}/wwwroot*. Especifique uma raiz da Web diferente definindo seu caminho ao [compilar o host](#host). Para obter mais informações, confira [Diretório base](xref:fundamentals/host/generic-host#webroot).

Impedir a publicação de arquivos em *wwwroot* com o [ \<conteúdo> item de projeto](/visualstudio/msbuild/common-msbuild-project-items#content) no arquivo de projeto. O exemplo a seguir impede a publicação de conteúdo em *wwwroot/local* e em seus subdiretórios:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Nos arquivos Razor *. cshtml* , a barra til (`~/`) aponta para a raiz da Web. Um caminho que começa `~/` com é conhecido como um *caminho virtual*.

Para obter mais informações, consulte <xref:fundamentals/static-files>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este artigo é uma visão geral dos principais tópicos para entender como desenvolver aplicativos ASP.NET Core.

## <a name="the-startup-class"></a>A classe Startup

A classe `Startup` é o local em que:

* Os serviços exigidos pelo aplicativo são configurados.
* O pipeline de tratamento de solicitação é definido.

*Serviços* são componentes usados pelo aplicativo. Por exemplo, um componente de registro em log é um serviço. Código para configurar (ou *registrar*) serviços é adicionado ao método `Startup.ConfigureServices`.

O pipeline de tratamento de solicitação é composto como uma série de componentes de *middleware* . Por exemplo, um middleware pode manipular as solicitações para arquivos estáticos ou redirecionar solicitações HTTP para HTTPS. Cada middleware executa operações assíncronas em um `HttpContext` e invoca o próximo middleware no pipeline ou encerra a solicitação. O código para configurar a pipeline de tratamento de solicitação é adicionado ao método `Startup.Configure`.

Aqui está um exemplo de classe `Startup`:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Injeção de dependência (serviços)

O ASP.NET Core tem uma estrutura de DI (injeção de dependência) interna que torna serviços configurados disponíveis para classes do aplicativo. Uma maneira de obter uma instância de um serviço em uma classe criar um construtor com um parâmetro do tipo necessário. O parâmetro pode ser o tipo de serviço ou uma interface. O sistema de DI fornece o serviço em runtime.

Aqui está uma classe que usa DI para obter um objeto de contexto do Entity Framework Core. A linha realçada é um exemplo de injeção de construtor:

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

Embora a DI seja interna, ela foi projetada para permitir que você conecte um contêiner de IoC (inversão de controle) de terceiros, se preferir.

Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

O pipeline de tratamento de solicitação é composto como uma série de componentes de middleware. Cada componente executa operações assíncronas em um `HttpContext` e então invoca o próximo middleware no pipeline ou encerra a solicitação.

Por convenção, um componente de middleware é adicionado ao pipeline invocando seu método de extensão `Use...` no método `Startup.Configure`. Por exemplo, para habilitar o processamento de arquivos estáticos, chame `UseStaticFiles`.

O código realçado no exemplo a seguir configura o pipeline de tratamento de solicitação:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

O ASP.NET Core inclui um conjunto de middleware interno, e você pode escrever um middleware personalizado.

Para obter mais informações, consulte <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Um aplicativo ASP.NET Core cria um *host* na inicialização. O host é um objeto que encapsula todos os recursos do aplicativo, como:

* Uma implementação do servidor HTTP
* Componentes de middleware
* Registrando em log
* DI
* Configuração

O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.

Dois hosts estão disponíveis: o Host Web e o Host Genérico. No ASP.NET Core 2.x, o Host Genérico é somente para cenários não Web.

O código para criar um host está em `Program.Main`:

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

O método `CreateDefaultBuilder` configura um host com as opções usadas com frequência, como as seguintes:

* Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.
* Configuração de carregamento de *appsettings.json*, *appsettings.{EnvironmentName}.json*, de variáveis de ambiente, de argumentos de linha de comando e outras fontes de configuração.
* Envio da saída de log para os provedores de console e de depuração.

Para obter mais informações, consulte <xref:fundamentals/host/web-host>.

### <a name="non-web-scenarios"></a>Cenários não Web

O Host Genérico permite que outros tipos de aplicativos usem extensões de estruturas abrangentes como registro em log, DI (Injeção de Dependência), configuração e gerenciamento do tempo de vida dos aplicativos. Para obter mais informações, consulte <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Servidores

Um aplicativo ASP.NET Core usa uma implementação do servidor HTTP para ouvir solicitações HTTP. O servidor descobre solicitações ao aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um `HttpContext`.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

O ASP.NET Core vem com as seguintes implementações de servidor:

* *Kestrel* é um servidor Web multiplataforma. O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/). Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.
* O *Servidor HTTP de IIS* é um servidor do Windows que usa o IIS. Com esse servidor, o aplicativo ASP.NET Core e o IIS são executados no mesmo processo.
* *HTTP.sys* é um servidor para Windows que não é usado com IIS.

# <a name="macos"></a>[macOS](#tab/macos)

O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*. Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet. O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*. Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet. O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

O ASP.NET Core vem com as seguintes implementações de servidor:

* *Kestrel* é um servidor Web multiplataforma. O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/). Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.
* *HTTP.sys* é um servidor para Windows que não é usado com IIS.

# <a name="macos"></a>[macOS](#tab/macos)

O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*. Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet. O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*. Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet. O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Para obter mais informações, consulte <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Configuração

O ASP.NET Core fornece uma estrutura de configuração que obtém as configurações como pares nome-valor de um conjunto ordenado de provedores de configuração. Há provedores de configuração internos para uma variedade de fontes, como arquivos *.json*, arquivos *.xml*, variáveis de ambiente e argumentos de linha de comando. Você também pode escrever seus próprios provedores de configuração personalizados.

Por exemplo, você poderia especificar que a configuração é proveniente de *appsettings.json* e variáveis de ambiente. Então, quando o valor de *ConnectionString* for solicitado, a estrutura procura primeiro no arquivo *appsettings.json*. Se o valor for encontrado lá, mas também em uma variável de ambiente, o valor da variável de ambiente terá precedência.

Para gerenciar dados de configuração confidenciais como senhas, o ASP.NET Core fornece uma [ferramenta Secret Manager](xref:security/app-secrets). Para segredos de produção, recomendamos o [Azure Key Vault](xref:security/key-vault-configuration).

Para obter mais informações, consulte <xref:fundamentals/configuration/index>.

## <a name="options"></a>Opções

Sempre que possível, o ASP.NET Core segue o *padrão de opções* para armazenar e recuperar valores de configuração. O padrão de opções usa classes para representar grupos de configurações relacionadas.

Por exemplo, o código a seguir define as opções de WebSockets:

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

Para obter mais informações, consulte <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Ambientes

Ambientes de execução, como *Desenvolvimento*, *Preparo* e *Produção*, são uma noção de primeira classe no ASP.NET Core. Você pode especificar o ambiente em que um aplicativo está em execução definindo a variável de ambiente `ASPNETCORE_ENVIRONMENT`. O ASP.NET Core lê a variável de ambiente na inicialização do aplicativo e armazena o valor em uma implementação `IHostingEnvironment`. O objeto de ambiente está disponível em qualquer lugar no aplicativo por meio de DI.

O seguinte código de exemplo da classe `Startup` configura o aplicativo para fornecer informações de erro detalhadas somente quando ele é executado no desenvolvimento:

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

Para obter mais informações, consulte <xref:fundamentals/environments>.

## <a name="logging"></a>Registrando em log

O ASP.NET Core dá suporte a uma API de registro em log que funciona com uma série de provedores de registro em log internos e de terceiros. Provedores disponíveis incluem os seguintes:

* Console
* Depurar
* Rastreamento de Eventos no Windows
* Log de eventos do Windows
* TraceSource
* Serviço de aplicativo do Azure
* Azure Application Insights

Escreva logs de qualquer lugar no código do aplicativo obtendo um objeto `ILogger` da DI e chamando os métodos de log.

Aqui está o código de exemplo que usa um objeto `ILogger`, com injeção de construtor e chamadas de método de registro em log realçadas.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

A interface `ILogger` permite que você passe qualquer número de campos para o provedor de registro em log. Os campos são comumente usados para construir uma cadeia de caracteres de mensagem, mas o provedor também pode enviá-los como campos separados para um armazenamento de dados. Esse recurso torna possível para provedores de log implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Para obter mais informações, consulte <xref:fundamentals/logging/index>.

## <a name="routing"></a>Roteamento

Um *rota* é um padrão de URL mapeado para um manipulador. O manipulador é normalmente uma Razor página, um método de ação em um controlador MVC ou um middleware. O roteamento do ASP.NET Core lhe dá controle sobre as URLs usadas pelo seu aplicativo.

Para obter mais informações, consulte <xref:fundamentals/routing>.

## <a name="error-handling"></a>Tratamento de erros

O ASP.NET Core tem recursos internos para tratamento de erros, como:

* Uma página de exceção do desenvolvedor
* Páginas de erro personalizadas
* Páginas de código de status estático
* Tratamento de exceção na inicialização

Para obter mais informações, consulte <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Fazer solicitações HTTP

Uma implementação de `IHttpClientFactory` está disponível para a criação de instâncias do `HttpClient`. O alocador:

* Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`. Por exemplo, um cliente *GitHub* pode ser registrado e configurado para acessar o github. Um cliente padrão pode ser registrado para outras finalidades.
* Dá suporte ao registro e ao encadeamento de vários manipuladores de delegação para criar um pipeline do middleware de solicitação saída. Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core. O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.
* Integra-se com a *Polly*, uma biblioteca de terceiros popular para tratamento de falhas transitórias.
* Gerencia o pooling e o tempo de vida das instâncias de `HttpClientHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.
* Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.

Para obter mais informações, consulte <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Raiz do conteúdo

A raiz do conteúdo é o caminho base para:

* Executável que hospeda o aplicativo (*. exe*).
* Assemblies compilados que compõem o aplicativo (*. dll*).
* Arquivos de conteúdo que não são de código usados pelo aplicativo, como:
  * Razorarquivos (*. cshtml*, *. Razor*)
  * Arquivos de configuração (*. JSON*, *. xml*)
  * Arquivos de dados (*. db*)
* [Raiz da Web](#web-root), normalmente a pasta *wwwroot* publicada.

Durante o desenvolvimento:

* A raiz do conteúdo assume como padrão o diretório raiz do projeto.
* O diretório raiz do projeto é usado para criar:
  * Caminho para os arquivos de conteúdo sem código do aplicativo no diretório raiz do projeto.
  * [Raiz da Web](#web-root), normalmente a pasta *wwwroot* no diretório raiz do projeto.

Um caminho de raiz de conteúdo alternativo pode ser especificado ao [criar o host](#host). Para obter mais informações, consulte <xref:fundamentals/host/web-host#content-root>.

## <a name="web-root"></a>Raiz da Web

A raiz da Web é o caminho base para arquivos de recurso estáticos, não de código e públicos, como:

* Folhas de estilo (*. css*)
* JavaScript (*. js*)
* Imagens (*. png*, *. jpg*)

Os arquivos estáticos são servidos apenas por padrão no diretório raiz da Web (e subdiretórios).

O caminho raiz da Web tem como padrão *{Content root}/wwwroot*, mas uma raiz da Web diferente pode ser especificada ao [criar o host](#host). Para obter mais informações, confira [Diretório base](xref:fundamentals/host/web-host#web-root).

Impedir a publicação de arquivos em *wwwroot* com o [ \<conteúdo> item de projeto](/visualstudio/msbuild/common-msbuild-project-items#content) no arquivo de projeto. O exemplo a seguir impede a publicação de conteúdo no diretório *wwwroot/local* e nos subpastas:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Em Razor arquivos (*. cshtml*), a barra til (`~/`) aponta para a raiz da Web. Um caminho que começa `~/` com é conhecido como um *caminho virtual*.

Para obter mais informações, consulte <xref:fundamentals/static-files>.

::: moniker-end
