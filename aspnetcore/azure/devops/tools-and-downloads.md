---
title: Ferramentas e downloads-DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Ferramentas e downloads necessários para o DevOps com o ASP.NET Core e o Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: ed8aee214ff9b9e941aeea01887882c3bdfc56a7
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400291"
---
# <a name="tools-and-downloads"></a>Ferramentas e downloads

O Azure tem várias interfaces para provisionar e gerenciar recursos, como o [portal do Azure](https://portal.azure.com), [CLI do Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure cloud Shell](https://shell.azure.com/bash)e Visual Studio. Este guia usa uma abordagem de solução de dutos e utiliza o Azure Cloud Shell sempre que possível para reduzir as etapas necessárias. No entanto, o portal do Azure deve ser usado para algumas partes.

## <a name="prerequisites"></a>Pré-requisitos

As seguintes assinaturas são necessárias:

* Azure &mdash; se você não tiver uma conta, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/dotnet/).
* Azure DevOps Services &mdash; sua assinatura e organização do DevOps do Azure é criada no capítulo 4.
* GitHub &mdash; se você não tiver uma conta, [Inscreva-se gratuitamente](https://github.com/join).

As seguintes ferramentas são necessárias:

* [Git](https://git-scm.com/downloads) &mdash; Uma compreensão fundamental do git é recomendada para este guia. Examine a [documentação do git](https://git-scm.com/doc), especificamente [git remoto](https://git-scm.com/docs/git-remote) e [git Push](https://git-scm.com/docs/git-push).
* [SDK do .NET Core](https://dotnet.microsoft.com/download/) &mdash; A versão 2.1.300 ou posterior é necessária para compilar e executar o aplicativo de exemplo. Se o Visual Studio estiver instalado com a carga de trabalho de **desenvolvimento de plataforma cruzada do .NET Core** , o SDK do .NET Core já estará instalado.

    Verifique a instalação do SDK do .NET Core. Abra um shell de comando e execute o seguinte comando:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Ferramentas recomendadas (somente Windows)

* As ferramentas robustas do Azure do [Visual Studio](https://visualstudio.microsoft.com)fornecem uma GUI para a maioria das funcionalidades descritas neste guia. Qualquer edição do Visual Studio funcionará, incluindo o Visual Studio Community Edition gratuito. Os tutoriais são escritos para demonstrar desenvolvimento, implantação e DevOps com e sem o Visual Studio.

  Confirme se o Visual Studio tem as seguintes [cargas de trabalho](/visualstudio/install/modify-visual-studio) instaladas:

  * Desenvolvimento Web e ASP.NET
  * Desenvolvimento do Azure
  * Desenvolvimento multiplataforma com o .NET Core
