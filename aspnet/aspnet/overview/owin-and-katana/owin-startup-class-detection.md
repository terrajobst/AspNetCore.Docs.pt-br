---
uid: aspnet/overview/owin-and-katana/owin-startup-class-detection
title: Detecção de classe de inicialização OWIN | Microsoft Docs
author: Praburaj
description: Este tutorial mostra como configurar qual classe de inicialização OWIN é carregado. Para obter mais informações sobre o OWIN, consulte uma visão geral do projeto Katana. Este tutorial foi...
ms.author: riande
ms.date: 01/28/2019
ms.assetid: 08257f55-36f4-4e39-9c88-2a5602838c79
msc.legacyurl: /aspnet/overview/owin-and-katana/owin-startup-class-detection
msc.type: authoredcontent
ms.openlocfilehash: 0b34cca8b48383dbb028106651758dff889ed614
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667291"
---
<a name="owin-startup-class-detection"></a><span data-ttu-id="440d6-105">Detecção de classe de inicialização OWIN</span><span class="sxs-lookup"><span data-stu-id="440d6-105">OWIN Startup Class Detection</span></span>
====================

> <span data-ttu-id="440d6-106">Este tutorial mostra como configurar qual classe de inicialização OWIN é carregado.</span><span class="sxs-lookup"><span data-stu-id="440d6-106">This tutorial shows how to configure which OWIN startup class is loaded.</span></span> <span data-ttu-id="440d6-107">Para obter mais informações sobre o OWIN, consulte [uma visão geral do projeto Katana](an-overview-of-project-katana.md).</span><span class="sxs-lookup"><span data-stu-id="440d6-107">For more information on OWIN, see [An Overview of Project Katana](an-overview-of-project-katana.md).</span></span> <span data-ttu-id="440d6-108">Este tutorial foi escrito por Rick Anderson ( [ @RickAndMSFT ](https://twitter.com/#!/RickAndMSFT) ), Howard Dierking e Praburaj Thiagarajan ( [ @howard \_dierking](https://twitter.com/howard_dierking) ).</span><span class="sxs-lookup"><span data-stu-id="440d6-108">This tutorial was written by Rick Anderson ( [@RickAndMSFT](https://twitter.com/#!/RickAndMSFT) ), Praburaj Thiagarajan, and Howard Dierking ( [@howard\_dierking](https://twitter.com/howard_dierking) ).</span></span>
>
> ## <a name="prerequisites"></a><span data-ttu-id="440d6-109">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="440d6-109">Prerequisites</span></span>
>
> [<span data-ttu-id="440d6-110">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="440d6-110">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/)


## <a name="owin-startup-class-detection"></a><span data-ttu-id="440d6-111">Detecção de classe de inicialização OWIN</span><span class="sxs-lookup"><span data-stu-id="440d6-111">OWIN Startup Class Detection</span></span>

 <span data-ttu-id="440d6-112">Todos os aplicativos OWIN tem uma classe de inicialização na qual você pode especificar componentes para o pipeline do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="440d6-112">Every OWIN Application has a startup class where you specify components for the application pipeline.</span></span> <span data-ttu-id="440d6-113">Você pode se conectar a sua classe de inicialização com o tempo de execução de maneiras diferentes, dependendo do modelo de hospedagem escolhida (OwinHost, IIS e IIS Express).</span><span class="sxs-lookup"><span data-stu-id="440d6-113">There are different ways you can connect your startup class with the runtime, depending on the hosting model you choose (OwinHost, IIS, and IIS-Express).</span></span> <span data-ttu-id="440d6-114">A classe de inicialização mostrada neste tutorial pode ser usada em cada aplicativo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="440d6-114">The startup class shown in this tutorial can be used in every hosting application.</span></span> <span data-ttu-id="440d6-115">Você se conectar a classe de inicialização com o tempo de execução hospedagem usando uma dessas abordagens:</span><span class="sxs-lookup"><span data-stu-id="440d6-115">You connect the startup class with the hosting runtime using one of the these approaches:</span></span>

1. <span data-ttu-id="440d6-116">**Convenção de nomenclatura**: Katana procura por uma classe chamada `Startup` no namespace, o nome do assembly ou namespace global correspondentes.</span><span class="sxs-lookup"><span data-stu-id="440d6-116">**Naming Convention**: Katana looks for a class named `Startup` in namespace matching the assembly name or the global namespace.</span></span>
2. <span data-ttu-id="440d6-117">**Atributo OwinStartup**: Essa é a abordagem que a maioria dos desenvolvedores levará para especificar a classe de inicialização.</span><span class="sxs-lookup"><span data-stu-id="440d6-117">**OwinStartup Attribute**: This is the approach most developers will take to specify the startup class.</span></span> <span data-ttu-id="440d6-118">O seguinte atributo definirá a classe de inicialização o `TestStartup` classe o `StartupDemo` namespace.</span><span class="sxs-lookup"><span data-stu-id="440d6-118">The following attribute will set the startup class to the `TestStartup` class in the `StartupDemo` namespace.</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample1.cs)]

   <span data-ttu-id="440d6-119">O `OwinStartup` atributo substitui a convenção de nomenclatura.</span><span class="sxs-lookup"><span data-stu-id="440d6-119">The `OwinStartup` attribute overrides the naming convention.</span></span> <span data-ttu-id="440d6-120">Você também pode especificar um nome amigável com esse atributo, no entanto, usar um nome amigável requer a também usar o `appSetting` elemento no arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="440d6-120">You can also specify a friendly name with this attribute, however, using a friendly name requires you to also use the `appSetting` element in the configuration file.</span></span>
3. <span data-ttu-id="440d6-121">**O elemento de appSetting no arquivo de configuração**: O `appSetting` substitui o `OwinStartup` atributo e a convenção de nomenclatura.</span><span class="sxs-lookup"><span data-stu-id="440d6-121">**The appSetting element in the Configuration file**: The `appSetting` element overrides the `OwinStartup` attribute and naming convention.</span></span> <span data-ttu-id="440d6-122">Você pode ter várias classes de inicialização (cada um usando um `OwinStartup` atributo) e configurar qual classe de inicialização será carregado em um arquivo de configuração usando marcação semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="440d6-122">You can have multiple startup classes (each using an `OwinStartup` attribute) and configure which startup class will be loaded in a configuration file using markup similar to the following:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample2.xml)]

   <span data-ttu-id="440d6-123">A chave a seguir, que especifica explicitamente a classe de inicialização e o assembly também pode ser usada:</span><span class="sxs-lookup"><span data-stu-id="440d6-123">The following key, which explicitly specifies the startup class and assembly can also be used:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample3.xml)]

   <span data-ttu-id="440d6-124">O XML no arquivo de configuração a seguir especifica um nome de classe de inicialização amigável de `ProductionConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="440d6-124">The following XML in the configuration file specifies a friendly startup class name of `ProductionConfiguration`.</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample4.xml)]

   <span data-ttu-id="440d6-125">A marcação acima deve ser usada com o seguinte `OwinStartup` atributo que especifica um nome amigável e faz com que o `ProductionStartup2` classe a ser executada.</span><span class="sxs-lookup"><span data-stu-id="440d6-125">The above markup must be used with the following `OwinStartup` attribute which specifies a friendly name and causes the `ProductionStartup2` class to run.</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample5.cs?highlight=1,16)]
4. <span data-ttu-id="440d6-126">Para desabilitar a descoberta de inicialização OWIN, adicione a `appSetting owin:AutomaticAppStartup` com um valor de `"false"` no arquivo Web. config.</span><span class="sxs-lookup"><span data-stu-id="440d6-126">To disable OWIN startup discovery add the `appSetting owin:AutomaticAppStartup` with a value of `"false"` in the web.config file.</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample6.xml)]

## <a name="create-an-aspnet-web-app-using-owin-startup"></a><span data-ttu-id="440d6-127">Criar um aplicativo Web do ASP.NET usando a inicialização do OWIN</span><span class="sxs-lookup"><span data-stu-id="440d6-127">Create an ASP.NET Web App using OWIN Startup</span></span>

1. <span data-ttu-id="440d6-128">Crie um aplicativo de web Asp.Net vazio e denomine **StartupDemo**.</span><span class="sxs-lookup"><span data-stu-id="440d6-128">Create an empty Asp.Net web application and name it **StartupDemo**.</span></span> <span data-ttu-id="440d6-129">-Instalar `Microsoft.Owin.Host.SystemWeb` usando o Gerenciador de pacotes do NuGet.</span><span class="sxs-lookup"><span data-stu-id="440d6-129">- Install `Microsoft.Owin.Host.SystemWeb` using the NuGet package manager.</span></span> <span data-ttu-id="440d6-130">Do **ferramentas** menu, selecione **Gerenciador de pacotes NuGet**e então **Package Manager Console**.</span><span class="sxs-lookup"><span data-stu-id="440d6-130">From the **Tools** menu, select **NuGet Package Manager**, and then **Package Manager Console**.</span></span> <span data-ttu-id="440d6-131">Insira o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="440d6-131">Enter the following command:</span></span>

    [!code-powershell[Main](owin-startup-class-detection/samples/sample7.ps1)]
2. <span data-ttu-id="440d6-132">Adicione uma classe de inicialização do OWIN.</span><span class="sxs-lookup"><span data-stu-id="440d6-132">Add an OWIN startup class.</span></span> <span data-ttu-id="440d6-133">No Visual Studio 2017 com o botão direito no projeto e selecione **Add Class**. - na **Adicionar Novo Item** caixa de diálogo, digite *OWIN* no campo de pesquisa e altere o nome para o Startup.cs, e, em seguida, selecione **adicionar**.</span><span class="sxs-lookup"><span data-stu-id="440d6-133">In Visual Studio 2017 right-click the project and select **Add Class**.- In the **Add New Item** dialog box, enter *OWIN* in the search field, and change the name to Startup.cs, and then select **Add**.</span></span>

     ![](owin-startup-class-detection/_static/image1.png)

   <span data-ttu-id="440d6-134">Na próxima vez em que você deseja adicionar um *classe de inicialização Owin*, ele estará em disponível na **Add** menu.</span><span class="sxs-lookup"><span data-stu-id="440d6-134">The next time you want to add an *Owin Startup class*, it will be in available from the **Add** menu.</span></span>

     ![](owin-startup-class-detection/_static/image2.png)

   <span data-ttu-id="440d6-135">Como alternativa, é possível com o botão direito no projeto e selecionar **Add**, em seguida, selecione **Novo Item**e, em seguida, selecione o **classe de inicialização Owin**.</span><span class="sxs-lookup"><span data-stu-id="440d6-135">Alternatively, you can right-click the project and select **Add**, then select **New Item**, and then select the **Owin Startup class**.</span></span>

     ![](owin-startup-class-detection/_static/image3.png)

- <span data-ttu-id="440d6-136">Substitua o código gerado na *Startup.cs* arquivo com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="440d6-136">Replace the generated code in the *Startup.cs* file with the following:</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample8.cs?highlight=5,7,15-28,31-34)]

  <span data-ttu-id="440d6-137">O `app.Use` expressão lambda é usado para registrar o componente especificado de middleware ao pipeline do OWIN.</span><span class="sxs-lookup"><span data-stu-id="440d6-137">The `app.Use` lambda expression is used to register the specified middleware component to the OWIN pipeline.</span></span> <span data-ttu-id="440d6-138">Nesse caso, estamos configurando registro em log de solicitações de entrada antes de responder à solicitação de entrada.</span><span class="sxs-lookup"><span data-stu-id="440d6-138">In this case we are setting up logging of incoming requests before responding to the incoming request.</span></span> <span data-ttu-id="440d6-139">O `next` parâmetro é o delegado ( [Func](https://msdn.microsoft.com/library/bb534960(v=vs.100).aspx) &lt; [tarefa](https://msdn.microsoft.com/library/dd321424(v=vs.100).aspx) &gt; ) para o próximo componente no pipeline.</span><span class="sxs-lookup"><span data-stu-id="440d6-139">The `next` parameter is the delegate ( [Func](https://msdn.microsoft.com/library/bb534960(v=vs.100).aspx) &lt; [Task](https://msdn.microsoft.com/library/dd321424(v=vs.100).aspx) &gt; ) to the next component in the pipeline.</span></span> <span data-ttu-id="440d6-140">O `app.Run` expressão lambda conecta-se a solicitações de entrada do pipeline e fornece o mecanismo de resposta.</span><span class="sxs-lookup"><span data-stu-id="440d6-140">The `app.Run` lambda expression hooks up the pipeline to incoming requests and provides the response mechanism.</span></span>
     > [!NOTE]
     > <span data-ttu-id="440d6-141">No código acima é ter comentado a `OwinStartup` atributo e nós depende da convenção de executar a classe denominada `Startup` .-pressione ***F5*** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="440d6-141">In the code above we have commented out the `OwinStartup` attribute and we're relying on the convention of running the class named `Startup` .- Press ***F5*** to run the application.</span></span> <span data-ttu-id="440d6-142">Clique em atualizar algumas vezes.</span><span class="sxs-lookup"><span data-stu-id="440d6-142">Hit refresh a few times.</span></span>

    <span data-ttu-id="440d6-143">![](owin-startup-class-detection/_static/image4.png) Observação: O número mostrado nas imagens neste tutorial não corresponderá o número que você vê.</span><span class="sxs-lookup"><span data-stu-id="440d6-143">![](owin-startup-class-detection/_static/image4.png) Note: The number shown in the images in this tutorial will not match the number you see.</span></span> <span data-ttu-id="440d6-144">A cadeia de caracteres de milissegundo é usada para mostrar uma nova resposta quando a página for atualizada.</span><span class="sxs-lookup"><span data-stu-id="440d6-144">The millisecond string is used to show a new response when you refresh the page.</span></span>
  <span data-ttu-id="440d6-145">Você pode ver as informações de rastreamento a **saída** janela.</span><span class="sxs-lookup"><span data-stu-id="440d6-145">You can see the trace information in the **Output** window.</span></span>

    ![](owin-startup-class-detection/_static/image5.png)

## <a name="add-more-startup-classes"></a><span data-ttu-id="440d6-146">Adicionar mais Classes de inicialização</span><span class="sxs-lookup"><span data-stu-id="440d6-146">Add More Startup Classes</span></span>

<span data-ttu-id="440d6-147">Nesta seção vamos adicionar outra classe de inicialização.</span><span class="sxs-lookup"><span data-stu-id="440d6-147">In this section we'll add another Startup class.</span></span> <span data-ttu-id="440d6-148">Você pode adicionar vários classe de inicialização OWIN para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="440d6-148">You can add multiple OWIN startup class to your application.</span></span> <span data-ttu-id="440d6-149">Por exemplo, você talvez queira criar classes de inicialização para o desenvolvimento, teste e produção.</span><span class="sxs-lookup"><span data-stu-id="440d6-149">For example, you might want to create startup classes for development, testing and production.</span></span>

1. <span data-ttu-id="440d6-150">Crie uma nova classe de inicialização OWIN e nomeie- `ProductionStartup`.</span><span class="sxs-lookup"><span data-stu-id="440d6-150">Create a new OWIN Startup class and name it `ProductionStartup`.</span></span>
2. <span data-ttu-id="440d6-151">Substitua o código gerado pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="440d6-151">Replace the generated code with the following:</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample9.cs?highlight=14-18)]
3. <span data-ttu-id="440d6-152">Pressione F5 de controle para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="440d6-152">Press Control F5 to run the app.</span></span> <span data-ttu-id="440d6-153">O `OwinStartup` atributo especifica a classe de inicialização de produção é executada.</span><span class="sxs-lookup"><span data-stu-id="440d6-153">The `OwinStartup` attribute specifies the production startup class is run.</span></span>

    ![](owin-startup-class-detection/_static/image6.png)
4. <span data-ttu-id="440d6-154">Crie outra classe de inicialização OWIN e nomeie- `TestStartup`.</span><span class="sxs-lookup"><span data-stu-id="440d6-154">Create another OWIN Startup class and name it `TestStartup`.</span></span>
5. <span data-ttu-id="440d6-155">Substitua o código gerado pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="440d6-155">Replace the generated code with the following:</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample10.cs?highlight=6,14-18)]

   <span data-ttu-id="440d6-156">O `OwinStartup` sobrecarga do atributo acima especifica `TestingConfiguration` como o *amigável* nome da classe Startup.</span><span class="sxs-lookup"><span data-stu-id="440d6-156">The `OwinStartup` attribute overload above specifies `TestingConfiguration` as the *friendly* name of the Startup class.</span></span>
6. <span data-ttu-id="440d6-157">Abra o *Web. config* arquivo e adicione a chave de inicialização do aplicativo OWIN que especifica o nome amigável da classe de inicialização:</span><span class="sxs-lookup"><span data-stu-id="440d6-157">Open the *web.config* file and add the OWIN App startup key which specifies the friendly name of the Startup class:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample11.xml?highlight=3-5)]
7. <span data-ttu-id="440d6-158">Pressione F5 de controle para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="440d6-158">Press Control F5 to run the app.</span></span> <span data-ttu-id="440d6-159">O elemento de configurações do aplicativo leva precedentes e o teste de configuração é executada.</span><span class="sxs-lookup"><span data-stu-id="440d6-159">The app settings element takes precedent, and the test configuration is run.</span></span>

    ![](owin-startup-class-detection/_static/image7.png)
8. <span data-ttu-id="440d6-160">Remover o *amigável* nome da `OwinStartup` de atributo no `TestStartup` classe.</span><span class="sxs-lookup"><span data-stu-id="440d6-160">Remove the *friendly* name from the `OwinStartup` attribute in the `TestStartup` class.</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample12.cs)]
9. <span data-ttu-id="440d6-161">Substitua a chave de inicialização do aplicativo OWIN na *Web. config* arquivo com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="440d6-161">Replace the OWIN App startup key in the *web.config* file with the following:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample13.xml)]
10. <span data-ttu-id="440d6-162">Reverter o `OwinStartup` atributo em cada classe para o código do atributo padrão gerado pelo Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="440d6-162">Revert the `OwinStartup` attribute in each class to the default attribute code generated by Visual Studio:</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample14.cs)]

    <span data-ttu-id="440d6-163">Cada uma das chaves de inicialização do aplicativo OWIN abaixo fará com que a classe de produção ser executado.</span><span class="sxs-lookup"><span data-stu-id="440d6-163">Each of the OWIN App startup keys below will cause the production class to run.</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample15.xml)]

    <span data-ttu-id="440d6-164">A última chave de inicialização especifica o método de configuração de inicialização.</span><span class="sxs-lookup"><span data-stu-id="440d6-164">The last startup key specifies the startup configuration method.</span></span> <span data-ttu-id="440d6-165">A seguinte chave de inicialização do aplicativo OWIN permite que você altere o nome da classe de configuração para `MyConfiguration` .</span><span class="sxs-lookup"><span data-stu-id="440d6-165">The following OWIN App startup key allows you to change the name of the configuration class to `MyConfiguration` .</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample16.xml)]

## <a name="using-owinhostexe"></a><span data-ttu-id="440d6-166">Usando Owinhost.exe</span><span class="sxs-lookup"><span data-stu-id="440d6-166">Using Owinhost.exe</span></span>

1. <span data-ttu-id="440d6-167">Substitua o arquivo Web. config com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="440d6-167">Replace the Web.config file with the following markup:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample17.xml?highlight=3-6)]

   <span data-ttu-id="440d6-168">A última chave wins, portanto, neste caso, `TestStartup` for especificado.</span><span class="sxs-lookup"><span data-stu-id="440d6-168">The last key wins, so in this case `TestStartup` is specified.</span></span>
2. <span data-ttu-id="440d6-169">Instale o Owinhost do PMC:</span><span class="sxs-lookup"><span data-stu-id="440d6-169">Install Owinhost from the PMC:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample18.cmd)]
3. <span data-ttu-id="440d6-170">Navegue até a pasta de aplicativo (a pasta que contém o *Web. config* arquivo) e, em um prompt de comando e digite:</span><span class="sxs-lookup"><span data-stu-id="440d6-170">Navigate to the application folder (the folder containing the *Web.config* file) and in a command prompt and type:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample19.cmd)]

   <span data-ttu-id="440d6-171">A janela de comando mostrará:</span><span class="sxs-lookup"><span data-stu-id="440d6-171">The command window will show:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample20.cmd)]
4. <span data-ttu-id="440d6-172">Inicie um navegador com a URL `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="440d6-172">Launch a browser with the URL `http://localhost:5000/`.</span></span>

    ![](owin-startup-class-detection/_static/image8.png)

   <span data-ttu-id="440d6-173">OwinHost cumprido as convenções de inicialização listadas acima.</span><span class="sxs-lookup"><span data-stu-id="440d6-173">OwinHost honored the startup conventions listed above.</span></span>
5. <span data-ttu-id="440d6-174">Na janela de comando, pressione Enter para sair OwinHost.</span><span class="sxs-lookup"><span data-stu-id="440d6-174">In the command window, press Enter to exit OwinHost.</span></span>
6. <span data-ttu-id="440d6-175">No `ProductionStartup` da classe, adicione o seguinte atributo OwinStartup que especifica um nome amigável da *ProductionConfiguration*.</span><span class="sxs-lookup"><span data-stu-id="440d6-175">In the `ProductionStartup` class, add the following OwinStartup attribute which specifies a friendly name of *ProductionConfiguration*.</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample21.cs)]
7. <span data-ttu-id="440d6-176">No prompt de comando e digite:</span><span class="sxs-lookup"><span data-stu-id="440d6-176">In the command prompt and type:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample22.cmd)]

   <span data-ttu-id="440d6-177">A classe de inicialização de produção é carregada.</span><span class="sxs-lookup"><span data-stu-id="440d6-177">The Production startup class is loaded.</span></span>
    ![](owin-startup-class-detection/_static/image9.png)

   <span data-ttu-id="440d6-178">Nosso aplicativo tem várias classes de inicialização e, neste exemplo, podemos adiaram qual classe de inicialização para carregar até o tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="440d6-178">Our application has multiple startup classes, and in this example we have deferred which startup class to load until runtime.</span></span>
8. <span data-ttu-id="440d6-179">As seguintes opções de inicialização de tempo de execução de teste:</span><span class="sxs-lookup"><span data-stu-id="440d6-179">Test the following runtime startup options:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample23.cmd)]
