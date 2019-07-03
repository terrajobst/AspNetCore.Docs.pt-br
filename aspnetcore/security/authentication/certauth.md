---
title: Configurar a autenticação de certificado no ASP.NET Core
author: blowdart
description: Saiba como configurar a autenticação de certificado no ASP.NET Core para IIS e o HTTP. sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 06/11/2019
uid: security/authentication/certauth
ms.openlocfilehash: 8609c58265340da1d618135795915d6c49e750a3
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538714"
---
# <a name="overview"></a>Visão geral

`Microsoft.AspNetCore.Authentication.Certificate` contém uma implementação semelhante à [autenticação de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) para ASP.NET Core. Autenticação de certificado ocorre no nível de TLS, longa antes que ela nunca seja ao ASP.NET Core. Mais precisamente, isso é um manipulador de autenticação que valida o certificado e, em seguida, lhe dá um evento em que você pode resolver esse certificado para um `ClaimsPrincipal`. 

[Configurar o host](#configure-your-host-to-require-certificates) para autenticação de certificado, seja ele IIS, o Kestrel, aplicativos Web do Azure ou qualquer outra coisa que você está usando.

## <a name="get-started"></a>Introdução

Adquirir um certificado HTTPS, aplicá-la, e [configurar o host](#configure-your-host-to-require-certificates) para exigir certificados.

No seu aplicativo web, adicione uma referência para o `Microsoft.AspNetCore.Authentication.Certificate` pacote. Em seguida, nos `Startup.Configure` método, chame `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` com suas opções, fornecendo um delegado para `OnCertificateValidated` fazer nenhuma validação suplementar no certificado do cliente enviado com solicitações. Ativar essas informações em um `ClaimsPrincipal` e defini-lo sobre a `context.Principal` propriedade.

Se a autenticação falhar, esse manipulador retorna um `403 (Forbidden)` resposta em vez disso, um `401 (Unauthorized)`, como você poderia esperar. O raciocínio é que a autenticação deve acontecer durante a conexão inicial do TLS. No momento em que ele atingir o manipulador, é tarde demais. Não é possível atualizar a conexão de uma conexão anônima para um com um certificado.

Também adicione `app.UseAuthentication();` no `Startup.Configure` método. Caso contrário, o HttpContext não será definido como `ClaimsPrincipal` criado no certificado. Por exemplo:

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

O exemplo anterior demonstra a maneira padrão para adicionar a autenticação de certificado. O manipulador constrói uma entidade de usuário usando as propriedades comuns de certificado.

## <a name="configure-certificate-validation"></a>Configurar a validação do certificado

O `CertificateAuthenticationOptions` manipulador tem algumas validações internas que são as validações mínimo, você deve executar em um certificado. Cada uma dessas configurações é habilitada por padrão.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = encadeadas, SelfSigned ou All (encadeadas | SelfSigned)

Essa verificação valida que apenas o tipo de certificado apropriado é permitido.

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Essa verificação valida o certificado apresentado pelo cliente tem a autenticação de cliente estendido do uso da chave (EKU) ou nenhum EKU em todos os. Conforme as especificações que, se nenhum EKU for especificado, todos os EKUs são considerados válidos.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

Essa verificação valida o certificado está dentro do período de validade. Em cada solicitação, o manipulador garante que um certificado era válido quando ele foi apresentado expirou durante sua sessão atual.

### <a name="revocationflag"></a>RevocationFlag

Um sinalizador que especifica quais certificados na cadeia é verificado para revogação.

Verificações de revogação são executadas somente quando o certificado está encadeado a um certificado raiz.

### <a name="revocationmode"></a>RevocationMode

Um sinalizador que especifica como as verificações de revogação são executadas.

Especificar uma verificação on-line pode resultar em um longo atraso enquanto a autoridade de certificação é contatada.

Verificações de revogação são executadas somente quando o certificado está encadeado a um certificado raiz.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Posso configurar meu aplicativo para exigir um certificado somente em determinados caminhos?

Não é possível. Lembre-se de que a troca de certificados é feita para que o início da conversa HTTPS, ele é feito pelo servidor antes que a primeira solicitação é recebida nessa conexão para que não seja possível ao escopo com base em quaisquer campos de solicitação.

## <a name="handler-events"></a>Eventos de manipulador

O manipulador tem dois eventos:

* `OnAuthenticationFailed` &ndash; Chamado se uma exceção ocorre durante a autenticação e permite reagir.
* `OnCertificateValidated` &ndash; Chamado depois que o certificado tiver sido validado, passou na validação e uma entidade de segurança padrão foi criada. Esse evento permite que você execute sua própria validação e incrementar ou substituir a entidade de segurança. Para obter exemplos incluem:
  * Determinando se o certificado é conhecido por seus serviços.
  * Construindo sua própria entidade. Considere o exemplo a seguir em `Startup.ConfigureServices`:

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

Se você encontrar o certificado de entrada não atende à sua validação extra, chamada `context.Fail("failure reason")` com um motivo da falha.

Funcionalidade de verdade, você provavelmente desejará chamar um serviço registrado na injeção de dependência que se conecta a um banco de dados ou outro tipo de repositório do usuário. Acesse seu serviço usando o contexto passado para o seu representante. Considere o exemplo a seguir em `Startup.ConfigureServices`:

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

Conceitualmente, a validação do certificado é uma preocupação de autorização. Adicionando uma verificação, por exemplo, um emissor ou impressão digital em uma política de autorização, em vez de inside `OnCertificateValidated`, é perfeitamente aceitável.

## <a name="configure-your-host-to-require-certificates"></a>Configurar o host para exigir certificados

### <a name="kestrel"></a>Kestrel

Na *Program.cs*, configure o Kestrel da seguinte maneira:

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

Conclua as etapas a seguir no Gerenciador do IIS:

1. Selecione o seu site do **conexões** guia.
1. Clique duas vezes o **configurações de SSL** opção a **exibição de recursos** janela.
1. Verificar a **exigir SSL** caixa de seleção e selecione o **exigem** botão de opção a **certificados de cliente** seção.

![Configurações de certificado de cliente no IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure e proxies da web personalizado

Consulte a [hospedar e implantar documentação](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) para saber como configurar o encaminhamento de middleware de certificado.
