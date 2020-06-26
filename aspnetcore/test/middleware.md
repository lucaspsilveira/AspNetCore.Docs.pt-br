---
title: Testar ASP.NET Core middleware
author: tratcher
description: Saiba como testar ASP.NET Core middleware com o TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: f4ed16b136da37c093a72a8866301a188a8518a2
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406479"
---
# <a name="test-aspnet-core-middleware"></a>Testar ASP.NET Core middleware

Por [Chris Ross](https://github.com/Tratcher)

O middleware pode ser testado isoladamente com o <xref:Microsoft.AspNetCore.TestHost.TestServer> . Ele permite que você:

* Crie uma instância de um pipeline de aplicativo contendo apenas os componentes que você precisa testar.
* Envie solicitações personalizadas para verificar o comportamento do middleware.

Vantagens:

* As solicitações são enviadas na memória em vez de serem serializadas pela rede.
* Isso evita preocupações adicionais, como o gerenciamento de porta e os certificados HTTPS.
* As exceções no middleware podem fluir diretamente de volta para o teste de chamada.
* É possível personalizar as estruturas de dados do servidor, como <xref:Microsoft.AspNetCore.Http.HttpContext> , diretamente no teste.

## <a name="set-up-the-testserver"></a>Configurar o TestServer

No projeto de teste, crie um teste:

* Crie e inicie um host que usa o <xref:Microsoft.AspNetCore.TestHost.TestServer> .
* Adicione todos os serviços necessários que o middleware usa.
* Adicione o pacote NuGet [Microsoft. AspNetCore. TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) ao projeto:
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* Configure o pipeline de processamento para usar o middleware para o teste.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>Enviar solicitações com HttpClient
Enviar uma solicitação usando <xref:System.Net.Http.HttpClient> :

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Declare o resultado. Primeiro, faça uma asserção o oposto do resultado esperado. Uma execução inicial com uma asserção falsa positiva confirma que o teste falha quando o middleware está sendo executado corretamente. Execute o teste e confirme se o teste falha.

No exemplo a seguir, o middleware deve retornar um código de status 404 (*não encontrado*) quando o ponto de extremidade raiz é solicitado. Faça a execução do primeiro teste com `Assert.NotEqual( ... );` , que deve falhar:

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Altere a asserção para testar o middleware em condições normais de operação. O teste final usa `Assert.Equal( ... );` . Execute o teste novamente para confirmar que ele é aprovado.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>Enviar solicitações com HttpContext

Um aplicativo de teste também pode enviar uma solicitação usando [SendAsync (Action \<HttpContext> , CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A). No exemplo a seguir, várias verificações são feitas quando `https://example.com/A/Path/?and=query` o é processado pelo middleware:

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>permite a configuração direta de um <xref:Microsoft.AspNetCore.Http.HttpContext> objeto em vez de usar as <xref:System.Net.Http.HttpClient> abstrações. Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> para manipular estruturas somente disponíveis no servidor, como [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) ou [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).

Assim como no exemplo anterior testado para uma resposta *404-não encontrada* , verifique o oposto de cada `Assert` instrução no teste anterior. A verificação confirma que o teste falha corretamente quando o middleware está funcionando normalmente. Depois de confirmar que o teste falso positivo funciona, defina as instruções finais `Assert` para as condições e os valores esperados do teste. Execute-o novamente para confirmar que o teste foi aprovado.

## <a name="testserver-limitations"></a>Limitações do TestServer

TestServer

* Foi criado para replicar comportamentos de servidor para middleware de teste.
* ***Não*** tenta replicar todos os <xref:System.Net.Http.HttpClient> comportamentos.
* O tenta conceder ao cliente acesso ao máximo de controle possível do servidor e com o máximo de visibilidade sobre o que está acontecendo no servidor o mais possível. Por exemplo, ele pode lançar exceções normalmente não geradas pelo `HttpClient` para comunicar diretamente o estado do servidor.
* Não define alguns cabeçalhos específicos de transporte por padrão, pois aqueles geralmente não são relevantes para o middleware. Para obter mais informações, consulte a próxima seção.

### <a name="content-length-and-transfer-encoding-headers"></a>Cabeçalhos de comprimento de conteúdo e de codificação de transferência

TestServer não ***define os*** cabeçalhos de solicitação ou resposta relacionados ao transporte, como o [comprimento do conteúdo](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) ou [a codificação de transferência](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding). Os aplicativos devem evitar dependendo desses cabeçalhos porque seu uso varia de acordo com o cliente, o cenário e o protocolo. Se `Content-Length` e `Transfer-Encoding` forem necessários para testar um cenário específico, eles poderão ser especificados no teste ao compor o <xref:System.Net.Http.HttpRequestMessage> ou o <xref:Microsoft.AspNetCore.Http.HttpContext> . Para obter mais informações, consulte os seguintes problemas do GitHub:

* [dotnet/aspnetcore # 21677](https://github.com/dotnet/aspnetcore/issues/21677)
* [dotnet/aspnetcore # 18463](https://github.com/dotnet/aspnetcore/issues/18463)
* [dotnet/aspnetcore # 13273](https://github.com/dotnet/aspnetcore/issues/13273)
