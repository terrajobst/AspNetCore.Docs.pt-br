O componente `FetchData` mostra como:

* Provisionar um token de acesso.
* Use o token de acesso para chamar uma API de recurso protegido no aplicativo do *servidor* .

A diretiva `@attribute [Authorize]` indica para o sistema de autorização Webassembly mais incrivelmente que o usuário deve ser autorizado para visitar esse componente. A presença do atributo no aplicativo *cliente* não impede que a API no servidor seja chamada sem credenciais apropriadas. O aplicativo de *servidor* também deve usar `[Authorize]` nos pontos de extremidade apropriados para protegê-los corretamente.

`AuthenticationService.RequestAccessToken();` cuida da solicitação de um token de acesso que pode ser adicionado à solicitação para chamar a API. Se o token for armazenado em cache ou o serviço for capaz de provisionar um novo token de acesso sem interação do usuário, a solicitação de token terá sucesso. Caso contrário, a solicitação de token falhará.

Para obter o token real a ser incluído na solicitação, o aplicativo deve verificar se a solicitação foi bem-sucedida chamando `tokenResult.TryGetToken(out var token)`. 

Se a solicitação tiver sido bem-sucedida, a variável de token será populada com o token de acesso. A propriedade `Value` do token expõe a cadeia de caracteres literal a ser incluída no cabeçalho da solicitação `Authorization`.

Se a solicitação falhou porque o token não pôde ser provisionado sem interação do usuário, o resultado do token contém uma URL de redirecionamento. Navegar até essa URL leva o usuário para a página de logon e de volta para a página atual após uma autenticação bem-sucedida.

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

Para obter mais informações, consulte [salvar o estado do aplicativo antes de uma operação de autenticação](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).
