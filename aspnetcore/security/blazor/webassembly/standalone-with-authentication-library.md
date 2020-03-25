---
title: Proteger um ASP.NET Core aplicativo autônomo Webassembly Blazor com a biblioteca de autenticação
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: ea50d94835b044f9c3d6a0561868f081d32cb62a
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218993"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>Proteger um ASP.NET Core aplicativo autônomo Webassembly Blazor com a biblioteca de autenticação

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Para Azure Active Directory (AAD) e Azure Active Directory B2C (AAD B2C), não siga as orientações neste tópico. Consulte os tópicos do AAD e do AAD B2C no nó Sumário.*

Para criar um aplicativo autônomo Webassembly Blazor que usa `Microsoft.AspNetCore.Components.WebAssembly.Authentication` biblioteca, execute o seguinte comando em um shell de comando:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample`). O nome da pasta também se torna parte do nome do projeto.

No Visual Studio, [crie um aplicativo Webassembly Blazor](xref:blazor/get-started). Defina a **autenticação** para **contas de usuário individuais** com a opção **armazenar contas de usuário no aplicativo** .

## <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar contas de usuário individuais, o aplicativo recebe automaticamente uma referência de pacote para o pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` no arquivo de projeto do aplicativo. O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Substitua `{VERSION}` na referência do pacote anterior pela versão do pacote `Microsoft.AspNetCore.Blazor.Templates` mostrado no artigo <xref:blazor/get-started>.

## <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O suporte para autenticação de usuários é registrado no contêiner de serviço com o método de extensão `AddOidcAuthentication` fornecido pelo pacote `Microsoft.AspNetCore.Components.WebAssembly.Authentication`. Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

O suporte de autenticação para aplicativos autônomos é oferecido usando o Open ID Connect (OIDC). O método `AddOidcAuthentication` aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo usando OIDC. Os valores necessários para configurar o aplicativo podem ser obtidos do IP em conformidade com o OIDC. Obtenha os valores ao registrar o aplicativo, que normalmente ocorre em seu portal online.

## <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
