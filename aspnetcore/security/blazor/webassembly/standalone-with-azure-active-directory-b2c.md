---
title: Proteger um aplicativo ASP.NET Core Blazor Webassembly autônomo com Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: b4d32e91b4013cbea37baecb972a535d2874d3d1
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434454"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Proteger um aplicativo ASP.NET Core Blazor Webassembly autônomo com Azure Active Directory B2C

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Para criar um aplicativo autônomo Webassembly Blazor que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação:

1. Siga as orientações nos tópicos a seguir para criar um locatário e registrar um aplicativo Web no portal do Azure:

   * [Criar um locatário AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; registrar as seguintes informações:

     1 \. Instância de AAD B2C (por exemplo, `https://contoso.b2clogin.com/`, que inclui a barra à direita)<br>
     2 \. AAD B2C domínio de locatário (por exemplo, `contoso.onmicrosoft.com`)

   * [Registre um aplicativo web](/azure/active-directory-b2c/tutorial-register-applications) &ndash; faça as seguintes seleções durante o registro do aplicativo:

     1 \. Defina **aplicativo Web/API Web** como **Sim**.<br>
     2 \. Defina **permitir fluxo implícito** como **Sim**.<br>
     3 \. Adicione uma **URL de resposta** de `https://localhost:5001/authentication/login-callback`.

     Registre a ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111`).

   * [Criar fluxos de usuário](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; criar um fluxo de usuário de inscrição e entrada.

     No mínimo, selecione o atributo **Application claims** > **Display Name** user para preencher o `context.User.Identity.Name` no componente `LoginDisplay` (*Shared/LoginDisplay. Razor*).

     Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin`).

1. Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample`). O nome da pasta também se torna parte do nome do projeto.

## <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar uma conta B2C individual (`IndividualB2C`), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

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
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

O método `AddMsalAuthentication` aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo. Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.

O modelo Webassembly Blazor não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura. Para provisionar um token como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão do `MsalProviderOptions`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{API ID URI}/{SCOPE}");
});
```

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

* <xref:security/authentication/azure-ad-b2c>
* [Tutorial: Criar um locatário do Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
