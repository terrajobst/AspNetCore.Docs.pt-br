---
title: Solucionar problemas e depurar projetos ASP.NET Core
author: Rick-Anderson
description: Compreenda e solucione problemas de avisos e erros com projetos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 73a73fb51571e5f7b706ff4b958217854750c1fb
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354703"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="33ef3-103">Solucionar problemas e depurar projetos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33ef3-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="33ef3-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="33ef3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="33ef3-105">Os links a seguir fornecem orientação para a solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="33ef3-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="33ef3-106">NDC Conference (Londres, 2018): diagnosticar problemas em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33ef3-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="33ef3-107">Blog do ASP.NET: solução de problemas de desempenho de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33ef3-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="33ef3-108">Avisos de SDK do .NET Core</span><span class="sxs-lookup"><span data-stu-id="33ef3-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="33ef3-109">As versões de 32 bits e 64 bits do SDK do .NET Core estão instaladas</span><span class="sxs-lookup"><span data-stu-id="33ef3-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="33ef3-110">Na caixa de diálogo **novo projeto** para ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="33ef3-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="33ef3-111">As versões de 32 bits e 64 bits do SDK do .NET Core estão instaladas.</span><span class="sxs-lookup"><span data-stu-id="33ef3-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="33ef3-112">Somente os modelos das versões de 64 bits instaladas em ' C:\\arquivos de programas\\dotnet\\SDK\\' são exibidos.</span><span class="sxs-lookup"><span data-stu-id="33ef3-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="33ef3-113">Esse aviso aparece quando as versões de 32 bits (x86) e 64 de bits (x64) do [SDK do .NET Core](https://www.microsoft.com/net/download/all) são instaladas.</span><span class="sxs-lookup"><span data-stu-id="33ef3-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="33ef3-114">Os motivos comuns pelos quais ambas as versões podem ser instaladas incluem:</span><span class="sxs-lookup"><span data-stu-id="33ef3-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="33ef3-115">Você baixou originalmente o instalador do SDK do .NET Core usando um computador de 32 bits, mas o copiou e o instalou em um computador de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="33ef3-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="33ef3-116">O SDK do .NET Core de 32 bits foi instalado por outro aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33ef3-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="33ef3-117">A versão errada foi baixada e instalada.</span><span class="sxs-lookup"><span data-stu-id="33ef3-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="33ef3-118">Desinstale o SDK do .NET Core de 32 bits para evitar esse aviso.</span><span class="sxs-lookup"><span data-stu-id="33ef3-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="33ef3-119">Desinstale o **painel de controle** > **programas e recursos** > **desinstalar ou alterar um programa**.</span><span class="sxs-lookup"><span data-stu-id="33ef3-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="33ef3-120">Se você entender por que o aviso ocorre e suas implicações, poderá ignorar o aviso.</span><span class="sxs-lookup"><span data-stu-id="33ef3-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="33ef3-121">O SDK do .NET Core é instalado em vários locais</span><span class="sxs-lookup"><span data-stu-id="33ef3-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="33ef3-122">Na caixa de diálogo **novo projeto** para ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="33ef3-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="33ef3-123">O SDK do .NET Core é instalado em vários locais.</span><span class="sxs-lookup"><span data-stu-id="33ef3-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="33ef3-124">Somente os modelos dos SDKs instalados em ' C:\\arquivos de programas\\dotnet\\SDK\\' são exibidos.</span><span class="sxs-lookup"><span data-stu-id="33ef3-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="33ef3-125">Você verá essa mensagem quando tiver pelo menos uma instalação do SDK do .NET Core em um diretório fora dos arquivos de *programa C:\\\\dotnet\\SDK\\* .</span><span class="sxs-lookup"><span data-stu-id="33ef3-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="33ef3-126">Geralmente isso acontece quando o SDK do .NET Core foi implantado em um computador usando copiar/colar em vez do instalador MSI.</span><span class="sxs-lookup"><span data-stu-id="33ef3-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="33ef3-127">Desinstale todos os SDKs e tempos de execução do .NET Core de 32 bits para evitar esse aviso.</span><span class="sxs-lookup"><span data-stu-id="33ef3-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="33ef3-128">Desinstale o **painel de controle** > **programas e recursos** > **desinstalar ou alterar um programa**.</span><span class="sxs-lookup"><span data-stu-id="33ef3-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="33ef3-129">Se você entender por que o aviso ocorre e suas implicações, poderá ignorar o aviso.</span><span class="sxs-lookup"><span data-stu-id="33ef3-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="33ef3-130">Nenhum SDK do .NET Core foi detectado</span><span class="sxs-lookup"><span data-stu-id="33ef3-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="33ef3-131">Na caixa de diálogo **novo projeto** do Visual Studio para ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="33ef3-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="33ef3-132">Nenhum SDK do .NET Core foi detectado, verifique se eles estão incluídos na variável de ambiente `PATH`.</span><span class="sxs-lookup"><span data-stu-id="33ef3-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="33ef3-133">Ao executar um comando `dotnet`, o aviso aparece como:</span><span class="sxs-lookup"><span data-stu-id="33ef3-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="33ef3-134">Não foi possível encontrar nenhum SDKs dotnet instalado.</span><span class="sxs-lookup"><span data-stu-id="33ef3-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="33ef3-135">Esses avisos aparecem quando a variável de ambiente `PATH` não aponta para nenhum SDK do .NET Core no computador.</span><span class="sxs-lookup"><span data-stu-id="33ef3-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="33ef3-136">Para solucionar esse problema:</span><span class="sxs-lookup"><span data-stu-id="33ef3-136">To resolve this problem:</span></span>

* <span data-ttu-id="33ef3-137">Instale o SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="33ef3-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="33ef3-138">Obtenha o instalador mais recente de [downloads do .net](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="33ef3-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="33ef3-139">Verifique se a variável de ambiente `PATH` aponta para o local onde o SDK está instalado (`C:\Program Files\dotnet\` para 64 bits/x64 ou `C:\Program Files (x86)\dotnet\` para 32 bits/x86).</span><span class="sxs-lookup"><span data-stu-id="33ef3-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="33ef3-140">Normalmente, o instalador do SDK define o `PATH`.</span><span class="sxs-lookup"><span data-stu-id="33ef3-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="33ef3-141">Sempre instale os mesmos SDKs de bits e tempos de execução no mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="33ef3-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="33ef3-142">SDK ausente após a instalação do pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="33ef3-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="33ef3-143">A instalação do [pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifica o `PATH` quando ele instala o tempo de execução do .NET Core para apontar para a versão de 32 bits (x86) do .NET Core (`C:\Program Files (x86)\dotnet\`).</span><span class="sxs-lookup"><span data-stu-id="33ef3-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="33ef3-144">Isso pode resultar na ausência de SDKs quando o comando .NET Core `dotnet` de 32 bits (x86) for usado ([nenhum SDK do .NET Core foi detectado](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="33ef3-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="33ef3-145">Para resolver esse problema, mova `C:\Program Files\dotnet\` para uma posição antes de `C:\Program Files (x86)\dotnet\` no `PATH`.</span><span class="sxs-lookup"><span data-stu-id="33ef3-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="33ef3-146">Obter dados de um aplicativo</span><span class="sxs-lookup"><span data-stu-id="33ef3-146">Obtain data from an app</span></span>

<span data-ttu-id="33ef3-147">Se um aplicativo for capaz de responder às solicitações, você poderá obter os seguintes dados do aplicativo usando o middleware:</span><span class="sxs-lookup"><span data-stu-id="33ef3-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="33ef3-148">Solicitar &ndash; método, esquema, host, pathbase, caminho, Cadeia de caracteres de consulta, cabeçalhos</span><span class="sxs-lookup"><span data-stu-id="33ef3-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="33ef3-149">Conexão &ndash; endereço IP remoto, porta remota, endereço IP local, porta local, certificado de cliente</span><span class="sxs-lookup"><span data-stu-id="33ef3-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="33ef3-150">Nome da &ndash; de identidade, nome de exibição</span><span class="sxs-lookup"><span data-stu-id="33ef3-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="33ef3-151">Definições de configuração</span><span class="sxs-lookup"><span data-stu-id="33ef3-151">Configuration settings</span></span>
* <span data-ttu-id="33ef3-152">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="33ef3-152">Environment variables</span></span>

<span data-ttu-id="33ef3-153">Coloque o seguinte código de [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) no início do pipeline de processamento de solicitação do método de `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="33ef3-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="33ef3-154">O ambiente é verificado antes que o middleware seja executado para garantir que o código seja executado apenas no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="33ef3-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="33ef3-155">Para obter o ambiente, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="33ef3-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="33ef3-156">Insira o `IHostingEnvironment` no método `Startup.Configure` e verifique o ambiente com a variável local.</span><span class="sxs-lookup"><span data-stu-id="33ef3-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="33ef3-157">O código de exemplo a seguir demonstra essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="33ef3-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="33ef3-158">Atribua o ambiente a uma propriedade na classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="33ef3-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="33ef3-159">Verifique o ambiente usando a propriedade (por exemplo, `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="33ef3-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="33ef3-160">Depurar ASP.NET Core aplicativos</span><span class="sxs-lookup"><span data-stu-id="33ef3-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="33ef3-161">Os links a seguir fornecem informações sobre como depurar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="33ef3-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="33ef3-162">Depuração do ASP Core no Linux</span><span class="sxs-lookup"><span data-stu-id="33ef3-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="33ef3-163">Depuração do .NET Core no UNIX por SSH</span><span class="sxs-lookup"><span data-stu-id="33ef3-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="33ef3-164">Início rápido: Depurar ASP.NET com o depurador do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33ef3-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="33ef3-165">Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/2960) para obter mais informações de depuração.</span><span class="sxs-lookup"><span data-stu-id="33ef3-165">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
