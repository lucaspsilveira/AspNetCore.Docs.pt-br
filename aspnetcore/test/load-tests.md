---
title: teste de carga/estresse do núcleo ASP.NET
author: Jeremy-Meng
description: Conheça várias ferramentas e abordagens notáveis para testes de carga e testes de estresse ASP.NET aplicativos Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 1fd77a767fb53b9276081dd712e13108094a0382
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664685"
---
# <a name="aspnet-core-loadstress-testing"></a>teste de carga/estresse do núcleo ASP.NET

Testes de carga e teste de estresse são importantes para garantir que um aplicativo web seja executável e escalável. Seus objetivos são diferentes, embora muitas vezes compartilhem testes semelhantes.

**Testes de** &ndash; carga Teste se o aplicativo pode lidar com uma carga especificada de usuários para um determinado cenário enquanto ainda satisfaz a meta de resposta. O aplicativo é executado em condições normais.

**Testes de** &ndash; estresse Teste a estabilidade do aplicativo ao correr em condições extremas, muitas vezes por um longo período de tempo. Os testes colocam alta carga do usuário, seja picos ou aumentando gradualmente a carga, no aplicativo, ou limitam os recursos de computação do aplicativo.

Testes de estresse determinam se um aplicativo sob estresse pode se recuperar da falha e retornar graciosamente ao comportamento esperado. Sob estresse, o aplicativo não é executado em condições normais.

Visual Studio 2019 é a última versão do Visual Studio com recursos de teste de carga. Para clientes que precisam de ferramentas de teste de carga no futuro, recomendamos ferramentas alternativas, como Apache JMeter, Akamai CloudTest e BlazeMeter. Para obter mais informações, consulte as Notas de Lançamento do [Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Ferramentas do Visual Studio

O Visual Studio permite que os usuários criem, desenvolvam e decemcedam testes de desempenho da Web e carga. Uma opção está disponível para criar testes gravando ações em um navegador da Web.

Para obter informações sobre como criar, configurar e executar um projeto de teste de carga usando o Visual Studio 2017, consulte [Quickstart: Crie um projeto de teste de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Os testes de carga podem ser configurados para executar no local ou executados na nuvem usando DevOps Azure.

## <a name="third-party-tools"></a>Ferramentas de terceiros

A lista a seguir contém ferramentas de desempenho da Web de terceiros com vários conjuntos de recursos:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [WebSurge do Vento Ocidental](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

