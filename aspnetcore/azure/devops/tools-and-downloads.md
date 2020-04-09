---
title: Ferramentas e downloads - DevOps com ASP.NET Core e Azure
author: CamSoper
description: Ferramentas e downloads necessários para DevOps com ASP.NET Core e Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511139"
---
# <a name="tools-and-downloads"></a>Ferramentas e downloads

O Azure possui diversas interfaces para provisionamento e gerenciamento de recursos, como o [portal Azure](https://portal.azure.com), [Azure CLI,](/cli/azure/) [Azure PowerShell,](/powershell/azure/overview) [Azure Cloud Shell](https://shell.azure.com/bash)e Visual Studio. Este guia adota uma abordagem minimalista e usa o Azure Cloud Shell sempre que possível para reduzir as etapas necessárias. No entanto, o portal Azure deve ser usado para algumas porções.

## <a name="prerequisites"></a>Pré-requisitos

As seguintes assinaturas são necessárias:

* Azure &mdash; Se você não tem uma conta, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).
* Azure DevOps &mdash; Services sua assinatura e organização Do Azure DevOps é criada no Capítulo 4.
* GitHub &mdash; Se você não tiver uma conta, [inscreva-se gratuitamente](https://github.com/join).

As seguintes ferramentas são necessárias:

* [Git](https://git-scm.com/downloads) &mdash; Uma compreensão fundamental de Git é recomendada para este guia. Revise a documentação do [Git,](https://git-scm.com/doc)especificamente [git remote](https://git-scm.com/docs/git-remote) e [git push](https://git-scm.com/docs/git-push).
* [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Versão 2.1.300 ou posterior é necessária para construir e executar o aplicativo de amostra. Se o Visual Studio estiver instalado com a carga **de trabalho de desenvolvimento multiplataforma .NET Core,** o .NET Core SDK já está instalado.

    Verifique a instalação do .NET Core SDK. Abra uma camada de comando e execute o seguinte comando:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Ferramentas recomendadas (somente Windows)

* [As](https://visualstudio.microsoft.com)ferramentas robustas do Azure do Visual Studio fornecem uma GUI para a maior parte das funcionalidades descritas neste guia. Qualquer edição do Visual Studio funcionará, incluindo o Visual Studio Community Edition gratuito. Os tutoriais são escritos para demonstrar desenvolvimento, implantação e DevOps com e sem visual studio.

  Confirme se o Visual Studio tem as [seguintes cargas de trabalho instaladas:](/visualstudio/install/modify-visual-studio)

  * Desenvolvimento Web e ASP.NET
  * Desenvolvimento do Azure
  * Desenvolvimento multiplataforma com o .NET Core
