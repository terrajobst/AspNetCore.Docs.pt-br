---
title: Impedir ataques de solicitação intersite forjada (XSRF/CSRF) no ASP.NET Core
author: steve-smith
description: Descubra como evitar ataques contra aplicativos Web em que um site mal-intencionado pode influenciar a interação entre um navegador cliente e o aplicativo.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: security/anti-request-forgery
ms.openlocfilehash: 54e153af55f28d9a89bbf16bce1c17f876567b59
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880803"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Impedir ataques de solicitação intersite forjada (XSRF/CSRF) no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)e [Steve Smith](https://ardalis.com/)

A solicitação entre sites forjada (também conhecida como XSRF ou CSRF) é um ataque contra aplicativos hospedados na Web, nos quais um aplicativo Web mal-intencionado pode influenciar a interação entre um navegador cliente e um aplicativo Web que confia nesse navegador. Esses ataques são possíveis porque os navegadores da Web enviam automaticamente alguns tipos de tokens de autenticação com cada solicitação a um site. Essa forma de exploração também é conhecida como um ataque ou *sessão* de *um clique* , pois o ataque aproveita a sessão autenticada anteriormente do usuário.

Um exemplo de um ataque de CSRF:

1. Um usuário entra em `www.good-banking-site.com` usando a autenticação de formulários. O servidor autentica o usuário e emite uma resposta que inclui um cookie de autenticação. O site está vulnerável a ataques porque confia em qualquer solicitação recebida com um cookie de autenticação válido.
1. O usuário visita um site mal-intencionado, `www.bad-crook-site.com`.

   O site mal-intencionado, `www.bad-crook-site.com`, contém um formulário HTML semelhante ao seguinte:

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   Observe que o `action` do formulário é Postado para o site vulnerável, não para o site mal-intencionado. Esta é a parte "entre sites" do CSRF.

1. O usuário seleciona o botão enviar. O navegador faz a solicitação e inclui automaticamente o cookie de autenticação para o domínio solicitado, `www.good-banking-site.com`.
1. A solicitação é executada no servidor de `www.good-banking-site.com` com o contexto de autenticação do usuário e pode executar qualquer ação que um usuário autenticado tem permissão para executar.

Além do cenário em que o usuário seleciona o botão para enviar o formulário, o site mal-intencionado poderia:

* Execute um script que envia automaticamente o formulário.
* Envie o envio do formulário como uma solicitação AJAX.
* Oculte o formulário usando o CSS.

Esses cenários alternativos não exigem nenhuma ação ou entrada do usuário que não seja a visita inicial do site mal-intencionado.

O uso de HTTPS não impede um ataque de CSRF. O site mal-intencionado pode enviar uma solicitação de `https://www.good-banking-site.com/` tão facilmente quanto pode enviar uma solicitação insegura.

Alguns ataques têm como alvo pontos de extremidade que respondem a solicitações GET. nesse caso, uma marca de imagem pode ser usada para executar a ação. Essa forma de ataque é comum em sites de fórum que permitem imagens, mas bloqueiam JavaScript. Aplicativos que alteram o estado em solicitações GET, em que variáveis ou recursos são alterados, são vulneráveis a ataques mal-intencionados. **As solicitações GET que alteram o estado são inseguras. Uma prática recomendada é nunca alterar o estado em uma solicitação GET.**

Ataques de CSRF são possíveis em aplicativos Web que usam cookies para autenticação porque:

* Os navegadores armazenam cookies emitidos por um aplicativo Web.
* Os cookies armazenados incluem cookies de sessão para usuários autenticados.
* Os navegadores enviam todos os cookies associados a um domínio para o aplicativo Web a cada solicitação, independentemente de como a solicitação para o aplicativo foi gerada no navegador.

No entanto, ataques CSRF não são limitados à exploração de cookies. Por exemplo, a autenticação básica e resumida também são vulneráveis. Depois que um usuário entrar com a autenticação básica ou resumida, o navegador enviará automaticamente as credenciais até que a sessão&dagger; seja encerrada.

&dagger;neste contexto, *Session* refere-se à sessão do lado do cliente durante a qual o usuário é autenticado. Não está relacionado a sessões do lado do servidor ou ao [middleware de sessão ASP.NET Core](xref:fundamentals/app-state).

Os usuários podem se proteger contra vulnerabilidades do CSRF tomando precauções:

* Desconexão de aplicativos Web quando terminar de usá-los.
* Limpe os cookies do navegador periodicamente.

No entanto, as vulnerabilidades do CSRF são fundamentalmente um problema com o aplicativo Web, não com o usuário final.

## <a name="authentication-fundamentals"></a>Conceitos básicos de autenticação

A autenticação baseada em cookie é uma forma popular de autenticação. Os sistemas de autenticação baseados em token estão crescendo em popularidade, especialmente para aplicativos de página única (SPAs).

### <a name="cookie-based-authentication"></a>Autenticação baseada em cookie

Quando um usuário é autenticado usando seu nome de usuário e senha, ele recebe um token que contém um tíquete de autenticação que pode ser usado para autenticação e autorização. O token é armazenado como um cookie que acompanha cada solicitação que o cliente faz. Gerar e validar esse cookie é executado pelo middleware de autenticação de cookie. O [middleware](xref:fundamentals/middleware/index) serializa uma entidade de usuário em um cookie criptografado. Em solicitações subsequentes, o middleware valida o cookie, recria a entidade de segurança e atribui a entidade de segurança à propriedade de [usuário](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) de [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).

### <a name="token-based-authentication"></a>Autenticação baseada em token

Quando um usuário é autenticado, ele recebe um token (não um token de falsificação). O token contém informações de usuário na forma de [declarações](/dotnet/framework/security/claims-based-identity-model) ou um token de referência que aponta o aplicativo para o estado do usuário mantido no aplicativo. Quando um usuário tenta acessar um recurso que requer autenticação, o token é enviado ao aplicativo com um cabeçalho de autorização adicional na forma de token de portador. Isso torna o aplicativo sem estado. Em cada solicitação subsequente, o token é passado na solicitação de validação do lado do servidor. Esse token não está *criptografado*; Ele está *codificado*. No servidor, o token é decodificado para acessar suas informações. Para enviar o token em solicitações subsequentes, armazene o token no armazenamento local do navegador. Não se preocupe com a vulnerabilidade de CSRF se o token for armazenado no armazenamento local do navegador. CSRF é uma preocupação quando o token é armazenado em um cookie. Para obter mais informações, consulte o exemplo de código de spa problema do GitHub [adiciona dois cookies](https://github.com/aspnet/AspNetCore.Docs/issues/13369).

### <a name="multiple-apps-hosted-at-one-domain"></a>Vários aplicativos hospedados em um domínio

Os ambientes de hospedagem compartilhados são vulneráveis ao seqüestro de sessão, CSRF de logon e outros ataques.

Embora `example1.contoso.net` e `example2.contoso.net` sejam hosts diferentes, há uma relação de confiança implícita entre os hosts no domínio de `*.contoso.net`. Essa relação de confiança implícita permite que hosts potencialmente não confiáveis afetem os cookies uns dos outros (as políticas de mesma origem que regem as solicitações AJAX não necessariamente se aplicam a cookies HTTP).

Os ataques que exploram cookies confiáveis entre aplicativos hospedados no mesmo domínio podem ser impedidos por não compartilhar domínios. Quando cada aplicativo é hospedado em seu próprio domínio, não há nenhuma relação de confiança de cookie implícita para explorar.

## <a name="aspnet-core-antiforgery-configuration"></a>Configuração de antifalsificação de ASP.NET Core

> [!WARNING]
> ASP.NET Core implementa antifalsificação usando a [proteção de dados do ASP.NET Core](xref:security/data-protection/introduction). A pilha de proteção de dados deve ser configurada para funcionar em um farm de servidores. Consulte [Configurando a proteção de dados](xref:security/data-protection/configuration/overview) para obter mais informações.

::: moniker range=">= aspnetcore-3.0"

O middleware antifalsificação é adicionado ao contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) quando uma das seguintes APIs é chamada no `Startup.ConfigureServices`:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O middleware antifalsificação é adicionado ao contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) quando <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> é chamado em `Startup.ConfigureServices`

::: moniker-end

No ASP.NET Core 2,0 ou posterior, o [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injeta tokens de antifalsificação em elementos de formulário HTML. A seguinte marcação em um arquivo Razor gera automaticamente tokens de antifalsificação:

```cshtml
<form method="post">
    ...
</form>
```

Da mesma forma, [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) gera tokens antifalsificação por padrão se o método do formulário não for Get.

A geração automática de tokens antifalsificação para elementos de formulário HTML acontece quando a marca de `<form>` contém o atributo `method="post"` e qualquer uma das seguintes opções é verdadeira:

* O atributo action está vazio (`action=""`).
* O atributo action não é fornecido (`<form method="post">`).

A geração automática de tokens antifalsificação para elementos de formulário HTML pode ser desabilitada:

* Desabilite explicitamente os tokens antifalsificação com o atributo `asp-antiforgery`:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* O elemento form é aceito por auxiliares de marca usando o símbolo de desativação auxiliar de marca [!](xref:mvc/views/tag-helpers/intro#opt-out):

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* Remova a `FormTagHelper` da exibição. O `FormTagHelper` pode ser removido de um modo de exibição adicionando a seguinte diretiva ao modo de exibição do Razor:

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Razor Pages](xref:razor-pages/index) são automaticamente protegidos de XSRF/CSRF. Para obter mais informações, consulte [XSRF/CSRF e Razor Pages](xref:razor-pages/index#xsrf).

A abordagem mais comum para a defesa contra ataques CSRF é usar o *padrão de token do sincronizador* (STP). O STP é usado quando o usuário solicita uma página com dados de formulário:

1. O servidor envia um token associado à identidade do usuário atual para o cliente.
1. O cliente envia de volta o token para o servidor para verificação.
1. Se o servidor receber um token que não corresponda à identidade do usuário autenticado, a solicitação será rejeitada.

O token é exclusivo e imprevisível. O token também pode ser usado para garantir o sequenciamento adequado de uma série de solicitações (por exemplo, garantindo a sequência de solicitação de: Página 1 &ndash; página 2 &ndash; página 3). Todos os formulários em modelos ASP.NET Core MVC e Razor Pages geram tokens antifalsificação. O seguinte par de exemplos de exibição gera tokens antifalsificação:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

Adicione explicitamente um token de antifalsificação a um elemento `<form>` sem usar auxiliares de marcas com o HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

Em cada um dos casos anteriores, ASP.NET Core adiciona um campo de formulário oculto semelhante ao seguinte:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core inclui três [filtros](xref:mvc/controllers/filters) para trabalhar com tokens antifalsificação:

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Opções de antifalsificação

Personalize [as opções de antifalsificação](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) no `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

&dagger;definir as propriedades de `Cookie` antifalsificação usando as propriedades da classe [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .

| Opção | Descrição |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determina as configurações usadas para criar os cookies antifalsificação. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | O nome do campo de formulário oculto usado pelo sistema antifalsificação para renderizar tokens de antifalsificação em exibições. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | O nome do cabeçalho usado pelo sistema antifalsificação. Se `null`, o sistema considerará apenas os dados do formulário. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Especifica se a geração do cabeçalho de `X-Frame-Options` deve ser suprimida. Por padrão, o cabeçalho é gerado com um valor de "SAMEORIGIN". Assume o padrão de `false`. |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| Opção | Descrição |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determina as configurações usadas para criar os cookies antifalsificação. |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | O domínio do cookie. Assume o padrão de `null`. Esta propriedade é obsoleta e será removida em uma versão futura. A alternativa recomendada é cookie. domain. |
| [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | O nome do cookie. Se não estiver definido, o sistema gera um nome exclusivo que começa com o [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. antifalsificação. "). Esta propriedade é obsoleta e será removida em uma versão futura. A alternativa recomendada é Cookie.Name. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | O caminho definido no cookie. Esta propriedade é obsoleta e será removida em uma versão futura. A alternativa recomendada é cookie. Path. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | O nome do campo de formulário oculto usado pelo sistema antifalsificação para renderizar tokens de antifalsificação em exibições. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | O nome do cabeçalho usado pelo sistema antifalsificação. Se `null`, o sistema considerará apenas os dados do formulário. |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Especifica se o HTTPS é exigido pelo sistema antifalsificação. Se `true`, as solicitações não-HTTPS falharão. Assume o padrão de `false`. Esta propriedade é obsoleta e será removida em uma versão futura. A alternativa recomendada é definir cookie. SecurePolicy. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Especifica se a geração do cabeçalho de `X-Frame-Options` deve ser suprimida. Por padrão, o cabeçalho é gerado com um valor de "SAMEORIGIN". Assume o padrão de `false`. |

::: moniker-end

Para obter mais informações, consulte [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).

## <a name="configure-antiforgery-features-with-iantiforgery"></a>Configurar recursos antifalsificação com IAntiforgery

O [IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) fornece a API para configurar recursos antifalsificação. `IAntiforgery` pode ser solicitado no método `Configure` da classe `Startup`. O exemplo a seguir usa o middleware do home page do aplicativo para gerar um token de antifalsificação e enviá-lo na resposta como um cookie (usando a Convenção de nomenclatura angular padrão descrita mais adiante neste tópico):

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a>Exigir validação de antifalsificação

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) é um filtro de ação que pode ser aplicado a uma ação individual, a um controlador ou globalmente. As solicitações feitas às ações que têm esse filtro aplicado são bloqueadas, a menos que a solicitação inclua um token antifalsificação válido.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

O atributo `ValidateAntiForgeryToken` requer um token para solicitações para os métodos de ação que ele marca, incluindo solicitações HTTP GET. Se o atributo `ValidateAntiForgeryToken` for aplicado nos controladores do aplicativo, ele poderá ser substituído pelo atributo `IgnoreAntiforgeryToken`.

> [!NOTE]
> ASP.NET Core não dá suporte à adição de tokens antifalsificação para obter solicitações automaticamente.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>Validar automaticamente tokens antifalsificação somente para métodos HTTP inseguros

ASP.NET Core aplicativos não geram tokens antifalsificação para métodos HTTP seguros (GET, cabeçalho, opções e rastreamento). Em vez de aplicar amplamente o atributo `ValidateAntiForgeryToken` e, em seguida, substituí-lo por `IgnoreAntiforgeryToken` atributos, o atributo [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) pode ser usado. Esse atributo funciona de forma idêntica ao atributo `ValidateAntiForgeryToken`, exceto pelo fato de que ele não requer tokens para solicitações feitas usando os seguintes métodos HTTP:

* OBTER
* HOME
* OPÇÕES
* TRACE

Recomendamos o uso de `AutoValidateAntiforgeryToken` amplamente para cenários que não são da API. Isso garante que as ações de POSTAgem sejam protegidas por padrão. A alternativa é ignorar os tokens antifalsificação por padrão, a menos que `ValidateAntiForgeryToken` seja aplicado a métodos de ação individuais. É mais provável que esse cenário para um método POST Action seja deixado desprotegido por engano, deixando o aplicativo vulnerável a ataques CSRF. Todas as postagens devem enviar o token de antifalsificação.

As APIs não têm um mecanismo automático para enviar a parte não cookie do token. A implementação provavelmente depende da implementação do código do cliente. Alguns exemplos são mostrados abaixo:

Exemplo de nível de classe:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Exemplo global:

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a>Substituir atributos globais ou de antifalsificação do controlador

O filtro [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) é usado para eliminar a necessidade de um token de antifalsificação para uma determinada ação (ou controlador). Quando aplicado, esse filtro substitui `ValidateAntiForgeryToken` e `AutoValidateAntiforgeryToken` filtros especificados em um nível mais alto (globalmente ou em um controlador).

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a>Atualizar tokens após a autenticação

Os tokens devem ser atualizados depois que o usuário é autenticado redirecionando o usuário para uma página de exibição ou Razor Pages.

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX e SPAs

Em aplicativos baseados em HTML tradicionais, os tokens antifalsificação são passados para o servidor usando campos de formulário ocultos. Em aplicativos modernos baseados em JavaScript e SPAs, muitas solicitações são feitas programaticamente. Essas solicitações AJAX podem usar outras técnicas (como cabeçalhos de solicitação ou cookies) para enviar o token.

Se os cookies forem usados para armazenar tokens de autenticação e autenticar solicitações de API no servidor, o CSRF será um problema potencial. Se o armazenamento local for usado para armazenar o token, a vulnerabilidade CSRF poderá ser reduzida porque os valores do armazenamento local não serão enviados automaticamente para o servidor com cada solicitação. Portanto, o uso do armazenamento local para armazenar o token de antifalsificação no cliente e o envio do token como um cabeçalho de solicitação é uma abordagem recomendada.

### <a name="javascript"></a>JavaScript

Usando JavaScript com exibições, o token pode ser criado usando um serviço de dentro da exibição. Insira o serviço [Microsoft. AspNetCore. antifalsificating. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) na exibição e chame [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

Essa abordagem elimina a necessidade de lidar diretamente com a configuração de cookies do servidor ou leitura deles a partir do cliente.

O exemplo anterior usa JavaScript para ler o valor do campo oculto para o cabeçalho de POSTAgem AJAX.

O JavaScript também pode acessar tokens em cookies e usar o conteúdo do cookie para criar um cabeçalho com o valor do token.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Supondo que as solicitações de script enviem o token em um cabeçalho chamado `X-CSRF-TOKEN`, configure o serviço antifalsificação para procurar o cabeçalho `X-CSRF-TOKEN`:

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

O exemplo a seguir usa JavaScript para fazer uma solicitação AJAX com o cabeçalho apropriado:

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a>AngularJS

O AngularJS usa uma Convenção para tratar CSRF. Se o servidor enviar um cookie com o nome `XSRF-TOKEN`, o serviço AngularJS `$http` adicionará o valor de cookie a um cabeçalho ao enviar uma solicitação ao servidor. Esse processo é automático. O cabeçalho não precisa ser definido explicitamente no cliente. O nome do cabeçalho é `X-XSRF-TOKEN`. O servidor deve detectar esse cabeçalho e validar seu conteúdo.

Para ASP.NET Core API funcione com essa convenção na inicialização do seu aplicativo:

* Configure seu aplicativo para fornecer um token em um cookie chamado `XSRF-TOKEN`.
* Configure o serviço antifalsificação para procurar um cabeçalho chamado `X-XSRF-TOKEN`.

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="extend-antiforgery"></a>Estender antifalsificação

O tipo [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) permite que os desenvolvedores estendam o comportamento do sistema CSRF por meio da ida e volta de dados adicionais em cada token. O método [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) é chamado cada vez que um token de campo é gerado e o valor de retorno é inserido no token gerado. Um implementador poderia retornar um carimbo de data/hora, um nonce ou qualquer outro valor e, em seguida, chamar [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) para validar esses dados quando o token for validado. O nome de usuário do cliente já está inserido nos tokens gerados, portanto, não é necessário incluir essas informações. Se um token incluir dados complementares, mas nenhum `IAntiForgeryAdditionalDataProvider` estiver configurado, os dados complementares não serão validados.

## <a name="additional-resources"></a>Recursos adicionais

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) em [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).
* <xref:host-and-deploy/web-farm>
