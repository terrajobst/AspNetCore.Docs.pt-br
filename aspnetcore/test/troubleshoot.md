---
title: Solucionar problemas e depurar projetos ASP.NET Core
author: Rick-Anderson
description: Compreenda e solucione problemas de avisos e erros com projetos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511503"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Solucionar problemas e depurar projetos ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Os links a seguir fornecem orientação para a solução de problemas:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC Conference (Londres, 2018): diagnosticar problemas em aplicativos ASP.NET Core](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [Blog do ASP.NET: solução de problemas de desempenho de ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>Avisos de SDK do .NET Core

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>As versões de 32 bits e 64 bits do SDK do .NET Core estão instaladas

Na caixa de diálogo **novo projeto** para ASP.NET Core, você poderá ver o seguinte aviso:

> As versões de 32 bits e 64 bits do SDK do .NET Core estão instaladas. Somente os modelos das versões de 64 bits instaladas em ' C:\\arquivos de programas\\dotnet\\SDK\\' são exibidos.

Esse aviso aparece quando as versões de 32 bits (x86) e 64 de bits (x64) do [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) são instaladas. Os motivos comuns pelos quais ambas as versões podem ser instaladas incluem:

* Você baixou originalmente o instalador do SDK do .NET Core usando um computador de 32 bits, mas o copiou e o instalou em um computador de 64 bits.
* O SDK do .NET Core de 32 bits foi instalado por outro aplicativo.
* A versão errada foi baixada e instalada.

Desinstale o SDK do .NET Core de 32 bits para evitar esse aviso. Desinstale o **painel de controle** > **programas e recursos** > **desinstalar ou alterar um programa**. Se você entender por que o aviso ocorre e suas implicações, poderá ignorar o aviso.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>O SDK do .NET Core é instalado em vários locais

Na caixa de diálogo **novo projeto** para ASP.NET Core, você poderá ver o seguinte aviso:

> O SDK do .NET Core é instalado em vários locais. Somente os modelos dos SDKs instalados em ' C:\\arquivos de programas\\dotnet\\SDK\\' são exibidos.

Você verá essa mensagem quando tiver pelo menos uma instalação do SDK do .NET Core em um diretório fora dos arquivos de *programa C:\\\\dotnet\\SDK\\* . Geralmente isso acontece quando o SDK do .NET Core foi implantado em um computador usando copiar/colar em vez do instalador MSI.

Desinstale todos os SDKs e tempos de execução do .NET Core de 32 bits para evitar esse aviso. Desinstale o **painel de controle** > **programas e recursos** > **desinstalar ou alterar um programa**. Se você entender por que o aviso ocorre e suas implicações, poderá ignorar o aviso.

### <a name="no-net-core-sdks-were-detected"></a>Nenhum SDK do .NET Core foi detectado

* Na caixa de diálogo **novo projeto** do Visual Studio para ASP.NET Core, você poderá ver o seguinte aviso:

  > Nenhum SDK do .NET Core foi detectado, verifique se eles estão incluídos na variável de ambiente `PATH`.

* Ao executar um comando `dotnet`, o aviso aparece como:

  > Não foi possível encontrar nenhum SDKs dotnet instalado.

Esses avisos aparecem quando a variável de ambiente `PATH` não aponta para nenhum SDK do .NET Core no computador. Para solucionar esse problema:

* Instale o SDK do .NET Core. Obtenha o instalador mais recente de [downloads do .net](https://dotnet.microsoft.com/download).
* Verifique se a variável de ambiente `PATH` aponta para o local onde o SDK está instalado (`C:\Program Files\dotnet\` para 64 bits/x64 ou `C:\Program Files (x86)\dotnet\` para 32 bits/x86). Normalmente, o instalador do SDK define o `PATH`. Sempre instale os mesmos SDKs de bits e tempos de execução no mesmo computador.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>SDK ausente após a instalação do pacote de hospedagem do .NET Core

A instalação do [pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifica o `PATH` quando ele instala o tempo de execução do .NET Core para apontar para a versão de 32 bits (x86) do .NET Core (`C:\Program Files (x86)\dotnet\`). Isso pode resultar na ausência de SDKs quando o comando .NET Core `dotnet` de 32 bits (x86) for usado ([nenhum SDK do .NET Core foi detectado](#no-net-core-sdks-were-detected)). Para resolver esse problema, mova `C:\Program Files\dotnet\` para uma posição antes de `C:\Program Files (x86)\dotnet\` no `PATH`.

## <a name="obtain-data-from-an-app"></a>Obter dados de um aplicativo

Se um aplicativo for capaz de responder às solicitações, você poderá obter os seguintes dados do aplicativo usando o middleware:

* Solicitar &ndash; método, esquema, host, pathbase, caminho, Cadeia de caracteres de consulta, cabeçalhos
* Conexão &ndash; endereço IP remoto, porta remota, endereço IP local, porta local, certificado de cliente
* Nome da &ndash; de identidade, nome de exibição
* Definições de configuração
* Variáveis de ambiente

Coloque o seguinte código de [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) no início do pipeline de processamento de solicitação do método de `Startup.Configure`. O ambiente é verificado antes que o middleware seja executado para garantir que o código seja executado apenas no ambiente de desenvolvimento.

Para obter o ambiente, use uma das seguintes abordagens:

* Insira o `IHostingEnvironment` no método `Startup.Configure` e verifique o ambiente com a variável local. O código de exemplo a seguir demonstra essa abordagem.

* Atribua o ambiente a uma propriedade na classe `Startup`. Verifique o ambiente usando a propriedade (por exemplo, `if (Environment.IsDevelopment())`).

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

## <a name="debug-aspnet-core-apps"></a>Depurar ASP.NET Core aplicativos

Os links a seguir fornecem informações sobre como depurar aplicativos ASP.NET Core.

* [Depuração do ASP Core no Linux](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Depuração do .NET Core no UNIX por SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Início rápido: Depurar ASP.NET com o depurador do Visual Studio](/visualstudio/debugger/quickstart-debug-aspnet)
* Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) para obter mais informações de depuração.
