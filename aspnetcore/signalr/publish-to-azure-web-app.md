---
title: Publicar um aplicativo de SignalR de ASP.NET Core no serviço Azure App
author: bradygaster
description: Saiba como publicar um aplicativo de SignalR de ASP.NET Core no serviço Azure App.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: d03a007ca883b3d0391b848e3e92c90469ee640a
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963928"
---
# <a name="publish-an-aspnet-core-opno-locsignalr-app-to-azure-app-service"></a>Publicar um aplicativo de SignalR de ASP.NET Core no serviço Azure App

Por [Brady GASTER](https://twitter.com/bradygaster)

O [serviço de Azure app](/azure/app-service/app-service-web-overview) é um serviço de plataforma de computação em nuvem da [Microsoft](https://azure.microsoft.com/) para hospedar aplicativos Web, incluindo ASP.NET Core.

> [!NOTE]
> Este artigo refere-se à publicação de um aplicativo ASP.NET Core SignalR do Visual Studio. Para obter mais informações, consulte [SignalR Service for Azure](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Publique o aplicativo

Este artigo aborda a publicação usando as ferramentas do Visual Studio. Visual Studio Code usuários podem usar [CLI do Azure](/cli/azure) comandos para publicar aplicativos no Azure. Para obter mais informações, consulte [publicar um aplicativo ASP.NET Core no Azure com ferramentas de linha de comando](/azure/app-service/app-service-web-get-started-dotnet).

1. Clique com o botão direito do mouse no projeto, no **Gerenciador de Soluções**, e selecione **Publicar**.

1. Confirme se o **serviço de aplicativo** e **criar novo** estão selecionados na caixa de diálogo **escolher um destino de publicação** .

1. Selecione **Criar perfil** na lista suspensa do botão **publicar** .

   Insira as informações descritas na tabela a seguir na caixa de diálogo **Criar serviço de aplicativo** e selecione **criar**.

   | Item               | Descrição |
   | ------------------ | ----------- |
   | **Nome**           | Nome exclusivo do aplicativo. |
   | **Assinatura**   | Assinatura do Azure que o aplicativo usa. |
   | **Grupo de recursos** | Grupo de recursos relacionados aos quais o aplicativo pertence. |
   | **Plano de hospedagem**   | Plano de preços para o aplicativo Web. |

1. Selecione o **serviço de SignalR do Azure** na lista suspensa **dependências** > **Adicionar** :

   ![Área de dependências mostrando a seleção do Azure [! Parar. Serviço no-LOC (Signalr)] na lista suspensa adicionar](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. Na caixa de diálogo **serviço de SignalR do Azure** , selecione **criar uma nova instância do serviço de SignalR do Azure**.

1. Forneça um **nome**, um **grupo de recursos**e um **local**. Volte para a caixa de diálogo **serviço de SignalR do Azure** e selecione **Adicionar**.

O Visual Studio conclui as seguintes tarefas:

* Cria um perfil de publicação que contém as configurações de publicação.
* Cria um *aplicativo Web do Azure* com os detalhes fornecidos.
* Publica o aplicativo.
* Inicia um navegador, que carrega o aplicativo Web.

O formato da URL do aplicativo é `{APP SERVICE NAME}.azurewebsites.net`. Por exemplo, um aplicativo chamado `SignalRChatApp` tem uma URL de `https://signalrchatapp.azurewebsites.net`.

Se ocorrer um erro de *Gateway insatisfatório HTTP 502,2* ao implantar um aplicativo destinado a uma versão prévia do .NET Core, consulte [implantar ASP.NET Core versão de visualização para Azure app serviço](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) para resolvê-lo.

## <a name="configure-the-app-in-azure-app-service"></a>Configurar o aplicativo no serviço Azure App

> [!NOTE]
> *Esta seção se aplica somente a aplicativos que não usam o serviço de SignalR do Azure.*
>
> Se o aplicativo usar o serviço de SignalR do Azure, o serviço de aplicativo não exigirá a configuração de afinidade de Application Request Routing (ARR) e os soquetes da Web descritos nesta seção. Os clientes conectam seus soquetes Web ao serviço de SignalR do Azure, não diretamente ao aplicativo.

Para aplicativos hospedados sem o serviço de SignalR do Azure, habilite:

* [Afinidade arr](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) para rotear solicitações de um usuário de volta para a mesma instância do serviço de aplicativo. A configuração padrão é **on**.
* [Web Sockets](xref:fundamentals/websockets) para permitir que o transporte de soquetes da Web funcione. A configuração padrão é **off**.

1. Na portal do Azure, navegue até o aplicativo Web nos **serviços de aplicativos**.
1. Abra a **configuração** > **configurações gerais**.
1. Defina os **soquetes da Web** como **ativado**.
1. Verifique se a **afinidade arr** está definida como **on**.

## <a name="app-service-plan-limits"></a>Limites do plano do serviço de aplicativo

Os soquetes da Web e outros transportes são limitados com base no plano do serviço de aplicativo selecionado. Para obter mais informações, consulte as seções *limites de serviços de nuvem do Azure* e limites de serviço de *aplicativo* do artigo [assinatura e limites de serviço, cotas e restrições do Azure](/azure/azure-subscription-service-limits#app-service-limits) .

## <a name="additional-resources"></a>Recursos adicionais

* [O que é o serviço de SignalR do Azure?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Publicar um aplicativo ASP.NET Core no Azure com ferramentas de linha de comando](/azure/app-service/app-service-web-get-started-dotnet)
* [Hospedar e implantar ASP.NET Core aplicativos de visualização no Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
