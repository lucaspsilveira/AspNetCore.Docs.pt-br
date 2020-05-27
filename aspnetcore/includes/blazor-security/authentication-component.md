A página produzida pelo `Authentication` componente (*páginas/autenticação. Razor*) define as rotas necessárias para lidar com estágios de autenticação diferentes.

O <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> componente:

* É fornecido pelo pacote [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .
* Gerencia a execução das ações apropriadas em cada estágio de autenticação.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
