---
title: Hospedagem de ASP.NET Core imagem no contêiner usando o Docker Compose com HTTPS
author: ravipal
description: Saiba como hospedar ASP.NET Core imagens com Docker Compose por HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/docker-compose-https
ms.openlocfilehash: 533d86fb17e3c89fdca59685b090645a11ba5473
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775135"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Hospedando ASP.NET Core imagens com Docker Compose via HTTPS


O ASP.NET Core usa [https por padrão](/aspnet/core/security/enforcing-ssl). O [https](https://en.wikipedia.org/wiki/HTTPS) depende de [certificados](https://en.wikipedia.org/wiki/Public_key_certificate) para confiança, identidade e criptografia.

Este documento explica como executar imagens de contêiner predefinidas com HTTPS.

Consulte [desenvolvendo aplicativos ASP.NET Core com o Docker sobre HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) para cenários de desenvolvimento.

Este exemplo requer o [docker 17, 6](https://docs.docker.com/release-notes/docker-ce) ou posterior do [cliente do Docker](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Pré-requisitos

O [SDK do .NET Core 2,2](https://dotnet.microsoft.com/download) ou posterior é necessário para algumas das instruções neste documento.

## <a name="certificates"></a>Certificados

Um certificado de uma [autoridade de certificação](https://wikipedia.org/wiki/Certificate_authority) é necessário para [Hospedagem de produção](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) para um domínio. [Let's Encrypt](https://letsencrypt.org/)é uma autoridade de certificação que oferece certificados gratuitos.

Este documento usa [certificados de desenvolvimento autoassinados](https://wikipedia.org/wiki/Self-signed_certificate) para hospedar imagens predefinidas `localhost`. As instruções são semelhantes ao uso de certificados de produção.

Para certificados de produção:

* A `dotnet dev-certs` ferramenta não é necessária.
* Os certificados não precisam ser armazenados no local usado nas instruções. Armazene os certificados em qualquer local fora do diretório do site.

As instruções contidas na seção a seguir montam os certificados de montagem `volumes` em contêineres usando a propriedade em *Docker-Compose. yml.* Você pode adicionar certificados em imagens de contêiner com `COPY` um comando em um *Dockerfile*, mas isso não é recomendado. A cópia de certificados em uma imagem não é recomendada pelos seguintes motivos:

* Isso dificulta o uso da mesma imagem para teste com certificados de desenvolvedor.
* Isso dificulta o uso da mesma imagem para hospedagem com certificados de produção.
* Há um risco significativo de divulgação de certificado.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Iniciando um contêiner com suporte a https usando o Docker Compose

Use as instruções a seguir para a configuração do seu sistema operacional.

### <a name="windows-using-linux-containers"></a>Windows usando contêineres do Linux

Gerar certificado e configurar computador local:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Nos comandos anteriores, substitua `{ password here }` por uma senha.

Crie um arquivo _Docker-Compose. Debug. yml_ com o seguinte conteúdo:

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
A senha especificada no arquivo Docker Compose deve corresponder à senha usada para o certificado.

Inicie o contêiner com ASP.NET Core configurado para HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS ou Linux

Gerar certificado e configurar computador local:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`Só tem suporte no macOS e no Windows. Você precisa confiar em certificados no Linux na forma com que o seu distribuição dá suporte. É provável que você precise confiar no certificado em seu navegador.

Nos comandos anteriores, substitua `{ password here }` por uma senha.

Crie um arquivo _Docker-Compose. Debug. yml_ com o seguinte conteúdo:

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
A senha especificada no arquivo Docker Compose deve corresponder à senha usada para o certificado.

Inicie o contêiner com ASP.NET Core configurado para HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Windows usando contêineres do Windows

Gerar certificado e configurar computador local:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Nos comandos anteriores, substitua `{ password here }` por uma senha.

Crie um arquivo _Docker-Compose. Debug. yml_ com o seguinte conteúdo:

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
A senha especificada no arquivo Docker Compose deve corresponder à senha usada para o certificado.

Inicie o contêiner com ASP.NET Core configurado para HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
