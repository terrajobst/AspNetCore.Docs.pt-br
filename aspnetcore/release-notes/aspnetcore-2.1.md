---
title: Novidades do ASP.NET Core 2.1
author: isaac2004
description: Conheça os novos recursos no ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2019
uid: aspnetcore-2.1
ms.openlocfilehash: 359f961db768b9048427c8ab296ee3e035879408
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086400"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="80d19-103">Novidades do ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="80d19-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="80d19-104">Este artigo destaca as alterações mais significativas no ASP.NET Core 2.1, com links para a documentação relevante.</span><span class="sxs-lookup"><span data-stu-id="80d19-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## <a name="signalr"></a><span data-ttu-id="80d19-105">SignalR</span><span class="sxs-lookup"><span data-stu-id="80d19-105">SignalR</span></span>

<span data-ttu-id="80d19-106">O SignalR foi reescrito para ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="80d19-106">SignalR has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="80d19-107">O SignalR do ASP.NET Core inclui uma série de melhorias:</span><span class="sxs-lookup"><span data-stu-id="80d19-107">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="80d19-108">Um modelo de expansão simplificado.</span><span class="sxs-lookup"><span data-stu-id="80d19-108">A simplified scale-out model.</span></span>
* <span data-ttu-id="80d19-109">Um novo cliente JavaScript sem dependência jQuery.</span><span class="sxs-lookup"><span data-stu-id="80d19-109">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="80d19-110">Um novo protocolo binário compacto com base em MessagePack.</span><span class="sxs-lookup"><span data-stu-id="80d19-110">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="80d19-111">Suporte para protocolos personalizados.</span><span class="sxs-lookup"><span data-stu-id="80d19-111">Support for custom protocols.</span></span>
* <span data-ttu-id="80d19-112">Um novo modelo de resposta de transmissão.</span><span class="sxs-lookup"><span data-stu-id="80d19-112">A new streaming response model.</span></span>
* <span data-ttu-id="80d19-113">Suporte para clientes com base em WebSockets básicos.</span><span class="sxs-lookup"><span data-stu-id="80d19-113">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="80d19-114">Para obter mais informações, veja [ASP.NET Core SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="80d19-114">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="razor-class-libraries"></a><span data-ttu-id="80d19-115">Biblioteca de classes Razor</span><span class="sxs-lookup"><span data-stu-id="80d19-115">Razor class libraries</span></span>

<span data-ttu-id="80d19-116">O ASP.NET Core 2.1 torna mais fácil criar e incluir interface do usuário baseada em Razor em uma biblioteca e compartilhá-la em vários projetos.</span><span class="sxs-lookup"><span data-stu-id="80d19-116">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="80d19-117">O novo SDK do Razor habilita o build de arquivos do Razor em um projeto de biblioteca de classes que podem ser empacotados em um pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="80d19-117">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="80d19-118">Exibições e páginas em bibliotecas são descobertas automaticamente e podem ser substituídas pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="80d19-118">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="80d19-119">Ao integrar a compilação do Razor ao build:</span><span class="sxs-lookup"><span data-stu-id="80d19-119">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="80d19-120">O tempo de inicialização do aplicativo é significativamente mais rápido.</span><span class="sxs-lookup"><span data-stu-id="80d19-120">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="80d19-121">Atualizações rápidas a modos de exibição e páginas Razor em tempo de execução ainda estão disponíveis como parte de um fluxo de trabalho de desenvolvimento iterativo.</span><span class="sxs-lookup"><span data-stu-id="80d19-121">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="80d19-122">Para obter mais informações, veja [Criar interface do usuário reutilizável usando o projeto de Biblioteca de Classes do Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="80d19-122">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="identity-ui-library--scaffolding"></a><span data-ttu-id="80d19-123">Scaffolding e biblioteca de interface do usuário de Identidade</span><span class="sxs-lookup"><span data-stu-id="80d19-123">Identity UI library & scaffolding</span></span>

<span data-ttu-id="80d19-124">O ASP.NET Core 2.1 fornece a [Identidade do ASP.NET Core](xref:security/authentication/identity) como uma [Biblioteca de Classes do Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="80d19-124">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="80d19-125">Aplicativos que incluem Identidade podem aplicar o novo scaffolder de Identidade para adicionar seletivamente o código-fonte contido na RCL (Biblioteca de Classes do Razor) de Identidade.</span><span class="sxs-lookup"><span data-stu-id="80d19-125">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="80d19-126">Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento.</span><span class="sxs-lookup"><span data-stu-id="80d19-126">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="80d19-127">Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro.</span><span class="sxs-lookup"><span data-stu-id="80d19-127">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="80d19-128">Código gerado tem precedência sobre o mesmo código na RCL de Identidade.</span><span class="sxs-lookup"><span data-stu-id="80d19-128">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="80d19-129">Aplicativos que **não** incluem autenticação podem aplicar o scaffolder de Identidade para adicionar o pacote de Identidade RCL.</span><span class="sxs-lookup"><span data-stu-id="80d19-129">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="80d19-130">Você tem a opção de selecionar o código de Identidade a ser gerado.</span><span class="sxs-lookup"><span data-stu-id="80d19-130">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="80d19-131">Para obter mais informações, veja [Identidade scaffold em projetos ASP.NET Core](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="80d19-131">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="80d19-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="80d19-132">HTTPS</span></span>

<span data-ttu-id="80d19-133">Com o foco cada vez maior em segurança e privacidade, é importante habilitar HTTPS para aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="80d19-133">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="80d19-134">A imposição de HTTPS está se tornando cada vez mais rígida na Web.</span><span class="sxs-lookup"><span data-stu-id="80d19-134">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="80d19-135">Sites que não usam HTTPS são considerados inseguros.</span><span class="sxs-lookup"><span data-stu-id="80d19-135">Sites that don’t use HTTPS are considered insecure.</span></span> <span data-ttu-id="80d19-136">Navegadores (Chrome, Mozilla) estão começando a impor o uso de recursos Web em um contexto de seguro.</span><span class="sxs-lookup"><span data-stu-id="80d19-136">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="80d19-137">O [RGPD](xref:security/gdpr) requer o uso de HTTPS para proteger a privacidade do usuário.</span><span class="sxs-lookup"><span data-stu-id="80d19-137">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="80d19-138">Quando usar HTTPS em produção for fundamental, usar HTTPS no desenvolvimento pode ajudar a evitar problemas de implantação (por exemplo, links inseguros).</span><span class="sxs-lookup"><span data-stu-id="80d19-138">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="80d19-139">O ASP.NET Core 2.1 inclui uma série de melhorias que tornam mais fácil usar HTTPS em desenvolvimento e configurar HTTPS em produção.</span><span class="sxs-lookup"><span data-stu-id="80d19-139">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="80d19-140">Para obter mais informações, veja [Impor HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="80d19-140">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="80d19-141">Ativo por padrão</span><span class="sxs-lookup"><span data-stu-id="80d19-141">On by default</span></span>

<span data-ttu-id="80d19-142">Para facilitar o desenvolvimento de site seguro, HTTPS está habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="80d19-142">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="80d19-143">Da versão 2.1 em diante, o Kestrel escuta em `https://localhost:5001` quando um certificado de desenvolvimento local está presente.</span><span class="sxs-lookup"><span data-stu-id="80d19-143">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="80d19-144">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="80d19-144">A development certificate is created:</span></span>

* <span data-ttu-id="80d19-145">Como parte da experiência de primeira execução do SDK do .NET Core, quando você usa o SDK pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="80d19-145">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="80d19-146">Manualmente usando a nova ferramenta `dev-certs`.</span><span class="sxs-lookup"><span data-stu-id="80d19-146">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="80d19-147">Execute `dotnet dev-certs https --trust` para confiar no certificado.</span><span class="sxs-lookup"><span data-stu-id="80d19-147">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="80d19-148">Redirecionamento e imposição de HTTPS</span><span class="sxs-lookup"><span data-stu-id="80d19-148">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="80d19-149">Aplicativos Web geralmente precisam escutar em HTTP e HTTPS, mas, então redirecionam todo o tráfego HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="80d19-149">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="80d19-150">Na versão 2.1, foi introduzido o middleware de redirecionamento de HTTPS especializado que redireciona de modo inteligente com base na presença de portas do servidor associado ou de configuração.</span><span class="sxs-lookup"><span data-stu-id="80d19-150">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="80d19-151">O uso de HTTPS pode ser imposto ainda mais empregando [HSTS (Protocolo de Segurança do Transporte Estrita HTTP)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="80d19-151">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="80d19-152">HSTS instrui navegadores a sempre acessarem o site por meio de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="80d19-152">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="80d19-153">O ASP.NET Core 2.1 adiciona middleware HSTS compatível com opções para idade máxima, subdomínios e a lista de pré-carregamento de HSTS.</span><span class="sxs-lookup"><span data-stu-id="80d19-153">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="80d19-154">Configuração para produção</span><span class="sxs-lookup"><span data-stu-id="80d19-154">Configuration for production</span></span>

<span data-ttu-id="80d19-155">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="80d19-155">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="80d19-156">Na versão 2.1, foi adicionado o esquema de configuração padrão para configurar HTTPS para Kestrel.</span><span class="sxs-lookup"><span data-stu-id="80d19-156">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="80d19-157">Os aplicativos podem ser configurados para usar:</span><span class="sxs-lookup"><span data-stu-id="80d19-157">Apps can be configured to use:</span></span>

* <span data-ttu-id="80d19-158">Vários pontos de extremidade incluindo as URLs.</span><span class="sxs-lookup"><span data-stu-id="80d19-158">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="80d19-159">Para saber mais, confira [Implementação do servidor Web Kestrel: configuração do ponto de extremidade](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="80d19-159">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="80d19-160">O certificado a ser usado para HTTPS de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="80d19-160">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="80d19-161">RGPD</span><span class="sxs-lookup"><span data-stu-id="80d19-161">GDPR</span></span>

<span data-ttu-id="80d19-162">O ASP.NET Core fornece APIs e modelos para ajudar a atender alguns dos requisitos do [RGPD (Regulamento de Proteção de Dados Geral) da UE](https://www.eugdpr.org/).</span><span class="sxs-lookup"><span data-stu-id="80d19-162">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="80d19-163">Para obter mais informações, veja [Suporte RGPD no ASP.NET Core](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="80d19-163">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="80d19-164">Um [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) mostra como usar e permite que você teste a maioria dos pontos de extensão RGPD e APIs adicionados aos modelos do ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="80d19-164">A [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="80d19-165">Testes de integração</span><span class="sxs-lookup"><span data-stu-id="80d19-165">Integration tests</span></span>

<span data-ttu-id="80d19-166">É introduzido um novo pacote que simplifica a criação e a execução do teste.</span><span class="sxs-lookup"><span data-stu-id="80d19-166">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="80d19-167">O pacote [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) lida com as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="80d19-167">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="80d19-168">Copia o arquivo de dependência (*\*.deps*) do aplicativo testado para a pasta *bin* do projeto de teste.</span><span class="sxs-lookup"><span data-stu-id="80d19-168">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="80d19-169">Define a raiz de conteúdo para a raiz do projeto do aplicativo testado para que arquivos estáticos e páginas/exibições sejam encontrados quando os testes forem executados.</span><span class="sxs-lookup"><span data-stu-id="80d19-169">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="80d19-170">Fornece a classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para simplificar a inicialização do aplicativo testado com [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="80d19-170">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="80d19-171">O teste a seguir usa [xUnit](https://xunit.github.io/) para verificar se a página de índice é carregada com um código de status de êxito e o cabeçalho Content-Type correto:</span><span class="sxs-lookup"><span data-stu-id="80d19-171">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

<span data-ttu-id="80d19-172">Para obter mais informações, veja o tópico [Testes de integração](xref:test/integration-tests).</span><span class="sxs-lookup"><span data-stu-id="80d19-172">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="80d19-173">[ApiController], ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="80d19-173">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="80d19-174">O ASP.NET Core 2.1 adiciona novas convenções de programação que facilitam o build de APIs Web limpas e descritivas.</span><span class="sxs-lookup"><span data-stu-id="80d19-174">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="80d19-175">`ActionResult<T>` é um novo tipo adicionado para permitir que um aplicativo retorne um tipo de resposta ou qualquer outro resultado da ação (semelhante a IActionResult), enquanto ainda indica o tipo de resposta.</span><span class="sxs-lookup"><span data-stu-id="80d19-175">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="80d19-176">O atributo `[ApiController]` também foi adicionado como a maneira de aceitar convenções e comportamentos específicos da API Web.</span><span class="sxs-lookup"><span data-stu-id="80d19-176">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="80d19-177">Para obter mais informações, veja [Criar APIs Web com ASP.NET Core](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="80d19-177">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="80d19-178">IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="80d19-178">IHttpClientFactory</span></span>

<span data-ttu-id="80d19-179">O ASP.NET Core 2.1 inclui um novo serviço `IHttpClientFactory` que torna mais fácil configurar e consumir instâncias de `HttpClient` em aplicativos.</span><span class="sxs-lookup"><span data-stu-id="80d19-179">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="80d19-180">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados uns aos outros para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="80d19-180">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="80d19-181">O alocador:</span><span class="sxs-lookup"><span data-stu-id="80d19-181">The factory:</span></span>

* <span data-ttu-id="80d19-182">Torna o registro de instâncias de `HttpClient` por cliente nomeado mais intuitivo.</span><span class="sxs-lookup"><span data-stu-id="80d19-182">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="80d19-183">Implementa um manipulador Polly que permite que políticas Polly sejam usadas para Retry, CircuitBreakers etc.</span><span class="sxs-lookup"><span data-stu-id="80d19-183">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="80d19-184">Para obter mais informações, veja [Iniciar solicitações de HTTP](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="80d19-184">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="80d19-185">Configuração de transporte do Kestrel</span><span class="sxs-lookup"><span data-stu-id="80d19-185">Kestrel transport configuration</span></span>

<span data-ttu-id="80d19-186">Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados.</span><span class="sxs-lookup"><span data-stu-id="80d19-186">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="80d19-187">Para saber mais, confira [Implementação do servidor Web Kestrel: configuração de transporte](xref:fundamentals/servers/kestrel#transport-configuration).</span><span class="sxs-lookup"><span data-stu-id="80d19-187">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="80d19-188">Construtor de host genérico</span><span class="sxs-lookup"><span data-stu-id="80d19-188">Generic host builder</span></span>

<span data-ttu-id="80d19-189">O Construtor de Host Genérico (`HostBuilder`) foi introduzido.</span><span class="sxs-lookup"><span data-stu-id="80d19-189">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="80d19-190">Este construtor pode ser usado para aplicativos que não processam solicitações HTTP (mensagens, tarefas em segundo plano etc.).</span><span class="sxs-lookup"><span data-stu-id="80d19-190">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="80d19-191">Para obter mais informações, veja [Host Genérico do .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="80d19-191">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="80d19-192">Modelos do SPA atualizados</span><span class="sxs-lookup"><span data-stu-id="80d19-192">Updated SPA templates</span></span>

<span data-ttu-id="80d19-193">Os modelos de Aplicativo de Página Única para Angular, React e React com Redux são atualizados para usar estruturas de projeto padrão e criar sistemas para cada estrutura.</span><span class="sxs-lookup"><span data-stu-id="80d19-193">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="80d19-194">O modelo Angular se baseia na CLI Angular e os modelos React baseiam-se em create-react-app.</span><span class="sxs-lookup"><span data-stu-id="80d19-194">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="80d19-195">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="80d19-195">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a><span data-ttu-id="80d19-196">Pesquisa de Razor Pages para ativos Razor</span><span class="sxs-lookup"><span data-stu-id="80d19-196">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="80d19-197">Na versão 2.1, as Razor Pages pesquisam ativos do Razor (como layouts e parciais) nos seguintes diretórios na ordem listada:</span><span class="sxs-lookup"><span data-stu-id="80d19-197">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="80d19-198">Pasta de Páginas atual.</span><span class="sxs-lookup"><span data-stu-id="80d19-198">Current Pages folder.</span></span>
1. <span data-ttu-id="80d19-199">*/Pages/Shared/*</span><span class="sxs-lookup"><span data-stu-id="80d19-199">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="80d19-200">*/Views/Shared/*</span><span class="sxs-lookup"><span data-stu-id="80d19-200">*/Views/Shared/*</span></span>

## <a name="razor-pages-in-an-area"></a><span data-ttu-id="80d19-201">Razor Pages em uma área</span><span class="sxs-lookup"><span data-stu-id="80d19-201">Razor Pages in an area</span></span>

<span data-ttu-id="80d19-202">Razor Pages agora são compatíveis com [áreas](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="80d19-202">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="80d19-203">Para ver um exemplo de áreas, crie um novo aplicativo Web Razor Pages com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="80d19-203">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="80d19-204">Um aplicativo Web Razor Pages com contas de usuário individuais inclui */Areas/Identity/Pages*.</span><span class="sxs-lookup"><span data-stu-id="80d19-204">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="80d19-205">Versão de compatibilidade do MVC</span><span class="sxs-lookup"><span data-stu-id="80d19-205">MVC compatibility version</span></span>

<span data-ttu-id="80d19-206">O método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permite que um aplicativo aceite ou recuse as possíveis alterações da falha de comportamento introduzidas no ASP.NET Core MVC 2.1 ou posteriores.</span><span class="sxs-lookup"><span data-stu-id="80d19-206">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="80d19-207">Para obter mais informações, consulte <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="80d19-207">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="80d19-208">Migrar de 2.0 para 2.1</span><span class="sxs-lookup"><span data-stu-id="80d19-208">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="80d19-209">Veja [Migrar do ASP.NET Core 2.0 para 2.1](xref:migration/20_21).</span><span class="sxs-lookup"><span data-stu-id="80d19-209">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="80d19-210">Informações adicionais</span><span class="sxs-lookup"><span data-stu-id="80d19-210">Additional information</span></span>

<span data-ttu-id="80d19-211">Para obter uma lista de alterações, vejas as [Notas de versão do ASP.NET Core 2.1](https://github.com/aspnet/Home/releases/tag/2.1.0).</span><span class="sxs-lookup"><span data-stu-id="80d19-211">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/aspnet/Home/releases/tag/2.1.0).</span></span>
