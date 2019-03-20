---
title: Solucionar problemas de projetos do ASP.NET Core
author: Rick-Anderson
description: Compreenda e solucione problemas de avisos e erros com projetos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: test/troubleshoot
ms.openlocfilehash: 1f53375a6aa504c989920a0bff83b400b7bd83ec
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208837"
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

![Uma captura de tela da caixa de diálogo OneASP.NET mostrando a mensagem de aviso](troubleshoot/_static/both32and64bit.png)

Esse aviso é exibido quando (x86) 32 bits e 64 bits (x64) versões dos [SDK do .NET Core](https://www.microsoft.com/net/download/all) estão instalados. Ambas as versões podem ser instaladas os motivos comuns incluem:

* Você originalmente baixou o instalador do SDK do .NET Core usando um computador de 32 bits, mas, em seguida, copiado entre e instalado em um computador de 64 bits.
* O .NET Core SDK de 32 bits foi instalado por outro aplicativo.
* A versão incorreta foi baixada e instalada.

Desinstale o .NET Core SDK 32 bits para evitar esse aviso. Desinstalar do **painel de controle** > **programas e recursos** > **desinstalar ou alterar um programa**. Se você entender por que o aviso ocorre e suas implicações, você pode ignorar o aviso.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>SDK do .NET Core é instalado em vários locais

No **novo projeto** caixa de diálogo para o ASP.NET Core, você poderá ver o seguinte aviso:

> SDK do .NET Core é instalado em vários locais. Somente modelos dos SDKs instalados em ' c:\\Program Files\\dotnet\\sdk\\' será exibida.

![Uma captura de tela da caixa de diálogo OneASP.NET mostrando a mensagem de aviso](troubleshoot/_static/multiplelocations.png)

Você verá esta mensagem quando você tem pelo menos uma instalação do SDK do .NET Core em um diretório fora do *c:\\arquivos de programas\\dotnet\\sdk\\*. Geralmente isso acontece quando o SDK do .NET Core foi implantado em um computador usando copiar/colar em vez do instalador MSI.

Desinstale todos os 32 bits SDKs do .NET Core e tempos de execução para evitar esse aviso. Desinstalar do **painel de controle** > **programas e recursos** > **desinstalar ou alterar um programa**. Se você entender por que o aviso ocorre e suas implicações, você pode ignorar o aviso.

### <a name="no-net-core-sdks-were-detected"></a>Não há SDKs do .NET Core foram detectados

No **novo projeto** caixa de diálogo para o ASP.NET Core, você poderá ver o seguinte aviso:

> Nenhum SDK do .NET Core foi detectado, verifique se que eles estão incluídos na variável de ambiente 'PATH'.

![Uma captura de tela da caixa de diálogo OneASP.NET mostrando a mensagem de aviso](troubleshoot/_static/NoNetCore.png)

Esse aviso é exibido quando a variável de ambiente `PATH` não apontar para qualquer SDKs do .NET Core no computador (por exemplo, `C:\Program Files\dotnet\` e `C:\Program Files (x86)\dotnet\`). Para resolver esse problema:

* Instale ou verifique se o que SDK do .NET Core é instalado. Obter o instalador mais recente do [Downloads do .NET](https://dotnet.microsoft.com/download). 
* Verifique o `PATH` variável de ambiente aponta para o local em que o SDK está instalado. O instalador normalmente define o `PATH`.

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
