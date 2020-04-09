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
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="1c1ad-103">Solucionar problemas gRPC no .NET Core</span><span class="sxs-lookup"><span data-stu-id="1c1ad-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="1c1ad-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="1c1ad-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="1c1ad-105">Este documento discute problemas comumente encontrados ao desenvolver aplicativos gRPC no .NET.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="1c1ad-106">Incompatibilidade entre a configuração SSL/TLS de serviço e cliente</span><span class="sxs-lookup"><span data-stu-id="1c1ad-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="1c1ad-107">O modelo e as amostras gRPC usam [O TLS (Transport Layer Security, segurança de camada de transporte)](https://tools.ietf.org/html/rfc5246) para proteger os serviços gRPC por padrão.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="1c1ad-108">Os clientes gRPC precisam usar uma conexão segura para chamar os serviços gRPC garantidos com sucesso.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="1c1ad-109">Você pode verificar o ASP.NET serviço core gRPC está usando TLS nos logs escritos no início do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="1c1ad-110">O serviço será ouvido em um ponto final HTTPS:</span><span class="sxs-lookup"><span data-stu-id="1c1ad-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="1c1ad-111">O cliente .NET `https` Core deve usar no endereço do servidor para fazer chamadas com uma conexão segura:</span><span class="sxs-lookup"><span data-stu-id="1c1ad-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="1c1ad-112">Todas as implementações de clientes gRPC suportam TLS.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="1c1ad-113">clientes gRPC de outros idiomas normalmente `SslCredentials`requerem o canal configurado com .</span><span class="sxs-lookup"><span data-stu-id="1c1ad-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="1c1ad-114">`SslCredentials`especifica o certificado que o cliente usará e deve ser usado em vez de credenciais inseguras.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="1c1ad-115">Para exemplos de configuração das diferentes implementações de cliente saqueador para usar O TLS, consulte [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="1c1ad-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="1c1ad-116">Chame um serviço gRPC com um certificado não confiável/inválido</span><span class="sxs-lookup"><span data-stu-id="1c1ad-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="1c1ad-117">O cliente .NET gRPC requer que o serviço tenha um certificado confiável.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="1c1ad-118">A seguinte mensagem de erro é retornada ao ligar para um serviço gRPC sem um certificado confiável:</span><span class="sxs-lookup"><span data-stu-id="1c1ad-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="1c1ad-119">Exceção não tratada.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-119">Unhandled exception.</span></span> <span data-ttu-id="1c1ad-120">System.Net.Http.HttpRequestException: A conexão SSL não pôde ser estabelecida, veja exceção interna.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="1c1ad-121">---> System.Security.Authentication.AuthenticationException: o certificado remoto é inválido de acordo com o procedimento de validação.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="1c1ad-122">Você pode ver esse erro se estiver testando seu aplicativo localmente e o certificado de desenvolvimento ASP.NET Core HTTPS não é confiável.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="1c1ad-123">Para obter instruções sobre como corrigir esse problema, confira [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core no Windows e no macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="1c1ad-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="1c1ad-124">Se você estiver chamando um serviço gRPC em outra máquina e não puder confiar no certificado, o cliente gRPC pode ser configurado para ignorar o certificado inválido.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="1c1ad-125">O código a seguir usa [httpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) para permitir chamadas sem um certificado confiável:</span><span class="sxs-lookup"><span data-stu-id="1c1ad-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

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
> <span data-ttu-id="1c1ad-126">Certificados não confiáveis só devem ser usados durante o desenvolvimento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="1c1ad-127">Os aplicativos de produção devem sempre usar certificados válidos.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="1c1ad-128">Ligue para serviços gRPC inseguros com o cliente .NET Core</span><span class="sxs-lookup"><span data-stu-id="1c1ad-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="1c1ad-129">A configuração adicional é necessária para chamar serviços gRPC inseguros com o cliente .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="1c1ad-130">O cliente gRPC `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` deve `true` definir `http` o switch e usar no endereço do servidor:</span><span class="sxs-lookup"><span data-stu-id="1c1ad-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="1c1ad-131">Não é possível iniciar ASP.NET aplicativo Core gRPC no macOS</span><span class="sxs-lookup"><span data-stu-id="1c1ad-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="1c1ad-132">Kestrel não suporta HTTP/2 com TLS no macOS e versões mais antigas do Windows, como o Windows 7.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="1c1ad-133">O ASP.NET modelo core gRPC e as amostras usam TLS por padrão.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="1c1ad-134">Você verá a seguinte mensagem de erro ao tentar iniciar o servidor gRPC:</span><span class="sxs-lookup"><span data-stu-id="1c1ad-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="1c1ad-135">Não é https://localhost:5001 possível vincular à interface de loopback IPv4: 'HTTP/2 sobre O TLS não é suportado no macOS devido à falta de suporte ao ALPN.'.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="1c1ad-136">Para contornar esse problema, configure o Kestrel e o cliente gRPC para usar HTTP/2 *sem* TLS.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="1c1ad-137">Você só deve fazer isso durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-137">You should only do this during development.</span></span> <span data-ttu-id="1c1ad-138">O não uso do TLS resultará no e-vides mensagens gRPC sem criptografia.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="1c1ad-139">O Kestrel deve configurar um ponto final HTTP/2 sem TLS em *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="1c1ad-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="1c1ad-140">Quando um ponto final HTTP/2 estiver configurado sem TLS, os [protocolos ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto final devem ser definidos como `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="1c1ad-141">`HttpProtocols.Http1AndHttp2`não pode ser usado porque o TLS é obrigado a negociar HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="1c1ad-142">Sem o TLS, todas as conexões ao ponto final são padrão para HTTP/1.1 e as chamadas gRPC falham.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="1c1ad-143">O cliente gRPC também deve ser configurado para não usar TLS.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="1c1ad-144">Para obter mais informações, consulte [Chamar serviços gRPC inseguros com o cliente .NET Core](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="1c1ad-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="1c1ad-145">HTTP/2 sem TLS só deve ser usado durante o desenvolvimento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="1c1ad-146">Os aplicativos de produção devem sempre usar a segurança do transporte.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-146">Production apps should always use transport security.</span></span> <span data-ttu-id="1c1ad-147">Para obter mais informações, consulte [considerações de segurança no gRPC para ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="1c1ad-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="1c1ad-148">os ativos gRPC C# não são código gerado a partir de arquivos .proto</span><span class="sxs-lookup"><span data-stu-id="1c1ad-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="1c1ad-149">A geração de código gRPC de clientes concretos e classes de base de serviços requer arquivos protobuf e ferramentas para serem referenciados a partir de um projeto.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="1c1ad-150">Você deve incluir:</span><span class="sxs-lookup"><span data-stu-id="1c1ad-150">You must include:</span></span>

* <span data-ttu-id="1c1ad-151">*arquivos .proto* que você `<Protobuf>` deseja usar no grupo de itens.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="1c1ad-152">[Os arquivos *.proto* importados](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) devem ser referenciados pelo projeto.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="1c1ad-153">Referência do pacote ao pacote de ferramentas [gRPC Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="1c1ad-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="1c1ad-154">Para obter mais informações sobre a geração <xref:grpc/basics>de ativos gRPC C#, consulte .</span><span class="sxs-lookup"><span data-stu-id="1c1ad-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="1c1ad-155">Por padrão, `<Protobuf>` uma referência gera um cliente concreto e uma classe base de serviço.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="1c1ad-156">O atributo `GrpcServices` do elemento de referência pode ser usado para limitar a geração de ativos C#.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="1c1ad-157">As `GrpcServices` opções válidas são:</span><span class="sxs-lookup"><span data-stu-id="1c1ad-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="1c1ad-158">`Both`(padrão quando não está presente)</span><span class="sxs-lookup"><span data-stu-id="1c1ad-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="1c1ad-159">Um aplicativo web ASP.NET que hospeda serviços gRPC só precisa da classe base de serviço gerada:</span><span class="sxs-lookup"><span data-stu-id="1c1ad-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="1c1ad-160">Um aplicativo cliente gRPC fazendo chamadas gRPC só precisa do cliente concreto gerado:</span><span class="sxs-lookup"><span data-stu-id="1c1ad-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="1c1ad-161">Projetos WPF incapazes de gerar ativos gRPC C# a partir de arquivos .proto</span><span class="sxs-lookup"><span data-stu-id="1c1ad-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="1c1ad-162">Os projetos WPF têm um [problema conhecido](https://github.com/dotnet/wpf/issues/810) que impede que a geração de código gRPC funcionasse corretamente.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="1c1ad-163">Quaisquer tipos de gRPC gerados em `Grpc.Tools` um projeto WPF fazendo referência e *arquivos .proto* criarão erros de compilação quando usados:</span><span class="sxs-lookup"><span data-stu-id="1c1ad-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="1c1ad-164">erro CS0246: O nome de tipo ou namespace 'MyGrpcServices' não foi encontrado (você está faltando uma diretiva de uso ou uma referência de montagem?)</span><span class="sxs-lookup"><span data-stu-id="1c1ad-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="1c1ad-165">Você pode contornar esse problema por:</span><span class="sxs-lookup"><span data-stu-id="1c1ad-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="1c1ad-166">Crie um novo projeto de biblioteca de classes .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="1c1ad-167">No novo projeto, adicione referências para habilitar a [geração de código C# a partir de \* \*arquivos .proto\* ](xref:grpc/basics#generated-c-assets):</span><span class="sxs-lookup"><span data-stu-id="1c1ad-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="1c1ad-168">Adicione uma referência de pacote ao pacote [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)</span><span class="sxs-lookup"><span data-stu-id="1c1ad-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="1c1ad-169">Adicione \* \*arquivos .proto\* ao `<Protobuf>` grupo de itens.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="1c1ad-170">No aplicativo WPF, adicione uma referência ao novo projeto.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="1c1ad-171">O aplicativo WPF pode usar os tipos gerados gRPC a partir do novo projeto de biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="1c1ad-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
