---
uid: signalr/overview/performance/scaleout-with-sql-server
title: Expansão do SignalR com o SQL Server | Microsoft Docs
author: bradygaster
description: Versões de software usado neste tópico, o Visual Studio 2013 .NET 4.5 SignalR versões anteriores de versão 2 deste tópico para obter informações sobre versões anteriores do...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 98358b6e-9139-4239-ba3a-2d7dd74dd664
msc.legacyurl: /signalr/overview/performance/scaleout-with-sql-server
msc.type: authoredcontent
ms.openlocfilehash: 5984a5e6c3215e7dde8c09ef702bf6453730a3ee
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837891"
---
<a name="signalr-scaleout-with-sql-server"></a><span data-ttu-id="eb48c-103">Expansão do SignalR com o SQL Server</span><span class="sxs-lookup"><span data-stu-id="eb48c-103">SignalR Scaleout with SQL Server</span></span>
====================
<span data-ttu-id="eb48c-104">por [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="eb48c-104">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="eb48c-105">Versões de software usadas neste tópico</span><span class="sxs-lookup"><span data-stu-id="eb48c-105">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="eb48c-106">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="eb48c-106">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="eb48c-107">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="eb48c-107">.NET 4.5</span></span>
> - <span data-ttu-id="eb48c-108">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="eb48c-108">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="eb48c-109">Versões anteriores deste tópico</span><span class="sxs-lookup"><span data-stu-id="eb48c-109">Previous versions of this topic</span></span>
>
> <span data-ttu-id="eb48c-110">Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="eb48c-110">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="eb48c-111">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="eb48c-111">Questions and comments</span></span>
>
> <span data-ttu-id="eb48c-112">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="eb48c-112">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="eb48c-113">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="eb48c-113">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="eb48c-114">Neste tutorial, você usará o SQL Server para distribuir mensagens através de um aplicativo de SignalR é implantado em duas instâncias separadas do IIS.</span><span class="sxs-lookup"><span data-stu-id="eb48c-114">In this tutorial, you will use SQL Server to distribute messages across a SignalR application that is deployed in two separate IIS instances.</span></span> <span data-ttu-id="eb48c-115">Você também pode executar este tutorial em um computador de teste único, mas para obter o efeito completo, você precisará implantar o aplicativo do SignalR em dois ou mais servidores.</span><span class="sxs-lookup"><span data-stu-id="eb48c-115">You can also run this tutorial on a single test machine, but to get the full effect, you need to deploy the SignalR application to two or more servers.</span></span> <span data-ttu-id="eb48c-116">Você também deve instalar o SQL Server em um dos servidores, ou em um servidor dedicado separado.</span><span class="sxs-lookup"><span data-stu-id="eb48c-116">You must also install SQL Server on one of the servers, or on a separate dedicated server.</span></span> <span data-ttu-id="eb48c-117">Outra opção é executar o tutorial usando as máquinas virtuais no Azure.</span><span class="sxs-lookup"><span data-stu-id="eb48c-117">Another option is to run the tutorial using VMs on Azure.</span></span>

![](scaleout-with-sql-server/_static/image1.png)

## <a name="prerequisites"></a><span data-ttu-id="eb48c-118">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="eb48c-118">Prerequisites</span></span>

<span data-ttu-id="eb48c-119">Microsoft SQL Server 2005 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="eb48c-119">Microsoft SQL Server 2005 or later.</span></span> <span data-ttu-id="eb48c-120">Backplane dá suporte a edições de área de trabalho e o servidor do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="eb48c-120">The backplane supports both desktop and server editions of SQL Server.</span></span> <span data-ttu-id="eb48c-121">Ele não oferece suporte a banco de dados do Azure ou SQL Server Compact Edition.</span><span class="sxs-lookup"><span data-stu-id="eb48c-121">It does not support SQL Server Compact Edition or Azure SQL Database.</span></span> <span data-ttu-id="eb48c-122">(Se seu aplicativo estiver hospedado no Azure, considere o backplane do barramento de serviço em vez disso.)</span><span class="sxs-lookup"><span data-stu-id="eb48c-122">(If your application is hosted on Azure, consider the Service Bus backplane instead.)</span></span>

## <a name="overview"></a><span data-ttu-id="eb48c-123">Visão geral</span><span class="sxs-lookup"><span data-stu-id="eb48c-123">Overview</span></span>

<span data-ttu-id="eb48c-124">Antes de passarmos para o tutorial detalhado, aqui está uma visão rápida do que você deve fazer.</span><span class="sxs-lookup"><span data-stu-id="eb48c-124">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="eb48c-125">Crie um novo banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="eb48c-125">Create a new empty database.</span></span> <span data-ttu-id="eb48c-126">Backplane criará as tabelas necessárias neste banco de dados.</span><span class="sxs-lookup"><span data-stu-id="eb48c-126">The backplane will create the necessary tables in this database.</span></span>
2. <span data-ttu-id="eb48c-127">Adicione esses pacotes do NuGet ao seu aplicativo:</span><span class="sxs-lookup"><span data-stu-id="eb48c-127">Add these NuGet packages to your application:</span></span>

    - [<span data-ttu-id="eb48c-128">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="eb48c-128">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="eb48c-129">Microsoft.AspNet.SignalR.SqlServer</span><span class="sxs-lookup"><span data-stu-id="eb48c-129">Microsoft.AspNet.SignalR.SqlServer</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR.SqlServer)
3. <span data-ttu-id="eb48c-130">Crie um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="eb48c-130">Create a SignalR application.</span></span>
4. <span data-ttu-id="eb48c-131">Adicione o seguinte código para o Startup.cs para configurar o backplane:</span><span class="sxs-lookup"><span data-stu-id="eb48c-131">Add the following code to Startup.cs to configure the backplane:</span></span>

    [!code-csharp[Main](scaleout-with-sql-server/samples/sample1.cs)]

   <span data-ttu-id="eb48c-132">Esse código configura o backplane com os valores padrão para [TableCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.sqlscaleoutconfiguration.tablecount(v=vs.118).aspx) e [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx).</span><span class="sxs-lookup"><span data-stu-id="eb48c-132">This code configures the backplane with the default values for [TableCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.sqlscaleoutconfiguration.tablecount(v=vs.118).aspx) and [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx).</span></span> <span data-ttu-id="eb48c-133">Para obter informações sobre como alterar esses valores, consulte [desempenho do SignalR: Métricas de expansão](signalr-performance.md#scaleout_metrics).</span><span class="sxs-lookup"><span data-stu-id="eb48c-133">For information on changing these values, see [SignalR Performance: Scaleout Metrics](signalr-performance.md#scaleout_metrics).</span></span>

## <a name="configure-the-database"></a><span data-ttu-id="eb48c-134">Configurar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="eb48c-134">Configure the Database</span></span>

<span data-ttu-id="eb48c-135">Decida se o aplicativo usará a autenticação do Windows ou autenticação do SQL Server para acessar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="eb48c-135">Decide whether the application will use Windows authentication or SQL Server authentication to access the database.</span></span> <span data-ttu-id="eb48c-136">Independentemente disso, certifique-se de que o usuário de banco de dados tem permissões para fazer logon, criar esquemas e criar tabelas.</span><span class="sxs-lookup"><span data-stu-id="eb48c-136">Regardless, make sure the database user has permissions to log in, create schemas, and create tables.</span></span>

<span data-ttu-id="eb48c-137">Crie um novo banco de dados para o backplane usar.</span><span class="sxs-lookup"><span data-stu-id="eb48c-137">Create a new database for the backplane to use.</span></span> <span data-ttu-id="eb48c-138">Você pode atribuir qualquer nome para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="eb48c-138">You can give the database any name.</span></span> <span data-ttu-id="eb48c-139">Você não precisa criar todas as tabelas no banco de dados; backplane criará as tabelas necessárias.</span><span class="sxs-lookup"><span data-stu-id="eb48c-139">You don't need to create any tables in the database; the backplane will create the necessary tables.</span></span>

![](scaleout-with-sql-server/_static/image2.png)

## <a name="enable-service-broker"></a><span data-ttu-id="eb48c-140">Habilite o Service Broker</span><span class="sxs-lookup"><span data-stu-id="eb48c-140">Enable Service Broker</span></span>

<span data-ttu-id="eb48c-141">É recomendável habilitar o Service Broker para o banco de dados do backplane.</span><span class="sxs-lookup"><span data-stu-id="eb48c-141">It is recommended to enable Service Broker for the backplane database.</span></span> <span data-ttu-id="eb48c-142">O Service Broker fornece suporte nativo para mensagens e enfileiramento no SQL Server, que permite que o backplane receber atualizações com mais eficiência.</span><span class="sxs-lookup"><span data-stu-id="eb48c-142">Service Broker provides native support for messaging and queuing in SQL Server, which lets the backplane receive updates more efficiently.</span></span> <span data-ttu-id="eb48c-143">(No entanto, o backplane também funciona sem o Service Broker.)</span><span class="sxs-lookup"><span data-stu-id="eb48c-143">(However, the backplane also works without Service Broker.)</span></span>

<span data-ttu-id="eb48c-144">Para verificar se o Service Broker está habilitado, consulte o **está\_broker\_habilitada** coluna no **sys. Databases** exibição do catálogo.</span><span class="sxs-lookup"><span data-stu-id="eb48c-144">To check whether Service Broker is enabled, query the **is\_broker\_enabled** column in the **sys.databases** catalog view.</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample2.sql)]

![](scaleout-with-sql-server/_static/image3.png)

<span data-ttu-id="eb48c-145">Para habilitar o Service Broker, use a seguinte consulta SQL:</span><span class="sxs-lookup"><span data-stu-id="eb48c-145">To enable Service Broker, use the following SQL query:</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample3.sql)]

> [!NOTE]
> <span data-ttu-id="eb48c-146">Se essa consulta é exibido um deadlock, certifique-se não há nenhum aplicativo conectado ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="eb48c-146">If this query appears to deadlock, make sure there are no applications connected to the DB.</span></span>


<span data-ttu-id="eb48c-147">Se você tiver habilitado o rastreamento, os rastreamentos também mostrará se o Service Broker está habilitado.</span><span class="sxs-lookup"><span data-stu-id="eb48c-147">If you have enabled tracing, the traces will also show whether Service Broker is enabled.</span></span>

## <a name="create-a-signalr-application"></a><span data-ttu-id="eb48c-148">Criar um aplicativo de SignalR</span><span class="sxs-lookup"><span data-stu-id="eb48c-148">Create a SignalR Application</span></span>

<span data-ttu-id="eb48c-149">Crie um aplicativo do SignalR, seguindo um destes tutoriais:</span><span class="sxs-lookup"><span data-stu-id="eb48c-149">Create a SignalR application by following either of these tutorials:</span></span>

- [<span data-ttu-id="eb48c-150">Introdução ao SignalR 2.0</span><span class="sxs-lookup"><span data-stu-id="eb48c-150">Getting Started with SignalR 2.0</span></span>](../getting-started/tutorial-getting-started-with-signalr.md)
- [<span data-ttu-id="eb48c-151">Introdução ao SignalR 2.0 e ao MVC 5</span><span class="sxs-lookup"><span data-stu-id="eb48c-151">Getting Started with SignalR 2.0 and MVC 5</span></span>](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md)

<span data-ttu-id="eb48c-152">Em seguida, modificaremos o aplicativo de bate-papo para dar suporte à expansão com o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="eb48c-152">Next, we'll modify the chat application to support scaleout with SQL Server.</span></span> <span data-ttu-id="eb48c-153">Primeiro, adicione o pacote do SignalR.SqlServer NuGet ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="eb48c-153">First, add the SignalR.SqlServer NuGet package to your project.</span></span> <span data-ttu-id="eb48c-154">No Visual Studio, do **ferramentas** menu, selecione **Gerenciador de pacotes NuGet**, em seguida, selecione **Package Manager Console**.</span><span class="sxs-lookup"><span data-stu-id="eb48c-154">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="eb48c-155">Na janela do Console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="eb48c-155">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](scaleout-with-sql-server/samples/sample4.ps1)]

<span data-ttu-id="eb48c-156">Em seguida, abra o arquivo Startup.cs.</span><span class="sxs-lookup"><span data-stu-id="eb48c-156">Next, open the Startup.cs file.</span></span> <span data-ttu-id="eb48c-157">Adicione o seguinte código para o **configurar** método:</span><span class="sxs-lookup"><span data-stu-id="eb48c-157">Add the following code to the **Configure** method:</span></span>

[!code-csharp[Main](scaleout-with-sql-server/samples/sample5.cs)]

## <a name="deploy-and-run-the-application"></a><span data-ttu-id="eb48c-158">Implantar e executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="eb48c-158">Deploy and Run the Application</span></span>

<span data-ttu-id="eb48c-159">Prepare suas instâncias do Windows Server para implantar o aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="eb48c-159">Prepare your Windows Server instances to deploy the SignalR application.</span></span>

<span data-ttu-id="eb48c-160">Adicione a função do IIS.</span><span class="sxs-lookup"><span data-stu-id="eb48c-160">Add the IIS role.</span></span> <span data-ttu-id="eb48c-161">Inclua recursos de "Desenvolvimento de aplicativos", incluindo o protocolo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="eb48c-161">Include "Application Development" features, including the WebSocket Protocol.</span></span>

![](scaleout-with-sql-server/_static/image4.png)

<span data-ttu-id="eb48c-162">Também incluem o serviço de gerenciamento (listados em "Ferramentas de gerenciamento").</span><span class="sxs-lookup"><span data-stu-id="eb48c-162">Also include the Management Service (listed under "Management Tools").</span></span>

![](scaleout-with-sql-server/_static/image5.png)

<span data-ttu-id="eb48c-163">**Install Web implantação 3.0.**</span><span class="sxs-lookup"><span data-stu-id="eb48c-163">**Install Web Deploy 3.0.**</span></span> <span data-ttu-id="eb48c-164">Quando você executar o Gerenciador do IIS, ele solicitará que você instale o Microsoft Web Platform, ou pode [baixar o intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span><span class="sxs-lookup"><span data-stu-id="eb48c-164">When you run IIS Manager, it will prompt you to install Microsoft Web Platform, or you can [download the intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span></span> <span data-ttu-id="eb48c-165">No instalador da plataforma, procure implantação da Web e instale o Web Deploy 3.0</span><span class="sxs-lookup"><span data-stu-id="eb48c-165">In the Platform Installer, search for Web Deploy and install Web Deploy 3.0</span></span>

![](scaleout-with-sql-server/_static/image6.png)

<span data-ttu-id="eb48c-166">Verifique se o serviço de gerenciamento da Web está em execução.</span><span class="sxs-lookup"><span data-stu-id="eb48c-166">Check that the Web Management Service is running.</span></span> <span data-ttu-id="eb48c-167">Caso contrário, inicie o serviço.</span><span class="sxs-lookup"><span data-stu-id="eb48c-167">If not, start the service.</span></span> <span data-ttu-id="eb48c-168">(Se você não vir o serviço de gerenciamento da Web na lista de serviços do Windows, certifique-se de que você instalou o serviço de gerenciamento quando você adicionou a função do IIS.)</span><span class="sxs-lookup"><span data-stu-id="eb48c-168">(If you don't see Web Management Service in the list of Windows services, make sure that you installed the Management Service when you added the IIS role.)</span></span>

<span data-ttu-id="eb48c-169">Por fim, abra a porta 8172 para TCP.</span><span class="sxs-lookup"><span data-stu-id="eb48c-169">Finally, open port 8172 for TCP.</span></span> <span data-ttu-id="eb48c-170">Essa é a porta que usa a ferramenta de implantação da Web.</span><span class="sxs-lookup"><span data-stu-id="eb48c-170">This is the port that the Web Deploy tool uses.</span></span>

<span data-ttu-id="eb48c-171">Agora você está pronto para implantar o projeto do Visual Studio em seu computador de desenvolvimento para o servidor.</span><span class="sxs-lookup"><span data-stu-id="eb48c-171">Now you are ready to deploy the Visual Studio project from your development machine to the server.</span></span> <span data-ttu-id="eb48c-172">No Gerenciador de soluções, clique com botão direito a solução e clique em **publicar**.</span><span class="sxs-lookup"><span data-stu-id="eb48c-172">In Solution Explorer, right-click the solution and click **Publish**.</span></span>

<span data-ttu-id="eb48c-173">Para obter mais documentação sobre a implantação da web, consulte [mapa de conteúdo de implantação da Web para Visual Studio e o ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="eb48c-173">For more detailed documentation about web deployment, see [Web Deployment Content Map for Visual Studio and ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span></span>

<span data-ttu-id="eb48c-174">Se você implantar o aplicativo em dois servidores, você pode abrir cada instância em uma janela separada do navegador e ver que cada um receba mensagens do SignalR de outra.</span><span class="sxs-lookup"><span data-stu-id="eb48c-174">If you deploy the application to two servers, you can open each instance in a separate browser window and see that they each receive SignalR messages from the other.</span></span> <span data-ttu-id="eb48c-175">(É claro que, em um ambiente de produção, os dois servidores estaria atrás de um balanceador de carga.)</span><span class="sxs-lookup"><span data-stu-id="eb48c-175">(Of course, in a production environment, the two servers would sit behind a load balancer.)</span></span>

![](scaleout-with-sql-server/_static/image7.png)

<span data-ttu-id="eb48c-176">Depois de executar o aplicativo, você pode ver que o SignalR cria automaticamente tabelas no banco de dados:</span><span class="sxs-lookup"><span data-stu-id="eb48c-176">After you run the application, you can see that SignalR has automatically created tables in the database:</span></span>

![](scaleout-with-sql-server/_static/image8.png)

<span data-ttu-id="eb48c-177">O SignalR gerencia as tabelas.</span><span class="sxs-lookup"><span data-stu-id="eb48c-177">SignalR manages the tables.</span></span> <span data-ttu-id="eb48c-178">Desde que o aplicativo for implantado, não excluir linhas, modifique a tabela e assim por diante.</span><span class="sxs-lookup"><span data-stu-id="eb48c-178">As long as your application is deployed, don't delete rows, modify the table, and so forth.</span></span>
