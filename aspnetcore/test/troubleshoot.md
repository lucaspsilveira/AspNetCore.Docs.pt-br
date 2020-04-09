---
title: Solução de problemas e depuração ASP.NET projetos core
author: Rick-Anderson
description: Compreenda e solucione problemas de avisos e erros com projetos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511503"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Solução de problemas e depuração ASP.NET projetos core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Os links a seguir fornecem orientação para solução de problemas:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Conferência NDC (Londres, 2018): Questões de diagnóstico em ASP.NET principais aplicações](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET Blog: Solução de problemas ASP.NET problemas de desempenho principal](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>Avisos .NET Core SDK

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>As versões de 32 bits e 64 bits do .NET Core SDK estão instaladas

Na caixa de diálogo **Novo Projeto** para ASP.NET Core, você pode ver o seguinte aviso:

> Ambas as versões de 32 bits e 64 bits do .NET Core SDK estão instaladas. Somente modelos das versões de 64\\bits\\instaladas\\em\\'C: Arquivos de programa dotnet sdk ' são exibidos.

Este aviso aparece quando as versões de 32 bits (x86) e 64 bits (x64) do [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) são instaladas. As razões comuns pelas quais ambas as versões podem ser instaladas incluem:

* Você originalmente baixou o instalador .NET Core SDK usando uma máquina de 32 bits, mas depois copiou-o e instalou-o em uma máquina de 64 bits.
* O SDK .NET Core de 32 bits foi instalado por outro aplicativo.
* A versão errada foi baixada e instalada.

Desinstale o SDK do Núcleo .NET de 32 bits para evitar esse aviso. Desinstale a partir de programas e recursos do **painel** > de > controle**Desinstale ou altere um programa**.**Programs and Features** Se você entender por que o aviso ocorre e suas implicações, você pode ignorar o aviso.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>O .NET Core SDK está instalado em vários locais

Na caixa de diálogo **Novo Projeto** para ASP.NET Core, você pode ver o seguinte aviso:

> O .NET Core SDK está instalado em vários locais. Somente modelos dos SDKs instalados\\em\\'C:\\Arquivos\\do programa dotnet sdk ' são exibidos.

Você vê esta mensagem quando você tem pelo menos uma instalação do .NET Core SDK em um diretório fora de *C:\\\\Arquivos do programa dotnet\\sdk\\*. Normalmente isso acontece quando o .NET Core SDK foi implantado em uma máquina usando cópia/colar em vez do instalador MSI.

Desinstale todos os SDKs do Núcleo .NET de 32 bits e tempos de execução para evitar esse aviso. Desinstale a partir de programas e recursos do **painel** > de > controle**Desinstale ou altere um programa**.**Programs and Features** Se você entender por que o aviso ocorre e suas implicações, você pode ignorar o aviso.

### <a name="no-net-core-sdks-were-detected"></a>Não foram detectados SDKs do núcleo .NET

* Na caixa de diálogo Visual Studio **New Project** para ASP.NET Core, você pode ver o seguinte aviso:

  > Não foram detectados SDKs de núcleo .NET, `PATH`certifique-se de que estão incluídos na variável ambiente .

* Ao executar `dotnet` um comando, o aviso aparece como:

  > Não foi possível encontrar nenhum SDK dotnet instalado.

Esses avisos aparecem quando `PATH` a variável ambiente não aponta para nenhum SDK do Núcleo .NET na máquina. Para resolver esse problema:

* Instale o SDK do .NET Core. Obtenha o instalador mais recente a partir de [.NET Downloads](https://dotnet.microsoft.com/download).
* Verifique se `PATH` a variável ambiente aponta para o local`C:\Program Files\dotnet\` onde o SDK está `C:\Program Files (x86)\dotnet\` instalado ( para 64 bits/x64 ou para 32 bits/x86). O instalador SDK `PATH`normalmente define o . Instale sempre os mesmos SDKs de bitness e tempos de execução na mesma máquina.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>Faltando SDK depois de instalar o Pacote de hospedagem .NET Core

A instalação do .NET Core `PATH` Hosting Bundle modifica o tempo de [execução](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) do .NET Core para apontar para a`C:\Program Files (x86)\dotnet\`versão de 32 bits (x86) do .NET Core ( ). Isso pode resultar em SDKs ausentes quando o comando .86 do .NET Core `dotnet` de 32 bits é usado ( Não foram[detectados SDKs de núcleo .NET](#no-net-core-sdks-were-detected)). Para resolver este `C:\Program Files\dotnet\` problema, mova-se para uma posição antes `C:\Program Files (x86)\dotnet\` no `PATH`.

## <a name="obtain-data-from-an-app"></a>Obter dados de um aplicativo

Se um aplicativo é capaz de responder às solicitações, você pode obter os seguintes dados do aplicativo usando middleware:

* Método &ndash; de solicitação, esquema, host, base de caminho, caminho, seqüência de consulta, cabeçalhos
* Conexão &ndash; Endereço IP remoto, porta remota, endereço IP local, porta local, certificado do cliente
* Nome &ndash; de identidade, nome de exibição
* Definições de configuração
* Variáveis de ambiente

Coloque o seguinte código [de middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) no início do `Startup.Configure` pipeline de processamento de solicitação do método. O ambiente é verificado antes que o middleware seja executado para garantir que o código seja executado apenas no ambiente Desenvolvimento.

Para obter o ambiente, use qualquer uma das seguintes abordagens:

* Injete-o `IHostingEnvironment` no `Startup.Configure` método e verifique o ambiente com a variável local. O seguinte código de amostra demonstra essa abordagem.

* Atribuir o ambiente a uma `Startup` propriedade da classe. Verifique o ambiente usando a `if (Environment.IsDevelopment())`propriedade (por exemplo, ).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a>Depurar aplicativos ASP.NET Core

Os links a seguir fornecem informações sobre depuração ASP.NET aplicativos Core.

* [Depuração do ASP Core no Linux](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Depuração do Núcleo .NET no Unix sobre SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Início Rápido: depurar o ASP.NET com o depurador do Visual Studio](/visualstudio/debugger/quickstart-debug-aspnet)
* Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) para obter mais informações de depuração.
