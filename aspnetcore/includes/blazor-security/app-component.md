<span data-ttu-id="9985c-101">O `App` componente ( `App.razor` ) é semelhante ao `App` componente encontrado nos aplicativos de servidor mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="9985c-101">The `App` component (`App.razor`) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="9985c-102">O <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> componente gerencia a exposição do <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> ao restante do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9985c-102">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component manages exposing the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> to the rest of the app.</span></span>
* <span data-ttu-id="9985c-103">O <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> componente garante que o usuário atual esteja autorizado a acessar uma determinada página ou, de outra forma, renderiza o `RedirectToLogin` componente.</span><span class="sxs-lookup"><span data-stu-id="9985c-103">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="9985c-104">O `RedirectToLogin` componente gerencia o redirecionamento de usuários não autorizados para a página de logon.</span><span class="sxs-lookup"><span data-stu-id="9985c-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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
