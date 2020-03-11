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
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655487"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.NET Core Blazor globalização e localização

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Os componentes do Razor podem ser acessíveis para usuários em várias culturas e idiomas. Os seguintes cenários de globalização e localização do .NET estão disponíveis:

* . Sistema de recursos da rede
* Formatação de número e data específicos da cultura

No momento, há suporte para um conjunto limitado de cenários de localização de ASP.NET Core:

* `IStringLocalizer<>` tem *suporte* em aplicativos Blazor.
* `IHtmlLocalizer<>`, `IViewLocalizer<>`e localização de anotações de dados são ASP.NET Core cenários MVC e **não têm suporte** em aplicativos Blazor.

Para obter mais informações, consulte <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalização

a funcionalidade de `@bind` do Blazorexecuta formatos e analisa valores para exibição com base na cultura atual do usuário.

A cultura atual pode ser acessada a partir da propriedade <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) é usado para os seguintes tipos de campo (`<input type="{TYPE}" />`):

* `date`
* `number`

Os tipos de campo anteriores:

* São exibidos usando suas regras de formatação baseadas em navegador apropriadas.
* Não pode conter texto de forma livre.
* Fornecer características de interação do usuário com base na implementação do navegador.

Os seguintes tipos de campo têm requisitos de formatação específicos e atualmente não têm suporte do Blazor porque eles não têm suporte em todos os principais navegadores:

* `datetime-local`
* `month`
* `week`

`@bind` dá suporte ao parâmetro `@bind:culture` para fornecer um <xref:System.Globalization.CultureInfo?displayProperty=fullName> para análise e formatação de um valor. Não é recomendável especificar uma cultura ao usar os tipos de campo `date` e `number`. `date` e `number` têm suporte interno a Blazor que fornece a cultura necessária.

## <a name="localization"></a>Localização

os aplicativos do Blazor Server são localizados usando o [middleware de localização](xref:fundamentals/localization#localization-middleware). O middleware seleciona a cultura apropriada para os usuários que solicitam recursos do aplicativo.

A cultura pode ser definida usando uma das seguintes abordagens:

* [Cookies](#cookies)
* [Fornecer interface do usuário para escolher a cultura](#provide-ui-to-choose-the-culture)

Para obter mais informações e exemplos, consulte <xref:fundamentals/localization>.

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a>Configurar o vinculador para internacionalização (Blazor Webassembly)

Por padrão, a configuração do vinculador Blazorpara aplicativos Webassembly Blazor retira informações de internacionalização, exceto as localidades explicitamente solicitadas. Para obter mais informações e orientação sobre como controlar o comportamento do vinculador, consulte <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

### <a name="cookies"></a>Cookies

Um cookie de cultura de localização pode persistir a cultura do usuário. O cookie é criado pelo método `OnGet` da página host do aplicativo (*pages/host. cshtml. cs*). O middleware de localização lê o cookie em solicitações subsequentes para definir a cultura do usuário. 

O uso de um cookie garante que a conexão WebSocket possa propagar corretamente a cultura. Se os esquemas de localização forem baseados no caminho da URL ou na cadeia de caracteres de consulta, o esquema pode não ser capaz de trabalhar com WebSockets, portanto, falha ao persistir a cultura. Portanto, o uso de um cookie de cultura de localização é a abordagem recomendada.

Qualquer técnica pode ser usada para atribuir uma cultura se a cultura persistir em um cookie de localização. Se o aplicativo já tiver um esquema de localização estabelecido para ASP.NET Core do lado do servidor, continue a usar a infraestrutura de localização existente do aplicativo e defina o cookie de cultura de localização no esquema do aplicativo.

O exemplo a seguir mostra como definir a cultura atual em um cookie que pode ser lido pelo middleware de localização. Crie um arquivo *pages/host. cshtml. cs* com o seguinte conteúdo no aplicativo Blazor Server:

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

A localização é manipulada pelo aplicativo na seguinte sequência de eventos:

1. O navegador envia uma solicitação HTTP inicial para o aplicativo.
1. A cultura é atribuída pelo middleware de localização.
1. O método `OnGet` em *_Host. cshtml. cs* persiste a cultura em um cookie como parte da resposta.
1. O navegador abre uma conexão WebSocket para criar uma sessão interativa do Blazor Server.
1. O middleware de localização lê o cookie e atribui a cultura.
1. A sessão do Blazor Server começa com a cultura correta.

### <a name="provide-ui-to-choose-the-culture"></a>Fornecer interface do usuário para escolher a cultura

Para fornecer à interface do usuário a fim de permitir a seleção de uma cultura, é recomendável uma *abordagem baseada em redirecionamento* . O processo é semelhante ao que acontece em um aplicativo Web quando um usuário tenta acessar um recurso seguro&mdash;o usuário é redirecionado para uma página de entrada e, em seguida, Redirecionado de volta para o recurso original. 

O aplicativo persiste a cultura selecionada do usuário por meio de um redirecionamento para um controlador. O controlador define a cultura selecionada do usuário em um cookie e redireciona o usuário de volta para o URI original.

Estabeleça um ponto de extremidade HTTP no servidor para definir a cultura selecionada do usuário em um cookie e execute o redirecionamento de volta para o URI original:

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
> Use o resultado da ação `LocalRedirect` para evitar ataques de redirecionamento abertos. Para obter mais informações, consulte <xref:security/preventing-open-redirects>.

O componente a seguir mostra um exemplo de como executar o redirecionamento inicial quando o usuário seleciona uma cultura:

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

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/localization>
