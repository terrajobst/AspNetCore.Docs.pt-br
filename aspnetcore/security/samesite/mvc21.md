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
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a><span data-ttu-id="60661-103">Exemplo de cookie do ASP.NET Core 2,1 MVC SameSite</span><span class="sxs-lookup"><span data-stu-id="60661-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="60661-104">ASP.NET Core 2,1 tem suporte interno para o atributo [SameSite](https://www.owasp.org/index.php/SameSite) , mas foi gravado no padrão original.</span><span class="sxs-lookup"><span data-stu-id="60661-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="60661-105">O [comportamento de patches](https://github.com/dotnet/aspnetcore/issues/8212) alterou o significado de `SameSite.None` para emitir o atributo sameSite com um valor de `None`, em vez de não emitir o valor de forma alguma.</span><span class="sxs-lookup"><span data-stu-id="60661-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="60661-106">Se você quiser não emitir o valor, poderá definir a propriedade `SameSite` em um cookie como-1.</span><span class="sxs-lookup"><span data-stu-id="60661-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

## <a name="sampleCode"></a><span data-ttu-id="60661-107">Gravando o atributo SameSite</span><span class="sxs-lookup"><span data-stu-id="60661-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="60661-108">Veja a seguir um exemplo de como escrever um atributo SameSite em um cookie:</span><span class="sxs-lookup"><span data-stu-id="60661-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

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

## <a name="setting-cookie-authentication-and-session-state-cookies"></a><span data-ttu-id="60661-109">Definindo a autenticação de cookie e cookies de estado de sessão</span><span class="sxs-lookup"><span data-stu-id="60661-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="60661-110">Autenticação de cookie, estado de sessão e [vários outros componentes](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) definem suas opções de sameSite por meio de opções de cookie, por exemplo</span><span class="sxs-lookup"><span data-stu-id="60661-110">Cookie authentication, session state and [various other components](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

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

<span data-ttu-id="60661-111">No código anterior, a autenticação de cookie e o estado de sessão definem seu atributo sameSite como `None`, emitindo o atributo com um valor de `None` e também definimos o atributo Secure como true.</span><span class="sxs-lookup"><span data-stu-id="60661-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="60661-112">Executar o exemplo</span><span class="sxs-lookup"><span data-stu-id="60661-112">Run the sample</span></span>

<span data-ttu-id="60661-113">Se você executar o [projeto de exemplo](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), carregue o depurador do navegador na página inicial e use-o para exibir a coleção de cookies para o site.</span><span class="sxs-lookup"><span data-stu-id="60661-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="60661-114">Para fazer isso no Edge e no Chrome, pressione `F12` selecione a guia `Application` e clique na URL do site na opção `Cookies` na seção `Storage`.</span><span class="sxs-lookup"><span data-stu-id="60661-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Lista de cookies do depurador do navegador](BrowserDebugger.png)

<span data-ttu-id="60661-116">Você pode ver na imagem acima que o cookie criado pelo exemplo ao clicar no botão "Create SameSite cookie" tem um valor de atributo SameSite de `Lax`, correspondendo ao valor definido no código de [exemplo](#sampleCode).</span><span class="sxs-lookup"><span data-stu-id="60661-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="interception"></a><span data-ttu-id="60661-117">Interceptando cookies</span><span class="sxs-lookup"><span data-stu-id="60661-117">Intercepting cookies</span></span>

<span data-ttu-id="60661-118">Para interceptar cookies, para ajustar o valor None de acordo com seu suporte no agente de navegador do usuário, você deve usar o middleware `CookiePolicy`.</span><span class="sxs-lookup"><span data-stu-id="60661-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="60661-119">Isso deve ser colocado no pipeline de solicitação HTTP **antes** de quaisquer componentes que gravam cookies e configurados dentro de `ConfigureServices()`.</span><span class="sxs-lookup"><span data-stu-id="60661-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="60661-120">Para inseri-lo no pipeline, use `app.UseCookiePolicy()` no método `Configure(IApplicationBuilder, IHostingEnvironment)` no [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="60661-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="60661-121">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="60661-121">For example:</span></span>

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

<span data-ttu-id="60661-122">Em seguida, no `ConfigureServices(IServiceCollection services)` configure a política de cookie para chamar uma classe auxiliar quando os cookies são anexados ou excluídos.</span><span class="sxs-lookup"><span data-stu-id="60661-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="60661-123">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="60661-123">For example:</span></span>

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

<span data-ttu-id="60661-124">A função auxiliar `CheckSameSite(HttpContext, CookieOptions)`:</span><span class="sxs-lookup"><span data-stu-id="60661-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="60661-125">É chamado quando os cookies são anexados à solicitação ou excluídos da solicitação.</span><span class="sxs-lookup"><span data-stu-id="60661-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="60661-126">Verifica se a propriedade `SameSite` está definida como `None`.</span><span class="sxs-lookup"><span data-stu-id="60661-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="60661-127">Se `SameSite` for definido como `None` e o agente do usuário atual for conhecido por não oferecer suporte ao valor do atributo None.</span><span class="sxs-lookup"><span data-stu-id="60661-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="60661-128">A verificação é feita usando a classe [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :</span><span class="sxs-lookup"><span data-stu-id="60661-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="60661-129">Define `SameSite` para não emitir o valor definindo a propriedade como `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="60661-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="60661-130">Direcionamento .NET Framework</span><span class="sxs-lookup"><span data-stu-id="60661-130">Targeting .NET Framework</span></span>

<span data-ttu-id="60661-131">ASP.NET Core e System. Web (ASP.NET clássico) têm implementações independentes de SameSite.</span><span class="sxs-lookup"><span data-stu-id="60661-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="60661-132">Os patches do SameSite KB para .NET Framework não são necessários se usar ASP.NET Core nem o requisito de versão de estrutura mínima do System. Web SameSite (.NET 4.7.2) se aplicam ao ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60661-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="60661-133">ASP.NET Core no .NET requer a atualização das dependências do pacote NuGet para obter as correções apropriadas.</span><span class="sxs-lookup"><span data-stu-id="60661-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="60661-134">Para obter as alterações de ASP.NET Core para .NET Framework Verifique se você tem uma referência direta para os pacotes e versões de patch (2.1.14 ou versões posteriores 2,1).</span><span class="sxs-lookup"><span data-stu-id="60661-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="60661-135">Mais informações</span><span class="sxs-lookup"><span data-stu-id="60661-135">More Information</span></span>
 
<span data-ttu-id="60661-136">O [Chrome updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core documentação do SameSite](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core notificação de alteração do SameSite 2,1](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="60661-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>