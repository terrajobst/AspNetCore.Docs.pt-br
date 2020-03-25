---
title: Proteger um ASP.NET Core aplicativo autônomo Webassembly Blazor com contas da Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: be73bec971f96bd64afc735a1ea750d47c7bc383
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219253"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Proteger um ASP.NET Core aplicativo autônomo Webassembly Blazor com contas da Microsoft

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Para criar um aplicativo autônomo Webassembly Blazor que usa [contas da Microsoft com Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para autenticação:

1. [Criar um locatário do AAD e um aplicativo Web](/azure/active-directory/develop/v2-overview)

   Registre um aplicativo do AAD na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:

   1 \. Forneça um **nome** para o aplicativo (por exemplo, **Blazor AAD do cliente**).<br>
   2 \. Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional**.<br>
   3 \. Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI de redirecionamento de `https://localhost:5001/authentication/login-callback`.<br>
   4 \. Desabilite as **permissões** > **conceder administrador concento para OpenID e caixa de seleção permissões de offline_access** .<br>
   5 \. Selecione **Registrar**.

   Em **autenticação** > **configurações da plataforma** > **Web**:

   1 \. Confirme se o **URI de redirecionamento** do `https://localhost:5001/authentication/login-callback` está presente.<br>
   2 \. Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.<br>
   3 \. Os padrões restantes para o aplicativo são aceitáveis para essa experiência.<br>
   4 \. Selecione o botão **Salvar**.

   Registre a ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111`).

1. Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample`). O nome da pasta também se torna parte do nome do projeto.

Depois de criar o aplicativo, você deve ser capaz de:

* Faça logon no aplicativo usando uma conta da Microsoft.
* Solicite tokens de acesso para APIs da Microsoft usando a mesma abordagem que para aplicativos Blazor autônomos desde que você tenha configurado o aplicativo corretamente. Para obter mais informações, consulte [início rápido: configurar um aplicativo para expor APIs Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

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
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

O método `AddMsalAuthentication` aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo. Os valores necessários para configurar o aplicativo podem ser obtidos na configuração de contas da Microsoft quando você registra o aplicativo.

## <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

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

* [Início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Início rápido: configurar um aplicativo para expor APIs Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
