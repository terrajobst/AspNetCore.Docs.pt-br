---
title: Solucionar problemas de projetos ASP.NET Core
author: Rick-Anderson
description: Compreenda e solucione problemas de avisos e erros com projetos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: b434af2dd046045836d2f6f7f7b7b2d57699bedc
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308276"
---
# <a name="troubleshoot-aspnet-core-projects"></a><span data-ttu-id="d7389-103">Solucionar problemas de projetos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7389-103">Troubleshoot ASP.NET Core projects</span></span>

<span data-ttu-id="d7389-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d7389-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d7389-105">Os links a seguir fornecem orientação para a solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="d7389-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="d7389-106">NDC Conference (Londres, 2018): Diagnosticar problemas em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7389-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="d7389-107">Blog do ASP.NET: Solução de problemas de desempenho ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7389-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="d7389-108">Avisos de SDK do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d7389-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="d7389-109">As versões de 32 bits e 64 bits do SDK do .NET Core estão instaladas</span><span class="sxs-lookup"><span data-stu-id="d7389-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="d7389-110">Na caixa de diálogo **novo projeto** para ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="d7389-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="d7389-111">As versões de 32 bits e 64 bits do SDK do .NET Core estão instaladas.</span><span class="sxs-lookup"><span data-stu-id="d7389-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="d7389-112">Somente os modelos das versões de 64 bits instaladas em ' C\\: arquivos\\de\\programas\\SDK do dotnet ' são exibidos.</span><span class="sxs-lookup"><span data-stu-id="d7389-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="d7389-113">Esse aviso aparece quando as versões de 32 bits (x86) e 64 de bits (x64) do [SDK do .NET Core](https://www.microsoft.com/net/download/all) são instaladas.</span><span class="sxs-lookup"><span data-stu-id="d7389-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="d7389-114">Os motivos comuns pelos quais ambas as versões podem ser instaladas incluem:</span><span class="sxs-lookup"><span data-stu-id="d7389-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="d7389-115">Você baixou originalmente o instalador do SDK do .NET Core usando um computador de 32 bits, mas o copiou e o instalou em um computador de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="d7389-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="d7389-116">O SDK do .NET Core de 32 bits foi instalado por outro aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d7389-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="d7389-117">A versão errada foi baixada e instalada.</span><span class="sxs-lookup"><span data-stu-id="d7389-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="d7389-118">Desinstale o SDK do .NET Core de 32 bits para evitar esse aviso.</span><span class="sxs-lookup"><span data-stu-id="d7389-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="d7389-119">Desinstalar de**programas e recursos** > do **painel** > de controle**desinstalar ou alterar um programa**.</span><span class="sxs-lookup"><span data-stu-id="d7389-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="d7389-120">Se você entender por que o aviso ocorre e suas implicações, poderá ignorar o aviso.</span><span class="sxs-lookup"><span data-stu-id="d7389-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="d7389-121">O SDK do .NET Core é instalado em vários locais</span><span class="sxs-lookup"><span data-stu-id="d7389-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="d7389-122">Na caixa de diálogo **novo projeto** para ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="d7389-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="d7389-123">O SDK do .NET Core é instalado em vários locais.</span><span class="sxs-lookup"><span data-stu-id="d7389-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="d7389-124">Somente os modelos dos SDKs instalados em ' C:\\arquivos\\de programas\\SDK\\do dotnet ' são exibidos.</span><span class="sxs-lookup"><span data-stu-id="d7389-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="d7389-125">Você verá essa mensagem quando tiver pelo menos uma instalação do SDK do .NET Core em um diretório fora de *C:\\arquivos\\de\\programas SDK do\\dotnet*.</span><span class="sxs-lookup"><span data-stu-id="d7389-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="d7389-126">Geralmente isso acontece quando o SDK do .NET Core foi implantado em um computador usando copiar/colar em vez do instalador MSI.</span><span class="sxs-lookup"><span data-stu-id="d7389-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="d7389-127">Desinstale todos os SDKs e tempos de execução do .NET Core de 32 bits para evitar esse aviso.</span><span class="sxs-lookup"><span data-stu-id="d7389-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="d7389-128">Desinstalar de**programas e recursos** > do **painel** > de controle**desinstalar ou alterar um programa**.</span><span class="sxs-lookup"><span data-stu-id="d7389-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="d7389-129">Se você entender por que o aviso ocorre e suas implicações, poderá ignorar o aviso.</span><span class="sxs-lookup"><span data-stu-id="d7389-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="d7389-130">Nenhum SDK do .NET Core foi detectado</span><span class="sxs-lookup"><span data-stu-id="d7389-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="d7389-131">Na caixa de diálogo **novo projeto** do Visual Studio para ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="d7389-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="d7389-132">Nenhum SDK do .NET Core foi detectado, verifique se eles estão incluídos na variável `PATH`de ambiente.</span><span class="sxs-lookup"><span data-stu-id="d7389-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="d7389-133">Ao executar um `dotnet` comando, o aviso aparece como:</span><span class="sxs-lookup"><span data-stu-id="d7389-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="d7389-134">Não foi possível encontrar nenhum SDKs dotnet instalado.</span><span class="sxs-lookup"><span data-stu-id="d7389-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="d7389-135">Esses avisos aparecem quando a variável `PATH` de ambiente não aponta para nenhum SDK do .NET Core no computador.</span><span class="sxs-lookup"><span data-stu-id="d7389-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="d7389-136">Para resolver esse problema:</span><span class="sxs-lookup"><span data-stu-id="d7389-136">To resolve this problem:</span></span>

* <span data-ttu-id="d7389-137">Instale o SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7389-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="d7389-138">Obtenha o instalador mais recente de [downloads do .net](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="d7389-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="d7389-139">Verifique se a `PATH` variável de ambiente aponta para o local onde o SDK está instalado`C:\Program Files\dotnet\` (para 64 bits/x64 ou `C:\Program Files (x86)\dotnet\` para 32 bits/x86).</span><span class="sxs-lookup"><span data-stu-id="d7389-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="d7389-140">O instalador do SDK normalmente define `PATH`o.</span><span class="sxs-lookup"><span data-stu-id="d7389-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="d7389-141">Sempre instale os mesmos SDKs de bits e tempos de execução no mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="d7389-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="d7389-142">SDK ausente após a instalação do pacote de hospedagem do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d7389-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="d7389-143">A instalação do [pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifica o `PATH` quando ele instala o tempo de execução do .NET Core para apontar para a versão de 32 bits (x86) do .NET Core (`C:\Program Files (x86)\dotnet\`).</span><span class="sxs-lookup"><span data-stu-id="d7389-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="d7389-144">Isso pode resultar na ausência de SDKs quando o comando .NET Core `dotnet` de 32 bits (x86) for usado ([nenhum SDK do .NET Core foi detectado](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="d7389-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="d7389-145">Para resolver esse problema, mova `C:\Program Files\dotnet\` para uma posição anterior `C:\Program Files (x86)\dotnet\` no `PATH`.</span><span class="sxs-lookup"><span data-stu-id="d7389-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="d7389-146">Obter dados de um aplicativo</span><span class="sxs-lookup"><span data-stu-id="d7389-146">Obtain data from an app</span></span>

<span data-ttu-id="d7389-147">Se um aplicativo for capaz de responder às solicitações, você poderá obter os seguintes dados do aplicativo usando o middleware:</span><span class="sxs-lookup"><span data-stu-id="d7389-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="d7389-148">Método &ndash; de solicitação, esquema, host, pathbase, caminho, Cadeia de caracteres de consulta, cabeçalhos</span><span class="sxs-lookup"><span data-stu-id="d7389-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="d7389-149">Endereço &ndash; IP remoto de conexão, porta remota, endereço IP local, porta local, certificado de cliente</span><span class="sxs-lookup"><span data-stu-id="d7389-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="d7389-150">Nome &ndash; da identidade, nome de exibição</span><span class="sxs-lookup"><span data-stu-id="d7389-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="d7389-151">Definições de configuração</span><span class="sxs-lookup"><span data-stu-id="d7389-151">Configuration settings</span></span>
* <span data-ttu-id="d7389-152">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="d7389-152">Environment variables</span></span>

<span data-ttu-id="d7389-153">Coloque o seguinte código de [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) no início do `Startup.Configure` pipeline de processamento de solicitação do método.</span><span class="sxs-lookup"><span data-stu-id="d7389-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="d7389-154">O ambiente é verificado antes que o middleware seja executado para garantir que o código seja executado apenas no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d7389-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="d7389-155">Para obter o ambiente, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="d7389-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="d7389-156">Insira o `IHostingEnvironment` `Startup.Configure` no método e verifique o ambiente com a variável local.</span><span class="sxs-lookup"><span data-stu-id="d7389-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="d7389-157">O código de exemplo a seguir demonstra essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="d7389-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="d7389-158">Atribua o ambiente a uma propriedade na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="d7389-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="d7389-159">Verifique o ambiente usando a propriedade (por exemplo, `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="d7389-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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
