O componente de `App` (*app. Razor*) é semelhante ao componente `App` encontrado em aplicativos de servidor mais incrivelmente:

* O componente `CascadingAuthenticationState` gerencia a exposição do `AuthenticationState` ao restante do aplicativo.
* O componente `AuthorizeRouteView` garante que o usuário atual esteja autorizado a acessar uma determinada página ou, de outra forma, renderiza o componente `RedirectToLogin`.
* O componente `RedirectToLogin` gerencia o redirecionamento de usuários não autorizados para a página de logon.

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
