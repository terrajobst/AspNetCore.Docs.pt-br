<span data-ttu-id="e827a-101">O componente de `App` (*app. Razor*) é semelhante ao componente `App` encontrado em aplicativos de servidor mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="e827a-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="e827a-102">O componente `CascadingAuthenticationState` gerencia a exposição do `AuthenticationState` ao restante do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e827a-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="e827a-103">O componente `AuthorizeRouteView` garante que o usuário atual esteja autorizado a acessar uma determinada página ou, de outra forma, renderiza o componente `RedirectToLogin`.</span><span class="sxs-lookup"><span data-stu-id="e827a-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="e827a-104">O componente `RedirectToLogin` gerencia o redirecionamento de usuários não autorizados para a página de logon.</span><span class="sxs-lookup"><span data-stu-id="e827a-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <RedirectToLogin />
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
