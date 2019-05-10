---
title: Autenticação de nuvem com o Azure Active Directory B2C no ASP.NET Core
author: camsoper
description: Descubra como configurar a autenticação do Azure Active Directory B2C com o ASP.NET Core.
ms.date: 02/27/2019
ms.custom: mvc
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 86be999e02cfe34193bd594dcf89e8872590cca5
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898013"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>Autenticação de nuvem com o Azure Active Directory B2C no ASP.NET Core

Por [Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C do diretório](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) é uma solução de gerenciamento de identidade de nuvem para aplicativos web e móveis. O serviço fornece autenticação para aplicativos hospedados na nuvem e locais. Tipos de autenticação incluem contas individuais, contas de rede social e contas corporativas de federado. Além disso, o Azure AD B2C pode fornecer a autenticação multifator com configuração mínima.

> [!TIP]
> Azure Active Directory (Azure AD) e o Azure AD B2C são ofertas de produtos separados. Um locatário do AD do Azure representa uma organização, enquanto que um locatário do Azure AD B2C representa uma coleção de identidades a serem usados com aplicativos de terceira parte confiável. Para obter mais informações, consulte [do Azure AD B2C: Perguntas frequentes (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

Neste tutorial, saiba como:

> [!div class="checklist"]
> * Criar um locatário do Azure Active Directory B2C
> * Registrar um aplicativo no Azure B2C do AD
> * Usar o Visual Studio para criar um aplicativo web do ASP.NET Core configurado para usar o locatário do Azure AD B2C para autenticação
> * Configurar políticas para controlar o comportamento do locatário do Azure AD B2C

## <a name="prerequisites"></a>Pré-requisitos

A seguir é necessários para este passo a passo:

* [Assinatura do Microsoft Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2017](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) (qualquer edição)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Criar o locatário do Azure Active Directory B2C

Criar um locatário do Azure Active Directory B2C [conforme descrito na documentação do](/azure/active-directory-b2c/active-directory-b2c-get-started). Quando solicitado, associar o locatário com uma assinatura do Azure é opcional para este tutorial.

## <a name="register-the-app-in-azure-ad-b2c"></a>Registrar o aplicativo no Azure B2C do AD

No locatário do Azure AD B2C recém-criado, registre seu aplicativo usando [as etapas na documentação do](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) sob o **registrar um aplicativo web** seção. Parar na **criar um segredo do cliente de aplicativo web** seção. Um segredo do cliente não é necessário para este tutorial. 

Use os seguintes valores:

| Configuração                       | Valor                     | Observações                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**                      | *&lt;Nome do aplicativo&gt;*        | Insira um **nome** para o aplicativo que descrevem seu aplicativo para os consumidores.                                                                                                                                 |
| **Incluir aplicativo web / API web** | Sim                       |                                                                                                                                                                                                    |
| **Permitir fluxo implícito**       | Sim                       |                                                                                                                                                                                                    |
| **URL de resposta**                 | `https://localhost:44300/signin-oidc` | URLs de resposta são pontos de extremidade onde o Azure AD B2C retornará os tokens que o aplicativo solicitar. O Visual Studio fornece a URL de resposta para usar. Por enquanto, insira `https://localhost:44300/signin-oidc` preencha o formulário. |
| **URI da ID do aplicativo**                | Deixe em branco               | Não é necessário para este tutorial.                                                                                                                                                                    |
| **Incluir cliente nativo**     | Não                        |                                                                                                                                                                                                    |

> [!WARNING]
> Se estar ciente de como configurar uma URL de resposta não sejam localhost, o [restrições sobre o que é permitido na lista de URL de resposta](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url). 

Depois que o aplicativo é registrado, é exibida a lista de aplicativos no locatário. Selecione o aplicativo que acabou de registrar. Selecione o **cópia** ícone à direita do **ID do aplicativo** campo para copiá-lo na área de transferência.

Nada mais podem ser configurado no locatário do Azure AD B2C neste momento, mas deixe a janela do navegador aberta. Não há mais de configuração depois que o aplicativo ASP.NET Core é criado.

## <a name="create-an-aspnet-core-app-in-visual-studio-2017"></a>Criar um aplicativo ASP.NET Core no Visual Studio 2017

O modelo de aplicativo Web Visual Studio pode ser configurado para usar o locatário do Azure AD B2C para autenticação.

No Visual Studio:

1. Crie um novo Aplicativo Web ASP.NET Core. 
2. Selecione **aplicativo Web** da lista de modelos.
3. Selecione o **alterar autenticação** botão.
    
    ![Botão de autenticação de alteração](./azure-ad-b2c/_static/changeauth.png)

4. No **alterar autenticação** caixa de diálogo, selecione **contas de usuário individuais**e, em seguida, selecione **conectar-se ao repositório de usuário existente na nuvem** na lista suspensa. 
    
    ![Caixa de diálogo Alterar autenticação](./azure-ad-b2c/_static/changeauthdialog.png)

5. Preencha o formulário com os seguintes valores:
    
    | Configuração                       | Valor                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Nome de domínio**               | *&lt;o nome de domínio do seu locatário do B2C&gt;*          |
    | **ID do aplicativo**            | *&lt;Cole a ID do aplicativo da área de transferência&gt;* |
    | **Caminho de retorno de chamada**             | *&lt;Use o valor padrão&gt;*                       |
    | **Política de inscrição ou entrada** | `B2C_1_SiUpIn`                                        |
    | **Política de redefinição de senha**     | `B2C_1_SSPR`                                          |
    | **Editar política de perfil**       | *&lt;Deixe em branco&gt;*                                 |
    
    Selecione o **cópia** próximo ao link **URI de resposta** para copiar o URI de resposta para a área de transferência. Selecione **Okey** para fechar o **alterar autenticação** caixa de diálogo. Selecione **Okey** para criar o aplicativo web.

## <a name="finish-the-b2c-app-registration"></a>Concluir o registro do aplicativo B2C

Retornar à janela do navegador com as propriedades do aplicativo B2C ainda abertas. Alterar temporários **URL de resposta** especificado anteriormente para o valor copiado do Visual Studio. Selecione **salvar** na parte superior da janela.

> [!TIP]
> Se você não copiar a URL de resposta, use o endereço HTTPS na guia Debug nas propriedades do projeto da web e acrescente o **CallbackPath** o valor da *appSettings. JSON*.

## <a name="configure-policies"></a>Configurar políticas

Use as etapas na documentação do Azure AD B2C para [criar uma política de inscrição ou entrada](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy)e então [criar uma política de redefinição de senha](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy). Use os valores de exemplo fornecidos na documentação do **provedores de identidade**, **atributos de inscrição**, e **declarações do aplicativo**. Usando o **executar agora** botão para testar as políticas, conforme descrito na documentação é opcional.

> [!WARNING]
> Verifique se os nomes de política são exatamente como descrito na documentação, como essas políticas foram usadas na **alterar autenticação** caixa de diálogo no Visual Studio. Os nomes de política podem ser verificados no *appSettings. JSON*.

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a>Configure as opções de JwtBearer/OpenIdConnectOptions/Cookie subjacentes

Para configurar as opções subjacentes diretamente, use a constante do esquema apropriado no `Startup.ConfigureServices`:

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a>Executar o aplicativo

No Visual Studio, pressione **F5** para compilar e executar o aplicativo. Depois que o aplicativo web for iniciado, selecione **Accept** para aceitar o uso de cookies (se solicitado) e, em seguida, selecione **entrar**.

![Entrar no aplicativo](./azure-ad-b2c/_static/signin.png)

O navegador é redirecionado para o locatário do Azure AD B2C. Entrar com uma conta existente (se uma foi criada a testar as políticas) ou selecione **Inscreva-se agora** para criar uma nova conta. O **esqueceu sua senha?** link é usado para redefinir uma senha esquecida.

![Logon no Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

Depois de entrar com êxito, o navegador é redirecionado para o aplicativo web.

![Êxito](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um locatário do Azure Active Directory B2C
> * Registrar um aplicativo no Azure B2C do AD
> * Usar o Visual Studio para criar um aplicativo Web do ASP.NET Core configurados para usar o locatário do Azure AD B2C para autenticação
> * Configurar políticas para controlar o comportamento do locatário do Azure AD B2C

Agora que o aplicativo ASP.NET Core está configurado para usar o Azure AD B2C para autenticação, o [atributo Authorize](xref:security/authorization/simple) pode ser usado para proteger seu aplicativo. Continue a desenvolver seu aplicativo aprendendo a:

* [Personalizar a interface do usuário do Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Configurar os requisitos de complexidade de senha](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Habilitar a autenticação multifator](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Configurar provedores de identidade adicional, como [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [do Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)e outros.
* [Usar a API do Graph do Azure AD](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) para recuperar informações de usuário adicionais, como associação de grupo, do locatário do Azure AD B2C.
* [Proteger um ASP.NET Core API da web usando o Azure AD B2C](xref:security/authentication/azure-ad-b2c-webapi).
* [Chamar uma API web de um aplicativo web do .NET usando o Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).
