<span data-ttu-id="55e87-101">O componente `FetchData` mostra como:</span><span class="sxs-lookup"><span data-stu-id="55e87-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="55e87-102">Provisionar um token de acesso.</span><span class="sxs-lookup"><span data-stu-id="55e87-102">Provision an access token.</span></span>
* <span data-ttu-id="55e87-103">Use o token de acesso para chamar uma API de recurso protegido no aplicativo do *servidor* .</span><span class="sxs-lookup"><span data-stu-id="55e87-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="55e87-104">A diretiva `@attribute [Authorize]` indica para o sistema de autorização Webassembly mais incrivelmente que o usuário deve ser autorizado para visitar esse componente.</span><span class="sxs-lookup"><span data-stu-id="55e87-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="55e87-105">A presença do atributo no aplicativo *cliente* não impede que a API no servidor seja chamada sem credenciais apropriadas.</span><span class="sxs-lookup"><span data-stu-id="55e87-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="55e87-106">O aplicativo de *servidor* também deve usar `[Authorize]` nos pontos de extremidade apropriados para protegê-los corretamente.</span><span class="sxs-lookup"><span data-stu-id="55e87-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="55e87-107">`AuthenticationService.RequestAccessToken();` cuida da solicitação de um token de acesso que pode ser adicionado à solicitação para chamar a API.</span><span class="sxs-lookup"><span data-stu-id="55e87-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="55e87-108">Se o token for armazenado em cache ou o serviço for capaz de provisionar um novo token de acesso sem interação do usuário, a solicitação de token terá sucesso.</span><span class="sxs-lookup"><span data-stu-id="55e87-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="55e87-109">Caso contrário, a solicitação de token falhará.</span><span class="sxs-lookup"><span data-stu-id="55e87-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="55e87-110">Para obter o token real a ser incluído na solicitação, o aplicativo deve verificar se a solicitação foi bem-sucedida chamando `tokenResult.TryGetToken(out var token)`.</span><span class="sxs-lookup"><span data-stu-id="55e87-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="55e87-111">Se a solicitação tiver sido bem-sucedida, a variável de token será populada com o token de acesso.</span><span class="sxs-lookup"><span data-stu-id="55e87-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="55e87-112">A propriedade `Value` do token expõe a cadeia de caracteres literal a ser incluída no cabeçalho da solicitação `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="55e87-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="55e87-113">Se a solicitação falhou porque o token não pôde ser provisionado sem interação do usuário, o resultado do token contém uma URL de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="55e87-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="55e87-114">Navegar até essa URL leva o usuário para a página de logon e de volta para a página atual após uma autenticação bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="55e87-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
...
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

<span data-ttu-id="55e87-115">Para obter mais informações, consulte [salvar o estado do aplicativo antes de uma operação de autenticação](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span><span class="sxs-lookup"><span data-stu-id="55e87-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
