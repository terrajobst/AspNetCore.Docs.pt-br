---
title: Publicar um ASP.NET Core SignalR aplicativo ao aplicativo Web do Azure
author: bradygaster
description: Publicar um ASP.NET Core SignalR aplicativo ao aplicativo Web do Azure
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/20/2018
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 66fa855590c49c4284e4b42cae57f3d4d81dd0fc
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837670"
---
# <a name="publish-an-aspnet-core-signalr-app-to-an-azure-web-app"></a>Publicar um ASP.NET Core aplicativo SignalR em um aplicativo Web do Azure

[Aplicativo Web do Azure](/azure/app-service/app-service-web-overview) é um [a computação em nuvem do Microsoft](https://azure.microsoft.com/) plataforma de serviço para hospedar aplicativos web, incluindo o ASP.NET Core.

> [!NOTE]
> Este artigo refere-se a publicação de um aplicativo de SignalR do ASP.NET Core no Visual Studio. Visite [serviço SignalR para o Azure](https://azure.microsoft.com/en-gb/services/signalr-service?) para obter mais informações sobre como usar o SignalR no Azure.

## <a name="publish-the-app"></a>Publique o aplicativo

Visual Studio fornece ferramentas internas para publicação de um aplicativo da Web do Azure. Usuário do Visual Studio Code pode usar [CLI do Azure](/cli/azure) comandos para publicar aplicativos no Azure. Este artigo aborda a publicação usando as ferramentas do Visual Studio. Para publicar um aplicativo usando a CLI do Azure, consulte [publicar um aplicativo ASP.NET Core no Azure com ferramentas de linha de comando](/azure/app-service/app-service-web-get-started-dotnet).

Clique com botão direito no projeto no **Gerenciador de soluções** e selecione **publicar**. Confirme **criar novo** check-in a **escolher um destino de publicação** caixa de diálogo e selecione **publicar**.

![Escolha o destino de publicação](publish-to-azure-web-app/_static/pick-publish-target-dialog.png)

Insira as seguintes informações na **criar serviço de aplicativo** caixa de diálogo e selecione **criar**.

| Item | Descrição |
| ---- | ----------- |
| **Nome do aplicativo** | Um nome exclusivo do aplicativo. |
| **Assinatura** | A assinatura do Azure que o aplicativo usa. |
| **Grupo de recursos** | O grupo de recursos relacionados ao qual pertence o aplicativo.  |
| **Plano de hospedagem** | O plano de preços para o aplicativo web. |

![Criar serviço de aplicativo](publish-to-azure-web-app/_static/create-app-service-dialog.png)

Visual Studio conclui as seguintes tarefas:

* Cria um perfil de publicação que contém as configurações de publicação.
* Cria ou usa um existente *aplicativo Web do Azure* com os detalhes fornecidos.
* Publica o aplicativo.
* Inicia um navegador, com o aplicativo web publicado carregado.

Observe o formato da URL para o aplicativo é *amp;#42;.azurewebsites.NET {nome do aplicativo} .net*. Por exemplo, um aplicativo chamado `SignalRChattR` tem uma URL que se parece com `https://signalrchattr.azurewebsites.net`.

Se ocorrer um erro de HTTP 502.2, consulte [versão de visualização de implantar o ASP.NET Core no serviço de aplicativo do Azure](xref:host-and-deploy/azure-apps/index) resolvê-lo.

## <a name="configure-signalr-web-app"></a>Configurar o aplicativo web de SignalR

Aplicativos do ASP.NET SignalR Core que são publicados como um aplicativo Web deve ter [afinidade ARR](https://en.wikipedia.org/wiki/Application_Request_Routing) habilitado. [WebSockets](xref:fundamentals/websockets) deve ser habilitada, para permitir que o transporte de WebSockets para a função.

No portal do Azure, navegue até **configurações do aplicativo** para seu aplicativo web. Definir **WebSockets** à **na**e verifique se **afinidade ARR** é **em**.

![Configurações do aplicativo Web do Azure no portal do Azure](publish-to-azure-web-app/_static/azure-web-app-settings.png)

 WebSockets e outros transportes [são limitados com base no plano do serviço de aplicativo](/azure/azure-subscription-service-limits#app-service-limits).

## <a name="related-resources"></a>Recursos relacionados

* [Publicar um aplicativo ASP.NET Core no Azure com ferramentas de linha de comando](/azure/app-service/app-service-web-get-started-dotnet)
* [Publicar um aplicativo ASP.NET Core no Azure com o Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs)
* [Hospedar e implantar aplicativos de visualização do ASP.NET Core no Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
