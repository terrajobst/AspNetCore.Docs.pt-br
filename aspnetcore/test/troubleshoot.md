---
title: Solucionar problemas de projetos do ASP.NET Core
author: Rick-Anderson
description: Compreenda e solucione problemas de avisos e erros com projetos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/13/2019
uid: test/troubleshoot
ms.openlocfilehash: 3d755b2f0c509d65dea86bbe719e42935d87d546
ms.sourcegitcommit: 687ffb15ebe65379f75c84739ea851d5a0d788b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488735"
---
# <a name="troubleshoot-aspnet-core-projects"></a>Solucionar problemas de projetos do ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Os links a seguir fornecem diretrizes de solução de problemas:

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC Conference (2018 Londres): Diagnosticar problemas em aplicativos do ASP.NET Core](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [Blog do ASP.NET: Solucionando problemas de desempenho do ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>Avisos do SDK do .NET core

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Os 32 bits e versões de 64 bits do SDK do .NET Core estão instaladas

No **novo projeto** caixa de diálogo para o ASP.NET Core, você poderá ver o seguinte aviso:

> As versões de bit 32 e 64 do SDK do .NET Core são instaladas. Somente modelos das versões de 64 bits instalados em ' c:\\Program Files\\dotnet\\sdk\\' será exibida.

Esse aviso é exibido quando (x86) 32 bits e 64 bits (x64) versões dos [SDK do .NET Core](https://www.microsoft.com/net/download/all) estão instalados. Ambas as versões podem ser instaladas os motivos comuns incluem:

* Você originalmente baixou o instalador do SDK do .NET Core usando um computador de 32 bits, mas, em seguida, copiado entre e instalado em um computador de 64 bits.
* O .NET Core SDK de 32 bits foi instalado por outro aplicativo.
* A versão incorreta foi baixada e instalada.

Desinstale o .NET Core SDK 32 bits para evitar esse aviso. Desinstalar do **painel de controle** > **programas e recursos** > **desinstalar ou alterar um programa**. Se você entender por que o aviso ocorre e suas implicações, você pode ignorar o aviso.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>SDK do .NET Core é instalado em vários locais

No **novo projeto** caixa de diálogo para o ASP.NET Core, você poderá ver o seguinte aviso:

> SDK do .NET Core é instalado em vários locais. Somente modelos dos SDKs instalados em ' c:\\Program Files\\dotnet\\sdk\\' será exibida.

Você verá esta mensagem quando você tem pelo menos uma instalação do SDK do .NET Core em um diretório fora do *c:\\arquivos de programas\\dotnet\\sdk\\*. Geralmente isso acontece quando o SDK do .NET Core foi implantado em um computador usando copiar/colar em vez do instalador MSI.

Desinstale todos os 32 bits SDKs do .NET Core e tempos de execução para evitar esse aviso. Desinstalar do **painel de controle** > **programas e recursos** > **desinstalar ou alterar um programa**. Se você entender por que o aviso ocorre e suas implicações, você pode ignorar o aviso.

### <a name="no-net-core-sdks-were-detected"></a>Não há SDKs do .NET Core foram detectados

* No Visual Studio **novo projeto** caixa de diálogo para o ASP.NET Core, você poderá ver o seguinte aviso:

  > Nenhum SDK do .NET Core foi detectado, verifique se eles estão incluídos na variável de ambiente `PATH`.

* Ao executar um `dotnet` de comando, o aviso é exibido como:

  > Não foi possível encontrar qualquer dotnet instalado de SDKs.

Esses avisos são exibidos quando a variável de ambiente `PATH` não apontar para qualquer SDKs do .NET Core no computador. Para resolver esse problema:

* Instale o SDK do .NET Core. Obter o instalador mais recente do [Downloads do .NET](https://dotnet.microsoft.com/download).
* Verifique se que o `PATH` variável de ambiente aponta para o local em que o SDK está instalado (`C:\Program Files\dotnet\` para 64-bit/x64 ou `C:\Program Files (x86)\dotnet\` para/x86 de 32-bit). O instalador do SDK normalmente define o `PATH`. Sempre instale o mesmo número de bits SDKs e tempos de execução no mesmo computador.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>SDK ausente depois de instalar o pacote de hospedagem do .NET Core

Instalando o [pacote de hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifica a `PATH` quando ele instala o tempo de execução do .NET Core para apontar para a versão de 32 bits (x86) do .NET Core (`C:\Program Files (x86)\dotnet\`). Isso pode resultar na ausência de SDKs quando o .NET Core de 32 bits (x86) `dotnet` comando é usado ([SDKs do .NET Core não foram detectados](#no-net-core-sdks-were-detected)). Para resolver esse problema, mova `C:\Program Files\dotnet\` uma posição antes `C:\Program Files (x86)\dotnet\` sobre o `PATH`.

## <a name="obtain-data-from-an-app"></a>Obter dados de um aplicativo

Se um aplicativo é capaz de responder a solicitações, você pode obter os seguintes dados do aplicativo usando o middleware:

* Solicitar &ndash; cadeia, cabeçalhos de consulta de método, esquema, host, pathbase, caminho,
* Conexão &ndash; endereço IP remoto, porta remota, endereço IP local, porta local, o certificado de cliente
* Identidade &ndash; nome, nome de exibição
* Definições de configuração
* Variáveis de ambiente

Coloque o seguinte [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) código no início do `Startup.Configure` pipeline de processamento de solicitação do método. O ambiente é verificado antes do middleware é executado para garantir que o código só é executado no ambiente de desenvolvimento.

Para obter o ambiente, use qualquer uma das seguintes abordagens:

* Injetar o `IHostingEnvironment` para o `Startup.Configure` método e verifique se o ambiente com a variável local. O código de exemplo a seguir demonstra essa abordagem.

* Atribuir o ambiente para uma propriedade no `Startup` classe. Verificar o ambiente usando a propriedade (por exemplo, `if (Environment.IsDevelopment())`).

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
