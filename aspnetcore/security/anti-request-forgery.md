---
title: Ataques de evitar entre solicitação intersite forjada (CSRF/XSRF) no ASP.NET Core
author: steve-smith
description: Descubra como evitar ataques contra aplicativos web em que um site mal-intencionado pode influenciar a interação entre um navegador cliente e o aplicativo.
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: security/anti-request-forgery
ms.openlocfilehash: a32e0e2dbd7fab95562a562cb88767d4c1e8049d
ms.sourcegitcommit: c5339594101d30b189f61761275b7d310e80d18a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/02/2019
ms.locfileid: "66458491"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Ataques de evitar entre solicitação intersite forjada (CSRF/XSRF) no ASP.NET Core

Por [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), e [Rick Anderson](https://twitter.com/RickAndMSFT)

Falsificação de solicitação intersite forjada (também conhecida como XSRF ou CSRF) é um ataque contra aplicativos hospedados na web, no qual um aplicativo web mal-intencionado pode influenciar a interação entre um navegador cliente e um aplicativo web que confia nesse navegador. Esses ataques são possíveis, pois navegadores da web enviam alguns tipos de tokens de autenticação automaticamente com cada solicitação de um site. Essa forma de exploração é também conhecido como um *ataque de um clique* ou *sequestro de sessão* porque o ataque aproveita o usuário do autenticado anteriormente sessão.

Um exemplo de um ataque CSRF:

1. Um usuário entra em `www.good-banking-site.com` usando a autenticação de formulários. O servidor autentica o usuário e emite uma resposta que inclui um cookie de autenticação. O site é vulnerável a ataques porque ele confia qualquer solicitação recebida com um cookie de autenticação válido.
1. O usuário visitar um site mal-intencionado, `www.bad-crook-site.com`.

   O site mal-intencionado, `www.bad-crook-site.com`, contém um formulário HTML semelhante ao seguinte:

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   Observe que o formulário `action` postagens para o site vulnerável, não para o site mal-intencionado. Essa é a parte de "site cruzado" de CSRF.

1. O usuário seleciona o botão Enviar. O navegador faz a solicitação e inclui automaticamente o cookie de autenticação para o domínio solicitado, `www.good-banking-site.com`.
1. A solicitação é executada `www.good-banking-site.com` server com o contexto de autenticação do usuário e pode executar qualquer ação que um usuário autenticado tem permissão para executar.

Além do cenário em que o usuário seleciona o botão para enviar o formulário, o site mal-intencionado pode:

* Execute um script que envia automaticamente o formulário.
* Envie o envio do formulário como uma solicitação AJAX.
* Oculte o formulário usando CSS.

Esses cenários alternativos não exigem qualquer entrada do usuário que não sejam inicialmente visitar o site mal-intencionado ou ação.

Usar o HTTPS não impede que um ataque CSRF. O site mal-intencionado pode enviar um `https://www.good-banking-site.com/` solicitar tão fácil quanto ele pode enviar uma solicitação não segura.

Alguns ataques são direcionados a pontos de extremidade que respondem às solicitações GET, neste caso, uma marca de imagem pode ser usada para executar a ação. Essa forma de ataque é comum nos sites de Fórum que permitem imagens, mas bloqueiam o JavaScript. Aplicativos que alteram o estado em solicitações GET, em que as variáveis ou os recursos são alterados, são vulneráveis a ataques mal-intencionados. **Solicitações GET que alteram o estado são inseguras. Uma prática recomendada é nunca alterar o estado em uma solicitação GET.**

Ataques de CSRF são possíveis em relação a aplicativos web que usam cookies para autenticação porque:

* Os navegadores armazenam os cookies emitidos por um aplicativo web.
* Armazenado cookies incluem cookies de sessão para usuários autenticados.
* Os navegadores enviam a que todos os cookies associados com um domínio para o aplicativo web cada solicitação, independentemente de como a solicitação para o aplicativo foi gerada dentro do navegador.

No entanto, os ataques de CSRF não estão limitadas a exploração de cookies. Por exemplo, a autenticação básica e Digest também são vulneráveis. Depois que um usuário entra com a autenticação básica ou Digest, o navegador automaticamente envia as credenciais até que a sessão&dagger; termina.

&dagger;Nesse contexto, *sessão* refere-se à sessão do lado do cliente durante o qual o usuário é autenticado. Ele é não relacionada a sessões do lado do servidor ou [Middleware de sessão do ASP.NET Core](xref:fundamentals/app-state).

Os usuários podem se proteger contra vulnerabilidades CSRF tomando precauções:

* Entrar em aplicativos web quando terminar de usá-los.
* Limpar os cookies do navegador periodicamente.

No entanto, as vulnerabilidades CSRF são basicamente um problema com o aplicativo web, não pelo usuário final.

## <a name="authentication-fundamentals"></a>Conceitos básicos de autenticação

Autenticação baseada em cookie é uma forma popular de autenticação. Sistemas de autenticação baseada em token estão crescendo em termos de popularidade, especialmente para aplicativos de página única (SPAs).

### <a name="cookie-based-authentication"></a>Autenticação baseada em cookie

Quando um usuário é autenticado usando o nome de usuário e senha, elas emitidas um token, que contém um tíquete de autenticação que pode ser usado para autenticação e autorização. O token é armazenado como um cookie que acompanha cada solicitação, o cliente faz. Gerar e validar esse cookie é realizada pelo Middleware de autenticação de Cookie. O [middleware](xref:fundamentals/middleware/index) serializa uma entidade de usuário em um cookie criptografado. Nas próximas solicitações, o middleware valida o cookie, recria a entidade de segurança e a atribui a entidade de segurança de [usuário](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) propriedade de [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).

### <a name="token-based-authentication"></a>Autenticação baseada em token

Quando um usuário é autenticado, elas emitidas um token (não um token antifalsificação). O token contém informações de usuário na forma de [declarações](/dotnet/framework/security/claims-based-identity-model) ou um token de referência que aponta o aplicativo para o estado do usuário mantido no aplicativo. Quando um usuário tenta acessar um recurso que requer autenticação, o token é enviado para o aplicativo com um cabeçalho de autorização adicionais na forma de token de portador. Isso torna o aplicativo sem monitoração de estado. Em cada solicitação subsequente, o token é passado na solicitação para a validação do lado do servidor. Não é esse token *criptografado*; ele tem *codificado*. No servidor, o token é decodificado para acessar suas informações. Para enviar o token em solicitações subsequentes, armazene o token no armazenamento local do navegador. Não fique preocupado sobre vulnerabilidade CSRF, se o token é armazenado no armazenamento local do navegador. CSRF é uma preocupação quando o token é armazenado em um cookie.

### <a name="multiple-apps-hosted-at-one-domain"></a>Vários aplicativos hospedados em um domínio

Ambientes de hospedagem compartilhados são vulneráveis a sequestro de sessão, login CSRF e outros ataques.

Embora `example1.contoso.net` e `example2.contoso.net` são hosts diferentes, há uma relação de confiança implícita entre os hosts sob o `*.contoso.net` domínio. Essa relação de confiança implícita permite que os hosts potencialmente não confiáveis afetam uns dos outros cookies (as políticas de mesma origem que regem as solicitações AJAX não necessariamente se aplicam aos cookies HTTP).

Ataques que exploram confiáveis cookies entre aplicativos hospedados no mesmo domínio podem ser evitadas por meio do compartilhamento não domínios. Quando cada aplicativo é hospedado em seu próprio domínio, não há nenhuma relação de confiança implícita de cookie para explorar.

## <a name="aspnet-core-antiforgery-configuration"></a>Configuração antifalsificação do ASP.NET Core

> [!WARNING]
> ASP.NET Core implementa usando antifalsificação [proteção de dados do ASP.NET Core](xref:security/data-protection/introduction). A pilha da proteção de dados deve ser configurada para funcionar em um farm de servidores. Ver [Configurando a proteção de dados](xref:security/data-protection/configuration/overview) para obter mais informações.

No ASP.NET Core 2.0 ou posterior, o [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injeta tokens antifalsificação em elementos de formulário HTML. A marcação a seguir em um arquivo Razor gera automaticamente os tokens antifalsificação:

```cshtml
<form method="post">
    ...
</form>
```

Da mesma forma, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) gera tokens antifalsificação por padrão, se o método do formulário não é GET.

A geração automática de tokens antifalsificação para elementos de formulário HTML acontece quando o `<form>` marca contém o `method="post"` atributo e uma das opções a seguir forem verdadeira:

* O atributo action está vazio (`action=""`).
* O atributo de ação não for fornecido (`<form method="post">`).

Geração automática de tokens antifalsificação para elementos de formulário HTML pode ser desabilitada:

* Desabilitar explicitamente tokens antifalsificação com o `asp-antiforgery` atributo:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* O elemento de formulário é aceito-out dos auxiliares de marca usando o auxiliar de marca [! recusar símbolo](xref:mvc/views/tag-helpers/intro#opt-out):

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* Remover o `FormTagHelper` da exibição. O `FormTagHelper` pode ser removido de uma exibição, adicionando a seguinte diretiva para o modo de exibição do Razor:

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Páginas do Razor](xref:razor-pages/index) são protegidos automaticamente contra XSRF/CSRF. Para obter mais informações, consulte [XSRF/CSRF e páginas Razor](xref:razor-pages/index#xsrf).

A abordagem mais comum para proteger contra ataques CSRF é usar o *padrão de Token do sincronizador* (STP). STP é usado quando o usuário solicita uma página com os dados de formulário:

1. O servidor envia um token associado com a atual identidade do usuário para o cliente.
1. O cliente envia o token para o servidor para verificação.
1. Se o servidor recebe um token que não corresponde à identidade do usuário autenticado, a solicitação será rejeitada.

O token é exclusivo e imprevisíveis. O token também pode ser usado para garantir que o sequenciamento adequado de uma série de solicitações (por exemplo, garantindo a sequência de solicitação de: a página 1 &ndash; página 2 &ndash; página 3). Todos os formulários em modelos do ASP.NET Core MVC e páginas do Razor geram tokens contra falsificação. O seguinte par de exemplos do modo de exibição gera tokens contra falsificação:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

Adicionar explicitamente um token antifalsificação para um `<form>` elemento sem o uso de auxiliares de marca com o auxiliar HTML [ @Html.AntiForgeryToken ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

Em cada um dos casos anteriores, o ASP.NET Core adiciona um campo de formulário oculto semelhante ao seguinte:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core inclui três [filtros](xref:mvc/controllers/filters) para trabalhar com tokens antifalsificação:

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Opções de antifalsificação

Personalize [opções antifalsificação](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) em `Startup.ConfigureServices`:

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

&dagger;Definir a antifalsificação `Cookie` usando as propriedades do objeto de propriedades de [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) classe.

| Opção | Descrição |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determina as configurações usadas para criar o cookie antifalsificação. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | O nome do campo de formulário oculto usado pelo sistema antifalsificação para processar tokens antifalsificação nos modos de exibição. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | O nome do cabeçalho usado pelo sistema antifalsificação. Se `null`, o sistema considera apenas os dados de formulário. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Especifica se deve suprimir a geração do `X-Frame-Options` cabeçalho. Por padrão, o cabeçalho é gerado com um valor de "SAMEORIGIN". Assume o padrão de `false`. |

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
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determina as configurações usadas para criar o cookie antifalsificação. |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | O domínio do cookie. Assume o padrão de `null`. Essa propriedade está obsoleta e será removida em uma versão futura. A alternativa recomendada é Cookie.Domain. |
| [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | O nome do cookie. Se não definido, o sistema gera um nome exclusivo que começa com o [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore.Antiforgery."). Essa propriedade está obsoleta e será removida em uma versão futura. A alternativa recomendada é Cookie.Name. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | O caminho definido no cookie. Essa propriedade está obsoleta e será removida em uma versão futura. A alternativa recomendada é Cookie.Path. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | O nome do campo de formulário oculto usado pelo sistema antifalsificação para processar tokens antifalsificação nos modos de exibição. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | O nome do cabeçalho usado pelo sistema antifalsificação. Se `null`, o sistema considera apenas os dados de formulário. |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Especifica se o HTTPS é exigido pelo sistema antifalsificação. Se `true`, não HTTPS solicitações falham. Assume o padrão de `false`. Essa propriedade está obsoleta e será removida em uma versão futura. A alternativa recomendada é definir Cookie.SecurePolicy. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Especifica se deve suprimir a geração do `X-Frame-Options` cabeçalho. Por padrão, o cabeçalho é gerado com um valor de "SAMEORIGIN". Assume o padrão de `false`. |

::: moniker-end

Para obter mais informações, consulte [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).

## <a name="configure-antiforgery-features-with-iantiforgery"></a>Configurar recursos antiforgery com IAntiforgery

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) fornece a API para configurar recursos antifalsificação. `IAntiforgery` podem ser solicitadas a `Configure` método da `Startup` classe. O exemplo a seguir usa o middleware da home page do aplicativo para gerar um token antifalsificação e enviá-lo na resposta como um cookie (usando a convenção de nomenclatura Angular da padrão descrita mais adiante neste tópico):

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

### <a name="require-antiforgery-validation"></a>Exigir validação antifalsificação

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) é um filtro de ação que pode ser aplicado a uma ação individual, um controlador ou globalmente. As solicitações feitas a ações que têm esse filtro aplicado são bloqueadas, a menos que a solicitação inclui um token antifalsificação válido.

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

O `ValidateAntiForgeryToken` atributo requer um token para solicitações para os métodos de ação decora, incluindo as solicitações HTTP GET. Se o `ValidateAntiForgeryToken` atributo é aplicado em controladores do aplicativo, ele pode ser substituído com o `IgnoreAntiforgeryToken` atributo.

> [!NOTE]
> ASP.NET Core não dá suporte a adição de tokens antifalsificação para solicitações GET automaticamente.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>Automaticamente validar tokens antifalsificação para métodos HTTP não seguros apenas

Aplicativos ASP.NET Core não geram tokens contra falsificação para métodos HTTP seguros (GET, HEAD, opções e rastreamento). Em vez de aplicar amplamente a `ValidateAntiForgeryToken` atributo e, em seguida, substituindo-o com `IgnoreAntiforgeryToken` atributos, o [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) atributo pode ser usado. Esse atributo funciona de maneira idêntica ao `ValidateAntiForgeryToken` de atributo, exceto que ele não exige tokens para solicitações feitas usando os seguintes métodos HTTP:

* OBTER
* HOME
* OPÇÕES
* TRACE

É recomendável o uso de `AutoValidateAntiforgeryToken` em larga escala para cenários de não-API. Isso garante que as ações de POSTAGEM são protegidas por padrão. A alternativa é ignorar tokens antifalsificação por padrão, a menos que `ValidateAntiForgeryToken` é aplicado a métodos de ação individual. Ele tem mais provavelmente neste cenário para um método de ação a ser deixado POST desprotegidos por engano, deixando o aplicativo vulnerável a ataques CSRF. Todas as postagens devem enviar o token antifalsificação.

APIs não têm um mecanismo automático para enviar a parte não-cookie do token. A implementação provavelmente depende a implementação de código do cliente. Alguns exemplos são mostrados abaixo:

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

### <a name="override-global-or-controller-antiforgery-attributes"></a>Substituição global ou atributos antifalsificação do controlador

O [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filtro é usado para eliminar a necessidade de um token antifalsificação para uma determinada ação (ou controlador). Quando aplicado, esse filtro substitui `ValidateAntiForgeryToken` e `AutoValidateAntiforgeryToken` filtros especificados em um nível mais alto (globalmente ou em um controlador).

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

## <a name="refresh-tokens-after-authentication"></a>Tokens de atualização após a autenticação

Tokens devem ser atualizados depois que o usuário é autenticado ao redirecionar o usuário para uma exibição ou página do Razor.

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX e SPAs

Em aplicativos tradicionais baseados em HTML, tokens antifalsificação são passados para o servidor usando os campos de formulário oculto. Em aplicativos modernos baseados em JavaScript e SPAs, muitas solicitações são feitas por meio de programação. Essas solicitações AJAX podem usar outras técnicas (como cabeçalhos de solicitação ou cookies) para enviar o token.

Se os cookies são usados para armazenar tokens de autenticação e para autenticar solicitações de API no servidor, CSRF é um problema potencial. Se o armazenamento local é usado para armazenar o token, vulnerabilidade CSRF pode ser reduzida porque os valores do armazenamento local não são enviadas automaticamente para o servidor com cada solicitação. Dessa forma, usando o armazenamento local para armazenar o token antifalsificação no cliente e enviar o token como um cabeçalho de solicitação é uma abordagem recomendada.

### <a name="javascript"></a>JavaScript

Usando o JavaScript com modos de exibição, o token pode ser criado usando um serviço de dentro da exibição. Injetar o [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) serviço para o modo de exibição e chame [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

Essa abordagem elimina a necessidade de lidar diretamente com definir cookies a partir do servidor ou lê-los a partir do cliente.

O exemplo anterior usa JavaScript para ler o valor do campo oculto para o cabeçalho de POSTAGEM de AJAX.

JavaScript pode também tokens de acesso em cookies e usar o conteúdo do cookie para criar um cabeçalho com o valor do token.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Supondo que o script solicita para enviar o token em um cabeçalho chamado `X-CSRF-TOKEN`, configure o serviço antifalsificação para procurar o `X-CSRF-TOKEN` cabeçalho:

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

AngularJS usa uma convenção para endereço CSRF. Se o servidor envia um cookie com o nome `XSRF-TOKEN`, o AngularJS `$http` serviço adiciona o valor do cookie a um cabeçalho quando ele envia uma solicitação ao servidor. Esse processo é automático. O cabeçalho não precisa ser definido explicitamente no cliente. O nome do cabeçalho é `X-XSRF-TOKEN`. O servidor deve detectar esse cabeçalho e validar seu conteúdo.

API do ASP.NET Core trabalhar com essa convenção na inicialização do seu aplicativo:

* Configurar seu aplicativo para fornecer um token em um cookie chamado `XSRF-TOKEN`.
* Configurar o serviço antifalsificação para procurar por um cabeçalho chamado `X-XSRF-TOKEN`.

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

O [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) tipo permite aos desenvolvedores estender o comportamento do sistema anti-CSRF por dados adicionais de ciclo completo em cada token. O [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) método é chamado sempre que um token de campo é gerado e o valor de retorno é incorporado dentro do token gerado. Um implementador poderia retornar um carimbo de hora, um nonce ou qualquer outro valor e, em seguida, chame [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) para validar dados quando o token é validado. Nome de usuário do cliente já está incorporado em tokens gerados, portanto, não há nenhuma necessidade de incluir essas informações. Se um token inclui dados complementares, mas não `IAntiForgeryAdditionalDataProvider` é configurado, os dados complementares não são validados.

## <a name="additional-resources"></a>Recursos adicionais

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) na [Abrir Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).
* <xref:host-and-deploy/web-farm>
