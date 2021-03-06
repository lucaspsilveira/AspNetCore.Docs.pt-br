---
title: Novidades do ASP.NET Core 2.0
author: rick-anderson
description: Saiba mais sobre os novos recursos no ASP.NET Core 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-2.0
ms.openlocfilehash: 97d8606ba6bef0bb8bf23d1553c7cb5681fbdbd8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404360"
---
# <a name="whats-new-in-aspnet-core-20"></a>Novidades do ASP.NET Core 2.0

Este artigo destaca as alterações mais significativas no ASP.NET Core 2.0, com links para a documentação relevante.

## <a name="razor-pages"></a>RazorPages

RazorAs páginas são um novo recurso do ASP.NET Core MVC que torna os cenários voltados para a página de codificação mais fáceis e produtivos.

Para obter mais informações, consulte a introdução e o tutorial:

* [Introdução às Razor páginas](xref:razor-pages/index)
* [Introdução às Razor páginas](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>Metapacote do ASP.NET Core

Um novo metapacote do ASP.NET Core inclui todos os pacotes feitos e com suporte pelas equipes do ASP.NET Core e do Entity Framework Core, juntamente com as respectivas dependências internas e de terceiros. Você não precisa mais escolher recursos individuais do ASP.NET Core por pacote. Todos os recursos estão incluídos no pacote [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All). Os modelos padrão usam este pacote.

Para obter mais informações, consulte [Metapacote do Microsoft.AspNetCore.All para ASP.NET Core 2.0](xref:fundamentals/metapackage).

## <a name="runtime-store"></a>Repositório de runtime

Aplicativos que usam o metapacote `Microsoft.AspNetCore.All` aproveitam automaticamente o novo repositório de runtime do .NET Core. O repositório contém todos os ativos de runtime necessários para executar aplicativos ASP.NET Core 2.0. Quando você usa o metapacote `Microsoft.AspNetCore.All`, nenhum ativo dos pacotes NuGet do ASP.NET Core referenciados são implantados com o aplicativo porque eles já estão no sistema de destino. Os ativos no repositório de runtime também são pré-compilados para melhorar o tempo de inicialização do aplicativo.

Para obter mais informações, consulte [Repositório de runtime](/dotnet/core/deploying/runtime-store)

## <a name="net-standard-20"></a>.NET Standard 2.0

Os pacotes do ASP.NET Core 2.0 são direcionados ao .NET Standard 2.0. Os pacotes podem ser referenciados por outras bibliotecas do .NET Standard 2.0 e podem ser executados em implementações em conformidade com o .NET Standard 2.0, incluindo o .NET Core 2.0 e o .NET Framework 4.6.1. 

O metapacote `Microsoft.AspNetCore.All` aborda apenas o .Net Core 2.0 porque ele foi projetado para ser utilizado com o repositório de runtime do .Net Core 2.0.

## <a name="configuration-update"></a>Atualização da configuração

Uma instância de `IConfiguration` é adicionada ao contêiner de serviços por padrão no ASP.NET Core 2.0. `IConfiguration` no contêiner de serviços torna mais fácil para aplicativos recuperarem valores de configuração do contêiner.

Para obter informações sobre o status da documentação planejada, consulte o [problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3387).

## <a name="logging-update"></a>Atualização de registro em log

No ASP.NET Core 2.0, o log será incorporado no sistema de DI (injeção de dependência) por padrão. Você adiciona provedores e configura a filtragem no arquivo *Program.cs* em vez de usar o arquivo *Startup.cs*. E o `ILoggerFactory` padrão dá suporte à filtragem de forma que lhe permite usar uma abordagem flexível para filtragem entre provedores e filtragem específica do provedor.

Para obter mais informações, consulte [introdução ao registro em log](xref:fundamentals/logging/index).

## <a name="authentication-update"></a>Atualização de autenticação

Um novo modelo de autenticação torna mais fácil configurar a autenticação para um aplicativo usando a DI.

Novos modelos estão disponíveis para configurar a autenticação para aplicativos Web e APIs Web usando o [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).

Para obter informações sobre o status da documentação planejada, consulte o [problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3054).

## <a name="identity-update"></a>atualização de Identity

Tornamos mais fácil criar APIs Web seguras usando o Identity no ASP.NET Core 2,0. Você pode adquirir tokens de acesso para acessar suas APIs Web usando a [MSAL (Biblioteca de Autenticação da Microsoft)](https://www.nuget.org/packages/Microsoft.Identity.Client).

Para obter mais informações sobre alterações de autenticação no 2.0, consulte os seguintes recursos:

* [Confirmação de conta e de recuperação de senha no ASP.NET Core](xref:security/authentication/accconfirm)
* [Habilitar a geração de código QR para aplicativos de autenticador no ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Migrar autenticação e Identity para ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>Modelos do SPA

Modelos de projeto de SPA (aplicativo de página único) para Angular, Aurelia, Knockout.js, React.js e React.js com Redux estão disponíveis. O modelo Angular foi atualizado para Angular 4. Os modelos Angular e React estão disponíveis por padrão. Para saber como obter os outros modelos, confira [Criar um novo projeto de SPA](xref:client-side/spa-services#create-a-new-project). Para obter informações sobre como criar um SPA no ASP.NET Core, confira <xref:client-side/spa-services>.

## <a name="kestrel-improvements"></a>Melhorias do Kestrel

O servidor Web Kestrel tem novos recursos que o tornam mais adequado como um servidor voltado para a Internet. Uma série de opções de configuração de restrição de servidor serão adicionadas na nova propriedade `Limits` da classe `KestrelServerOptions`. Adicione limites para o seguinte:

* Número máximo de conexões de cliente
* Tamanho máximo do corpo da solicitação
* Taxa de dados mínima do corpo da solicitação

Para obter mais informações, consulte [Implementação do servidor web Kestrel no ASP.NET Core](xref:fundamentals/servers/kestrel).

## <a name="weblistener-renamed-to-httpsys"></a>WebListener renomeado para HTTP.sys

Os pacotes `Microsoft.AspNetCore.Server.WebListener` e `Microsoft.Net.Http.Server` foram mesclados em um novo pacote `Microsoft.AspNetCore.Server.HttpSys`. Os namespaces foram atualizados para corresponderem.

Para obter mais informações, consulte [Implementação do servidor Web HTTP.sys no ASP.NET Core](xref:fundamentals/servers/httpsys).

## <a name="enhanced-http-header-support"></a>Suporte aprimorado a cabeçalho HTTP

Ao usar o MVC para transmitir um `FileStreamResult` ou um `FileContentResult`, agora você tem a opção de definir uma `ETag` ou uma data `LastModified` no conteúdo que você transmitir. Você pode definir esses valores no conteúdo retornado com código semelhante ao seguinte:

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

O arquivo retornado para os visitantes tem os cabeçalhos HTTP apropriados para os `ETag` `LastModified` valores e.

Se um visitante do aplicativo solicitar o conteúdo com um cabeçalho de solicitação de intervalo, o ASP.NET Core reconhecerá a solicitação e lidará com o cabeçalho. Se parte do conteúdo solicitado puder ser entregue, o ASP.NET Core ignorará a parte em questão e retornará apenas o conjunto de bytes solicitado. Você não precisa gravar nenhum manipulador especial em seus métodos para adaptar ou manipular esse recurso; ele é manipulado automaticamente para você.

## <a name="hosting-startup-and-application-insights"></a>Inicialização de hospedagem e o Application Insights

Ambientes de hospedagem podem injetar dependências de pacote extras e executar código durante a inicialização do aplicativo, sem que o aplicativo precise tomar uma dependência explicitamente ou chamar algum método. Esse recurso pode ser usado para habilitar determinados ambientes para recursos de "esclarecimento" exclusivos para esse ambiente sem que o aplicativo precise saber antecipadamente. 

No ASP.NET Core 2.0, esse recurso é usado para habilitar o diagnóstico do Application Insights automaticamente durante a depuração no Visual Studio e (depois de optar por isto) quando em execução nos Serviços de Aplicativos do Azure. Como resultado, os modelos de projeto não adicionam mais código e pacotes do Application Insights por padrão.

Para obter informações sobre o status da documentação planejada, consulte o [problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3389).

## <a name="automatic-use-of-anti-forgery-tokens"></a>Uso automático de tokens antifalsificação

O ASP.NET Core sempre ajudou a fazer a codificação HTML do conteúdo por padrão, mas com a nova versão é necessário um passo adicional para ajudar a impedir ataques de XSRF (falsificação de solicitação entre sites). O ASP.NET Core agora emitirá tokens antifalsificação por padrão e os validará em ações de POST de formulário e em páginas sem configuração adicional.

Para obter mais informações, confira [Impedir ataques de XSRF/CSRF (solicitação intersite forjada)](xref:security/anti-request-forgery).

## <a name="automatic-precompilation"></a>Pré-compilação automática

Razora exibição de pré-compilação é habilitada durante a publicação por padrão, reduzindo o tamanho da saída de publicação e o tempo de inicialização do aplicativo.

Para obter mais informações, consulte [ Razor Exibir compilação e pré-compilação no ASP.NET Core](xref:mvc/views/view-compilation).

## <a name="razor-support-for-c-71"></a>Razorsuporte para C# 7,1

O Razor mecanismo de exibição foi atualizado para funcionar com o novo compilador Roslyn. Isso inclui suporte para recursos do C# 7.1 como expressões padrão, nomes de tupla inferidos e correspondência de padrões com genéricos. Para usar o C# 7.1 em seu projeto, adicione a seguinte propriedade no arquivo de projeto e, em seguida, recarregue a solução:

```xml
<LangVersion>latest</LangVersion>
```

Para obter informações sobre o status dos recursos do C# 7.1, consulte [o repositório GitHub do Roslyn](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).

## <a name="other-documentation-updates-for-20"></a>Outras atualizações de documentação para 2.0

* [Perfis de publicação do Visual Studio para a implantação do aplicativo ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles)
* [Gerenciamento de chaves](xref:security/data-protection/implementation/key-management)
* [Configurar a autenticação do Facebook](xref:security/authentication/facebook-logins)
* [Configurar a autenticação do Twitter](xref:security/authentication/twitter-logins)
* [Configurar a autenticação do Google](xref:security/authentication/google-logins)
* [Configurar a autenticação da conta da Microsoft](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a>Guia de migração

Para obter diretrizes sobre como migrar aplicativos ASP.NET Core 1.x para o ASP.NET Core 2.0, consulte os seguintes recursos:

* [Migrar do ASP.NET Core 1.x para o ASP.NET Core 2.0](xref:migration/1x-to-2x/index)
* [Migrar autenticação e Identity para ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>Informações adicionais

Para obter uma lista de alterações, consulte as [Notas de versão do ASP.NET Core 2.0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).

Para se conectar ao progresso e aos planos da equipe de desenvolvimento do ASP.NET Core, fique ligado no [ASP.NET Community Standup](https://live.asp.net/).
