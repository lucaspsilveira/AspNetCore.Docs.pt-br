---
title: Migrar configuração para ASP.NET Core
author: ardalis
description: Saiba como migrar a configuração de um projeto MVC do ASP.NET para um projeto ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/configuration
ms.openlocfilehash: 9be321850b14847973877fb6a32217bd2dbb5171
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399810"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Migrar configuração para ASP.NET Core

Por [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)

No artigo anterior, começamos a [migrar um projeto ASP.NET MVC para ASP.NET Core MVC](xref:migration/mvc). Neste artigo, migramos a configuração.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Configuração da instalação

ASP.NET Core não usa mais os arquivos *global. asax* e *web.config* que as versões anteriores do ASP.net utilizaram. Nas versões anteriores do ASP.NET, a lógica de inicialização do aplicativo foi colocada em um `Application_StartUp` método dentro de *global. asax*. Posteriormente, no ASP.NET MVC, um arquivo *Startup.cs* foi incluído na raiz do projeto; e ele foi chamado quando o aplicativo foi iniciado. ASP.NET Core adotou essa abordagem completamente colocando toda a lógica de inicialização no arquivo *Startup.cs* .

O arquivo de *web.config* também foi substituído em ASP.NET Core. A configuração em si agora pode ser configurada, como parte do procedimento de inicialização do aplicativo descrito em *Startup.cs*. A configuração ainda pode utilizar arquivos XML, mas, normalmente ASP.NET Core projetos posicionarão valores de configuração em um arquivo formatado em JSON, como *appsettings.jsem*. O sistema de configuração de ASP.NET Core também pode acessar facilmente variáveis de ambiente, o que pode fornecer um [local mais seguro e robusto](xref:security/app-secrets) para valores específicos do ambiente. Isso é especialmente verdadeiro para segredos como cadeias de conexão e chaves de API que não devem ser verificados no controle do código-fonte. Consulte [configuração](xref:fundamentals/configuration/index) para saber mais sobre a configuração no ASP.NET Core.

Para este artigo, estamos começando com o projeto de ASP.NET Core migrado parcialmente do [artigo anterior](xref:migration/mvc). Para configurar a configuração, adicione o seguinte construtor e propriedade ao arquivo *Startup.cs* localizado na raiz do projeto:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Observe que neste ponto, o arquivo *Startup.cs* não será compilado, pois ainda precisamos adicionar a seguinte `using` instrução:

```csharp
using Microsoft.Extensions.Configuration;
```

Adicione um *appsettings.jsno* arquivo à raiz do projeto usando o modelo de item apropriado:

![Adicionar a AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Migrar definições de configuração de web.config

Nosso projeto MVC ASP.NET incluía a cadeia de conexão de banco de dados necessária no *web.config*, no `<connectionStrings>` elemento. Em nosso projeto de ASP.NET Core, vamos armazenar essas informações na *appsettings.jsno* arquivo. Abra o *appsettings.jsem*e observe que ele já inclui o seguinte:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

Na linha realçada descrita acima, altere o nome do banco de dados de **_CHANGE_ME** para o nome do seu banco de dados.

## <a name="summary"></a>Resumo

ASP.NET Core coloca toda a lógica de inicialização para o aplicativo em um único arquivo, no qual os serviços e as dependências necessários podem ser definidos e configurados. Ele substitui o arquivo de *web.config* por um recurso de configuração flexível que pode aproveitar uma variedade de formatos de arquivo, como JSON, bem como variáveis de ambiente.
