---
title: Hospedagem ASP.NET imagem Core em contêiner usando docker compor com HTTPS
author: ravipal
description: Saiba como hospedar ASP.NET Imagens Principais com o Docker Compose sobre HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Let's Encrypt
uid: security/docker-compose-https
ms.openlocfilehash: 616ccf906e98534ffda08c0c2b6d0a171f063cc1
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381820"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="e6860-103">Hospedagem ASP.NET Imagens Core com Docker Compose over HTTPS</span><span class="sxs-lookup"><span data-stu-id="e6860-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="e6860-104">ASP.NET Core usa [HTTPS por padrão](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="e6860-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="e6860-105">[O HTTPS](https://en.wikipedia.org/wiki/HTTPS) conta com [certificados](https://en.wikipedia.org/wiki/Public_key_certificate) de confiança, identidade e criptografia.</span><span class="sxs-lookup"><span data-stu-id="e6860-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="e6860-106">Este documento explica como executar imagens de contêiner pré-construídas com HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e6860-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="e6860-107">Consulte [O Desenvolvimento de aplicativos principais ASP.NET com o Docker sobre HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e6860-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="e6860-108">Esta amostra requer [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) ou posterior do [cliente Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="e6860-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e6860-109">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e6860-109">Prerequisites</span></span>

<span data-ttu-id="e6860-110">O [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) ou posterior é necessário para algumas das instruções deste documento.</span><span class="sxs-lookup"><span data-stu-id="e6860-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="e6860-111">Certificados</span><span class="sxs-lookup"><span data-stu-id="e6860-111">Certificates</span></span>

<span data-ttu-id="e6860-112">Um certificado de uma [autoridade de certificado](https://wikipedia.org/wiki/Certificate_authority) é necessário para [hospedagem de produção](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) para um domínio.</span><span class="sxs-lookup"><span data-stu-id="e6860-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="e6860-113">[Let's Encrypt](https://letsencrypt.org/)é uma autoridade de certificados que oferece certificados gratuitos.</span><span class="sxs-lookup"><span data-stu-id="e6860-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="e6860-114">Este documento usa [certificados de desenvolvimento auto-assinados](https://wikipedia.org/wiki/Self-signed_certificate) para hospedar imagens pré-construídas em vez `localhost`de .</span><span class="sxs-lookup"><span data-stu-id="e6860-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="e6860-115">As instruções são semelhantes ao uso de certificados de produção.</span><span class="sxs-lookup"><span data-stu-id="e6860-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="e6860-116">Para certificados de produção:</span><span class="sxs-lookup"><span data-stu-id="e6860-116">For production certificates:</span></span>

* <span data-ttu-id="e6860-117">A `dotnet dev-certs` ferramenta não é necessária.</span><span class="sxs-lookup"><span data-stu-id="e6860-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="e6860-118">Os certificados não precisam ser armazenados no local utilizado nas instruções.</span><span class="sxs-lookup"><span data-stu-id="e6860-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="e6860-119">Armazene os certificados em qualquer local fora do diretório do site.</span><span class="sxs-lookup"><span data-stu-id="e6860-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="e6860-120">As instruções contidas nos certificados de montagem `volumes` de volume da seção a seguir em recipientes usando a propriedade em *docker-compose.yml.*</span><span class="sxs-lookup"><span data-stu-id="e6860-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="e6860-121">Você pode adicionar certificados em `COPY` imagens de contêiner com um comando em um *arquivo Docker,* mas não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="e6860-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="e6860-122">Copiar certificados em uma imagem não é recomendado pelas seguintes razões:</span><span class="sxs-lookup"><span data-stu-id="e6860-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="e6860-123">Isso dificulta o uso da mesma imagem para testes com certificados de desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="e6860-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="e6860-124">Isso dificulta o uso da mesma imagem para hospedagem com certificados de produção.</span><span class="sxs-lookup"><span data-stu-id="e6860-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="e6860-125">Há um risco significativo de divulgação do certificado.</span><span class="sxs-lookup"><span data-stu-id="e6860-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="e6860-126">Iniciar um contêiner com suporte https usando docker compor</span><span class="sxs-lookup"><span data-stu-id="e6860-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="e6860-127">Use as seguintes instruções para a configuração do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="e6860-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="e6860-128">Windows usando contêineres Linux</span><span class="sxs-lookup"><span data-stu-id="e6860-128">Windows using Linux containers</span></span>

<span data-ttu-id="e6860-129">Gerar certificado e configurar máquina local:</span><span class="sxs-lookup"><span data-stu-id="e6860-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="e6860-130">Nos comandos anteriores, substitua por `{ password here }` uma senha.</span><span class="sxs-lookup"><span data-stu-id="e6860-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="e6860-131">Crie um arquivo _docker-compose.debug.yml_ com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="e6860-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="e6860-132">A senha especificada no arquivo de composição do docker deve corresponder à senha usada para o certificado.</span><span class="sxs-lookup"><span data-stu-id="e6860-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="e6860-133">Inicie o contêiner com ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="e6860-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="e6860-134">macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="e6860-134">macOS or Linux</span></span>

<span data-ttu-id="e6860-135">Gerar certificado e configurar máquina local:</span><span class="sxs-lookup"><span data-stu-id="e6860-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="e6860-136">`dotnet dev-certs https --trust`só é suportado no macOS e Windows.</span><span class="sxs-lookup"><span data-stu-id="e6860-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="e6860-137">Você precisa confiar em certificados no Linux da maneira que é suportado pela sua distro.</span><span class="sxs-lookup"><span data-stu-id="e6860-137">You need to trust certificates on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="e6860-138">É provável que você precise confiar no certificado em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="e6860-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="e6860-139">Nos comandos anteriores, substitua por `{ password here }` uma senha.</span><span class="sxs-lookup"><span data-stu-id="e6860-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="e6860-140">Crie um arquivo _docker-compose.debug.yml_ com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="e6860-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="e6860-141">A senha especificada no arquivo de composição do docker deve corresponder à senha usada para o certificado.</span><span class="sxs-lookup"><span data-stu-id="e6860-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="e6860-142">Inicie o contêiner com ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="e6860-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="e6860-143">Windows usando recipientes windows</span><span class="sxs-lookup"><span data-stu-id="e6860-143">Windows using Windows containers</span></span>

<span data-ttu-id="e6860-144">Gerar certificado e configurar máquina local:</span><span class="sxs-lookup"><span data-stu-id="e6860-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="e6860-145">Nos comandos anteriores, substitua por `{ password here }` uma senha.</span><span class="sxs-lookup"><span data-stu-id="e6860-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="e6860-146">Crie um arquivo _docker-compose.debug.yml_ com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="e6860-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="e6860-147">A senha especificada no arquivo de composição do docker deve corresponder à senha usada para o certificado.</span><span class="sxs-lookup"><span data-stu-id="e6860-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="e6860-148">Inicie o contêiner com ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="e6860-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
