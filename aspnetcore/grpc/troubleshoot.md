---
title: Solucionar problemas gRPC no .NET Core
author: jamesnk
description: Solucionar problemas ao usar o gRPC no .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664125"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Solucionar problemas gRPC no .NET Core

Por [James Newton-King](https://twitter.com/jamesnk)

Este documento discute problemas comumente encontrados ao desenvolver aplicativos gRPC no .NET.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Incompatibilidade entre a configuração SSL/TLS de serviço e cliente

O modelo e as amostras gRPC usam [O TLS (Transport Layer Security, segurança de camada de transporte)](https://tools.ietf.org/html/rfc5246) para proteger os serviços gRPC por padrão. Os clientes gRPC precisam usar uma conexão segura para chamar os serviços gRPC garantidos com sucesso.

Você pode verificar o ASP.NET serviço core gRPC está usando TLS nos logs escritos no início do aplicativo. O serviço será ouvido em um ponto final HTTPS:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

O cliente .NET `https` Core deve usar no endereço do servidor para fazer chamadas com uma conexão segura:

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Todas as implementações de clientes gRPC suportam TLS. clientes gRPC de outros idiomas normalmente `SslCredentials`requerem o canal configurado com . `SslCredentials`especifica o certificado que o cliente usará e deve ser usado em vez de credenciais inseguras. Para exemplos de configuração das diferentes implementações de cliente saqueador para usar O TLS, consulte [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Chame um serviço gRPC com um certificado não confiável/inválido

O cliente .NET gRPC requer que o serviço tenha um certificado confiável. A seguinte mensagem de erro é retornada ao ligar para um serviço gRPC sem um certificado confiável:

> Exceção não tratada. System.Net.Http.HttpRequestException: A conexão SSL não pôde ser estabelecida, veja exceção interna.
> ---> System.Security.Authentication.AuthenticationException: o certificado remoto é inválido de acordo com o procedimento de validação.

Você pode ver esse erro se estiver testando seu aplicativo localmente e o certificado de desenvolvimento ASP.NET Core HTTPS não é confiável. Para obter instruções sobre como corrigir esse problema, confira [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core no Windows e no macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Se você estiver chamando um serviço gRPC em outra máquina e não puder confiar no certificado, o cliente gRPC pode ser configurado para ignorar o certificado inválido. O código a seguir usa [httpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) para permitir chamadas sem um certificado confiável:

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> Certificados não confiáveis só devem ser usados durante o desenvolvimento do aplicativo. Os aplicativos de produção devem sempre usar certificados válidos.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Ligue para serviços gRPC inseguros com o cliente .NET Core

A configuração adicional é necessária para chamar serviços gRPC inseguros com o cliente .NET Core. O cliente gRPC `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` deve `true` definir `http` o switch e usar no endereço do servidor:

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>Não é possível iniciar ASP.NET aplicativo Core gRPC no macOS

Kestrel não suporta HTTP/2 com TLS no macOS e versões mais antigas do Windows, como o Windows 7. O ASP.NET modelo core gRPC e as amostras usam TLS por padrão. Você verá a seguinte mensagem de erro ao tentar iniciar o servidor gRPC:

> Não é https://localhost:5001 possível vincular à interface de loopback IPv4: 'HTTP/2 sobre O TLS não é suportado no macOS devido à falta de suporte ao ALPN.'.

Para contornar esse problema, configure o Kestrel e o cliente gRPC para usar HTTP/2 *sem* TLS. Você só deve fazer isso durante o desenvolvimento. O não uso do TLS resultará no e-vides mensagens gRPC sem criptografia.

O Kestrel deve configurar um ponto final HTTP/2 sem TLS em *Program.cs:*

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

Quando um ponto final HTTP/2 estiver configurado sem TLS, os [protocolos ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto final devem ser definidos como `HttpProtocols.Http2`. `HttpProtocols.Http1AndHttp2`não pode ser usado porque o TLS é obrigado a negociar HTTP/2. Sem o TLS, todas as conexões ao ponto final são padrão para HTTP/1.1 e as chamadas gRPC falham.

O cliente gRPC também deve ser configurado para não usar TLS. Para obter mais informações, consulte [Chamar serviços gRPC inseguros com o cliente .NET Core](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> HTTP/2 sem TLS só deve ser usado durante o desenvolvimento do aplicativo. Os aplicativos de produção devem sempre usar a segurança do transporte. Para obter mais informações, consulte [considerações de segurança no gRPC para ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>os ativos gRPC C# não são código gerado a partir de arquivos .proto

A geração de código gRPC de clientes concretos e classes de base de serviços requer arquivos protobuf e ferramentas para serem referenciados a partir de um projeto. Você deve incluir:

* *arquivos .proto* que você `<Protobuf>` deseja usar no grupo de itens. [Os arquivos *.proto* importados](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) devem ser referenciados pelo projeto.
* Referência do pacote ao pacote de ferramentas [gRPC Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).

Para obter mais informações sobre a geração <xref:grpc/basics>de ativos gRPC C#, consulte .

Por padrão, `<Protobuf>` uma referência gera um cliente concreto e uma classe base de serviço. O atributo `GrpcServices` do elemento de referência pode ser usado para limitar a geração de ativos C#. As `GrpcServices` opções válidas são:

* `Both`(padrão quando não está presente)
* `Server`
* `Client`
* `None`

Um aplicativo web ASP.NET que hospeda serviços gRPC só precisa da classe base de serviço gerada:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Um aplicativo cliente gRPC fazendo chamadas gRPC só precisa do cliente concreto gerado:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>Projetos WPF incapazes de gerar ativos gRPC C# a partir de arquivos .proto

Os projetos WPF têm um [problema conhecido](https://github.com/dotnet/wpf/issues/810) que impede que a geração de código gRPC funcionasse corretamente. Quaisquer tipos de gRPC gerados em `Grpc.Tools` um projeto WPF fazendo referência e *arquivos .proto* criarão erros de compilação quando usados:

> erro CS0246: O nome de tipo ou namespace 'MyGrpcServices' não foi encontrado (você está faltando uma diretiva de uso ou uma referência de montagem?)

Você pode contornar esse problema por:

1. Crie um novo projeto de biblioteca de classes .NET Core.
2. No novo projeto, adicione referências para habilitar a [geração de código C# a partir de * \*arquivos .proto* ](xref:grpc/basics#generated-c-assets):
    * Adicione uma referência de pacote ao pacote [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)
    * Adicione * \*arquivos .proto* ao `<Protobuf>` grupo de itens.
3. No aplicativo WPF, adicione uma referência ao novo projeto.

O aplicativo WPF pode usar os tipos gerados gRPC a partir do novo projeto de biblioteca de classes.

[!INCLUDE[](~/includes/gRPCazure.md)]
