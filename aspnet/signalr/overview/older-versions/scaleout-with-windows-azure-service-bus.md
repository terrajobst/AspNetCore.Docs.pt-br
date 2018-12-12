---
uid: signalr/overview/older-versions/scaleout-with-windows-azure-service-bus
title: Expansão do SignalR com o barramento de serviço do Azure (SignalR 1.x) | Microsoft Docs
author: MikeWasson
description: ''
ms.author: riande
ms.date: 05/01/2013
ms.assetid: 501db899-e68c-49ff-81b2-1dc561bfe908
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-windows-azure-service-bus
msc.type: authoredcontent
ms.openlocfilehash: 687d3d7787baa69410ee35d651a029c69d28c70b
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53286994"
---
<a name="signalr-scaleout-with-azure-service-bus-signalr-1x"></a><span data-ttu-id="081ae-102">Expansão do SignalR com o barramento de serviço do Azure (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="081ae-102">SignalR Scaleout with Azure Service Bus (SignalR 1.x)</span></span>
====================
<span data-ttu-id="081ae-103">por [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="081ae-103">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="081ae-104">Neste tutorial, você implantará um aplicativo do SignalR para uma função do Windows Azure Web, usando o backplane do barramento de serviço para distribuir mensagens a cada instância de função.</span><span class="sxs-lookup"><span data-stu-id="081ae-104">In this tutorial, you will deploy a SignalR application to a Windows Azure Web Role, using the Service Bus backplane to distribute messages to each role instance.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image1.png)

<span data-ttu-id="081ae-105">Pré-requisitos:</span><span class="sxs-lookup"><span data-stu-id="081ae-105">Prerequisites:</span></span>

- <span data-ttu-id="081ae-106">Uma conta do Windows Azure.</span><span class="sxs-lookup"><span data-stu-id="081ae-106">A Windows Azure account.</span></span>
- <span data-ttu-id="081ae-107">O [Windows Azure SDK](https://go.microsoft.com/fwlink/?linkid=254364&amp;clcid=0x409).</span><span class="sxs-lookup"><span data-stu-id="081ae-107">The [Windows Azure SDK](https://go.microsoft.com/fwlink/?linkid=254364&amp;clcid=0x409).</span></span>
- <span data-ttu-id="081ae-108">Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="081ae-108">Visual Studio 2012.</span></span>

<span data-ttu-id="081ae-109">O backplane de barramento de serviço também é compatível com [do barramento de serviço para o Windows Server](https://msdn.microsoft.com/library/windowsazure/dn282144.aspx), versão 1.1.</span><span class="sxs-lookup"><span data-stu-id="081ae-109">The service bus backplane is also compatible with [Service Bus for Windows Server](https://msdn.microsoft.com/library/windowsazure/dn282144.aspx), version 1.1.</span></span> <span data-ttu-id="081ae-110">No entanto, não é compatível com a versão 1.0 do barramento de serviço para o Windows Server.</span><span class="sxs-lookup"><span data-stu-id="081ae-110">However, it is not compatible with version 1.0 of Service Bus for Windows Server.</span></span>

## <a name="pricing"></a><span data-ttu-id="081ae-111">Preços</span><span class="sxs-lookup"><span data-stu-id="081ae-111">Pricing</span></span>

<span data-ttu-id="081ae-112">Backplane do barramento de serviço usa tópicos para enviar mensagens.</span><span class="sxs-lookup"><span data-stu-id="081ae-112">The Service Bus backplane uses topics to send messages.</span></span> <span data-ttu-id="081ae-113">Para as informações de preços mais recentes, consulte [do barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/).</span><span class="sxs-lookup"><span data-stu-id="081ae-113">For the latest pricing information, see [Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).</span></span> <span data-ttu-id="081ae-114">No momento da redação deste artigo, você pode enviar 1.000.000 mensagens por mês para menos de US $1.</span><span class="sxs-lookup"><span data-stu-id="081ae-114">At the time of this writing, you can send 1,000,000 messages per month for less than $1.</span></span> <span data-ttu-id="081ae-115">Backplane envia uma mensagem do barramento de serviço para cada invocação de um método de hub do SignalR.</span><span class="sxs-lookup"><span data-stu-id="081ae-115">The backplane sends a service bus message for each invocation of a SignalR hub method.</span></span> <span data-ttu-id="081ae-116">Também há algumas mensagens de controle para conexões, desconexões, junção ou deixando grupos e assim por diante.</span><span class="sxs-lookup"><span data-stu-id="081ae-116">There are also some control messages for connections, disconnections, joining or leaving groups, and so forth.</span></span> <span data-ttu-id="081ae-117">Na maioria dos aplicativos, a maioria do tráfego de mensagem será invocações de método de hub.</span><span class="sxs-lookup"><span data-stu-id="081ae-117">In most applications, the majority of the message traffic will be hub method invocations.</span></span>

## <a name="overview"></a><span data-ttu-id="081ae-118">Visão geral</span><span class="sxs-lookup"><span data-stu-id="081ae-118">Overview</span></span>

<span data-ttu-id="081ae-119">Antes de passarmos para o tutorial detalhado, aqui está uma visão rápida do que você deve fazer.</span><span class="sxs-lookup"><span data-stu-id="081ae-119">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="081ae-120">Use o portal do Windows Azure para criar um novo namespace do barramento de serviço.</span><span class="sxs-lookup"><span data-stu-id="081ae-120">Use the Windows Azure portal to create a new Service Bus namespace.</span></span>
2. <span data-ttu-id="081ae-121">Adicione esses pacotes do NuGet ao seu aplicativo:</span><span class="sxs-lookup"><span data-stu-id="081ae-121">Add these NuGet packages to your application:</span></span> 

    - [<span data-ttu-id="081ae-122">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="081ae-122">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="081ae-123">Microsoft.AspNet.SignalR.ServiceBus</span><span class="sxs-lookup"><span data-stu-id="081ae-123">Microsoft.AspNet.SignalR.ServiceBus</span></span>](http://www.nuget.org/packages/SignalR.WindowsAzureServiceBus)
3. <span data-ttu-id="081ae-124">Crie um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="081ae-124">Create a SignalR application.</span></span>
4. <span data-ttu-id="081ae-125">Adicione o seguinte código ao global. asax para configurar o backplane:</span><span class="sxs-lookup"><span data-stu-id="081ae-125">Add the following code to Global.asax to configure the backplane:</span></span> 

    [!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample1.cs)]

<span data-ttu-id="081ae-126">Para cada aplicativo, escolha um valor diferente para "Nomedoseuaplicativo".</span><span class="sxs-lookup"><span data-stu-id="081ae-126">For each application, pick a different value for "YourAppName".</span></span> <span data-ttu-id="081ae-127">Não use o mesmo valor em vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="081ae-127">Do not use the same value across multiple applications.</span></span>

## <a name="create-the-azure-services"></a><span data-ttu-id="081ae-128">Criar os serviços do Azure</span><span class="sxs-lookup"><span data-stu-id="081ae-128">Create the Azure Services</span></span>

<span data-ttu-id="081ae-129">Criar um serviço de nuvem, conforme descrito em [como criar e implantar um serviço de nuvem](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy).</span><span class="sxs-lookup"><span data-stu-id="081ae-129">Create a Cloud Service, as described in [How to Create and Deploy a Cloud Service](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy).</span></span> <span data-ttu-id="081ae-130">Siga as etapas na seção "como: Crie um serviço de nuvem usando a criação rápida".</span><span class="sxs-lookup"><span data-stu-id="081ae-130">Follow the steps in the section "How to: Create a cloud service using Quick Create".</span></span> <span data-ttu-id="081ae-131">Para este tutorial, você não precisa carregar um certificado.</span><span class="sxs-lookup"><span data-stu-id="081ae-131">For this tutorial, you do not need to upload a certificate.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image2.png)

<span data-ttu-id="081ae-132">Crie um novo namespace do barramento de serviço, conforme descrito em [como o barramento de serviço para usar tópicos/assinaturas](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions).</span><span class="sxs-lookup"><span data-stu-id="081ae-132">Create a new Service Bus namespace, as described in [How to Use Service Bus Topics/Subscriptions](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions).</span></span> <span data-ttu-id="081ae-133">Siga as etapas na seção "Criar um Namespace de serviço".</span><span class="sxs-lookup"><span data-stu-id="081ae-133">Follow the steps in the section "Create a Service Namespace".</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image3.png)

> [!NOTE]
> <span data-ttu-id="081ae-134">Certifique-se de selecionar a mesma região para o serviço de nuvem e o namespace do barramento de serviço.</span><span class="sxs-lookup"><span data-stu-id="081ae-134">Make sure to select the same region for the cloud service and the Service Bus namespace.</span></span>


## <a name="create-the-visual-studio-project"></a><span data-ttu-id="081ae-135">Criar o projeto do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="081ae-135">Create the Visual Studio Project</span></span>

<span data-ttu-id="081ae-136">Inicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="081ae-136">Start Visual Studio.</span></span> <span data-ttu-id="081ae-137">Dos **arquivo** menu, clique em **novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="081ae-137">From the **File** menu, click **New Project**.</span></span>

<span data-ttu-id="081ae-138">No **novo projeto** diálogo caixa, expanda **Visual c#**.</span><span class="sxs-lookup"><span data-stu-id="081ae-138">In the **New Project** dialog box, expand **Visual C#**.</span></span> <span data-ttu-id="081ae-139">Sob **modelos instalados**, selecione **nuvem** e, em seguida, selecione **serviço de nuvem do Windows Azure**.</span><span class="sxs-lookup"><span data-stu-id="081ae-139">Under **Installed Templates**, select **Cloud** and then select **Windows Azure Cloud Service**.</span></span> <span data-ttu-id="081ae-140">Mantenha o padrão do .NET Framework 4.5.</span><span class="sxs-lookup"><span data-stu-id="081ae-140">Keep the default .NET Framework 4.5.</span></span> <span data-ttu-id="081ae-141">Nome do aplicativo ChatService e clique em **Okey**.</span><span class="sxs-lookup"><span data-stu-id="081ae-141">Name the application ChatService and click **OK**.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image4.png)

<span data-ttu-id="081ae-142">No **novo serviço de nuvem do Windows Azure** caixa de diálogo Selecionar função de Web do ASP.NET MVC 4.</span><span class="sxs-lookup"><span data-stu-id="081ae-142">In the **New Windows Azure Cloud Service** dialog, select ASP.NET MVC 4 Web Role.</span></span> <span data-ttu-id="081ae-143">Clique no botão de seta para a direita (**&gt;**) para adicionar a função à sua solução.</span><span class="sxs-lookup"><span data-stu-id="081ae-143">Click the right-arrow button (**&gt;**) to add the role to your solution.</span></span>

<span data-ttu-id="081ae-144">Passe o mouse sobre a nova função, portanto, visíveis no ícone de lápis.</span><span class="sxs-lookup"><span data-stu-id="081ae-144">Hover the mouse over the new role, so the pencil icon visible.</span></span> <span data-ttu-id="081ae-145">Clique nesse ícone para renomear a função.</span><span class="sxs-lookup"><span data-stu-id="081ae-145">Click this icon to rename the role.</span></span> <span data-ttu-id="081ae-146">Nome da função "SignalRChat" e clique em **Okey**.</span><span class="sxs-lookup"><span data-stu-id="081ae-146">Name the role "SignalRChat" and click **OK**.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image5.png)

<span data-ttu-id="081ae-147">No **novo projeto ASP.NET MVC 4** assistente, selecione **aplicativo de Internet**.</span><span class="sxs-lookup"><span data-stu-id="081ae-147">In the **New ASP.NET MVC 4 Project** wizard, select **Internet Application**.</span></span> <span data-ttu-id="081ae-148">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="081ae-148">Click **OK**.</span></span> <span data-ttu-id="081ae-149">O Assistente de projeto cria dois projetos:</span><span class="sxs-lookup"><span data-stu-id="081ae-149">The project wizard creates two projects:</span></span>

- <span data-ttu-id="081ae-150">ChatService: Este projeto é o aplicativo do Windows Azure.</span><span class="sxs-lookup"><span data-stu-id="081ae-150">ChatService: This project is the Windows Azure application.</span></span> <span data-ttu-id="081ae-151">Ele define as funções do Azure e outras opções de configuração.</span><span class="sxs-lookup"><span data-stu-id="081ae-151">It defines the Azure roles and other configuration options.</span></span>
- <span data-ttu-id="081ae-152">SignalRChat: Esse projeto é o seu projeto ASP.NET MVC 4.</span><span class="sxs-lookup"><span data-stu-id="081ae-152">SignalRChat: This project is your ASP.NET MVC 4 project.</span></span>

## <a name="create-the-signalr-chat-application"></a><span data-ttu-id="081ae-153">Criar o aplicativo de Chat SignalR</span><span class="sxs-lookup"><span data-stu-id="081ae-153">Create the SignalR Chat Application</span></span>

<span data-ttu-id="081ae-154">Para criar o aplicativo de bate-papo, siga as etapas no tutorial [Introdução ao SignalR e MVC 4](tutorial-getting-started-with-signalr-and-mvc-4.md).</span><span class="sxs-lookup"><span data-stu-id="081ae-154">To create the chat application, follow the steps in the tutorial [Getting Started with SignalR and MVC 4](tutorial-getting-started-with-signalr-and-mvc-4.md).</span></span>

<span data-ttu-id="081ae-155">Use o NuGet para instalar as bibliotecas necessárias.</span><span class="sxs-lookup"><span data-stu-id="081ae-155">Use NuGet to install the required libraries.</span></span> <span data-ttu-id="081ae-156">Dos **ferramentas** menu, selecione **Gerenciador de pacotes NuGet**, em seguida, selecione **Package Manager Console**.</span><span class="sxs-lookup"><span data-stu-id="081ae-156">From the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="081ae-157">No **Package Manager Console** janela, digite os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="081ae-157">In the **Package Manager Console** window, enter the following commands:</span></span>

[!code-powershell[Main](scaleout-with-windows-azure-service-bus/samples/sample2.ps1)]

<span data-ttu-id="081ae-158">Use o `-ProjectName` opção para instalar os pacotes para o projeto ASP.NET MVC, em vez de projeto do Windows Azure.</span><span class="sxs-lookup"><span data-stu-id="081ae-158">Use the `-ProjectName` option to install the packages to the ASP.NET MVC project, rather than the Windows Azure project.</span></span>

## <a name="configure-the-backplane"></a><span data-ttu-id="081ae-159">Configurar o Backplane</span><span class="sxs-lookup"><span data-stu-id="081ae-159">Configure the Backplane</span></span>

<span data-ttu-id="081ae-160">No arquivo global asax do aplicativo, adicione o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="081ae-160">In your application's Global.asax file, add the following code:</span></span>

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample3.cs)]

<span data-ttu-id="081ae-161">Agora, você precisará obter sua cadeia de conexão do barramento de serviço.</span><span class="sxs-lookup"><span data-stu-id="081ae-161">Now you need to get your service bus connection string.</span></span> <span data-ttu-id="081ae-162">No portal do Azure, selecione o namespace do barramento de serviço que você criou e clique no ícone de chave de acesso.</span><span class="sxs-lookup"><span data-stu-id="081ae-162">In the Azure portal, select the service bus namespace that you created and click the Access Key icon.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image6.png)

<span data-ttu-id="081ae-163">Copie a cadeia de conexão para a área de transferência e, em seguida, cole-o na *connectionString* variável.</span><span class="sxs-lookup"><span data-stu-id="081ae-163">Copy the connection string to the clipboard, then paste it into the *connectionString* variable.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image7.png)

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample4.cs)]

## <a name="deploy-to-azure"></a><span data-ttu-id="081ae-164">Implantar no Azure</span><span class="sxs-lookup"><span data-stu-id="081ae-164">Deploy to Azure</span></span>

<span data-ttu-id="081ae-165">No Gerenciador de soluções, expanda o **funções** pasta dentro do projeto ChatService.</span><span class="sxs-lookup"><span data-stu-id="081ae-165">In Solution Explorer, expand the **Roles** folder inside the ChatService project.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image8.png)

<span data-ttu-id="081ae-166">A função SignalRChat com o botão direito e selecione **propriedades**.</span><span class="sxs-lookup"><span data-stu-id="081ae-166">Right-click the SignalRChat role and select **Properties**.</span></span> <span data-ttu-id="081ae-167">Selecione o **configuração** guia. Sob **instâncias** selecione 2.</span><span class="sxs-lookup"><span data-stu-id="081ae-167">Select the **Configuration** tab. Under **Instances** select 2.</span></span> <span data-ttu-id="081ae-168">Você também pode definir o tamanho da VM **Extra pequena**.</span><span class="sxs-lookup"><span data-stu-id="081ae-168">You can also set the VM size to **Extra Small**.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image9.png)

<span data-ttu-id="081ae-169">Salve as alterações.</span><span class="sxs-lookup"><span data-stu-id="081ae-169">Save the changes.</span></span>

<span data-ttu-id="081ae-170">No Gerenciador de soluções, clique com botão direito no projeto ChatService.</span><span class="sxs-lookup"><span data-stu-id="081ae-170">In Solution Explorer, right-click the ChatService project.</span></span> <span data-ttu-id="081ae-171">Selecione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="081ae-171">Select **Publish**.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image10.png)

<span data-ttu-id="081ae-172">Se esta for sua primeira tempo publicando no Windows Azure, você deve baixar suas credenciais.</span><span class="sxs-lookup"><span data-stu-id="081ae-172">If this is your first time publishing to Windows Azure, you must download your credentials.</span></span> <span data-ttu-id="081ae-173">No **publicar** assistente, clique em "Entrar baixar credenciais".</span><span class="sxs-lookup"><span data-stu-id="081ae-173">In the **Publish** wizard, click "Sign in to download credentials".</span></span> <span data-ttu-id="081ae-174">Isso solicitará que você entrar no portal do Windows Azure e baixar um arquivo de configurações de publicação.</span><span class="sxs-lookup"><span data-stu-id="081ae-174">This will prompt you to sign into the Windows Azure portal and download a publish settings file.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image11.png)

<span data-ttu-id="081ae-175">Clique em **importação** e selecione o arquivo de configurações de publicação que você baixou.</span><span class="sxs-lookup"><span data-stu-id="081ae-175">Click **Import** and select the publish settings file that you downloaded.</span></span>

<span data-ttu-id="081ae-176">Clique em **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="081ae-176">Click **Next**.</span></span> <span data-ttu-id="081ae-177">No **configurações de publicação** caixa de diálogo, em **serviço de nuvem**, selecione o serviço de nuvem que você criou anteriormente.</span><span class="sxs-lookup"><span data-stu-id="081ae-177">In the **Publish Settings** dialog, under **Cloud Service**, select the cloud service that you created earlier.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image12.png)

<span data-ttu-id="081ae-178">Clique em **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="081ae-178">Click **Publish**.</span></span> <span data-ttu-id="081ae-179">Ele pode levar alguns minutos para implantar o aplicativo e iniciar as máquinas virtuais.</span><span class="sxs-lookup"><span data-stu-id="081ae-179">It can take a few minutes to deploy the application and start the VMs.</span></span>

<span data-ttu-id="081ae-180">Agora quando você executa o aplicativo de bate-papo, as instâncias de função se comunicam por meio do barramento de serviço do Azure, usando um tópico do barramento de serviço.</span><span class="sxs-lookup"><span data-stu-id="081ae-180">Now when you run the chat application, the role instances communicate through Azure Service Bus, using a Service Bus topic.</span></span> <span data-ttu-id="081ae-181">Um tópico é uma fila de mensagens que permite que vários assinantes.</span><span class="sxs-lookup"><span data-stu-id="081ae-181">A topic is a message queue that allows multiple subscribers.</span></span>

<span data-ttu-id="081ae-182">Backplane cria automaticamente o tópico e as assinaturas.</span><span class="sxs-lookup"><span data-stu-id="081ae-182">The backplane automatically creates the topic and the subscriptions.</span></span> <span data-ttu-id="081ae-183">Para ver as assinaturas e a atividade de mensagens, abra o portal do Azure, selecione o namespace do barramento de serviço e clique em "Tópicos".</span><span class="sxs-lookup"><span data-stu-id="081ae-183">To see the subscriptions and message activity, open the Azure portal, select the Service Bus namespace, and click on "Topics".</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image13.png)

<span data-ttu-id="081ae-184">Poderá levar alguns minutos para que a atividade de mensagem sejam exibidos no painel.</span><span class="sxs-lookup"><span data-stu-id="081ae-184">It make take a few minutes for the message activity to show up in the dashboard.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image14.png)

<span data-ttu-id="081ae-185">O SignalR gerencia o tempo de vida do tópico.</span><span class="sxs-lookup"><span data-stu-id="081ae-185">SignalR manages the topic lifetime.</span></span> <span data-ttu-id="081ae-186">Desde que o aplicativo for implantado, não tente excluir tópicos ou alterar as configurações de tópico manualmente.</span><span class="sxs-lookup"><span data-stu-id="081ae-186">As long as your application is deployed, don't try to manually delete topics or change settings on the topic.</span></span>
