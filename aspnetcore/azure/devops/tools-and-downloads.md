---
title: Ferramentas e downloads-DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Ferramentas e downloads necessários para o DevOps com o ASP.NET Core e o Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 321d506760d057914136f77c15e85043fa9d9832
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766531"
---
# <a name="tools-and-downloads"></a>Ferramentas e downloads

O Azure tem várias interfaces para provisionar e gerenciar recursos, como o [portal do Azure](https://portal.azure.com), [CLI do Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure cloud Shell](https://shell.azure.com/bash)e Visual Studio. Este guia usa uma abordagem de solução de dutos e utiliza o Azure Cloud Shell sempre que possível para reduzir as etapas necessárias. No entanto, o portal do Azure deve ser usado para algumas partes.

## <a name="prerequisites"></a>Pré-requisitos

As seguintes assinaturas são necessárias:

* Azure &mdash; se você não tiver uma conta, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).
* Azure DevOps Services &mdash; sua assinatura e organização do DevOps do Azure é criada no capítulo 4.
* GitHub &mdash; se você não tiver uma conta, [Inscreva-se gratuitamente](https://github.com/join).

As seguintes ferramentas são necessárias:

* O [git](https://git-scm.com/downloads) &mdash; , uma compreensão fundamental do git, é recomendável para este guia. Examine a [documentação do git](https://git-scm.com/doc), especificamente [git remoto](https://git-scm.com/docs/git-remote) e [git Push](https://git-scm.com/docs/git-push).
* [SDK do .NET Core](https://dotnet.microsoft.com/download/) &mdash; versão 2.1.300 ou posterior é necessária para compilar e executar o aplicativo de exemplo. Se o Visual Studio estiver instalado com a carga de trabalho de **desenvolvimento de plataforma cruzada do .NET Core** , o SDK do .NET Core já estará instalado.

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
