---
title: Proteger um aplicativo ASP.NET Core Blazor Webassembly autônomo com Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 76bcac29d86a236e56c0eaea24a694c4845ecbcf
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083744"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a>Proteger um aplicativo ASP.NET Core Blazor Webassembly autônomo com Azure Active Directory

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Para criar um aplicativo autônomo Webassembly Blazor que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação:

[Criar um locatário do AAD e um aplicativo Web](/azure/active-directory/develop/v2-overview):

Registre um aplicativo do AAD na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:

1. Forneça um **nome** para o aplicativo (por exemplo, **Blazor AAD do cliente**).
1. Escolha um **tipo de conta com suporte**. Você pode selecionar **contas neste diretório organizacional somente** para esta experiência.
1. Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI de redirecionamento de `https://localhost:5001/authentication/login-callback`.
1. Desabilite as **permissões** > **conceder administrador concento para OpenID e caixa de seleção permissões de offline_access** .
1. Selecione **Registrar**.

Em **autenticação** > **configurações da plataforma** > **Web**:

1. Confirme se o **URI de redirecionamento** do `https://localhost:5001/authentication/login-callback` está presente.
1. Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.
1. Os padrões restantes para o aplicativo são aceitáveis para essa experiência.
1. Selecione o botão **Salvar**.

Registre as seguintes informações:

* ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111`)
* ID do diretório (ID do locatário) (por exemplo, `22222222-2222-2222-2222-222222222222`)

Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample`). O nome da pasta também se torna parte do nome do projeto.

## <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar contas corporativas ou de estudante (`SingleOrg`), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Substitua `{VERSION}` na referência do pacote anterior pela versão do pacote `Microsoft.AspNetCore.Blazor.Templates` mostrado no artigo <xref:blazor/get-started>.

O pacote de `Microsoft.Authentication.WebAssembly.Msal` adiciona transitivamente o pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ao aplicativo.

## <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O suporte para autenticação de usuários é registrado no contêiner de serviço com o método de extensão `AddMsalAuthentication` fornecido pelo pacote `Microsoft.Authentication.WebAssembly.Msal`. Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

O método `AddMsalAuthentication` aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo. Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.

O modelo Webassembly Blazor não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura. Para provisionar um token como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão do `MsalProviderOptions`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> O escopo do token de acesso padrão deve estar no formato `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (por exemplo, `11111111-1111-1111-1111-111111111111/API.Access`). Se um esquema ou esquema e host for fornecido para a configuração de escopo (como mostrado no portal do Azure), o *aplicativo cliente* lançará uma exceção sem tratamento quando receber uma resposta de *401 não autorizado* do *aplicativo de API do servidor*.

## <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authentication/azure-active-directory/index>
