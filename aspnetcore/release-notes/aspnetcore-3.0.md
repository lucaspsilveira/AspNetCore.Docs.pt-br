---
title: O que há de novo em ASP.NET Core 3.0
author: rick-anderson
description: Conheça os novos recursos do ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 4886673a9b16b8be8d9a0b0d5c7002a91760544e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976970"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="80cf9-103">O que há de novo em ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="80cf9-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="80cf9-104">Este artigo destaca as mudanças mais significativas no ASP.NET Núcleo 3.0 com links para documentação relevante.</span><span class="sxs-lookup"><span data-stu-id="80cf9-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

Blazor<span data-ttu-id="80cf9-105">é uma nova estrutura no ASP.NET Core para construir uma ui web interativa do lado do cliente com .NET:</span><span class="sxs-lookup"><span data-stu-id="80cf9-105"> is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="80cf9-106">Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80cf9-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="80cf9-107">Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada no .NET.</span><span class="sxs-lookup"><span data-stu-id="80cf9-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="80cf9-108">Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.</span><span class="sxs-lookup"><span data-stu-id="80cf9-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

Blazor<span data-ttu-id="80cf9-109">cenários suportados por estruturas:</span><span class="sxs-lookup"><span data-stu-id="80cf9-109"> framework supported scenarios:</span></span>

* <span data-ttu-id="80cf9-110">Componentes de iu de ui reutilizáveis (componentes de navalha)</span><span class="sxs-lookup"><span data-stu-id="80cf9-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="80cf9-111">Roteamento do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="80cf9-111">Client-side routing</span></span>
* <span data-ttu-id="80cf9-112">Layouts de componentes</span><span class="sxs-lookup"><span data-stu-id="80cf9-112">Component layouts</span></span>
* <span data-ttu-id="80cf9-113">Suporte para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="80cf9-113">Support for dependency injection</span></span>
* <span data-ttu-id="80cf9-114">Formulários e validação</span><span class="sxs-lookup"><span data-stu-id="80cf9-114">Forms and validation</span></span>
* <span data-ttu-id="80cf9-115">Construa bibliotecas de componentes com bibliotecas de classe Razor</span><span class="sxs-lookup"><span data-stu-id="80cf9-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="80cf9-116">Interoperabilidade do JavaScript</span><span class="sxs-lookup"><span data-stu-id="80cf9-116">JavaScript interop</span></span>

<span data-ttu-id="80cf9-117">Para obter mais informações, consulte <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="80cf9-117">For more information, see <xref:blazor/index>.</span></span>

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="80cf9-118">Servidor</span><span class="sxs-lookup"><span data-stu-id="80cf9-118"> Server</span></span>

Blazor<span data-ttu-id="80cf9-119">desacoplar a lógica de renderização de componentes de como as atualizações de ida e futura são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="80cf9-119"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="80cf9-120">O servidor oferece suporte para hospedar componentes Razor no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="80cf9-120"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="80cf9-121">As atualizações de iu SignalR de ui são tratadas por uma conexão.</span><span class="sxs-lookup"><span data-stu-id="80cf9-121">UI updates are handled over a SignalR connection.</span></span> Blazor<span data-ttu-id="80cf9-122">O servidor é suportado em ASP.NET Núcleo 3.0.</span><span class="sxs-lookup"><span data-stu-id="80cf9-122"> Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="opno-locblazor-webassembly-preview"></a>Blazor<span data-ttu-id="80cf9-123">WebAssembly (Visualização)</span><span class="sxs-lookup"><span data-stu-id="80cf9-123"> WebAssembly (Preview)</span></span>

Blazor<span data-ttu-id="80cf9-124">aplicativos também podem ser executados diretamente no navegador usando um tempo de execução .NET baseado no WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="80cf9-124"> apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> Blazor<span data-ttu-id="80cf9-125">O WebAssembly está em pré-visualização e *não* é suportado em ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="80cf9-125"> WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> Blazor<span data-ttu-id="80cf9-126">O WebAssembly será suportado em um futuro lançamento do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="80cf9-126"> WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="80cf9-127">Componentes de navalha</span><span class="sxs-lookup"><span data-stu-id="80cf9-127">Razor components</span></span>

Blazor<span data-ttu-id="80cf9-128">aplicativos são construídos a partir de componentes.</span><span class="sxs-lookup"><span data-stu-id="80cf9-128"> apps are built from components.</span></span> <span data-ttu-id="80cf9-129">Os componentes são pedaços independentes da interface do usuário (UI), como uma página, diálogo ou formulário.</span><span class="sxs-lookup"><span data-stu-id="80cf9-129">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="80cf9-130">Os componentes são classes .NET normais que definem a lógica de renderização de ui e os manipuladores de eventos do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="80cf9-130">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="80cf9-131">Você pode criar aplicativos web interativos ricos sem JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80cf9-131">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="80cf9-132">Os componentes são Blazor tipicamente de autoria usando a sintaxe razor, uma mistura natural de HTML e C#.</span><span class="sxs-lookup"><span data-stu-id="80cf9-132">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="80cf9-133">Os componentes da navalha são semelhantes às páginas de navalha e visualizações de MVC, pois ambos usam Razor.</span><span class="sxs-lookup"><span data-stu-id="80cf9-133">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="80cf9-134">Ao contrário de páginas e visualizações, que são baseadas em um modelo de solicitação-resposta, os componentes são usados especificamente para lidar com a composição da IU.</span><span class="sxs-lookup"><span data-stu-id="80cf9-134">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="80cf9-135">gRPC</span><span class="sxs-lookup"><span data-stu-id="80cf9-135">gRPC</span></span>

<span data-ttu-id="80cf9-136">[gRPC:](https://grpc.io/)</span><span class="sxs-lookup"><span data-stu-id="80cf9-136">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="80cf9-137">É uma estrutura rpc (chamada de procedimento remoto) popular e de alto desempenho.</span><span class="sxs-lookup"><span data-stu-id="80cf9-137">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="80cf9-138">Oferece uma abordagem opinativo de contrato-primeiro para o desenvolvimento da API.</span><span class="sxs-lookup"><span data-stu-id="80cf9-138">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="80cf9-139">Usa tecnologias modernas como:</span><span class="sxs-lookup"><span data-stu-id="80cf9-139">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="80cf9-140">HTTP/2 para transporte.</span><span class="sxs-lookup"><span data-stu-id="80cf9-140">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="80cf9-141">Buffers de protocolo como a linguagem de descrição da interface.</span><span class="sxs-lookup"><span data-stu-id="80cf9-141">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="80cf9-142">Formato de serialização binário.</span><span class="sxs-lookup"><span data-stu-id="80cf9-142">Binary serialization format.</span></span>
* <span data-ttu-id="80cf9-143">Fornece recursos como:</span><span class="sxs-lookup"><span data-stu-id="80cf9-143">Provides features such as:</span></span>

  * <span data-ttu-id="80cf9-144">Autenticação</span><span class="sxs-lookup"><span data-stu-id="80cf9-144">Authentication</span></span>
  * <span data-ttu-id="80cf9-145">Fluxo bidirecional e controle de fluxo.</span><span class="sxs-lookup"><span data-stu-id="80cf9-145">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="80cf9-146">Cancelamento e intervalos.</span><span class="sxs-lookup"><span data-stu-id="80cf9-146">Cancellation and timeouts.</span></span>

<span data-ttu-id="80cf9-147">A funcionalidade gRPC no ASP.NET Core 3.0 inclui:</span><span class="sxs-lookup"><span data-stu-id="80cf9-147">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="80cf9-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; Uma estrutura ASP.NET Core para hospedagem de serviços gRPC.</span><span class="sxs-lookup"><span data-stu-id="80cf9-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="80cf9-149">O gRPC no ASP.NET Core integra-se aos recursos padrão do ASP.NET Core, como registro, injeção de dependência (DI), autenticação e autorização.</span><span class="sxs-lookup"><span data-stu-id="80cf9-149">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="80cf9-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; Um cliente gRPC para .NET Core `HttpClient`que se baseia no familiar .</span><span class="sxs-lookup"><span data-stu-id="80cf9-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="80cf9-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client `HttpClientFactory`integration with .</span><span class="sxs-lookup"><span data-stu-id="80cf9-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="80cf9-152">Para obter mais informações, consulte <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="80cf9-152">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="80cf9-153">Consulte [ SignalR O código de atualização](xref:migration/22-to-30#signalr) para obter instruções de migração.</span><span class="sxs-lookup"><span data-stu-id="80cf9-153">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> SignalR<span data-ttu-id="80cf9-154">agora `System.Text.Json` usa para serializar/desserializar mensagens JSON.</span><span class="sxs-lookup"><span data-stu-id="80cf9-154"> now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="80cf9-155">Consulte [Alternar para Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) `Newtonsoft.Json`para obter instruções para restaurar o serializador baseado em newton.</span><span class="sxs-lookup"><span data-stu-id="80cf9-155">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="80cf9-156">Nos Clientes JavaScript e SignalR.NET para , o suporte foi adicionado para reconexão automática.</span><span class="sxs-lookup"><span data-stu-id="80cf9-156">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="80cf9-157">Por padrão, o cliente tenta se reconectar imediatamente e tentar novamente após 2, 10 e 30 segundos, se necessário.</span><span class="sxs-lookup"><span data-stu-id="80cf9-157">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="80cf9-158">Se o cliente se reconectar com sucesso, ele receberá um novo ID de conexão.</span><span class="sxs-lookup"><span data-stu-id="80cf9-158">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="80cf9-159">Reconexão automática é opt-in:</span><span class="sxs-lookup"><span data-stu-id="80cf9-159">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="80cf9-160">Os intervalos de reconexão podem ser especificados passando por uma matriz de durações baseadas em milissegundos:</span><span class="sxs-lookup"><span data-stu-id="80cf9-160">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="80cf9-161">Uma implementação personalizada pode ser aprovada para controle total dos intervalos de reconexão.</span><span class="sxs-lookup"><span data-stu-id="80cf9-161">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="80cf9-162">Se a reconexão falhar após o último intervalo de reconexão:</span><span class="sxs-lookup"><span data-stu-id="80cf9-162">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="80cf9-163">O cliente considera que a conexão está offline.</span><span class="sxs-lookup"><span data-stu-id="80cf9-163">The client considers the connection is offline.</span></span>
* <span data-ttu-id="80cf9-164">O cliente para de tentar se reconectar.</span><span class="sxs-lookup"><span data-stu-id="80cf9-164">The client stops trying to reconnect.</span></span>

<span data-ttu-id="80cf9-165">Durante as tentativas de reconexão, atualize a ui do aplicativo para notificar o usuário de que a reconexão está sendo tentada.</span><span class="sxs-lookup"><span data-stu-id="80cf9-165">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="80cf9-166">Para fornecer feedback de iu quando SignalR a conexão é interrompida, a API do cliente foi expandida para incluir os seguintes manipuladores de eventos:</span><span class="sxs-lookup"><span data-stu-id="80cf9-166">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="80cf9-167">`onreconnecting`: Dá aos desenvolvedores a oportunidade de desativar a ui ou de informar aos usuários que o aplicativo está offline.</span><span class="sxs-lookup"><span data-stu-id="80cf9-167">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="80cf9-168">`onreconnected`: Dá aos desenvolvedores a oportunidade de atualizar a ui assim que a conexão for restabelecida.</span><span class="sxs-lookup"><span data-stu-id="80cf9-168">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="80cf9-169">O código `onreconnecting` a seguir é usa para atualizar a ida e volta enquanto tenta conectar:</span><span class="sxs-lookup"><span data-stu-id="80cf9-169">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="80cf9-170">O código `onreconnected` a seguir é usa para atualizar a ia de ida e volta na conexão:</span><span class="sxs-lookup"><span data-stu-id="80cf9-170">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR<span data-ttu-id="80cf9-171">3.0 e, posteriormente, fornece um recurso personalizado para os manipuladores de autorização quando um método de hub requer autorização.</span><span class="sxs-lookup"><span data-stu-id="80cf9-171"> 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="80cf9-172">O recurso é `HubInvocationContext`uma instância de .</span><span class="sxs-lookup"><span data-stu-id="80cf9-172">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="80cf9-173">O `HubInvocationContext` inclui o:</span><span class="sxs-lookup"><span data-stu-id="80cf9-173">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="80cf9-174">Nome do método hub sendo invocado.</span><span class="sxs-lookup"><span data-stu-id="80cf9-174">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="80cf9-175">Argumentos para o método hub.</span><span class="sxs-lookup"><span data-stu-id="80cf9-175">Arguments to the hub method.</span></span>

<span data-ttu-id="80cf9-176">Considere o seguinte exemplo de um aplicativo de sala de bate-papo que permite o login de várias organizações via Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="80cf9-176">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="80cf9-177">Qualquer pessoa com uma conta microsoft pode fazer login no chat, mas apenas membros da organização proprietária podem banir usuários ou visualizar históricos de bate-papo dos usuários.</span><span class="sxs-lookup"><span data-stu-id="80cf9-177">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="80cf9-178">O aplicativo poderia restringir certas funcionalidades de usuários específicos.</span><span class="sxs-lookup"><span data-stu-id="80cf9-178">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="80cf9-179">No código anterior, `DomainRestrictedRequirement` serve `IAuthorizationRequirement`como um costume .</span><span class="sxs-lookup"><span data-stu-id="80cf9-179">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="80cf9-180">Como `HubInvocationContext` o parâmetro de recursos está sendo passado, a lógica interna pode:</span><span class="sxs-lookup"><span data-stu-id="80cf9-180">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="80cf9-181">Inspecione o contexto em que o Hub está sendo chamado.</span><span class="sxs-lookup"><span data-stu-id="80cf9-181">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="80cf9-182">Tome decisões sobre permitir que o usuário execute métodos individuais do Hub.</span><span class="sxs-lookup"><span data-stu-id="80cf9-182">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="80cf9-183">Os métodos individuais do Hub podem ser marcados com o nome da política que o código verifica em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="80cf9-183">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="80cf9-184">À medida que os clientes `DomainRestrictedRequirement` tentam chamar métodos individuais do Hub, o manipulador executa e controla o acesso aos métodos.</span><span class="sxs-lookup"><span data-stu-id="80cf9-184">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="80cf9-185">Com base na `DomainRestrictedRequirement` forma como os controles acessam:</span><span class="sxs-lookup"><span data-stu-id="80cf9-185">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="80cf9-186">Todos os usuários conectados podem `SendMessage` chamar o método.</span><span class="sxs-lookup"><span data-stu-id="80cf9-186">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="80cf9-187">Somente usuários que fizeram login `@jabbr.net` com um endereço de e-mail podem visualizar os históricos dos usuários.</span><span class="sxs-lookup"><span data-stu-id="80cf9-187">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="80cf9-188">Só `bob42@jabbr.net` pode banir os usuários da sala de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="80cf9-188">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="80cf9-189">A `DomainRestricted` criação da política pode envolver:</span><span class="sxs-lookup"><span data-stu-id="80cf9-189">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="80cf9-190">Em *Startup.cs*, adicionando a nova política.</span><span class="sxs-lookup"><span data-stu-id="80cf9-190">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="80cf9-191">Forneça o `DomainRestrictedRequirement` requisito personalizado como parâmetro.</span><span class="sxs-lookup"><span data-stu-id="80cf9-191">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="80cf9-192">Registrando-se `DomainRestricted` com o middleware de autorização.</span><span class="sxs-lookup"><span data-stu-id="80cf9-192">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

SignalR<span data-ttu-id="80cf9-193">hubs usam [Endpoint Routing](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="80cf9-193"> hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> SignalR<span data-ttu-id="80cf9-194">a conexão do hub foi feita anteriormente explicitamente:</span><span class="sxs-lookup"><span data-stu-id="80cf9-194"> hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="80cf9-195">Na versão anterior, os desenvolvedores precisavam conectar controladores, páginas de navalha e hubs em uma variedade de lugares.</span><span class="sxs-lookup"><span data-stu-id="80cf9-195">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="80cf9-196">A conexão explícita resulta em uma série de segmentos de roteamento quase idênticos:</span><span class="sxs-lookup"><span data-stu-id="80cf9-196">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

SignalR<span data-ttu-id="80cf9-197">Os hubs 3.0 podem ser roteados através do roteamento de ponto final.</span><span class="sxs-lookup"><span data-stu-id="80cf9-197"> 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="80cf9-198">Com o roteamento de ponto final, normalmente todo o roteamento pode ser configurado em `UseRouting`:</span><span class="sxs-lookup"><span data-stu-id="80cf9-198">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="80cf9-199">ASP.NET Núcleo 3.0 SignalR adicionado:</span><span class="sxs-lookup"><span data-stu-id="80cf9-199">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="80cf9-200">Streaming de cliente para servidor.</span><span class="sxs-lookup"><span data-stu-id="80cf9-200">Client-to-server streaming.</span></span> <span data-ttu-id="80cf9-201">Com o streaming de cliente para servidor, os métodos `IAsyncEnumerable<T>` `ChannelReader<T>`do lado do servidor podem tomar instâncias de um ou .</span><span class="sxs-lookup"><span data-stu-id="80cf9-201">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="80cf9-202">Na seguinte amostra C#, o `UploadStream` método no Hub receberá um fluxo de strings do cliente:</span><span class="sxs-lookup"><span data-stu-id="80cf9-202">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="80cf9-203">Os aplicativos clientes .NET `ChannelReader<T>` podem `stream` passar uma `UploadStream` `IAsyncEnumerable<T>` ou instância como o argumento do método Hub acima.</span><span class="sxs-lookup"><span data-stu-id="80cf9-203">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="80cf9-204">Depois `for` que o loop for concluído e a função local sair, a conclusão do fluxo é enviada:</span><span class="sxs-lookup"><span data-stu-id="80cf9-204">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="80cf9-205">Os aplicativos cliente SignalR `Subject` JavaScript usam o (ou um `stream` Assunto `UploadStream` [RxJS)](https://rxjs.dev/api/index/class/Subject)para o argumento do método Hub acima.</span><span class="sxs-lookup"><span data-stu-id="80cf9-205">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="80cf9-206">O código JavaScript `subject.next` poderia usar o método para lidar com as strings à medida que elas são capturadas e prontas para serem enviadas ao servidor.</span><span class="sxs-lookup"><span data-stu-id="80cf9-206">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="80cf9-207">Usando códigos como os dois trechos anteriores, experiências de streaming em tempo real podem ser criadas.</span><span class="sxs-lookup"><span data-stu-id="80cf9-207">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="80cf9-208">Nova serialização JSON</span><span class="sxs-lookup"><span data-stu-id="80cf9-208">New JSON serialization</span></span>

<span data-ttu-id="80cf9-209">ASP.NET Core 3.0 <xref:System.Text.Json> agora usa por padrão para serialização JSON:</span><span class="sxs-lookup"><span data-stu-id="80cf9-209">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="80cf9-210">Lê e escreve JSON assíncronamente.</span><span class="sxs-lookup"><span data-stu-id="80cf9-210">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="80cf9-211">É otimizado para texto UTF-8.</span><span class="sxs-lookup"><span data-stu-id="80cf9-211">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="80cf9-212">Tipicamente desempenho `Newtonsoft.Json`superior a .</span><span class="sxs-lookup"><span data-stu-id="80cf9-212">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="80cf9-213">Para adicionar Json.NET ao ASP.NET Core 3.0, consulte Adicionar suporte ao [formato JSON baseado em Newtonsoft.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="80cf9-213">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="80cf9-214">Novas diretivas de Razor</span><span class="sxs-lookup"><span data-stu-id="80cf9-214">New Razor directives</span></span>

<span data-ttu-id="80cf9-215">A lista a seguir contém novas diretivas de Razor:</span><span class="sxs-lookup"><span data-stu-id="80cf9-215">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="80cf9-216">[`@attribute`](xref:mvc/views/razor#attribute)&ndash; A `@attribute` diretiva aplica o atributo dado à classe da página ou exibição gerada.</span><span class="sxs-lookup"><span data-stu-id="80cf9-216">[`@attribute`](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="80cf9-217">Por exemplo, `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="80cf9-217">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="80cf9-218">[`@implements`](xref:mvc/views/razor#implements)&ndash; A `@implements` diretiva implementa uma interface para a classe gerada.</span><span class="sxs-lookup"><span data-stu-id="80cf9-218">[`@implements`](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="80cf9-219">Por exemplo, `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="80cf9-219">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="80cf9-220">IdentityServer4 suporta autenticação e autorização para APIs e SPAs da Web</span><span class="sxs-lookup"><span data-stu-id="80cf9-220">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="80cf9-221">ASP.NET Core 3.0 oferece autenticação em SPAs (Single Page Apps, aplicativos de página única) usando o suporte para autorização de API web.</span><span class="sxs-lookup"><span data-stu-id="80cf9-221">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="80cf9-222">ASP.NET Identidade Central para autenticar e armazenar usuários é combinada com [o IdentityServer4](https://identityserver.io/) para implementar o Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="80cf9-222">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="80cf9-223">IdentityServer4 é uma estrutura OpenID Connect e OAuth 2.0 para ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="80cf9-223">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="80cf9-224">Ele habilita os seguintes recursos de segurança:</span><span class="sxs-lookup"><span data-stu-id="80cf9-224">It enables the following security features:</span></span>

* <span data-ttu-id="80cf9-225">Autenticação como serviço (AaaS)</span><span class="sxs-lookup"><span data-stu-id="80cf9-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="80cf9-226">SSO (Single Sign-on/off) (SSO) sobre vários tipos de aplicativos</span><span class="sxs-lookup"><span data-stu-id="80cf9-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="80cf9-227">Controle de acesso para APIs</span><span class="sxs-lookup"><span data-stu-id="80cf9-227">Access control for APIs</span></span>
* <span data-ttu-id="80cf9-228">Gateway da Federação</span><span class="sxs-lookup"><span data-stu-id="80cf9-228">Federation Gateway</span></span>

<span data-ttu-id="80cf9-229">Para obter mais informações, consulte [a documentação IdentityServer4](http://docs.identityserver.io/en/latest/index.html) ou [autenticação e autorização para SPAs](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="80cf9-229">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="80cf9-230">Autenticação de Certificado e Kerberos</span><span class="sxs-lookup"><span data-stu-id="80cf9-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="80cf9-231">A autenticação do certificado requer:</span><span class="sxs-lookup"><span data-stu-id="80cf9-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="80cf9-232">Configurando o servidor para aceitar certificados.</span><span class="sxs-lookup"><span data-stu-id="80cf9-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="80cf9-233">Adicionando o middleware `Startup.Configure`de autenticação em .</span><span class="sxs-lookup"><span data-stu-id="80cf9-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="80cf9-234">Adicionando o serviço `Startup.ConfigureServices`de autenticação de certificados em .</span><span class="sxs-lookup"><span data-stu-id="80cf9-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="80cf9-235">As opções de autenticação de certificados incluem a capacidade de:</span><span class="sxs-lookup"><span data-stu-id="80cf9-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="80cf9-236">Aceite certificados auto-assinados.</span><span class="sxs-lookup"><span data-stu-id="80cf9-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="80cf9-237">Verifique a revogação do certificado.</span><span class="sxs-lookup"><span data-stu-id="80cf9-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="80cf9-238">Verifique se o certificado oferecido tem as bandeiras de uso corretas nele.</span><span class="sxs-lookup"><span data-stu-id="80cf9-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="80cf9-239">Um principal usuário padrão é construído a partir das propriedades do certificado.</span><span class="sxs-lookup"><span data-stu-id="80cf9-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="80cf9-240">O principal usuário contém um evento que permite complementar ou substituir o principal.</span><span class="sxs-lookup"><span data-stu-id="80cf9-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="80cf9-241">Para obter mais informações, consulte <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="80cf9-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="80cf9-242">[A autenticação do Windows](/windows-server/security/windows-authentication/windows-authentication-overview) foi estendida para linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="80cf9-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="80cf9-243">Nas versões anteriores, a Autenticação do Windows era limitada ao [IIS](xref:host-and-deploy/iis/index) e [ao HttpSys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="80cf9-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="80cf9-244">Em ASP.NET Core 3.0, o [Kestrel](xref:fundamentals/servers/kestrel) tem a capacidade de usar o Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)e [NTLM no Windows,](/windows-server/security/kerberos/ntlm-overview)Linux e macOS para hosts com domínio do Windows.</span><span class="sxs-lookup"><span data-stu-id="80cf9-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="80cf9-245">O suporte kestrel desses esquemas de autenticação é fornecido pelo pacote [Microsoft.AspNetCore.Authentication.Negotiate NuGet.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)</span><span class="sxs-lookup"><span data-stu-id="80cf9-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="80cf9-246">Como acontece com os outros serviços de autenticação, configure o aplicativo de autenticação em largura e configure o serviço:</span><span class="sxs-lookup"><span data-stu-id="80cf9-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="80cf9-247">Requisitos do host:</span><span class="sxs-lookup"><span data-stu-id="80cf9-247">Host requirements:</span></span>

* <span data-ttu-id="80cf9-248">Os hosts do Windows devem ter SPNs [(Service Principal Names, nomes principais](/windows/win32/ad/service-principal-names) de serviço) adicionados à conta de usuário que hospeda o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80cf9-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="80cf9-249">As máquinas Linux e macOS devem ser unidas ao domínio.</span><span class="sxs-lookup"><span data-stu-id="80cf9-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="80cf9-250">As SPNs devem ser criadas para o processo web.</span><span class="sxs-lookup"><span data-stu-id="80cf9-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="80cf9-251">[Os arquivos keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) devem ser gerados e configurados na máquina host.</span><span class="sxs-lookup"><span data-stu-id="80cf9-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="80cf9-252">Para obter mais informações, consulte <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="80cf9-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="80cf9-253">Alterações de modelo</span><span class="sxs-lookup"><span data-stu-id="80cf9-253">Template changes</span></span>

<span data-ttu-id="80cf9-254">Os modelos de IU da Web (Razor Pages, MVC com controlador e visualizações) têm os seguintes removedores:</span><span class="sxs-lookup"><span data-stu-id="80cf9-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="80cf9-255">A ui de consentimento de cookies não está mais incluída.</span><span class="sxs-lookup"><span data-stu-id="80cf9-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="80cf9-256">Para habilitar o recurso de consentimento de cookies em <xref:security/gdpr>um aplicativo ASP.NET gerado pelo modelo Core 3.0, consulte .</span><span class="sxs-lookup"><span data-stu-id="80cf9-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="80cf9-257">Scripts e ativos estáticos relacionados são agora referenciados como arquivos locais em vez de usar CDNs.</span><span class="sxs-lookup"><span data-stu-id="80cf9-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="80cf9-258">Para obter mais informações, consulte [Scripts e ativos estáticos relacionados são agora referenciados como arquivos locais em vez de usar CDNs com base no ambiente atual (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="80cf9-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="80cf9-259">O modelo Angular atualizado para usar Angular 8.</span><span class="sxs-lookup"><span data-stu-id="80cf9-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="80cf9-260">O modelo de biblioteca de classe Razor (RCL) é padrão para o desenvolvimento de componentes Razor por padrão.</span><span class="sxs-lookup"><span data-stu-id="80cf9-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="80cf9-261">Uma nova opção de modelo no Visual Studio fornece suporte a modelos para páginas e visualizações.</span><span class="sxs-lookup"><span data-stu-id="80cf9-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="80cf9-262">Ao criar uma RCL a partir do `--support-pages-and-views` modelo`dotnet new razorclasslib --support-pages-and-views`em um shell de comando, passe a opção ( ).</span><span class="sxs-lookup"><span data-stu-id="80cf9-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="80cf9-263">Host Genérico</span><span class="sxs-lookup"><span data-stu-id="80cf9-263">Generic Host</span></span>

<span data-ttu-id="80cf9-264">Os modelos ASP.NET Core <xref:fundamentals/host/generic-host>3.0 usam .</span><span class="sxs-lookup"><span data-stu-id="80cf9-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="80cf9-265">Versões <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>anteriores utilizadas .</span><span class="sxs-lookup"><span data-stu-id="80cf9-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="80cf9-266">O uso do .NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core Generic Host () oferece uma melhor integração dos aplicativos ASP.NET Core com outros cenários de servidor que não são específicos da Web.</span><span class="sxs-lookup"><span data-stu-id="80cf9-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="80cf9-267">Para obter mais informações, consulte [HostBuilder substitui WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="80cf9-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="80cf9-268">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="80cf9-268">Host configuration</span></span>

<span data-ttu-id="80cf9-269">Antes do lançamento do ASP.NET Core 3.0, `ASPNETCORE_` as variáveis de ambiente prefixadas com foram carregadas para a configuração do host do Web Host.</span><span class="sxs-lookup"><span data-stu-id="80cf9-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="80cf9-270">Em 3.0, `AddEnvironmentVariables` é usado para carregar `DOTNET_` variáveis de `CreateDefaultBuilder`ambiente prefixadas com para configuração do host com .</span><span class="sxs-lookup"><span data-stu-id="80cf9-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="80cf9-271">Mudanças na injeção de construtor de startup</span><span class="sxs-lookup"><span data-stu-id="80cf9-271">Changes to Startup constructor injection</span></span>

<span data-ttu-id="80cf9-272">O Host Genérico só suporta `Startup` os seguintes tipos de injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="80cf9-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="80cf9-273">Todos os serviços ainda podem ser `Startup.Configure` injetados diretamente como argumentos para o método.</span><span class="sxs-lookup"><span data-stu-id="80cf9-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="80cf9-274">Para obter mais informações, consulte [Generic Host restringe a injeção de construtor de inicialização (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="80cf9-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="80cf9-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="80cf9-275">Kestrel</span></span>

* <span data-ttu-id="80cf9-276">A configuração do Kestrel foi atualizada para a migração para o Host genérico.</span><span class="sxs-lookup"><span data-stu-id="80cf9-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="80cf9-277">Em 3.0, o Kestrel é configurado no `ConfigureWebHostDefaults`construtor de host web fornecido por .</span><span class="sxs-lookup"><span data-stu-id="80cf9-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="80cf9-278">Os adaptadores de conexão foram removidos do Kestrel e substituídos pelo Connection Middleware, que é semelhante ao HTTP Middleware no pipeline ASP.NET Core, mas para conexões de nível inferior.</span><span class="sxs-lookup"><span data-stu-id="80cf9-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="80cf9-279">A camada de transporte Kestrel foi exposta `Connections.Abstractions`como uma interface pública em .</span><span class="sxs-lookup"><span data-stu-id="80cf9-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="80cf9-280">A ambiguidade entre cabeçalhos e reboques foi resolvida movendo cabeçalhos para uma nova coleção.</span><span class="sxs-lookup"><span data-stu-id="80cf9-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="80cf9-281">APIs síncronas de I/O, como `HttpRequest.Body.Read`, são uma fonte comum de fome de thread suscitado síncronos.</span><span class="sxs-lookup"><span data-stu-id="80cf9-281">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="80cf9-282">No 3.0, `AllowSynchronousIO` é desativado por padrão.</span><span class="sxs-lookup"><span data-stu-id="80cf9-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="80cf9-283">Para obter mais informações, consulte <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="80cf9-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="80cf9-284">HTTP/2 ativado por padrão</span><span class="sxs-lookup"><span data-stu-id="80cf9-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="80cf9-285">HTTP/2 é habilitado por padrão em Kestrel para pontos finais HTTPS.</span><span class="sxs-lookup"><span data-stu-id="80cf9-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="80cf9-286">O suporte HTTP/2 para IIS ou HTTP.sys é ativado quando suportado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="80cf9-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="80cf9-287">EventCounters a pedido</span><span class="sxs-lookup"><span data-stu-id="80cf9-287">EventCounters on request</span></span>

<span data-ttu-id="80cf9-288">O Hosting EventSource, `Microsoft.AspNetCore.Hosting`emite <xref:System.Diagnostics.Tracing.EventCounter> os seguintes novos tipos relacionados às solicitações recebidas:</span><span class="sxs-lookup"><span data-stu-id="80cf9-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="80cf9-289">Roteamento de ponto final</span><span class="sxs-lookup"><span data-stu-id="80cf9-289">Endpoint routing</span></span>

<span data-ttu-id="80cf9-290">O roteamento de ponto final, que permite que os frameworks (por exemplo, MVC) funcionem bem com middleware, é aprimorado:</span><span class="sxs-lookup"><span data-stu-id="80cf9-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="80cf9-291">A ordem de middleware e endpoints é configurável `Startup.Configure`no pipeline de processamento de solicitações de .</span><span class="sxs-lookup"><span data-stu-id="80cf9-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="80cf9-292">Endpoints e middleware compõem bem com outras tecnologias baseadas ASP.NET Core, como health checks.</span><span class="sxs-lookup"><span data-stu-id="80cf9-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="80cf9-293">Os pontos finais podem implementar uma política, como CORS ou autorização, tanto em middleware quanto em MVC.</span><span class="sxs-lookup"><span data-stu-id="80cf9-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="80cf9-294">Filtros e atributos podem ser colocados em métodos nos controladores.</span><span class="sxs-lookup"><span data-stu-id="80cf9-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="80cf9-295">Para obter mais informações, consulte <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="80cf9-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="80cf9-296">Verificações de Integridade</span><span class="sxs-lookup"><span data-stu-id="80cf9-296">Health Checks</span></span>

<span data-ttu-id="80cf9-297">As verificações de saúde usam roteamento de ponto final com o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="80cf9-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="80cf9-298">Em `Startup.Configure`, `MapHealthChecks` chamar o construtor de ponto final com a URL de ponto final ou caminho relativo:</span><span class="sxs-lookup"><span data-stu-id="80cf9-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="80cf9-299">Os pontos finais da Verificação de Saúde podem:</span><span class="sxs-lookup"><span data-stu-id="80cf9-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="80cf9-300">Especifique um ou mais hosts/portas permitidas.</span><span class="sxs-lookup"><span data-stu-id="80cf9-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="80cf9-301">Requer autorização.</span><span class="sxs-lookup"><span data-stu-id="80cf9-301">Require authorization.</span></span>
* <span data-ttu-id="80cf9-302">Requeira o CORS.</span><span class="sxs-lookup"><span data-stu-id="80cf9-302">Require CORS.</span></span>

<span data-ttu-id="80cf9-303">Para obter mais informações, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="80cf9-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="80cf9-304">Tubos em HttpContext</span><span class="sxs-lookup"><span data-stu-id="80cf9-304">Pipes on HttpContext</span></span>

<span data-ttu-id="80cf9-305">Agora é possível ler o corpo de solicitação e <xref:System.IO.Pipelines> escrever o corpo de resposta usando a API.</span><span class="sxs-lookup"><span data-stu-id="80cf9-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="80cf9-306">O</span><span class="sxs-lookup"><span data-stu-id="80cf9-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="80cf9-307">`HttpRequest.BodyReader`propriedade fornece <xref:System.IO.Pipelines.PipeReader> um que pode ser usado para ler o corpo de solicitação.</span><span class="sxs-lookup"><span data-stu-id="80cf9-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="80cf9-308">O</span><span class="sxs-lookup"><span data-stu-id="80cf9-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="80cf9-309">`HttpResponse.BodyWriter`a propriedade <xref:System.IO.Pipelines.PipeWriter> fornece um que pode ser usado para escrever o corpo de resposta.</span><span class="sxs-lookup"><span data-stu-id="80cf9-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="80cf9-310">`HttpRequest.BodyReader`é um análogo do `HttpRequest.Body` fluxo.</span><span class="sxs-lookup"><span data-stu-id="80cf9-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="80cf9-311">`HttpResponse.BodyWriter`é um análogo do `HttpResponse.Body` fluxo.</span><span class="sxs-lookup"><span data-stu-id="80cf9-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="80cf9-312">Relatório de erro melhorado no IIS</span><span class="sxs-lookup"><span data-stu-id="80cf9-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="80cf9-313">Erros de inicialização ao hospedar aplicativos ASP.NET Core no IIS agora produzem dados de diagnóstico mais ricos.</span><span class="sxs-lookup"><span data-stu-id="80cf9-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="80cf9-314">Esses erros são reportados ao Registro de Eventos do Windows com vestígios de pilha sempre que aplicável.</span><span class="sxs-lookup"><span data-stu-id="80cf9-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="80cf9-315">Além disso, todos os avisos, erros e exceções não tratadas são registrados no Registro de Eventos do Windows.</span><span class="sxs-lookup"><span data-stu-id="80cf9-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="80cf9-316">Serviço do Trabalhador e SDK do Trabalhador</span><span class="sxs-lookup"><span data-stu-id="80cf9-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="80cf9-317">O .NET Core 3.0 introduz o novo modelo de aplicativo do Serviço de Trabalhadores.</span><span class="sxs-lookup"><span data-stu-id="80cf9-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="80cf9-318">Este modelo fornece um ponto de partida para escrever serviços de longa duração no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="80cf9-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="80cf9-319">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="80cf9-319">For more information, see:</span></span>

* [<span data-ttu-id="80cf9-320">.NET Core Workers como Windows Services</span><span class="sxs-lookup"><span data-stu-id="80cf9-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="80cf9-321">Melhorias no Middleware de cabeçalhos encaminhados</span><span class="sxs-lookup"><span data-stu-id="80cf9-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="80cf9-322">Em versões anteriores <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> do ASP.NET Core, chamadas e eram problemáticas quando implantadas em um Azure Linux ou por trás de qualquer proxy reverso que não fosse o IIS.</span><span class="sxs-lookup"><span data-stu-id="80cf9-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="80cf9-323">A correção para versões anteriores está documentada no [Forward o esquema para Proxies reversos Linux e não-IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="80cf9-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="80cf9-324">Este cenário está fixado em ASP.NET Núcleo 3.0.</span><span class="sxs-lookup"><span data-stu-id="80cf9-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="80cf9-325">O host habilita o [Middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) quando a `ASPNETCORE_FORWARDEDHEADERS_ENABLED` variável ambiente é definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="80cf9-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="80cf9-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`está definido `true` em nossas imagens de contêiner.</span><span class="sxs-lookup"><span data-stu-id="80cf9-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="80cf9-327">Melhorias de desempenho</span><span class="sxs-lookup"><span data-stu-id="80cf9-327">Performance improvements</span></span>

<span data-ttu-id="80cf9-328">ASP.NET Core 3.0 inclui muitas melhorias que reduzem o uso da memória e melhoram o throughput:</span><span class="sxs-lookup"><span data-stu-id="80cf9-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="80cf9-329">Redução do uso da memória ao usar o recipiente de injeção de dependência incorporado para serviços com escopo.</span><span class="sxs-lookup"><span data-stu-id="80cf9-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="80cf9-330">Redução de alocações em toda a estrutura, incluindo cenários de middleware e roteamento.</span><span class="sxs-lookup"><span data-stu-id="80cf9-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="80cf9-331">Redução do uso de memória para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="80cf9-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="80cf9-332">Redução de memória e melhorias de throughput para conexões HTTPS.</span><span class="sxs-lookup"><span data-stu-id="80cf9-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="80cf9-333">Novo serializador JSON otimizado e totalmente assíncrono.</span><span class="sxs-lookup"><span data-stu-id="80cf9-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="80cf9-334">Redução do uso da memória e melhorias de throughput no parsing de formulários.</span><span class="sxs-lookup"><span data-stu-id="80cf9-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="80cf9-335">ASP.NET Core 3.0 só é executado no .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="80cf9-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="80cf9-336">A partir de ASP.NET Núcleo 3.0, o .NET Framework não é mais um quadro-alvo suportado.</span><span class="sxs-lookup"><span data-stu-id="80cf9-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="80cf9-337">Os projetos direcionados ao .NET Framework podem continuar de forma totalmente suportada usando a [versão LTS .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span><span class="sxs-lookup"><span data-stu-id="80cf9-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="80cf9-338">A maioria dos pacotes relacionados ao Core 2.1.x ASP.NET serão suportados indefinidamente, além do período LTS de três anos para o .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="80cf9-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="80cf9-339">Para obter informações de migração, consulte ['Portar seu código de .NET Framework' para .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="80cf9-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="80cf9-340">Use a estrutura compartilhada ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80cf9-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="80cf9-341">O ASP.NET framework compartilhado Core 3.0, contido no [metapacote Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)não requer mais um elemento explícito `<PackageReference />` no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="80cf9-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="80cf9-342">A estrutura compartilhada é automaticamente referenciada `Microsoft.NET.Sdk.Web` ao usar o SDK no arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="80cf9-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="80cf9-343">Assembléias removidas da estrutura compartilhada do Núcleo ASP.NET</span><span class="sxs-lookup"><span data-stu-id="80cf9-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="80cf9-344">As assembléias mais notáveis removidas da estrutura compartilhada do ASP.NET Core 3.0 são:</span><span class="sxs-lookup"><span data-stu-id="80cf9-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="80cf9-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span><span class="sxs-lookup"><span data-stu-id="80cf9-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="80cf9-346">Para adicionar Json.NET ao ASP.NET Core 3.0, consulte Adicionar suporte ao [formato JSON baseado em Newtonsoft.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="80cf9-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="80cf9-347">ASP.NET Core 3.0 `System.Text.Json` é introduzido para leitura e escrita JSON.</span><span class="sxs-lookup"><span data-stu-id="80cf9-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="80cf9-348">Para obter mais informações, consulte [a serialização do Novo JSON](#new-json-serialization) neste documento.</span><span class="sxs-lookup"><span data-stu-id="80cf9-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="80cf9-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="80cf9-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="80cf9-350">Para obter uma lista completa de conjuntos removidos da estrutura compartilhada, consulte [Assembléias sendo removidas do Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="80cf9-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="80cf9-351">Para obter mais informações sobre a motivação para essa mudança, consulte [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) e [A first look to changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="80cf9-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 