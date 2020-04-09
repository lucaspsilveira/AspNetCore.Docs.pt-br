---
title: ASP.NET Blazor Núcleo de Globalização e Localização
author: guardrex
description: Aprenda a tornar os componentes razor acessíveis aos usuários em múltiplas culturas e idiomas.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 2795b21f418ccb2722e7fd0e76a77cfde58e524a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976736"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="24b57-103">ASP.NET Blazor Núcleo de Globalização e Localização</span><span class="sxs-lookup"><span data-stu-id="24b57-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="24b57-104">Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="24b57-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="24b57-105">Os componentes da navalha podem ser acessíveis aos usuários em múltiplas culturas e idiomas.</span><span class="sxs-lookup"><span data-stu-id="24b57-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="24b57-106">Os seguintes cenários de globalização e localização .NET estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="24b57-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="24b57-107">. Sistema de recursos da NET</span><span class="sxs-lookup"><span data-stu-id="24b57-107">.NET's resources system</span></span>
* <span data-ttu-id="24b57-108">Formato de número e data específicos da cultura</span><span class="sxs-lookup"><span data-stu-id="24b57-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="24b57-109">Atualmente, um conjunto limitado dos cenários de localização do ASP.NET Core são suportados:</span><span class="sxs-lookup"><span data-stu-id="24b57-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="24b57-110">`IStringLocalizer<>`*é suportado* em Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="24b57-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="24b57-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`e a localização de Anotações de Dados são ASP.NET Blazor cenários de MVC principais e **não suportados** em aplicativos.</span><span class="sxs-lookup"><span data-stu-id="24b57-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="24b57-112">Para obter mais informações, consulte <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="24b57-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="24b57-113">Globalização</span><span class="sxs-lookup"><span data-stu-id="24b57-113">Globalization</span></span>

Blazor<span data-ttu-id="24b57-114">A `@bind` funcionalidade executa formatos e analisa valores para exibição com base na cultura atual do usuário.</span><span class="sxs-lookup"><span data-stu-id="24b57-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="24b57-115">A cultura atual pode ser <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> acessada a partir da propriedade.</span><span class="sxs-lookup"><span data-stu-id="24b57-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="24b57-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) é usado para os`<input type="{TYPE}" />`seguintes tipos de campo ( ):</span><span class="sxs-lookup"><span data-stu-id="24b57-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="24b57-117">Os tipos de campo anteriores:</span><span class="sxs-lookup"><span data-stu-id="24b57-117">The preceding field types:</span></span>

* <span data-ttu-id="24b57-118">São exibidos usando suas regras de formatação baseadas no navegador apropriadas.</span><span class="sxs-lookup"><span data-stu-id="24b57-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="24b57-119">Não pode conter texto de forma livre.</span><span class="sxs-lookup"><span data-stu-id="24b57-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="24b57-120">Forneça características de interação do usuário com base na implementação do navegador.</span><span class="sxs-lookup"><span data-stu-id="24b57-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="24b57-121">Os seguintes tipos de campo têm requisitos específicos Blazor de formatação e não são suportados atualmente porque não são suportados por todos os principais navegadores:</span><span class="sxs-lookup"><span data-stu-id="24b57-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="24b57-122">`@bind`suporta o `@bind:culture` parâmetro para <xref:System.Globalization.CultureInfo?displayProperty=fullName> fornecer um para análise e formatação de um valor.</span><span class="sxs-lookup"><span data-stu-id="24b57-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="24b57-123">Especificar uma cultura não é recomendado `date` `number` ao usar os tipos de campo e e.</span><span class="sxs-lookup"><span data-stu-id="24b57-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="24b57-124">`date`e `number` têm Blazor suporte embutido que fornece a cultura necessária.</span><span class="sxs-lookup"><span data-stu-id="24b57-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="24b57-125">Localização</span><span class="sxs-lookup"><span data-stu-id="24b57-125">Localization</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="24b57-126">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="24b57-126"> WebAssembly</span></span>

Blazor<span data-ttu-id="24b57-127">Os aplicativos WebAssembly definem a cultura usando a preferência de [idioma](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)do usuário .</span><span class="sxs-lookup"><span data-stu-id="24b57-127"> WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="24b57-128">Para configurar explicitamente a cultura, `CultureInfo.DefaultThreadCurrentUICulture` `Program.Main`definir `CultureInfo.DefaultThreadCurrentCulture` e em .</span><span class="sxs-lookup"><span data-stu-id="24b57-128">To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.</span></span>

<span data-ttu-id="24b57-129">Por padrão, Blazora configuração Blazor de linker para aplicativos WebAssembly elimina as informações de internacionalização, exceto para locais explicitamente solicitados.</span><span class="sxs-lookup"><span data-stu-id="24b57-129">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="24b57-130">Para obter mais informações e orientações sobre o <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>controle do comportamento do linker, consulte .</span><span class="sxs-lookup"><span data-stu-id="24b57-130">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="24b57-131">Embora a Blazor cultura que seleciona por padrão possa ser suficiente para a maioria dos usuários, considere oferecer uma maneira para os usuários especificarem seu local preferido.</span><span class="sxs-lookup"><span data-stu-id="24b57-131">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="24b57-132">Para Blazor obter um aplicativo de exemplo do WebAssembly com um seletor de cultura, consulte o aplicativo de amostra de localização [LocSample.](https://github.com/pranavkm/LocSample)</span><span class="sxs-lookup"><span data-stu-id="24b57-132">For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="24b57-133">Servidor</span><span class="sxs-lookup"><span data-stu-id="24b57-133"> Server</span></span>

Blazor<span data-ttu-id="24b57-134">Os aplicativos do servidor são localizados usando [o Siteization Middleware](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="24b57-134"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="24b57-135">O middleware seleciona a cultura apropriada para usuários que solicitam recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="24b57-135">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="24b57-136">A cultura pode ser definida usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="24b57-136">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="24b57-137">Cookies</span><span class="sxs-lookup"><span data-stu-id="24b57-137">Cookies</span></span>](#cookies)
* [<span data-ttu-id="24b57-138">Fornecer ui para escolher a cultura</span><span class="sxs-lookup"><span data-stu-id="24b57-138">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="24b57-139">Para obter mais informações e exemplos, consulte <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="24b57-139">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="24b57-140">Cookies</span><span class="sxs-lookup"><span data-stu-id="24b57-140">Cookies</span></span>

<span data-ttu-id="24b57-141">Um cookie de cultura de localização pode persistir a cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="24b57-141">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="24b57-142">O cookie é criado `OnGet` pelo método da página de host do aplicativo *(Pages/Host.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="24b57-142">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="24b57-143">O Siteization Middleware lê o cookie em solicitações subseqüentes para definir a cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="24b57-143">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="24b57-144">O uso de um cookie garante que a conexão WebSocket possa propagar corretamente a cultura.</span><span class="sxs-lookup"><span data-stu-id="24b57-144">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="24b57-145">Se os esquemas de localização forem baseados no caminho de URL ou na seqüência de consultas, o esquema pode não ser capaz de trabalhar com WebSockets, portanto, não persistir á cultura.</span><span class="sxs-lookup"><span data-stu-id="24b57-145">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="24b57-146">Portanto, o uso de um cookie de cultura de localização é a abordagem recomendada.</span><span class="sxs-lookup"><span data-stu-id="24b57-146">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="24b57-147">Qualquer técnica pode ser usada para atribuir uma cultura se a cultura persistir em um cookie de localização.</span><span class="sxs-lookup"><span data-stu-id="24b57-147">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="24b57-148">Se o aplicativo já tiver um esquema de localização estabelecido para o ASP.NET Core do lado do servidor, continue a usar a infra-estrutura de localização existente do aplicativo e defina o cookie de cultura de localização dentro do esquema do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="24b57-148">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="24b57-149">O exemplo a seguir mostra como definir a cultura atual em um cookie que pode ser lido pelo Meio de Localização.</span><span class="sxs-lookup"><span data-stu-id="24b57-149">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="24b57-150">Crie um arquivo *Páginas/_Host.cshtml.cs* com Blazor o seguinte conteúdo no aplicativo Server:</span><span class="sxs-lookup"><span data-stu-id="24b57-150">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="24b57-151">A localização é tratada pelo aplicativo na seguinte seqüência de eventos:</span><span class="sxs-lookup"><span data-stu-id="24b57-151">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="24b57-152">O navegador envia uma solicitação HTTP inicial para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="24b57-152">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="24b57-153">A cultura é atribuída pelo Localization Middleware.</span><span class="sxs-lookup"><span data-stu-id="24b57-153">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="24b57-154">O `OnGet` método em *_Host.cshtml.cs* persiste a cultura em um cookie como parte da resposta.</span><span class="sxs-lookup"><span data-stu-id="24b57-154">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="24b57-155">O navegador abre uma conexão Blazor WebSocket para criar uma sessão interativa do Servidor.</span><span class="sxs-lookup"><span data-stu-id="24b57-155">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="24b57-156">O Meio-Termo de Localização lê o cookie e atribui a cultura.</span><span class="sxs-lookup"><span data-stu-id="24b57-156">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="24b57-157">A Blazor sessão Servidor começa com a cultura correta.</span><span class="sxs-lookup"><span data-stu-id="24b57-157">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="24b57-158">Fornecer ui para escolher a cultura</span><span class="sxs-lookup"><span data-stu-id="24b57-158">Provide UI to choose the culture</span></span>

<span data-ttu-id="24b57-159">Para fornecer iu para permitir que um usuário selecione uma cultura, *recomenda-se uma abordagem baseada em redirecionamento.*</span><span class="sxs-lookup"><span data-stu-id="24b57-159">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="24b57-160">O processo é semelhante ao que acontece em um aplicativo web quando um usuário tenta acessar um recurso seguro.</span><span class="sxs-lookup"><span data-stu-id="24b57-160">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="24b57-161">O usuário é redirecionado para uma página de login e, em seguida, redirecionado de volta para o recurso original.</span><span class="sxs-lookup"><span data-stu-id="24b57-161">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="24b57-162">O aplicativo persiste na cultura selecionada do usuário através de um redirecionamento para um controlador.</span><span class="sxs-lookup"><span data-stu-id="24b57-162">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="24b57-163">O controlador define a cultura selecionada do usuário em um cookie e redireciona o usuário de volta para o URI original.</span><span class="sxs-lookup"><span data-stu-id="24b57-163">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="24b57-164">Estabeleça um ponto final HTTP no servidor para definir a cultura selecionada do usuário em um cookie e execute o redirecionamento de volta para o URI original:</span><span class="sxs-lookup"><span data-stu-id="24b57-164">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="24b57-165">Use `LocalRedirect` o resultado da ação para evitar ataques de redirecionamento abertos.</span><span class="sxs-lookup"><span data-stu-id="24b57-165">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="24b57-166">Para obter mais informações, consulte <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="24b57-166">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="24b57-167">O componente a seguir mostra um exemplo de como executar o redirecionamento inicial quando o usuário seleciona uma cultura:</span><span class="sxs-lookup"><span data-stu-id="24b57-167">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="24b57-168">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="24b57-168">Additional resources</span></span>

* <xref:fundamentals/localization>
