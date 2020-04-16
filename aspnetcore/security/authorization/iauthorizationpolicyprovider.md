---
title: Provedores de políticas de autorização personalizadas no núcleo ASP.NET
author: mjrousos
description: Aprenda a usar um IAuthorizationPolicyProvider personalizado em um aplicativo ASP.NET Core para gerar dinamicamente políticas de autorização.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2a8b189cc9f17529a962a1f9642c7bb199d5781b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440916"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="6c9e3-103">Provedores de políticas de autorização personalizadas que usam iAuthorizationPolicyProvider no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c9e3-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="6c9e3-104">Por [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="6c9e3-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="6c9e3-105">Normalmente, ao usar a autorização baseada em `AuthorizationOptions.AddPolicy` [políticas,](xref:security/authorization/policies)as políticas são registradas ligando como parte da configuração do serviço de autorização.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="6c9e3-106">Em alguns cenários, pode não ser possível (ou desejável) registrar todas as políticas de autorização dessa forma.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="6c9e3-107">Nesses casos, você pode `IAuthorizationPolicyProvider` usar um costume para controlar como as políticas de autorização são fornecidas.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="6c9e3-108">Exemplos de cenários em que um [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personalizado pode ser útil incluem:</span><span class="sxs-lookup"><span data-stu-id="6c9e3-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="6c9e3-109">Usando um serviço externo para fornecer avaliação de políticas.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="6c9e3-110">Usando uma grande variedade de políticas (para diferentes números de quartos ou idades, por `AuthorizationOptions.AddPolicy` exemplo), por isso não faz sentido adicionar cada política de autorização individual com uma chamada.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="6c9e3-111">Criar políticas em tempo de execução com base em informações em uma fonte de dados externo (como um banco de dados) ou determinar os requisitos de autorização dinamicamente através de outro mecanismo.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="6c9e3-112">[Exibir ou baixar](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) o código de exemplo do [repositório AspNetCore GitHub](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="6c9e3-112">[View or download sample code](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="6c9e3-113">Baixe o arquivo ZIP dotnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="6c9e3-114">Descompacte o arquivo.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-114">Unzip the file.</span></span> <span data-ttu-id="6c9e3-115">Navegue até a pasta de projeto *src/Security/samples/CustomPolicyProvider.*</span><span class="sxs-lookup"><span data-stu-id="6c9e3-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="6c9e3-116">Personalizar a recuperação de políticas</span><span class="sxs-lookup"><span data-stu-id="6c9e3-116">Customize policy retrieval</span></span>

<span data-ttu-id="6c9e3-117">ASP.NET os aplicativos Core `IAuthorizationPolicyProvider` usam uma implementação da interface para recuperar políticas de autorização.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="6c9e3-118">Por padrão, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) é registrado e usado.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="6c9e3-119">`DefaultAuthorizationPolicyProvider`retorna políticas `AuthorizationOptions` do fornecido `IServiceCollection.AddAuthorization` em uma chamada.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="6c9e3-120">Personalize esse comportamento registrando `IAuthorizationPolicyProvider` uma implementação diferente no recipiente de injeção de [dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="6c9e3-121">A `IAuthorizationPolicyProvider` interface contém três APIs:</span><span class="sxs-lookup"><span data-stu-id="6c9e3-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="6c9e3-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) retorna uma política de autorização para um determinado nome.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="6c9e3-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) retorna a política de autorização `[Authorize]` padrão (a diretiva usada para atributos sem uma política especificada).</span><span class="sxs-lookup"><span data-stu-id="6c9e3-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="6c9e3-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) retorna a política de autorização de recuo (a política usada pelo Middleware de autorização quando nenhuma política é especificada).</span><span class="sxs-lookup"><span data-stu-id="6c9e3-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="6c9e3-125">Ao implementar essas APIs, você pode personalizar como as políticas de autorização são fornecidas.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="6c9e3-126">Exemplo de atributo de autorização parametrizada</span><span class="sxs-lookup"><span data-stu-id="6c9e3-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="6c9e3-127">Um cenário `IAuthorizationPolicyProvider` onde é útil `[Authorize]` é habilitar atributos personalizados cujos requisitos dependem de um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="6c9e3-128">Por exemplo, na documentação [de autorização baseada em políticas,](xref:security/authorization/policies) uma política baseada na idade ("Pelo menos21") foi usada como amostra.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="6c9e3-129">Se diferentes ações de controlador em um aplicativo devem ser disponibilizadas para usuários de *diferentes* idades, pode ser útil ter muitas políticas diferentes baseadas na idade.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="6c9e3-130">Em vez de registrar todas as diferentes políticas `AuthorizationOptions`baseadas em idade que o `IAuthorizationPolicyProvider`aplicativo precisará, você pode gerar as políticas dinamicamente com um costume .</span><span class="sxs-lookup"><span data-stu-id="6c9e3-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="6c9e3-131">Para facilitar o uso das políticas, você pode anotar `[MinimumAgeAuthorize(20)]`ações com atributo de autorização personalizado como .</span><span class="sxs-lookup"><span data-stu-id="6c9e3-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="6c9e3-132">Atributos de Autorização Personalizada</span><span class="sxs-lookup"><span data-stu-id="6c9e3-132">Custom Authorization attributes</span></span>

<span data-ttu-id="6c9e3-133">As políticas de autorização são identificadas por seus nomes.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="6c9e3-134">O `MinimumAgeAuthorizeAttribute` costume descrito anteriormente precisa mapear argumentos em uma seqüência que pode ser usada para recuperar a política de autorização correspondente.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="6c9e3-135">Você pode fazer isso `AuthorizeAttribute` derivando `Age` e fazendo `AuthorizeAttribute.Policy` a propriedade envolver a propriedade.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="6c9e3-136">Este tipo de `Policy` atributo tem uma seqüência baseada no prefixo codificado rígido (`"MinimumAge"`) e um inteiro passado através do construtor.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="6c9e3-137">Você pode aplicá-lo a `Authorize` ações da mesma forma que outros atributos, exceto que ele toma um inteiro como parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="6c9e3-138">Provedor de políticas de iautorização personalizada</span><span class="sxs-lookup"><span data-stu-id="6c9e3-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="6c9e3-139">O `MinimumAgeAuthorizeAttribute` costume facilita a solicitação de políticas de autorização para qualquer idade mínima desejada.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="6c9e3-140">O próximo problema a ser resolvido é garantir que as políticas de autorização estejam disponíveis para todas essas idades diferentes.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="6c9e3-141">É aqui `IAuthorizationPolicyProvider` que um é útil.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="6c9e3-142">Ao `MinimumAgeAuthorizationAttribute`utilizar , os nomes da `"MinimumAge" + Age`política de `IAuthorizationPolicyProvider` autorização seguirão o padrão, de modo que o costume deve gerar políticas de autorização por:</span><span class="sxs-lookup"><span data-stu-id="6c9e3-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="6c9e3-143">Analisar a idade do nome da apólice.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="6c9e3-144">Usando `AuthorizationPolicyBuilder` para criar um novo`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="6c9e3-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="6c9e3-145">Neste e nos exemplos seguintes, presume-se que o usuário seja autenticado através de um cookie.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="6c9e3-146">O `AuthorizationPolicyBuilder` deve ser construído com pelo menos um nome de esquema de autorização ou sempre ter sucesso.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="6c9e3-147">Caso contrário, não há informações sobre como fornecer um desafio ao usuário e uma exceção será lançada.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="6c9e3-148">Adicionando requisitos à apólice `AuthorizationPolicyBuilder.AddRequirements`com base na idade com .</span><span class="sxs-lookup"><span data-stu-id="6c9e3-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="6c9e3-149">Em outros cenários, `RequireClaim` `RequireRole`você `RequireUserName` pode usar , ou em vez disso.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="6c9e3-150">Vários provedores de políticas de autorização</span><span class="sxs-lookup"><span data-stu-id="6c9e3-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="6c9e3-151">Ao usar `IAuthorizationPolicyProvider` implementações personalizadas, tenha em mente `IAuthorizationPolicyProvider`que ASP.NET Core usa apenas uma instância de .</span><span class="sxs-lookup"><span data-stu-id="6c9e3-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="6c9e3-152">Se um provedor personalizado não for capaz de fornecer políticas de autorização para todos os nomes de políticas que serão usados, ele deve adiar para um provedor de backup.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="6c9e3-153">Por exemplo, considere um aplicativo que precise tanto de políticas de idade personalizadas quanto de recuperação de políticas baseadas em papéis mais tradicionais.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="6c9e3-154">Tal aplicativo poderia usar um provedor de política de autorização personalizado que:</span><span class="sxs-lookup"><span data-stu-id="6c9e3-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="6c9e3-155">Tentativas de analisar nomes de políticas.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="6c9e3-156">Chama para um provedor `DefaultAuthorizationPolicyProvider`de apólice diferente (como ) se o nome da apólice não contiver uma idade.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="6c9e3-157">O `IAuthorizationPolicyProvider` exemplo de implementação mostrado `DefaultAuthorizationPolicyProvider` acima pode ser atualizado para usar o criando um provedor de política de backup em seu construtor (a ser usado no caso de o nome da diretiva não corresponder ao padrão esperado de 'MinimumAge' + idade).</span><span class="sxs-lookup"><span data-stu-id="6c9e3-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="6c9e3-158">Em seguida, o `GetPolicyAsync` método pode `BackupPolicyProvider` ser atualizado para usar o em vez de retornar nulo:</span><span class="sxs-lookup"><span data-stu-id="6c9e3-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="6c9e3-159">Política padrão</span><span class="sxs-lookup"><span data-stu-id="6c9e3-159">Default policy</span></span>

<span data-ttu-id="6c9e3-160">Além de fornecer políticas de `IAuthorizationPolicyProvider` autorização nomeadas, um costume precisa ser implementado `GetDefaultPolicyAsync` para fornecer uma política de autorização para `[Authorize]` atributos sem um nome de política especificado.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="6c9e3-161">Em muitos casos, esse atributo de autorização requer apenas um usuário autenticado, `RequireAuthenticatedUser`para que você possa fazer a política necessária com uma chamada para :</span><span class="sxs-lookup"><span data-stu-id="6c9e3-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="6c9e3-162">Como em todos os `IAuthorizationPolicyProvider`aspectos de um costume, você pode personalizar isso, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="6c9e3-163">Em alguns casos, pode ser desejável recuperar a `IAuthorizationPolicyProvider`política padrão de um recuo .</span><span class="sxs-lookup"><span data-stu-id="6c9e3-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="6c9e3-164">Política de recuo</span><span class="sxs-lookup"><span data-stu-id="6c9e3-164">Fallback policy</span></span>

<span data-ttu-id="6c9e3-165">Um `IAuthorizationPolicyProvider` personalizado pode `GetFallbackPolicyAsync` implementar opcionalmente para fornecer uma política usada ao [combinar políticas](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) e quando nenhuma política é especificada.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="6c9e3-166">Se `GetFallbackPolicyAsync` retornar uma política não nula, a diretiva retornada será usada pelo Middleware de autorização quando nenhuma política for especificada para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="6c9e3-167">Se nenhuma política de recuo for `null` necessária, o provedor poderá retornar ou adiar para o provedor de recuo:</span><span class="sxs-lookup"><span data-stu-id="6c9e3-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="6c9e3-168">Use um Provedor de IAutorização personalizado</span><span class="sxs-lookup"><span data-stu-id="6c9e3-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="6c9e3-169">Para usar políticas `IAuthorizationPolicyProvider`personalizadas de um , você deve:</span><span class="sxs-lookup"><span data-stu-id="6c9e3-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="6c9e3-170">Registre os `AuthorizationHandler` tipos apropriados com injeção de dependência (descrito em autorização baseada em [políticas),](xref:security/authorization/policies#authorization-handlers)como em todos os cenários de autorização baseados em políticas.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="6c9e3-171">Registre o `IAuthorizationPolicyProvider` tipo personalizado na coleção de serviços `Startup.ConfigureServices`de injeção de dependência do aplicativo (in ) para substituir o provedor de política padrão.</span><span class="sxs-lookup"><span data-stu-id="6c9e3-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="6c9e3-172">Uma amostra `IAuthorizationPolicyProvider` personalizada completa está disponível no [repositório Dotnet/Aspnetcore GitHub](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="6c9e3-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).</span></span>
