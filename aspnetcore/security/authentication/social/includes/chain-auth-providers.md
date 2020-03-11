## <a name="multiple-authentication-providers"></a><span data-ttu-id="3e0f2-101">Vários provedores de autenticação</span><span class="sxs-lookup"><span data-stu-id="3e0f2-101">Multiple authentication providers</span></span>

<span data-ttu-id="3e0f2-102">Caso o aplicativo exija vários provedores, encadeie os métodos de extensão do provedor em [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span><span class="sxs-lookup"><span data-stu-id="3e0f2-102">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
