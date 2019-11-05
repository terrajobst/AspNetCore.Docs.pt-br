---
title: Configurar a autenticação de certificado no ASP.NET Core
author: blowdart
description: Saiba como configurar a autenticação de certificado no ASP.NET Core para IIS e HTTP. sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 08/19/2019
uid: security/authentication/certauth
ms.openlocfilehash: 1e646aabb4e384e6906575e7beaa680e91f968a0
ms.sourcegitcommit: e5d4768aaf85703effb4557a520d681af8284e26
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73616573"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>Configurar a autenticação de certificado no ASP.NET Core

`Microsoft.AspNetCore.Authentication.Certificate` contém uma implementação semelhante à [autenticação de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) para ASP.NET Core. A autenticação de certificado ocorre no nível de TLS, muito antes que ele chegue a ASP.NET Core. Mais precisamente, esse é um manipulador de autenticação que valida o certificado e, em seguida, fornece um evento em que você pode resolver esse certificado para um `ClaimsPrincipal`. 

[Configure seu host](#configure-your-host-to-require-certificates) para autenticação de certificado, seja ele IIS, Kestrel, aplicativos Web do Azure ou qualquer outra coisa que você esteja usando.

## <a name="proxy-and-load-balancer-scenarios"></a>Cenários de balanceador de carga e proxy

A autenticação de certificado é um cenário com monitoração de estado usado principalmente onde um proxy ou um balanceador de carga não trata o tráfego entre clientes e servidores. Se um proxy ou um balanceador de carga for usado, a autenticação de certificado só funcionará se o proxy ou o balanceador de carga:

* Manipula a autenticação.
* Passa as informações de autenticação do usuário para o aplicativo (por exemplo, em um cabeçalho de solicitação), que atua nas informações de autenticação.

Uma alternativa à autenticação de certificado em ambientes em que proxies e balanceadores de carga são usados é Active Directory serviços federados (ADFS) com o OpenID Connect (OIDC).

## <a name="get-started"></a>Introdução

Adquira um certificado HTTPS, aplique-o e [configure seu host](#configure-your-host-to-require-certificates) para exigir certificados.

Em seu aplicativo Web, adicione uma referência ao pacote de `Microsoft.AspNetCore.Authentication.Certificate`. Em seguida, no método `Startup.ConfigureServices`, chame `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` com suas opções, fornecendo um delegado para `OnCertificateValidated` fazer qualquer validação suplementar no certificado do cliente enviado com solicitações. Transforme essas informações em um `ClaimsPrincipal` e defina-as na propriedade `context.Principal`.

Se a autenticação falhar, esse manipulador retornará uma `403 (Forbidden)` resposta em vez de uma `401 (Unauthorized)`, como você pode esperar. O raciocínio é que a autenticação deve ocorrer durante a conexão de TLS inicial. No momento em que atinge o manipulador, é tarde demais. Não há como atualizar a conexão de uma conexão anônima para uma com um certificado.

Além disso, adicione `app.UseAuthentication();` no método `Startup.Configure`. Caso contrário, o HttpContext. User não será definido como `ClaimsPrincipal` criado a partir do certificado. Por exemplo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // All the other service configuration.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All the other app configuration.
}
```

O exemplo anterior demonstra a maneira padrão de adicionar autenticação de certificado. O manipulador constrói uma entidade de usuário usando as propriedades comuns do certificado.

## <a name="configure-certificate-validation"></a>Configurar validação de certificado

O manipulador de `CertificateAuthenticationOptions` tem algumas validações internas que são as validações mínimas que você deve executar em um certificado. Cada uma dessas configurações é habilitada por padrão.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = encadeado, SelfSigned ou todos (encadeados | SelfSigned)

Essa verificação valida que apenas o tipo de certificado apropriado é permitido.

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Essa verificação valida que o certificado apresentado pelo cliente tem o EKU (uso estendido de chave) de autenticação de cliente ou nenhum EKUs. Como as especificações dizem, se nenhum EKU for especificado, todos os EKUs serão considerados válidos.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

Essa verificação valida que o certificado está dentro do período de validade. Em cada solicitação, o manipulador garante que um certificado que era válido quando foi apresentado não tenha expirado durante sua sessão atual.

### <a name="revocationflag"></a>RevocationFlag

Um sinalizador que especifica quais certificados na cadeia são verificados para revogação.

As verificações de revogação são executadas somente quando o certificado é encadeado a um certificado raiz.

### <a name="revocationmode"></a>Rerevocationmode

Um sinalizador que especifica como as verificações de revogação são executadas.

A especificação de uma verificação online pode resultar em um longo atraso enquanto a autoridade de certificação é contatada.

As verificações de revogação são executadas somente quando o certificado é encadeado a um certificado raiz.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Posso configurar meu aplicativo para exigir um certificado somente em determinados caminhos?

Isso não é possível. Lembre-se de que a troca de certificado faz com que o início da conversa HTTPS seja feito pelo servidor antes que a primeira solicitação seja recebida nessa conexão, portanto, não é possível fazer o escopo com base em qualquer campo de solicitação.

## <a name="handler-events"></a>Eventos de manipulador

O manipulador tem dois eventos:

* `OnAuthenticationFailed` &ndash; chamado se ocorrer uma exceção durante a autenticação e permitir que você reaja.
* `OnCertificateValidated` &ndash; chamado depois que o certificado foi validado, aprovado na validação e uma entidade de segurança padrão foi criada. Esse evento permite que você execute sua própria validação e aumente ou substitua a entidade de segurança. Para obter exemplos, inclua:
  * Determinando se o certificado é conhecido por seus serviços.
  * Construindo sua própria entidade de segurança. Considere o exemplo a seguir em `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

Se você achar que o certificado de entrada não atende à sua validação extra, chame `context.Fail("failure reason")` com um motivo de falha.

Para a funcionalidade real, você provavelmente desejará chamar um serviço registrado na injeção de dependência que se conecta a um banco de dados ou a outro tipo de armazenamento de usuário. Acesse seu serviço usando o contexto passado para seu representante. Considere o exemplo a seguir em `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

Conceitualmente, a validação do certificado é um problema de autorização. A adição de um check-in, por exemplo, um emissor ou uma impressão digital em uma política de autorização, em vez de dentro de `OnCertificateValidated`, é perfeitamente aceitável.

## <a name="configure-your-host-to-require-certificates"></a>Configurar o host para exigir certificados

### <a name="kestrel"></a>Kestrel

No *Program.cs*, configure o Kestrel da seguinte maneira:

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel(options =>
        {
            options.ConfigureHttpsDefaults(opt => 
                opt.ClientCertificateMode = 
                    ClientCertificateMode.RequireCertificate);
        })
        .Build();
```

### <a name="iis"></a>IIS

Conclua as seguintes etapas no Gerenciador do IIS:

1. Selecione seu site na guia **conexões** .
1. Clique duas vezes na opção **configurações de SSL** na janela exibição de **recursos** .
1. Marque a caixa de seleção **exigir SSL** e selecione o botão de opção **exigir** na seção **certificados do cliente** .

![Configurações de certificado de cliente no IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Proxies da Web personalizados e do Azure

Consulte a [documentação de host e implantação](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) para saber como configurar o middleware de encaminhamento de certificado.
