---
title: Hospedar o ASP.NET Core no Linux com o Apache
author: rick-anderson
description: Saiba como configurar o Apache como um servidor proxy reverso no CentOS para redirecionar o tráfego HTTP para um aplicativo do Web ASP.NET Core em execução no Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 5efd84cc4b54c2ad21a7c038137fe68ee3a40f55
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403983"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="e1afb-103">Hospedar o ASP.NET Core no Linux com o Apache</span><span class="sxs-lookup"><span data-stu-id="e1afb-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="e1afb-104">Por [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="e1afb-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="e1afb-105">Usando este guia, saiba como configurar o [Apache](https://httpd.apache.org/) como um servidor proxy reverso no [CentOS 7](https://www.centos.org/) para redirecionar o tráfego HTTP para um aplicativo Web ASP.NET Core em execução no servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="e1afb-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="e1afb-106">A [extensão mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) e os módulos relacionados criam o proxy reverso do servidor.</span><span class="sxs-lookup"><span data-stu-id="e1afb-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e1afb-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e1afb-107">Prerequisites</span></span>

* <span data-ttu-id="e1afb-108">Servidor que executa o CentOS 7 com uma conta de usuário padrão com privilégio sudo.</span><span class="sxs-lookup"><span data-stu-id="e1afb-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="e1afb-109">Instale o runtime do .NET Core no servidor.</span><span class="sxs-lookup"><span data-stu-id="e1afb-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="e1afb-110">Visite a [página baixar o .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="e1afb-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="e1afb-111">Selecione a versão mais recente do .NET Core sem visualização.</span><span class="sxs-lookup"><span data-stu-id="e1afb-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="e1afb-112">Baixe o tempo de execução de não visualização mais recente na tabela em **executar aplicativos-tempo de execução**.</span><span class="sxs-lookup"><span data-stu-id="e1afb-112">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="e1afb-113">Selecione o link de **instruções do Gerenciador de pacotes** do Linux e siga as instruções do CentOS.</span><span class="sxs-lookup"><span data-stu-id="e1afb-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="e1afb-114">Um aplicativo ASP.NET Core existente.</span><span class="sxs-lookup"><span data-stu-id="e1afb-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="e1afb-115">A qualquer momento no futuro depois de atualizar a estrutura compartilhada, reinicie o ASP.NET Core aplicativos hospedados pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="e1afb-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="e1afb-116">Publicar e copiar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="e1afb-116">Publish and copy over the app</span></span>

<span data-ttu-id="e1afb-117">Configurar o aplicativo para um [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="e1afb-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="e1afb-118">Se o aplicativo for executado localmente e não estiver configurado para fazer conexões seguras (HTTPS), adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e1afb-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="e1afb-119">Configure o aplicativo para lidar com conexões seguras locais.</span><span class="sxs-lookup"><span data-stu-id="e1afb-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="e1afb-120">Para obter mais informações, veja a seção [Configuração de HTTPS](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="e1afb-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="e1afb-121">Remova `https://localhost:5001` (se houver) da propriedade `applicationUrl` no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e1afb-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="e1afb-122">Execute [dotnet publish](/dotnet/core/tools/dotnet-publish) do ambiente de desenvolvimento para empacotar um aplicativo em um diretório (por exemplo, *bin/Release/&lt;target_framework_moniker&gt;/publish*) que pode ser executado no servidor:</span><span class="sxs-lookup"><span data-stu-id="e1afb-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="e1afb-123">O aplicativo também poderá ser publicado como uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd) se você preferir não manter o runtime do .NET Core no servidor.</span><span class="sxs-lookup"><span data-stu-id="e1afb-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="e1afb-124">Copie o aplicativo ASP.NET Core para o servidor usando uma ferramenta que se integre ao fluxo de trabalho da organização (por exemplo, SCP, SFTP).</span><span class="sxs-lookup"><span data-stu-id="e1afb-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="e1afb-125">É comum para localizar os aplicativos Web no diretório *var* (por exemplo, *var/www/helloapp*).</span><span class="sxs-lookup"><span data-stu-id="e1afb-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="e1afb-126">Em um cenário de implantação de produção, um fluxo de trabalho de integração contínua faz o trabalho de publicar o aplicativo e copiar os ativos para o servidor.</span><span class="sxs-lookup"><span data-stu-id="e1afb-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="e1afb-127">Configurar um servidor proxy</span><span class="sxs-lookup"><span data-stu-id="e1afb-127">Configure a proxy server</span></span>

<span data-ttu-id="e1afb-128">Um proxy reverso é uma configuração comum para atender a aplicativos Web dinâmicos.</span><span class="sxs-lookup"><span data-stu-id="e1afb-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="e1afb-129">O proxy reverso encerra a solicitação HTTP e a encaminha para o aplicativo ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e1afb-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="e1afb-130">Um servidor proxy é aquele que encaminha as solicitações de cliente para outro servidor, em vez de atendê-las por conta própria.</span><span class="sxs-lookup"><span data-stu-id="e1afb-130">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="e1afb-131">Um proxy reverso encaminha para um destino fixo, normalmente, em nome de clientes arbitrários.</span><span class="sxs-lookup"><span data-stu-id="e1afb-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="e1afb-132">Neste guia, o Apache é configurado como o proxy reverso em execução no mesmo servidor em que o Kestrel está servindo o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e1afb-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="e1afb-133">Como as solicitações são encaminhadas pelo proxy reverso, use o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer) do pacote [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) .</span><span class="sxs-lookup"><span data-stu-id="e1afb-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="e1afb-134">O middleware atualiza o `Request.Scheme` usando o cabeçalho `X-Forwarded-Proto`, de forma que URIs de redirecionamento e outras políticas de segurança funcionam corretamente.</span><span class="sxs-lookup"><span data-stu-id="e1afb-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="e1afb-135">Qualquer componente que dependa do esquema, como autenticação, geração de link, redirecionamentos e localização geográfica, deverá ser colocado depois de invocar o Middleware de Cabeçalhos Encaminhados.</span><span class="sxs-lookup"><span data-stu-id="e1afb-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="e1afb-136">Invoque o <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> método na parte superior `Startup.Configure` antes de chamar outro middleware.</span><span class="sxs-lookup"><span data-stu-id="e1afb-136">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="e1afb-137">Configure o middleware para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto`:</span><span class="sxs-lookup"><span data-stu-id="e1afb-137">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="e1afb-138">Se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado para o middleware, os cabeçalhos padrão para encaminhar serão `None`.</span><span class="sxs-lookup"><span data-stu-id="e1afb-138">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="e1afb-139">Os proxies em execução em endereços de loopback (127.0.0.0/8, [::1]), incluindo o endereço de host local padrão (127.0.0.1), são confiáveis por padrão.</span><span class="sxs-lookup"><span data-stu-id="e1afb-139">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="e1afb-140">Se outros proxies ou redes confiáveis em que a organização trata solicitações entre a Internet e o servidor Web, adicione-os à lista de <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> ou <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="e1afb-140">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="e1afb-141">O exemplo a seguir adiciona um servidor proxy confiável no endereço IP 10.0.0.100 ao Middleware de cabeçalhos encaminhados `KnownProxies` em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e1afb-141">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="e1afb-142">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="e1afb-142">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="e1afb-143">Instalar o Apache</span><span class="sxs-lookup"><span data-stu-id="e1afb-143">Install Apache</span></span>

<span data-ttu-id="e1afb-144">Atualize os pacotes CentOS para as respectivas versões estáveis mais recentes:</span><span class="sxs-lookup"><span data-stu-id="e1afb-144">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="e1afb-145">Instale o servidor Web do Apache no CentOS com um único comando `yum`:</span><span class="sxs-lookup"><span data-stu-id="e1afb-145">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="e1afb-146">Saída de exemplo depois de executar o comando:</span><span class="sxs-lookup"><span data-stu-id="e1afb-146">Sample output after running the command:</span></span>

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> <span data-ttu-id="e1afb-147">Neste exemplo, o resultado reflete httpd.86_64, pois a versão do CentOS 7 é de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="e1afb-147">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="e1afb-148">Para verificar o local em que o Apache está instalado, execute `whereis httpd` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="e1afb-148">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="e1afb-149">Configurar o Apache</span><span class="sxs-lookup"><span data-stu-id="e1afb-149">Configure Apache</span></span>

<span data-ttu-id="e1afb-150">Os arquivos de configuração do Apache estão localizados no diretório `/etc/httpd/conf.d/`.</span><span class="sxs-lookup"><span data-stu-id="e1afb-150">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="e1afb-151">Qualquer arquivo com a extensão *.conf* é processado em ordem alfabética, além dos arquivos de configuração do módulo em `/etc/httpd/conf.modules.d/`, que contém todos os arquivos de configuração necessários para carregar os módulos.</span><span class="sxs-lookup"><span data-stu-id="e1afb-151">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="e1afb-152">Crie um arquivo de configuração chamado *helloapp.conf* para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e1afb-152">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

<span data-ttu-id="e1afb-153">O bloco `VirtualHost` pode aparecer várias vezes, em um ou mais arquivos em um servidor.</span><span class="sxs-lookup"><span data-stu-id="e1afb-153">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="e1afb-154">No arquivo de configuração anterior, o Apache aceita tráfego público na porta 80.</span><span class="sxs-lookup"><span data-stu-id="e1afb-154">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="e1afb-155">O domínio `www.example.com` está sendo atendido e o alias `*.example.com` é resolvido para o mesmo site.</span><span class="sxs-lookup"><span data-stu-id="e1afb-155">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="e1afb-156">Veja [Suporte a host virtual baseado em nome](https://httpd.apache.org/docs/current/vhosts/name-based.html) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="e1afb-156">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="e1afb-157">As solicitações passadas por proxy na raiz para a porta 5000 do servidor em 127.0.0.1.</span><span class="sxs-lookup"><span data-stu-id="e1afb-157">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="e1afb-158">Para a comunicação bidirecional, `ProxyPass` e `ProxyPassReverse` são necessários.</span><span class="sxs-lookup"><span data-stu-id="e1afb-158">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="e1afb-159">Para alterar o IP/porta do Kestrel, veja [Kestrel: configuração de ponto de extremidade](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="e1afb-159">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="e1afb-160">Falha ao especificar uma [diretiva ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) no bloco **VirtualHost** expõe seu aplicativo para vulnerabilidades de segurança.</span><span class="sxs-lookup"><span data-stu-id="e1afb-160">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="e1afb-161">Associações de curinga de subdomínio (por exemplo, `*.example.com`) não oferecerão esse risco de segurança se você controlar o domínio pai completo (em vez de `*.com`, o qual é vulnerável).</span><span class="sxs-lookup"><span data-stu-id="e1afb-161">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e1afb-162">Veja [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="e1afb-162">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="e1afb-163">O registro em log pode ser configurado por `VirtualHost` usando diretivas `ErrorLog` e `CustomLog`.</span><span class="sxs-lookup"><span data-stu-id="e1afb-163">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="e1afb-164">`ErrorLog` é o local em que o servidor registrará em log os erros, enquanto `CustomLog` define o nome de arquivo e o formato do arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="e1afb-164">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="e1afb-165">Nesse caso, esse é o local em que as informações de solicitação são registradas em log.</span><span class="sxs-lookup"><span data-stu-id="e1afb-165">In this case, this is where request information is logged.</span></span> <span data-ttu-id="e1afb-166">Há uma linha para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="e1afb-166">There's one line for each request.</span></span>

<span data-ttu-id="e1afb-167">Salve o arquivo e teste a configuração.</span><span class="sxs-lookup"><span data-stu-id="e1afb-167">Save the file and test the configuration.</span></span> <span data-ttu-id="e1afb-168">Se tudo passar, a resposta deverá ser `Syntax [OK]`.</span><span class="sxs-lookup"><span data-stu-id="e1afb-168">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="e1afb-169">Reinicie o Apache:</span><span class="sxs-lookup"><span data-stu-id="e1afb-169">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="e1afb-170">Monitorar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="e1afb-170">Monitor the app</span></span>

<span data-ttu-id="e1afb-171">O Apache agora está configurado para encaminhar solicitações feitas a `http://localhost:80` para o aplicativo ASP.NET Core em execução no Kestrel em `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="e1afb-171">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="e1afb-172">No entanto, o Apache não está configurado para gerenciar o processo do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e1afb-172">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="e1afb-173">Use *systemd* e crie um arquivo de serviço para iniciar e monitorar o aplicativo Web subjacente.</span><span class="sxs-lookup"><span data-stu-id="e1afb-173">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="e1afb-174">*systemd* é um sistema de inicialização que fornece muitos recursos poderosos para iniciar, parar e gerenciar processos.</span><span class="sxs-lookup"><span data-stu-id="e1afb-174">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="e1afb-175">Criar o arquivo de serviço</span><span class="sxs-lookup"><span data-stu-id="e1afb-175">Create the service file</span></span>

<span data-ttu-id="e1afb-176">Crie o arquivo de definição de serviço:</span><span class="sxs-lookup"><span data-stu-id="e1afb-176">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="e1afb-177">Eis um exemplo de arquivo de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e1afb-177">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="e1afb-178">No exemplo anterior, o usuário que gerencia o serviço é especificado pela `User` opção.</span><span class="sxs-lookup"><span data-stu-id="e1afb-178">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="e1afb-179">O usuário ( `apache` ) deve existir e ter a propriedade adequada dos arquivos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1afb-179">The user (`apache`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="e1afb-180">Use `TimeoutStopSec` para configurar a duração do tempo de espera para o aplicativo desligar depois de receber o sinal de interrupção inicial.</span><span class="sxs-lookup"><span data-stu-id="e1afb-180">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="e1afb-181">Se o aplicativo não desligar nesse período, o SIGKILL será emitido para encerrá-lo.</span><span class="sxs-lookup"><span data-stu-id="e1afb-181">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="e1afb-182">Forneça o valor como segundos sem unidade (por exemplo, `150`), um valor de duração (por exemplo, `2min 30s`) ou `infinity` para desabilitar o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="e1afb-182">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="e1afb-183">`TimeoutStopSec` é revertido para o valor padrão de `DefaultTimeoutStopSec` no arquivo de configuração do gerenciador (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf* e *user.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="e1afb-183">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="e1afb-184">O tempo limite padrão para a maioria das distribuições é de 90 segundos.</span><span class="sxs-lookup"><span data-stu-id="e1afb-184">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="e1afb-185">Alguns valores (por exemplo, cadeias de conexão de SQL) devem ser escapadas para que os provedores de configuração leiam as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e1afb-185">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="e1afb-186">Use o seguinte comando para gerar um valor corretamente com caracteres de escape para uso no arquivo de configuração:</span><span class="sxs-lookup"><span data-stu-id="e1afb-186">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e1afb-187">Separadores do tipo dois-pontos (`:`) não são compatíveis com nomes de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e1afb-187">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="e1afb-188">Use um sublinhado duplo (`__`) no lugar de dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="e1afb-188">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="e1afb-189">O [provedor de configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converte caracteres de sublinhado duplo em dois-pontos quando as variáveis de ambiente são lidas na configuração.</span><span class="sxs-lookup"><span data-stu-id="e1afb-189">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="e1afb-190">No exemplo a seguir, a chave de cadeia de conexão `ConnectionStrings:DefaultConnection` está definida no arquivo de definição de serviço como `ConnectionStrings__DefaultConnection`:</span><span class="sxs-lookup"><span data-stu-id="e1afb-190">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e1afb-191">Separadores do tipo dois-pontos (`:`) não são compatíveis com nomes de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e1afb-191">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="e1afb-192">Use um sublinhado duplo (`__`) no lugar de dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="e1afb-192">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="e1afb-193">O [provedor de configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables) converte caracteres de sublinhado duplo em dois-pontos quando as variáveis de ambiente são lidas na configuração.</span><span class="sxs-lookup"><span data-stu-id="e1afb-193">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="e1afb-194">No exemplo a seguir, a chave de cadeia de conexão `ConnectionStrings:DefaultConnection` está definida no arquivo de definição de serviço como `ConnectionStrings__DefaultConnection`:</span><span class="sxs-lookup"><span data-stu-id="e1afb-194">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="e1afb-195">Salve o arquivo e habilite o serviço:</span><span class="sxs-lookup"><span data-stu-id="e1afb-195">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="e1afb-196">Inicie o serviço e verifique se ele está em execução:</span><span class="sxs-lookup"><span data-stu-id="e1afb-196">Start the service and verify that it's running:</span></span>

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="e1afb-197">Com o proxy reverso configurado e o Kestrel gerenciado por meio de *systemd*, o aplicativo Web está totalmente configurado e pode ser acessado em um navegador no computador local em `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="e1afb-197">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="e1afb-198">Inspecionando os cabeçalhos de resposta, o cabeçalho **Server** indica que o aplicativo ASP.NET Core é servido pelo Kestrel:</span><span class="sxs-lookup"><span data-stu-id="e1afb-198">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="e1afb-199">Exibir logs</span><span class="sxs-lookup"><span data-stu-id="e1afb-199">View logs</span></span>

<span data-ttu-id="e1afb-200">Já que o aplicativo Web usando Kestrel é gerenciado usando *systemd*, os eventos e os processos são registrados em um diário centralizado.</span><span class="sxs-lookup"><span data-stu-id="e1afb-200">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="e1afb-201">No entanto, esse diário contém todas as entradas para todos os serviços e processos gerenciados por *systemd*.</span><span class="sxs-lookup"><span data-stu-id="e1afb-201">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="e1afb-202">Para exibir os itens específicos de `kestrel-helloapp.service`, use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="e1afb-202">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="e1afb-203">Para filtragem de hora, especifique opções de tempo com o comando.</span><span class="sxs-lookup"><span data-stu-id="e1afb-203">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="e1afb-204">Por exemplo, use `--since today` para filtrar o dia atual ou `--until 1 hour ago` para ver as entradas da hora anterior.</span><span class="sxs-lookup"><span data-stu-id="e1afb-204">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="e1afb-205">Para obter mais informações, confira a [página de manual para journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="e1afb-205">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="e1afb-206">Proteção de dados</span><span class="sxs-lookup"><span data-stu-id="e1afb-206">Data protection</span></span>

<span data-ttu-id="e1afb-207">A [pilha de proteção de dados do ASP.NET Core](xref:security/data-protection/introduction) é usada por vários [middlewares](xref:fundamentals/middleware/index) do ASP.NET Core, incluindo o middleware de autenticação (por exemplo, o middleware de cookie) e as proteções de CSRF (solicitação intersite forjada).</span><span class="sxs-lookup"><span data-stu-id="e1afb-207">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="e1afb-208">Mesmo se as APIs de Proteção de Dados não forem chamadas pelo código do usuário, a proteção de dados deverá ser configurada para criar um [repositório de chaves](xref:security/data-protection/implementation/key-management) criptográfico persistente.</span><span class="sxs-lookup"><span data-stu-id="e1afb-208">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="e1afb-209">Se a proteção de dados não estiver configurada, as chaves serão mantidas na memória e descartadas quando o aplicativo for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="e1afb-209">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="e1afb-210">Se o token de autenticação for armazenado na memória quando o aplicativo for reiniciado:</span><span class="sxs-lookup"><span data-stu-id="e1afb-210">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="e1afb-211">Todos os tokens de autenticação baseados em cookies serão invalidados.</span><span class="sxs-lookup"><span data-stu-id="e1afb-211">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="e1afb-212">Os usuários precisam entrar novamente na próxima solicitação deles.</span><span class="sxs-lookup"><span data-stu-id="e1afb-212">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="e1afb-213">Todos os dados protegidos com o token de autenticação não poderão mais ser descriptografados.</span><span class="sxs-lookup"><span data-stu-id="e1afb-213">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="e1afb-214">Isso pode incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e [ASP.NET Core cookies TempData do MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="e1afb-214">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="e1afb-215">Para configurar a proteção de dados de modo que ela mantenha e criptografe o token de autenticação, consulte:</span><span class="sxs-lookup"><span data-stu-id="e1afb-215">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="e1afb-216">Proteger o aplicativo</span><span class="sxs-lookup"><span data-stu-id="e1afb-216">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="e1afb-217">Configurar o firewall</span><span class="sxs-lookup"><span data-stu-id="e1afb-217">Configure firewall</span></span>

<span data-ttu-id="e1afb-218">*Firewalld* é um daemon dinâmico para gerenciar o firewall com suporte para zonas de rede.</span><span class="sxs-lookup"><span data-stu-id="e1afb-218">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="e1afb-219">Portas e filtragem de pacote ainda podem ser gerenciados pelo iptables.</span><span class="sxs-lookup"><span data-stu-id="e1afb-219">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="e1afb-220">*Firewalld* deve ser instalado por padrão.</span><span class="sxs-lookup"><span data-stu-id="e1afb-220">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="e1afb-221">`yum` pode ser usado para instalar o pacote ou verificar se ele está instalado.</span><span class="sxs-lookup"><span data-stu-id="e1afb-221">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="e1afb-222">Use `firewalld` para abrir somente as portas necessárias para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1afb-222">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="e1afb-223">Nesse caso, as portas 80 e 443 são usadas.</span><span class="sxs-lookup"><span data-stu-id="e1afb-223">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="e1afb-224">Os comandos a seguir definem permanentemente as portas 80 e 443 para estarem abertas:</span><span class="sxs-lookup"><span data-stu-id="e1afb-224">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="e1afb-225">Recarregue as configurações de firewall.</span><span class="sxs-lookup"><span data-stu-id="e1afb-225">Reload the firewall settings.</span></span> <span data-ttu-id="e1afb-226">Verifique os serviços e as portas disponíveis na zona padrão.</span><span class="sxs-lookup"><span data-stu-id="e1afb-226">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="e1afb-227">As opções estão disponíveis por meio da inspeção de `firewall-cmd -h`.</span><span class="sxs-lookup"><span data-stu-id="e1afb-227">Options are available by inspecting `firewall-cmd -h`.</span></span>

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a><span data-ttu-id="e1afb-228">Configuração de HTTPS</span><span class="sxs-lookup"><span data-stu-id="e1afb-228">HTTPS configuration</span></span>

<span data-ttu-id="e1afb-229">**Configurar o aplicativo para conexões seguras (HTTPS) locais**</span><span class="sxs-lookup"><span data-stu-id="e1afb-229">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="e1afb-230">O comando [dotnet run](/dotnet/core/tools/dotnet-run) usa o arquivo *Properties/launchSettings.json* do aplicativo, que configura o aplicativo para escutar nas URLs fornecidas pela propriedade `applicationUrl` (por exemplo, `https://localhost:5001;http://localhost:5000`).</span><span class="sxs-lookup"><span data-stu-id="e1afb-230">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="e1afb-231">Configure o aplicativo para usar um certificado no desenvolvimento para o comando `dotnet run` ou no ambiente de desenvolvimento (F5 ou Ctrl + F5 no Visual Studio Code) usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e1afb-231">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="e1afb-232">[Substituir o certificado padrão da configuração](xref:fundamentals/servers/kestrel#configuration) (*Recomendado*)</span><span class="sxs-lookup"><span data-stu-id="e1afb-232">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="e1afb-233">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="e1afb-233">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="e1afb-234">**Configurar o proxy reverso para conexões de cliente seguras (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="e1afb-234">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="e1afb-235">Para configurar o Apache para HTTPS, o módulo *mod_ssl* é usado.</span><span class="sxs-lookup"><span data-stu-id="e1afb-235">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="e1afb-236">Quando o módulo *httpd* foi instalado, o módulo *mod_ssl* também foi instalado.</span><span class="sxs-lookup"><span data-stu-id="e1afb-236">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="e1afb-237">Se ele não foi instalado, use `yum` para adicioná-lo à configuração.</span><span class="sxs-lookup"><span data-stu-id="e1afb-237">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="e1afb-238">Para impor o HTTPS, instale o módulo `mod_rewrite` para habilitar a regravação de URL:</span><span class="sxs-lookup"><span data-stu-id="e1afb-238">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="e1afb-239">Modifique o arquivo *helloapp.conf* para habilitar a regravação de URL e proteger a comunicação na porta 443:</span><span class="sxs-lookup"><span data-stu-id="e1afb-239">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="e1afb-240">Este exemplo usa um certificado gerado localmente.</span><span class="sxs-lookup"><span data-stu-id="e1afb-240">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="e1afb-241">**SSLCertificateFile** deve ser o arquivo de certificado primário para o nome de domínio.</span><span class="sxs-lookup"><span data-stu-id="e1afb-241">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="e1afb-242">**SSLCertificateKeyFile** deve ser o arquivo de chave gerado quando o CSR é criado.</span><span class="sxs-lookup"><span data-stu-id="e1afb-242">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="e1afb-243">**SSLCertificateChainFile** deve ser o arquivo de certificado intermediário (se houver) fornecido pela autoridade de certificação.</span><span class="sxs-lookup"><span data-stu-id="e1afb-243">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="e1afb-244">Salve o arquivo e teste a configuração:</span><span class="sxs-lookup"><span data-stu-id="e1afb-244">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="e1afb-245">Reinicie o Apache:</span><span class="sxs-lookup"><span data-stu-id="e1afb-245">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="e1afb-246">Sugestões adicionais do Apache</span><span class="sxs-lookup"><span data-stu-id="e1afb-246">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="e1afb-247">Reiniciar aplicativos com atualizações de estrutura compartilhadas</span><span class="sxs-lookup"><span data-stu-id="e1afb-247">Restart apps with shared framework updates</span></span>

<span data-ttu-id="e1afb-248">Depois de atualizar a estrutura compartilhada no servidor, reinicie o ASP.NET Core aplicativos hospedados pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="e1afb-248">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="e1afb-249">Cabeçalhos adicionais</span><span class="sxs-lookup"><span data-stu-id="e1afb-249">Additional headers</span></span>

<span data-ttu-id="e1afb-250">Para proteção contra ataques mal-intencionados, há alguns cabeçalhos que devem ser modificados ou adicionados.</span><span class="sxs-lookup"><span data-stu-id="e1afb-250">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="e1afb-251">Verifique se o módulo `mod_headers` está instalado:</span><span class="sxs-lookup"><span data-stu-id="e1afb-251">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="e1afb-252">Proteger o Apache contra ataques de clickjacking</span><span class="sxs-lookup"><span data-stu-id="e1afb-252">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="e1afb-253">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), também conhecido como um *ataque por inferência na interface do usuário*, é um ataque mal-intencionado em que o visitante do site é levado a clicar em um link ou botão em uma página diferente daquela que está visitando atualmente.</span><span class="sxs-lookup"><span data-stu-id="e1afb-253">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="e1afb-254">Use `X-FRAME-OPTIONS` para proteger o site.</span><span class="sxs-lookup"><span data-stu-id="e1afb-254">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="e1afb-255">Para atenuar ataques de clickjacking:</span><span class="sxs-lookup"><span data-stu-id="e1afb-255">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="e1afb-256">Edite o arquivo *httpd.conf*:</span><span class="sxs-lookup"><span data-stu-id="e1afb-256">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="e1afb-257">Adicione a linha `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span><span class="sxs-lookup"><span data-stu-id="e1afb-257">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="e1afb-258">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="e1afb-258">Save the file.</span></span>
1. <span data-ttu-id="e1afb-259">Reinicie o Apache.</span><span class="sxs-lookup"><span data-stu-id="e1afb-259">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="e1afb-260">Detecção de tipo MIME</span><span class="sxs-lookup"><span data-stu-id="e1afb-260">MIME-type sniffing</span></span>

<span data-ttu-id="e1afb-261">O cabeçalho `X-Content-Type-Options` impedirá o Internet Explorer de *farejar por MIME* (determinar o `Content-Type` de um arquivo com base no conteúdo do arquivo).</span><span class="sxs-lookup"><span data-stu-id="e1afb-261">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="e1afb-262">Se o servidor define o cabeçalho `Content-Type` para `text/html` com a opção `nosniff` definida, o Internet Explorer renderiza o conteúdo como `text/html`, independentemente do conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="e1afb-262">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="e1afb-263">Edite o arquivo *httpd.conf*:</span><span class="sxs-lookup"><span data-stu-id="e1afb-263">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="e1afb-264">Adicione a linha `Header set X-Content-Type-Options "nosniff"`.</span><span class="sxs-lookup"><span data-stu-id="e1afb-264">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="e1afb-265">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="e1afb-265">Save the file.</span></span> <span data-ttu-id="e1afb-266">Reinicie o Apache.</span><span class="sxs-lookup"><span data-stu-id="e1afb-266">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="e1afb-267">Balanceamento de Carga</span><span class="sxs-lookup"><span data-stu-id="e1afb-267">Load Balancing</span></span>

<span data-ttu-id="e1afb-268">Este exemplo mostra como instalar e configurar o Apache no CentOS 7 e no Kestrel no mesmo computador da instância.</span><span class="sxs-lookup"><span data-stu-id="e1afb-268">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="e1afb-269">Para não ter um ponto único de falha, o uso de *mod_proxy_balancer* e a modificação do **VirtualHost** permitiriam o gerenciamento de várias instâncias dos aplicativos Web protegidos pelo servidor proxy do Apache.</span><span class="sxs-lookup"><span data-stu-id="e1afb-269">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="e1afb-270">No arquivo de configuração mostrado abaixo, uma instância adicional do `helloapp` é configurada para ser executada na porta 5001.</span><span class="sxs-lookup"><span data-stu-id="e1afb-270">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="e1afb-271">A seção *Proxy* é definida com uma configuração de balanceador com dois membros para balancear carga de *byrequests*.</span><span class="sxs-lookup"><span data-stu-id="e1afb-271">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="e1afb-272">Limites de taxa</span><span class="sxs-lookup"><span data-stu-id="e1afb-272">Rate Limits</span></span>

<span data-ttu-id="e1afb-273">Usando *mod_ratelimit*, que está incluído no módulo *httpd*, a largura de banda de clientes pode ser limitada:</span><span class="sxs-lookup"><span data-stu-id="e1afb-273">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="e1afb-274">O arquivo de exemplo limita a largura de banda a 600 KB/s no local raiz:</span><span class="sxs-lookup"><span data-stu-id="e1afb-274">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="e1afb-275">Campos de cabeçalho da solicitação muito grandes</span><span class="sxs-lookup"><span data-stu-id="e1afb-275">Long request header fields</span></span>

<span data-ttu-id="e1afb-276">As configurações padrão do servidor proxy normalmente limitam os campos de cabeçalho de solicitação a 8.190 bytes.</span><span class="sxs-lookup"><span data-stu-id="e1afb-276">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="e1afb-277">Um aplicativo pode exigir campos maiores do que o padrão (por exemplo, aplicativos que usam [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span><span class="sxs-lookup"><span data-stu-id="e1afb-277">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="e1afb-278">Se forem necessários campos mais longos, a diretiva [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) do servidor proxy exigirá ajuste.</span><span class="sxs-lookup"><span data-stu-id="e1afb-278">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="e1afb-279">O valor a ser aplicado depende do cenário.</span><span class="sxs-lookup"><span data-stu-id="e1afb-279">The value to apply depends on the scenario.</span></span> <span data-ttu-id="e1afb-280">Para obter mais informações, confira a documentação do servidor.</span><span class="sxs-lookup"><span data-stu-id="e1afb-280">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="e1afb-281">Não aumente o valor padrão de `LimitRequestFieldSize` a menos que necessário.</span><span class="sxs-lookup"><span data-stu-id="e1afb-281">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="e1afb-282">Aumentar esse valor aumenta o risco de estouro de buffer (estouro) e ataques de DoS (negação de serviço) por usuários mal-intencionados.</span><span class="sxs-lookup"><span data-stu-id="e1afb-282">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1afb-283">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e1afb-283">Additional resources</span></span>

* [<span data-ttu-id="e1afb-284">Pré-requisitos para o .NET Core no Linux</span><span class="sxs-lookup"><span data-stu-id="e1afb-284">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
