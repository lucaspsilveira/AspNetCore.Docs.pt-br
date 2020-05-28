---
<span data-ttu-id="8a5ff-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-102">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-103">'Identity'</span></span>
- <span data-ttu-id="8a5ff-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-105">'Razor'</span></span>
- <span data-ttu-id="8a5ff-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-106">'SignalR' uid:</span></span> 

---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="8a5ff-107">Implementação do servidor Web Kestrel no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a5ff-107">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="8a5ff-108">Por [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) e [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-108">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8a5ff-109">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-109">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="8a5ff-110">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-110">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="8a5ff-111">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-111">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="8a5ff-112">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8a5ff-112">HTTPS</span></span>
* <span data-ttu-id="8a5ff-113">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-113">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="8a5ff-114">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="8a5ff-114">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="8a5ff-115">HTTP/2 (exceto em macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-115">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="8a5ff-116">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-116">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="8a5ff-117">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-117">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="8a5ff-118">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a5ff-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="8a5ff-119">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8a5ff-119">HTTP/2 support</span></span>

<span data-ttu-id="8a5ff-120">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-120">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="8a5ff-121">Sistema operacional&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a5ff-121">Operating system&dagger;</span></span>
  * <span data-ttu-id="8a5ff-122">Windows Server 2016/Windows 10 ou posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="8a5ff-122">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="8a5ff-123">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-123">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="8a5ff-124">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="8a5ff-124">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="8a5ff-125">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-125">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="8a5ff-126">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="8a5ff-126">TLS 1.2 or later connection</span></span>

<span data-ttu-id="8a5ff-127">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-127">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="8a5ff-128">&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-128">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="8a5ff-129">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-129">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="8a5ff-130">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-130">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="8a5ff-131">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-131">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="8a5ff-132">HTTP/2 está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-132">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="8a5ff-133">Para obter mais informações sobre a configuração, consulte as seções [Opções do Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-133">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="8a5ff-134">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="8a5ff-134">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="8a5ff-135">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-135">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="8a5ff-136">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-136">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="8a5ff-137">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="8a5ff-137">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="8a5ff-139">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-139">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="8a5ff-141">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-141">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="8a5ff-142">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-142">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="8a5ff-143">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-143">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="8a5ff-144">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-144">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="8a5ff-145">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-145">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="8a5ff-146">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-146">A reverse proxy:</span></span>

* <span data-ttu-id="8a5ff-147">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-147">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="8a5ff-148">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-148">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="8a5ff-149">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-149">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="8a5ff-150">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-150">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="8a5ff-151">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-151">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="8a5ff-152">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-152">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="8a5ff-153">KESTREL em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a5ff-153">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="8a5ff-154">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-154">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="8a5ff-155">No *Program.cs*, o <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> método chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="8a5ff-155">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="8a5ff-156">Para obter mais informações sobre como criar o host, consulte as seções *configurar um host* e *as configurações do construtor padrão* de <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-156">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="8a5ff-157">Para fornecer configuração adicional após chamar `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-157">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="8a5ff-158">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="8a5ff-158">Kestrel options</span></span>

<span data-ttu-id="8a5ff-159">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-159">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="8a5ff-160">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-160">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="8a5ff-161">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-161">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="8a5ff-162">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-162">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="8a5ff-163">Nos exemplos mostrados posteriormente neste artigo, as opções de Kestrel são configuradas no código C#.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-163">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="8a5ff-164">As opções de Kestrel também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-164">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="8a5ff-165">Por exemplo, o [provedor de configuração de arquivo](xref:fundamentals/configuration/index#file-configuration-provider) pode carregar a configuração Kestrel de *appSettings. JSON* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8a5ff-165">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="8a5ff-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>e a [configuração do ponto de extremidade](#endpoint-configuration) são configuráveis de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="8a5ff-167">A configuração restante do Kestrel deve ser configurada no código C#.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-167">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="8a5ff-168">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-168">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="8a5ff-169">Configurar Kestrel em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8a5ff-169">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="8a5ff-170">Injetar uma instância do `IConfiguration` na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-170">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="8a5ff-171">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à `Configuration` propriedade.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-171">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="8a5ff-172">No `Startup.ConfigureServices` , carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-172">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="8a5ff-173">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-173">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="8a5ff-174">No *Program.cs*, carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-174">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="8a5ff-175">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-175">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="8a5ff-176">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="8a5ff-176">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="8a5ff-177">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-177">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="8a5ff-178">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-178">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="8a5ff-179">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="8a5ff-179">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="8a5ff-180">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-180">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="8a5ff-181">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-181">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="8a5ff-182">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-182">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="8a5ff-183">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-183">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="8a5ff-184">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="8a5ff-184">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="8a5ff-185">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-185">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="8a5ff-186">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-186">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="8a5ff-187">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-187">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="8a5ff-188">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-188">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="8a5ff-189">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-189">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="8a5ff-190">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-190">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="8a5ff-191">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-191">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="8a5ff-192">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="8a5ff-192">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="8a5ff-193">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-193">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="8a5ff-194">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-194">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="8a5ff-195">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-195">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="8a5ff-196">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-196">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="8a5ff-197">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-197">A minimum rate also applies to the response.</span></span> <span data-ttu-id="8a5ff-198">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-198">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="8a5ff-199">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-199">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="8a5ff-200">Substitua os limites de taxa mínimo por solicitação no middleware:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-200">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="8a5ff-201">O <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenciado no exemplo anterior não está presente no `HttpContext.Features` para solicitações HTTP/2, porque a modificação dos limites de taxa em cada solicitação não é geralmente compatível com HTTP/2 devido ao suporte de multiplexação de solicitação do protocolo.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-201">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="8a5ff-202">No entanto, o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ainda está presente em `HttpContext.Features` para solicitações HTTP/2, pois o limite de taxa de leitura ainda pode ser *desabilitado totalmente* em cada solicitação, definindo `IHttpMinRequestBodyDataRateFeature.MinDataRate` para `null` mesmo em uma solicitação HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-202">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="8a5ff-203">Tentar ler `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou tentar defini-lo como um valor diferente de `null` resultará na geração de um `NotSupportedException` devido a uma solicitação HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-203">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="8a5ff-204">Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-204">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="8a5ff-205">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="8a5ff-205">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="8a5ff-206">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-206">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="8a5ff-207">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-207">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="8a5ff-208">Fluxos máximos por conexão</span><span class="sxs-lookup"><span data-stu-id="8a5ff-208">Maximum streams per connection</span></span>

<span data-ttu-id="8a5ff-209">O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-209">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="8a5ff-210">Os fluxos em excesso são recusados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-210">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="8a5ff-211">O valor padrão é 100.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-211">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="8a5ff-212">Tamanho da tabela de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="8a5ff-212">Header table size</span></span>

<span data-ttu-id="8a5ff-213">O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-213">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="8a5ff-214">O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-214">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="8a5ff-215">O valor é fornecido em octetos e deve ser maior do que zero (0).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-215">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="8a5ff-216">O valor padrão é 4096.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-216">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="8a5ff-217">Tamanho máximo do quadro</span><span class="sxs-lookup"><span data-stu-id="8a5ff-217">Maximum frame size</span></span>

<span data-ttu-id="8a5ff-218">`Http2.MaxFrameSize`indica o tamanho máximo permitido de um conteúdo de quadro de conexão HTTP/2 recebido ou enviado pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-218">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="8a5ff-219">O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-219">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="8a5ff-220">O valor padrão é 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-220">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="8a5ff-221">Tamanho máximo do cabeçalho de solicitação</span><span class="sxs-lookup"><span data-stu-id="8a5ff-221">Maximum request header size</span></span>

<span data-ttu-id="8a5ff-222">`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-222">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="8a5ff-223">Esse limite se aplica tanto ao nome quanto ao valor em suas representações compactadas e descompactadas.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-223">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="8a5ff-224">O valor deve ser maior que zero (0).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-224">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="8a5ff-225">O valor padrão é 8.192.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-225">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="8a5ff-226">Tamanho inicial da janela de conexão</span><span class="sxs-lookup"><span data-stu-id="8a5ff-226">Initial connection window size</span></span>

<span data-ttu-id="8a5ff-227">`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-227">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="8a5ff-228">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-228">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="8a5ff-229">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-229">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="8a5ff-230">O valor padrão é 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-230">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="8a5ff-231">Tamanho inicial da janela de fluxo</span><span class="sxs-lookup"><span data-stu-id="8a5ff-231">Initial stream window size</span></span>

<span data-ttu-id="8a5ff-232">`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-232">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="8a5ff-233">As solicitações também são limitadas por `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-233">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="8a5ff-234">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-234">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="8a5ff-235">O valor padrão é 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-235">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="8a5ff-236">E/S Síncrona</span><span class="sxs-lookup"><span data-stu-id="8a5ff-236">Synchronous I/O</span></span>

<span data-ttu-id="8a5ff-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>controla se a e/s síncrona é permitida para a solicitação e a resposta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="8a5ff-238">O valor padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-238">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="8a5ff-239">Um grande número de operações de e/s síncronas de bloqueio pode levar à privação do pool de threads, o que torna o aplicativo sem resposta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-239">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="8a5ff-240">Habilite somente `AllowSynchronousIO` ao usar uma biblioteca que não ofereça suporte a e/s assíncrona.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-240">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="8a5ff-241">O exemplo a seguir habilita e/s síncrona:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-241">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="8a5ff-242">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-242">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="8a5ff-243">Configuração de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="8a5ff-243">Endpoint configuration</span></span>

<span data-ttu-id="8a5ff-244">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-244">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="8a5ff-245">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-245">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="8a5ff-246">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-246">Specify URLs using the:</span></span>

* <span data-ttu-id="8a5ff-247">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-247">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="8a5ff-248">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-248">`--urls` command-line argument.</span></span>
* <span data-ttu-id="8a5ff-249">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-249">`urls` host configuration key.</span></span>
* <span data-ttu-id="8a5ff-250">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-250">`UseUrls` extension method.</span></span>

<span data-ttu-id="8a5ff-251">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-251">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="8a5ff-252">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-252">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="8a5ff-253">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-253">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="8a5ff-254">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-254">A development certificate is created:</span></span>

* <span data-ttu-id="8a5ff-255">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-255">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="8a5ff-256">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-256">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="8a5ff-257">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-257">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="8a5ff-258">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-258">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="8a5ff-259">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-259">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="8a5ff-260">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-260">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="8a5ff-261">`KestrelServerOptions`configuração</span><span class="sxs-lookup"><span data-stu-id="8a5ff-261">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="8a5ff-262">ConfigureEndpointDefaults (ação \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="8a5ff-262">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="8a5ff-263">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-263">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="8a5ff-264">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-264">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="8a5ff-265">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="8a5ff-266">ConfigureHttpsDefaults (ação \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="8a5ff-266">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="8a5ff-267">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-267">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="8a5ff-268">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-268">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="8a5ff-269">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-269">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="8a5ff-270">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-270">Configure(IConfiguration)</span></span>

<span data-ttu-id="8a5ff-271">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-271">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="8a5ff-272">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-272">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="8a5ff-273">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="8a5ff-273">ListenOptions.UseHttps</span></span>

<span data-ttu-id="8a5ff-274">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-274">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="8a5ff-275">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-275">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="8a5ff-276">`UseHttps`: Configure Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-276">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="8a5ff-277">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-277">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="8a5ff-278">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-278">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="8a5ff-279">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-279">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="8a5ff-280">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-280">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="8a5ff-281">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-281">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="8a5ff-282">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-282">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="8a5ff-283">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-283">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="8a5ff-284">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-284">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="8a5ff-285">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-285">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="8a5ff-286">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-286">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="8a5ff-287">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-287">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="8a5ff-288">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-288">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="8a5ff-289">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-289">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="8a5ff-290">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-290">Supported configurations described next:</span></span>

* <span data-ttu-id="8a5ff-291">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="8a5ff-291">No configuration</span></span>
* <span data-ttu-id="8a5ff-292">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="8a5ff-292">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="8a5ff-293">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="8a5ff-293">Change the defaults in code</span></span>

<span data-ttu-id="8a5ff-294">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-294">*No configuration*</span></span>

<span data-ttu-id="8a5ff-295">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-295">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="8a5ff-296">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-296">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="8a5ff-297">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-297">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="8a5ff-298">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-298">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="8a5ff-299">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-299">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="8a5ff-300">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-300">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="8a5ff-301">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-301">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="8a5ff-302">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-302">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="8a5ff-303">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-303">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="8a5ff-304">Por exemplo, o **Certificates**  >  certificado**padrão** de certificados pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-304">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="8a5ff-305">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-305">Schema notes:</span></span>

* <span data-ttu-id="8a5ff-306">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-306">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="8a5ff-307">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-307">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="8a5ff-308">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-308">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="8a5ff-309">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-309">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="8a5ff-310">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-310">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="8a5ff-311">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-311">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="8a5ff-312">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-312">The `Certificate` section is optional.</span></span> <span data-ttu-id="8a5ff-313">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-313">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="8a5ff-314">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-314">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="8a5ff-315">A `Certificate` seção dá suporte **Path**a &ndash; **senha** de caminho e certificados de repositório de **assunto** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-315">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="8a5ff-316">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-316">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="8a5ff-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="8a5ff-318">`KestrelServerOptions.ConfigurationLoader`pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-318">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="8a5ff-319">A seção de configuração para cada ponto de extremidade está disponível nas opções no `Endpoint` método para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-319">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="8a5ff-320">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-320">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="8a5ff-321">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-321">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="8a5ff-322">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-322">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="8a5ff-323">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-323">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="8a5ff-324">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-324">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="8a5ff-325">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-325">*Change the defaults in code*</span></span>

<span data-ttu-id="8a5ff-326">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-326">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="8a5ff-327">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-327">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="8a5ff-328">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-328">*Kestrel support for SNI*</span></span>

<span data-ttu-id="8a5ff-329">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-329">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="8a5ff-330">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-330">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="8a5ff-331">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-331">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="8a5ff-332">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-332">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="8a5ff-333">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-333">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="8a5ff-334">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-334">SNI support requires:</span></span>

* <span data-ttu-id="8a5ff-335">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-335">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="8a5ff-336">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` sempre é `null` .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-336">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="8a5ff-337">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-337">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="8a5ff-338">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-338">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="8a5ff-339">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-339">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="8a5ff-340">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="8a5ff-340">Connection logging</span></span>

<span data-ttu-id="8a5ff-341">Chamada <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-341">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="8a5ff-342">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-342">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="8a5ff-343">Se `UseConnectionLogging` for colocado antes `UseHttps` , o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-343">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="8a5ff-344">Se `UseConnectionLogging` for colocado depois `UseHttps` , o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-344">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="8a5ff-345">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="8a5ff-345">Bind to a TCP socket</span></span>

<span data-ttu-id="8a5ff-346">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-346">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="8a5ff-347">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-347">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="8a5ff-348">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-348">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="8a5ff-349">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="8a5ff-349">Bind to a Unix socket</span></span>

<span data-ttu-id="8a5ff-350">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-350">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="8a5ff-351">No arquivo de configuração Nginx, defina a `server`  >  `location`  >  `proxy_pass` entrada como `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-351">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="8a5ff-352">`{KESTREL SOCKET}`é o nome do soquete fornecido para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-352">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="8a5ff-353">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-353">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="8a5ff-354">Porta 0</span><span class="sxs-lookup"><span data-stu-id="8a5ff-354">Port 0</span></span>

<span data-ttu-id="8a5ff-355">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-355">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="8a5ff-356">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-356">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="8a5ff-357">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-357">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="8a5ff-358">Limitações</span><span class="sxs-lookup"><span data-stu-id="8a5ff-358">Limitations</span></span>

<span data-ttu-id="8a5ff-359">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-359">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="8a5ff-360">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="8a5ff-360">`--urls` command-line argument</span></span>
* <span data-ttu-id="8a5ff-361">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="8a5ff-361">`urls` host configuration key</span></span>
* <span data-ttu-id="8a5ff-362">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="8a5ff-362">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="8a5ff-363">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-363">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="8a5ff-364">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-364">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="8a5ff-365">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-365">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="8a5ff-366">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-366">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="8a5ff-367">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="8a5ff-367">IIS endpoint configuration</span></span>

<span data-ttu-id="8a5ff-368">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-368">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="8a5ff-369">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-369">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="8a5ff-370">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="8a5ff-370">ListenOptions.Protocols</span></span>

<span data-ttu-id="8a5ff-371">A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-371">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="8a5ff-372">Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-372">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="8a5ff-373">Valor de enumeração `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="8a5ff-373">`HttpProtocols` enum value</span></span> | <span data-ttu-id="8a5ff-374">Protocolo de conexão permitido</span><span class="sxs-lookup"><span data-stu-id="8a5ff-374">Connection protocol permitted</span></span> |
| ---
<span data-ttu-id="8a5ff-375">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-376">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-377">'Identity'</span></span>
- <span data-ttu-id="8a5ff-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-379">'Razor'</span></span>
- <span data-ttu-id="8a5ff-380">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-381">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-382">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-383">'Identity'</span></span>
- <span data-ttu-id="8a5ff-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-385">'Razor'</span></span>
- <span data-ttu-id="8a5ff-386">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-387">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-388">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-389">'Identity'</span></span>
- <span data-ttu-id="8a5ff-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-391">'Razor'</span></span>
- <span data-ttu-id="8a5ff-392">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-393">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-394">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-395">'Identity'</span></span>
- <span data-ttu-id="8a5ff-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-397">'Razor'</span></span>
- <span data-ttu-id="8a5ff-398">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-399">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-400">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-401">'Identity'</span></span>
- <span data-ttu-id="8a5ff-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-403">'Razor'</span></span>
- <span data-ttu-id="8a5ff-404">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-405">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-406">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-407">'Identity'</span></span>
- <span data-ttu-id="8a5ff-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-409">'Razor'</span></span>
- <span data-ttu-id="8a5ff-410">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-411">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-412">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-413">'Identity'</span></span>
- <span data-ttu-id="8a5ff-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-415">'Razor'</span></span>
- <span data-ttu-id="8a5ff-416">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-417">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-418">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-419">'Identity'</span></span>
- <span data-ttu-id="8a5ff-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-421">'Razor'</span></span>
- <span data-ttu-id="8a5ff-422">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-423">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-424">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-425">'Identity'</span></span>
- <span data-ttu-id="8a5ff-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-427">'Razor'</span></span>
- <span data-ttu-id="8a5ff-428">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-429">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-430">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-431">'Identity'</span></span>
- <span data-ttu-id="8a5ff-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-433">'Razor'</span></span>
- <span data-ttu-id="8a5ff-434">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-435">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-436">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-437">'Identity'</span></span>
- <span data-ttu-id="8a5ff-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-439">'Razor'</span></span>
- <span data-ttu-id="8a5ff-440">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-440">'SignalR' uid:</span></span> 

<span data-ttu-id="8a5ff-441">------------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-441">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-442">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-443">'Identity'</span></span>
- <span data-ttu-id="8a5ff-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-445">'Razor'</span></span>
- <span data-ttu-id="8a5ff-446">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-447">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-448">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-449">'Identity'</span></span>
- <span data-ttu-id="8a5ff-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-451">'Razor'</span></span>
- <span data-ttu-id="8a5ff-452">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-453">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-454">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-455">'Identity'</span></span>
- <span data-ttu-id="8a5ff-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-457">'Razor'</span></span>
- <span data-ttu-id="8a5ff-458">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-459">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-460">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-461">'Identity'</span></span>
- <span data-ttu-id="8a5ff-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-463">'Razor'</span></span>
- <span data-ttu-id="8a5ff-464">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-465">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-466">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-467">'Identity'</span></span>
- <span data-ttu-id="8a5ff-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-469">'Razor'</span></span>
- <span data-ttu-id="8a5ff-470">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-471">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-472">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-473">'Identity'</span></span>
- <span data-ttu-id="8a5ff-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-475">'Razor'</span></span>
- <span data-ttu-id="8a5ff-476">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-477">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-478">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-479">'Identity'</span></span>
- <span data-ttu-id="8a5ff-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-481">'Razor'</span></span>
- <span data-ttu-id="8a5ff-482">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-483">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-484">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-485">'Identity'</span></span>
- <span data-ttu-id="8a5ff-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-487">'Razor'</span></span>
- <span data-ttu-id="8a5ff-488">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-489">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-490">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-491">'Identity'</span></span>
- <span data-ttu-id="8a5ff-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-493">'Razor'</span></span>
- <span data-ttu-id="8a5ff-494">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-495">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-496">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-497">'Identity'</span></span>
- <span data-ttu-id="8a5ff-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-499">'Razor'</span></span>
- <span data-ttu-id="8a5ff-500">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-501">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-502">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-503">'Identity'</span></span>
- <span data-ttu-id="8a5ff-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-505">'Razor'</span></span>
- <span data-ttu-id="8a5ff-506">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-507">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-508">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-509">'Identity'</span></span>
- <span data-ttu-id="8a5ff-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-511">'Razor'</span></span>
- <span data-ttu-id="8a5ff-512">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-512">'SignalR' uid:</span></span> 

<span data-ttu-id="8a5ff-513">--------------- | | `Http1`                    | Somente HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-513">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span> <span data-ttu-id="8a5ff-514">Pode ser usado com ou sem TLS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-514">Can be used with or without TLS.</span></span> <span data-ttu-id="8a5ff-515">| | `Http2`                    | Somente HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-515">| | `Http2`                    | HTTP/2 only.</span></span> <span data-ttu-id="8a5ff-516">Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-516">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> <span data-ttu-id="8a5ff-517">| | `Http1AndHttp2`            | HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-517">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="8a5ff-518">O HTTP/2 requer que o cliente selecione HTTP/2 no handshake de [ALPN (negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS; caso contrário, a conexão será padronizada como HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-518">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="8a5ff-519">O `ListenOptions.Protocols` valor padrão para qualquer ponto de extremidade é `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-519">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="8a5ff-520">Restrições TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-520">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="8a5ff-521">Versão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="8a5ff-521">TLS version 1.2 or later</span></span>
* <span data-ttu-id="8a5ff-522">Renegociação desabilitada</span><span class="sxs-lookup"><span data-stu-id="8a5ff-522">Renegotiation disabled</span></span>
* <span data-ttu-id="8a5ff-523">Compactação desabilitada</span><span class="sxs-lookup"><span data-stu-id="8a5ff-523">Compression disabled</span></span>
* <span data-ttu-id="8a5ff-524">Tamanhos mínimos de troca de chaves efêmera:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-524">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="8a5ff-525">Diffie-Hellman (ECDHE) RFC4492 de curva elíptica &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : mínimo de 224 bits</span><span class="sxs-lookup"><span data-stu-id="8a5ff-525">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="8a5ff-526">Campo finito Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : mínimo de 2048 bits</span><span class="sxs-lookup"><span data-stu-id="8a5ff-526">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="8a5ff-527">Pacote de criptografia não autorizado</span><span class="sxs-lookup"><span data-stu-id="8a5ff-527">Cipher suite not blacklisted</span></span>

<span data-ttu-id="8a5ff-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 tem &lbrack; `FIPS186` &rbrack; suporte por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="8a5ff-529">O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-529">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="8a5ff-530">As conexões são protegidas pela TLS com um certificado fornecido:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-530">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="8a5ff-531">Use o middleware de conexão para filtrar os Handshakes TLS em uma base por conexão para codificações específicas, se necessário.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-531">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="8a5ff-532">O exemplo a seguir gera <xref:System.NotSupportedException> um algoritmo de codificação para o qual o aplicativo não dá suporte.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-532">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="8a5ff-533">Como alternativa, defina e compare [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) com uma lista de pacotes de codificação aceitáveis.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-533">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="8a5ff-534">Nenhuma criptografia é usada com um algoritmo de codificação [CipherAlgorithmType. NULL](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-534">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="8a5ff-535">A filtragem de conexão também pode ser configurada por meio de um <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-535">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="8a5ff-536">No Linux, <xref:System.Net.Security.CipherSuitesPolicy> o pode ser usado para filtrar os Handshakes de TLS em uma base por conexão:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-536">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="8a5ff-537">*Definir o protocolo com base na configuração*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-537">*Set the protocol from configuration*</span></span>

<span data-ttu-id="8a5ff-538">`CreateDefaultBuilder` chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-538">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="8a5ff-539">O exemplo *appSettings. JSON* a seguir estabelece o http/1.1 como o protocolo de conexão padrão para todos os pontos de extremidade:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-539">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="8a5ff-540">O exemplo *appSettings. JSON* a seguir estabelece o protocolo de conexão HTTP/1.1 para um ponto de extremidade específico:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-540">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="8a5ff-541">Os protocolos especificados no código substituem os valores definidos pela configuração.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-541">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="8a5ff-542">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="8a5ff-542">Transport configuration</span></span>

<span data-ttu-id="8a5ff-543">Para projetos que exigem o uso de Libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ):</span><span class="sxs-lookup"><span data-stu-id="8a5ff-543">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="8a5ff-544">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-544">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="8a5ff-545">Ligue para <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> o `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="8a5ff-545">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="8a5ff-546">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="8a5ff-546">URL prefixes</span></span>

<span data-ttu-id="8a5ff-547">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-547">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="8a5ff-548">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-548">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="8a5ff-549">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-549">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="8a5ff-550">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="8a5ff-550">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="8a5ff-551">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-551">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="8a5ff-552">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="8a5ff-552">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="8a5ff-553">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-553">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="8a5ff-554">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="8a5ff-554">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="8a5ff-555">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-555">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="8a5ff-556">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-556">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="8a5ff-557">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-557">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="8a5ff-558">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-558">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="8a5ff-559">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="8a5ff-559">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="8a5ff-560">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-560">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="8a5ff-561">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-561">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="8a5ff-562">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-562">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="8a5ff-563">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="8a5ff-563">Host filtering</span></span>

<span data-ttu-id="8a5ff-564">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-564">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="8a5ff-565">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-565">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="8a5ff-566">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-566">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="8a5ff-567">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-567">`Host` headers aren't validated.</span></span>

<span data-ttu-id="8a5ff-568">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-568">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="8a5ff-569">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que é fornecido implicitamente para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-569">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="8a5ff-570">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-570">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="8a5ff-571">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-571">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="8a5ff-572">Para habilitar o middleware, defina uma `AllowedHosts` chave em *appSettings. JSON* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-572">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="8a5ff-573">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-573">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="8a5ff-574">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-574">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="8a5ff-575">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-575">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="8a5ff-576">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-576">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="8a5ff-577">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-577">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="8a5ff-578">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-578">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="8a5ff-579">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-579">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="8a5ff-580">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-580">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="8a5ff-581">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-581">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="8a5ff-582">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-582">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="8a5ff-583">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8a5ff-583">HTTPS</span></span>
* <span data-ttu-id="8a5ff-584">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-584">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="8a5ff-585">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="8a5ff-585">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="8a5ff-586">HTTP/2 (exceto em macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-586">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="8a5ff-587">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-587">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="8a5ff-588">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-588">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="8a5ff-589">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a5ff-589">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="8a5ff-590">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8a5ff-590">HTTP/2 support</span></span>

<span data-ttu-id="8a5ff-591">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-591">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="8a5ff-592">Sistema operacional&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a5ff-592">Operating system&dagger;</span></span>
  * <span data-ttu-id="8a5ff-593">Windows Server 2016/Windows 10 ou posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="8a5ff-593">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="8a5ff-594">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-594">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="8a5ff-595">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="8a5ff-595">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="8a5ff-596">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-596">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="8a5ff-597">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="8a5ff-597">TLS 1.2 or later connection</span></span>

<span data-ttu-id="8a5ff-598">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-598">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="8a5ff-599">&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-599">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="8a5ff-600">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-600">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="8a5ff-601">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-601">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="8a5ff-602">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-602">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="8a5ff-603">HTTP/2 está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-603">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="8a5ff-604">Para obter mais informações sobre a configuração, consulte as seções [Opções do Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-604">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="8a5ff-605">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="8a5ff-605">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="8a5ff-606">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-606">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="8a5ff-607">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-607">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="8a5ff-608">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="8a5ff-608">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="8a5ff-610">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-610">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="8a5ff-612">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-612">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="8a5ff-613">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-613">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="8a5ff-614">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-614">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="8a5ff-615">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-615">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="8a5ff-616">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-616">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="8a5ff-617">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-617">A reverse proxy:</span></span>

* <span data-ttu-id="8a5ff-618">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-618">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="8a5ff-619">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-619">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="8a5ff-620">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-620">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="8a5ff-621">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-621">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="8a5ff-622">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-622">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="8a5ff-623">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-623">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="8a5ff-624">Como usar o Kestrel em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a5ff-624">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="8a5ff-625">O pacote [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) está incluído no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-625">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8a5ff-626">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-626">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="8a5ff-627">Em *Program.cs*, o código de modelo chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-627">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="8a5ff-628">Para obter mais informações sobre `CreateDefaultBuilder` e criar o host, consulte a seção *configurar um host* do <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-628">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="8a5ff-629">Para fornecer configuração adicional após chamar `CreateDefaultBuilder`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-629">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="8a5ff-630">Se o aplicativo não chamar `CreateDefaultBuilder` para configurar o host, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **antes** de chamar `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-630">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="8a5ff-631">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="8a5ff-631">Kestrel options</span></span>

<span data-ttu-id="8a5ff-632">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-632">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="8a5ff-633">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-633">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="8a5ff-634">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-634">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="8a5ff-635">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-635">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="8a5ff-636">As opções de Kestrel, que são configuradas no código C# nos exemplos a seguir, também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-636">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="8a5ff-637">Por exemplo, o provedor de configuração de arquivo pode carregar a configuração Kestrel de *appSettings. JSON* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8a5ff-637">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="8a5ff-638">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-638">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="8a5ff-639">Configurar Kestrel em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8a5ff-639">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="8a5ff-640">Injetar uma instância do `IConfiguration` na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-640">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="8a5ff-641">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à `Configuration` propriedade.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-641">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="8a5ff-642">No `Startup.ConfigureServices` , carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-642">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="8a5ff-643">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-643">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="8a5ff-644">No *Program.cs*, carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-644">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="8a5ff-645">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-645">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="8a5ff-646">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="8a5ff-646">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="8a5ff-647">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-647">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="8a5ff-648">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-648">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="8a5ff-649">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="8a5ff-649">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="8a5ff-650">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-650">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="8a5ff-651">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-651">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="8a5ff-652">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-652">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="8a5ff-653">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-653">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="8a5ff-654">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="8a5ff-654">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="8a5ff-655">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-655">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="8a5ff-656">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-656">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="8a5ff-657">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-657">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="8a5ff-658">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-658">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="8a5ff-659">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-659">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="8a5ff-660">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-660">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="8a5ff-661">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-661">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="8a5ff-662">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="8a5ff-662">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="8a5ff-663">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-663">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="8a5ff-664">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-664">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="8a5ff-665">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-665">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="8a5ff-666">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-666">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="8a5ff-667">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-667">A minimum rate also applies to the response.</span></span> <span data-ttu-id="8a5ff-668">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-668">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="8a5ff-669">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-669">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="8a5ff-670">Substitua os limites de taxa mínimo por solicitação no middleware:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-670">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="8a5ff-671">Nenhum desses recursos de taxa referenciados no exemplo anterior está presente no `HttpContext.Features` para solicitações HTTP/2, porque a modificação dos limites de taxa em cada solicitação não é compatível com HTTP/2 devido ao suporte de multiplexação de solicitação do protocolo.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-671">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="8a5ff-672">Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-672">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="8a5ff-673">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="8a5ff-673">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="8a5ff-674">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-674">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="8a5ff-675">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-675">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="8a5ff-676">Fluxos máximos por conexão</span><span class="sxs-lookup"><span data-stu-id="8a5ff-676">Maximum streams per connection</span></span>

<span data-ttu-id="8a5ff-677">O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-677">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="8a5ff-678">Os fluxos em excesso são recusados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-678">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="8a5ff-679">O valor padrão é 100.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-679">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="8a5ff-680">Tamanho da tabela de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="8a5ff-680">Header table size</span></span>

<span data-ttu-id="8a5ff-681">O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-681">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="8a5ff-682">O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-682">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="8a5ff-683">O valor é fornecido em octetos e deve ser maior do que zero (0).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-683">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="8a5ff-684">O valor padrão é 4096.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-684">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="8a5ff-685">Tamanho máximo do quadro</span><span class="sxs-lookup"><span data-stu-id="8a5ff-685">Maximum frame size</span></span>

<span data-ttu-id="8a5ff-686">O `Http2.MaxFrameSize` indica o tamanho máximo do payload do quadro da conexão HTTP/2 a ser recebido.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-686">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="8a5ff-687">O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-687">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="8a5ff-688">O valor padrão é 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-688">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="8a5ff-689">Tamanho máximo do cabeçalho de solicitação</span><span class="sxs-lookup"><span data-stu-id="8a5ff-689">Maximum request header size</span></span>

<span data-ttu-id="8a5ff-690">`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-690">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="8a5ff-691">Esse limite se aplica ao nome e ao valor em suas representações compactadas e descompactadas.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-691">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="8a5ff-692">O valor deve ser maior que zero (0).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-692">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="8a5ff-693">O valor padrão é 8.192.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-693">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="8a5ff-694">Tamanho inicial da janela de conexão</span><span class="sxs-lookup"><span data-stu-id="8a5ff-694">Initial connection window size</span></span>

<span data-ttu-id="8a5ff-695">`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-695">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="8a5ff-696">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-696">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="8a5ff-697">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-697">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="8a5ff-698">O valor padrão é 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-698">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="8a5ff-699">Tamanho inicial da janela de fluxo</span><span class="sxs-lookup"><span data-stu-id="8a5ff-699">Initial stream window size</span></span>

<span data-ttu-id="8a5ff-700">`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-700">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="8a5ff-701">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-701">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="8a5ff-702">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-702">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="8a5ff-703">O valor padrão é 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-703">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="8a5ff-704">E/S Síncrona</span><span class="sxs-lookup"><span data-stu-id="8a5ff-704">Synchronous I/O</span></span>

<span data-ttu-id="8a5ff-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>controla se a e/s síncrona é permitida para a solicitação e a resposta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="8a5ff-706">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-706">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="8a5ff-707">Um grande número de operações de e/s síncronas de bloqueio pode levar à privação do pool de threads, o que torna o aplicativo sem resposta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-707">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="8a5ff-708">Habilite somente `AllowSynchronousIO` ao usar uma biblioteca que não ofereça suporte a e/s assíncrona.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-708">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="8a5ff-709">O exemplo a seguir habilita e/s síncrona:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-709">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="8a5ff-710">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-710">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="8a5ff-711">Configuração de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="8a5ff-711">Endpoint configuration</span></span>

<span data-ttu-id="8a5ff-712">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-712">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="8a5ff-713">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-713">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="8a5ff-714">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-714">Specify URLs using the:</span></span>

* <span data-ttu-id="8a5ff-715">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-715">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="8a5ff-716">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-716">`--urls` command-line argument.</span></span>
* <span data-ttu-id="8a5ff-717">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-717">`urls` host configuration key.</span></span>
* <span data-ttu-id="8a5ff-718">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-718">`UseUrls` extension method.</span></span>

<span data-ttu-id="8a5ff-719">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-719">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="8a5ff-720">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-720">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="8a5ff-721">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-721">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="8a5ff-722">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-722">A development certificate is created:</span></span>

* <span data-ttu-id="8a5ff-723">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-723">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="8a5ff-724">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-724">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="8a5ff-725">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-725">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="8a5ff-726">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-726">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="8a5ff-727">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-727">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="8a5ff-728">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-728">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="8a5ff-729">`KestrelServerOptions`configuração</span><span class="sxs-lookup"><span data-stu-id="8a5ff-729">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="8a5ff-730">ConfigureEndpointDefaults (ação \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="8a5ff-730">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="8a5ff-731">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-731">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="8a5ff-732">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-732">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="8a5ff-733">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-733">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="8a5ff-734">ConfigureHttpsDefaults (ação \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="8a5ff-734">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="8a5ff-735">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-735">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="8a5ff-736">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-736">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="8a5ff-737">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-737">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="8a5ff-738">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-738">Configure(IConfiguration)</span></span>

<span data-ttu-id="8a5ff-739">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-739">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="8a5ff-740">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-740">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="8a5ff-741">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="8a5ff-741">ListenOptions.UseHttps</span></span>

<span data-ttu-id="8a5ff-742">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-742">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="8a5ff-743">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-743">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="8a5ff-744">`UseHttps`: Configure Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-744">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="8a5ff-745">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-745">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="8a5ff-746">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-746">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="8a5ff-747">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-747">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="8a5ff-748">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-748">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="8a5ff-749">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-749">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="8a5ff-750">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-750">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="8a5ff-751">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-751">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="8a5ff-752">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-752">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="8a5ff-753">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-753">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="8a5ff-754">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-754">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="8a5ff-755">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-755">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="8a5ff-756">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-756">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="8a5ff-757">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-757">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="8a5ff-758">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-758">Supported configurations described next:</span></span>

* <span data-ttu-id="8a5ff-759">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="8a5ff-759">No configuration</span></span>
* <span data-ttu-id="8a5ff-760">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="8a5ff-760">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="8a5ff-761">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="8a5ff-761">Change the defaults in code</span></span>

<span data-ttu-id="8a5ff-762">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-762">*No configuration*</span></span>

<span data-ttu-id="8a5ff-763">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-763">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="8a5ff-764">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-764">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="8a5ff-765">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-765">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="8a5ff-766">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-766">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="8a5ff-767">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-767">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="8a5ff-768">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-768">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="8a5ff-769">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-769">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="8a5ff-770">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-770">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="8a5ff-771">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-771">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="8a5ff-772">Por exemplo, o **Certificates**  >  certificado**padrão** de certificados pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-772">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="8a5ff-773">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-773">Schema notes:</span></span>

* <span data-ttu-id="8a5ff-774">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-774">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="8a5ff-775">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-775">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="8a5ff-776">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-776">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="8a5ff-777">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-777">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="8a5ff-778">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-778">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="8a5ff-779">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-779">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="8a5ff-780">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-780">The `Certificate` section is optional.</span></span> <span data-ttu-id="8a5ff-781">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-781">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="8a5ff-782">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-782">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="8a5ff-783">A `Certificate` seção dá suporte **Path**a &ndash; **senha** de caminho e certificados de repositório de **assunto** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-783">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="8a5ff-784">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-784">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="8a5ff-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="8a5ff-786">`KestrelServerOptions.ConfigurationLoader`pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-786">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="8a5ff-787">A seção de configuração para cada ponto de extremidade está disponível nas opções no `Endpoint` método para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-787">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="8a5ff-788">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-788">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="8a5ff-789">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-789">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="8a5ff-790">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-790">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="8a5ff-791">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-791">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="8a5ff-792">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-792">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="8a5ff-793">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-793">*Change the defaults in code*</span></span>

<span data-ttu-id="8a5ff-794">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-794">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="8a5ff-795">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-795">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="8a5ff-796">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-796">*Kestrel support for SNI*</span></span>

<span data-ttu-id="8a5ff-797">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-797">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="8a5ff-798">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-798">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="8a5ff-799">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-799">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="8a5ff-800">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-800">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="8a5ff-801">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-801">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="8a5ff-802">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-802">SNI support requires:</span></span>

* <span data-ttu-id="8a5ff-803">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-803">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="8a5ff-804">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` sempre é `null` .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-804">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="8a5ff-805">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-805">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="8a5ff-806">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-806">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="8a5ff-807">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-807">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="8a5ff-808">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="8a5ff-808">Connection logging</span></span>

<span data-ttu-id="8a5ff-809">Chamada <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-809">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="8a5ff-810">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-810">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="8a5ff-811">Se `UseConnectionLogging` for colocado antes `UseHttps` , o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-811">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="8a5ff-812">Se `UseConnectionLogging` for colocado depois `UseHttps` , o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-812">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="8a5ff-813">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="8a5ff-813">Bind to a TCP socket</span></span>

<span data-ttu-id="8a5ff-814">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-814">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="8a5ff-815">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-815">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="8a5ff-816">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-816">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="8a5ff-817">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="8a5ff-817">Bind to a Unix socket</span></span>

<span data-ttu-id="8a5ff-818">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-818">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="8a5ff-819">No arquivo Nginx confiuguration, defina a `server`  >  `location`  >  `proxy_pass` entrada como `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-819">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="8a5ff-820">`{KESTREL SOCKET}`é o nome do soquete fornecido para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-820">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="8a5ff-821">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-821">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="8a5ff-822">Porta 0</span><span class="sxs-lookup"><span data-stu-id="8a5ff-822">Port 0</span></span>

<span data-ttu-id="8a5ff-823">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-823">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="8a5ff-824">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-824">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="8a5ff-825">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-825">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="8a5ff-826">Limitações</span><span class="sxs-lookup"><span data-stu-id="8a5ff-826">Limitations</span></span>

<span data-ttu-id="8a5ff-827">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-827">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="8a5ff-828">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="8a5ff-828">`--urls` command-line argument</span></span>
* <span data-ttu-id="8a5ff-829">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="8a5ff-829">`urls` host configuration key</span></span>
* <span data-ttu-id="8a5ff-830">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="8a5ff-830">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="8a5ff-831">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-831">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="8a5ff-832">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-832">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="8a5ff-833">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-833">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="8a5ff-834">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-834">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="8a5ff-835">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="8a5ff-835">IIS endpoint configuration</span></span>

<span data-ttu-id="8a5ff-836">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-836">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="8a5ff-837">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-837">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="8a5ff-838">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="8a5ff-838">ListenOptions.Protocols</span></span>

<span data-ttu-id="8a5ff-839">A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-839">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="8a5ff-840">Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-840">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="8a5ff-841">Valor de enumeração `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="8a5ff-841">`HttpProtocols` enum value</span></span> | <span data-ttu-id="8a5ff-842">Protocolo de conexão permitido</span><span class="sxs-lookup"><span data-stu-id="8a5ff-842">Connection protocol permitted</span></span> |
| ---
<span data-ttu-id="8a5ff-843">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-844">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-845">'Identity'</span></span>
- <span data-ttu-id="8a5ff-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-847">'Razor'</span></span>
- <span data-ttu-id="8a5ff-848">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-849">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-850">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-851">'Identity'</span></span>
- <span data-ttu-id="8a5ff-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-853">'Razor'</span></span>
- <span data-ttu-id="8a5ff-854">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-855">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-856">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-857">'Identity'</span></span>
- <span data-ttu-id="8a5ff-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-859">'Razor'</span></span>
- <span data-ttu-id="8a5ff-860">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-861">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-862">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-863">'Identity'</span></span>
- <span data-ttu-id="8a5ff-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-865">'Razor'</span></span>
- <span data-ttu-id="8a5ff-866">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-867">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-868">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-869">'Identity'</span></span>
- <span data-ttu-id="8a5ff-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-871">'Razor'</span></span>
- <span data-ttu-id="8a5ff-872">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-873">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-874">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-875">'Identity'</span></span>
- <span data-ttu-id="8a5ff-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-877">'Razor'</span></span>
- <span data-ttu-id="8a5ff-878">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-879">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-880">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-881">'Identity'</span></span>
- <span data-ttu-id="8a5ff-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-883">'Razor'</span></span>
- <span data-ttu-id="8a5ff-884">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-885">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-886">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-887">'Identity'</span></span>
- <span data-ttu-id="8a5ff-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-889">'Razor'</span></span>
- <span data-ttu-id="8a5ff-890">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-891">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-892">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-893">'Identity'</span></span>
- <span data-ttu-id="8a5ff-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-895">'Razor'</span></span>
- <span data-ttu-id="8a5ff-896">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-896">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-897">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-898">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-899">'Identity'</span></span>
- <span data-ttu-id="8a5ff-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-901">'Razor'</span></span>
- <span data-ttu-id="8a5ff-902">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-902">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-903">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-904">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-904">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-905">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-905">'Identity'</span></span>
- <span data-ttu-id="8a5ff-906">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-906">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-907">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-907">'Razor'</span></span>
- <span data-ttu-id="8a5ff-908">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-908">'SignalR' uid:</span></span> 

<span data-ttu-id="8a5ff-909">------------- | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-909">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-910">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-910">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-911">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-911">'Identity'</span></span>
- <span data-ttu-id="8a5ff-912">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-912">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-913">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-913">'Razor'</span></span>
- <span data-ttu-id="8a5ff-914">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-914">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-915">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-916">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-916">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-917">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-917">'Identity'</span></span>
- <span data-ttu-id="8a5ff-918">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-918">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-919">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-919">'Razor'</span></span>
- <span data-ttu-id="8a5ff-920">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-920">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-921">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-922">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-922">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-923">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-923">'Identity'</span></span>
- <span data-ttu-id="8a5ff-924">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-924">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-925">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-925">'Razor'</span></span>
- <span data-ttu-id="8a5ff-926">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-926">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-927">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-928">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-928">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-929">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-929">'Identity'</span></span>
- <span data-ttu-id="8a5ff-930">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-930">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-931">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-931">'Razor'</span></span>
- <span data-ttu-id="8a5ff-932">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-932">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-933">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-934">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-934">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-935">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-935">'Identity'</span></span>
- <span data-ttu-id="8a5ff-936">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-936">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-937">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-937">'Razor'</span></span>
- <span data-ttu-id="8a5ff-938">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-938">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-939">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-940">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-940">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-941">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-941">'Identity'</span></span>
- <span data-ttu-id="8a5ff-942">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-942">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-943">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-943">'Razor'</span></span>
- <span data-ttu-id="8a5ff-944">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-944">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-945">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-946">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-946">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-947">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-947">'Identity'</span></span>
- <span data-ttu-id="8a5ff-948">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-948">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-949">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-949">'Razor'</span></span>
- <span data-ttu-id="8a5ff-950">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-950">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-951">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-952">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-952">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-953">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-953">'Identity'</span></span>
- <span data-ttu-id="8a5ff-954">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-954">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-955">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-955">'Razor'</span></span>
- <span data-ttu-id="8a5ff-956">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-956">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-957">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-958">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-958">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-959">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-959">'Identity'</span></span>
- <span data-ttu-id="8a5ff-960">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-960">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-961">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-961">'Razor'</span></span>
- <span data-ttu-id="8a5ff-962">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-962">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-963">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-964">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-964">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-965">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-965">'Identity'</span></span>
- <span data-ttu-id="8a5ff-966">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-966">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-967">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-967">'Razor'</span></span>
- <span data-ttu-id="8a5ff-968">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-968">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-969">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-970">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-970">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-971">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-971">'Identity'</span></span>
- <span data-ttu-id="8a5ff-972">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-972">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-973">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-973">'Razor'</span></span>
- <span data-ttu-id="8a5ff-974">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-974">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a5ff-975">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a5ff-976">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-976">'Blazor'</span></span>
- <span data-ttu-id="8a5ff-977">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-977">'Identity'</span></span>
- <span data-ttu-id="8a5ff-978">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-978">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a5ff-979">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a5ff-979">'Razor'</span></span>
- <span data-ttu-id="8a5ff-980">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="8a5ff-980">'SignalR' uid:</span></span> 

<span data-ttu-id="8a5ff-981">--------------- | | `Http1`                    | Somente HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-981">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span> <span data-ttu-id="8a5ff-982">Pode ser usado com ou sem TLS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-982">Can be used with or without TLS.</span></span> <span data-ttu-id="8a5ff-983">| | `Http2`                    | Somente HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-983">| | `Http2`                    | HTTP/2 only.</span></span> <span data-ttu-id="8a5ff-984">Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-984">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> <span data-ttu-id="8a5ff-985">| | `Http1AndHttp2`            | HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-985">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="8a5ff-986">O HTTP/2 requer uma conexão TLS e [ALPN (negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) ; caso contrário, a conexão será padronizada como HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-986">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="8a5ff-987">O protocolo padrão é HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-987">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="8a5ff-988">Restrições TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-988">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="8a5ff-989">Versão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="8a5ff-989">TLS version 1.2 or later</span></span>
* <span data-ttu-id="8a5ff-990">Renegociação desabilitada</span><span class="sxs-lookup"><span data-stu-id="8a5ff-990">Renegotiation disabled</span></span>
* <span data-ttu-id="8a5ff-991">Compactação desabilitada</span><span class="sxs-lookup"><span data-stu-id="8a5ff-991">Compression disabled</span></span>
* <span data-ttu-id="8a5ff-992">Tamanhos mínimos de troca de chaves efêmera:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-992">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="8a5ff-993">Diffie-Hellman (ECDHE) RFC4492 de curva elíptica &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : mínimo de 224 bits</span><span class="sxs-lookup"><span data-stu-id="8a5ff-993">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="8a5ff-994">Campo finito Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : mínimo de 2048 bits</span><span class="sxs-lookup"><span data-stu-id="8a5ff-994">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="8a5ff-995">Pacote de criptografia não autorizado</span><span class="sxs-lookup"><span data-stu-id="8a5ff-995">Cipher suite not blacklisted</span></span>

<span data-ttu-id="8a5ff-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 tem &lbrack; `FIPS186` &rbrack; suporte por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="8a5ff-997">O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-997">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="8a5ff-998">As conexões são protegidas pela TLS com um certificado fornecido:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-998">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="8a5ff-999">Crie opcionalmente uma implementação do `IConnectionAdapter` para filtrar os handshakes TLS por conexão para codificações específicas:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-999">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="8a5ff-1000">*Definir o protocolo com base na configuração*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1000">*Set the protocol from configuration*</span></span>

<span data-ttu-id="8a5ff-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="8a5ff-1002">No exemplo *appsettings.json* a seguir, um protocolo de conexão padrão (HTTP/1.1 e HTTP/2) é estabelecido para todos os pontos de extremidade do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1002">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="8a5ff-1003">O arquivo de configuração de exemplo a seguir estabelece um protocolo de conexão para um ponto de extremidade específico:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1003">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="8a5ff-1004">Os protocolos especificados no código substituem os valores definidos pela configuração.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1004">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="8a5ff-1005">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1005">Transport configuration</span></span>

<span data-ttu-id="8a5ff-1006">Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1006">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="8a5ff-1007">Essa é uma alteração da falha para aplicativos ASP.NET Core 2.0 que atualizam para o 2.1 que chamam <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dependem de um dos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1007">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="8a5ff-1008">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referência direta de pacote)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1008">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="8a5ff-1009">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1009">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="8a5ff-1010">Para projetos que exigem o uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1010">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="8a5ff-1011">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1011">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="8a5ff-1012">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1012">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="8a5ff-1013">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1013">URL prefixes</span></span>

<span data-ttu-id="8a5ff-1014">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1014">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="8a5ff-1015">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1015">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="8a5ff-1016">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1016">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="8a5ff-1017">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1017">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="8a5ff-1018">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1018">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="8a5ff-1019">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1019">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="8a5ff-1020">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1020">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="8a5ff-1021">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1021">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="8a5ff-1022">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1022">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="8a5ff-1023">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1023">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="8a5ff-1024">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1024">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="8a5ff-1025">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1025">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="8a5ff-1026">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1026">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="8a5ff-1027">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1027">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="8a5ff-1028">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1028">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="8a5ff-1029">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1029">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="8a5ff-1030">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1030">Host filtering</span></span>

<span data-ttu-id="8a5ff-1031">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1031">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="8a5ff-1032">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1032">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="8a5ff-1033">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1033">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="8a5ff-1034">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1034">`Host` headers aren't validated.</span></span>

<span data-ttu-id="8a5ff-1035">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1035">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="8a5ff-1036">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que está incluído no [metapacote Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1036">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="8a5ff-1037">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1037">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="8a5ff-1038">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1038">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="8a5ff-1039">Para habilitar o middleware, defina uma `AllowedHosts` chave em *appSettings. JSON* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1039">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="8a5ff-1040">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1040">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="8a5ff-1041">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1041">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="8a5ff-1042">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1042">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="8a5ff-1043">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1043">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="8a5ff-1044">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1044">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="8a5ff-1045">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1045">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="8a5ff-1046">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1046">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="8a5ff-1047">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1047">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="8a5ff-1048">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1048">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="8a5ff-1049">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1049">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="8a5ff-1050">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1050">HTTPS</span></span>
* <span data-ttu-id="8a5ff-1051">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1051">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="8a5ff-1052">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1052">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="8a5ff-1053">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1053">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="8a5ff-1054">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1054">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="8a5ff-1055">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1055">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="8a5ff-1056">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1056">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="8a5ff-1057">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1057">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="8a5ff-1058">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1058">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="8a5ff-1060">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1060">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="8a5ff-1062">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1062">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="8a5ff-1063">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1063">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="8a5ff-1064">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1064">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="8a5ff-1065">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1065">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="8a5ff-1066">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1066">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="8a5ff-1067">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1067">A reverse proxy:</span></span>

* <span data-ttu-id="8a5ff-1068">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1068">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="8a5ff-1069">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1069">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="8a5ff-1070">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1070">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="8a5ff-1071">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1071">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="8a5ff-1072">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1072">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="8a5ff-1073">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1073">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="8a5ff-1074">Como usar o Kestrel em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1074">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="8a5ff-1075">O pacote [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) está incluído no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1075">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8a5ff-1076">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1076">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="8a5ff-1077">Em *Program.cs*, o código de modelo chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1077">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="8a5ff-1078">Para fornecer configuração adicional após chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1078">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="8a5ff-1079">Para obter mais informações sobre `CreateDefaultBuilder` e criar o host, consulte a seção *configurar um host* do <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1079">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="8a5ff-1080">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1080">Kestrel options</span></span>

<span data-ttu-id="8a5ff-1081">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1081">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="8a5ff-1082">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1082">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="8a5ff-1083">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1083">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="8a5ff-1084">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1084">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="8a5ff-1085">As opções de Kestrel, que são configuradas no código C# nos exemplos a seguir, também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1085">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="8a5ff-1086">Por exemplo, o provedor de configuração de arquivo pode carregar a configuração Kestrel de *appSettings. JSON* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1086">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="8a5ff-1087">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1087">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="8a5ff-1088">Configurar Kestrel em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1088">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="8a5ff-1089">Injetar uma instância do `IConfiguration` na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1089">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="8a5ff-1090">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à `Configuration` propriedade.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1090">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="8a5ff-1091">No `Startup.ConfigureServices` , carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1091">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="8a5ff-1092">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1092">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="8a5ff-1093">No *Program.cs*, carregue a `Kestrel` seção de configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1093">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="8a5ff-1094">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1094">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="8a5ff-1095">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1095">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="8a5ff-1096">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1096">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="8a5ff-1097">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1097">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="8a5ff-1098">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1098">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="8a5ff-1099">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1099">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="8a5ff-1100">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1100">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="8a5ff-1101">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1101">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="8a5ff-1102">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1102">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="8a5ff-1103">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1103">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="8a5ff-1104">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1104">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="8a5ff-1105">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1105">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="8a5ff-1106">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1106">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="8a5ff-1107">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1107">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="8a5ff-1108">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1108">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="8a5ff-1109">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1109">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="8a5ff-1110">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1110">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="8a5ff-1111">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1111">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="8a5ff-1112">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1112">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="8a5ff-1113">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1113">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="8a5ff-1114">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1114">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="8a5ff-1115">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1115">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="8a5ff-1116">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1116">A minimum rate also applies to the response.</span></span> <span data-ttu-id="8a5ff-1117">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1117">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="8a5ff-1118">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1118">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="8a5ff-1119">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1119">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="8a5ff-1120">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1120">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="8a5ff-1121">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1121">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="8a5ff-1122">E/S Síncrona</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1122">Synchronous I/O</span></span>

<span data-ttu-id="8a5ff-1123"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>controla se a e/s síncrona é permitida para a solicitação e a resposta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1123"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="8a5ff-1124">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1124">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="8a5ff-1125">Um grande número de operações de e/s síncronas de bloqueio pode levar à privação do pool de threads, o que torna o aplicativo sem resposta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1125">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="8a5ff-1126">Habilite somente `AllowSynchronousIO` ao usar uma biblioteca que não ofereça suporte a e/s assíncrona.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1126">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="8a5ff-1127">O exemplo a seguir desabilita e/s síncrona:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1127">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="8a5ff-1128">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1128">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="8a5ff-1129">Configuração de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1129">Endpoint configuration</span></span>

<span data-ttu-id="8a5ff-1130">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1130">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="8a5ff-1131">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1131">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="8a5ff-1132">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1132">Specify URLs using the:</span></span>

* <span data-ttu-id="8a5ff-1133">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1133">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="8a5ff-1134">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1134">`--urls` command-line argument.</span></span>
* <span data-ttu-id="8a5ff-1135">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1135">`urls` host configuration key.</span></span>
* <span data-ttu-id="8a5ff-1136">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1136">`UseUrls` extension method.</span></span>

<span data-ttu-id="8a5ff-1137">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1137">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="8a5ff-1138">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1138">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="8a5ff-1139">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1139">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="8a5ff-1140">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1140">A development certificate is created:</span></span>

* <span data-ttu-id="8a5ff-1141">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1141">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="8a5ff-1142">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1142">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="8a5ff-1143">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1143">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="8a5ff-1144">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1144">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="8a5ff-1145">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1145">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="8a5ff-1146">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1146">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="8a5ff-1147">`KestrelServerOptions`configuração</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1147">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="8a5ff-1148">ConfigureEndpointDefaults (ação \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1148">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="8a5ff-1149">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1149">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="8a5ff-1150">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1150">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="8a5ff-1151">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1151">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="8a5ff-1152">ConfigureHttpsDefaults (ação \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1152">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="8a5ff-1153">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1153">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="8a5ff-1154">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1154">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="8a5ff-1155">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> de chamar não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1155">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="8a5ff-1156">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1156">Configure(IConfiguration)</span></span>

<span data-ttu-id="8a5ff-1157">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1157">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="8a5ff-1158">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1158">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="8a5ff-1159">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1159">ListenOptions.UseHttps</span></span>

<span data-ttu-id="8a5ff-1160">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1160">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="8a5ff-1161">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1161">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="8a5ff-1162">`UseHttps`: Configure Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1162">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="8a5ff-1163">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1163">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="8a5ff-1164">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1164">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="8a5ff-1165">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1165">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="8a5ff-1166">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1166">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="8a5ff-1167">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1167">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="8a5ff-1168">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1168">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="8a5ff-1169">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1169">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="8a5ff-1170">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1170">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="8a5ff-1171">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1171">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="8a5ff-1172">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1172">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="8a5ff-1173">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1173">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="8a5ff-1174">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1174">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="8a5ff-1175">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1175">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="8a5ff-1176">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1176">Supported configurations described next:</span></span>

* <span data-ttu-id="8a5ff-1177">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1177">No configuration</span></span>
* <span data-ttu-id="8a5ff-1178">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1178">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="8a5ff-1179">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1179">Change the defaults in code</span></span>

<span data-ttu-id="8a5ff-1180">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1180">*No configuration*</span></span>

<span data-ttu-id="8a5ff-1181">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1181">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="8a5ff-1182">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1182">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="8a5ff-1183">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1183">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="8a5ff-1184">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1184">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="8a5ff-1185">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1185">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="8a5ff-1186">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1186">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="8a5ff-1187">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1187">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="8a5ff-1188">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1188">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="8a5ff-1189">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1189">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="8a5ff-1190">Por exemplo, o **Certificates**  >  certificado**padrão** de certificados pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1190">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="8a5ff-1191">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1191">Schema notes:</span></span>

* <span data-ttu-id="8a5ff-1192">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1192">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="8a5ff-1193">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1193">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="8a5ff-1194">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1194">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="8a5ff-1195">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1195">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="8a5ff-1196">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1196">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="8a5ff-1197">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1197">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="8a5ff-1198">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1198">The `Certificate` section is optional.</span></span> <span data-ttu-id="8a5ff-1199">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1199">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="8a5ff-1200">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1200">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="8a5ff-1201">A `Certificate` seção dá suporte **Path**a &ndash; **senha** de caminho e certificados de repositório de **assunto** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1201">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="8a5ff-1202">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1202">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="8a5ff-1203">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1203">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="8a5ff-1204">`KestrelServerOptions.ConfigurationLoader`pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1204">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="8a5ff-1205">A seção de configuração para cada ponto de extremidade está disponível nas opções no `Endpoint` método para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1205">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="8a5ff-1206">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1206">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="8a5ff-1207">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1207">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="8a5ff-1208">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1208">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="8a5ff-1209">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1209">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="8a5ff-1210">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1210">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="8a5ff-1211">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1211">*Change the defaults in code*</span></span>

<span data-ttu-id="8a5ff-1212">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1212">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="8a5ff-1213">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1213">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="8a5ff-1214">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1214">*Kestrel support for SNI*</span></span>

<span data-ttu-id="8a5ff-1215">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1215">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="8a5ff-1216">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1216">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="8a5ff-1217">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1217">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="8a5ff-1218">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1218">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="8a5ff-1219">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1219">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="8a5ff-1220">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1220">SNI support requires:</span></span>

* <span data-ttu-id="8a5ff-1221">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1221">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="8a5ff-1222">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` sempre é `null` .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1222">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="8a5ff-1223">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1223">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="8a5ff-1224">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1224">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="8a5ff-1225">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1225">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="8a5ff-1226">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1226">Connection logging</span></span>

<span data-ttu-id="8a5ff-1227">Chamada <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1227">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="8a5ff-1228">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1228">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="8a5ff-1229">Se `UseConnectionLogging` for colocado antes `UseHttps` , o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1229">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="8a5ff-1230">Se `UseConnectionLogging` for colocado depois `UseHttps` , o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1230">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="8a5ff-1231">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1231">Bind to a TCP socket</span></span>

<span data-ttu-id="8a5ff-1232">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1232">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="8a5ff-1233">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1233">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="8a5ff-1234">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1234">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="8a5ff-1235">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1235">Bind to a Unix socket</span></span>

<span data-ttu-id="8a5ff-1236">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1236">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="8a5ff-1237">No arquivo Nginx confiuguration, defina a `server`  >  `location`  >  `proxy_pass` entrada como `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1237">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="8a5ff-1238">`{KESTREL SOCKET}`é o nome do soquete fornecido para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1238">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="8a5ff-1239">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1239">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="8a5ff-1240">Porta 0</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1240">Port 0</span></span>

<span data-ttu-id="8a5ff-1241">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1241">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="8a5ff-1242">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1242">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="8a5ff-1243">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1243">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="8a5ff-1244">Limitações</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1244">Limitations</span></span>

<span data-ttu-id="8a5ff-1245">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1245">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="8a5ff-1246">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1246">`--urls` command-line argument</span></span>
* <span data-ttu-id="8a5ff-1247">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1247">`urls` host configuration key</span></span>
* <span data-ttu-id="8a5ff-1248">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1248">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="8a5ff-1249">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1249">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="8a5ff-1250">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1250">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="8a5ff-1251">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1251">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="8a5ff-1252">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1252">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="8a5ff-1253">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1253">IIS endpoint configuration</span></span>

<span data-ttu-id="8a5ff-1254">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1254">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="8a5ff-1255">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1255">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="8a5ff-1256">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1256">Transport configuration</span></span>

<span data-ttu-id="8a5ff-1257">Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1257">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="8a5ff-1258">Essa é uma alteração da falha para aplicativos ASP.NET Core 2.0 que atualizam para o 2.1 que chamam <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dependem de um dos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1258">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="8a5ff-1259">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referência direta de pacote)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1259">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="8a5ff-1260">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1260">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="8a5ff-1261">Para projetos que exigem o uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1261">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="8a5ff-1262">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1262">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="8a5ff-1263">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1263">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="8a5ff-1264">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1264">URL prefixes</span></span>

<span data-ttu-id="8a5ff-1265">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1265">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="8a5ff-1266">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1266">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="8a5ff-1267">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1267">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="8a5ff-1268">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1268">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="8a5ff-1269">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1269">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="8a5ff-1270">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1270">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="8a5ff-1271">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1271">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="8a5ff-1272">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1272">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="8a5ff-1273">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1273">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="8a5ff-1274">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1274">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="8a5ff-1275">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1275">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="8a5ff-1276">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1276">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="8a5ff-1277">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1277">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="8a5ff-1278">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1278">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="8a5ff-1279">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1279">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="8a5ff-1280">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1280">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="8a5ff-1281">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1281">Host filtering</span></span>

<span data-ttu-id="8a5ff-1282">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1282">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="8a5ff-1283">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1283">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="8a5ff-1284">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1284">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="8a5ff-1285">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1285">`Host` headers aren't validated.</span></span>

<span data-ttu-id="8a5ff-1286">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1286">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="8a5ff-1287">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que está incluído no [metapacote Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1287">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="8a5ff-1288">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1288">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="8a5ff-1289">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1289">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="8a5ff-1290">Para habilitar o middleware, defina uma `AllowedHosts` chave em *appSettings. JSON* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1290">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="8a5ff-1291">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1291">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="8a5ff-1292">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1292">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="8a5ff-1293">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1293">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="8a5ff-1294">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1294">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="8a5ff-1295">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1295">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="8a5ff-1296">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1296">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="8a5ff-1297">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1297">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="8a5ff-1298">Descarga de solicitação HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1298">HTTP/1.1 request draining</span></span>

<span data-ttu-id="8a5ff-1299">A abertura de conexões HTTP é demorada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1299">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="8a5ff-1300">Para HTTPS, ele também consome muitos recursos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1300">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="8a5ff-1301">Portanto, o Kestrel tenta reutilizar conexões de acordo com o protocolo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1301">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="8a5ff-1302">Um corpo de solicitação deve ser totalmente consumido para permitir que a conexão seja reutilizada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1302">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="8a5ff-1303">O aplicativo nem sempre consome o corpo da solicitação, como `POST` solicitações em que o servidor retorna uma resposta de redirecionamento ou 404.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1303">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="8a5ff-1304">No `POST` – caso de redirecionamento:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1304">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="8a5ff-1305">O cliente pode já ter enviado parte dos `POST` dados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1305">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="8a5ff-1306">O servidor grava a resposta 301.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1306">The server writes the 301 response.</span></span>
* <span data-ttu-id="8a5ff-1307">A conexão não pode ser usada para uma nova solicitação até que os `POST` dados do corpo da solicitação anterior tenham sido totalmente lidos.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1307">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="8a5ff-1308">Kestrel tenta drenar o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1308">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="8a5ff-1309">Drenar o corpo da solicitação significa ler e descartar os dados sem processá-los.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1309">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="8a5ff-1310">O processo de descarga faz um tradoff entre permitir que a conexão seja reutilizada e o tempo necessário para drenar os dados restantes:</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1310">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="8a5ff-1311">O descarregamento tem um tempo limite de cinco segundos, o que não é configurável.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1311">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="8a5ff-1312">Se todos os dados especificados pelo `Content-Length` `Transfer-Encoding` cabeçalho ou não tiverem sido lidos antes do tempo limite, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1312">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="8a5ff-1313">Às vezes, você pode querer encerrar a solicitação imediatamente, antes ou depois de gravar a resposta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1313">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="8a5ff-1314">Por exemplo, os clientes podem ter limites de dados restritivos, portanto, limitar os dados carregados pode ser uma prioridade.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1314">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="8a5ff-1315">Nesses casos, para encerrar uma solicitação, chame [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) de um controlador, Razor página ou middleware.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1315">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="8a5ff-1316">Há limitações para chamar `Abort` :</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1316">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="8a5ff-1317">A criação de novas conexões pode ser lenta e dispendiosa.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1317">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="8a5ff-1318">Não há nenhuma garantia de que o cliente leu a resposta antes que a conexão seja fechada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1318">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="8a5ff-1319">`Abort`A chamada deve ser rara e reservada para casos de erro graves, não erros comuns.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1319">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="8a5ff-1320">Só chame `Abort` quando um problema específico precisar ser resolvido.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1320">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="8a5ff-1321">Por exemplo, chame `Abort` se clientes mal-intencionados estão tentando `POST` dados ou quando há um bug no código do cliente que causa grandes ou inúmeras solicitações.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1321">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="8a5ff-1322">Não chame `Abort` situações de erro comuns, como HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1322">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="8a5ff-1323">Chamar [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) antes `Abort` de chamar garante que o servidor concluiu a gravação da resposta.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1323">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="8a5ff-1324">No entanto, o comportamento do cliente não é previsível e pode não ler a resposta antes que a conexão seja anulada.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1324">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="8a5ff-1325">Esse processo é diferente para HTTP/2 porque o protocolo dá suporte à anulação de fluxos de solicitação individuais sem fechar a conexão.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1325">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="8a5ff-1326">O tempo limite de esgotamento de cinco segundos não se aplica.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1326">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="8a5ff-1327">Se houver algum dado de corpo de solicitação não lido depois de concluir uma resposta, o servidor enviará um quadro RST HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1327">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="8a5ff-1328">Quadros de dados de corpo de solicitação adicionais são ignorados.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1328">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="8a5ff-1329">Se possível, é melhor para os clientes utilizarem o cabeçalho de solicitação [esperado: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) e aguarde até que o servidor responda antes de começar a enviar o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1329">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="8a5ff-1330">Isso dá ao cliente uma oportunidade de examinar a resposta e anular antes de enviar dados desnecessários.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1330">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a5ff-1331">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1331">Additional resources</span></span>

* <span data-ttu-id="8a5ff-1332">Ao usar soquetes UNIX no Linux, o soquete não é excluído automaticamente no aplicativo desligado.</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1332">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="8a5ff-1333">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1333">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="8a5ff-1334">RFC 7230: Sintaxe e roteamento da mensagem (Seção 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="8a5ff-1334">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
