<span data-ttu-id="e871c-101">A página produzida pelo `Authentication` componente (*páginas/autenticação. Razor*) define as rotas necessárias para lidar com estágios de autenticação diferentes.</span><span class="sxs-lookup"><span data-stu-id="e871c-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="e871c-102">O `RemoteAuthenticatorView` componente:</span><span class="sxs-lookup"><span data-stu-id="e871c-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="e871c-103">É fornecido pelo pacote [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="e871c-103">Is provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="e871c-104">Gerencia a execução das ações apropriadas em cada estágio de autenticação.</span><span class="sxs-lookup"><span data-stu-id="e871c-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
