---
title: ASP.NET Blazor Núcleo de Globalização e Localização
author: guardrex
description: Aprenda a tornar os componentes razor acessíveis aos usuários em múltiplas culturas e idiomas.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 0883a67e0129590f7a3fb68689eaba8d85e5523f
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440708"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.NET Blazor Núcleo de Globalização e Localização

Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)

Os componentes da navalha podem ser acessíveis aos usuários em múltiplas culturas e idiomas. Os seguintes cenários de globalização e localização .NET estão disponíveis:

* . Sistema de recursos da NET
* Formato de número e data específicos da cultura

Atualmente, um conjunto limitado dos cenários de localização do ASP.NET Core são suportados:

* `IStringLocalizer<>`*é suportado* em Blazor aplicativos.
* `IHtmlLocalizer<>`, `IViewLocalizer<>`e a localização de Anotações de Dados são ASP.NET Blazor cenários de MVC principais e **não suportados** em aplicativos.

Para obter mais informações, consulte <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalização

BlazorA `@bind` funcionalidade executa formatos e analisa valores para exibição com base na cultura atual do usuário.

A cultura atual pode ser <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> acessada a partir da propriedade.

[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) é usado para os`<input type="{TYPE}" />`seguintes tipos de campo ( ):

* `date`
* `number`

Os tipos de campo anteriores:

* São exibidos usando suas regras de formatação baseadas no navegador apropriadas.
* Não pode conter texto de forma livre.
* Forneça características de interação do usuário com base na implementação do navegador.

Os seguintes tipos de campo têm requisitos específicos Blazor de formatação e não são suportados atualmente porque não são suportados por todos os principais navegadores:

* `datetime-local`
* `month`
* `week`

`@bind`suporta o `@bind:culture` parâmetro para <xref:System.Globalization.CultureInfo?displayProperty=fullName> fornecer um para análise e formatação de um valor. Especificar uma cultura não é recomendado `date` `number` ao usar os tipos de campo e e. `date`e `number` têm Blazor suporte embutido que fornece a cultura necessária.

## <a name="localization"></a>Localização

### <a name="opno-locblazor-webassembly"></a>BlazorWebAssembly

Por padrão, Blazora configuração Blazor de linker para aplicativos WebAssembly elimina as informações de internacionalização, exceto para locais explicitamente solicitados. Para obter mais informações e orientações sobre o <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>controle do comportamento do linker, consulte .

<!-- HOLD FOR 3.2 PREVIEW 4: Replace prior paragraph with ...

Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).

To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.

By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested. For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale. For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.

-->

### <a name="opno-locblazor-server"></a>BlazorServidor

BlazorOs aplicativos do servidor são localizados usando [o Siteization Middleware](xref:fundamentals/localization#localization-middleware). O middleware seleciona a cultura apropriada para usuários que solicitam recursos do aplicativo.

A cultura pode ser definida usando uma das seguintes abordagens:

* [Cookies](#cookies)
* [Fornecer ui para escolher a cultura](#provide-ui-to-choose-the-culture)

Para obter mais informações e exemplos, consulte <xref:fundamentals/localization>.

#### <a name="cookies"></a>Cookies

Um cookie de cultura de localização pode persistir a cultura do usuário. O cookie é criado `OnGet` pelo método da página de host do aplicativo *(Pages/Host.cshtml.cs*). O Siteization Middleware lê o cookie em solicitações subseqüentes para definir a cultura do usuário. 

O uso de um cookie garante que a conexão WebSocket possa propagar corretamente a cultura. Se os esquemas de localização forem baseados no caminho de URL ou na seqüência de consultas, o esquema pode não ser capaz de trabalhar com WebSockets, portanto, não persistir á cultura. Portanto, o uso de um cookie de cultura de localização é a abordagem recomendada.

Qualquer técnica pode ser usada para atribuir uma cultura se a cultura persistir em um cookie de localização. Se o aplicativo já tiver um esquema de localização estabelecido para o ASP.NET Core do lado do servidor, continue a usar a infra-estrutura de localização existente do aplicativo e defina o cookie de cultura de localização dentro do esquema do aplicativo.

O exemplo a seguir mostra como definir a cultura atual em um cookie que pode ser lido pelo Meio de Localização. Crie um arquivo *Páginas/_Host.cshtml.cs* com Blazor o seguinte conteúdo no aplicativo Server:

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

A localização é tratada pelo aplicativo na seguinte seqüência de eventos:

1. O navegador envia uma solicitação HTTP inicial para o aplicativo.
1. A cultura é atribuída pelo Localization Middleware.
1. O `OnGet` método em *_Host.cshtml.cs* persiste a cultura em um cookie como parte da resposta.
1. O navegador abre uma conexão Blazor WebSocket para criar uma sessão interativa do Servidor.
1. O Meio-Termo de Localização lê o cookie e atribui a cultura.
1. A Blazor sessão Servidor começa com a cultura correta.

#### <a name="provide-ui-to-choose-the-culture"></a>Fornecer ui para escolher a cultura

Para fornecer iu para permitir que um usuário selecione uma cultura, *recomenda-se uma abordagem baseada em redirecionamento.* O processo é semelhante ao que acontece em um aplicativo web quando um usuário tenta acessar um recurso seguro. O usuário é redirecionado para uma página de login e, em seguida, redirecionado de volta para o recurso original. 

O aplicativo persiste na cultura selecionada do usuário através de um redirecionamento para um controlador. O controlador define a cultura selecionada do usuário em um cookie e redireciona o usuário de volta para o URI original.

Estabeleça um ponto final HTTP no servidor para definir a cultura selecionada do usuário em um cookie e execute o redirecionamento de volta para o URI original:

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
> Use `LocalRedirect` o resultado da ação para evitar ataques de redirecionamento abertos. Para obter mais informações, consulte <xref:security/preventing-open-redirects>.

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
