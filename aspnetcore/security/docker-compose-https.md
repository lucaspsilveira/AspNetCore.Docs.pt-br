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
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Hospedagem ASP.NET Imagens Core com Docker Compose over HTTPS


ASP.NET Core usa [HTTPS por padrão](/aspnet/core/security/enforcing-ssl). [O HTTPS](https://en.wikipedia.org/wiki/HTTPS) conta com [certificados](https://en.wikipedia.org/wiki/Public_key_certificate) de confiança, identidade e criptografia.

Este documento explica como executar imagens de contêiner pré-construídas com HTTPS.

Consulte [O Desenvolvimento de aplicativos principais ASP.NET com o Docker sobre HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) para cenários de desenvolvimento.

Esta amostra requer [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) ou posterior do [cliente Docker](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Pré-requisitos

O [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) ou posterior é necessário para algumas das instruções deste documento.

## <a name="certificates"></a>Certificados

Um certificado de uma [autoridade de certificado](https://wikipedia.org/wiki/Certificate_authority) é necessário para [hospedagem de produção](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) para um domínio. [Let's Encrypt](https://letsencrypt.org/)é uma autoridade de certificados que oferece certificados gratuitos.

Este documento usa [certificados de desenvolvimento auto-assinados](https://wikipedia.org/wiki/Self-signed_certificate) para hospedar imagens pré-construídas em vez `localhost`de . As instruções são semelhantes ao uso de certificados de produção.

Para certificados de produção:

* A `dotnet dev-certs` ferramenta não é necessária.
* Os certificados não precisam ser armazenados no local utilizado nas instruções. Armazene os certificados em qualquer local fora do diretório do site.

As instruções contidas nos certificados de montagem `volumes` de volume da seção a seguir em recipientes usando a propriedade em *docker-compose.yml.* Você pode adicionar certificados em `COPY` imagens de contêiner com um comando em um *arquivo Docker,* mas não é recomendado. Copiar certificados em uma imagem não é recomendado pelas seguintes razões:

* Isso dificulta o uso da mesma imagem para testes com certificados de desenvolvedor.
* Isso dificulta o uso da mesma imagem para hospedagem com certificados de produção.
* Há um risco significativo de divulgação do certificado.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Iniciar um contêiner com suporte https usando docker compor

Use as seguintes instruções para a configuração do sistema operacional.

### <a name="windows-using-linux-containers"></a>Windows usando contêineres Linux

Gerar certificado e configurar máquina local:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Nos comandos anteriores, substitua por `{ password here }` uma senha.

Crie um arquivo _docker-compose.debug.yml_ com o seguinte conteúdo:

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
A senha especificada no arquivo de composição do docker deve corresponder à senha usada para o certificado.

Inicie o contêiner com ASP.NET Core configurado para HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS ou Linux

Gerar certificado e configurar máquina local:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`só é suportado no macOS e Windows. Você precisa confiar em certificados no Linux da maneira que é suportado pela sua distro. É provável que você precise confiar no certificado em seu navegador.

Nos comandos anteriores, substitua por `{ password here }` uma senha.

Crie um arquivo _docker-compose.debug.yml_ com o seguinte conteúdo:

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
A senha especificada no arquivo de composição do docker deve corresponder à senha usada para o certificado.

Inicie o contêiner com ASP.NET Core configurado para HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Windows usando recipientes windows

Gerar certificado e configurar máquina local:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Nos comandos anteriores, substitua por `{ password here }` uma senha.

Crie um arquivo _docker-compose.debug.yml_ com o seguinte conteúdo:

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
A senha especificada no arquivo de composição do docker deve corresponder à senha usada para o certificado.

Inicie o contêiner com ASP.NET Core configurado para HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
