---
uid: signalr/overview/older-versions/scaleout-with-sql-server
title: Expansão do SignalR com o SQL Server (SignalR 1.x) | Microsoft Docs
author: MikeWasson
description: ''
ms.author: riande
ms.date: 05/01/2013
ms.assetid: 1dca7967-8296-444a-9533-837eb284e78c
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-sql-server
msc.type: authoredcontent
ms.openlocfilehash: 682aa837ed991cbf5d78dcb304e2c1bce905c52c
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287515"
---
<a name="signalr-scaleout-with-sql-server-signalr-1x"></a><span data-ttu-id="46c71-102">Expansão do SignalR com o SQL Server (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="46c71-102">SignalR Scaleout with SQL Server (SignalR 1.x)</span></span>
====================
<span data-ttu-id="46c71-103">por [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="46c71-103">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="46c71-104">Neste tutorial, você usará o SQL Server para distribuir mensagens através de um aplicativo de SignalR é implantado em duas instâncias separadas do IIS.</span><span class="sxs-lookup"><span data-stu-id="46c71-104">In this tutorial, you will use SQL Server to distribute messages across a SignalR application that is deployed in two separate IIS instances.</span></span> <span data-ttu-id="46c71-105">Você também pode executar este tutorial em um computador de teste único, mas para obter o efeito completo, você precisará implantar o aplicativo do SignalR em dois ou mais servidores.</span><span class="sxs-lookup"><span data-stu-id="46c71-105">You can also run this tutorial on a single test machine, but to get the full effect, you need to deploy the SignalR application to two or more servers.</span></span> <span data-ttu-id="46c71-106">Você também deve instalar o SQL Server em um dos servidores, ou em um servidor dedicado separado.</span><span class="sxs-lookup"><span data-stu-id="46c71-106">You must also install SQL Server on one of the servers, or on a separate dedicated server.</span></span> <span data-ttu-id="46c71-107">Outra opção é executar o tutorial usando as máquinas virtuais no Azure.</span><span class="sxs-lookup"><span data-stu-id="46c71-107">Another option is to run the tutorial using VMs on Azure.</span></span>

![](scaleout-with-sql-server/_static/image1.png)

## <a name="prerequisites"></a><span data-ttu-id="46c71-108">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="46c71-108">Prerequisites</span></span>

<span data-ttu-id="46c71-109">Microsoft SQL Server 2005 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="46c71-109">Microsoft SQL Server 2005 or later.</span></span> <span data-ttu-id="46c71-110">Backplane dá suporte a edições de área de trabalho e o servidor do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="46c71-110">The backplane supports both desktop and server editions of SQL Server.</span></span> <span data-ttu-id="46c71-111">Ele não oferece suporte a banco de dados do Azure ou SQL Server Compact Edition.</span><span class="sxs-lookup"><span data-stu-id="46c71-111">It does not support SQL Server Compact Edition or Azure SQL Database.</span></span> <span data-ttu-id="46c71-112">(Se seu aplicativo estiver hospedado no Azure, considere o backplane do barramento de serviço em vez disso.)</span><span class="sxs-lookup"><span data-stu-id="46c71-112">(If your application is hosted on Azure, consider the Service Bus backplane instead.)</span></span>

## <a name="overview"></a><span data-ttu-id="46c71-113">Visão geral</span><span class="sxs-lookup"><span data-stu-id="46c71-113">Overview</span></span>

<span data-ttu-id="46c71-114">Antes de passarmos para o tutorial detalhado, aqui está uma visão rápida do que você deve fazer.</span><span class="sxs-lookup"><span data-stu-id="46c71-114">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="46c71-115">Crie um novo banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="46c71-115">Create a new empty database.</span></span> <span data-ttu-id="46c71-116">Backplane criará as tabelas necessárias neste banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46c71-116">The backplane will create the necessary tables in this database.</span></span>
2. <span data-ttu-id="46c71-117">Adicione esses pacotes do NuGet ao seu aplicativo:</span><span class="sxs-lookup"><span data-stu-id="46c71-117">Add these NuGet packages to your application:</span></span> 

    - [<span data-ttu-id="46c71-118">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="46c71-118">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="46c71-119">Microsoft.AspNet.SignalR.SqlServer</span><span class="sxs-lookup"><span data-stu-id="46c71-119">Microsoft.AspNet.SignalR.SqlServer</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR.SqlServer)
3. <span data-ttu-id="46c71-120">Crie um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="46c71-120">Create a SignalR application.</span></span>
4. <span data-ttu-id="46c71-121">Adicione o seguinte código ao global. asax para configurar o backplane:</span><span class="sxs-lookup"><span data-stu-id="46c71-121">Add the following code to Global.asax to configure the backplane:</span></span> 

    [!code-csharp[Main](scaleout-with-sql-server/samples/sample1.cs)]

## <a name="configure-the-database"></a><span data-ttu-id="46c71-122">Configurar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="46c71-122">Configure the Database</span></span>

<span data-ttu-id="46c71-123">Decida se o aplicativo usará a autenticação do Windows ou autenticação do SQL Server para acessar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46c71-123">Decide whether the application will use Windows authentication or SQL Server authentication to access the database.</span></span> <span data-ttu-id="46c71-124">Independentemente disso, certifique-se de que o usuário de banco de dados tem permissões para fazer logon, criar esquemas e criar tabelas.</span><span class="sxs-lookup"><span data-stu-id="46c71-124">Regardless, make sure the database user has permissions to log in, create schemas, and create tables.</span></span>

<span data-ttu-id="46c71-125">Crie um novo banco de dados para o backplane usar.</span><span class="sxs-lookup"><span data-stu-id="46c71-125">Create a new database for the backplane to use.</span></span> <span data-ttu-id="46c71-126">Você pode atribuir qualquer nome para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46c71-126">You can give the database any name.</span></span> <span data-ttu-id="46c71-127">Você não precisa criar todas as tabelas no banco de dados; backplane criará as tabelas necessárias.</span><span class="sxs-lookup"><span data-stu-id="46c71-127">You don't need to create any tables in the database; the backplane will create the necessary tables.</span></span>

![](scaleout-with-sql-server/_static/image2.png)

## <a name="enable-service-broker"></a><span data-ttu-id="46c71-128">Habilite o Service Broker</span><span class="sxs-lookup"><span data-stu-id="46c71-128">Enable Service Broker</span></span>

<span data-ttu-id="46c71-129">É recomendável habilitar o Service Broker para o banco de dados do backplane.</span><span class="sxs-lookup"><span data-stu-id="46c71-129">It is recommended to enable Service Broker for the backplane database.</span></span> <span data-ttu-id="46c71-130">O Service Broker fornece suporte nativo para mensagens e enfileiramento no SQL Server, que permite que o backplane receber atualizações com mais eficiência.</span><span class="sxs-lookup"><span data-stu-id="46c71-130">Service Broker provides native support for messaging and queuing in SQL Server, which lets the backplane receive updates more efficiently.</span></span> <span data-ttu-id="46c71-131">(No entanto, o backplane também funciona sem o Service Broker.)</span><span class="sxs-lookup"><span data-stu-id="46c71-131">(However, the backplane also works without Service Broker.)</span></span>

<span data-ttu-id="46c71-132">Para verificar se o Service Broker está habilitado, consulte o **está\_broker\_habilitada** coluna no **sys. Databases** exibição do catálogo.</span><span class="sxs-lookup"><span data-stu-id="46c71-132">To check whether Service Broker is enabled, query the **is\_broker\_enabled** column in the **sys.databases** catalog view.</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample2.sql)]

![](scaleout-with-sql-server/_static/image3.png)

<span data-ttu-id="46c71-133">Para habilitar o Service Broker, use a seguinte consulta SQL:</span><span class="sxs-lookup"><span data-stu-id="46c71-133">To enable Service Broker, use the following SQL query:</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample3.sql)]

> [!NOTE]
> <span data-ttu-id="46c71-134">Se essa consulta é exibido um deadlock, certifique-se não há nenhum aplicativo conectado ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46c71-134">If this query appears to deadlock, make sure there are no applications connected to the DB.</span></span>

<span data-ttu-id="46c71-135">Se você tiver habilitado o rastreamento, os rastreamentos também mostrará se o Service Broker está habilitado.</span><span class="sxs-lookup"><span data-stu-id="46c71-135">If you have enabled tracing, the traces will also show whether Service Broker is enabled.</span></span>

## <a name="create-a-signalr-application"></a><span data-ttu-id="46c71-136">Criar um aplicativo de SignalR</span><span class="sxs-lookup"><span data-stu-id="46c71-136">Create a SignalR Application</span></span>

<span data-ttu-id="46c71-137">Crie um aplicativo do SignalR, seguindo um destes tutoriais:</span><span class="sxs-lookup"><span data-stu-id="46c71-137">Create a SignalR application by following either of these tutorials:</span></span>

- [<span data-ttu-id="46c71-138">Introdução ao SignalR</span><span class="sxs-lookup"><span data-stu-id="46c71-138">Getting Started with SignalR</span></span>](../getting-started/tutorial-getting-started-with-signalr.md)
- [<span data-ttu-id="46c71-139">Introdução ao SignalR e MVC 4</span><span class="sxs-lookup"><span data-stu-id="46c71-139">Getting Started with SignalR and MVC 4</span></span>](tutorial-getting-started-with-signalr-and-mvc-4.md)

<span data-ttu-id="46c71-140">Em seguida, modificaremos o aplicativo de bate-papo para dar suporte à expansão com o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="46c71-140">Next, we'll modify the chat application to support scaleout with SQL Server.</span></span> <span data-ttu-id="46c71-141">Primeiro, adicione o pacote do SignalR.SqlServer NuGet ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="46c71-141">First, add the SignalR.SqlServer NuGet package to your project.</span></span> <span data-ttu-id="46c71-142">No Visual Studio, do **ferramentas** menu, selecione **Gerenciador de pacotes NuGet**, em seguida, selecione **Package Manager Console**.</span><span class="sxs-lookup"><span data-stu-id="46c71-142">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="46c71-143">Na janela do Console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="46c71-143">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](scaleout-with-sql-server/samples/sample4.ps1)]

<span data-ttu-id="46c71-144">Em seguida, abra o arquivo global asax.</span><span class="sxs-lookup"><span data-stu-id="46c71-144">Next, open the Global.asax file.</span></span> <span data-ttu-id="46c71-145">Adicione o seguinte código para o **Application\_iniciar** método:</span><span class="sxs-lookup"><span data-stu-id="46c71-145">Add the following code to the **Application\_Start** method:</span></span>

[!code-csharp[Main](scaleout-with-sql-server/samples/sample5.cs)]

## <a name="deploy-and-run-the-application"></a><span data-ttu-id="46c71-146">Implantar e executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="46c71-146">Deploy and Run the Application</span></span>

<span data-ttu-id="46c71-147">Prepare suas instâncias do Windows Server para implantar o aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="46c71-147">Prepare your Windows Server instances to deploy the SignalR application.</span></span>

<span data-ttu-id="46c71-148">Adicione a função do IIS.</span><span class="sxs-lookup"><span data-stu-id="46c71-148">Add the IIS role.</span></span> <span data-ttu-id="46c71-149">Inclua recursos de "Desenvolvimento de aplicativos", incluindo o protocolo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="46c71-149">Include "Application Development" features, including the WebSocket Protocol.</span></span>

![](scaleout-with-sql-server/_static/image4.png)

<span data-ttu-id="46c71-150">Também incluem o serviço de gerenciamento (listados em "Ferramentas de gerenciamento").</span><span class="sxs-lookup"><span data-stu-id="46c71-150">Also include the Management Service (listed under "Management Tools").</span></span>

![](scaleout-with-sql-server/_static/image5.png)

<span data-ttu-id="46c71-151">**Install Web implantação 3.0.**</span><span class="sxs-lookup"><span data-stu-id="46c71-151">**Install Web Deploy 3.0.**</span></span> <span data-ttu-id="46c71-152">Quando você executar o Gerenciador do IIS, ele solicitará que você instale o Microsoft Web Platform, ou pode [baixar o intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span><span class="sxs-lookup"><span data-stu-id="46c71-152">When you run IIS Manager, it will prompt you to install Microsoft Web Platform, or you can [download the intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span></span> <span data-ttu-id="46c71-153">No instalador da plataforma, procure implantação da Web e instale o Web Deploy 3.0</span><span class="sxs-lookup"><span data-stu-id="46c71-153">In the Platform Installer, search for Web Deploy and install Web Deploy 3.0</span></span>

![](scaleout-with-sql-server/_static/image6.png)

<span data-ttu-id="46c71-154">Verifique se o serviço de gerenciamento da Web está em execução.</span><span class="sxs-lookup"><span data-stu-id="46c71-154">Check that the Web Management Service is running.</span></span> <span data-ttu-id="46c71-155">Caso contrário, inicie o serviço.</span><span class="sxs-lookup"><span data-stu-id="46c71-155">If not, start the service.</span></span> <span data-ttu-id="46c71-156">(Se você não vir o serviço de gerenciamento da Web na lista de serviços do Windows, certifique-se de que você instalou o serviço de gerenciamento quando você adicionou a função do IIS.)</span><span class="sxs-lookup"><span data-stu-id="46c71-156">(If you don't see Web Management Service in the list of Windows services, make sure that you installed the Management Service when you added the IIS role.)</span></span>

<span data-ttu-id="46c71-157">Por fim, abra a porta 8172 para TCP.</span><span class="sxs-lookup"><span data-stu-id="46c71-157">Finally, open port 8172 for TCP.</span></span> <span data-ttu-id="46c71-158">Essa é a porta que usa a ferramenta de implantação da Web.</span><span class="sxs-lookup"><span data-stu-id="46c71-158">This is the port that the Web Deploy tool uses.</span></span>

<span data-ttu-id="46c71-159">Agora você está pronto para implantar o projeto do Visual Studio em seu computador de desenvolvimento para o servidor.</span><span class="sxs-lookup"><span data-stu-id="46c71-159">Now you are ready to deploy the Visual Studio project from your development machine to the server.</span></span> <span data-ttu-id="46c71-160">No Gerenciador de soluções, clique com botão direito a solução e clique em **publicar**.</span><span class="sxs-lookup"><span data-stu-id="46c71-160">In Solution Explorer, right-click the solution and click **Publish**.</span></span>

<span data-ttu-id="46c71-161">Para obter mais documentação sobre a implantação da web, consulte [mapa de conteúdo de implantação da Web para Visual Studio e o ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="46c71-161">For more detailed documentation about web deployment, see [Web Deployment Content Map for Visual Studio and ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span></span>

<span data-ttu-id="46c71-162">Se você implantar o aplicativo em dois servidores, você pode abrir cada instância em uma janela separada do navegador e ver que cada um receba mensagens do SignalR de outra.</span><span class="sxs-lookup"><span data-stu-id="46c71-162">If you deploy the application to two servers, you can open each instance in a separate browser window and see that they each receive SignalR messages from the other.</span></span> <span data-ttu-id="46c71-163">(É claro que, em um ambiente de produção, os dois servidores estaria atrás de um balanceador de carga.)</span><span class="sxs-lookup"><span data-stu-id="46c71-163">(Of course, in a production environment, the two servers would sit behind a load balancer.)</span></span>

![](scaleout-with-sql-server/_static/image7.png)

<span data-ttu-id="46c71-164">Depois de executar o aplicativo, você pode ver que o SignalR cria automaticamente tabelas no banco de dados:</span><span class="sxs-lookup"><span data-stu-id="46c71-164">After you run the application, you can see that SignalR has automatically created tables in the database:</span></span>

![](scaleout-with-sql-server/_static/image8.png)

<span data-ttu-id="46c71-165">O SignalR gerencia as tabelas.</span><span class="sxs-lookup"><span data-stu-id="46c71-165">SignalR manages the tables.</span></span> <span data-ttu-id="46c71-166">Desde que o aplicativo for implantado, não excluir linhas, modifique a tabela e assim por diante.</span><span class="sxs-lookup"><span data-stu-id="46c71-166">As long as your application is deployed, don't delete rows, modify the table, and so forth.</span></span>
