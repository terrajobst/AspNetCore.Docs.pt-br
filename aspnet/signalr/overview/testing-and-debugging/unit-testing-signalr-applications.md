---
uid: signalr/overview/testing-and-debugging/unit-testing-signalr-applications
title: Aplicativos do SignalR de teste de unidade | Microsoft Docs
author: pfletcher
description: Este artigo descreve como usar os recursos de teste de unidade do SignalR 2.0.
ms.author: riande
ms.date: 06/10/2014
ms.assetid: d1983524-e0d5-4ee6-9d87-1f552f7cb964
msc.legacyurl: /signalr/overview/testing-and-debugging/unit-testing-signalr-applications
msc.type: authoredcontent
ms.openlocfilehash: d8f3afdc2749173d1e260096ee6bd4bf1ae4c7cb
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287501"
---
<a name="unit-testing-signalr-applications"></a><span data-ttu-id="f54b0-103">Aplicativos do SignalR de testes de unidade</span><span class="sxs-lookup"><span data-stu-id="f54b0-103">Unit Testing SignalR Applications</span></span>
====================
<span data-ttu-id="f54b0-104">por [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="f54b0-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="f54b0-105">Este artigo descreve como usar os recursos de teste de unidade de mensagens do SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="f54b0-105">This article describes using the Unit Testing features of SignalR 2.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="f54b0-106">Versões de software usadas neste tópico</span><span class="sxs-lookup"><span data-stu-id="f54b0-106">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="f54b0-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="f54b0-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="f54b0-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="f54b0-108">.NET 4.5</span></span>
> - <span data-ttu-id="f54b0-109">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="f54b0-109">SignalR version 2</span></span>
>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="f54b0-110">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="f54b0-110">Questions and comments</span></span>
>
> <span data-ttu-id="f54b0-111">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="f54b0-111">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="f54b0-112">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="f54b0-112">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<a id="unit"></a>
## <a name="unit-testing-signalr-applications"></a><span data-ttu-id="f54b0-113">Aplicativos do SignalR de teste de unidade</span><span class="sxs-lookup"><span data-stu-id="f54b0-113">Unit testing SignalR applications</span></span>

<span data-ttu-id="f54b0-114">Você pode usar os recursos de teste de unidade no SignalR 2 para criar testes de unidade para seu aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="f54b0-114">You can use the unit test features in SignalR 2 to create unit tests for your SignalR application.</span></span> <span data-ttu-id="f54b0-115">O SignalR 2 inclui o [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) interface, que pode ser usado para criar um objeto fictício para simular os métodos de hub para teste.</span><span class="sxs-lookup"><span data-stu-id="f54b0-115">SignalR 2 includes the [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) interface, which can be used to create a mock object to simulate your hub methods for testing.</span></span>

<span data-ttu-id="f54b0-116">Nesta seção, você adicionará os testes de unidade para o aplicativo criado na [tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md) usando [XUnit.net](https://github.com/xunit/xunit) e [Moq](https://github.com/Moq/moq4).</span><span class="sxs-lookup"><span data-stu-id="f54b0-116">In this section, you'll add unit tests for the application created in the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md) using [XUnit.net](https://github.com/xunit/xunit) and [Moq](https://github.com/Moq/moq4).</span></span>

<span data-ttu-id="f54b0-117">XUnit.net será usado para controlar o teste; Moq será usado para criar uma [simular](http://en.wikipedia.org/wiki/Mock_object) objeto para teste.</span><span class="sxs-lookup"><span data-stu-id="f54b0-117">XUnit.net will be used to control the test; Moq will be used to create a [mock](http://en.wikipedia.org/wiki/Mock_object) object for testing.</span></span> <span data-ttu-id="f54b0-118">Outras estruturas de simulação podem ser usadas se desejado; [NSubstitute](http://nsubstitute.github.io/) também é uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="f54b0-118">Other mocking frameworks can be used if desired; [NSubstitute](http://nsubstitute.github.io/) is also a good choice.</span></span> <span data-ttu-id="f54b0-119">Este tutorial demonstra como configurar o objeto fictício de duas maneiras: Primeiro, usando um `dynamic` objeto (introduzida no .NET Framework 4) e, segundo, usando uma interface.</span><span class="sxs-lookup"><span data-stu-id="f54b0-119">This tutorial demonstrates how to set up the mock object in two ways: First, using a `dynamic` object (introduced in .NET Framework 4), and second, using an interface.</span></span>

### <a name="contents"></a><span data-ttu-id="f54b0-120">Conteúdo</span><span class="sxs-lookup"><span data-stu-id="f54b0-120">Contents</span></span>

<span data-ttu-id="f54b0-121">Este tutorial contém as seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="f54b0-121">This tutorial contains the following sections.</span></span>

- [<span data-ttu-id="f54b0-122">Testes de unidade com dinâmico</span><span class="sxs-lookup"><span data-stu-id="f54b0-122">Unit testing with Dynamic</span></span>](#dynamic)
- [<span data-ttu-id="f54b0-123">Testes por tipo de unidade</span><span class="sxs-lookup"><span data-stu-id="f54b0-123">Unit testing by type</span></span>](#type)

<a id="dynamic"></a>
### <a name="unit-testing-with-dynamic"></a><span data-ttu-id="f54b0-124">Testes de unidade com dinâmico</span><span class="sxs-lookup"><span data-stu-id="f54b0-124">Unit testing with Dynamic</span></span>

<span data-ttu-id="f54b0-125">Nesta seção, você adicionará um teste de unidade para o aplicativo criado na [tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md) usando um objeto dinâmico.</span><span class="sxs-lookup"><span data-stu-id="f54b0-125">In this section, you'll add a unit test for the application created in the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md) using a dynamic object.</span></span>

1. <span data-ttu-id="f54b0-126">Instalar o [extensão de executor de XUnit](https://visualstudiogallery.msdn.microsoft.com/463c5987-f82b-46c8-a97e-b1cde42b9099) para Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="f54b0-126">Install the [XUnit Runner extension](https://visualstudiogallery.msdn.microsoft.com/463c5987-f82b-46c8-a97e-b1cde42b9099) for Visual Studio 2013.</span></span>
2. <span data-ttu-id="f54b0-127">Completar a [tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md), ou baixe o aplicativo concluído no [MSDN Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).</span><span class="sxs-lookup"><span data-stu-id="f54b0-127">Either complete the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md), or download the completed application from [MSDN Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).</span></span>
3. <span data-ttu-id="f54b0-128">Se você estiver usando a versão de download do aplicativo de Introdução, abra **Package Manager Console** e clique em **restaurar** para adicionar o pacote de SignalR ao projeto.</span><span class="sxs-lookup"><span data-stu-id="f54b0-128">If you are using the download version of the Getting Started application, open **Package Manager Console** and click **Restore** to add the SignalR package to the project.</span></span>

    ![Restaurar pacotes](unit-testing-signalr-applications/_static/image1.png)
4. <span data-ttu-id="f54b0-130">Adicione um projeto à solução para o teste de unidade.</span><span class="sxs-lookup"><span data-stu-id="f54b0-130">Add a project to the solution for the unit test.</span></span> <span data-ttu-id="f54b0-131">Sua solução no botão direito do mouse **Gerenciador de soluções** e selecione **Add**, **novo projeto...** . Sob o **c#** nó, selecione o **Windows** nó.</span><span class="sxs-lookup"><span data-stu-id="f54b0-131">Right-click your solution in **Solution Explorer** and select **Add**, **New Project...**. Under the **C#** node, select the **Windows** node.</span></span> <span data-ttu-id="f54b0-132">Selecione **biblioteca de classes**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-132">Select **Class Library**.</span></span> <span data-ttu-id="f54b0-133">Nomeie o novo projeto **TestLibrary** e clique em **Okey**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-133">Name the new project **TestLibrary** and click **OK**.</span></span>

    ![Criar a biblioteca de teste](unit-testing-signalr-applications/_static/image2.png)
5. <span data-ttu-id="f54b0-135">Adicione uma referência no projeto de biblioteca de teste ao projeto SignalRChat.</span><span class="sxs-lookup"><span data-stu-id="f54b0-135">Add a reference in the test library project to the SignalRChat project.</span></span> <span data-ttu-id="f54b0-136">Clique com botão direito do **TestLibrary** do projeto e selecione **Add**, **referência...** . Selecione o **projetos** nó sob o **solução** nó e verificação **SignalRChat**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-136">Right-click the **TestLibrary** project and select **Add**, **Reference...**. Select the **Projects** node under the **Solution** node, and check **SignalRChat**.</span></span> <span data-ttu-id="f54b0-137">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-137">Click **OK**.</span></span>

    ![Adicionar referência de projeto](unit-testing-signalr-applications/_static/image3.png)
6. <span data-ttu-id="f54b0-139">Adicione os pacotes SignalR, Moq e XUnit para o **TestLibrary** projeto.</span><span class="sxs-lookup"><span data-stu-id="f54b0-139">Add the SignalR, Moq, and XUnit packages to the **TestLibrary** project.</span></span> <span data-ttu-id="f54b0-140">No **Package Manager Console**, defina as **projeto padrão** na lista suspensa para **TestLibrary**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-140">In the **Package Manager Console**, set the **Default Project** dropdown to **TestLibrary**.</span></span> <span data-ttu-id="f54b0-141">Execute os comandos a seguir na janela do console:</span><span class="sxs-lookup"><span data-stu-id="f54b0-141">Run the following commands in the console window:</span></span>

   - `Install-Package Microsoft.AspNet.SignalR`
   - `Install-Package Moq`
   - `Install-Package XUnit`

     ![Instalar pacotes](unit-testing-signalr-applications/_static/image4.png)
7. <span data-ttu-id="f54b0-143">Crie o arquivo de teste.</span><span class="sxs-lookup"><span data-stu-id="f54b0-143">Create the test file.</span></span> <span data-ttu-id="f54b0-144">Clique com botão direito do **TestLibrary** do projeto e clique em **adicionar...** , **Classe**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-144">Right-click the **TestLibrary** project and click **Add...**, **Class**.</span></span> <span data-ttu-id="f54b0-145">Nomeie a nova classe **Tests.cs**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-145">Name the new class **Tests.cs**.</span></span>
8. <span data-ttu-id="f54b0-146">Substitua o conteúdo de Tests.cs com o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="f54b0-146">Replace the contents of Tests.cs with the following code.</span></span>

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample1.cs)]

    <span data-ttu-id="f54b0-147">No código acima, um cliente de teste é criado usando o `Mock` do objeto do [Moq](https://github.com/Moq/moq4) biblioteca de tipo [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (no SignalR 2.1, atribua `dynamic` para o tipo parâmetro). O `IHubCallerConnectionContext` interface é o objeto de proxy com o qual você invocar métodos no cliente.</span><span class="sxs-lookup"><span data-stu-id="f54b0-147">In the code above, a test client is created using the `Mock` object from the [Moq](https://github.com/Moq/moq4) library, of type [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (in SignalR 2.1, assign `dynamic` for the type parameter.) The `IHubCallerConnectionContext` interface is the proxy object with which you invoke methods on the client.</span></span> <span data-ttu-id="f54b0-148">O `broadcastMessage` função, em seguida, é definida para o cliente fictício para que ela pode ser chamada pelo `ChatHub` classe.</span><span class="sxs-lookup"><span data-stu-id="f54b0-148">The `broadcastMessage` function is then defined for the mock client so that it can be called by the `ChatHub` class.</span></span> <span data-ttu-id="f54b0-149">O mecanismo de teste, em seguida, chama o `Send` método da `ChatHub` classe, que por sua vez chama o fictícia `broadcastMessage` função.</span><span class="sxs-lookup"><span data-stu-id="f54b0-149">The test engine then calls the `Send` method of the `ChatHub` class, which in turn calls the mocked `broadcastMessage` function.</span></span>
9. <span data-ttu-id="f54b0-150">Compile a solução pressionando **F6**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-150">Build the solution by pressing **F6**.</span></span>
10. <span data-ttu-id="f54b0-151">Execute o teste de unidade.</span><span class="sxs-lookup"><span data-stu-id="f54b0-151">Run the unit test.</span></span> <span data-ttu-id="f54b0-152">No Visual Studio, selecione **teste**, **Windows**, **Gerenciador de testes**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-152">In Visual Studio, select **Test**, **Windows**, **Test Explorer**.</span></span> <span data-ttu-id="f54b0-153">Na janela Test Explorer, clique com botão direito **HubsAreMockableViaDynamic** e selecione **executar testes selecionados**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-153">In the Test Explorer window, right-click **HubsAreMockableViaDynamic** and select **Run Selected Tests**.</span></span>

    ![Gerenciador de Testes](unit-testing-signalr-applications/_static/image5.png)
11. <span data-ttu-id="f54b0-155">Verifique se que o teste foi aprovado, verificando o painel inferior na janela do Gerenciador de testes.</span><span class="sxs-lookup"><span data-stu-id="f54b0-155">Verify that the test passed by checking the lower pane in the Test Explorer window.</span></span> <span data-ttu-id="f54b0-156">A janela mostrará que o teste foi aprovado.</span><span class="sxs-lookup"><span data-stu-id="f54b0-156">The window will show that the test passed.</span></span>

    ![Teste aprovado](unit-testing-signalr-applications/_static/image6.png)

<a id="type"></a>
### <a name="unit-testing-by-type"></a><span data-ttu-id="f54b0-158">Testes por tipo de unidade</span><span class="sxs-lookup"><span data-stu-id="f54b0-158">Unit testing by type</span></span>

<span data-ttu-id="f54b0-159">Nesta seção, você adicionará um teste para o aplicativo criado na [tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md) usando uma interface que contém o método a ser testado.</span><span class="sxs-lookup"><span data-stu-id="f54b0-159">In this section, you'll add a test for the application created in the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md) using an interface that contains the method to be tested.</span></span>

1. <span data-ttu-id="f54b0-160">Conclua as etapas 1 a 7 na [testes de unidade com dinâmico](#dynamic) tutorial acima.</span><span class="sxs-lookup"><span data-stu-id="f54b0-160">Complete steps 1-7 in the [Unit testing with Dynamic](#dynamic) tutorial above.</span></span>
2. <span data-ttu-id="f54b0-161">Substitua o conteúdo de Tests.cs com o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="f54b0-161">Replace the contents of Tests.cs with the following code.</span></span>

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample2.cs)]

    <span data-ttu-id="f54b0-162">No código acima, uma interface é criada definindo a assinatura do `broadcastMessage` método para o qual o mecanismo de teste irá criar um cliente fictício.</span><span class="sxs-lookup"><span data-stu-id="f54b0-162">In the code above, an interface is created defining the signature of the `broadcastMessage` method for which the test engine will create a mock client.</span></span> <span data-ttu-id="f54b0-163">Um cliente fictício, em seguida, é criado usando o `Mock` objeto do tipo [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (no SignalR 2.1, atribua `dynamic` para o parâmetro de tipo.) O `IHubCallerConnectionContext` interface é o objeto de proxy com o qual você invocar métodos no cliente.</span><span class="sxs-lookup"><span data-stu-id="f54b0-163">A mock client is then created using the `Mock` object, of type [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (in SignalR 2.1, assign `dynamic` for the type parameter.) The `IHubCallerConnectionContext` interface is the proxy object with which you invoke methods on the client.</span></span>

    <span data-ttu-id="f54b0-164">O teste, em seguida, cria uma instância de `ChatHub`e, em seguida, cria uma versão fictícia do `broadcastMessage` método, que por sua vez é invocado chamando o `Send` método no hub.</span><span class="sxs-lookup"><span data-stu-id="f54b0-164">The test then creates an instance of `ChatHub`, and then creates a mock version of the `broadcastMessage` method, which in turn is invoked by calling the `Send` method on the hub.</span></span>
3. <span data-ttu-id="f54b0-165">Compile a solução pressionando **F6**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-165">Build the solution by pressing **F6**.</span></span>
4. <span data-ttu-id="f54b0-166">Execute o teste de unidade.</span><span class="sxs-lookup"><span data-stu-id="f54b0-166">Run the unit test.</span></span> <span data-ttu-id="f54b0-167">No Visual Studio, selecione **teste**, **Windows**, **Gerenciador de testes**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-167">In Visual Studio, select **Test**, **Windows**, **Test Explorer**.</span></span> <span data-ttu-id="f54b0-168">Na janela Test Explorer, clique com botão direito **HubsAreMockableViaDynamic** e selecione **executar testes selecionados**.</span><span class="sxs-lookup"><span data-stu-id="f54b0-168">In the Test Explorer window, right-click **HubsAreMockableViaDynamic** and select **Run Selected Tests**.</span></span>

    ![Gerenciador de Testes](unit-testing-signalr-applications/_static/image7.png)
5. <span data-ttu-id="f54b0-170">Verifique se que o teste foi aprovado, verificando o painel inferior na janela do Gerenciador de testes.</span><span class="sxs-lookup"><span data-stu-id="f54b0-170">Verify that the test passed by checking the lower pane in the Test Explorer window.</span></span> <span data-ttu-id="f54b0-171">A janela mostrará que o teste foi aprovado.</span><span class="sxs-lookup"><span data-stu-id="f54b0-171">The window will show that the test passed.</span></span>

    ![Teste aprovado](unit-testing-signalr-applications/_static/image8.png)
