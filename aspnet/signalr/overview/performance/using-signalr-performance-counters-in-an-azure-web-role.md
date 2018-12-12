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
# <a name="using-signalr-performance-counters-in-an-azure-web-role"></a>Usando contadores de desempenho do SignalR em uma função da Web do Azure

Por [Luke Latham](https://github.com/guardrex)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

Contadores de desempenho do SignalR são usados para monitorar o desempenho do seu aplicativo em uma função da Web do Azure. Os contadores são capturados pelo diagnóstico do Microsoft Azure. Instalar contadores de desempenho do SignalR no Azure com o *signalr.exe*, da mesma ferramenta usada para aplicativos autônomos ou local. Como as funções do Azure são transitórias, você pode configurar um aplicativo para instalar e registrar os contadores de desempenho do SignalR após a inicialização.

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio 2015 ou [2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
* [SDK do Microsoft Azure para Visual Studio](https://azure.microsoft.com/downloads/) **Observação: Reinicie seu computador depois de instalar o SDK.**
* Assinatura do Microsoft Azure: Para se inscrever para uma conta de avaliação gratuita do Azure, consulte [avaliação gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="creating-an-azure-web-role-application-that-exposes-signalr-performance-counters"></a>Criar um aplicativo de função Web do Azure que expõe contadores de desempenho do SignalR

1. Abra o Visual Studio.

2. No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.

3. No **novo projeto** caixa de diálogo, selecione o **Visual c#** > **nuvem** categoria à esquerda e, em seguida, selecione o **serviço de nuvem do Azure** modelo. Nomeie o aplicativo **SignalRPerfCounters** e selecione **Okey**.

   ![Novo aplicativo de nuvem](using-signalr-performance-counters-in-an-azure-web-role/_static/image1.png)

   > [!NOTE]
   > Se você não vir as **nuvem** categoria do modelo ou o **serviço de nuvem do Azure** modelo, você precisará instalar o **desenvolvimento do Azure** carga de trabalho do Visual Studio 2017. Escolha o **abrir instalador do Visual Studio** link no lado inferior esquerdo das **novo projeto** caixa de diálogo para abrir o instalador do Visual Studio. Selecione o **desenvolvimento do Azure** carga de trabalho e, em seguida, escolha **modificar** para começar a instalar a carga de trabalho.
   >
   > ![Carga de trabalho de desenvolvimento do Azure no instalador do Visual Studio](using-signalr-performance-counters-in-an-azure-web-role/_static/azure-development-workload.png)

4. No **novo serviço de nuvem do Microsoft Azure** caixa de diálogo, selecione **função Web ASP.NET** e selecione o > botão para adicionar a função ao projeto. Selecione **OK**.

   ![Adicionar a função Web do ASP.NET](using-signalr-performance-counters-in-an-azure-web-role/_static/image2.png)

5. No **novo aplicativo de Web do ASP.NET - WebRole1** caixa de diálogo, selecione o **MVC** modelo e, em seguida, selecione **Okey**.

   ![Adicionar a API do MVC e Web](using-signalr-performance-counters-in-an-azure-web-role/_static/image3.png)

6. Na **Gerenciador de soluções**, abra o *Diagnostics. wadcfgx* arquivo sob **WebRole1**.

   ![Diagnostics. wadcfgx do Solution Explorer](using-signalr-performance-counters-in-an-azure-web-role/_static/image4.png)

7. Substitua o conteúdo do arquivo com a configuração a seguir e salve o arquivo:

   [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample1.xml)]

8. Abra o **Package Manager Console** de **ferramentas** > **NuGet Package Manager**. Insira os seguintes comandos para instalar a versão mais recente do SignalR e o pacote de utilitários do SignalR:

   [!code-powershell[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample2.ps1)]

9. Configure o aplicativo para instalar os contadores de desempenho do SignalR para a instância de função quando ele for inicializado ou se reciclar. No **Gerenciador de soluções**, clique com botão direito no **WebRole1** do projeto e selecione **Add** > **nova pasta**. Nomeie a nova pasta *inicialização*.

   ![Adicionar pasta de inicialização](using-signalr-performance-counters-in-an-azure-web-role/_static/image5.png)

10. Cópia de *signalr.exe* arquivo (adicionada com o **Microsoft.AspNet.SignalR.Utils** pacote) do \<pasta do projeto > / SignalRPerfCounters/packages/Microsoft.AspNet.SignalR.Utils.\< versão > / tools para o *inicialização* pasta que você criou na etapa anterior.

11. No **Gerenciador de soluções**, clique com botão direito do *inicialização* pasta e selecione **Add** > **Item existente**. Na caixa de diálogo que aparece, selecione *signalr.exe* e selecione **Add**.

    ![Adicionar signalr.exe ao projeto](using-signalr-performance-counters-in-an-azure-web-role/_static/image6.png)

12. Clique com botão direito no *inicialização* pasta que você criou. Selecione **Adicionar** > **Novo Item**. Selecione o **gerais** nó, selecione **arquivo de texto**e nomeie o novo item *SignalRPerfCounterInstall.cmd*. Esse arquivo de comando instalará os contadores de desempenho do SignalR para a função da web.

    ![Criar o arquivo de lote de instalação do contador de desempenho de SignalR](using-signalr-performance-counters-in-an-azure-web-role/_static/image7.png)

13. Quando o Visual Studio cria o *SignalRPerfCounterInstall.cmd* arquivo, ele será aberto automaticamente na janela principal. Substitua o conteúdo do arquivo de script a seguir, em seguida, salve e feche o arquivo. Esse script é executado *signalr.exe*, que adiciona os contadores de desempenho do SignalR para a instância de função.

    [!code-console[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample3.cmd)]

14. Selecione o *signalr.exe* arquivo no **Gerenciador de soluções**. No arquivo de **propriedades**, defina **Copy to Output Directory** para **copiar sempre**.

    ![Defina a cópia do diretório de saída para copiar sempre](using-signalr-performance-counters-in-an-azure-web-role/_static/image8.png)

15. Repita a etapa anterior para o *SignalRPerfCounterInstall.cmd* arquivo.


16. Clique com botão direito no *SignalRPerfCounterInstall.cmd* do arquivo e selecione **abrir com**. Na caixa de diálogo que aparece, selecione **Editor binário** e selecione **Okey**.

    ![Abrir com Editor binário](using-signalr-performance-counters-in-an-azure-web-role/_static/image9.png)

17. No editor binário, selecione qualquer bytes à esquerda no arquivo e excluí-los. Salve e feche o arquivo.

    ![Excluir bytes à esquerda](using-signalr-performance-counters-in-an-azure-web-role/_static/image10.png)

18. Abra *servicedefinition. Csdef* e adicione uma tarefa de inicialização que executa o *SignalrPerfCounterInstall.cmd* arquivo quando o serviço é iniciado:

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample4.xml?highlight=4-7)]

19. Abra `Views/Shared/_Layout.cshtml` e remover o script de pacote jQuery do final do arquivo.

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample5.cshtml)]

20. Adicionar um cliente JavaScript que chama continuamente o `increment` método no servidor. Abra `Views/Home/Index.cshtml` e substitua o conteúdo pelo seguinte código:

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample6.cshtml)]

21. Crie uma nova pasta na **WebRole1** projeto chamado *Hubs*. Clique com botão direito a *Hubs* pasta no **Gerenciador de soluções** e selecione **Add** > **Novo Item**. No **Adicionar Novo Item** caixa de diálogo, selecione o **Web** > **SignalR** categoria e, em seguida, selecione o **classe de Hub do SignalR (v2)** modelo de item. Nomeie o novo hub *MyHub.cs* e selecione **Add**.

    ![Adicionar classe de Hub do SignalR para a pasta Hubs na caixa de diálogo Adicionar Novo Item](using-signalr-performance-counters-in-an-azure-web-role/_static/image13.png)

22. *MyHub.cs* será aberto automaticamente na janela principal. Substitua o conteúdo pelo código a seguir, em seguida, salve e feche o arquivo:

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample7.cs)]

23. *[Crank.exe](signalr-connection-density-testing-with-crank.md)*  é uma ferramenta fornecida com a base de código do SignalR de testes de densidade de conexão. Como manivela requer uma conexão persistente, adicioná-lo seu site para uso durante o teste. Adicionar uma nova pasta para o **WebRole1** projeto chamado *PersistentConnections*. Essa pasta com o botão direito e selecione **Add** > **classe**. Nomeie o novo arquivo de classe *MyPersistentConnections.cs* e selecione **Add**.

24. Visual Studio abrirá o *MyPersistentConnections.cs* arquivo na janela principal. Substitua o conteúdo pelo código a seguir, em seguida, salve e feche o arquivo:

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample8.cs)]

25. Usando o `Startup` classe, os objetos de SignalR iniciado na inicialização do OWIN para cima. Abra ou crie *Startup.cs* e substitua o conteúdo pelo seguinte código:

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample9.cs)]

    No código acima, o `OwinStartup` atributo marca essa classe para iniciar o OWIN. O `Configuration` método inicia o SignalR.

26. Teste seu aplicativo no emulador do Microsoft Azure pressionando **F5**.

    > [!NOTE]
    > Se você encontrar um **FileLoadException** na **MapSignalR**, altere os redirecionamentos de associação no *Web. config* ao seguinte:

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample12.xml?highlight=3,7)]

27. Aguarde aproximadamente um minuto. Abra a janela da ferramenta Gerenciador de nuvem no Visual Studio (**modo de exibição** > **Cloud Explorer**) e expanda o caminho `(Local)/Storage Accounts/(Development)/Tables`. Clique duas vezes em **WADPerformanceCountersTable**. Você deve ver os contadores do SignalR nos dados de tabela. Se você não vir a tabela, talvez você precise inserir novamente suas credenciais de armazenamento do Azure. Talvez você precise selecionar o **atualize** botão para ver a tabela na **Cloud Explorer** ou selecione o **atualizar** botão na janela Abrir tabela para ver os dados na tabela.

    ![Selecionar a tabela de contadores de desempenho do WAD no Visual Studio Cloud Explorer](using-signalr-performance-counters-in-an-azure-web-role/_static/image11.png)

    ![Mostrar os contadores coletados na tabela de contadores de desempenho do WAD](using-signalr-performance-counters-in-an-azure-web-role/_static/image12.png)

28. Para testar seu aplicativo na nuvem, atualize o **ServiceConfiguration** do arquivo e defina o `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` para uma cadeia de conexão de conta de armazenamento do Azure válida.

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample10.xml)]

29. Implante o aplicativo em sua assinatura do Azure. Para obter detalhes sobre como implantar um aplicativo no Azure, consulte [como criar e implantar um serviço de nuvem](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy).

30. Aguarde alguns minutos. Na **Cloud Explorer**, localize a conta de armazenamento configurado anteriormente e localizar o `WADPerformanceCountersTable` tabela nele. Você deve ver os contadores do SignalR nos dados de tabela. Se você não vir a tabela, talvez você precise inserir novamente suas credenciais de armazenamento do Azure. Talvez você precise selecionar o **atualize** botão para ver a tabela na **Cloud Explorer** ou selecione o **atualizar** botão na janela Abrir tabela para ver os dados na tabela.

Agradecimentos especiais a [Martin Richard](https://social.msdn.microsoft.com/profile/Martin+Richard) para o conteúdo original usado neste tutorial.
