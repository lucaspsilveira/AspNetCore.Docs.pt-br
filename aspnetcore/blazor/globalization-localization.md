---
title: ASP.NET Core Blazor globalização e localização
author: guardrex
description: Saiba como tornar Razor os componentes acessíveis para os usuários em várias culturas e linguagens.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: b39695f8b506744b4af27a1d7e09bfac9594d7ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772485"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a>ASP.NET Core Blazor globalização e localização

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Razoros componentes podem se tornar acessíveis para os usuários em várias culturas e linguagens. Os seguintes cenários de globalização e localização do .NET estão disponíveis:

* . Sistema de recursos da rede
* Formatação de número e data específicos da cultura

No momento, há suporte para um conjunto limitado de cenários de localização de ASP.NET Core:

* `IStringLocalizer<>`tem *suporte* em Blazor aplicativos.
* `IHtmlLocalizer<>`, `IViewLocalizer<>`, e a localização de anotações de dados são ASP.NET Core cenários MVC e **não têm suporte** em Blazor aplicativos.

Para obter mais informações, consulte <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalização

Blazora `@bind` funcionalidade do executa formatos e analisa valores para exibição com base na cultura atual do usuário.

A cultura atual pode ser acessada <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> a partir da propriedade.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) é usado para os seguintes tipos de campo`<input type="{TYPE}" />`():

* `date`
* `number`

Os tipos de campo anteriores:

* São exibidos usando suas regras de formatação baseadas em navegador apropriadas.
* Não pode conter texto de forma livre.
* Fornecer características de interação do usuário com base na implementação do navegador.

Os seguintes tipos de campo têm requisitos de formatação específicos e não têm Blazor suporte no momento porque não têm suporte de todos os principais navegadores:

* `datetime-local`
* `month`
* `week`

`@bind`dá suporte `@bind:culture` ao parâmetro para fornecer <xref:System.Globalization.CultureInfo?displayProperty=fullName> um para análise e formatação de um valor. Não é recomendável especificar uma cultura ao `date` usar `number` os tipos de campo e. `date`e `number` têm Blazor suporte interno que fornece a cultura necessária.

## <a name="localization"></a>Localização

### <a name="blazor-webassembly"></a>BlazorWebassembly

BlazorOs aplicativos Webassembly definem a cultura usando a [preferência de idioma](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)do usuário.

Para configurar explicitamente a cultura, defina `CultureInfo.DefaultThreadCurrentCulture` e `CultureInfo.DefaultThreadCurrentUICulture` em `Program.Main`.

Por padrão, Blazora configuração do vinculador Blazor para aplicativos Webassembly retira informações de internacionalização, exceto as localidades explicitamente solicitadas. Para obter mais informações e orientação sobre como controlar o comportamento do vinculador <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>, consulte.

Embora a cultura que Blazor seleciona por padrão possa ser suficiente para a maioria dos usuários, considere a possibilidade de oferecer uma maneira para os usuários especificarem sua localidade preferida. Para um Blazor aplicativo de exemplo Webassembly com um seletor de cultura, consulte o aplicativo de exemplo de localização [LocSample](https://github.com/pranavkm/LocSample) .

### <a name="blazor-server"></a>BlazorServidor

BlazorOs aplicativos de servidor são localizados usando o [middleware de localização](xref:fundamentals/localization#localization-middleware). O middleware seleciona a cultura apropriada para os usuários que solicitam recursos do aplicativo.

A cultura pode ser definida usando uma das seguintes abordagens:

* [Cookies](#cookies)
* [Fornecer interface do usuário para escolher a cultura](#provide-ui-to-choose-the-culture)

Para obter mais informações e exemplos, consulte <xref:fundamentals/localization>.

#### <a name="cookies"></a>Cookies

Um cookie de cultura de localização pode persistir a cultura do usuário. O cookie é criado pelo `OnGet` método da página host do aplicativo (*pages/host. cshtml. cs*). O middleware de localização lê o cookie em solicitações subsequentes para definir a cultura do usuário. 

O uso de um cookie garante que a conexão WebSocket possa propagar corretamente a cultura. Se os esquemas de localização forem baseados no caminho da URL ou na cadeia de caracteres de consulta, o esquema pode não ser capaz de trabalhar com WebSockets, portanto, falha ao persistir a cultura. Portanto, o uso de um cookie de cultura de localização é a abordagem recomendada.

Qualquer técnica pode ser usada para atribuir uma cultura se a cultura persistir em um cookie de localização. Se o aplicativo já tiver um esquema de localização estabelecido para ASP.NET Core do lado do servidor, continue a usar a infraestrutura de localização existente do aplicativo e defina o cookie de cultura de localização no esquema do aplicativo.

O exemplo a seguir mostra como definir a cultura atual em um cookie que pode ser lido pelo middleware de localização. Crie um arquivo *pages/_Host. cshtml. cs* com o seguinte conteúdo no aplicativo Blazor do servidor:

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

A localização é manipulada pelo aplicativo na seguinte sequência de eventos:

1. O navegador envia uma solicitação HTTP inicial para o aplicativo.
1. A cultura é atribuída pelo middleware de localização.
1. O `OnGet` método em *_Host. cshtml. cs* persiste a cultura em um cookie como parte da resposta.
1. O navegador abre uma conexão WebSocket para criar uma sessão Blazor de servidor interativa.
1. O middleware de localização lê o cookie e atribui a cultura.
1. A Blazor sessão de servidor começa com a cultura correta.

#### <a name="provide-ui-to-choose-the-culture"></a>Fornecer interface do usuário para escolher a cultura

Para fornecer à interface do usuário a fim de permitir a seleção de uma cultura, é recomendável uma *abordagem baseada em redirecionamento* . O processo é semelhante ao que acontece em um aplicativo Web quando um usuário tenta acessar um recurso seguro. O usuário é redirecionado para uma página de entrada e Redirecionado de volta para o recurso original. 

O aplicativo persiste a cultura selecionada do usuário por meio de um redirecionamento para um controlador. O controlador define a cultura selecionada do usuário em um cookie e redireciona o usuário de volta para o URI original.

Estabeleça um ponto de extremidade HTTP no servidor para definir a cultura selecionada do usuário em um cookie e execute o redirecionamento de volta para o URI original:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Use o `LocalRedirect` resultado da ação para evitar ataques de redirecionamento abertos. Para obter mais informações, consulte <xref:security/preventing-open-redirects>.

O componente a seguir mostra um exemplo de como executar o redirecionamento inicial quando o usuário seleciona uma cultura:

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/localization>
