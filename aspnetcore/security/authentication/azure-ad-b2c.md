---
title: Autenticação de nuvem com Azure Active Directory B2C no ASP.NET Core
author: camsoper
description: Descubra como configurar a autenticação do Azure Active Directory B2C com o ASP.NET Core.
ms.author: casoper
ms.custom: mvc
ms.date: 01/21/2019
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 136fa47788456492a9a7fe6d9d9e5996c13e8c20
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727281"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>Autenticação de nuvem com Azure Active Directory B2C no ASP.NET Core

Por [Cam Soper](https://twitter.com/camsoper)

O [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure ad B2C) é uma solução de gerenciamento de identidade de nuvem para aplicativos Web e móveis. O serviço fornece autenticação para aplicativos hospedados na nuvem e locais. Tipos de autenticação incluem contas individuais, contas de rede social e contas corporativas de federado. Além disso, Azure AD B2C pode fornecer autenticação multifator com configuração mínima.

> [!TIP]
> Azure Active Directory (Azure AD) e o Azure AD B2C são ofertas de produtos separados. Um locatário do AD do Azure representa uma organização, enquanto que um locatário do Azure AD B2C representa uma coleção de identidades a serem usados com aplicativos de terceira parte confiável. Para saber mais, consulte [Azure ad B2C: perguntas frequentes (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um locatário do Azure Active Directory B2C
> * Registrar um aplicativo no Azure AD B2C
> * Use o Visual Studio para criar um aplicativo Web ASP.NET Core configurado para usar o locatário Azure AD B2C para autenticação
> * Configurar políticas que controlam o comportamento do locatário do Azure AD B2C

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

A seguir é necessários para este passo a passo:

* [Assinatura Microsoft Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Criar o locatário do Azure Active Directory B2C

Crie um locatário Azure Active Directory B2C [conforme descrito na documentação](/azure/active-directory-b2c/active-directory-b2c-get-started). Quando solicitado, associar o locatário com uma assinatura do Azure é opcional para este tutorial.

## <a name="register-the-app-in-azure-ad-b2c"></a>Registrar o aplicativo no Azure AD B2C

No locatário Azure AD B2C recém-criado, Registre seu aplicativo usando [as etapas na documentação na](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) seção **registrar um aplicativo Web** . Pare na seção **criar um segredo do cliente de aplicativo Web** . Um segredo do cliente não é necessário para este tutorial. 

Use os seguintes valores:

| Configuração                       | {1&gt;Valor&lt;1}                     | {1&gt;Observações&lt;1}                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**                      | *nome do aplicativo &lt;&gt;*        | Insira um **nome** para o aplicativo que descreve seu aplicativo para os consumidores.                                                                                                                                 |
| **Incluir aplicativo Web/API Web** | Sim                       |                                                                                                                                                                                                    |
| **Permitir fluxo implícito**       | Sim                       |                                                                                                                                                                                                    |
| **URL de Resposta**                 | `https://localhost:44300/signin-oidc` | As URLs de Resposta são pontos de extremidade para onde o Azure AD B2C retornará os tokens que o aplicativo solicitar. O Visual Studio fornece a URL de resposta a ser usada. Por enquanto, insira `https://localhost:44300/signin-oidc` para preencher o formulário. |
| **URI da ID do Aplicativo**                | Deixar em branco               | Não é necessário para este tutorial.                                                                                                                                                                    |
| **Incluir cliente nativo**     | Não                        |                                                                                                                                                                                                    |

> [!WARNING]
> Se estiver configurando uma URL de resposta não localhost, esteja ciente das [restrições sobre o que é permitido na lista URL de resposta](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application). 

Depois que o aplicativo é registrado, a lista de aplicativos no locatário é exibida. Selecione o aplicativo que acabou de ser registrado. Selecione o ícone de **cópia** à direita do campo **ID do aplicativo** para copiá-lo para a área de transferência.

Nada mais pode ser configurado no locatário Azure AD B2C neste momento, mas deixe a janela do navegador aberta. Há mais configuração após a criação do aplicativo ASP.NET Core.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Criar um aplicativo ASP.NET Core no Visual Studio

O modelo de aplicativo Web Visual Studio pode ser configurado para usar o locatário do Azure AD B2C para autenticação.

No Visual Studio:

1. Crie um novo Aplicativo Web ASP.NET Core. 
2. Selecione **aplicativo Web** na lista de modelos.
3. Selecione o botão **alterar autenticação** .
    
    ![Botão de autenticação de alteração](./azure-ad-b2c/_static/changeauth.png)

4. Na caixa de diálogo **alterar autenticação** , selecione **contas de usuário individuais**e, em seguida, selecione **conectar a um repositório de usuários existente na nuvem** na lista suspensa. 
    
    ![Caixa de diálogo Alterar autenticação](./azure-ad-b2c/_static/changeauthdialog.png)

5. Preencha o formulário com os seguintes valores:
    
    | Configuração                       | {1&gt;Valor&lt;1}                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Nome de domínio**               | *&lt;o nome de domínio do seu locatário B2C&gt;*          |
    | **ID do aplicativo**            | *&lt;colar a ID do aplicativo da área de transferência&gt;* |
    | **Caminho de retorno de chamada**             | *&lt;usar o valor padrão&gt;*                       |
    | **Política de inscrição ou entrada** | `B2C_1_SiUpIn`                                        |
    | **Redefinir política de senha**     | `B2C_1_SSPR`                                          |
    | **Editar política de perfil**       | *&lt;deixar em branco&gt;*                                 |
    
    Selecione o link **copiar** ao lado de **URI de resposta** para copiar o URI de resposta para a área de transferência. Selecione **OK** para fechar a caixa de diálogo **alterar autenticação** . Selecione **OK** para criar o aplicativo Web.

## <a name="finish-the-b2c-app-registration"></a>Concluir o registro do aplicativo B2C

Retorne à janela do navegador com as propriedades do aplicativo B2C ainda abertas. Altere a **URL de resposta** temporária especificada anteriormente para o valor copiado do Visual Studio. Selecione **salvar** na parte superior da janela.

> [!TIP]
> Se você não tiver copiado a URL de resposta, use o endereço HTTPS da guia Depurar nas propriedades do projeto Web e acrescente o valor **CallbackPath** de *appSettings. JSON*.

## <a name="configure-policies"></a>Configurar políticas

Use as etapas na documentação do Azure AD B2C para [criar uma política de inscrição ou de entrada](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)e, em seguida, [crie uma política de redefinição de senha](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions). Use os valores de exemplo fornecidos na documentação para **provedores de identidade**, **atributos de inscrição**e **declarações de aplicativo**. Usar o botão **executar agora** para testar as políticas, conforme descrito na documentação é opcional.

> [!WARNING]
> Verifique se os nomes de política são exatamente conforme descrito na documentação, pois essas políticas foram usadas na caixa de diálogo **alterar autenticação** no Visual Studio. Os nomes de política podem ser verificados em *appSettings. JSON*.

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a>Configurar as opções subjacentes de OpenIdConnectOptions/JwtBearer/cookie

Para configurar as opções subjacentes diretamente, use a constante de esquema apropriada no `Startup.ConfigureServices`:

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

No Visual Studio, pressione **F5** para compilar e executar o aplicativo. Depois que o aplicativo Web for iniciado, selecione **aceitar** para aceitar o uso de cookies (se solicitado) e, em seguida, selecione **entrar**.

![Entrar no aplicativo](./azure-ad-b2c/_static/signin.png)

O navegador redireciona para o locatário do Azure AD B2C. Entre com uma conta existente (se uma tiver sido criada testando as políticas) ou selecione **inscrever-se agora** para criar uma nova conta. O link **esqueceu sua senha?** é usado para redefinir uma senha esquecida.

![Logon do Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

Depois de entrar com êxito, o navegador redireciona para o aplicativo Web.

![Êxito](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um locatário do Azure Active Directory B2C
> * Registrar um aplicativo no Azure AD B2C
> * Use o Visual Studio para criar um aplicativo Web ASP.NET Core configurado para usar o locatário Azure AD B2C para autenticação
> * Configurar políticas que controlam o comportamento do locatário do Azure AD B2C

Agora que o aplicativo ASP.NET Core está configurado para usar Azure AD B2C para autenticação, o [atributo autorizar](xref:security/authorization/simple) pode ser usado para proteger seu aplicativo. Continue desenvolvendo seu aplicativo aprendendo a:

* [Personalizar a interface do usuário do Azure ad B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Configurar requisitos de complexidade de senha](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Habilite a autenticação multifator](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Configure provedores de identidade adicionais, como [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)e outros.
* [Use o API do Graph do Azure ad](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) para recuperar informações adicionais do usuário, como associação de grupo, do locatário Azure ad B2C.
* [Proteger uma API Web do ASP.NET Core usando Azure ad B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).
* [Chamar uma API Web .net de um aplicativo Web .NET usando Azure ad B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).