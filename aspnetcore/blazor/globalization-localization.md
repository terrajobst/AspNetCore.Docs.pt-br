---
title: ASP.NET Core Blazor globalização e localização
author: guardrex
description: Saiba como tornar os componentes do Razor acessíveis para os usuários em várias culturas e linguagens.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: aba62fa7b6285c8ba884652694f1ea3e3a66ed18
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453264"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="fff42-103">ASP.NET Core Blazor globalização e localização</span><span class="sxs-lookup"><span data-stu-id="fff42-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="fff42-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="fff42-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="fff42-105">Os componentes do Razor podem ser acessíveis para usuários em várias culturas e idiomas.</span><span class="sxs-lookup"><span data-stu-id="fff42-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="fff42-106">Os seguintes cenários de globalização e localização do .NET estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="fff42-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="fff42-107">. Sistema de recursos da rede</span><span class="sxs-lookup"><span data-stu-id="fff42-107">.NET's resources system</span></span>
* <span data-ttu-id="fff42-108">Formatação de número e data específicos da cultura</span><span class="sxs-lookup"><span data-stu-id="fff42-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="fff42-109">No momento, há suporte para um conjunto limitado de cenários de localização de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="fff42-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="fff42-110">`IStringLocalizer<>` tem *suporte* em aplicativos Blazor.</span><span class="sxs-lookup"><span data-stu-id="fff42-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="fff42-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`e localização de anotações de dados são ASP.NET Core cenários MVC e **não têm suporte** em aplicativos Blazor.</span><span class="sxs-lookup"><span data-stu-id="fff42-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="fff42-112">Para obter mais informações, consulte <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="fff42-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="fff42-113">Globalização</span><span class="sxs-lookup"><span data-stu-id="fff42-113">Globalization</span></span>

<span data-ttu-id="fff42-114">a funcionalidade de `@bind` do Blazorexecuta formatos e analisa valores para exibição com base na cultura atual do usuário.</span><span class="sxs-lookup"><span data-stu-id="fff42-114">Blazor's `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="fff42-115">A cultura atual pode ser acessada a partir da propriedade <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="fff42-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="fff42-116">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) é usado para os seguintes tipos de campo (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="fff42-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="fff42-117">Os tipos de campo anteriores:</span><span class="sxs-lookup"><span data-stu-id="fff42-117">The preceding field types:</span></span>

* <span data-ttu-id="fff42-118">São exibidos usando suas regras de formatação baseadas em navegador apropriadas.</span><span class="sxs-lookup"><span data-stu-id="fff42-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="fff42-119">Não pode conter texto de forma livre.</span><span class="sxs-lookup"><span data-stu-id="fff42-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="fff42-120">Fornecer características de interação do usuário com base na implementação do navegador.</span><span class="sxs-lookup"><span data-stu-id="fff42-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="fff42-121">Os seguintes tipos de campo têm requisitos de formatação específicos e atualmente não têm suporte do Blazor porque eles não têm suporte em todos os principais navegadores:</span><span class="sxs-lookup"><span data-stu-id="fff42-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="fff42-122">`@bind` dá suporte ao parâmetro `@bind:culture` para fornecer um <xref:System.Globalization.CultureInfo?displayProperty=fullName> para análise e formatação de um valor.</span><span class="sxs-lookup"><span data-stu-id="fff42-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="fff42-123">Não é recomendável especificar uma cultura ao usar os tipos de campo `date` e `number`.</span><span class="sxs-lookup"><span data-stu-id="fff42-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="fff42-124">`date` e `number` têm suporte interno a Blazor que fornece a cultura necessária.</span><span class="sxs-lookup"><span data-stu-id="fff42-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="fff42-125">Localização</span><span class="sxs-lookup"><span data-stu-id="fff42-125">Localization</span></span>

<span data-ttu-id="fff42-126">os aplicativos do Blazor Server são localizados usando o [middleware de localização](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="fff42-126">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="fff42-127">O middleware seleciona a cultura apropriada para os usuários que solicitam recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fff42-127">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="fff42-128">A cultura pode ser definida usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="fff42-128">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="fff42-129">Cookies</span><span class="sxs-lookup"><span data-stu-id="fff42-129">Cookies</span></span>](#cookies)
* [<span data-ttu-id="fff42-130">Fornecer interface do usuário para escolher a cultura</span><span class="sxs-lookup"><span data-stu-id="fff42-130">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="fff42-131">Para obter mais informações e exemplos, consulte <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="fff42-131">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="fff42-132">Configurar o vinculador para internacionalização (Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="fff42-132">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="fff42-133">Por padrão, a configuração do vinculador Blazorpara aplicativos Webassembly Blazor retira informações de internacionalização, exceto as localidades explicitamente solicitadas.</span><span class="sxs-lookup"><span data-stu-id="fff42-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="fff42-134">Para obter mais informações e orientação sobre como controlar o comportamento do vinculador, consulte <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="fff42-134">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="fff42-135">Cookies</span><span class="sxs-lookup"><span data-stu-id="fff42-135">Cookies</span></span>

<span data-ttu-id="fff42-136">Um cookie de cultura de localização pode persistir a cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="fff42-136">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="fff42-137">O cookie é criado pelo método `OnGet` da página host do aplicativo (*pages/host. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="fff42-137">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="fff42-138">O middleware de localização lê o cookie em solicitações subsequentes para definir a cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="fff42-138">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="fff42-139">O uso de um cookie garante que a conexão WebSocket possa propagar corretamente a cultura.</span><span class="sxs-lookup"><span data-stu-id="fff42-139">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="fff42-140">Se os esquemas de localização forem baseados no caminho da URL ou na cadeia de caracteres de consulta, o esquema pode não ser capaz de trabalhar com WebSockets, portanto, falha ao persistir a cultura.</span><span class="sxs-lookup"><span data-stu-id="fff42-140">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="fff42-141">Portanto, o uso de um cookie de cultura de localização é a abordagem recomendada.</span><span class="sxs-lookup"><span data-stu-id="fff42-141">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="fff42-142">Qualquer técnica pode ser usada para atribuir uma cultura se a cultura persistir em um cookie de localização.</span><span class="sxs-lookup"><span data-stu-id="fff42-142">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="fff42-143">Se o aplicativo já tiver um esquema de localização estabelecido para ASP.NET Core do lado do servidor, continue a usar a infraestrutura de localização existente do aplicativo e defina o cookie de cultura de localização no esquema do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fff42-143">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="fff42-144">O exemplo a seguir mostra como definir a cultura atual em um cookie que pode ser lido pelo middleware de localização.</span><span class="sxs-lookup"><span data-stu-id="fff42-144">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="fff42-145">Crie um arquivo *pages/host. cshtml. cs* com o seguinte conteúdo no aplicativo Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="fff42-145">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="fff42-146">A localização é manipulada pelo aplicativo na seguinte sequência de eventos:</span><span class="sxs-lookup"><span data-stu-id="fff42-146">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="fff42-147">O navegador envia uma solicitação HTTP inicial para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fff42-147">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="fff42-148">A cultura é atribuída pelo middleware de localização.</span><span class="sxs-lookup"><span data-stu-id="fff42-148">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="fff42-149">O método `OnGet` em *_Host. cshtml. cs* persiste a cultura em um cookie como parte da resposta.</span><span class="sxs-lookup"><span data-stu-id="fff42-149">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="fff42-150">O navegador abre uma conexão WebSocket para criar uma sessão interativa do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="fff42-150">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="fff42-151">O middleware de localização lê o cookie e atribui a cultura.</span><span class="sxs-lookup"><span data-stu-id="fff42-151">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="fff42-152">A sessão do Blazor Server começa com a cultura correta.</span><span class="sxs-lookup"><span data-stu-id="fff42-152">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="fff42-153">Fornecer interface do usuário para escolher a cultura</span><span class="sxs-lookup"><span data-stu-id="fff42-153">Provide UI to choose the culture</span></span>

<span data-ttu-id="fff42-154">Para fornecer à interface do usuário a fim de permitir a seleção de uma cultura, é recomendável uma *abordagem baseada em redirecionamento* .</span><span class="sxs-lookup"><span data-stu-id="fff42-154">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="fff42-155">O processo é semelhante ao que acontece em um aplicativo Web quando um usuário tenta acessar um recurso seguro&mdash;o usuário é redirecionado para uma página de entrada e, em seguida, Redirecionado de volta para o recurso original.</span><span class="sxs-lookup"><span data-stu-id="fff42-155">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="fff42-156">O aplicativo persiste a cultura selecionada do usuário por meio de um redirecionamento para um controlador.</span><span class="sxs-lookup"><span data-stu-id="fff42-156">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="fff42-157">O controlador define a cultura selecionada do usuário em um cookie e redireciona o usuário de volta para o URI original.</span><span class="sxs-lookup"><span data-stu-id="fff42-157">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="fff42-158">Estabeleça um ponto de extremidade HTTP no servidor para definir a cultura selecionada do usuário em um cookie e execute o redirecionamento de volta para o URI original:</span><span class="sxs-lookup"><span data-stu-id="fff42-158">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="fff42-159">Use o resultado da ação `LocalRedirect` para evitar ataques de redirecionamento abertos.</span><span class="sxs-lookup"><span data-stu-id="fff42-159">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="fff42-160">Para obter mais informações, consulte <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="fff42-160">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="fff42-161">O componente a seguir mostra um exemplo de como executar o redirecionamento inicial quando o usuário seleciona uma cultura:</span><span class="sxs-lookup"><span data-stu-id="fff42-161">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="fff42-162">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fff42-162">Additional resources</span></span>

* <xref:fundamentals/localization>
