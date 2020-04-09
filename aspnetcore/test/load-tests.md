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
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="49219-103">teste de carga/estresse do núcleo ASP.NET</span><span class="sxs-lookup"><span data-stu-id="49219-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="49219-104">Testes de carga e teste de estresse são importantes para garantir que um aplicativo web seja executável e escalável.</span><span class="sxs-lookup"><span data-stu-id="49219-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="49219-105">Seus objetivos são diferentes, embora muitas vezes compartilhem testes semelhantes.</span><span class="sxs-lookup"><span data-stu-id="49219-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="49219-106">**Testes de** &ndash; carga Teste se o aplicativo pode lidar com uma carga especificada de usuários para um determinado cenário enquanto ainda satisfaz a meta de resposta.</span><span class="sxs-lookup"><span data-stu-id="49219-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="49219-107">O aplicativo é executado em condições normais.</span><span class="sxs-lookup"><span data-stu-id="49219-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="49219-108">**Testes de** &ndash; estresse Teste a estabilidade do aplicativo ao correr em condições extremas, muitas vezes por um longo período de tempo.</span><span class="sxs-lookup"><span data-stu-id="49219-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="49219-109">Os testes colocam alta carga do usuário, seja picos ou aumentando gradualmente a carga, no aplicativo, ou limitam os recursos de computação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="49219-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="49219-110">Testes de estresse determinam se um aplicativo sob estresse pode se recuperar da falha e retornar graciosamente ao comportamento esperado.</span><span class="sxs-lookup"><span data-stu-id="49219-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="49219-111">Sob estresse, o aplicativo não é executado em condições normais.</span><span class="sxs-lookup"><span data-stu-id="49219-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="49219-112">Visual Studio 2019 é a última versão do Visual Studio com recursos de teste de carga.</span><span class="sxs-lookup"><span data-stu-id="49219-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="49219-113">Para clientes que precisam de ferramentas de teste de carga no futuro, recomendamos ferramentas alternativas, como Apache JMeter, Akamai CloudTest e BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="49219-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="49219-114">Para obter mais informações, consulte as Notas de Lançamento do [Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="49219-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="49219-115">Ferramentas do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49219-115">Visual Studio tools</span></span>

<span data-ttu-id="49219-116">O Visual Studio permite que os usuários criem, desenvolvam e decemcedam testes de desempenho da Web e carga.</span><span class="sxs-lookup"><span data-stu-id="49219-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="49219-117">Uma opção está disponível para criar testes gravando ações em um navegador da Web.</span><span class="sxs-lookup"><span data-stu-id="49219-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="49219-118">Para obter informações sobre como criar, configurar e executar um projeto de teste de carga usando o Visual Studio 2017, consulte [Quickstart: Crie um projeto de teste de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="49219-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="49219-119">Os testes de carga podem ser configurados para executar no local ou executados na nuvem usando DevOps Azure.</span><span class="sxs-lookup"><span data-stu-id="49219-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="49219-120">Ferramentas de terceiros</span><span class="sxs-lookup"><span data-stu-id="49219-120">Third-party tools</span></span>

<span data-ttu-id="49219-121">A lista a seguir contém ferramentas de desempenho da Web de terceiros com vários conjuntos de recursos:</span><span class="sxs-lookup"><span data-stu-id="49219-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="49219-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="49219-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="49219-123">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="49219-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="49219-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="49219-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="49219-125">k6</span><span class="sxs-lookup"><span data-stu-id="49219-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="49219-126">Locust</span><span class="sxs-lookup"><span data-stu-id="49219-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="49219-127">WebSurge do Vento Ocidental</span><span class="sxs-lookup"><span data-stu-id="49219-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="49219-128">Netling</span><span class="sxs-lookup"><span data-stu-id="49219-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="49219-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="49219-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

