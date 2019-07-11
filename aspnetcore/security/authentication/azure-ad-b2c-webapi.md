---
title: Autenticação em APIs web com o Azure Active Directory B2C no ASP.NET Core
author: camsoper
description: Descubra como configurar a autenticação do Azure Active Directory B2C com a API Web ASP.NET Core. Teste a autenticado API web com o Postman.
ms.author: casoper
ms.date: 09/21/2018
ms.custom: mvc, seodec18
uid: security/authentication/azure-ad-b2c-webapi
ms.openlocfilehash: c917a5130857165716bd801ac19dad0f53a7d214
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815375"
---
# <a name="authentication-in-web-apis-with-azure-active-directory-b2c-in-aspnet-core"></a>Autenticação em APIs web com o Azure Active Directory B2C no ASP.NET Core

Por [Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C do diretório](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) é uma solução de gerenciamento de identidade de nuvem para aplicativos web e móveis. O serviço fornece autenticação para aplicativos hospedados na nuvem e locais. Tipos de autenticação incluem contas individuais, contas de rede social e contas corporativas de federado. B2C do AD do Azure também fornece a autenticação multifator com configuração mínima.

Azure Active Directory (Azure AD) e o Azure AD B2C são ofertas de produtos separados. Um locatário do AD do Azure representa uma organização, enquanto que um locatário do Azure AD B2C representa uma coleção de identidades a serem usados com aplicativos de terceira parte confiável. Para obter mais informações, consulte [do Azure AD B2C: Perguntas frequentes (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

Uma vez que as APIs da web não tem nenhuma interface do usuário, eles são não é possível redirecionar o usuário a um serviço de token seguro, como o Azure AD B2C. Em vez disso, a API é passada um token de portador do aplicativo de chamada, que já tiver autenticado o usuário com o Azure AD B2C. A API, em seguida, valida o token sem interação direta do usuário.

Neste tutorial, saiba como:

> [!div class="checklist"]
> * Crie um locatário do Azure Active Directory B2C.
> * Registre uma API da Web no B2C do AD do Azure.
> * Use o Visual Studio para criar uma API da Web configurado para usar o locatário do Azure AD B2C para autenticação.
> * Configure políticas para controlar o comportamento do locatário do Azure AD B2C.
> * Usar o Postman para simular um aplicativo web que apresenta uma caixa de diálogo de logon, recupera um token e usa-o para fazer uma solicitação em relação a API da web.

## <a name="prerequisites"></a>Pré-requisitos

A seguir é necessários para este passo a passo:

* [Assinatura do Microsoft Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [Postman](https://www.getpostman.com/postman)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Criar o locatário do Azure Active Directory B2C

Criar um locatário do Azure AD B2C [conforme descrito na documentação do](/azure/active-directory-b2c/active-directory-b2c-get-started). Quando solicitado, associar o locatário com uma assinatura do Azure é opcional para este tutorial.

## <a name="configure-a-sign-up-or-sign-in-policy"></a>Configurar uma política de inscrição ou entrada

Use as etapas na documentação do Azure AD B2C para [criar uma política de inscrição ou entrada](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions). Nomeie a diretiva **SiUpIn**.  Use os valores de exemplo fornecidos na documentação do **provedores de identidade**, **atributos de inscrição**, e **declarações do aplicativo**. Usando o **executar agora** botão para testar a política conforme descrito na documentação é opcional.

## <a name="register-the-api-in-azure-ad-b2c"></a>Registrar a API no B2C do AD do Azure

No locatário do Azure AD B2C recém-criado, registre sua API usando [as etapas na documentação do](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) sob o **registrar uma API web** seção.

Use os seguintes valores:

| Configuração                       | Valor               | Observações                                                                                  |
|-------------------------------|---------------------|----------------------------------------------------------------------------------------|
| **Nome**                      | *{Nome da API}*        | Insira um **nome** para o aplicativo que descrevem seu aplicativo para os consumidores.                     |
| **Incluir aplicativo web / API web** | Sim                 |                                                                                        |
| **Permitir fluxo implícito**       | Sim                 |                                                                                        |
| **URL de resposta**                 | `https://localhost` | URLs de resposta são pontos de extremidade onde o Azure AD B2C retornará os tokens que o aplicativo solicitar. |
| **URI da ID do aplicativo**                | *api*               | O URI não precisa resolver para um endereço físico. Ele só precisa ser exclusivo.     |
| **Incluir cliente nativo**     | Não                  |                                                                                        |

Depois que a API é registrada, é exibida a lista de aplicativos e APIs no locatário. Selecione a API que foi registrada anteriormente. Selecione o **cópia** ícone à direita do **ID do aplicativo** campo para copiá-lo na área de transferência. Selecione **escopos publicados** e verifique se o padrão *user_impersonation* escopo está presente.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Criar um aplicativo ASP.NET Core no Visual Studio

O modelo de aplicativo Web Visual Studio pode ser configurado para usar o locatário do Azure AD B2C para autenticação.

No Visual Studio:

1. Crie um novo Aplicativo Web ASP.NET Core. 
2. Selecione **API Web** da lista de modelos.
3. Selecione o **alterar autenticação** botão.

    ![Botão de autenticação de alteração](./azure-ad-b2c-webapi/change-auth-button.png)

4. No **alterar autenticação** caixa de diálogo, selecione **contas de usuário individuais** > **conectar-se ao repositório de usuário existente na nuvem**.

    ![Caixa de diálogo Alterar autenticação](./azure-ad-b2c-webapi/change-auth-dialog.png)

5. Preencha o formulário com os seguintes valores:

    | Configuração                       | Valor                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Nome de domínio**               | *{o nome de domínio do seu locatário B2C}*                |
    | **ID do aplicativo**            | *{Colar a ID do aplicativo da área de transferência}*       |
    | **Política de inscrição ou entrada** | B2C_1_SiUpIn                                          |

    Selecione **Okey** para fechar o **alterar autenticação** caixa de diálogo. Selecione **Okey** para criar o aplicativo web.

O Visual Studio cria a API da web com um controlador chamado *ValuesController.cs* que retorna valores embutidos para solicitações GET. A classe é decorada com o [atributo Authorize](xref:security/authorization/simple), portanto, todas as solicitações requerem autenticação.

## <a name="run-the-web-api"></a>Executar a API da web

No Visual Studio, execute a API. O Visual Studio inicia um navegador apontado na URL da raiz da API. Anote a URL na barra de endereços e deixar a API em execução em segundo plano.

> [!NOTE]
> Como não há nenhum controlador definido para a URL da raiz, o navegador pode exibir um erro 404 de (página não encontrada). Esse comportamento é esperado.

## <a name="use-postman-to-get-a-token-and-test-the-api"></a>Usar o Postman para obter um token e a API de teste

[Postman](https://getpostman.com/postman) é uma ferramenta para testar APIs web. Para este tutorial, carteiro simula um aplicativo web que acessa a API da web em nome do usuário.

### <a name="register-postman-as-a-web-app"></a>Registre o Postman como um aplicativo web

Uma vez que o Postman simula um aplicativo web que obtém tokens de locatário do Azure AD B2C, ele deve ser registrado no locatário como um aplicativo web. Registrar o Postman usando [as etapas na documentação do](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) sob o **registrar um aplicativo web** seção. Parar na **criar um segredo do cliente de aplicativo web** seção. Um segredo do cliente não é necessário para este tutorial. 

Use os seguintes valores:

| Configuração                       | Valor                            | Observações                           |
|-------------------------------|----------------------------------|---------------------------------|
| **Nome**                      | Postman                          |                                 |
| **Incluir aplicativo web / API web** | Sim                              |                                 |
| **Permitir fluxo implícito**       | Sim                              |                                 |
| **URL de resposta**                 | `https://getpostman.com/postman` |                                 |
| **URI da ID do aplicativo**                | *{Deixe em branco}*                  | Não é necessário para este tutorial. |
| **Incluir cliente nativo**     | Não                               |                                 |

O aplicativo web registrado recentemente precisa de permissão para acessar a API da web em nome do usuário.  

1. Selecione **Postman** na lista de aplicativos e selecione **acesso à API** no menu à esquerda.
1. Selecione **+ adicionar**.
1. No **selecionar API** lista suspensa, selecione o nome da API web.
1. No **selecionar escopos** lista suspensa, verifique se todos os escopos são selecionados.
1. Selecione **Okey**.

Observe a ID do aplicativo do aplicativo Postman, pois ele é necessário para obter um token de portador.

### <a name="create-a-postman-request"></a>Criar uma solicitação do Postman

Inicie o Postman. Por padrão, o Postman exibe a **criar novo** caixa de diálogo após a inicialização. Se a caixa de diálogo não for exibida, selecione a **+ novo** botão no canto superior esquerdo.

Dos **criar novo** caixa de diálogo:

1. Selecione **solicitar**.

    ![Botão de solicitação](./azure-ad-b2c-webapi/postman-create-new.png)

2. Insira *obter valores* na **nome da solicitação** caixa.
3. Selecione **+ Criar coleção** para criar uma nova coleção para armazenar a solicitação. Nomear a coleção *tutoriais do ASP.NET Core* e, em seguida, selecione a marca de seleção.

    ![Criar uma nova coleção](./azure-ad-b2c-webapi/postman-create-collection.png)

4. Selecione o **salvar em tutoriais do ASP.NET Core** botão.

### <a name="test-the-web-api-without-authentication"></a>Testar a API da web sem autenticação

Para verificar que a API da web requer autenticação, primeiro verifique uma solicitação sem autenticação.

1. No **insira a URL da solicitação** , digite a URL para `ValuesController`. A URL é o mesmo exibido no navegador com **api/valores** acrescentado. Por exemplo, `https://localhost:44375/api/values`.
2. Selecione o **enviar** botão.
3. Observe o status da resposta é *401 não autorizado*.

    ![resposta 401 não autorizado](./azure-ad-b2c-webapi/postman-401-status.png)

> [!IMPORTANT]
> Se você receber um erro "Não foi possível obter qualquer resposta", você talvez precise desabilitar a verificação do certificado SSL na [configurações do Postman](https://learning.getpostman.com/docs/postman/launching_postman/settings).

### <a name="obtain-a-bearer-token"></a>Obter um token de portador

Para fazer uma solicitação autenticada a API da web, é necessário um token de portador. Postman torna mais fácil entrar no locatário do Azure AD B2C e obter um token.

1. Sobre o **autorização** guia da **tipo** lista suspensa, selecione **OAuth 2.0**. No **adicionar dados de autorização** lista suspensa, selecione **cabeçalhos de solicitação**. Selecione **obter Token de acesso novo**.

    ![Guia de autorização com configurações](./azure-ad-b2c-webapi/postman-auth-tab.png)

2. Conclua o **obter novo TOKEN de acesso** caixa de diálogo da seguinte maneira:

   |                Configuração                 |                                             Valor                                             |                                                                                                                                    Observações                                                                                                                                     |
   |----------------------------------------|-----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |      **Nome do token**       |                                          *{nome do token}*                                       |                                                                                                                   Insira um nome descritivo para o token.                                                                                                                    |
   |      **Tipo de concessão**       |                                           Implícita                                            |                                                                                                                                                                                                                                                                              |
   |     **URL de retorno de chamada**      |                                 `https://getpostman.com/postman`                              |                                                                                                                                                                                                                                                                              |
   |       **URL de autenticação**        | `https://login.microsoftonline.com/{tenant domain name}/oauth2/v2.0/authorize?p=B2C_1_SiUpIn` |  Substitua *{nome de domínio do locatário}* com o nome de domínio do locatário. **IMPORTANTE**: Essa URL deve ter o mesmo nome de domínio que o que for encontrado na `AzureAdB2C.Instance` da API web *appSettings. JSON* arquivo. Consulte a Observação&dagger;.                                                  |
   |       **ID do cliente**       |                *{entrar no aplicativo de Postman **ID do aplicativo**}*                              |                                                                                                                                                                                                                                                                              |
   |         **Escopo**         |         `https://{tenant domain name}/{api}/user_impersonation openid offline_access`       | Substitua *{nome de domínio do locatário}* com o nome de domínio do locatário. Substitua *{api}* com o URI da ID do aplicativo você deu a API da web ao registrado pela primeira vez (nesse caso, `api`). O padrão para a URL é: `https://{tenant}.onmicrosoft.com/{api-id-uri}/{scope name}`.         |
   |         **Estado**         |                                      *{Deixe em branco}*                                          |                                                                                                                                                                                                                                                                              |
   | **Autenticação de cliente** |                                Enviar as credenciais do cliente no corpo                                |                                                                                                                                                                                                                                                                              |

    > [!NOTE]
    > &dagger; A caixa de diálogo de configurações de política no portal do Azure Active Directory B2C exibe duas URLs possíveis: Uma no formato `https://login.microsoftonline.com/`{nome de domínio do locatário} / {informações adicionais de caminho} e o outro no formato `https://{tenant name}.b2clogin.com/`{nome de domínio do locatário} / {informações adicionais de caminho}. Ele tem **críticos** que o domínio encontrado na `AzureAdB2C.Instance` da API web *appSettings. JSON* arquivo corresponde ao usado no aplicativo de web *appSettings. JSON* arquivo. Isso é o mesmo domínio usado para o campo de URL do Auth no Postman. Observe que o Visual Studio usa um formato de URL ligeiramente diferente que o que é exibido no portal. Desde que os domínios corresponderem, a URL funciona.

3. Selecione o **solicitar Token** botão.

4. Postman abre uma nova janela que contém o diálogo de logon do locatário do Azure AD B2C. Entrar com uma conta existente (se uma foi criada a testar as políticas) ou selecione **Inscreva-se agora** para criar uma nova conta. O **esqueceu sua senha?** link é usado para redefinir uma senha esquecida.

5. Depois de entrar com êxito, a janela será fechada e o **gerenciar TOKENS de acesso** caixa de diálogo é exibida. Role para baixo até a parte inferior e selecione o **uso Token** botão.

    ![Onde encontrar o botão "Token de uso"](./azure-ad-b2c-webapi/postman-access-token.png)

### <a name="test-the-web-api-with-authentication"></a>A API da web com autenticação de teste

Selecione o **enviar** botão para enviar a solicitação novamente. Neste momento, o status de resposta estiver *200 Okey* e o conteúdo do JSON está visível na resposta **corpo** guia.

![Status de êxito e de carga](./azure-ad-b2c-webapi/postman-success.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Crie um locatário do Azure Active Directory B2C.
> * Registre uma API da Web no B2C do AD do Azure.
> * Use o Visual Studio para criar uma API da Web configurado para usar o locatário do Azure AD B2C para autenticação.
> * Configure políticas para controlar o comportamento do locatário do Azure AD B2C.
> * Usar o Postman para simular um aplicativo web que apresenta uma caixa de diálogo de logon, recupera um token e usa-o para fazer uma solicitação em relação a API da web.

Continue a desenvolver sua API pelo learning para:

* [Proteger um ASP.NET Core em aplicativo web usando o Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* [Chamar uma API web de um aplicativo web do .NET usando o Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).
* [Personalizar a interface do usuário do Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Configurar os requisitos de complexidade de senha](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Habilitar a autenticação multifator](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Configurar provedores de identidade adicional, como [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [do Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)e outros.
* [Usar a API do Graph do Azure AD](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) para recuperar informações de usuário adicionais, como associação de grupo, do locatário do Azure AD B2C.
