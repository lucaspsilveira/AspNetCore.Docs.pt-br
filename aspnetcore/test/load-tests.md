---
title: ASP.NET Core teste de carga/estresse
author: Jeremy-Meng
description: Saiba mais sobre várias ferramentas e abordagens notáveis para teste de carga e teste de estresse ASP.NET Core aplicativos.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: f0c930a7689c7288a58cfc3ca630d3cd324bf2cd
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106800"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="eebc5-103">ASP.NET Core teste de carga/estresse</span><span class="sxs-lookup"><span data-stu-id="eebc5-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="eebc5-104">Testes de carga e testes de estresse são importantes para garantir que um aplicativo Web seja eficaz e escalonável.</span><span class="sxs-lookup"><span data-stu-id="eebc5-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="eebc5-105">Suas metas são diferentes, embora elas geralmente compartilhem testes semelhantes.</span><span class="sxs-lookup"><span data-stu-id="eebc5-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="eebc5-106">**Testes de carga**: teste se o aplicativo pode tratar de uma carga especificada de usuários para um determinado cenário e ainda atender à meta de resposta.</span><span class="sxs-lookup"><span data-stu-id="eebc5-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="eebc5-107">O aplicativo é executado sob condições normais.</span><span class="sxs-lookup"><span data-stu-id="eebc5-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="eebc5-108">**Testes de estresse**: teste a estabilidade do aplicativo ao executar sob condições extremas, geralmente por um longo período de tempo.</span><span class="sxs-lookup"><span data-stu-id="eebc5-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="eebc5-109">Os testes colocam alta carga de usuário, picos ou aumentam a carga gradualmente, no aplicativo, ou limitam os recursos de computação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eebc5-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="eebc5-110">Testes de estresse determinam se um aplicativo sob estresse pode se recuperar de uma falha e retornar normalmente para o comportamento esperado.</span><span class="sxs-lookup"><span data-stu-id="eebc5-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="eebc5-111">Sob estresse, o aplicativo não é executado sob condições normais.</span><span class="sxs-lookup"><span data-stu-id="eebc5-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="eebc5-112">O Visual Studio 2019 é a última versão do Visual Studio com recursos de teste de carga.</span><span class="sxs-lookup"><span data-stu-id="eebc5-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="eebc5-113">Para clientes que precisam de ferramentas de teste de carga no futuro, recomendamos ferramentas alternativas, como Apache JMeter, Akamai CloudTest e BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="eebc5-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="eebc5-114">Para obter mais informações, consulte as [notas de versão do Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="eebc5-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="eebc5-115">Ferramentas do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eebc5-115">Visual Studio tools</span></span>

<span data-ttu-id="eebc5-116">O Visual Studio permite que os usuários criem, desenvolvam e depurem testes de carga e desempenho da Web.</span><span class="sxs-lookup"><span data-stu-id="eebc5-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="eebc5-117">Uma opção está disponível para criar testes por meio da gravação de ações em um navegador da Web.</span><span class="sxs-lookup"><span data-stu-id="eebc5-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="eebc5-118">Para obter informações sobre como criar, configurar e executar projetos de teste de carga usando o Visual Studio 2017, consulte [início rápido: criar um projeto de teste de carga](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="eebc5-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="eebc5-119">Os testes de carga podem ser configurados para serem executados no local ou executados na nuvem usando o Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="eebc5-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="eebc5-120">Ferramentas de terceiros</span><span class="sxs-lookup"><span data-stu-id="eebc5-120">Third-party tools</span></span>

<span data-ttu-id="eebc5-121">A lista a seguir contém ferramentas de desempenho da Web de terceiros com vários conjuntos de recursos:</span><span class="sxs-lookup"><span data-stu-id="eebc5-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="eebc5-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="eebc5-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="eebc5-123">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="eebc5-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="eebc5-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="eebc5-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="eebc5-125">K6</span><span class="sxs-lookup"><span data-stu-id="eebc5-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="eebc5-126">Locust</span><span class="sxs-lookup"><span data-stu-id="eebc5-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="eebc5-127">Websurto de vento oeste</span><span class="sxs-lookup"><span data-stu-id="eebc5-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="eebc5-128">Netling</span><span class="sxs-lookup"><span data-stu-id="eebc5-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="eebc5-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="eebc5-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

