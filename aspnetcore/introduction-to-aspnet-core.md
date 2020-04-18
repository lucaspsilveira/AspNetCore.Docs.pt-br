---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: Obtenha uma introdução ao ASP.NET Core, uma estrutura multiplataforma, de alto desempenho e código aberto para a construção de aplicativos modernos, habilitados para nuvem e conectados à Internet.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: c5a5a0ada996d88cb9252da25b5580fe0cf46f0b
ms.sourcegitcommit: 636efd1afc0a1e6fd4b12ae3a542917b356abb93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81615941"
---
# <a name="introduction-to-aspnet-core"></a>Introdução ao ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Shaun Luttin](https://twitter.com/dicshaunary)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core é uma estrutura multiplataforma, de alto desempenho e [de código aberto](https://github.com/dotnet/aspnetcore) para a construção de aplicativos modernos, habilitados para nuvem e conectados à Internet. Com o ASP.NET Core, você pode:

* Crie aplicativos e serviços web, aplicativos [de Internet das Coisas (IoT)](https://www.microsoft.com/internet-of-things/) e backends móveis.
* Usar suas ferramentas de desenvolvimento favoritas no Windows, macOS e Linux.
* Implantar na nuvem ou local.
* Executar em [.NET Core](/dotnet/core/introduction).

## <a name="why-choose-aspnet-core"></a>Por que escolher o ASP.NET Core?

Milhões de desenvolvedores usam ou usaram [ASP.NET 4.x](/aspnet/overview) para criar aplicativos web. ASP.NET Core é um redesenho de ASP.NET 4.x, incluindo mudanças arquitetônicas que resultam em uma estrutura mais enxuta e modular.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Compilar APIs Web e uma interface do usuário da Web usando o ASP.NET Core MVC

O ASP.NET Core MVC fornece recursos que ajudam você a compilar [APIs Web](xref:tutorials/first-web-api) e [aplicativos Web](xref:tutorials/razor-pages/index):

* O [padrão MVC (Model-View-Controller)](xref:mvc/overview) ajuda a tornar as APIs Web e os aplicativos Web testáveis.
* [Razor Pages](xref:razor-pages/index) é um modelo de programação baseado em página que torna a construção da interface do iu web mais fácil e mais produtiva.
* A [marcação Razor](xref:mvc/views/razor) fornece uma sintaxe produtiva para [Páginas Razor](xref:razor-pages/index) e as [Exibições do MVC](xref:mvc/views/overview).
* Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.
* O suporte interno para [vários formatos de dados e negociação de conteúdo](xref:web-api/advanced/formatting) permite que as APIs Web alcancem uma ampla gama de clientes, incluindo navegadores e dispositivos móveis.
* O [model binding](xref:mvc/models/model-binding) mapeia automaticamente os dados de solicitações HTTP para os parâmetros de método de ação.
* A [Validação de Modelos](xref:mvc/models/validation) executa automaticamente a validação no lado do cliente e do servidor.

## <a name="client-side-development"></a>Desenvolvimento do lado do cliente

O ASP.NET Core integra-se perfeitamente com estruturas conhecidas do lado do cliente e bibliotecas, incluindo [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) e [Bootstrap](https://getbootstrap.com/). Para obter mais informações, confira <xref:blazor/index> e os tópicos relacionados em *Desenvolvimento do lado do cliente*.

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>ASP.NET frameworks de alvo do Núcleo

ASP.NET Core 3.x e posteriores só podem atingir o .NET Core. Geralmente, ASP.NET Core é composto por bibliotecas [.NET Standard.](/dotnet/standard/net-standard) As bibliotecas gravadas com .NET Standard 2.0 podem ser executadas em qualquer [plataforma .NET que implemente o .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

Há várias vantagens em direcionar para o .NET Core, e essas vantagens aumentam com cada versão. Algumas vantagens do .NET Core em relação ao .NET Framework incluem:

* Multiplataforma. É executado no Windows, macOS e Linux.
* desempenho aprimorado
* [Versão lado a lado](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* Novas APIs
* Código-fonte aberto

## <a name="recommended-learning-path"></a>Caminho de aprendizado recomendado

Recomendamos a seguinte seqüência de tutoriais para uma introdução ao desenvolvimento de aplicativos ASP.NET Core:

1. Siga um tutorial para o tipo de aplicativo que você deseja desenvolver ou manter.

   |Tipo de aplicativo  |Cenário  |Tutorial  |
   |----------|----------|----------|
   |Aplicativo Web                   | Novo desenvolvimento de IU web do lado do servidor |[Introdução a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) |
   |Aplicativo Web                   | Manutenção de um aplicativo MVC |[Introdução ao MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Aplicativo Web                   | Desenvolvimento de IU web do lado do cliente |[Comece com Blazor](xref:tutorials/first-blazor-app) |
   |API Web                   | Serviços HTTP RESTful |[Crie uma API web](xref:tutorials/first-web-api)&dagger; |
   |Aplicativo de chamada de procedimento remoto | Serviços de primeiro contrato usando buffers de protocolo |[Introdução a um serviço gRPC](xref:tutorials/grpc/grpc-start) |
   |Aplicativo em tempo real             | Comunicação bidirecional entre servidores e clientes conectados |[Introdução ao SignalR](xref:tutorials/signalr) |

1. Siga um tutorial que mostra como fazer acesso básico aos dados.

   |Cenário  |Tutorial  |
   |----------|----------|
   |Novo desenvolvimento        |[Razor Pages com o Entity Framework Core](xref:data/ef-rp/intro) |
   |Manutenção de um aplicativo MVC |[MVC com o Entity Framework Core](xref:data/ef-mvc/intro) |

1. Leia uma visão geral dos [fundamentos](xref:fundamentals/index) ASP.NET Core que se aplicam a todos os tipos de aplicativos.

1. Navegue pela tabela de conteúdos para outros tópicos de interesse.

&dagger;Há também um [tutorial interativo de API web](/learn/modules/build-web-api-net-core). Não é necessária a instalação local de ferramentas de desenvolvimento. O código é executado em [um Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) em seu navegador, e o [cacho](https://curl.haxx.se/) é usado para testes.

## <a name="migrate-from-net-framework"></a>Migrar do .NET Framework

Para obter um guia de referência para migrar ASP.NET <xref:migration/proper-to-2x/index>aplicativos 4.x para ASP.NET Core, consulte .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core é uma estrutura multiplataforma, de alto desempenho e [de código aberto](https://github.com/dotnet/aspnetcore) para a construção de aplicativos modernos, habilitados para nuvem e conectados à Internet. Com o ASP.NET Core, você pode:

* Crie aplicativos e serviços web, aplicativos [de Internet das Coisas (IoT)](https://www.microsoft.com/internet-of-things/) e backends móveis.
* Usar suas ferramentas de desenvolvimento favoritas no Windows, macOS e Linux.
* Implantar na nuvem ou local.
* Executar no [.NET Core ou no .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="why-choose-aspnet-core"></a>Por que escolher o ASP.NET Core?

Milhões de desenvolvedores usam ou usaram [ASP.NET 4.x](/aspnet/overview) para criar aplicativos web. O ASP.NET Core é uma reformulação do ASP.NET 4.x, com alterações de arquitetura que resultam em uma estrutura mais enxuta e modular.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Compilar APIs Web e uma interface do usuário da Web usando o ASP.NET Core MVC

O ASP.NET Core MVC fornece recursos que ajudam você a compilar [APIs Web](xref:tutorials/first-web-api) e [aplicativos Web](xref:tutorials/razor-pages/index):

* O [padrão MVC (Model-View-Controller)](xref:mvc/overview) ajuda a tornar as APIs Web e os aplicativos Web testáveis.
* [Razor Pages](xref:razor-pages/index) é um modelo de programação baseado em página que torna a construção da interface do iu web mais fácil e mais produtiva.
* A [marcação Razor](xref:mvc/views/razor) fornece uma sintaxe produtiva para [Páginas Razor](xref:razor-pages/index) e as [Exibições do MVC](xref:mvc/views/overview).
* Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.
* O suporte interno para [vários formatos de dados e negociação de conteúdo](xref:web-api/advanced/formatting) permite que as APIs Web alcancem uma ampla gama de clientes, incluindo navegadores e dispositivos móveis.
* O [model binding](xref:mvc/models/model-binding) mapeia automaticamente os dados de solicitações HTTP para os parâmetros de método de ação.
* A [Validação de Modelos](xref:mvc/models/validation) executa automaticamente a validação no lado do cliente e do servidor.

## <a name="client-side-development"></a>Desenvolvimento do lado do cliente

O ASP.NET Core integra-se perfeitamente com estruturas conhecidas do lado do cliente e bibliotecas, incluindo [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) e [Bootstrap](https://getbootstrap.com/). Para obter mais informações, confira <xref:blazor/index> e os tópicos relacionados em *Desenvolvimento do lado do cliente*.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>ASP.NET Core direcionado para o .NET Framework

O ASP.NET Core 2.x pode ser direcionado para o .NET Core ou ao .NET Framework. Os aplicativos do ASP.NET Core direcionados ao .NET Framework não são multiplataforma,&mdash; são executados somente no Windows. Em geral, o ASP.NET Core 2.x é composto de bibliotecas do [.NET Standard](/dotnet/standard/net-standard). As bibliotecas gravadas com .NET Standard 2.0 podem ser executadas em qualquer [plataforma .NET que implemente o .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

O ASP.NET Core 2.x dá suporte para as versões do .NET Framework que implementam o .NET Standard 2.0:

* Recomenda-se a versão mais recente do .NET Framework.
* .NET Framework 4.6.1 e versões posteriores.

O ASP.NET Core 3.0 e posterior somente executará no .NET Core. Para obter mais detalhes sobre essa alteração, confira [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Uma primeira análise das alterações no ASP.NET Core 3.0).

Há várias vantagens em direcionar para o .NET Core, e essas vantagens aumentam com cada versão. Algumas vantagens do .NET Core em relação ao .NET Framework incluem:

* Multiplataforma. É executado no Windows, no Linux e no macOS.
* desempenho aprimorado
* [Versão lado a lado](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* Novas APIs
* Código-fonte aberto

Estamos trabalhando duro para diminuir a diferença de API entre o .NET Framework e o .NET Core. O [Pacote de Compatibilidade do Windows](/dotnet/core/porting/windows-compat-pack) disponibilizou milhares de APIs exclusivas do Windows no .NET Core. Essas APIs não estavam disponíveis no .NET Core 1.x.

## <a name="recommended-learning-path"></a>Caminho de aprendizado recomendado

Recomendamos a seguinte sequência de tutoriais e artigos para obter uma introdução ao desenvolvimento de aplicativos ASP.NET Core:

1. Siga um tutorial para o tipo de aplicativo que você deseja desenvolver ou manter.

   |Tipo de aplicativo  |Cenário  |Tutorial  |
   |----------|----------|----------|
   |Aplicativo Web                   | Para novo desenvolvimento        |[Introdução a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) |
   |Aplicativo Web                   | Para manter um aplicativo MVC |[Introdução ao MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |API Web                   |                            |[Crie uma API web](xref:tutorials/first-web-api)&dagger; |
   |Aplicativo em tempo real             |                            |[Introdução ao SignalR](xref:tutorials/signalr) |

1. Siga um tutorial que mostra como fazer acesso básico aos dados.

   |Cenário  |Tutorial  |
   |----------|----------|
   | Para novo desenvolvimento        |[Razor Pages com o Entity Framework Core](xref:data/ef-rp/intro) |
   | Para manter um aplicativo MVC |[MVC com o Entity Framework Core](xref:data/ef-mvc/intro) |

1. Leia uma visão geral dos [fundamentos](xref:fundamentals/index) ASP.NET Core que se aplicam a todos os tipos de aplicativos.

1. Pesquise no Sumário outros tópicos de interesse.

&dagger;Há também um [tutorial de API web que você segue inteiramente no navegador](/learn/modules/build-web-api-net-core), sem necessidade de instalação local do IDE. O código é executado em um [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) e [curl](https://curl.haxx.se/) é usado para teste.

## <a name="migrate-from-net-framework"></a>Migrar do .NET Framework

Para obter um guia de referência para <xref:migration/proper-to-2x/index>migrar ASP.NET aplicativos para ASP.NET Core, consulte .

::: moniker-end

## <a name="how-to-download-a-sample"></a>Como baixar uma amostra

Muitos dos artigos e tutoriais incluem links para exemplos de código.

1. [Baixe o arquivo zip do repositório ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).
1. Descompacte o arquivo *Docs-master.zip*.
1. Use a URL no link de exemplo para ajudá-lo a navegar até o diretório de exemplo.

### <a name="preprocessor-directives-in-sample-code"></a>Diretivas do pré-processador no código de exemplo

Para demonstrar vários cenários, `#define` `#if-#else/#elif-#endif` os aplicativos de exemplo usam as diretivas e pré-processadores para compilar e executar seletivamente diferentes seções do código de amostra. Para as amostras que fazem uso `#define` desta abordagem, defina a diretiva na parte superior dos arquivos C# para definir o símbolo associado ao cenário que você deseja executar. Algumas amostras requerem definir o símbolo na parte superior de vários arquivos para executar um cenário.

Por exemplo, a seguinte lista de símbolo `#define` indica que quatro cenários estão disponíveis (um cenário por símbolo). A configuração da amostra atual executa o cenário `TemplateCode`:

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

Para alterar a amostra que executará o cenário `ExpandDefault`, defina o símbolo `ExpandDefault` e deixe os símbolos restantes comentados de fora:

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

Para obter mais informações sobre como usar [diretivas de pré-processador C#](/dotnet/csharp/language-reference/preprocessor-directives/) para compilar seletivamente as seções de código, consulte [#define (Referência C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) e [#if (Referência C#) ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).

### <a name="regions-in-sample-code"></a>Regiões no código de exemplo

Alguns aplicativos de exemplo contêm seções de código cercadas por [diretivas #region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) e [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C#. O sistema de build de documentação injeta essas regiões nos tópicos renderizados da documentação.  

Os nomes das regiões geralmente contêm a palavra "snippet". O exemplo a seguir mostra uma região chamada `snippet_WebHostDefaults`:

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

O snippet de código C# precedente é referenciado no arquivo de markdown do tópico com a seguinte linha:

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

Você pode ignorar (ou remover) com segurança as `#region` diretivas e `#endregion` diretivas que cercam o código. Não altere o código dentro dessas diretivas se você planeja executar os cenários de amostra descritos no tópico. Fique à vontade para alterar o código ao experimentar com outros cenários.

Para obter mais informações, veja [Contribuir para a documentação do ASP.NET: snippets de código](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Conceitos básicos do ASP.NET Core](xref:fundamentals/index)
* O [Community Standup semanal do ASP.NET](https://live.asp.net/) aborda o progresso e os planos da equipe. Ele apresenta o novo software de terceiros e blogs.
