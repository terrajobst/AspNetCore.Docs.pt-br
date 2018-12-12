---
uid: signalr/overview/older-versions/scaleout-with-redis
title: Expansão do SignalR com Redis (SignalR 1.x) | Microsoft Docs
author: MikeWasson
description: ''
ms.author: riande
ms.date: 05/01/2013
ms.assetid: 6abecf80-8ffa-41ba-b0d9-1d9edbe7687b
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-redis
msc.type: authoredcontent
ms.openlocfilehash: 3f70536a4bda90a0298f6bb4f7ef47b8a257175b
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287540"
---
<a name="signalr-scaleout-with-redis-signalr-1x"></a><span data-ttu-id="b3965-102">Expansão do SignalR com Redis (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="b3965-102">SignalR Scaleout with Redis (SignalR 1.x)</span></span>
====================
<span data-ttu-id="b3965-103">por [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="b3965-103">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="b3965-104">Neste tutorial, você usará [Redis](http://redis.io/) para distribuir mensagens através de um aplicativo de SignalR é implantado em duas instâncias separadas do IIS.</span><span class="sxs-lookup"><span data-stu-id="b3965-104">In this tutorial, you will use [Redis](http://redis.io/) to distribute messages across a SignalR application that is deployed on two separate IIS instances.</span></span>

<span data-ttu-id="b3965-105">O redis é um repositório de chave-valor na memória.</span><span class="sxs-lookup"><span data-stu-id="b3965-105">Redis is an in-memory key-value store.</span></span> <span data-ttu-id="b3965-106">Ele também dá suporte a um sistema de mensagens com um modelo de publicação/assinatura.</span><span class="sxs-lookup"><span data-stu-id="b3965-106">It also supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="b3965-107">O backplane SignalR Redis usa o recurso de publicação/assinatura para encaminhar mensagens para outros servidores.</span><span class="sxs-lookup"><span data-stu-id="b3965-107">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span>

![](scaleout-with-redis/_static/image1.png)

<span data-ttu-id="b3965-108">Para este tutorial, você usará os três servidores:</span><span class="sxs-lookup"><span data-stu-id="b3965-108">For this tutorial, you will use three servers:</span></span>

- <span data-ttu-id="b3965-109">Dois servidores que executam o Windows, o que você usará para implantar um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="b3965-109">Two servers running Windows, which you will use to deploy a SignalR application.</span></span>
- <span data-ttu-id="b3965-110">Um servidor que executa o Linux, o que você usará para executar o Redis.</span><span class="sxs-lookup"><span data-stu-id="b3965-110">One server running Linux, which you will use to run Redis.</span></span> <span data-ttu-id="b3965-111">Para as capturas de tela neste tutorial, usei o Ubuntu 12.04 TLS.</span><span class="sxs-lookup"><span data-stu-id="b3965-111">For the screenshots in this tutorial, I used Ubuntu 12.04 TLS.</span></span>

<span data-ttu-id="b3965-112">Se você não tiver três servidores físicos para usar, você pode criar máquinas virtuais no Hyper-V.</span><span class="sxs-lookup"><span data-stu-id="b3965-112">If you don't have three physical servers to use, you can create VMs on Hyper-V.</span></span> <span data-ttu-id="b3965-113">Outra opção é criar VMs no Azure.</span><span class="sxs-lookup"><span data-stu-id="b3965-113">Another option is to create VMs on Azure.</span></span>

<span data-ttu-id="b3965-114">Embora este tutorial usa a implementação do Redis oficial, há também uma [porta do Windows do Redis](https://github.com/MSOpenTech/redis) do MSOpenTech.</span><span class="sxs-lookup"><span data-stu-id="b3965-114">Although this tutorial uses the official Redis implementation, there is also a [Windows port of Redis](https://github.com/MSOpenTech/redis) from MSOpenTech.</span></span> <span data-ttu-id="b3965-115">Instalação e configuração são diferentes, mas caso contrário, as etapas são as mesmas.</span><span class="sxs-lookup"><span data-stu-id="b3965-115">Setup and configuration are different, but otherwise the steps are the same.</span></span>

> [!NOTE] 
> 
> <span data-ttu-id="b3965-116">Expansão do SignalR com Redis não oferece suporte a clusters do Redis.</span><span class="sxs-lookup"><span data-stu-id="b3965-116">SignalR scaleout with Redis does not support Redis clusters.</span></span>


## <a name="overview"></a><span data-ttu-id="b3965-117">Visão geral</span><span class="sxs-lookup"><span data-stu-id="b3965-117">Overview</span></span>

<span data-ttu-id="b3965-118">Antes de passarmos para o tutorial detalhado, aqui está uma visão rápida do que você deve fazer.</span><span class="sxs-lookup"><span data-stu-id="b3965-118">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="b3965-119">Instale o Redis e inicie o servidor Redis.</span><span class="sxs-lookup"><span data-stu-id="b3965-119">Install Redis and start the Redis server.</span></span>
2. <span data-ttu-id="b3965-120">Adicione esses pacotes do NuGet ao seu aplicativo:</span><span class="sxs-lookup"><span data-stu-id="b3965-120">Add these NuGet packages to your application:</span></span> 

    - [<span data-ttu-id="b3965-121">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="b3965-121">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="b3965-122">Microsoft.AspNet.SignalR.Redis</span><span class="sxs-lookup"><span data-stu-id="b3965-122">Microsoft.AspNet.SignalR.Redis</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR.Redis)
3. <span data-ttu-id="b3965-123">Crie um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="b3965-123">Create a SignalR application.</span></span>
4. <span data-ttu-id="b3965-124">Adicione o seguinte código ao global. asax para configurar o backplane:</span><span class="sxs-lookup"><span data-stu-id="b3965-124">Add the following code to Global.asax to configure the backplane:</span></span> 

    [!code-csharp[Main](scaleout-with-redis/samples/sample1.cs)]

## <a name="ubuntu-on-hyper-v"></a><span data-ttu-id="b3965-125">Ubuntu no Hyper-V</span><span class="sxs-lookup"><span data-stu-id="b3965-125">Ubuntu on Hyper-V</span></span>

<span data-ttu-id="b3965-126">Usando o Windows Hyper-V, você pode criar facilmente uma VM do Ubuntu no Windows Server.</span><span class="sxs-lookup"><span data-stu-id="b3965-126">Using Windows Hyper-V, you can easily create an Ubuntu VM on Windows Server.</span></span>

<span data-ttu-id="b3965-127">Baixe o ISO do Ubuntu da [ http://www.ubuntu.com ](http://www.ubuntu.com/).</span><span class="sxs-lookup"><span data-stu-id="b3965-127">Download the Ubuntu ISO from [http://www.ubuntu.com](http://www.ubuntu.com/).</span></span>

<span data-ttu-id="b3965-128">No Hyper-V, adicione uma nova VM.</span><span class="sxs-lookup"><span data-stu-id="b3965-128">In Hyper-V, add a new VM.</span></span> <span data-ttu-id="b3965-129">No **conectar disco rígido Virtual** etapa, selecione **criar um disco rígido virtual**.</span><span class="sxs-lookup"><span data-stu-id="b3965-129">In the **Connect Virtual Hard Disk** step, select **Create a virtual hard disk**.</span></span>

![](scaleout-with-redis/_static/image2.png)

<span data-ttu-id="b3965-130">No **opções de instalação** etapa, selecione **arquivo de imagem (. ISO)**, clique em **procurar**e navegue até o ISO de instalação do Ubuntu.</span><span class="sxs-lookup"><span data-stu-id="b3965-130">In the **Installation Options** step, select **Image file (.iso)**, click **Browse**, and browse to the Ubuntu installation ISO.</span></span>

![](scaleout-with-redis/_static/image3.png)

## <a name="install-redis"></a><span data-ttu-id="b3965-131">Instale o Redis</span><span class="sxs-lookup"><span data-stu-id="b3965-131">Install Redis</span></span>

<span data-ttu-id="b3965-132">Siga as etapas em [ http://redis.io/download ](http://redis.io/download) para baixar e compilar o Redis.</span><span class="sxs-lookup"><span data-stu-id="b3965-132">Follow the steps at [http://redis.io/download](http://redis.io/download) to download and build Redis.</span></span>

[!code-console[Main](scaleout-with-redis/samples/sample2.cmd)]

<span data-ttu-id="b3965-133">Isso compila os binários do Redis `src` directory.</span><span class="sxs-lookup"><span data-stu-id="b3965-133">This builds the Redis binaries in the `src` directory.</span></span>

<span data-ttu-id="b3965-134">Por padrão, o Redis não requer uma senha.</span><span class="sxs-lookup"><span data-stu-id="b3965-134">By default, Redis does not require a password.</span></span> <span data-ttu-id="b3965-135">Para definir uma senha, edite o `redis.conf` arquivo, que está localizado no diretório raiz do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="b3965-135">To set a password, edit the `redis.conf` file, which is located in the root directory of the source code.</span></span> <span data-ttu-id="b3965-136">(Faça uma cópia de backup do arquivo antes de editá-lo!) Adicione a seguinte diretiva para `redis.conf`:</span><span class="sxs-lookup"><span data-stu-id="b3965-136">(Make a backup copy of the file before you edit it!) Add the following directive to `redis.conf`:</span></span>

[!code-powershell[Main](scaleout-with-redis/samples/sample3.ps1)]

<span data-ttu-id="b3965-137">Agora, inicie o servidor Redis:</span><span class="sxs-lookup"><span data-stu-id="b3965-137">Now start the Redis server:</span></span>

[!code-css[Main](scaleout-with-redis/samples/sample4.css)]

![](scaleout-with-redis/_static/image4.png)

<span data-ttu-id="b3965-138">Abrir porta 6379, que é a porta padrão que Redis escuta.</span><span class="sxs-lookup"><span data-stu-id="b3965-138">Open port 6379, which is the default port that Redis listens on.</span></span> <span data-ttu-id="b3965-139">(Você pode alterar o número da porta no arquivo de configuração).</span><span class="sxs-lookup"><span data-stu-id="b3965-139">(You can change the port number in the configuration file.)</span></span>

## <a name="create-the-signalr-application"></a><span data-ttu-id="b3965-140">Criar o aplicativo de SignalR</span><span class="sxs-lookup"><span data-stu-id="b3965-140">Create the SignalR Application</span></span>

<span data-ttu-id="b3965-141">Crie um aplicativo do SignalR, seguindo um destes tutoriais:</span><span class="sxs-lookup"><span data-stu-id="b3965-141">Create a SignalR application by following either of these tutorials:</span></span>

- [<span data-ttu-id="b3965-142">Introdução ao SignalR</span><span class="sxs-lookup"><span data-stu-id="b3965-142">Getting Started with SignalR</span></span>](../getting-started/tutorial-getting-started-with-signalr.md)
- [<span data-ttu-id="b3965-143">Introdução ao SignalR e MVC 4</span><span class="sxs-lookup"><span data-stu-id="b3965-143">Getting Started with SignalR and MVC 4</span></span>](tutorial-getting-started-with-signalr-and-mvc-4.md)

<span data-ttu-id="b3965-144">Em seguida, modificaremos o aplicativo de bate-papo para dar suporte à expansão com Redis.</span><span class="sxs-lookup"><span data-stu-id="b3965-144">Next, we'll modify the chat application to support scaleout with Redis.</span></span> <span data-ttu-id="b3965-145">Primeiro, adicione o pacote do SignalR.Redis NuGet ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="b3965-145">First, add the SignalR.Redis NuGet package to your project.</span></span> <span data-ttu-id="b3965-146">No Visual Studio, do **ferramentas** menu, selecione **Gerenciador de pacotes NuGet**, em seguida, selecione **Package Manager Console**.</span><span class="sxs-lookup"><span data-stu-id="b3965-146">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="b3965-147">Na janela do Console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b3965-147">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](scaleout-with-redis/samples/sample5.ps1)]

<span data-ttu-id="b3965-148">Em seguida, abra o arquivo global asax.</span><span class="sxs-lookup"><span data-stu-id="b3965-148">Next, open the Global.asax file.</span></span> <span data-ttu-id="b3965-149">Adicione o seguinte código para o **Application\_iniciar** método:</span><span class="sxs-lookup"><span data-stu-id="b3965-149">Add the following code to the **Application\_Start** method:</span></span>

[!code-csharp[Main](scaleout-with-redis/samples/sample6.cs)]

- <span data-ttu-id="b3965-150">"servidor" é o nome do servidor que está executando o Redis.</span><span class="sxs-lookup"><span data-stu-id="b3965-150">"server" is the name of the server that is running Redis.</span></span>
- <span data-ttu-id="b3965-151">*porta* é o número da porta</span><span class="sxs-lookup"><span data-stu-id="b3965-151">*port* is the port number</span></span>
- <span data-ttu-id="b3965-152">"password" é a senha que você definiu no arquivo conf.</span><span class="sxs-lookup"><span data-stu-id="b3965-152">"password" is the password that you defined in the redis.conf file.</span></span>
- <span data-ttu-id="b3965-153">"AppName" é qualquer cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="b3965-153">"AppName" is any string.</span></span> <span data-ttu-id="b3965-154">O SignalR cria um canal de pub/sub Redis com esse nome.</span><span class="sxs-lookup"><span data-stu-id="b3965-154">SignalR creates a Redis pub/sub channel with this name.</span></span>

<span data-ttu-id="b3965-155">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b3965-155">For example:</span></span>

[!code-csharp[Main](scaleout-with-redis/samples/sample7.cs)]

## <a name="deploy-and-run-the-application"></a><span data-ttu-id="b3965-156">Implantar e executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="b3965-156">Deploy and Run the Application</span></span>

<span data-ttu-id="b3965-157">Prepare suas instâncias do Windows Server para implantar o aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="b3965-157">Prepare your Windows Server instances to deploy the SignalR application.</span></span>

<span data-ttu-id="b3965-158">Adicione a função do IIS.</span><span class="sxs-lookup"><span data-stu-id="b3965-158">Add the IIS role.</span></span> <span data-ttu-id="b3965-159">Inclua recursos de "Desenvolvimento de aplicativos", incluindo o protocolo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b3965-159">Include "Application Development" features, including the WebSocket Protocol.</span></span>

![](scaleout-with-redis/_static/image5.png)

<span data-ttu-id="b3965-160">Também incluem o serviço de gerenciamento (listados em "Ferramentas de gerenciamento").</span><span class="sxs-lookup"><span data-stu-id="b3965-160">Also include the Management Service (listed under "Management Tools").</span></span>

![](scaleout-with-redis/_static/image6.png)

<span data-ttu-id="b3965-161">**Install Web implantação 3.0.**</span><span class="sxs-lookup"><span data-stu-id="b3965-161">**Install Web Deploy 3.0.**</span></span> <span data-ttu-id="b3965-162">Quando você executar o Gerenciador do IIS, ele solicitará que você instale o Microsoft Web Platform, ou pode [baixar o intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span><span class="sxs-lookup"><span data-stu-id="b3965-162">When you run IIS Manager, it will prompt you to install Microsoft Web Platform, or you can [download the intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span></span> <span data-ttu-id="b3965-163">No instalador da plataforma, procure implantação da Web e instale o Web Deploy 3.0</span><span class="sxs-lookup"><span data-stu-id="b3965-163">In the Platform Installer, search for Web Deploy and install Web Deploy 3.0</span></span>

![](scaleout-with-redis/_static/image7.png)

<span data-ttu-id="b3965-164">Verifique se o serviço de gerenciamento da Web está em execução.</span><span class="sxs-lookup"><span data-stu-id="b3965-164">Check that the Web Management Service is running.</span></span> <span data-ttu-id="b3965-165">Caso contrário, inicie o serviço.</span><span class="sxs-lookup"><span data-stu-id="b3965-165">If not, start the service.</span></span> <span data-ttu-id="b3965-166">(Se você não vir o serviço de gerenciamento da Web na lista de serviços do Windows, certifique-se de que você instalou o serviço de gerenciamento quando você adicionou a função do IIS.)</span><span class="sxs-lookup"><span data-stu-id="b3965-166">(If you don't see Web Management Service in the list of Windows services, make sure that you installed the Management Service when you added the IIS role.)</span></span>

<span data-ttu-id="b3965-167">Por padrão, o serviço de gerenciamento da Web escuta na porta TCP 8172.</span><span class="sxs-lookup"><span data-stu-id="b3965-167">By default, the Web Management Service listens on TCP port 8172.</span></span> <span data-ttu-id="b3965-168">No Firewall do Windows, crie uma nova regra de entrada para permitir o tráfego TCP na porta 8172.</span><span class="sxs-lookup"><span data-stu-id="b3965-168">In Windows Firewall, create a new inbound rule to allow TCP traffic on port 8172.</span></span> <span data-ttu-id="b3965-169">Para obter mais informações, consulte [Configurando regras de Firewall](https://technet.microsoft.com/library/dd448559(WS.10).aspx).</span><span class="sxs-lookup"><span data-stu-id="b3965-169">For more information, see [Configuring Firewall Rules](https://technet.microsoft.com/library/dd448559(WS.10).aspx).</span></span> <span data-ttu-id="b3965-170">(Se você estiver hospedando as VMs no Azure, você pode fazer isso diretamente no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="b3965-170">(If you are hosting the VMs on Azure, you can do this directly in the Azure portal.</span></span> <span data-ttu-id="b3965-171">Ver [como configurar pontos de extremidade para uma máquina Virtual](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).)</span><span class="sxs-lookup"><span data-stu-id="b3965-171">See [How to Set Up Endpoints to a Virtual Machine](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).)</span></span>

<span data-ttu-id="b3965-172">Agora você está pronto para implantar o projeto do Visual Studio em seu computador de desenvolvimento para o servidor.</span><span class="sxs-lookup"><span data-stu-id="b3965-172">Now you are ready to deploy the Visual Studio project from your development machine to the server.</span></span> <span data-ttu-id="b3965-173">No Gerenciador de soluções, clique com botão direito a solução e clique em **publicar**.</span><span class="sxs-lookup"><span data-stu-id="b3965-173">In Solution Explorer, right-click the solution and click **Publish**.</span></span>

<span data-ttu-id="b3965-174">Para obter mais documentação sobre a implantação da web, consulte [mapa de conteúdo de implantação da Web para Visual Studio e o ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="b3965-174">For more detailed documentation about web deployment, see [Web Deployment Content Map for Visual Studio and ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span></span>

<span data-ttu-id="b3965-175">Se você implantar o aplicativo em dois servidores, você pode abrir cada instância em uma janela separada do navegador e ver que cada um receba mensagens do SignalR de outra.</span><span class="sxs-lookup"><span data-stu-id="b3965-175">If you deploy the application to two servers, you can open each instance in a separate browser window and see that they each receive SignalR messages from the other.</span></span> <span data-ttu-id="b3965-176">(É claro que, em um ambiente de produção, os dois servidores estaria atrás de um balanceador de carga.)</span><span class="sxs-lookup"><span data-stu-id="b3965-176">(Of course, in a production environment, the two servers would sit behind a load balancer.)</span></span>

![](scaleout-with-redis/_static/image8.png)

<span data-ttu-id="b3965-177">Se você estiver curioso para ver as mensagens que são enviadas para o Redis, você pode usar o **redis-cli** cliente, que é instalado com o Redis.</span><span class="sxs-lookup"><span data-stu-id="b3965-177">If you're curious to see the messages that are sent to Redis, you can use the **redis-cli** client, which installs with Redis.</span></span>

[!code-xml[Main](scaleout-with-redis/samples/sample8.xml)]

![](scaleout-with-redis/_static/image9.png)
