---
uid: web-api/overview/security/enabling-cross-origin-requests-in-web-api
title: Permitindo solicitações entre origens na API Web ASP.NET 2 | Microsoft Docs
author: MikeWasson
description: Mostra como dar suporte a compartilhamento de recursos entre origens (CORS) na API Web ASP.NET.
ms.author: riande
ms.date: 01/29/2019
ms.assetid: 9b265a5a-6a70-4a82-adce-2d7c56ae8bdd
msc.legacyurl: /web-api/overview/security/enabling-cross-origin-requests-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 97a0027194b019b09e220493dcb593e682027fe3
ms.sourcegitcommit: d22b3c23c45a076c4f394a70b1c8df2fbcdf656d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55428441"
---
<a name="enable-cross-origin-requests-in-aspnet-web-api-2"></a><span data-ttu-id="46add-103">Habilitar solicitações entre origens na API Web ASP.NET 2</span><span class="sxs-lookup"><span data-stu-id="46add-103">Enable cross-origin requests in ASP.NET Web API 2</span></span>
====================
<span data-ttu-id="46add-104">por [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="46add-104">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

> <span data-ttu-id="46add-105">Segurança do navegador impede que uma página da web faça solicitações AJAX para outro domínio.</span><span class="sxs-lookup"><span data-stu-id="46add-105">Browser security prevents a web page from making AJAX requests to another domain.</span></span> <span data-ttu-id="46add-106">Essa restrição é chamada de *política de mesma origem*e impede que um site mal-intencionado lendo dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="46add-106">This restriction is called the *same-origin policy*, and prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="46add-107">No entanto, às vezes, talvez queira permitir que outros sites chamar sua API da web.</span><span class="sxs-lookup"><span data-stu-id="46add-107">However, sometimes you might want to let other sites call your web API.</span></span>
>
> <span data-ttu-id="46add-108">[Entre o compartilhamento de recursos de origem](http://www.w3.org/TR/cors/) (CORS) é um padrão W3C que permite que um servidor relaxar a política de mesma origem.</span><span class="sxs-lookup"><span data-stu-id="46add-108">[Cross Origin Resource Sharing](http://www.w3.org/TR/cors/) (CORS) is a W3C standard that allows a server to relax the same-origin policy.</span></span> <span data-ttu-id="46add-109">Usando o CORS, um servidor pode explicitamente permitir algumas solicitações entre origens enquanto rejeita outras.</span><span class="sxs-lookup"><span data-stu-id="46add-109">Using CORS, a server can explicitly allow some cross-origin requests while rejecting others.</span></span> <span data-ttu-id="46add-110">O CORS é mais seguro e flexível que técnicas anteriores, como [JSONP](http://en.wikipedia.org/wiki/JSONP).</span><span class="sxs-lookup"><span data-stu-id="46add-110">CORS is safer and more flexible than earlier techniques such as [JSONP](http://en.wikipedia.org/wiki/JSONP).</span></span> <span data-ttu-id="46add-111">Este tutorial mostra como habilitar o CORS em seu aplicativo de API da Web.</span><span class="sxs-lookup"><span data-stu-id="46add-111">This tutorial shows how to enable CORS in your Web API application.</span></span>
>
> ## <a name="software-used-in-the-tutorial"></a><span data-ttu-id="46add-112">Software usado no tutorial</span><span class="sxs-lookup"><span data-stu-id="46add-112">Software used in the tutorial</span></span>
>
> - [<span data-ttu-id="46add-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46add-113">Visual Studio</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
> - <span data-ttu-id="46add-114">API Web 2.2</span><span class="sxs-lookup"><span data-stu-id="46add-114">Web API 2.2</span></span>

## <a name="introduction"></a><span data-ttu-id="46add-115">Introdução</span><span class="sxs-lookup"><span data-stu-id="46add-115">Introduction</span></span>

<span data-ttu-id="46add-116">Este tutorial demonstra o que suporte a CORS na API Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="46add-116">This tutorial demonstrates CORS support in ASP.NET Web API.</span></span> <span data-ttu-id="46add-117">Vamos começar criando dois projetos do ASP.NET – um chamado "serviço Web", que hospeda um controlador de API da Web, e outro chamado "WebClient", que chama o serviço Web.</span><span class="sxs-lookup"><span data-stu-id="46add-117">We'll start by creating two ASP.NET projects – one called "WebService", which hosts a Web API controller, and the other called "WebClient", which calls WebService.</span></span> <span data-ttu-id="46add-118">Porque os dois aplicativos hospedados em diferentes domínios, uma solicitação de AJAX do WebClient para o serviço Web é uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="46add-118">Because the two applications are hosted at different domains, an AJAX request from WebClient to WebService is a cross-origin request.</span></span>

![](enabling-cross-origin-requests-in-web-api/_static/image1.png)

### <a name="what-is-same-origin"></a><span data-ttu-id="46add-119">O que é "mesma origem"?</span><span class="sxs-lookup"><span data-stu-id="46add-119">What is "same origin"?</span></span>

<span data-ttu-id="46add-120">Duas URLs têm a mesma origem se eles tiverem as portas, hosts e esquemas idênticas.</span><span class="sxs-lookup"><span data-stu-id="46add-120">Two URLs have the same origin if they have identical schemes, hosts, and ports.</span></span> <span data-ttu-id="46add-121">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span><span class="sxs-lookup"><span data-stu-id="46add-121">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span></span>

<span data-ttu-id="46add-122">Essas duas URLs têm a mesma origem:</span><span class="sxs-lookup"><span data-stu-id="46add-122">These two URLs have the same origin:</span></span>

- `http://example.com/foo.html`
- `http://example.com/bar.html`

<span data-ttu-id="46add-123">Essas URLs tem diferentes origens que o anterior dois:</span><span class="sxs-lookup"><span data-stu-id="46add-123">These URLs have different origins than the previous two:</span></span>

- <span data-ttu-id="46add-124">`http://example.net` -Domínio diferente</span><span class="sxs-lookup"><span data-stu-id="46add-124">`http://example.net` - Different domain</span></span>
- <span data-ttu-id="46add-125">`http://example.com:9000/foo.html` -Porta diferente</span><span class="sxs-lookup"><span data-stu-id="46add-125">`http://example.com:9000/foo.html` - Different port</span></span>
- <span data-ttu-id="46add-126">`https://example.com/foo.html` -Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="46add-126">`https://example.com/foo.html` - Different scheme</span></span>
- <span data-ttu-id="46add-127">`http://www.example.com/foo.html` -Subdomínio diferente</span><span class="sxs-lookup"><span data-stu-id="46add-127">`http://www.example.com/foo.html` - Different subdomain</span></span>

> [!NOTE]
> <span data-ttu-id="46add-128">Internet Explorer não considera a porta ao comparar as origens.</span><span class="sxs-lookup"><span data-stu-id="46add-128">Internet Explorer does not consider the port when comparing origins.</span></span>

## <a name="create-the-webservice-project"></a><span data-ttu-id="46add-129">Criar o projeto WebService</span><span class="sxs-lookup"><span data-stu-id="46add-129">Create the WebService project</span></span>

> [!NOTE]
> <span data-ttu-id="46add-130">Esta seção pressupõe que você já sabe como criar projetos de API da Web.</span><span class="sxs-lookup"><span data-stu-id="46add-130">This section assumes you already know how to create Web API projects.</span></span> <span data-ttu-id="46add-131">Caso contrário, consulte [Introdução ao ASP.NET Web API](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="46add-131">If not, see [Getting Started with ASP.NET Web API](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).</span></span>

1. <span data-ttu-id="46add-132">Inicie o Visual Studio e crie um novo **aplicativo Web ASP.NET (.NET Framework)** projeto.</span><span class="sxs-lookup"><span data-stu-id="46add-132">Start Visual Studio and create a new **ASP.NET Web Application (.NET Framework)** project.</span></span>
2. <span data-ttu-id="46add-133">No **novo aplicativo Web ASP.NET** caixa de diálogo, selecione o **vazia** modelo de projeto.</span><span class="sxs-lookup"><span data-stu-id="46add-133">In the **New ASP.NET Web Application** dialog box, select the **Empty** project template.</span></span> <span data-ttu-id="46add-134">Sob **adicionar pastas e os principais referências para**, selecione o **API da Web** caixa de seleção.</span><span class="sxs-lookup"><span data-stu-id="46add-134">Under **Add folders and core references for**, select the **Web API** checkbox.</span></span>

   ![Novo diálogo de projeto do ASP.NET no Visual Studio](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog.png)

3. <span data-ttu-id="46add-136">Adicionar um controlador de API da Web chamado `TestController` com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="46add-136">Add a Web API controller named `TestController` with the following code:</span></span>

   [!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample1.cs)]

4. <span data-ttu-id="46add-137">Você pode executar o aplicativo localmente ou implantar no Azure.</span><span class="sxs-lookup"><span data-stu-id="46add-137">You can run the application locally or deploy to Azure.</span></span> <span data-ttu-id="46add-138">(Para as capturas de tela neste tutorial, o aplicativo é implantado para aplicativos de Web do serviço de aplicativo do Azure.) Para verificar se a API da web está funcionando, navegue até `http://hostname/api/test/`, onde *hostname* é o domínio onde você implantou o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46add-138">(For the screenshots in this tutorial, the app deploys to Azure App Service Web Apps.) To verify that the web API is working, navigate to `http://hostname/api/test/`, where *hostname* is the domain where you deployed the application.</span></span> <span data-ttu-id="46add-139">Você deve ver o texto de resposta, &quot;obter: Mensagem de teste&quot;.</span><span class="sxs-lookup"><span data-stu-id="46add-139">You should see the response text, &quot;GET: Test Message&quot;.</span></span>

   ![Mensagem de teste de mostrando de navegador da Web](enabling-cross-origin-requests-in-web-api/_static/image4.png)

## <a name="create-the-webclient-project"></a><span data-ttu-id="46add-141">Criar o projeto de WebClient</span><span class="sxs-lookup"><span data-stu-id="46add-141">Create the WebClient project</span></span>

1. <span data-ttu-id="46add-142">Criar outra **aplicativo Web ASP.NET (.NET Framework)** do projeto e selecione o **MVC** modelo de projeto.</span><span class="sxs-lookup"><span data-stu-id="46add-142">Create another **ASP.NET Web Application (.NET Framework)** project and select the **MVC** project template.</span></span> <span data-ttu-id="46add-143">Opcionalmente, selecione **alterar autenticação** > **sem autenticação**.</span><span class="sxs-lookup"><span data-stu-id="46add-143">Optionally, select **Change Authentication** > **No Authentication**.</span></span> <span data-ttu-id="46add-144">Autenticação não é necessário para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="46add-144">You don't need authentication for this tutorial.</span></span>

   ![Modelo MVC na caixa de diálogo Novo projeto ASP.NET no Visual Studio](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog-mvc.png)

2. <span data-ttu-id="46add-146">Na **Gerenciador de soluções**, abra o arquivo *Views/Home/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="46add-146">In **Solution Explorer**, open the file *Views/Home/Index.cshtml*.</span></span> <span data-ttu-id="46add-147">Substitua o código nesse arquivo com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="46add-147">Replace the code in this file with the following:</span></span>

   [!code-cshtml[Main](enabling-cross-origin-requests-in-web-api/samples/sample2.cshtml?highlight=13)]

   <span data-ttu-id="46add-148">Para o *serviceUrl* variável, use o URI do aplicativo de serviço Web.</span><span class="sxs-lookup"><span data-stu-id="46add-148">For the *serviceUrl* variable, use the URI of the WebService app.</span></span>

3. <span data-ttu-id="46add-149">Executar o aplicativo de WebClient localmente ou publicá-lo em outro site.</span><span class="sxs-lookup"><span data-stu-id="46add-149">Run the WebClient app locally or publish it to another website.</span></span>

<span data-ttu-id="46add-150">Quando você clica no botão "Experimente", uma solicitação AJAX é enviada para o aplicativo de serviço Web usando o método HTTP listado na caixa suspensa (GET, POST ou PUT).</span><span class="sxs-lookup"><span data-stu-id="46add-150">When you click the "Try It" button, an AJAX request is submitted to the WebService app using the HTTP method listed in the dropdown box (GET, POST, or PUT).</span></span> <span data-ttu-id="46add-151">Isso permite que você examinar as solicitações entre origens diferentes.</span><span class="sxs-lookup"><span data-stu-id="46add-151">This lets you examine different cross-origin requests.</span></span> <span data-ttu-id="46add-152">Atualmente, o aplicativo de serviço Web não dá suporte a CORS, portanto, se você clicar no botão, você obterá um erro.</span><span class="sxs-lookup"><span data-stu-id="46add-152">Currently, the WebService app does not support CORS, so if you click the button you'll get an error.</span></span>

!['Try' erro no navegador](enabling-cross-origin-requests-in-web-api/_static/image7.png)

> [!NOTE]
> <span data-ttu-id="46add-154">Se você observar o tráfego HTTP em uma ferramenta como [Fiddler](https://www.telerik.com/fiddler), você verá que o navegador envie a solicitação GET e a solicitação for bem-sucedida, mas a chamada do AJAX retorna um erro.</span><span class="sxs-lookup"><span data-stu-id="46add-154">If you watch the HTTP traffic in a tool like [Fiddler](https://www.telerik.com/fiddler), you'll see that the browser does send the GET request, and the request succeeds, but the AJAX call returns an error.</span></span> <span data-ttu-id="46add-155">É importante entender a política de mesma origem não impede que o navegador do *enviando* a solicitação.</span><span class="sxs-lookup"><span data-stu-id="46add-155">It's important to understand that same-origin policy does not prevent the browser from *sending* the request.</span></span> <span data-ttu-id="46add-156">Em vez disso, ele impede que o aplicativo vendo os *resposta*.</span><span class="sxs-lookup"><span data-stu-id="46add-156">Instead, it prevents the application from seeing the *response*.</span></span>

![Depurador da web Fiddler mostrando solicitações da web](enabling-cross-origin-requests-in-web-api/_static/image8.png)

## <a name="enable-cors"></a><span data-ttu-id="46add-158">Habilitar o CORS</span><span class="sxs-lookup"><span data-stu-id="46add-158">Enable CORS</span></span>

<span data-ttu-id="46add-159">Agora vamos habilitar CORS no aplicativo do serviço Web.</span><span class="sxs-lookup"><span data-stu-id="46add-159">Now let's enable CORS in the WebService app.</span></span> <span data-ttu-id="46add-160">Primeiro, adicione o pacote NuGet de CORS.</span><span class="sxs-lookup"><span data-stu-id="46add-160">First, add the CORS NuGet package.</span></span> <span data-ttu-id="46add-161">No Visual Studio, do **ferramentas** menu, selecione **Gerenciador de pacotes NuGet**, em seguida, selecione **Package Manager Console**.</span><span class="sxs-lookup"><span data-stu-id="46add-161">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="46add-162">Na janela do Console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="46add-162">In the Package Manager Console window, type the following command:</span></span>

[!code-powershell[Main](enabling-cross-origin-requests-in-web-api/samples/sample3.ps1)]

<span data-ttu-id="46add-163">Este comando instala o pacote mais recente e atualiza todas as dependências, incluindo as bibliotecas de API da Web principal.</span><span class="sxs-lookup"><span data-stu-id="46add-163">This command installs the latest package and updates all dependencies, including the core Web API libraries.</span></span> <span data-ttu-id="46add-164">Use o `-Version` sinalizador para direcionar uma versão específica.</span><span class="sxs-lookup"><span data-stu-id="46add-164">Use the `-Version` flag to target a specific version.</span></span> <span data-ttu-id="46add-165">O pacote CORS requer Web API 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="46add-165">The CORS package requires Web API 2.0 or later.</span></span>

<span data-ttu-id="46add-166">Abra o arquivo *App\_Start/WebApiConfig.cs*.</span><span class="sxs-lookup"><span data-stu-id="46add-166">Open the file *App\_Start/WebApiConfig.cs*.</span></span> <span data-ttu-id="46add-167">Adicione o seguinte código para o **Webapiconfig** método:</span><span class="sxs-lookup"><span data-stu-id="46add-167">Add the following code to the **WebApiConfig.Register** method:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample4.cs?highlight=9)]

<span data-ttu-id="46add-168">Em seguida, adicione a **[EnableCors]** atributo para o `TestController` classe:</span><span class="sxs-lookup"><span data-stu-id="46add-168">Next, add the **[EnableCors]** attribute to the `TestController` class:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample5.cs?highlight=3,7)]

<span data-ttu-id="46add-169">Para o *origens* parâmetro, use o URI de onde você implantou o aplicativo de WebClient.</span><span class="sxs-lookup"><span data-stu-id="46add-169">For the *origins* parameter, use the URI where you deployed the WebClient application.</span></span> <span data-ttu-id="46add-170">Isso permite que solicitações entre origens do WebClient, enquanto ainda desautorizando todas as outras solicitações entre domínios.</span><span class="sxs-lookup"><span data-stu-id="46add-170">This allows cross-origin requests from WebClient, while still disallowing all other cross-domain requests.</span></span> <span data-ttu-id="46add-171">Posteriormente, descreverei os parâmetros para **[EnableCors]** em mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="46add-171">Later, I'll describe the parameters for **[EnableCors]** in more detail.</span></span>

<span data-ttu-id="46add-172">Não inclua uma barra invertida no final de *origens* URL.</span><span class="sxs-lookup"><span data-stu-id="46add-172">Do not include a forward slash at the end of the *origins* URL.</span></span>

<span data-ttu-id="46add-173">Reimplante o aplicativo de serviço Web atualizado.</span><span class="sxs-lookup"><span data-stu-id="46add-173">Redeploy the updated WebService application.</span></span> <span data-ttu-id="46add-174">Você não precisa atualizar o WebClient.</span><span class="sxs-lookup"><span data-stu-id="46add-174">You don't need to update WebClient.</span></span> <span data-ttu-id="46add-175">Agora a solicitação AJAX do WebClient deve ser bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="46add-175">Now the AJAX request from WebClient should succeed.</span></span> <span data-ttu-id="46add-176">Os métodos GET, PUT e POST todos são permitidos.</span><span class="sxs-lookup"><span data-stu-id="46add-176">The GET, PUT, and POST methods are all allowed.</span></span>

![Mensagem de teste com êxito de mostrando de navegador da Web](enabling-cross-origin-requests-in-web-api/_static/image9.png)

## <a name="how-cors-works"></a><span data-ttu-id="46add-178">Como funciona o CORS</span><span class="sxs-lookup"><span data-stu-id="46add-178">How CORS Works</span></span>

<span data-ttu-id="46add-179">Esta seção descreve o que acontece em uma solicitação CORS no nível das mensagens HTTP.</span><span class="sxs-lookup"><span data-stu-id="46add-179">This section describes what happens in a CORS request, at the level of the HTTP messages.</span></span> <span data-ttu-id="46add-180">É importante entender como funciona o CORS, para que você possa configurar o **[EnableCors]** atributo corretamente e solucionar problemas se as coisas não funcionam conforme o esperado.</span><span class="sxs-lookup"><span data-stu-id="46add-180">It's important to understand how CORS works, so that you can configure the **[EnableCors]** attribute correctly and troubleshoot if things don't work as you expect.</span></span>

<span data-ttu-id="46add-181">A especificação CORS apresenta vários novos cabeçalhos HTTP que permitem que solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="46add-181">The CORS specification introduces several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="46add-182">Se um navegador oferece suporte a CORS, ele define esses cabeçalhos automaticamente para solicitações entre origens; Você não precisa fazer nada especial em seu código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="46add-182">If a browser supports CORS, it sets these headers automatically for cross-origin requests; you don't need to do anything special in your JavaScript code.</span></span>

<span data-ttu-id="46add-183">Aqui está um exemplo de uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="46add-183">Here is an example of a cross-origin request.</span></span> <span data-ttu-id="46add-184">O cabeçalho de "Origem" fornece o domínio do site que está fazendo a solicitação.</span><span class="sxs-lookup"><span data-stu-id="46add-184">The "Origin" header gives the domain of the site that is making the request.</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample6.cmd?highlight=5)]

<span data-ttu-id="46add-185">Se o servidor permite que a solicitação, ele define o cabeçalho Access-Control-Allow-Origin.</span><span class="sxs-lookup"><span data-stu-id="46add-185">If the server allows the request, it sets the Access-Control-Allow-Origin header.</span></span> <span data-ttu-id="46add-186">O valor desse cabeçalho corresponde o cabeçalho de origem, ou é o valor de curinga "\*", o que significa que qualquer origem é permitida.</span><span class="sxs-lookup"><span data-stu-id="46add-186">The value of this header either matches the Origin header, or is the wildcard value "\*", meaning that any origin is allowed.</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample7.cmd?highlight=5)]

<span data-ttu-id="46add-187">Se a resposta não incluir o cabeçalho Access-Control-Allow-Origin, a solicitação AJAX falha.</span><span class="sxs-lookup"><span data-stu-id="46add-187">If the response does not include the Access-Control-Allow-Origin header, the AJAX request fails.</span></span> <span data-ttu-id="46add-188">Especificamente, o navegador não permite a solicitação.</span><span class="sxs-lookup"><span data-stu-id="46add-188">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="46add-189">Mesmo se o servidor retorna uma resposta bem-sucedida, o navegador não disponibiliza a resposta para o aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="46add-189">Even if the server returns a successful response, the browser does not make the response available to the client application.</span></span>

<span data-ttu-id="46add-190">**Solicitações de simulação**</span><span class="sxs-lookup"><span data-stu-id="46add-190">**Preflight Requests**</span></span>

<span data-ttu-id="46add-191">Para algumas solicitações CORS, o navegador envia uma solicitação adicional, chamada de uma "solicitação de simulação," antes de enviar a solicitação real para o recurso.</span><span class="sxs-lookup"><span data-stu-id="46add-191">For some CORS requests, the browser sends an additional request, called a "preflight request", before it sends the actual request for the resource.</span></span>

<span data-ttu-id="46add-192">O navegador pode ignorar a solicitação de simulação se as seguintes condições forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="46add-192">The browser can skip the preflight request if the following conditions are true:</span></span>

- <span data-ttu-id="46add-193">O método de solicitação é GET, HEAD ou POST, *e*</span><span class="sxs-lookup"><span data-stu-id="46add-193">The request method is GET, HEAD, or POST, *and*</span></span>
- <span data-ttu-id="46add-194">O aplicativo não definir quaisquer cabeçalhos de solicitação que não seja Accept, Accept-Language, Content-Language, Content-Type ou última--ID do evento, *e*</span><span class="sxs-lookup"><span data-stu-id="46add-194">The application does not set any request headers other than Accept, Accept-Language, Content-Language, Content-Type, or Last-Event-ID, *and*</span></span>
- <span data-ttu-id="46add-195">O cabeçalho Content-Type (se definido) é um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="46add-195">The Content-Type header (if set) is one of the following:</span></span>

    - <span data-ttu-id="46add-196">application/x-www-form-urlencoded</span><span class="sxs-lookup"><span data-stu-id="46add-196">application/x-www-form-urlencoded</span></span>
    - <span data-ttu-id="46add-197">multipart/form-data</span><span class="sxs-lookup"><span data-stu-id="46add-197">multipart/form-data</span></span>
    - <span data-ttu-id="46add-198">texto/simples</span><span class="sxs-lookup"><span data-stu-id="46add-198">text/plain</span></span>

<span data-ttu-id="46add-199">A regra sobre cabeçalhos de solicitação se aplica aos cabeçalhos que o aplicativo define chamando **setRequestHeader** sobre o **XMLHttpRequest** objeto.</span><span class="sxs-lookup"><span data-stu-id="46add-199">The rule about request headers applies to headers that the application sets by calling **setRequestHeader** on the **XMLHttpRequest** object.</span></span> <span data-ttu-id="46add-200">(A especificação CORS chama esses cabeçalhos de solicitação"autor".) A regra não se aplica aos cabeçalhos de *navegador* pode definir, como o agente do usuário, o Host ou o comprimento do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="46add-200">(The CORS specification calls these "author request headers".) The rule does not apply to headers the *browser* can set, such as User-Agent, Host, or Content-Length.</span></span>

<span data-ttu-id="46add-201">Aqui está um exemplo de uma solicitação de simulação:</span><span class="sxs-lookup"><span data-stu-id="46add-201">Here is an example of a preflight request:</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample8.cmd?highlight=4-5)]

<span data-ttu-id="46add-202">A solicitação de simulação usa o método HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="46add-202">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="46add-203">Ele inclui dois cabeçalhos especiais:</span><span class="sxs-lookup"><span data-stu-id="46add-203">It includes two special headers:</span></span>

- <span data-ttu-id="46add-204">Access-Control-Request-Method: O método HTTP que será usado para a solicitação real.</span><span class="sxs-lookup"><span data-stu-id="46add-204">Access-Control-Request-Method: The HTTP method that will be used for the actual request.</span></span>
- <span data-ttu-id="46add-205">Access-Control-Request-Headers: Uma lista de cabeçalhos de solicitação que o *aplicativo* configurado na solicitação real.</span><span class="sxs-lookup"><span data-stu-id="46add-205">Access-Control-Request-Headers: A list of request headers that the *application* set on the actual request.</span></span> <span data-ttu-id="46add-206">(Novamente, isso não inclui cabeçalhos que define o navegador.)</span><span class="sxs-lookup"><span data-stu-id="46add-206">(Again, this does not include headers that the browser sets.)</span></span>

<span data-ttu-id="46add-207">Aqui está um exemplo de resposta, supondo que o servidor permite que a solicitação:</span><span class="sxs-lookup"><span data-stu-id="46add-207">Here is an example response, assuming that the server allows the request:</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample9.cmd?highlight=6-7)]

<span data-ttu-id="46add-208">A resposta inclui um cabeçalho Access-Control-Allow-Methods que lista os métodos permitidos e, opcionalmente, um cabeçalho Access-Control-Allow-Headers, que lista os cabeçalhos permitidos.</span><span class="sxs-lookup"><span data-stu-id="46add-208">The response includes an Access-Control-Allow-Methods header that lists the allowed methods, and optionally an Access-Control-Allow-Headers header, which lists the allowed headers.</span></span> <span data-ttu-id="46add-209">Se a solicitação de simulação for bem-sucedida, o navegador envia a solicitação real, conforme descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="46add-209">If the preflight request succeeds, the browser sends the actual request, as described earlier.</span></span>

<span data-ttu-id="46add-210">Ferramentas usadas comumente para testar os pontos de extremidade com solicitações de simulação OPTIONS (por exemplo, [Fiddler](https://www.telerik.com/fiddler) e [Postman](https://www.getpostman.com/)) não enviam os cabeçalhos necessários de opções por padrão.</span><span class="sxs-lookup"><span data-stu-id="46add-210">Tools commonly used to test endpoints with preflight OPTIONS requests (for example, [Fiddler](https://www.telerik.com/fiddler) and [Postman](https://www.getpostman.com/)) don't send the required OPTIONS headers by default.</span></span> <span data-ttu-id="46add-211">Confirme se o `Access-Control-Request-Method` e `Access-Control-Request-Headers` cabeçalhos são enviados com a solicitação e cabeçalhos de opções cheguem ao aplicativo por meio do IIS.</span><span class="sxs-lookup"><span data-stu-id="46add-211">Confirm that the `Access-Control-Request-Method` and `Access-Control-Request-Headers` headers are sent with the request and that OPTIONS headers reach the app through IIS.</span></span>

<span data-ttu-id="46add-212">Para configurar o IIS para permitir que um aplicativo ASP.NET receber e lidar com a opção as solicitações, adicione a seguinte configuração para o aplicativo *Web. config* arquivo no `<system.webServer><handlers>` seção:</span><span class="sxs-lookup"><span data-stu-id="46add-212">To configure IIS to allow an ASP.NET app to receive and handle OPTION requests, add the following configuration to the app's *web.config* file in the `<system.webServer><handlers>` section:</span></span>

```xml
<system.webServer>
  <handlers>
    <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
    <remove name="OPTIONSVerbHandler" />
    <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="*" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
  </handlers>
</system.webServer>
```

<span data-ttu-id="46add-213">A remoção de `OPTIONSVerbHandler` impede que o IIS tratar as solicitações de opções.</span><span class="sxs-lookup"><span data-stu-id="46add-213">The removal of `OPTIONSVerbHandler` prevents IIS from handling OPTIONS requests.</span></span> <span data-ttu-id="46add-214">A substituição de `ExtensionlessUrlHandler-Integrated-4.0` permite opções solicitações cheguem ao aplicativo, porque o registro do módulo padrão permite somente solicitações GET, HEAD, POST e depuração com URLs sem extensão.</span><span class="sxs-lookup"><span data-stu-id="46add-214">The replacement of `ExtensionlessUrlHandler-Integrated-4.0` allows OPTIONS requests to reach the app because the default module registration only allows GET, HEAD, POST, and DEBUG requests with extensionless URLs.</span></span>

## <a name="scope-rules-for-enablecors"></a><span data-ttu-id="46add-215">Regras de escopo para [EnableCors]</span><span class="sxs-lookup"><span data-stu-id="46add-215">Scope Rules for [EnableCors]</span></span>

<span data-ttu-id="46add-216">Você pode habilitar o CORS por ação, por controlador ou globalmente para todos os controladores de API da Web em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46add-216">You can enable CORS per action, per controller, or globally for all Web API controllers in your application.</span></span>

<span data-ttu-id="46add-217">**Por ação**</span><span class="sxs-lookup"><span data-stu-id="46add-217">**Per Action**</span></span>

<span data-ttu-id="46add-218">Para habilitar o CORS para uma única ação, defina as **[EnableCors]** atributo no método de ação.</span><span class="sxs-lookup"><span data-stu-id="46add-218">To enable CORS for a single action, set the **[EnableCors]** attribute on the action method.</span></span> <span data-ttu-id="46add-219">O exemplo a seguir habilita o CORS para o `GetItem` somente no método.</span><span class="sxs-lookup"><span data-stu-id="46add-219">The following example enables CORS for the `GetItem` method only.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample10.cs)]

<span data-ttu-id="46add-220">**Por controlador**</span><span class="sxs-lookup"><span data-stu-id="46add-220">**Per Controller**</span></span>

<span data-ttu-id="46add-221">Se você definir **[EnableCors]** na classe do controlador, ele se aplica a todas as ações no controlador.</span><span class="sxs-lookup"><span data-stu-id="46add-221">If you set **[EnableCors]** on the controller class, it applies to all the actions on the controller.</span></span> <span data-ttu-id="46add-222">Para desabilitar CORS para uma ação, adicione a **[DisableCors]** atributo à ação.</span><span class="sxs-lookup"><span data-stu-id="46add-222">To disable CORS for an action, add the **[DisableCors]** attribute to the action.</span></span> <span data-ttu-id="46add-223">O exemplo a seguir habilita o CORS para cada método, exceto `PutItem`.</span><span class="sxs-lookup"><span data-stu-id="46add-223">The following example enables CORS for every method except `PutItem`.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample11.cs)]

<span data-ttu-id="46add-224">**Globalmente**</span><span class="sxs-lookup"><span data-stu-id="46add-224">**Globally**</span></span>

<span data-ttu-id="46add-225">Para habilitar o CORS para todos os controladores de API da Web em seu aplicativo, passe uma **EnableCorsAttribute** da instância para o **EnableCors** método:</span><span class="sxs-lookup"><span data-stu-id="46add-225">To enable CORS for all Web API controllers in your application, pass an **EnableCorsAttribute** instance to the **EnableCors** method:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample12.cs)]

<span data-ttu-id="46add-226">Se você definir o atributo em mais de um escopo, a ordem de precedência é:</span><span class="sxs-lookup"><span data-stu-id="46add-226">If you set the attribute at more than one scope, the order of precedence is:</span></span>

1. <span data-ttu-id="46add-227">Ação</span><span class="sxs-lookup"><span data-stu-id="46add-227">Action</span></span>
2. <span data-ttu-id="46add-228">Controlador</span><span class="sxs-lookup"><span data-stu-id="46add-228">Controller</span></span>
3. <span data-ttu-id="46add-229">Global</span><span class="sxs-lookup"><span data-stu-id="46add-229">Global</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="46add-230">Defina as origens permitidas</span><span class="sxs-lookup"><span data-stu-id="46add-230">Set the allowed origins</span></span>

<span data-ttu-id="46add-231">O *origens* parâmetro do **[EnableCors]** atributo especifica quais origens têm permissão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="46add-231">The *origins* parameter of the **[EnableCors]** attribute specifies which origins are allowed to access the resource.</span></span> <span data-ttu-id="46add-232">O valor é uma lista separada por vírgulas das origens permitidas.</span><span class="sxs-lookup"><span data-stu-id="46add-232">The value is a comma-separated list of the allowed origins.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample13.cs)]

<span data-ttu-id="46add-233">Você também pode usar o valor de curinga "\*" para permitir solicitações de qualquer origem.</span><span class="sxs-lookup"><span data-stu-id="46add-233">You can also use the wildcard value "\*" to allow requests from any origins.</span></span>

<span data-ttu-id="46add-234">Considere cuidadosamente antes de permitir que solicitações de qualquer origem.</span><span class="sxs-lookup"><span data-stu-id="46add-234">Consider carefully before allowing requests from any origin.</span></span> <span data-ttu-id="46add-235">Isso significa que, literalmente, qualquer site possa fazer chamadas AJAX para sua API da web.</span><span class="sxs-lookup"><span data-stu-id="46add-235">It means that literally any website can make AJAX calls to your web API.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample14.cs)]

## <a name="set-the-allowed-http-methods"></a><span data-ttu-id="46add-236">Defina os métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="46add-236">Set the allowed HTTP methods</span></span>

<span data-ttu-id="46add-237">O *métodos* parâmetro do **[EnableCors]** atributo especifica quais métodos HTTP têm permissão para acessar o recurso.</span><span class="sxs-lookup"><span data-stu-id="46add-237">The *methods* parameter of the **[EnableCors]** attribute specifies which HTTP methods are allowed to access the resource.</span></span> <span data-ttu-id="46add-238">Para permitir que todos os métodos, use o valor de curinga "\*".</span><span class="sxs-lookup"><span data-stu-id="46add-238">To allow all methods, use the wildcard value "\*".</span></span> <span data-ttu-id="46add-239">O exemplo a seguir permite que somente as solicitações GET e POST.</span><span class="sxs-lookup"><span data-stu-id="46add-239">The following example allows only GET and POST requests.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample15.cs)]

## <a name="set-the-allowed-request-headers"></a><span data-ttu-id="46add-240">Definir os cabeçalhos de solicitação permitido</span><span class="sxs-lookup"><span data-stu-id="46add-240">Set the allowed request headers</span></span>

<span data-ttu-id="46add-241">Este artigo descreveu anteriormente como uma solicitação de simulação pode incluir um cabeçalho Access-Control-Request-Headers, listando os cabeçalhos HTTP definidos pelo aplicativo (os chamados "author cabeçalhos de solicitação").</span><span class="sxs-lookup"><span data-stu-id="46add-241">This article described earlier how a preflight request might include an Access-Control-Request-Headers header, listing the HTTP headers set by the application (the so-called "author request headers").</span></span> <span data-ttu-id="46add-242">O *cabeçalhos* parâmetro do **[EnableCors]** atributo especifica quais cabeçalhos de solicitação do autor são permitidos.</span><span class="sxs-lookup"><span data-stu-id="46add-242">The *headers* parameter of the **[EnableCors]** attribute specifies which author request headers are allowed.</span></span> <span data-ttu-id="46add-243">Para permitir que todos os cabeçalhos, defina *cabeçalhos* para "\*".</span><span class="sxs-lookup"><span data-stu-id="46add-243">To allow any headers, set *headers* to "\*".</span></span> <span data-ttu-id="46add-244">Para cabeçalhos específicos de lista de permissões, defina *cabeçalhos* a uma lista separada por vírgula de cabeçalhos permitidos:</span><span class="sxs-lookup"><span data-stu-id="46add-244">To whitelist specific headers, set *headers* to a comma-separated list of the allowed headers:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample16.cs)]

<span data-ttu-id="46add-245">No entanto, os navegadores não são totalmente consistentes em como eles definir Access-Control-Request-Headers.</span><span class="sxs-lookup"><span data-stu-id="46add-245">However, browsers are not entirely consistent in how they set Access-Control-Request-Headers.</span></span> <span data-ttu-id="46add-246">Por exemplo, o Chrome atualmente inclui "origem".</span><span class="sxs-lookup"><span data-stu-id="46add-246">For example, Chrome currently includes "origin".</span></span> <span data-ttu-id="46add-247">FireFox não inclui cabeçalhos padrão, como "Aceitar", mesmo quando o aplicativo define-los no script.</span><span class="sxs-lookup"><span data-stu-id="46add-247">FireFox does not include standard headers such as "Accept", even when the application sets them in script.</span></span>

<span data-ttu-id="46add-248">Se você definir *cabeçalhos* para qualquer coisa diferente de "\*", você deve incluir pelo menos "accept", "content-type" e "origem", além de quaisquer cabeçalhos personalizados que você deseja dar suporte.</span><span class="sxs-lookup"><span data-stu-id="46add-248">If you set *headers* to anything other than "\*", you should include at least "accept", "content-type", and "origin", plus any custom headers that you want to support.</span></span>

## <a name="set-the-allowed-response-headers"></a><span data-ttu-id="46add-249">Definir os cabeçalhos de resposta permitidos</span><span class="sxs-lookup"><span data-stu-id="46add-249">Set the allowed response headers</span></span>

<span data-ttu-id="46add-250">Por padrão, o navegador não expõe todos os cabeçalhos de resposta para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46add-250">By default, the browser does not expose all of the response headers to the application.</span></span> <span data-ttu-id="46add-251">Os cabeçalhos de resposta que estão disponíveis por padrão são:</span><span class="sxs-lookup"><span data-stu-id="46add-251">The response headers that are available by default are:</span></span>

- <span data-ttu-id="46add-252">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="46add-252">Cache-Control</span></span>
- <span data-ttu-id="46add-253">Idioma do conteúdo</span><span class="sxs-lookup"><span data-stu-id="46add-253">Content-Language</span></span>
- <span data-ttu-id="46add-254">Tipo de conteúdo</span><span class="sxs-lookup"><span data-stu-id="46add-254">Content-Type</span></span>
- <span data-ttu-id="46add-255">Expira</span><span class="sxs-lookup"><span data-stu-id="46add-255">Expires</span></span>
- <span data-ttu-id="46add-256">Última modificação</span><span class="sxs-lookup"><span data-stu-id="46add-256">Last-Modified</span></span>
- <span data-ttu-id="46add-257">Pragma</span><span class="sxs-lookup"><span data-stu-id="46add-257">Pragma</span></span>

<span data-ttu-id="46add-258">A especificação CORS chama esses [cabeçalhos de resposta simples](https://dvcs.w3.org/hg/cors/raw-file/tip/Overview.html#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="46add-258">The CORS spec calls these [simple response headers](https://dvcs.w3.org/hg/cors/raw-file/tip/Overview.html#simple-response-header).</span></span> <span data-ttu-id="46add-259">Para disponibilizar outros cabeçalhos para o aplicativo, defina as *exposedHeaders* parâmetro do **[EnableCors]**.</span><span class="sxs-lookup"><span data-stu-id="46add-259">To make other headers available to the application, set the *exposedHeaders* parameter of **[EnableCors]**.</span></span>

<span data-ttu-id="46add-260">No exemplo a seguir, o controlador `Get` método define um cabeçalho personalizado chamado 'X-Custom-Header'.</span><span class="sxs-lookup"><span data-stu-id="46add-260">In the following example, the controller's `Get` method sets a custom header named ‘X-Custom-Header'.</span></span> <span data-ttu-id="46add-261">Por padrão, o navegador não irá expor esse cabeçalho em uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="46add-261">By default, the browser will not expose this header in a cross-origin request.</span></span> <span data-ttu-id="46add-262">Para disponibilizar o cabeçalho, incluir 'X-Custom-Header' em *exposedHeaders*.</span><span class="sxs-lookup"><span data-stu-id="46add-262">To make the header available, include ‘X-Custom-Header' in *exposedHeaders*.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample17.cs)]

## <a name="pass-credentials-in-cross-origin-requests"></a><span data-ttu-id="46add-263">Passar credenciais nas solicitações entre origens</span><span class="sxs-lookup"><span data-stu-id="46add-263">Pass credentials in cross-origin requests</span></span>

<span data-ttu-id="46add-264">Credenciais exigem tratamento especial em uma solicitação CORS.</span><span class="sxs-lookup"><span data-stu-id="46add-264">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="46add-265">Por padrão, o navegador não envia todas as credenciais com uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="46add-265">By default, the browser does not send any credentials with a cross-origin request.</span></span> <span data-ttu-id="46add-266">As credenciais incluem cookies, bem como os esquemas de autenticação HTTP.</span><span class="sxs-lookup"><span data-stu-id="46add-266">Credentials include cookies as well as HTTP authentication schemes.</span></span> <span data-ttu-id="46add-267">Para enviar as credenciais com uma solicitação entre origens, o cliente deve definir **XMLHttpRequest.withCredentials** como true.</span><span class="sxs-lookup"><span data-stu-id="46add-267">To send credentials with a cross-origin request, the client must set **XMLHttpRequest.withCredentials** to true.</span></span>

<span data-ttu-id="46add-268">Usando o **XMLHttpRequest** diretamente:</span><span class="sxs-lookup"><span data-stu-id="46add-268">Using **XMLHttpRequest** directly:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample18.cs)]

<span data-ttu-id="46add-269">No jQuery:</span><span class="sxs-lookup"><span data-stu-id="46add-269">In jQuery:</span></span>

[!code-javascript[Main](enabling-cross-origin-requests-in-web-api/samples/sample19.js)]

<span data-ttu-id="46add-270">Além disso, o servidor deve permitir que as credenciais.</span><span class="sxs-lookup"><span data-stu-id="46add-270">In addition, the server must allow the credentials.</span></span> <span data-ttu-id="46add-271">Para permitir credenciais entre origens na API da Web, defina as **SupportsCredentials** propriedade como true na **[EnableCors]** atributo:</span><span class="sxs-lookup"><span data-stu-id="46add-271">To allow cross-origin credentials in Web API, set the **SupportsCredentials** property to true on the **[EnableCors]** attribute:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample20.cs)]

<span data-ttu-id="46add-272">Se essa propriedade for true, a resposta HTTP incluirá um cabeçalho Access-Control-Allow-Credentials.</span><span class="sxs-lookup"><span data-stu-id="46add-272">If this property is true, the HTTP response will include an Access-Control-Allow-Credentials header.</span></span> <span data-ttu-id="46add-273">Esse cabeçalho informa ao navegador que o servidor permite que as credenciais para uma solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="46add-273">This header tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="46add-274">Se o navegador envia as credenciais, mas a resposta não inclui um cabeçalho Access-Control-Allow-Credentials válido, o navegador não exporá a resposta para o aplicativo e a solicitação AJAX falhar.</span><span class="sxs-lookup"><span data-stu-id="46add-274">If the browser sends credentials, but the response does not include a valid Access-Control-Allow-Credentials header, the browser will not expose the response to the application, and the AJAX request fails.</span></span>

<span data-ttu-id="46add-275">Tenha cuidado sobre a configuração **SupportsCredentials** como true, porque significa que um site em outro domínio pode enviar credenciais do usuário conectado à sua API da Web em nome do usuário, sem que o usuário estar ciente.</span><span class="sxs-lookup"><span data-stu-id="46add-275">Be careful about setting **SupportsCredentials** to true, because it means a website at another domain can send a logged-in user's credentials to your Web API on the user's behalf, without the user being aware.</span></span> <span data-ttu-id="46add-276">A especificação CORS também declara que a configuração *origens* à &quot; \* &quot; não é válido se **SupportsCredentials** é verdadeiro.</span><span class="sxs-lookup"><span data-stu-id="46add-276">The CORS spec also states that setting *origins* to &quot;\*&quot; is invalid if **SupportsCredentials** is true.</span></span>

## <a name="custom-cors-policy-providers"></a><span data-ttu-id="46add-277">Provedores de política CORS personalizados</span><span class="sxs-lookup"><span data-stu-id="46add-277">Custom CORS policy providers</span></span>

<span data-ttu-id="46add-278">O **[EnableCors]** atributo implementa o **ICorsPolicyProvider** interface.</span><span class="sxs-lookup"><span data-stu-id="46add-278">The **[EnableCors]** attribute implements the **ICorsPolicyProvider** interface.</span></span> <span data-ttu-id="46add-279">Você pode fornecer sua própria implementação, criando uma classe que deriva de **atributo** e implementa **ICorsProlicyProvider**.</span><span class="sxs-lookup"><span data-stu-id="46add-279">You can provide your own implementation by creating a class that derives from **Attribute** and implements **ICorsProlicyProvider**.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample21.cs)]

<span data-ttu-id="46add-280">Agora você pode aplicar o atributo de qualquer lugar que você colocaria **[EnableCors]**.</span><span class="sxs-lookup"><span data-stu-id="46add-280">Now you can apply the attribute any place that you would put **[EnableCors]**.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample22.cs)]

<span data-ttu-id="46add-281">Por exemplo, um provedor de política CORS personalizado pode ler as configurações de um arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="46add-281">For example, a custom CORS policy provider could read the settings from a configuration file.</span></span>

<span data-ttu-id="46add-282">Como uma alternativa ao uso de atributos, você pode registrar um **ICorsPolicyProviderFactory** objeto cria **ICorsPolicyProvider** objetos.</span><span class="sxs-lookup"><span data-stu-id="46add-282">As an alternative to using attributes, you can register an **ICorsPolicyProviderFactory** object that creates **ICorsPolicyProvider** objects.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample23.cs)]

<span data-ttu-id="46add-283">Para definir a **ICorsPolicyProviderFactory**, chame o **SetCorsPolicyProviderFactory** método de extensão na inicialização, da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="46add-283">To set the **ICorsPolicyProviderFactory**, call the **SetCorsPolicyProviderFactory** extension method at startup, as follows:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample24.cs)]

## <a name="browser-support"></a><span data-ttu-id="46add-284">Suporte ao navegador</span><span class="sxs-lookup"><span data-stu-id="46add-284">Browser support</span></span>

<span data-ttu-id="46add-285">O pacote de CORS da API Web é uma tecnologia do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="46add-285">The Web API CORS package is a server-side technology.</span></span> <span data-ttu-id="46add-286">O navegador do usuário também precisa oferecer suporte a CORS.</span><span class="sxs-lookup"><span data-stu-id="46add-286">The user's browser also needs to support CORS.</span></span> <span data-ttu-id="46add-287">Felizmente, as versões atuais de todos os principais navegadores incluem [suporte para CORS](http://caniuse.com/cors).</span><span class="sxs-lookup"><span data-stu-id="46add-287">Fortunately, the current versions of all major browsers include [support for CORS](http://caniuse.com/cors).</span></span>
