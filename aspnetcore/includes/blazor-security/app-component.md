O `App` componente (*app. Razor*) é semelhante ao `App` componente encontrado nos aplicativos de servidor mais incrivelmente:

* O `CascadingAuthenticationState` componente gerencia a exposição do `AuthenticationState` ao restante do aplicativo.
* O `AuthorizeRouteView` componente garante que o usuário atual esteja autorizado a acessar uma determinada página ou, de outra forma, renderiza o `RedirectToLogin` componente.
* O `RedirectToLogin` componente gerencia o redirecionamento de usuários não autorizados para a página de logon.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
