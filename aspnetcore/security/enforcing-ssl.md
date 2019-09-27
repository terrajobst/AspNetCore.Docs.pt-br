---
title: Impor HTTPS em ASP.NET Core
author: rick-anderson
description: Saiba como exigir HTTPS/TLS em um aplicativo Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2019
uid: security/enforcing-ssl
ms.openlocfilehash: 1d1bba6a1f1da2af959bc69b31f79bac53bf48b9
ms.sourcegitcommit: fe3e556bf438fc4136fcf0bac61cf96e3e91caf5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71341470"
---
# <a name="enforce-https-in-aspnet-core"></a>Impor HTTPS em ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Este documento demonstra como:

* Exigir HTTPS para todas as solicitações.
* Redirecione todas as solicitações HTTP para HTTPS.

Nenhuma API pode impedir que um cliente envie dados confidenciais na primeira solicitação.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a>Projetos de API
>
> Não **use** [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) em APIs Web que recebam informações confidenciais. `RequireHttpsAttribute`usa códigos de status HTTP para redirecionar os navegadores de HTTP para HTTPS. Os clientes de API podem não entender ou obedecer a redirecionamentos de HTTP para HTTPS. Esses clientes podem enviar informações por HTTP. As APIs da Web devem ser:
>
> * Não escutar em HTTP.
> * Feche a conexão com o código de status 400 (solicitação inválida) e não atenda à solicitação.
>
> ## <a name="hsts-and-api-projects"></a>Projetos de API e HSTS
>
> Os projetos de API padrão não incluem [HSTS](#hsts) porque HSTS geralmente é uma instrução somente de navegador. Outros chamadores, como telefone ou aplicativos da área de trabalho, **não** obedecem à instrução. Mesmo em navegadores, uma única chamada autenticada para uma API sobre HTTP tem riscos em redes inseguras. A abordagem segura é configurar projetos de API para escutar e responder apenas por HTTPS.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a>Projetos de API
>
> Não **use** [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) em APIs Web que recebam informações confidenciais. `RequireHttpsAttribute`usa códigos de status HTTP para redirecionar os navegadores de HTTP para HTTPS. Os clientes de API podem não entender ou obedecer a redirecionamentos de HTTP para HTTPS. Esses clientes podem enviar informações por HTTP. As APIs da Web devem ser:
>
> * Não escutar em HTTP.
> * Feche a conexão com o código de status 400 (solicitação inválida) e não atenda à solicitação.

::: moniker-end

## <a name="require-https"></a>Exigir HTTPS

É recomendável que a produção ASP.NET Core aplicativos Web usem:

* Middleware de redirecionamento de HTTPS<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>() para redirecionar solicitações HTTP para https.
* Middleware HSTS ([UseHsts](#http-strict-transport-security-protocol-hsts)) para enviar cabeçalhos HSTS (protocolo de segurança de transporte estrito) http para clientes.

> [!NOTE]
> Os aplicativos implantados em uma configuração de proxy reverso permitem que o proxy manipule a segurança de conexão (HTTPS). Se o proxy também tratar o redirecionamento de HTTPS, não será necessário usar o middleware de redirecionamento de HTTPS. Se o servidor proxy também tratar da gravação de cabeçalhos HSTS (por exemplo, [suporte a HSTS nativo no IIS 10,0 (1709) ou posterior](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), o middleware HSTS não será exigido pelo aplicativo. Para obter mais informações, consulte [recusar https/HSTS na criação do projeto](#opt-out-of-httpshsts-on-project-creation).

### <a name="usehttpsredirection"></a>UseHttpsRedirection

O código a seguir `UseHttpsRedirection` chama a `Startup` classe:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

O código realçado anterior:

* Usa o padrão [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).
* Usa o [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (nulo) padrão, a menos que `ASPNETCORE_HTTPS_PORT` seja substituído pela variável de ambiente ou [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).

É recomendável usar redirecionamentos temporários em vez de redirecionamentos permanentes. O cache de link pode causar comportamento instável em ambientes de desenvolvimento. Se você preferir enviar um código de status de redirecionamento permanente quando o aplicativo estiver em um ambiente que não seja de desenvolvimento, consulte a seção [Configurar redirecionamentos permanentes na produção](#configure-permanent-redirects-in-production) . É recomendável usar [HSTS](#http-strict-transport-security-protocol-hsts) para sinalizar para clientes que apenas as solicitações de recursos de segurança devem ser enviadas para o aplicativo (somente em produção).

### <a name="port-configuration"></a>Configuração de porta

Uma porta deve estar disponível para o middleware redirecionar uma solicitação insegura para HTTPS. Se nenhuma porta estiver disponível:

* O redirecionamento para HTTPS não ocorre.
* O middleware registra o aviso "falha ao determinar a porta HTTPS para redirecionamento".

Especifique a porta HTTPS usando qualquer uma das seguintes abordagens:

* Defina [HttpsRedirectionOptions. HttpsPort](#options).

::: moniker range=">= aspnetcore-3.0"

* Defina a `https_port` [configuração de host](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):

  * Na configuração do host.
  * Definindo a variável `ASPNETCORE_HTTPS_PORT` de ambiente.
  * Adicionando uma entrada de nível superior em *appSettings. JSON*:

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* Indique uma porta com o esquema seguro usando a [variável de ambiente ASPNETCORE_URLS](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls). A variável de ambiente configura o servidor. O middleware descobre indiretamente a porta HTTPS via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>. Essa abordagem não funciona em implantações de proxy reverso.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* Defina a `https_port` [configuração de host](xref:fundamentals/host/web-host#https-port):

  * Na configuração do host.
  * Definindo a variável `ASPNETCORE_HTTPS_PORT` de ambiente.
  * Adicionando uma entrada de nível superior em *appSettings. JSON*:

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* Indique uma porta com o esquema seguro usando a [variável de ambiente ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls). A variável de ambiente configura o servidor. O middleware descobre indiretamente a porta HTTPS via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>. Essa abordagem não funciona em implantações de proxy reverso.

::: moniker-end

* Em desenvolvimento, defina uma URL HTTPS em *launchsettings. JSON*. Habilite HTTPS quando IIS Express for usado.

* Configure um ponto de extremidade de URL HTTPS para uma implantação de borda voltada para o público do servidor [Kestrel](xref:fundamentals/servers/kestrel) ou [http. sys](xref:fundamentals/servers/httpsys) . Somente **uma porta https** é usada pelo aplicativo. O middleware descobre a porta por meio <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>de.

> [!NOTE]
> Quando um aplicativo é executado em uma configuração de proxy reverso, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> o não está disponível. Defina a porta usando uma das outras abordagens descritas nesta seção.

### <a name="edge-deployments"></a>Implantações de borda 

Quando Kestrel ou HTTP. sys é usado como um servidor de borda voltado ao público, Kestrel ou HTTP. sys deve ser configurado para escutar em ambos:

* A porta segura em que o cliente é redirecionado (normalmente, 443 em produção e 5001 em desenvolvimento).
* A porta insegura (normalmente, 80 em produção e 5000 em desenvolvimento).

A porta insegura deve ser acessível pelo cliente para que o aplicativo receba uma solicitação insegura e redirecione o cliente para a porta segura.

Para obter mais informações, consulte configuração de ponto <xref:fundamentals/servers/httpsys>de [extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) ou.

### <a name="deployment-scenarios"></a>Cenários de implantação

Qualquer firewall entre o cliente e o servidor também deve ter portas de comunicação abertas para tráfego.

Se as solicitações forem encaminhadas em uma configuração de proxy reverso, use o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer) antes de chamar o middleware de redirecionamento de HTTPS. O middleware de cabeçalhos encaminhados atualiza `Request.Scheme`o, usando `X-Forwarded-Proto` o cabeçalho. O middleware permite que os URIs de redirecionamento e outras políticas de segurança funcionem corretamente. Quando o middleware de cabeçalhos encaminhados não é usado, o aplicativo de back-end pode não receber o esquema correto e terminar em um loop de redirecionamento. Uma mensagem de erro comum do usuário final é que ocorreram muitos redirecionamentos.

Ao implantar no serviço Azure app, siga as orientações em [tutorial: vincular um certificado SSL personalizado ao serviço Aplicativos Web do Azure](/azure/app-service/app-service-web-tutorial-custom-ssl).

### <a name="options"></a>Opções

O código realçado a seguir chama [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) para configurar opções de middleware:


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


A `AddHttpsRedirection` chamada só é necessária para alterar os valores `HttpsPort` de `RedirectStatusCode`ou.

O código realçado anterior:

* Define [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) como <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, que é o valor padrão. Use os campos da classe <xref:Microsoft.AspNetCore.Http.StatusCodes> para as atribuições `RedirectStatusCode`para.
* Define a porta HTTPS como 5001. O valor padrão é 443.

#### <a name="configure-permanent-redirects-in-production"></a>Configurar redirecionamentos permanentes na produção

O padrão do middleware é enviar um [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) com todos os redirecionamentos. Se você preferir enviar um código de status de redirecionamento permanente quando o aplicativo estiver em um ambiente que não seja de desenvolvimento, coloque a configuração de opções de middleware em uma verificação condicional para um ambiente que não seja de desenvolvimento.

::: moniker range=">= aspnetcore-3.0"

Ao configurar serviços no *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
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

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Ao configurar serviços no *Startup.cs*:

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

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a>Abordagem alternativa de middleware de redirecionamento de HTTPS

Uma alternativa ao uso do middleware de redirecionamento de`UseHttpsRedirection`HTTPS () é usar o middleware de reescrita`AddRedirectToHttps`de URL (). `AddRedirectToHttps`também é possível definir o código de status e a porta quando o redirecionamento é executado. Para obter mais informações, consulte [middleware de regravação de URL](xref:fundamentals/url-rewriting).

Ao redirecionar para https sem o requisito de regras de redirecionamento adicionais, recomendamos o uso do middleware de`UseHttpsRedirection`redirecionamento de HTTPS () descrito neste tópico.

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a>Protocolo de segurança de transporte estrito HTTP (HSTS)

Por [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), o [HSTS (segurança de transporte estrito) http](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) é um aprimoramento de segurança opcional que é especificado por um aplicativo Web por meio do uso de um cabeçalho de resposta. Quando um [navegador que dá suporte a HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) recebe este cabeçalho:

* O navegador armazena a configuração para o domínio que impede o envio de qualquer comunicação via HTTP. O navegador força toda a comunicação por HTTPS.
* O navegador impede que o usuário use certificados não confiáveis ou inválidos. O navegador desabilita as solicitações que permitem que um usuário confie temporariamente em um certificado.

Como o HSTS é imposto pelo cliente, ele tem algumas limitações:

* O cliente deve dar suporte a HSTS.
* HSTS requer pelo menos uma solicitação HTTPS bem-sucedida para estabelecer a política HSTS.
* O aplicativo deve verificar cada solicitação HTTP e redirecionar ou rejeitar a solicitação HTTP.

ASP.NET Core 2,1 e posterior implementa HSTS com o `UseHsts` método de extensão. O código a seguir `UseHsts` chama quando o aplicativo não está no [modo de desenvolvimento](xref:fundamentals/environments):

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

`UseHsts`Não é recomendado no desenvolvimento porque as configurações de HSTS são altamente armazenáveis em cache pelos navegadores. Por padrão, `UseHsts` o exclui o endereço de loopback local.

Para ambientes de produção que estão implementando https pela primeira vez, defina o [HstsOptions inicial. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) como um valor pequeno usando um dos <xref:System.TimeSpan> métodos. Defina o valor de horas para não mais do que um único dia, caso precise reverter a infraestrutura HTTPS para HTTP. Depois que você estiver confiante na sustentabilidade da configuração de HTTPS, aumente o valor de idade máxima HSTS; um valor comumente usado é de um ano.

O código a seguir:


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* Define o parâmetro PreLoad do cabeçalho Strict-Transport-Security. Pré-carregar não faz parte da [especificação RFC HSTS](https://tools.ietf.org/html/rfc6797), mas tem suporte de navegadores da Web para pré-carregar HSTS sites na nova instalação. Veja [https://hstspreload.org/](https://hstspreload.org/) para obter mais informações.
* Habilita [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), que aplica a política HSTS aos subdomínios de host.
* Define explicitamente o parâmetro Max-age do cabeçalho Strict-Transport-Security como 60 dias. Se não estiver definido, o padrão será 30 dias. Consulte a [diretiva Max-age](https://tools.ietf.org/html/rfc6797#section-6.1.1) para obter mais informações.
* Adiciona `example.com` à lista de hosts a serem excluídos.

`UseHsts`exclui os seguintes hosts de loopback:

* `localhost` : O endereço de loopback IPv4.
* `127.0.0.1` : O endereço de loopback IPv4.
* `[::1]` : O endereço de loopback IPv6.

## <a name="opt-out-of-httpshsts-on-project-creation"></a>Recusa de HTTPS/HSTS na criação do projeto

Em alguns cenários de serviço de back-end em que a segurança de conexão é tratada na borda voltada para o público da rede, não é necessário configurar a segurança de conexão em cada nó. Os aplicativos Web gerados nos modelos no Visual Studio ou no [novo comando dotnet](/dotnet/core/tools/dotnet-new) habilitam o [redirecionamento https](#require-https) e o [HSTS](#http-strict-transport-security-protocol-hsts). Para implantações que não exigem esses cenários, você pode recusar o HTTPS/HSTS quando o aplicativo é criado a partir do modelo.

Para recusar o HTTPS/HSTS:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Desmarque a caixa de seleção **Configurar para https** .

::: moniker range=">= aspnetcore-3.0"

![Diálogo novo ASP.NET Core aplicativo Web mostrando a caixa de seleção configurar para HTTPS desmarcada.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Diálogo novo ASP.NET Core aplicativo Web mostrando a caixa de seleção configurar para HTTPS desmarcada.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli) 

Use a opção `--no-https`. Por exemplo

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Confiar no certificado de desenvolvimento ASP.NET Core HTTPS no Windows e no macOS

O SDK do .NET Core inclui um certificado de desenvolvimento HTTPS. O certificado é instalado como parte da experiência de primeira execução. Por exemplo, `dotnet --info` produz uma saída semelhante à seguinte:

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

Instalar o SDK do .NET Core instala o certificado de desenvolvimento HTTPS do ASP.NET Core no repositório de certificados do usuário local. O certificado foi instalado, mas não é confiável. Para confiar no certificado, execute a etapa única para executar a ferramenta dotnet `dev-certs` :

```dotnetcli
dotnet dev-certs https --trust
```

O comando a seguir fornece ajuda para a ferramenta `dev-certs`:

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>Como configurar um certificado de desenvolvedor para o Docker

Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6199).

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a>Confiar no certificado HTTPS do subsistema do Windows para Linux

O subsistema do Windows para Linux (WSL) gera um certificado HTTPS autoassinado. Para configurar o repositório de certificados do Windows para confiar no certificado WSL:

* Execute o seguinte comando para exportar o certificado gerado WSL:`dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`
* Em uma janela do WSL, execute o seguinte comando:`ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`

  O comando anterior define as variáveis de ambiente para que o Linux use o certificado confiável do Windows.

## <a name="troubleshoot-certificate-problems"></a>Solucionar problemas de certificado

Esta seção fornece ajuda quando o ASP.NET Core certificado de desenvolvimento HTTPS foi [instalado e é confiável](#trust), mas você ainda tem avisos do navegador de que o certificado não é confiável.

### <a name="all-platforms---certificate-not-trusted"></a>Todas as plataformas-certificado não confiável

Execute os seguintes comandos:

```dotnetcli
dotnet devcerts https --clean
dotnet devcerts https --trust
```

Feche todas as instâncias do navegador abertas. Abra uma nova janela do navegador para o aplicativo. A confiança de certificado é armazenada em cache por navegadores.

Os comandos anteriores resolvem a maioria dos problemas de confiança do navegador. Se o navegador ainda não estiver confiando no certificado, siga as sugestões específicas da plataforma a seguir.

### <a name="docker---certificate-not-trusted"></a>Docker-certificado não confiável

* Exclua a pasta *C:\Users\{User} \AppData\Roaming\ASP.NET\Https*
* Limpe a solução. Exclua as pastas *bin* e *obj*.
* Reinicie a ferramenta de desenvolvimento. Por exemplo, Visual Studio, Visual Studio Code ou Visual Studio para Mac.

### <a name="windows---certificate-not-trusted"></a>Windows-certificado não confiável

* Verifique os certificados no repositório de certificados. Deve haver um `localhost` certificado com o `ASP.NET Core HTTPS development certificate` nome amigável em `Current User > Personal > Certificates` e`Current User > Trusted root certification authorities > Certificates`
* Remova todos os certificados encontrados das autoridades de certificação raiz pessoais e confiáveis. **Não** remova o IIS Express certificado localhost.
* Execute os seguintes comandos:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Feche todas as instâncias do navegador abertas. Abra uma nova janela do navegador para o aplicativo.

### <a name="os-x---certificate-not-trusted"></a>OS X-certificado não confiável

* Abra o acesso ao conjunto de chaves.
* Selecione o conjunto de chaves do sistema.
* Verifique a presença de um certificado localhost.
* Verifique se ele contém um `+` símbolo no ícone para indicar seu confiável para todos os usuários.
* Remova o certificado do conjunto de chaves do sistema.
* Execute os seguintes comandos:

```dotnetcli
dotnet devcerts https --clean
dotnet devcerts https --trust
```

Feche todas as instâncias do navegador abertas. Abra uma nova janela do navegador para o aplicativo.

## <a name="additional-information"></a>Informações adicionais

* <xref:host-and-deploy/proxy-load-balancer>
* [ASP.NET Core do host no Linux com Apache: Configuração de HTTPS](xref:host-and-deploy/linux-apache#https-configuration)
* [ASP.NET Core do host no Linux com Nginx: Configuração de HTTPS](xref:host-and-deploy/linux-nginx#https-configuration)
* [Como configurar o SSL no IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [Suporte ao navegador OWASP HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
