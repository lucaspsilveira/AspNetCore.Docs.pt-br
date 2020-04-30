---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: Obtenha uma introdução ao ASP.NET Core, uma estrutura de software livre de várias plataformas e de alto desempenho para a criação de aplicativos modernos conectados à Internet, em nuvem.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: d70998d0149a0cc7018946a93ab63b5e10b76b78
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558941"
---
# <a name="introduction-to-aspnet-core"></a>Introdução ao ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Shaun Luttin](https://twitter.com/dicshaunary)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core é uma estrutura de software livre de plataforma cruzada, de alto desempenho [para a criação](https://github.com/dotnet/aspnetcore) de aplicativos modernos conectados à Internet, em nuvem. Com o ASP.NET Core, você pode:

* Crie aplicativos Web e serviços, [Internet das coisas (IOT)](https://www.microsoft.com/internet-of-things/) e back-ends móveis.
* Usar suas ferramentas de desenvolvimento favoritas no Windows, macOS e Linux.
* Implantar na nuvem ou local.
* Executar no [.NET Core](/dotnet/core/introduction).

## <a name="why-choose-aspnet-core"></a>Por que escolher o ASP.NET Core?

Milhões de desenvolvedores usam ou usaram o [ASP.NET 4. x](/aspnet/overview) para criar aplicativos Web. ASP.NET Core é um redesign de ASP.NET 4. x, incluindo alterações arquitetônicas que resultam em uma estrutura mais Lean e mais modular.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Compilar APIs Web e uma interface do usuário da Web usando o ASP.NET Core MVC

O ASP.NET Core MVC fornece recursos que ajudam você a compilar [APIs Web](xref:tutorials/first-web-api) e [aplicativos Web](xref:tutorials/razor-pages/index):

* O [padrão MVC (Model-View-Controller)](xref:mvc/overview) ajuda a tornar as APIs Web e os aplicativos Web testáveis.
* [Razor Pages](xref:razor-pages/index) é um modelo de programação baseado em página que torna a criação da interface do usuário da Web mais fácil e mais produtiva.
* A [marcação Razor](xref:mvc/views/razor) fornece uma sintaxe produtiva para [Páginas Razor](xref:razor-pages/index) e as [Exibições do MVC](xref:mvc/views/overview).
* Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.
* O suporte interno para [vários formatos de dados e negociação de conteúdo](xref:web-api/advanced/formatting) permite que as APIs Web alcancem uma ampla gama de clientes, incluindo navegadores e dispositivos móveis.
* O [model binding](xref:mvc/models/model-binding) mapeia automaticamente os dados de solicitações HTTP para os parâmetros de método de ação.
* A [Validação de Modelos](xref:mvc/models/validation) executa automaticamente a validação no lado do cliente e do servidor.

## <a name="client-side-development"></a>Desenvolvimento do lado do cliente

O ASP.NET Core integra-se perfeitamente com estruturas conhecidas do lado do cliente e bibliotecas, incluindo [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) e [Bootstrap](https://getbootstrap.com/). Para obter mais informações, confira <xref:blazor/index> e os tópicos relacionados em *Desenvolvimento do lado do cliente*.

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>ASP.NET Core estruturas de destino

ASP.NET Core 3. x e posterior só podem ter como destino o .NET Core. Em geral, ASP.NET Core é composta de bibliotecas de [.net Standard](/dotnet/standard/net-standard) . As bibliotecas gravadas com .NET Standard 2.0 podem ser executadas em qualquer [plataforma .NET que implemente o .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

Há várias vantagens em direcionar para o .NET Core, e essas vantagens aumentam com cada versão. Algumas vantagens do .NET Core em relação ao .NET Framework incluem:

* Multiplataforma. É executado no Windows, no macOS e no Linux.
* desempenho aprimorado
* [Controle de versão lado a lado](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Novas APIs
* Código-fonte aberto

## <a name="recommended-learning-path"></a>Caminho de aprendizado recomendado

Recomendamos a seguinte sequência de tutoriais para uma introdução ao desenvolvimento de ASP.NET Core aplicativos:

1. Siga um tutorial para o tipo de aplicativo que você deseja desenvolver ou manter.

   |Tipo de aplicativo  |Cenário  |Tutorial  |
   |----------|----------|----------|
   |Aplicativo Web                   | Novo desenvolvimento de interface do usuário na Web do lado do servidor |[Introdução a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) |
   |Aplicativo Web                   | Mantendo um aplicativo MVC |[Introdução ao MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Aplicativo Web                   | Desenvolvimento de interface do usuário na Web do lado do cliente |[Introdução ao mais incrivelmente](xref:tutorials/first-blazor-app) |
   |API Web                   | Serviços HTTP RESTful |[Criar uma API Web](xref:tutorials/first-web-api)&dagger; |
   |Aplicativo de chamada de procedimento remoto | Serviços de primeiro contrato usando buffers de protocolo |[Introdução a um serviço gRPC](xref:tutorials/grpc/grpc-start) |
   |Aplicativo em tempo real             | Comunicação bidirecional entre servidores e clientes conectados |[Introdução ao SignalR](xref:tutorials/signalr) |

1. Siga um tutorial que mostra como fazer o acesso a dados básico.

   |Cenário  |Tutorial  |
   |----------|----------|
   |Novo desenvolvimento        |[Razor Pages com o Entity Framework Core](xref:data/ef-rp/intro) |
   |Mantendo um aplicativo MVC |[MVC com o Entity Framework Core](xref:data/ef-mvc/intro) |

1. Leia uma visão geral de ASP.NET Core [conceitos básicos](xref:fundamentals/index) que se aplicam a todos os tipos de aplicativos.

1. Procure outros tópicos de interesse no sumário.

&dagger;Há também um [tutorial de API Web interativa](/learn/modules/build-web-api-net-core). Nenhuma instalação local de ferramentas de desenvolvimento é necessária. O código é executado em um [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/) no navegador, e a [ondulação](https://curl.haxx.se/) é usada para teste.

## <a name="migrate-from-net-framework"></a>Migrar do .NET Framework

Para obter um guia de referência para migrar aplicativos ASP.NET 4. x para <xref:migration/proper-to-2x/index>ASP.NET Core, consulte.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core é uma estrutura de software livre de plataforma cruzada, de alto desempenho [para a criação](https://github.com/dotnet/aspnetcore) de aplicativos modernos conectados à Internet, em nuvem. Com o ASP.NET Core, você pode:

* Crie aplicativos Web e serviços, [Internet das coisas (IOT)](https://www.microsoft.com/internet-of-things/) e back-ends móveis.
* Usar suas ferramentas de desenvolvimento favoritas no Windows, macOS e Linux.
* Implantar na nuvem ou local.
* Executar no [.NET Core ou no .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="why-choose-aspnet-core"></a>Por que escolher o ASP.NET Core?

Milhões de desenvolvedores usam ou usaram o [ASP.NET 4. x](/aspnet/overview) para criar aplicativos Web. O ASP.NET Core é uma reformulação do ASP.NET 4.x, com alterações de arquitetura que resultam em uma estrutura mais enxuta e modular.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Compilar APIs Web e uma interface do usuário da Web usando o ASP.NET Core MVC

O ASP.NET Core MVC fornece recursos que ajudam você a compilar [APIs Web](xref:tutorials/first-web-api) e [aplicativos Web](xref:tutorials/razor-pages/index):

* O [padrão MVC (Model-View-Controller)](xref:mvc/overview) ajuda a tornar as APIs Web e os aplicativos Web testáveis.
* [Razor Pages](xref:razor-pages/index) é um modelo de programação baseado em página que torna a criação da interface do usuário da Web mais fácil e mais produtiva.
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

* .NET Framework versão mais recente é recomendada.
* .NET Framework 4.6.1 e versões posteriores.

O ASP.NET Core 3.0 e posterior somente executará no .NET Core. Para obter mais detalhes sobre essa alteração, confira [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Uma primeira análise das alterações no ASP.NET Core 3.0).

Há várias vantagens em direcionar para o .NET Core, e essas vantagens aumentam com cada versão. Algumas vantagens do .NET Core em relação ao .NET Framework incluem:

* Multiplataforma. É executado no Windows, no Linux e no macOS.
* desempenho aprimorado
* [Controle de versão lado a lado](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Novas APIs
* Código-fonte aberto

Para ajudar a fechar a lacuna da API do .NET Framework para o .NET Core, o [pacote de compatibilidade do Windows](/dotnet/core/porting/windows-compat-pack) tornou milhares de APIs somente do Windows disponíveis no .NET Core. Essas APIs não estavam disponíveis no .NET Core 1.x.

## <a name="recommended-learning-path"></a>Caminho de aprendizado recomendado

Recomendamos a seguinte sequência de tutoriais e artigos para obter uma introdução ao desenvolvimento de aplicativos ASP.NET Core:

1. Siga um tutorial para o tipo de aplicativo que você deseja desenvolver ou manter.

   |Tipo de aplicativo  |Cenário  |Tutorial  |
   |----------|----------|----------|
   |Aplicativo Web                   | Para novo desenvolvimento        |[Introdução a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) |
   |Aplicativo Web                   | Para manter um aplicativo MVC |[Introdução ao MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |API Web                   |                            |[Criar uma API Web](xref:tutorials/first-web-api)&dagger; |
   |Aplicativo em tempo real             |                            |[Introdução ao SignalR](xref:tutorials/signalr) |

1. Siga um tutorial que mostra como fazer o acesso a dados básico.

   |Cenário  |Tutorial  |
   |----------|----------|
   | Para novo desenvolvimento        |[Razor Pages com o Entity Framework Core](xref:data/ef-rp/intro) |
   | Para manter um aplicativo MVC |[MVC com o Entity Framework Core](xref:data/ef-mvc/intro) |

1. Leia uma visão geral de ASP.NET Core [conceitos básicos](xref:fundamentals/index) que se aplicam a todos os tipos de aplicativos.

1. Pesquise no Sumário outros tópicos de interesse.

&dagger;Há também um [tutorial da API Web que você segue inteiramente no navegador](/learn/modules/build-web-api-net-core), nenhuma instalação local do IDE é necessária. O código é executado em um [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) e [curl](https://curl.haxx.se/) é usado para teste.

## <a name="migrate-from-net-framework"></a>Migrar do .NET Framework

Para obter um guia de referência para migrar aplicativos ASP.NET para <xref:migration/proper-to-2x/index>ASP.NET Core, consulte.

::: moniker-end

## <a name="how-to-download-a-sample"></a>Como baixar uma amostra

Muitos dos artigos e tutoriais incluem links para exemplos de código.

1. [Baixe o arquivo zip do repositório ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).
1. Descompacte o arquivo *Docs-master.zip*.
1. Use a URL no link de exemplo para ajudá-lo a navegar até o diretório de exemplo.

### <a name="preprocessor-directives-in-sample-code"></a>Diretivas do pré-processador no código de exemplo

Para demonstrar vários cenários, aplicativos de exemplo usam `#define` as `#if-#else/#elif-#endif` diretivas de pré-processador e para compilar seletivamente e executar diferentes seções de código de exemplo. Para os exemplos que fazem uso dessa abordagem, defina a `#define` diretiva na parte superior dos arquivos C# para definir o símbolo associado ao cenário que você deseja executar. Alguns exemplos exigem a definição do símbolo na parte superior de vários arquivos para executar um cenário.

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

Alguns aplicativos de exemplo contêm seções de código circundadas por [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) e [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) diretivas C#. O sistema de build de documentação injeta essas regiões nos tópicos renderizados da documentação.  

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

Você pode ignorar (ou remover) com segurança as `#region` diretivas `#endregion` e que envolvem o código. Não altere o código nessas diretivas se você planeja executar os cenários de exemplo descritos no tópico. Fique à vontade para alterar o código ao experimentar com outros cenários.

Para obter mais informações, veja [Contribuir para a documentação do ASP.NET: snippets de código](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Conceitos básicos do ASP.NET Core](xref:fundamentals/index)
* O [Community Standup semanal do ASP.NET](https://live.asp.net/) aborda o progresso e os planos da equipe. Ele apresenta o novo software de terceiros e blogs.
