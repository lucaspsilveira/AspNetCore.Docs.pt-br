<span data-ttu-id="9466b-101">O `LoginDisplay` componente (*Shared/LoginDisplay. Razor*) é renderizado no `MainLayout` componente (*Shared/MainLayout. Razor*) e gerencia os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="9466b-101">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="9466b-102">Para usuários autenticados:</span><span class="sxs-lookup"><span data-stu-id="9466b-102">For authenticated users:</span></span>
  * <span data-ttu-id="9466b-103">Exibe o nome de usuário atual.</span><span class="sxs-lookup"><span data-stu-id="9466b-103">Displays the current username.</span></span>
  * <span data-ttu-id="9466b-104">Oferece um botão para fazer logoff do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9466b-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="9466b-105">Para usuários anônimos, o oferece a opção de fazer logon.</span><span class="sxs-lookup"><span data-stu-id="9466b-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
