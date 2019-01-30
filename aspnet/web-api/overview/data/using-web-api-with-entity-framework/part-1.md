---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-1
title: Usando a API Web 2 com o Entity Framework 6 | Microsoft Docs
author: MikeWasson
description: Este tutorial ensinará os fundamentos da criação de um aplicativo web com uma API Web ASP.NET de back-end. O tutorial usa o Entity Framework 6 para o layout de dados...
ms.author: riande
ms.date: 01/17/2019
ms.assetid: e879487e-dbcd-4b33-b092-d67c37ae768c
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-1
msc.type: authoredcontent
ms.openlocfilehash: 266c808e3525787181038d2de473194989039e02
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236517"
---
<a name="using-web-api-2-with-entity-framework-6"></a>Usando a API Web 2 com o Entity Framework 6
====================

[Baixe o projeto concluído](https://github.com/MikeWasson/BookService)

> Este tutorial ensina as Noções básicas da criação de um aplicativo web com uma API Web ASP.NET de back-end. O tutorial usa o Entity Framework 6 para a camada de dados e o Knockout. js para o aplicativo de JavaScript do lado do cliente. O tutorial também mostra como implantar o aplicativo em aplicativos de Web do serviço de aplicativo do Azure.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Versões de software usadas no tutorial
>
> - API Web 2.1
> - Visual Studio 2017 (Baixe o Visual Studio 2017 [aqui](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017))
> - Entity Framework 6
> - .NET 4.7
> - [Knockout.js](http://knockoutjs.com/) 3.1

Este tutorial usa a API Web ASP.NET 2 com o Entity Framework 6 para criar um aplicativo web que manipula um banco de dados de back-end. Aqui está uma captura de tela do aplicativo que você criará.

[![](part-1/_static/image2.png)](part-1/_static/image1.png)

O aplicativo usa um design de aplicativo de página única (SPA). "Aplicativo de página única" é o termo geral para um aplicativo web que carrega uma página HTML única e, em seguida, atualiza a página dinamicamente, em vez de carregar novas páginas. Após o carregamento de página inicial, o aplicativo se comunica com o servidor por meio de solicitações AJAX. O AJAX solicita dados JSON retornados, o que o aplicativo usa para atualizar a interface do usuário.

AJAX não é novo, mas hoje, há estruturas de JavaScript que facilitam criar e manter um grande aplicativo SPA sofisticado. Este tutorial usa [Knockout. js](http://knockoutjs.com/), mas você pode usar qualquer estrutura de cliente JavaScript.

Aqui estão os principais blocos de construção para este aplicativo:

- ASP.NET MVC cria a página HTML.
- API Web ASP.NET manipula as solicitações AJAX e retorna dados JSON.
- Knockout. js-associa dados os elementos HTML para os dados JSON.
- Entity Framework se comunica com o banco de dados.

## <a name="see-this-app-running-on-azure"></a>Consulte esse aplicativo em execução no Azure

Você gostaria de ver o site concluído em execução como um aplicativo web ao vivo? Você pode implantar uma versão completa do aplicativo para sua conta do Azure, selecionando o botão a seguir.

[![](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/BookService)

Você precisa de uma conta do Azure para implantar essa solução no Azure. Se você ainda não tiver uma conta, você tem as seguintes opções:

- [Abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) -obtenha créditos você pode usar para experimentar serviços pagos do Azure e até mesmo após eles serem utilizados, você pode manter a conta e utilizar os serviços do Azure gratuitos.
- [Ativar benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) -sua assinatura do MSDN concede créditos todos os meses que você pode usar para serviços pagos do Azure.

## <a name="create-the-project"></a>Criar o projeto

Abra o Visual Studio. Dos **arquivo** menu, selecione **New**, em seguida, selecione **projeto**. (Ou selecione **novo projeto** na página inicial.)

No **novo projeto** caixa de diálogo, selecione **Web** no painel esquerdo e **aplicativo Web ASP.NET (.NET Framework)** no painel central. Nomeie o projeto **BookService** e selecione **Okey**.

[![](part-1/_static/image11.png)](part-1/_static/image11.png)

No **novo projeto ASP.NET** caixa de diálogo, selecione o **API da Web** modelo.

[![](part-1/_static/image12.png)](part-1/_static/image12.png)


Selecione **OK** para criar o projeto.

## <a name="configure-azure-settings-optional"></a>Definir configurações do Azure (opcional)

Depois de criar o projeto, você pode optar por implantar aplicativos de Web do serviço de aplicativo do Azure a qualquer momento. 

1. No Gerenciador de soluções, clique com botão direito no seu projeto e selecione **publicar**.

2. Na janela que aparece, selecione **iniciar**. O **escolher um destino de publicação** caixa de diálogo é exibida.

   [![](part-1/_static/image14.png)](part-1/_static/image14.png)

3. Selecione **Criar Perfil**. A caixa de diálogo **Criar Serviço de Aplicativo** é exibida.

   [![](part-1/_static/image15.png)](part-1/_static/image15.png)

   Aceite os padrões ou inserir valores diferentes para o nome do aplicativo, grupo de recursos, hospedagem de plano, a assinatura do Azure e a região geográfica. 

4. Selecione **criar um banco de dados SQL**. O **configurar o SQL Server** caixa de diálogo é exibida. 

   [![](part-1/_static/image16.png)](part-1/_static/image16.png)

   Aceite os padrões ou inserir valores diferentes. Insira um **Adminstrator Username** e **senha de administrador** para seu novo banco de dados. Selecione **Okey** quando você terminar. O **criar serviço de aplicativo** página será exibida novamente.

5. Selecione **criar** para criar seu perfil. Uma mensagem é exibida no canto inferior direito, indicando que a implantação está em andamento. Após um curto tempo, o **publicar** janela será exibida novamente.

    [![](part-1/_static/image17.png)](part-1/_static/image17.png)
   
    O perfil que você criou para implantar o aplicativo agora está disponível. 


> [!div class="step-by-step"]
> [Avançar](part-2.md)
