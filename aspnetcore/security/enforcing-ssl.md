---
title: Impor HTTPS no ASP.NET Core
author: rick-anderson
description: Aprenda a exigir HTTPS/TLS em um aplicativo web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: security/enforcing-ssl
ms.openlocfilehash: 0c3add9c8860a47932cda3a8b07c83dc774bf1f1
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098968"
---
# <a name="enforce-https-in-aspnet-core"></a>Impor HTTPS no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Este documento demonstra como:

* Exigir HTTPS para todas as solicitações.
* Redirecione todas as solicitações HTTP para HTTPS.

Nenhuma API pode impedir que um cliente envie dados confidenciais na primeira solicitação.

> [!WARNING]
> Fazer **não** usar [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) em APIs da Web que recebe informações confidenciais. `RequireHttpsAttribute` usa códigos de status HTTP para redirecionar navegadores de HTTP para HTTPS. Os clientes da API não podem compreender ou obedecem redirecionamentos de HTTP para HTTPS. Esses clientes podem enviar informações por meio de HTTP. As APIs da Web deverá:
>
> * Não realizar a escuta em HTTP.
> * Feche a conexão com o código de status 400 (solicitação incorreta) e não atender à solicitação.

## <a name="require-https"></a>Exigir HTTPS

::: moniker range=">= aspnetcore-2.1"

É recomendável que produção do ASP.NET Core chamada de aplicativos web:

* Middleware de redirecionamento de HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) para redirecionar solicitações HTTP para HTTPS.
* Middleware HSTS ([UseHsts](#http-strict-transport-security-protocol-hsts)) para enviar cabeçalhos de protocolo de segurança de transporte estrito (HSTS) HTTP aos clientes.

> [!NOTE]
> Aplicativos implantados em uma configuração de proxy reverso permitem que o proxy lidar com a segurança de conexão (HTTPS). Se o proxy também manipula o redirecionamento de HTTPS, não é necessário usar o Middleware de redirecionamento de HTTPS. Se o servidor proxy também manipula a HSTS cabeçalhos de gravação (por exemplo, [dar suporte a HSTS nativos no IIS 10.0 (1709) ou posterior](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), Middleware HSTS não é necessário para o aplicativo. Para obter mais informações, consulte [Opt-out de HTTPS/HSTS na criação do projeto](#opt-out-of-httpshsts-on-project-creation).

### <a name="usehttpsredirection"></a>UseHttpsRedirection

O código a seguir chama `UseHttpsRedirection` no `Startup` classe:

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=13)]

O código realçado anterior:

* Usa o padrão [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).
* Usa o padrão [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), a menos que substituído pela `ASPNETCORE_HTTPS_PORT` variável de ambiente ou [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).

É recomendável usar redirecionamentos temporários em vez de redirecionamentos permanentes. Cache de link pode causar um comportamento instável em ambientes de desenvolvimento. Se você preferir enviar um código de status de redirecionamento permanente quando o aplicativo estiver em um ambiente de não desenvolvimento, consulte o [configurar redirecionamentos permanentes em produção](#configure-permanent-redirects-in-production) seção. É recomendável usar [HSTS](#http-strict-transport-security-protocol-hsts) para sinalizar para os clientes que proteger somente o recurso deve ser redirecionada para o aplicativo (somente em produção).

### <a name="port-configuration"></a>Configuração de porta

Uma porta deve estar disponível para o middleware redirecionar uma solicitação não segura para HTTPS. Se nenhuma porta estiver disponível:

* Redirecionamento de HTTPS não ocorre.
* O middleware registra o aviso "Falha ao determinar a porta https para redirecionamento."

Especifique a porta HTTPS usando qualquer uma das seguintes abordagens:

* Definir [HttpsRedirectionOptions.HttpsPort](#options).
* Defina as `ASPNETCORE_HTTPS_PORT` variável de ambiente ou [definição de configuração do Host da Web https_port](xref:fundamentals/host/web-host#https-port):

  **Chave**: `https_port`  
  **Tipo**: *string*  
  **Padrão**: Um valor padrão não está definido.  
  **Definido usando**: `UseSetting`  
  **Variável de ambiente**: `<PREFIX_>HTTPS_PORT` (É o prefixo `ASPNETCORE_` ao usar o [Host Web](xref:fundamentals/host/web-host).)

  Ao configurar uma <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> em `Program`:

  [!code-csharp[](enforcing-ssl/sample-snapshot/Program.cs?name=snippet_Program&highlight=10)]
* Indicar uma porta com o esquema segura usando o `ASPNETCORE_URLS` variável de ambiente. A variável de ambiente configura o servidor. O middleware indiretamente descobre a porta HTTPS por meio de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>. Essa abordagem não funciona em implantações de proxy reverso.
* No desenvolvimento, defina uma URL HTTPS *launchsettings. JSON*. Habilite HTTPS quando o IIS Express é usado.
* Configurar um ponto de extremidade de URL HTTPS para uma implantação de borda para o público do [Kestrel](xref:fundamentals/servers/kestrel) server ou [HTTP. sys](xref:fundamentals/servers/httpsys) server. Somente **uma porta HTTPS** é usado pelo aplicativo. O middleware descobre a porta por meio de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.

> [!NOTE]
> Quando um aplicativo é executado em uma configuração de proxy reverso, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> não está disponível. Defina a porta usando uma das outras abordagens descritas nesta seção.

Quando o Kestrel ou HTTP. sys é usado como um servidor de borda para o público, o Kestrel ou HTTP. sys deve ser configurado para escutar em ambos:

* A porta segura em que o cliente é redirecionado (normalmente, 443 em 5001 no desenvolvimento e produção).
* A porta não segura (normalmente, 80 na produção) e 5000 no desenvolvimento.

A porta não segura deve estar acessível pelo cliente para que o aplicativo para receber uma solicitação não segura e redirecionar o cliente para a porta segura.

Para obter mais informações, consulte [configuração de ponto de extremidade do Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) ou <xref:fundamentals/servers/httpsys>.

### <a name="deployment-scenarios"></a>Cenários de implantação

Qualquer firewall entre o cliente e o servidor também deve ter as portas de comunicação aberto para o tráfego.

Se as solicitações são encaminhadas em uma configuração de proxy reverso, use [Middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer) antes de chamar Middleware de redirecionamento de HTTPS. Encaminhado atualizações de Middleware de cabeçalhos a `Request.Scheme`, usando o `X-Forwarded-Proto` cabeçalho. O middleware permite redirecionar URIs e outras políticas de segurança para funcionar corretamente. Quando o Middleware de cabeçalhos encaminhados não for usado, o aplicativo de back-end não pode receber o esquema correto e acabar em um loop de redirecionamento. Uma mensagem de erro comuns do usuário final é que muitos redirecionamentos ocorreram.

Ao implantar o serviço de aplicativo do Azure, siga as orientações em [Tutorial: vincular um certificado SSL personalizado ao serviço Aplicativos Web do Azure](/azure/app-service/app-service-web-tutorial-custom-ssl).

### <a name="options"></a>Opções

O seguinte realçado código chama [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) para configurar as opções de middleware:

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=14-99)]

Chamando `AddHttpsRedirection` só é necessário alterar os valores de `HttpsPort` ou `RedirectStatusCode`.

O código realçado anterior:

* Conjuntos [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) para <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, que é o valor padrão. Use os campos do <xref:Microsoft.AspNetCore.Http.StatusCodes> classe atribuições para `RedirectStatusCode`.
* Define a porta HTTPS para 5001. O valor padrão é 443.

#### <a name="configure-permanent-redirects-in-production"></a>Configurar redirecionamentos permanentes em produção

O middleware usará como padrão para envio de uma [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) com todos os redirecionamentos. Se você preferir enviar um código de status de redirecionamento permanente quando o aplicativo estiver em um ambiente de não desenvolvimento, encapsule a configuração de opções de middleware em uma verificação condicional para um ambiente de desenvolvimento não.

Ao configurar uma `IWebHostBuilder` na *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

## <a name="https-redirection-middleware-alternative-approach"></a>Abordagem alternativa de Middleware de redirecionamento de HTTPS

Uma alternativa ao uso de Middleware de redirecionamento de HTTPS (`UseHttpsRedirection`) é usar o Middleware de reconfiguração de URL (`AddRedirectToHttps`). `AddRedirectToHttps` também pode definir o código de status e a porta quando o redirecionamento é executado. Para obter mais informações, consulte [Middleware de reconfiguração de URL](xref:fundamentals/url-rewriting).

Ao redirecionar para HTTPS sem a necessidade de regras de redirecionamento adicional, é recomendável usar o Middleware de redirecionamento de HTTPS (`UseHttpsRedirection`) descrito neste tópico.

::: moniker-end

::: moniker range="< aspnetcore-2.1"

O [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) é usada para exigir HTTPS. `[RequireHttpsAttribute]` pode decorar controladores ou métodos, ou podem ser aplicadas globalmente. Para aplicar o atributo globalmente, adicione o seguinte código ao `ConfigureServices` em `Startup`:

[!code-csharp[](~/security/authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-999)]

O código realçado anterior requer que todas as solicitações usem `HTTPS`; portanto, as solicitações HTTP são ignoradas. O seguinte código realçado redireciona todas as solicitações HTTP para HTTPS:

[!code-csharp[](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-999)]

Para obter mais informações, consulte [Middleware de reconfiguração de URL](xref:fundamentals/url-rewriting). O middleware também permite que o aplicativo para definir o código de status ou o código de status e a porta quando o redirecionamento é executado.

Exigir HTTPS globalmente (`options.Filters.Add(new RequireHttpsAttribute());`) é uma prática recomendada de segurança. Aplicando o `[RequireHttps]` atributo a todas as páginas do Razor/controladores não é considerado tão seguro quanto exigir HTTPS globalmente. Você não pode garantir o `[RequireHttps]` atributo é aplicado quando novos controladores e páginas Razor são adicionadas.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="http-strict-transport-security-protocol-hsts"></a>Protocolo de segurança de transporte estrito HTTP (HSTS)

Por [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [segurança de transporte estrito HTTP (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) é um aprimoramento de segurança opcional é especificado por um aplicativo web com o uso de um cabeçalho de resposta. Quando um [navegador que ofereça suporte a HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) recebe esse cabeçalho:

* O navegador armazena a configuração para o domínio que evita o envio de qualquer comunicação por HTTP. O navegador força todas as comunicações via HTTPS.
* O navegador impede que o usuário usando os certificados não confiáveis ou é inválidos. O navegador desativa prompts que permitem que um usuário para confiar temporariamente esses certificados.

Como HSTS é imposta pelo cliente, ele tem algumas limitações:

* O cliente deve oferecer suporte a HSTS.
* HSTS requer pelo menos uma solicitação HTTPS bem-sucedida para estabelecer a política HSTS.
* O aplicativo deve verificar todas as solicitações HTTP e redirecionar ou rejeitar a solicitação HTTP.

ASP.NET Core 2.1 ou posterior implementa HSTS com o `UseHsts` método de extensão. O código a seguir chama `UseHsts` quando o aplicativo não está no [modo de desenvolvimento](xref:fundamentals/environments):

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=10)]

`UseHsts` não é recomendado em desenvolvimento porque as configurações de HSTS são altamente armazenáveis em cache por navegadores. Por padrão, `UseHsts` exclui o endereço de loopback local.

Para ambientes de produção implementando HTTPS pela primeira vez, definir inicial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) com um valor pequeno usando um do <xref:System.TimeSpan> métodos. Defina o valor de horas como não mais do que um único dia caso você precise reverter a infraestrutura HTTPS para HTTP. Depois que você estiver confiante em sustentabilidade da configuração do HTTPS, aumente o valor de idade máxima HSTS; um valor comumente usado é um ano.

O código a seguir:

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=5-12)]

* Define o parâmetro de pré-carregamento do cabeçalho de segurança de transporte estrito. Pré-carregamento não faz parte dos [especificação RFC HSTS](https://tools.ietf.org/html/rfc6797), mas é compatível com navegadores da web para pré-carregar sites HSTS na nova instalação. Veja [https://hstspreload.org/](https://hstspreload.org/) para obter mais informações.
* Habilita [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), que se aplica a política HSTS para hospedar subdomínios.
* Define explicitamente o parâmetro de idade máxima do cabeçalho de segurança de transporte estrito para 60 dias. Se não for definido, o padrão é 30 dias. Consulte a [max-age diretiva](https://tools.ietf.org/html/rfc6797#section-6.1.1) para obter mais informações.
* Adiciona `example.com` à lista de hosts a serem excluídos.

`UseHsts` Exclui os seguintes hosts de loopback:

* `localhost`: O endereço de loopback do IPv4.
* `127.0.0.1`: O endereço de loopback do IPv4.
* `[::1]`: O endereço de loopback do IPv6.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="opt-out-of-httpshsts-on-project-creation"></a>Recusa de HTTPS/HSTS na criação do projeto

Em alguns cenários de serviço de back-end em que a segurança de conexão é manipulada na borda da rede para o público, configurando a segurança de conexão em cada nó não é necessária. Aplicativos gerados a partir de modelos no Visual Studio ou na Web a [dotnet new](/dotnet/core/tools/dotnet-new) comando enable [redirecionamento a HTTPS](#require-https) e [HSTS](#http-strict-transport-security-protocol-hsts). Para implantações que não exigem esses cenários, você pode recusar HTTPS/HSTS quando o aplicativo é criado a partir do modelo.

A recusa de HTTPS/HSTS:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Desmarque a **configurar para HTTPS** caixa de seleção.

![Novo aplicativo Web ASP.NET Core caixa de diálogo mostrando a configurar para HTTPS caixa de seleção desmarcada.](enforcing-ssl/_static/out.png)

#   <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli) 

Use a opção `--no-https`. Por exemplo

```console
dotnet new webapp --no-https
```

---

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Confiar no certificado de desenvolvimento do ASP.NET Core HTTPS no Windows e macOS

SDK do .NET core inclui um certificado de desenvolvimento de HTTPS. O certificado é instalado como parte da experiência de primeira execução. Por exemplo, `dotnet --info` produz uma saída semelhante à seguinte:

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

Instalar o SDK do .NET Core instala o certificado de desenvolvimento do ASP.NET Core HTTPS para o repositório de certificados de usuário local. O certificado foi instalado, mas não for confiável. Confiar em certificado execute a etapa única para executar o dotnet `dev-certs` ferramenta:

```console
dotnet dev-certs https --trust
```

O comando a seguir fornece ajuda sobre o `dev-certs` ferramenta:

```console
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>Como configurar um certificado de desenvolvedor para o Docker

Ver [esse problema de GitHub](https://github.com/aspnet/Docs/issues/6199).

::: moniker-end

## <a name="additional-information"></a>Informações adicionais

* <xref:host-and-deploy/proxy-load-balancer>
* [Hospede o ASP.NET Core no Linux com o Apache: Configuração HTTPS](xref:host-and-deploy/linux-apache#https-configuration)
* [Hospede o ASP.NET Core no Linux com Nginx: Configuração HTTPS](xref:host-and-deploy/linux-nginx#https-configuration)
* [Como configurar o SSL no IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [Suporte a navegador HSTS OWASP](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
