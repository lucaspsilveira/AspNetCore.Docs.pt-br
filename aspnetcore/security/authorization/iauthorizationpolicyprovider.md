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
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Provedores de políticas de autorização personalizadas que usam iAuthorizationPolicyProvider no ASP.NET Core 

Por [Mike Rousos](https://github.com/mjrousos)

Normalmente, ao usar a autorização baseada em `AuthorizationOptions.AddPolicy` [políticas,](xref:security/authorization/policies)as políticas são registradas ligando como parte da configuração do serviço de autorização. Em alguns cenários, pode não ser possível (ou desejável) registrar todas as políticas de autorização dessa forma. Nesses casos, você pode `IAuthorizationPolicyProvider` usar um costume para controlar como as políticas de autorização são fornecidas.

Exemplos de cenários em que um [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personalizado pode ser útil incluem:

* Usando um serviço externo para fornecer avaliação de políticas.
* Usando uma grande variedade de políticas (para diferentes números de quartos ou idades, por `AuthorizationOptions.AddPolicy` exemplo), por isso não faz sentido adicionar cada política de autorização individual com uma chamada.
* Criar políticas em tempo de execução com base em informações em uma fonte de dados externo (como um banco de dados) ou determinar os requisitos de autorização dinamicamente através de outro mecanismo.

[Exibir ou baixar](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) o código de exemplo do [repositório AspNetCore GitHub](https://github.com/dotnet/AspNetCore). Baixe o arquivo ZIP dotnet/AspNetCore. Descompacte o arquivo. Navegue até a pasta de projeto *src/Security/samples/CustomPolicyProvider.*

## <a name="customize-policy-retrieval"></a>Personalizar a recuperação de políticas

ASP.NET os aplicativos Core `IAuthorizationPolicyProvider` usam uma implementação da interface para recuperar políticas de autorização. Por padrão, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) é registrado e usado. `DefaultAuthorizationPolicyProvider`retorna políticas `AuthorizationOptions` do fornecido `IServiceCollection.AddAuthorization` em uma chamada.

Personalize esse comportamento registrando `IAuthorizationPolicyProvider` uma implementação diferente no recipiente de injeção de [dependência](xref:fundamentals/dependency-injection) do aplicativo. 

A `IAuthorizationPolicyProvider` interface contém três APIs:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) retorna uma política de autorização para um determinado nome.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) retorna a política de autorização `[Authorize]` padrão (a diretiva usada para atributos sem uma política especificada). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) retorna a política de autorização de recuo (a política usada pelo Middleware de autorização quando nenhuma política é especificada). 

Ao implementar essas APIs, você pode personalizar como as políticas de autorização são fornecidas.

## <a name="parameterized-authorize-attribute-example"></a>Exemplo de atributo de autorização parametrizada

Um cenário `IAuthorizationPolicyProvider` onde é útil `[Authorize]` é habilitar atributos personalizados cujos requisitos dependem de um parâmetro. Por exemplo, na documentação [de autorização baseada em políticas,](xref:security/authorization/policies) uma política baseada na idade ("Pelo menos21") foi usada como amostra. Se diferentes ações de controlador em um aplicativo devem ser disponibilizadas para usuários de *diferentes* idades, pode ser útil ter muitas políticas diferentes baseadas na idade. Em vez de registrar todas as diferentes políticas `AuthorizationOptions`baseadas em idade que o `IAuthorizationPolicyProvider`aplicativo precisará, você pode gerar as políticas dinamicamente com um costume . Para facilitar o uso das políticas, você pode anotar `[MinimumAgeAuthorize(20)]`ações com atributo de autorização personalizado como .

## <a name="custom-authorization-attributes"></a>Atributos de Autorização Personalizada

As políticas de autorização são identificadas por seus nomes. O `MinimumAgeAuthorizeAttribute` costume descrito anteriormente precisa mapear argumentos em uma seqüência que pode ser usada para recuperar a política de autorização correspondente. Você pode fazer isso `AuthorizeAttribute` derivando `Age` e fazendo `AuthorizeAttribute.Policy` a propriedade envolver a propriedade.

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

Este tipo de `Policy` atributo tem uma seqüência baseada no prefixo codificado rígido (`"MinimumAge"`) e um inteiro passado através do construtor.

Você pode aplicá-lo a `Authorize` ações da mesma forma que outros atributos, exceto que ele toma um inteiro como parâmetro.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Provedor de políticas de iautorização personalizada

O `MinimumAgeAuthorizeAttribute` costume facilita a solicitação de políticas de autorização para qualquer idade mínima desejada. O próximo problema a ser resolvido é garantir que as políticas de autorização estejam disponíveis para todas essas idades diferentes. É aqui `IAuthorizationPolicyProvider` que um é útil.

Ao `MinimumAgeAuthorizationAttribute`utilizar , os nomes da `"MinimumAge" + Age`política de `IAuthorizationPolicyProvider` autorização seguirão o padrão, de modo que o costume deve gerar políticas de autorização por:

* Analisar a idade do nome da apólice.
* Usando `AuthorizationPolicyBuilder` para criar um novo`AuthorizationPolicy`
* Neste e nos exemplos seguintes, presume-se que o usuário seja autenticado através de um cookie. O `AuthorizationPolicyBuilder` deve ser construído com pelo menos um nome de esquema de autorização ou sempre ter sucesso. Caso contrário, não há informações sobre como fornecer um desafio ao usuário e uma exceção será lançada.
* Adicionando requisitos à apólice `AuthorizationPolicyBuilder.AddRequirements`com base na idade com . Em outros cenários, `RequireClaim` `RequireRole`você `RequireUserName` pode usar , ou em vez disso.

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

## <a name="multiple-authorization-policy-providers"></a>Vários provedores de políticas de autorização

Ao usar `IAuthorizationPolicyProvider` implementações personalizadas, tenha em mente `IAuthorizationPolicyProvider`que ASP.NET Core usa apenas uma instância de . Se um provedor personalizado não for capaz de fornecer políticas de autorização para todos os nomes de políticas que serão usados, ele deve adiar para um provedor de backup. 

Por exemplo, considere um aplicativo que precise tanto de políticas de idade personalizadas quanto de recuperação de políticas baseadas em papéis mais tradicionais. Tal aplicativo poderia usar um provedor de política de autorização personalizado que:

* Tentativas de analisar nomes de políticas. 
* Chama para um provedor `DefaultAuthorizationPolicyProvider`de apólice diferente (como ) se o nome da apólice não contiver uma idade.

O `IAuthorizationPolicyProvider` exemplo de implementação mostrado `DefaultAuthorizationPolicyProvider` acima pode ser atualizado para usar o criando um provedor de política de backup em seu construtor (a ser usado no caso de o nome da diretiva não corresponder ao padrão esperado de 'MinimumAge' + idade).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Em seguida, o `GetPolicyAsync` método pode `BackupPolicyProvider` ser atualizado para usar o em vez de retornar nulo:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Política padrão

Além de fornecer políticas de `IAuthorizationPolicyProvider` autorização nomeadas, um costume precisa ser implementado `GetDefaultPolicyAsync` para fornecer uma política de autorização para `[Authorize]` atributos sem um nome de política especificado.

Em muitos casos, esse atributo de autorização requer apenas um usuário autenticado, `RequireAuthenticatedUser`para que você possa fazer a política necessária com uma chamada para :

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Como em todos os `IAuthorizationPolicyProvider`aspectos de um costume, você pode personalizar isso, conforme necessário. Em alguns casos, pode ser desejável recuperar a `IAuthorizationPolicyProvider`política padrão de um recuo .

## <a name="fallback-policy"></a>Política de recuo

Um `IAuthorizationPolicyProvider` personalizado pode `GetFallbackPolicyAsync` implementar opcionalmente para fornecer uma política usada ao [combinar políticas](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) e quando nenhuma política é especificada. Se `GetFallbackPolicyAsync` retornar uma política não nula, a diretiva retornada será usada pelo Middleware de autorização quando nenhuma política for especificada para a solicitação.

Se nenhuma política de recuo for `null` necessária, o provedor poderá retornar ou adiar para o provedor de recuo:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Use um Provedor de IAutorização personalizado

Para usar políticas `IAuthorizationPolicyProvider`personalizadas de um , você deve:

* Registre os `AuthorizationHandler` tipos apropriados com injeção de dependência (descrito em autorização baseada em [políticas),](xref:security/authorization/policies#authorization-handlers)como em todos os cenários de autorização baseados em políticas.
* Registre o `IAuthorizationPolicyProvider` tipo personalizado na coleção de serviços `Startup.ConfigureServices`de injeção de dependência do aplicativo (in ) para substituir o provedor de política padrão.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Uma amostra `IAuthorizationPolicyProvider` personalizada completa está disponível no [repositório Dotnet/Aspnetcore GitHub](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).
