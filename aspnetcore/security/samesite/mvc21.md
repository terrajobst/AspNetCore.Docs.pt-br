---
title: Exemplo de cookie do ASP.NET Core 2,1 MVC SameSite
author: rick-anderson
description: Exemplo de cookie do ASP.NET Core 2,1 MVC SameSite
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: security/samesite/mvc21
ms.openlocfilehash: 14f3d3d27d5740519e1ba57529d5694b4cdeb9ec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667744"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a>Exemplo de cookie do ASP.NET Core 2,1 MVC SameSite

ASP.NET Core 2,1 tem suporte interno para o atributo [SameSite](https://www.owasp.org/index.php/SameSite) , mas foi gravado no padrão original. O [comportamento de patches](https://github.com/dotnet/aspnetcore/issues/8212) alterou o significado de `SameSite.None` para emitir o atributo sameSite com um valor de `None`, em vez de não emitir o valor de forma alguma. Se você quiser não emitir o valor, poderá definir a propriedade `SameSite` em um cookie como-1.

## <a name="sampleCode"></a>Gravando o atributo SameSite

Veja a seguir um exemplo de como escrever um atributo SameSite em um cookie:

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a>Definindo a autenticação de cookie e cookies de estado de sessão

Autenticação de cookie, estado de sessão e [vários outros componentes](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) definem suas opções de sameSite por meio de opções de cookie, por exemplo

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

No código anterior, a autenticação de cookie e o estado de sessão definem seu atributo sameSite como `None`, emitindo o atributo com um valor de `None` e também definimos o atributo Secure como true.

### <a name="run-the-sample"></a>Executar o exemplo

Se você executar o [projeto de exemplo](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), carregue o depurador do navegador na página inicial e use-o para exibir a coleção de cookies para o site. Para fazer isso no Edge e no Chrome, pressione `F12` selecione a guia `Application` e clique na URL do site na opção `Cookies` na seção `Storage`.

![Lista de cookies do depurador do navegador](BrowserDebugger.png)

Você pode ver na imagem acima que o cookie criado pelo exemplo ao clicar no botão "Create SameSite cookie" tem um valor de atributo SameSite de `Lax`, correspondendo ao valor definido no código de [exemplo](#sampleCode).

## <a name="interception"></a>Interceptando cookies

Para interceptar cookies, para ajustar o valor None de acordo com seu suporte no agente de navegador do usuário, você deve usar o middleware `CookiePolicy`. Isso deve ser colocado no pipeline de solicitação HTTP **antes** de quaisquer componentes que gravam cookies e configurados dentro de `ConfigureServices()`.

Para inseri-lo no pipeline, use `app.UseCookiePolicy()` no método `Configure(IApplicationBuilder, IHostingEnvironment)` no [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs). Por exemplo:

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

Em seguida, no `ConfigureServices(IServiceCollection services)` configure a política de cookie para chamar uma classe auxiliar quando os cookies são anexados ou excluídos. Por exemplo:

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

A função auxiliar `CheckSameSite(HttpContext, CookieOptions)`:

* É chamado quando os cookies são anexados à solicitação ou excluídos da solicitação.
* Verifica se a propriedade `SameSite` está definida como `None`.
* Se `SameSite` for definido como `None` e o agente do usuário atual for conhecido por não oferecer suporte ao valor do atributo None. A verificação é feita usando a classe [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :
  * Define `SameSite` para não emitir o valor definindo a propriedade como `(SameSiteMode)(-1)`

## <a name="targeting-net-framework"></a>Direcionamento .NET Framework

ASP.NET Core e System. Web (ASP.NET clássico) têm implementações independentes de SameSite. Os patches do SameSite KB para .NET Framework não são necessários se usar ASP.NET Core nem o requisito de versão de estrutura mínima do System. Web SameSite (.NET 4.7.2) se aplicam ao ASP.NET Core.

ASP.NET Core no .NET requer a atualização das dependências do pacote NuGet para obter as correções apropriadas.

Para obter as alterações de ASP.NET Core para .NET Framework Verifique se você tem uma referência direta para os pacotes e versões de patch (2.1.14 ou versões posteriores 2,1).

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a>Mais informações
 
O [Chrome updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core documentação do SameSite](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core notificação de alteração do SameSite 2,1](https://github.com/dotnet/aspnetcore/issues/8212)