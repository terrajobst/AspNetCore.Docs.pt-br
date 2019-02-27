---
title: Solucionar problemas de projetos do ASP.NET Core
author: Rick-Anderson
description: Compreenda e solucione problemas de avisos e erros com projetos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: test/troubleshoot
ms.openlocfilehash: c8b34f51fd329eb9a7c34f7be93bd7f2aa054283
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899275"
---
# <a name="troubleshoot-aspnet-core-projects"></a><span data-ttu-id="e5d79-103">Solucionar problemas de projetos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e5d79-103">Troubleshoot ASP.NET Core projects</span></span>

<span data-ttu-id="e5d79-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e5d79-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e5d79-105">Os links a seguir fornecem diretrizes de solução de problemas:</span><span class="sxs-lookup"><span data-stu-id="e5d79-105">The following links provide troubleshooting guidance:</span></span>

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="e5d79-106">NDC Conference (2018 Londres): Diagnosticar problemas em aplicativos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e5d79-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="e5d79-107">Blog do ASP.NET: Solucionando problemas de desempenho do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e5d79-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="e5d79-108">Avisos do SDK do .NET core</span><span class="sxs-lookup"><span data-stu-id="e5d79-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="e5d79-109">Os 32 bits e versões de 64 bits do SDK do .NET Core estão instaladas</span><span class="sxs-lookup"><span data-stu-id="e5d79-109">Both the 32 bit and 64 bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="e5d79-110">No **novo projeto** caixa de diálogo para o ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="e5d79-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="e5d79-111">As versões de bit 32 e 64 do SDK do .NET Core são instaladas.</span><span class="sxs-lookup"><span data-stu-id="e5d79-111">Both 32 and 64 bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="e5d79-112">Somente modelos das versões de 64 bits instalados em ' c:\\Program Files\\dotnet\\sdk\\' será exibida.</span><span class="sxs-lookup"><span data-stu-id="e5d79-112">Only templates from the 64 bit version(s) installed at 'C:\\Program Files\\dotnet\\sdk\\' will be displayed.</span></span>

![Uma captura de tela da caixa de diálogo OneASP.NET mostrando a mensagem de aviso](troubleshoot/_static/both32and64bit.png)

<span data-ttu-id="e5d79-114">Esse aviso é exibido quando (x86) 32 bits e 64 bits (x64) versões dos [SDK do .NET Core](https://www.microsoft.com/net/download/all) estão instalados.</span><span class="sxs-lookup"><span data-stu-id="e5d79-114">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="e5d79-115">Ambas as versões podem ser instaladas os motivos comuns incluem:</span><span class="sxs-lookup"><span data-stu-id="e5d79-115">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="e5d79-116">Você originalmente baixou o instalador do SDK do .NET Core usando um computador de 32 bits, mas, em seguida, copiado entre e instalado em um computador de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="e5d79-116">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="e5d79-117">O .NET Core SDK de 32 bits foi instalado por outro aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e5d79-117">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="e5d79-118">A versão incorreta foi baixada e instalada.</span><span class="sxs-lookup"><span data-stu-id="e5d79-118">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="e5d79-119">Desinstale o .NET Core SDK 32 bits para evitar esse aviso.</span><span class="sxs-lookup"><span data-stu-id="e5d79-119">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="e5d79-120">Desinstalar do **painel de controle** > **programas e recursos** > **desinstalar ou alterar um programa**.</span><span class="sxs-lookup"><span data-stu-id="e5d79-120">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="e5d79-121">Se você entender por que o aviso ocorre e suas implicações, você pode ignorar o aviso.</span><span class="sxs-lookup"><span data-stu-id="e5d79-121">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="e5d79-122">SDK do .NET Core é instalado em vários locais</span><span class="sxs-lookup"><span data-stu-id="e5d79-122">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="e5d79-123">No **novo projeto** caixa de diálogo para o ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="e5d79-123">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="e5d79-124">SDK do .NET Core é instalado em vários locais.</span><span class="sxs-lookup"><span data-stu-id="e5d79-124">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="e5d79-125">Somente modelos dos SDKs instalados em ' c:\\Program Files\\dotnet\\sdk\\' será exibida.</span><span class="sxs-lookup"><span data-stu-id="e5d79-125">Only templates from the SDK(s) installed at 'C:\\Program Files\\dotnet\\sdk\\' will be displayed.</span></span>

![Uma captura de tela da caixa de diálogo OneASP.NET mostrando a mensagem de aviso](troubleshoot/_static/multiplelocations.png)

<span data-ttu-id="e5d79-127">Você verá esta mensagem quando você tem pelo menos uma instalação do SDK do .NET Core em um diretório fora do *c:\\arquivos de programas\\dotnet\\sdk\\*.</span><span class="sxs-lookup"><span data-stu-id="e5d79-127">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="e5d79-128">Geralmente isso acontece quando o SDK do .NET Core foi implantado em um computador usando copiar/colar em vez do instalador MSI.</span><span class="sxs-lookup"><span data-stu-id="e5d79-128">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="e5d79-129">Desinstale o .NET Core SDK 32 bits para evitar esse aviso.</span><span class="sxs-lookup"><span data-stu-id="e5d79-129">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="e5d79-130">Desinstalar do **painel de controle** > **programas e recursos** > **desinstalar ou alterar um programa**.</span><span class="sxs-lookup"><span data-stu-id="e5d79-130">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="e5d79-131">Se você entender por que o aviso ocorre e suas implicações, você pode ignorar o aviso.</span><span class="sxs-lookup"><span data-stu-id="e5d79-131">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="e5d79-132">Não há SDKs do .NET Core foram detectados</span><span class="sxs-lookup"><span data-stu-id="e5d79-132">No .NET Core SDKs were detected</span></span>

<span data-ttu-id="e5d79-133">No **novo projeto** caixa de diálogo para o ASP.NET Core, você poderá ver o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="e5d79-133">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="e5d79-134">Nenhum SDK do .NET Core foi detectado, verifique se que eles estão incluídos na variável de ambiente 'PATH'.</span><span class="sxs-lookup"><span data-stu-id="e5d79-134">No .NET Core SDKs were detected, ensure they are included in the environment variable 'PATH'.</span></span>

![Uma captura de tela da caixa de diálogo OneASP.NET mostrando a mensagem de aviso](troubleshoot/_static/NoNetCore.png)

<span data-ttu-id="e5d79-136">Esse aviso é exibido quando a variável de ambiente `PATH` não apontar para qualquer SDKs do .NET Core no computador (por exemplo, `C:\Program Files\dotnet\` e `C:\Program Files (x86)\dotnet\`).</span><span class="sxs-lookup"><span data-stu-id="e5d79-136">This warning appears when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine (for example, `C:\Program Files\dotnet\` and `C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="e5d79-137">Para resolver esse problema:</span><span class="sxs-lookup"><span data-stu-id="e5d79-137">To resolve this problem:</span></span>

* <span data-ttu-id="e5d79-138">Instale ou verifique se o que SDK do .NET Core é instalado.</span><span class="sxs-lookup"><span data-stu-id="e5d79-138">Install or verify the .NET Core SDK is installed.</span></span> <span data-ttu-id="e5d79-139">Obter o instalador mais recente do [Downloads do .NET](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="e5d79-139">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span> 
* <span data-ttu-id="e5d79-140">Verifique o `PATH` variável de ambiente aponta para o local em que o SDK está instalado.</span><span class="sxs-lookup"><span data-stu-id="e5d79-140">Verify that the `PATH` environment variable points to the location where the SDK is installed.</span></span> <span data-ttu-id="e5d79-141">O instalador normalmente define o `PATH`.</span><span class="sxs-lookup"><span data-stu-id="e5d79-141">The installer normally sets the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="e5d79-142">Obter dados de um aplicativo</span><span class="sxs-lookup"><span data-stu-id="e5d79-142">Obtain data from an app</span></span>

<span data-ttu-id="e5d79-143">Se um aplicativo é capaz de responder a solicitações, você pode obter os seguintes dados do aplicativo usando o middleware:</span><span class="sxs-lookup"><span data-stu-id="e5d79-143">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="e5d79-144">Solicitar &ndash; cadeia, cabeçalhos de consulta de método, esquema, host, pathbase, caminho,</span><span class="sxs-lookup"><span data-stu-id="e5d79-144">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="e5d79-145">Conexão &ndash; endereço IP remoto, porta remota, endereço IP local, porta local, o certificado de cliente</span><span class="sxs-lookup"><span data-stu-id="e5d79-145">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="e5d79-146">Identidade &ndash; nome, nome de exibição</span><span class="sxs-lookup"><span data-stu-id="e5d79-146">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="e5d79-147">Definições de configuração</span><span class="sxs-lookup"><span data-stu-id="e5d79-147">Configuration settings</span></span>
* <span data-ttu-id="e5d79-148">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="e5d79-148">Environment variables</span></span>

<span data-ttu-id="e5d79-149">Coloque o seguinte [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) código no início do `Startup.Configure` pipeline de processamento de solicitação do método.</span><span class="sxs-lookup"><span data-stu-id="e5d79-149">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="e5d79-150">O ambiente é verificado antes do middleware é executado para garantir que o código só é executado no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e5d79-150">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="e5d79-151">Para obter o ambiente, use qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e5d79-151">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="e5d79-152">Injetar o `IHostingEnvironment` para o `Startup.Configure` método e verifique se o ambiente com a variável local.</span><span class="sxs-lookup"><span data-stu-id="e5d79-152">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="e5d79-153">O código de exemplo a seguir demonstra essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="e5d79-153">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="e5d79-154">Atribuir o ambiente para uma propriedade no `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="e5d79-154">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="e5d79-155">Verificar o ambiente usando a propriedade (por exemplo, `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="e5d79-155">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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
