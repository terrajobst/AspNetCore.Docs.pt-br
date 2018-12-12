---
uid: signalr/overview/performance/using-signalr-performance-counters-in-an-azure-web-role
title: Usando contadores de desempenho do SignalR em uma função da Web do Azure | Microsoft Docs
author: guardrex
description: Como instalar e usar contadores de desempenho do SignalR em uma função da Web do Azure.
keywords: Contador ASP.NET,SignalR,Performance, função web do azure
ms.author: riande
ms.date: 10/03/2018
ms.assetid: 2a127d3b-21ed-4cc9-bec0-cdab4e742a25
msc.legacyurl: /signalr/overview/performance/using-signalr-performance-counters-in-an-azure-web-role
msc.type: authoredcontent
ms.openlocfilehash: bdd875201895c6eaf155b54582d0898c2570d93c
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287684"
---
# <a name="using-signalr-performance-counters-in-an-azure-web-role"></a><span data-ttu-id="43ea9-104">Usando contadores de desempenho do SignalR em uma função da Web do Azure</span><span class="sxs-lookup"><span data-stu-id="43ea9-104">Using SignalR performance counters in an Azure Web Role</span></span>

<span data-ttu-id="43ea9-105">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="43ea9-105">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="43ea9-106">Contadores de desempenho do SignalR são usados para monitorar o desempenho do seu aplicativo em uma função da Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="43ea9-106">SignalR performance counters are used to monitor your app's performance in an Azure Web Role.</span></span> <span data-ttu-id="43ea9-107">Os contadores são capturados pelo diagnóstico do Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="43ea9-107">The counters are captured by Microsoft Azure Diagnostics.</span></span> <span data-ttu-id="43ea9-108">Instalar contadores de desempenho do SignalR no Azure com o *signalr.exe*, da mesma ferramenta usada para aplicativos autônomos ou local.</span><span class="sxs-lookup"><span data-stu-id="43ea9-108">You install SignalR performance counters on Azure with *signalr.exe*, the same tool used for standalone or on-premises apps.</span></span> <span data-ttu-id="43ea9-109">Como as funções do Azure são transitórias, você pode configurar um aplicativo para instalar e registrar os contadores de desempenho do SignalR após a inicialização.</span><span class="sxs-lookup"><span data-stu-id="43ea9-109">Since Azure roles are transient, you configure an app to install and register SignalR performance counters upon startup.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="43ea9-110">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="43ea9-110">Prerequisites</span></span>

* <span data-ttu-id="43ea9-111">Visual Studio 2015 ou [2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)</span><span class="sxs-lookup"><span data-stu-id="43ea9-111">Visual Studio 2015 or [2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)</span></span>
* <span data-ttu-id="43ea9-112">[SDK do Microsoft Azure para Visual Studio](https://azure.microsoft.com/downloads/) **Observação: Reinicie seu computador depois de instalar o SDK.**</span><span class="sxs-lookup"><span data-stu-id="43ea9-112">[Microsoft Azure SDK for Visual Studio](https://azure.microsoft.com/downloads/) **Note: Restart your machine after installing the SDK.**</span></span>
* <span data-ttu-id="43ea9-113">Assinatura do Microsoft Azure: Para se inscrever para uma conta de avaliação gratuita do Azure, consulte [avaliação gratuita do Azure](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="43ea9-113">Microsoft Azure subscription: To sign up for a free Azure trial account, see [Azure Free Trial](https://azure.microsoft.com/free/).</span></span>

## <a name="creating-an-azure-web-role-application-that-exposes-signalr-performance-counters"></a><span data-ttu-id="43ea9-114">Criar um aplicativo de função Web do Azure que expõe contadores de desempenho do SignalR</span><span class="sxs-lookup"><span data-stu-id="43ea9-114">Creating an Azure Web Role application that exposes SignalR performance counters</span></span>

1. <span data-ttu-id="43ea9-115">Abra o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="43ea9-115">Open Visual Studio.</span></span>

2. <span data-ttu-id="43ea9-116">No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-116">In Visual Studio, select **File** > **New** > **Project**.</span></span>

3. <span data-ttu-id="43ea9-117">No **novo projeto** caixa de diálogo, selecione o **Visual c#** > **nuvem** categoria à esquerda e, em seguida, selecione o **serviço de nuvem do Azure** modelo.</span><span class="sxs-lookup"><span data-stu-id="43ea9-117">In the **New Project** dialog box, select the **Visual C#** > **Cloud** category on the left, and then select the **Azure Cloud Service** template.</span></span> <span data-ttu-id="43ea9-118">Nomeie o aplicativo **SignalRPerfCounters** e selecione **Okey**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-118">Name the app **SignalRPerfCounters** and select **OK**.</span></span>

   ![Novo aplicativo de nuvem](using-signalr-performance-counters-in-an-azure-web-role/_static/image1.png)

   > [!NOTE]
   > <span data-ttu-id="43ea9-120">Se você não vir as **nuvem** categoria do modelo ou o **serviço de nuvem do Azure** modelo, você precisará instalar o **desenvolvimento do Azure** carga de trabalho do Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="43ea9-120">If you don't see the **Cloud** template category or the **Azure Cloud Service** template, you need to install the **Azure development** workload for Visual Studio 2017.</span></span> <span data-ttu-id="43ea9-121">Escolha o **abrir instalador do Visual Studio** link no lado inferior esquerdo das **novo projeto** caixa de diálogo para abrir o instalador do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="43ea9-121">Choose the **Open Visual Studio Installer** link on the bottom-left side of the **New Project** dialog to open Visual Studio Installer.</span></span> <span data-ttu-id="43ea9-122">Selecione o **desenvolvimento do Azure** carga de trabalho e, em seguida, escolha **modificar** para começar a instalar a carga de trabalho.</span><span class="sxs-lookup"><span data-stu-id="43ea9-122">Select the **Azure development** workload, and then choose **Modify** to start installing the workload.</span></span>
   >
   > ![Carga de trabalho de desenvolvimento do Azure no instalador do Visual Studio](using-signalr-performance-counters-in-an-azure-web-role/_static/azure-development-workload.png)

4. <span data-ttu-id="43ea9-124">No **novo serviço de nuvem do Microsoft Azure** caixa de diálogo, selecione **função Web ASP.NET** e selecione o > botão para adicionar a função ao projeto.</span><span class="sxs-lookup"><span data-stu-id="43ea9-124">In the **New Microsoft Azure Cloud Service** dialog, select **ASP.NET Web Role** and select the > button to add the role to the project.</span></span> <span data-ttu-id="43ea9-125">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-125">Select **OK**.</span></span>

   ![Adicionar a função Web do ASP.NET](using-signalr-performance-counters-in-an-azure-web-role/_static/image2.png)

5. <span data-ttu-id="43ea9-127">No **novo aplicativo de Web do ASP.NET - WebRole1** caixa de diálogo, selecione o **MVC** modelo e, em seguida, selecione **Okey**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-127">In the **New ASP.NET Web Application - WebRole1** dialog, select the **MVC** template, and then select **OK**.</span></span>

   ![Adicionar a API do MVC e Web](using-signalr-performance-counters-in-an-azure-web-role/_static/image3.png)

6. <span data-ttu-id="43ea9-129">Na **Gerenciador de soluções**, abra o *Diagnostics. wadcfgx* arquivo sob **WebRole1**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-129">In **Solution Explorer**, open the *diagnostics.wadcfgx* file under **WebRole1**.</span></span>

   ![Diagnostics. wadcfgx do Solution Explorer](using-signalr-performance-counters-in-an-azure-web-role/_static/image4.png)

7. <span data-ttu-id="43ea9-131">Substitua o conteúdo do arquivo com a configuração a seguir e salve o arquivo:</span><span class="sxs-lookup"><span data-stu-id="43ea9-131">Replace the contents of the file with the following configuration and save the file:</span></span>

   [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample1.xml)]

8. <span data-ttu-id="43ea9-132">Abra o **Package Manager Console** de **ferramentas** > **NuGet Package Manager**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-132">Open the **Package Manager Console** from **Tools** > **NuGet Package Manager**.</span></span> <span data-ttu-id="43ea9-133">Insira os seguintes comandos para instalar a versão mais recente do SignalR e o pacote de utilitários do SignalR:</span><span class="sxs-lookup"><span data-stu-id="43ea9-133">Enter the following commands to install the latest version of SignalR and the SignalR utilities package:</span></span>

   [!code-powershell[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample2.ps1)]

9. <span data-ttu-id="43ea9-134">Configure o aplicativo para instalar os contadores de desempenho do SignalR para a instância de função quando ele for inicializado ou se reciclar.</span><span class="sxs-lookup"><span data-stu-id="43ea9-134">Configure the app to install the SignalR performance counters into the role instance when it starts up or recycles.</span></span> <span data-ttu-id="43ea9-135">No **Gerenciador de soluções**, clique com botão direito no **WebRole1** do projeto e selecione **Add** > **nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-135">In **Solution Explorer**, right-click on the **WebRole1** project and select **Add** > **New Folder**.</span></span> <span data-ttu-id="43ea9-136">Nomeie a nova pasta *inicialização*.</span><span class="sxs-lookup"><span data-stu-id="43ea9-136">Name the new folder *Startup*.</span></span>

   ![Adicionar pasta de inicialização](using-signalr-performance-counters-in-an-azure-web-role/_static/image5.png)

10. <span data-ttu-id="43ea9-138">Cópia de *signalr.exe* arquivo (adicionada com o **Microsoft.AspNet.SignalR.Utils** pacote) do \<pasta do projeto > / SignalRPerfCounters/packages/Microsoft.AspNet.SignalR.Utils.\< versão > / tools para o *inicialização* pasta que você criou na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="43ea9-138">Copy the *signalr.exe* file (added with the **Microsoft.AspNet.SignalR.Utils** package) from \<project folder>/SignalRPerfCounters/packages/Microsoft.AspNet.SignalR.Utils.\<version>/tools to the *Startup* folder you created in the previous step.</span></span>

11. <span data-ttu-id="43ea9-139">No **Gerenciador de soluções**, clique com botão direito do *inicialização* pasta e selecione **Add** > **Item existente**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-139">In **Solution Explorer**, right-click the *Startup* folder and select **Add** > **Existing Item**.</span></span> <span data-ttu-id="43ea9-140">Na caixa de diálogo que aparece, selecione *signalr.exe* e selecione **Add**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-140">In the dialog that appears, select *signalr.exe* and select **Add**.</span></span>

    ![Adicionar signalr.exe ao projeto](using-signalr-performance-counters-in-an-azure-web-role/_static/image6.png)

12. <span data-ttu-id="43ea9-142">Clique com botão direito no *inicialização* pasta que você criou.</span><span class="sxs-lookup"><span data-stu-id="43ea9-142">Right-click on the *Startup* folder you created.</span></span> <span data-ttu-id="43ea9-143">Selecione **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-143">Select **Add** > **New Item**.</span></span> <span data-ttu-id="43ea9-144">Selecione o **gerais** nó, selecione **arquivo de texto**e nomeie o novo item *SignalRPerfCounterInstall.cmd*.</span><span class="sxs-lookup"><span data-stu-id="43ea9-144">Select the **General** node, select **Text File**, and name the new item *SignalRPerfCounterInstall.cmd*.</span></span> <span data-ttu-id="43ea9-145">Esse arquivo de comando instalará os contadores de desempenho do SignalR para a função da web.</span><span class="sxs-lookup"><span data-stu-id="43ea9-145">This command file will install the SignalR performance counters into the web role.</span></span>

    ![Criar o arquivo de lote de instalação do contador de desempenho de SignalR](using-signalr-performance-counters-in-an-azure-web-role/_static/image7.png)

13. <span data-ttu-id="43ea9-147">Quando o Visual Studio cria o *SignalRPerfCounterInstall.cmd* arquivo, ele será aberto automaticamente na janela principal.</span><span class="sxs-lookup"><span data-stu-id="43ea9-147">When Visual Studio creates the *SignalRPerfCounterInstall.cmd* file, it will automatically open in the main window.</span></span> <span data-ttu-id="43ea9-148">Substitua o conteúdo do arquivo de script a seguir, em seguida, salve e feche o arquivo.</span><span class="sxs-lookup"><span data-stu-id="43ea9-148">Replace the contents of the file with the following script, then save and close the file.</span></span> <span data-ttu-id="43ea9-149">Esse script é executado *signalr.exe*, que adiciona os contadores de desempenho do SignalR para a instância de função.</span><span class="sxs-lookup"><span data-stu-id="43ea9-149">This script executes *signalr.exe*, which adds the SignalR performance counters to the role instance.</span></span>

    [!code-console[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample3.cmd)]

14. <span data-ttu-id="43ea9-150">Selecione o *signalr.exe* arquivo no **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-150">Select the *signalr.exe* file in **Solution Explorer**.</span></span> <span data-ttu-id="43ea9-151">No arquivo de **propriedades**, defina **Copy to Output Directory** para **copiar sempre**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-151">In the file's **Properties**, set **Copy to Output Directory** to **Copy Always**.</span></span>

    ![Defina a cópia do diretório de saída para copiar sempre](using-signalr-performance-counters-in-an-azure-web-role/_static/image8.png)

15. <span data-ttu-id="43ea9-153">Repita a etapa anterior para o *SignalRPerfCounterInstall.cmd* arquivo.</span><span class="sxs-lookup"><span data-stu-id="43ea9-153">Repeat the previous step for the *SignalRPerfCounterInstall.cmd* file.</span></span>


16. <span data-ttu-id="43ea9-154">Clique com botão direito no *SignalRPerfCounterInstall.cmd* do arquivo e selecione **abrir com**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-154">Right-click on the *SignalRPerfCounterInstall.cmd* file and select **Open With**.</span></span> <span data-ttu-id="43ea9-155">Na caixa de diálogo que aparece, selecione **Editor binário** e selecione **Okey**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-155">In the dialog that appears, select **Binary Editor** and select **OK**.</span></span>

    ![Abrir com Editor binário](using-signalr-performance-counters-in-an-azure-web-role/_static/image9.png)

17. <span data-ttu-id="43ea9-157">No editor binário, selecione qualquer bytes à esquerda no arquivo e excluí-los.</span><span class="sxs-lookup"><span data-stu-id="43ea9-157">In the binary editor, select any leading bytes in the file and delete them.</span></span> <span data-ttu-id="43ea9-158">Salve e feche o arquivo.</span><span class="sxs-lookup"><span data-stu-id="43ea9-158">Save and close the file.</span></span>

    ![Excluir bytes à esquerda](using-signalr-performance-counters-in-an-azure-web-role/_static/image10.png)

18. <span data-ttu-id="43ea9-160">Abra *servicedefinition. Csdef* e adicione uma tarefa de inicialização que executa o *SignalrPerfCounterInstall.cmd* arquivo quando o serviço é iniciado:</span><span class="sxs-lookup"><span data-stu-id="43ea9-160">Open *ServiceDefinition.csdef* and add a startup task that executes the *SignalrPerfCounterInstall.cmd* file when the service starts up:</span></span>

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample4.xml?highlight=4-7)]

19. <span data-ttu-id="43ea9-161">Abra `Views/Shared/_Layout.cshtml` e remover o script de pacote jQuery do final do arquivo.</span><span class="sxs-lookup"><span data-stu-id="43ea9-161">Open `Views/Shared/_Layout.cshtml` and remove the jQuery bundle script from the end of the file.</span></span>

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample5.cshtml)]

20. <span data-ttu-id="43ea9-162">Adicionar um cliente JavaScript que chama continuamente o `increment` método no servidor.</span><span class="sxs-lookup"><span data-stu-id="43ea9-162">Add a JavaScript client that continuously calls the `increment` method on the server.</span></span> <span data-ttu-id="43ea9-163">Abra `Views/Home/Index.cshtml` e substitua o conteúdo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="43ea9-163">Open `Views/Home/Index.cshtml` and replace the contents with the following code:</span></span>

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample6.cshtml)]

21. <span data-ttu-id="43ea9-164">Crie uma nova pasta na **WebRole1** projeto chamado *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="43ea9-164">Create a new folder in the **WebRole1** project named *Hubs*.</span></span> <span data-ttu-id="43ea9-165">Clique com botão direito a *Hubs* pasta no **Gerenciador de soluções** e selecione **Add** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-165">Right-click the *Hubs* folder in **Solution Explorer** and select **Add** > **New Item**.</span></span> <span data-ttu-id="43ea9-166">No **Adicionar Novo Item** caixa de diálogo, selecione o **Web** > **SignalR** categoria e, em seguida, selecione o **classe de Hub do SignalR (v2)** modelo de item.</span><span class="sxs-lookup"><span data-stu-id="43ea9-166">In the **Add New Item** dialog box, select the **Web** > **SignalR** category, and then select the **SignalR Hub Class (v2)** item template.</span></span> <span data-ttu-id="43ea9-167">Nomeie o novo hub *MyHub.cs* e selecione **Add**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-167">Name the new hub *MyHub.cs* and select **Add**.</span></span>

    ![Adicionar classe de Hub do SignalR para a pasta Hubs na caixa de diálogo Adicionar Novo Item](using-signalr-performance-counters-in-an-azure-web-role/_static/image13.png)

22. <span data-ttu-id="43ea9-169">*MyHub.cs* será aberto automaticamente na janela principal.</span><span class="sxs-lookup"><span data-stu-id="43ea9-169">*MyHub.cs* will automatically open in the main window.</span></span> <span data-ttu-id="43ea9-170">Substitua o conteúdo pelo código a seguir, em seguida, salve e feche o arquivo:</span><span class="sxs-lookup"><span data-stu-id="43ea9-170">Replace the contents with the following code, then save and close the file:</span></span>

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample7.cs)]

23. <span data-ttu-id="43ea9-171">*[Crank.exe](signalr-connection-density-testing-with-crank.md)*  é uma ferramenta fornecida com a base de código do SignalR de testes de densidade de conexão.</span><span class="sxs-lookup"><span data-stu-id="43ea9-171">*[Crank.exe](signalr-connection-density-testing-with-crank.md)* is a connection density testing tool provided with the SignalR codebase.</span></span> <span data-ttu-id="43ea9-172">Como manivela requer uma conexão persistente, adicioná-lo seu site para uso durante o teste.</span><span class="sxs-lookup"><span data-stu-id="43ea9-172">Since Crank requires a persistent connection, you add one to your site for use when testing.</span></span> <span data-ttu-id="43ea9-173">Adicionar uma nova pasta para o **WebRole1** projeto chamado *PersistentConnections*.</span><span class="sxs-lookup"><span data-stu-id="43ea9-173">Add a new folder to the **WebRole1** project called *PersistentConnections*.</span></span> <span data-ttu-id="43ea9-174">Essa pasta com o botão direito e selecione **Add** > **classe**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-174">Right-click this folder and select **Add** > **Class**.</span></span> <span data-ttu-id="43ea9-175">Nomeie o novo arquivo de classe *MyPersistentConnections.cs* e selecione **Add**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-175">Name the new class file *MyPersistentConnections.cs* and select **Add**.</span></span>

24. <span data-ttu-id="43ea9-176">Visual Studio abrirá o *MyPersistentConnections.cs* arquivo na janela principal.</span><span class="sxs-lookup"><span data-stu-id="43ea9-176">Visual Studio will open the *MyPersistentConnections.cs* file in the main window.</span></span> <span data-ttu-id="43ea9-177">Substitua o conteúdo pelo código a seguir, em seguida, salve e feche o arquivo:</span><span class="sxs-lookup"><span data-stu-id="43ea9-177">Replace the contents with the following code, then save and close the file:</span></span>

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample8.cs)]

25. <span data-ttu-id="43ea9-178">Usando o `Startup` classe, os objetos de SignalR iniciado na inicialização do OWIN para cima.</span><span class="sxs-lookup"><span data-stu-id="43ea9-178">Using the `Startup` class, the SignalR objects start when OWIN starts up.</span></span> <span data-ttu-id="43ea9-179">Abra ou crie *Startup.cs* e substitua o conteúdo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="43ea9-179">Open or create *Startup.cs* and replace the contents with the following code:</span></span>

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample9.cs)]

    <span data-ttu-id="43ea9-180">No código acima, o `OwinStartup` atributo marca essa classe para iniciar o OWIN.</span><span class="sxs-lookup"><span data-stu-id="43ea9-180">In the code above, the `OwinStartup` attribute marks this class to start OWIN.</span></span> <span data-ttu-id="43ea9-181">O `Configuration` método inicia o SignalR.</span><span class="sxs-lookup"><span data-stu-id="43ea9-181">The `Configuration` method starts SignalR.</span></span>

26. <span data-ttu-id="43ea9-182">Teste seu aplicativo no emulador do Microsoft Azure pressionando **F5**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-182">Test your application in the Microsoft Azure Emulator by pressing **F5**.</span></span>

    > [!NOTE]
    > <span data-ttu-id="43ea9-183">Se você encontrar um **FileLoadException** na **MapSignalR**, altere os redirecionamentos de associação no *Web. config* ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="43ea9-183">If you encounter a **FileLoadException** at **MapSignalR**, change the binding redirects in *web.config* to the following:</span></span>

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample12.xml?highlight=3,7)]

27. <span data-ttu-id="43ea9-184">Aguarde aproximadamente um minuto.</span><span class="sxs-lookup"><span data-stu-id="43ea9-184">Wait about one minute.</span></span> <span data-ttu-id="43ea9-185">Abra a janela da ferramenta Gerenciador de nuvem no Visual Studio (**modo de exibição** > **Cloud Explorer**) e expanda o caminho `(Local)/Storage Accounts/(Development)/Tables`.</span><span class="sxs-lookup"><span data-stu-id="43ea9-185">Open the Cloud Explorer tool window in Visual Studio (**View** > **Cloud Explorer**) and expand the path `(Local)/Storage Accounts/(Development)/Tables`.</span></span> <span data-ttu-id="43ea9-186">Clique duas vezes em **WADPerformanceCountersTable**.</span><span class="sxs-lookup"><span data-stu-id="43ea9-186">Double-click **WADPerformanceCountersTable**.</span></span> <span data-ttu-id="43ea9-187">Você deve ver os contadores do SignalR nos dados de tabela.</span><span class="sxs-lookup"><span data-stu-id="43ea9-187">You should see SignalR counters in the table data.</span></span> <span data-ttu-id="43ea9-188">Se você não vir a tabela, talvez você precise inserir novamente suas credenciais de armazenamento do Azure.</span><span class="sxs-lookup"><span data-stu-id="43ea9-188">If you don't see the table, you may need to re-enter your Azure Storage credentials.</span></span> <span data-ttu-id="43ea9-189">Talvez você precise selecionar o **atualize** botão para ver a tabela na **Cloud Explorer** ou selecione o **atualizar** botão na janela Abrir tabela para ver os dados na tabela.</span><span class="sxs-lookup"><span data-stu-id="43ea9-189">You may need to select the **Refresh** button to see the table in **Cloud Explorer** or select the **Refresh** button in the open table window to see data in the table.</span></span>

    ![Selecionar a tabela de contadores de desempenho do WAD no Visual Studio Cloud Explorer](using-signalr-performance-counters-in-an-azure-web-role/_static/image11.png)

    ![Mostrar os contadores coletados na tabela de contadores de desempenho do WAD](using-signalr-performance-counters-in-an-azure-web-role/_static/image12.png)

28. <span data-ttu-id="43ea9-192">Para testar seu aplicativo na nuvem, atualize o **ServiceConfiguration** do arquivo e defina o `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` para uma cadeia de conexão de conta de armazenamento do Azure válida.</span><span class="sxs-lookup"><span data-stu-id="43ea9-192">To test your application in the cloud, update the **ServiceConfiguration.Cloud.cscfg** file and set the `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` to a valid Azure Storage account connection string.</span></span>

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample10.xml)]

29. <span data-ttu-id="43ea9-193">Implante o aplicativo em sua assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="43ea9-193">Deploy the application to your Azure subscription.</span></span> <span data-ttu-id="43ea9-194">Para obter detalhes sobre como implantar um aplicativo no Azure, consulte [como criar e implantar um serviço de nuvem](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy).</span><span class="sxs-lookup"><span data-stu-id="43ea9-194">For details on how to deploy an application to Azure, see [How to Create and Deploy a Cloud Service](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy).</span></span>

30. <span data-ttu-id="43ea9-195">Aguarde alguns minutos.</span><span class="sxs-lookup"><span data-stu-id="43ea9-195">Wait a few minutes.</span></span> <span data-ttu-id="43ea9-196">Na **Cloud Explorer**, localize a conta de armazenamento configurado anteriormente e localizar o `WADPerformanceCountersTable` tabela nele.</span><span class="sxs-lookup"><span data-stu-id="43ea9-196">In **Cloud Explorer**, locate the storage account you configured above and find the `WADPerformanceCountersTable` table in it.</span></span> <span data-ttu-id="43ea9-197">Você deve ver os contadores do SignalR nos dados de tabela.</span><span class="sxs-lookup"><span data-stu-id="43ea9-197">You should see SignalR counters in the table data.</span></span> <span data-ttu-id="43ea9-198">Se você não vir a tabela, talvez você precise inserir novamente suas credenciais de armazenamento do Azure.</span><span class="sxs-lookup"><span data-stu-id="43ea9-198">If you don't see the table, you may need to re-enter your Azure Storage credentials.</span></span> <span data-ttu-id="43ea9-199">Talvez você precise selecionar o **atualize** botão para ver a tabela na **Cloud Explorer** ou selecione o **atualizar** botão na janela Abrir tabela para ver os dados na tabela.</span><span class="sxs-lookup"><span data-stu-id="43ea9-199">You may need to select the **Refresh** button to see the table in **Cloud Explorer** or select the **Refresh** button in the open table window to see data in the table.</span></span>

<span data-ttu-id="43ea9-200">Agradecimentos especiais a [Martin Richard](https://social.msdn.microsoft.com/profile/Martin+Richard) para o conteúdo original usado neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="43ea9-200">Special thanks to [Martin Richard](https://social.msdn.microsoft.com/profile/Martin+Richard) for the original content used in this tutorial.</span></span>
