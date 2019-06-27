---
title: Publicar um ASP.NET Core SignalR aplicativo no serviço de aplicativo do Azure
author: bradygaster
description: Saiba como publicar um aplicativo do SignalR do ASP.NET Core no serviço de aplicativo do Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/26/2019
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 87a9c93add373b24e3c473912cdbfcc00bbebf7e
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406109"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a>Publicar um ASP.NET Core SignalR aplicativo no serviço de aplicativo do Azure

Por [Brady Gaster](https://twitter.com/bradygaster)

[O serviço de aplicativo do Azure](/azure/app-service/app-service-web-overview) é um [a computação em nuvem do Microsoft](https://azure.microsoft.com/) plataforma de serviço para hospedar aplicativos web, incluindo o ASP.NET Core.

> [!NOTE]
> Este artigo refere-se a publicação de um aplicativo de SignalR do ASP.NET Core no Visual Studio. Para obter mais informações, consulte [serviço SignalR para o Azure](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Publique o aplicativo

Este artigo aborda a publicação usando as ferramentas do Visual Studio. Os usuários do Visual Studio Code podem usar [CLI do Azure](/cli/azure) comandos para publicar aplicativos no Azure. Para obter mais informações, consulte [publicar um aplicativo ASP.NET Core no Azure com ferramentas de linha de comando](/azure/app-service/app-service-web-get-started-dotnet).

1. Clique com o botão direito do mouse no projeto, no **Gerenciador de Soluções**, e selecione **Publicar**.

1. Confirme **serviço de aplicativo** e **criar novo** selecionados no **escolher um destino de publicação** caixa de diálogo.

1. Selecione **criar perfil** da **publicar** botão soltar para baixo.

   Insira as informações descritas na tabela a seguir na **criar serviço de aplicativo** caixa de diálogo e selecione **criar**.

   | Item               | Descrição |
   | ------------------ | ----------- |
   | **Nome**           | Nome exclusivo do aplicativo. |
   | **Assinatura**   | Assinatura do Azure que o aplicativo usa. |
   | **Grupo de recursos** | Grupo de recursos relacionados ao qual pertence o aplicativo. |
   | **Plano de hospedagem**   | Plano de preços para o aplicativo web. |

1. Selecione o **serviço do Azure SignalR** na **dependências** > **Add** lista suspensa:

   ![Área de dependências, mostrando a seleção de serviço do Azure SignalR na lista suspensa Add](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. No **serviço do Azure SignalR** caixa de diálogo, selecione **criar uma nova instância de serviço do Azure SignalR**.

1. Fornecer um **nome**, **grupo de recursos**, e **local**. Volte para o **serviço do Azure SignalR** caixa de diálogo e selecione **Add**.

Visual Studio conclui as seguintes tarefas:

* Cria um perfil de publicação que contém as configurações de publicação.
* Cria uma *aplicativo Web do Azure* com os detalhes fornecidos.
* Publica o aplicativo.
* Inicia um navegador, que carrega o aplicativo web.

O formato da URL do aplicativo é `{APP SERVICE NAME}.azurewebsites.net`. Por exemplo, um aplicativo chamado `SignalRChatApp` tem uma URL de `https://signalrchatapp.azurewebsites.net`.

Se um HTTP *502.2 - Gateway incorreto* erro ocorre ao implantar um aplicativo que tem como alvo uma versão do .NET Core da visualização, consulte [versão de visualização de implantar o ASP.NET Core no serviço de aplicativo do Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) resolvê-lo.

## <a name="configure-the-app-in-azure-app-service"></a>Configurar o aplicativo no serviço de aplicativo do Azure

> [!NOTE]
> *Esta seção aplica-se somente para aplicativos que não usam o serviço do Azure SignalR.*
>
> Se o aplicativo usa o serviço do Azure SignalR, o serviço de aplicativo não exige a configuração de afinidade do roteamento ARR (Application Request) e Web Sockets descritos nesta seção. Os clientes se conectam seus soquetes da Web ao serviço Azure SignalR, não diretamente para o aplicativo.

Para aplicativos hospedados sem o serviço do Azure SignalR, habilite:

* [Afinidade ARR](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) para rotear solicitações de um usuário de volta para a mesma instância do serviço de aplicativo. A configuração padrão é **em**.
* [Web Sockets](xref:fundamentals/websockets) para permitir que o transporte de soquetes da Web para a função. A configuração padrão é **desativar**.

1. No portal do Azure, navegue até o aplicativo web no **serviços de aplicativos**.
1. Abra **Configuration** > **configurações gerais**.
1. Definir **Web sockets** à **em**.
1. Verifique **afinidade ARR** é definido como **em**.

## <a name="app-service-plan-limits"></a>Limita o plano do serviço de aplicativo

Soquetes da Web e outros transportes são limitados com base no plano de serviço de aplicativo selecionado. Para obter mais informações, consulte o *serviços de nuvem do Azure limita* e *limites do serviço de aplicativo* seções o [assinatura do Azure e limites de serviço, cotas e restrições](/azure/azure-subscription-service-limits#app-service-limits) artigo.

## <a name="additional-resources"></a>Recursos adicionais

* [O que é o serviço do Azure SignalR?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Publicar um aplicativo ASP.NET Core no Azure com ferramentas de linha de comando](/azure/app-service/app-service-web-get-started-dotnet)
* [Hospedar e implantar aplicativos de visualização do ASP.NET Core no Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
