---
title: Configurar a autenticação de certificado no ASP.NET Core
author: blowdart
description: Saiba como configurar a autenticação de certificado no ASP.NET Core para IIS e HTTP. sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 01/02/2020
uid: security/authentication/certauth
ms.openlocfilehash: 9c175439c0313d62c75898f1af097774b06f353a
ms.sourcegitcommit: e7d4fe6727d423f905faaeaa312f6c25ef844047
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75608139"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="3c69e-103">Configurar a autenticação de certificado no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c69e-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="3c69e-104">`Microsoft.AspNetCore.Authentication.Certificate` contém uma implementação semelhante à [autenticação de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c69e-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="3c69e-105">A autenticação de certificado ocorre no nível de TLS, muito antes que ele chegue a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c69e-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="3c69e-106">Mais precisamente, esse é um manipulador de autenticação que valida o certificado e, em seguida, fornece um evento em que você pode resolver esse certificado para um `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="3c69e-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="3c69e-107">[Configure seu host](#configure-your-host-to-require-certificates) para autenticação de certificado, seja ele IIS, Kestrel, aplicativos Web do Azure ou qualquer outra coisa que você esteja usando.</span><span class="sxs-lookup"><span data-stu-id="3c69e-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="3c69e-108">Cenários de balanceador de carga e proxy</span><span class="sxs-lookup"><span data-stu-id="3c69e-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="3c69e-109">A autenticação de certificado é um cenário com monitoração de estado usado principalmente onde um proxy ou um balanceador de carga não trata o tráfego entre clientes e servidores.</span><span class="sxs-lookup"><span data-stu-id="3c69e-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="3c69e-110">Se um proxy ou um balanceador de carga for usado, a autenticação de certificado só funcionará se o proxy ou o balanceador de carga:</span><span class="sxs-lookup"><span data-stu-id="3c69e-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="3c69e-111">Manipula a autenticação.</span><span class="sxs-lookup"><span data-stu-id="3c69e-111">Handles the authentication.</span></span>
* <span data-ttu-id="3c69e-112">Passa as informações de autenticação do usuário para o aplicativo (por exemplo, em um cabeçalho de solicitação), que atua nas informações de autenticação.</span><span class="sxs-lookup"><span data-stu-id="3c69e-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="3c69e-113">Uma alternativa à autenticação de certificado em ambientes em que proxies e balanceadores de carga são usados é Active Directory serviços federados (ADFS) com o OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="3c69e-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="3c69e-114">Introdução</span><span class="sxs-lookup"><span data-stu-id="3c69e-114">Get started</span></span>

<span data-ttu-id="3c69e-115">Adquira um certificado HTTPS, aplique-o e [configure seu host](#configure-your-host-to-require-certificates) para exigir certificados.</span><span class="sxs-lookup"><span data-stu-id="3c69e-115">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="3c69e-116">Em seu aplicativo Web, adicione uma referência ao pacote de `Microsoft.AspNetCore.Authentication.Certificate`.</span><span class="sxs-lookup"><span data-stu-id="3c69e-116">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="3c69e-117">Em seguida, no método `Startup.ConfigureServices`, chame `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` com suas opções, fornecendo um delegado para `OnCertificateValidated` fazer qualquer validação suplementar no certificado do cliente enviado com solicitações.</span><span class="sxs-lookup"><span data-stu-id="3c69e-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="3c69e-118">Transforme essas informações em um `ClaimsPrincipal` e defina-as na propriedade `context.Principal`.</span><span class="sxs-lookup"><span data-stu-id="3c69e-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="3c69e-119">Se a autenticação falhar, esse manipulador retornará uma `403 (Forbidden)` resposta em vez de uma `401 (Unauthorized)`, como você pode esperar.</span><span class="sxs-lookup"><span data-stu-id="3c69e-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="3c69e-120">O raciocínio é que a autenticação deve ocorrer durante a conexão de TLS inicial.</span><span class="sxs-lookup"><span data-stu-id="3c69e-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="3c69e-121">No momento em que atinge o manipulador, é tarde demais.</span><span class="sxs-lookup"><span data-stu-id="3c69e-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="3c69e-122">Não há como atualizar a conexão de uma conexão anônima para uma com um certificado.</span><span class="sxs-lookup"><span data-stu-id="3c69e-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="3c69e-123">Além disso, adicione `app.UseAuthentication();` no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="3c69e-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="3c69e-124">Caso contrário, o `HttpContext.User` não será definido como `ClaimsPrincipal` criado a partir do certificado.</span><span class="sxs-lookup"><span data-stu-id="3c69e-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="3c69e-125">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3c69e-125">For example:</span></span>

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

<span data-ttu-id="3c69e-126">O exemplo anterior demonstra a maneira padrão de adicionar autenticação de certificado.</span><span class="sxs-lookup"><span data-stu-id="3c69e-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="3c69e-127">O manipulador constrói uma entidade de usuário usando as propriedades comuns do certificado.</span><span class="sxs-lookup"><span data-stu-id="3c69e-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="3c69e-128">Configurar validação de certificado</span><span class="sxs-lookup"><span data-stu-id="3c69e-128">Configure certificate validation</span></span>

<span data-ttu-id="3c69e-129">O manipulador de `CertificateAuthenticationOptions` tem algumas validações internas que são as validações mínimas que você deve executar em um certificado.</span><span class="sxs-lookup"><span data-stu-id="3c69e-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="3c69e-130">Cada uma dessas configurações é habilitada por padrão.</span><span class="sxs-lookup"><span data-stu-id="3c69e-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="3c69e-131">AllowedCertificateTypes = encadeado, SelfSigned ou todos (encadeados | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="3c69e-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="3c69e-132">Valor padrão: `CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="3c69e-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="3c69e-133">Essa verificação valida que apenas o tipo de certificado apropriado é permitido.</span><span class="sxs-lookup"><span data-stu-id="3c69e-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="3c69e-134">Se o aplicativo estiver usando certificados autoassinados, essa opção precisará ser definida como `CertificateTypes.All` ou `CertificateTypes.SelfSigned`.</span><span class="sxs-lookup"><span data-stu-id="3c69e-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="3c69e-135">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="3c69e-135">ValidateCertificateUse</span></span>

<span data-ttu-id="3c69e-136">Valor padrão: `true`</span><span class="sxs-lookup"><span data-stu-id="3c69e-136">Default value: `true`</span></span>

<span data-ttu-id="3c69e-137">Essa verificação valida que o certificado apresentado pelo cliente tem o EKU (uso estendido de chave) de autenticação de cliente ou nenhum EKUs.</span><span class="sxs-lookup"><span data-stu-id="3c69e-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="3c69e-138">Como as especificações dizem, se nenhum EKU for especificado, todos os EKUs serão considerados válidos.</span><span class="sxs-lookup"><span data-stu-id="3c69e-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="3c69e-139">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="3c69e-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="3c69e-140">Valor padrão: `true`</span><span class="sxs-lookup"><span data-stu-id="3c69e-140">Default value: `true`</span></span>

<span data-ttu-id="3c69e-141">Essa verificação valida que o certificado está dentro do período de validade.</span><span class="sxs-lookup"><span data-stu-id="3c69e-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="3c69e-142">Em cada solicitação, o manipulador garante que um certificado que era válido quando foi apresentado não tenha expirado durante sua sessão atual.</span><span class="sxs-lookup"><span data-stu-id="3c69e-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="3c69e-143">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="3c69e-143">RevocationFlag</span></span>

<span data-ttu-id="3c69e-144">Valor padrão: `X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="3c69e-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="3c69e-145">Um sinalizador que especifica quais certificados na cadeia são verificados para revogação.</span><span class="sxs-lookup"><span data-stu-id="3c69e-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="3c69e-146">As verificações de revogação são executadas somente quando o certificado é encadeado a um certificado raiz.</span><span class="sxs-lookup"><span data-stu-id="3c69e-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="3c69e-147">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="3c69e-147">RevocationMode</span></span>

<span data-ttu-id="3c69e-148">Valor padrão: `X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="3c69e-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="3c69e-149">Um sinalizador que especifica como as verificações de revogação são executadas.</span><span class="sxs-lookup"><span data-stu-id="3c69e-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="3c69e-150">A especificação de uma verificação online pode resultar em um longo atraso enquanto a autoridade de certificação é contatada.</span><span class="sxs-lookup"><span data-stu-id="3c69e-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="3c69e-151">As verificações de revogação são executadas somente quando o certificado é encadeado a um certificado raiz.</span><span class="sxs-lookup"><span data-stu-id="3c69e-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="3c69e-152">Posso configurar meu aplicativo para exigir um certificado somente em determinados caminhos?</span><span class="sxs-lookup"><span data-stu-id="3c69e-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="3c69e-153">Isso não é possível.</span><span class="sxs-lookup"><span data-stu-id="3c69e-153">This isn't possible.</span></span> <span data-ttu-id="3c69e-154">Lembre-se de que a troca de certificado faz com que o início da conversa HTTPS seja feito pelo servidor antes que a primeira solicitação seja recebida nessa conexão, portanto, não é possível fazer o escopo com base em qualquer campo de solicitação.</span><span class="sxs-lookup"><span data-stu-id="3c69e-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="3c69e-155">Eventos de manipulador</span><span class="sxs-lookup"><span data-stu-id="3c69e-155">Handler events</span></span>

<span data-ttu-id="3c69e-156">O manipulador tem dois eventos:</span><span class="sxs-lookup"><span data-stu-id="3c69e-156">The handler has two events:</span></span>

* <span data-ttu-id="3c69e-157">`OnAuthenticationFailed` &ndash; chamado se ocorrer uma exceção durante a autenticação e permitir que você reaja.</span><span class="sxs-lookup"><span data-stu-id="3c69e-157">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="3c69e-158">`OnCertificateValidated` &ndash; chamado depois que o certificado foi validado, aprovado na validação e uma entidade de segurança padrão foi criada.</span><span class="sxs-lookup"><span data-stu-id="3c69e-158">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="3c69e-159">Esse evento permite que você execute sua própria validação e aumente ou substitua a entidade de segurança.</span><span class="sxs-lookup"><span data-stu-id="3c69e-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="3c69e-160">Para obter exemplos, inclua:</span><span class="sxs-lookup"><span data-stu-id="3c69e-160">For examples include:</span></span>
  * <span data-ttu-id="3c69e-161">Determinando se o certificado é conhecido por seus serviços.</span><span class="sxs-lookup"><span data-stu-id="3c69e-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="3c69e-162">Construindo sua própria entidade de segurança.</span><span class="sxs-lookup"><span data-stu-id="3c69e-162">Constructing your own principal.</span></span> <span data-ttu-id="3c69e-163">Considere o exemplo a seguir em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c69e-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="3c69e-164">Se você achar que o certificado de entrada não atende à sua validação extra, chame `context.Fail("failure reason")` com um motivo de falha.</span><span class="sxs-lookup"><span data-stu-id="3c69e-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="3c69e-165">Para a funcionalidade real, você provavelmente desejará chamar um serviço registrado na injeção de dependência que se conecta a um banco de dados ou a outro tipo de armazenamento de usuário.</span><span class="sxs-lookup"><span data-stu-id="3c69e-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="3c69e-166">Acesse seu serviço usando o contexto passado para seu representante.</span><span class="sxs-lookup"><span data-stu-id="3c69e-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="3c69e-167">Considere o exemplo a seguir em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c69e-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="3c69e-168">Conceitualmente, a validação do certificado é um problema de autorização.</span><span class="sxs-lookup"><span data-stu-id="3c69e-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="3c69e-169">A adição de um check-in, por exemplo, um emissor ou uma impressão digital em uma política de autorização, em vez de dentro de `OnCertificateValidated`, é perfeitamente aceitável.</span><span class="sxs-lookup"><span data-stu-id="3c69e-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="3c69e-170">Configurar o host para exigir certificados</span><span class="sxs-lookup"><span data-stu-id="3c69e-170">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="3c69e-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="3c69e-171">Kestrel</span></span>

<span data-ttu-id="3c69e-172">No *Program.cs*, configure o Kestrel da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="3c69e-172">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="3c69e-173">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de chamar <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="3c69e-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="3c69e-174">IIS</span><span class="sxs-lookup"><span data-stu-id="3c69e-174">IIS</span></span>

<span data-ttu-id="3c69e-175">Conclua as seguintes etapas no Gerenciador do IIS:</span><span class="sxs-lookup"><span data-stu-id="3c69e-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="3c69e-176">Selecione seu site na guia **conexões** .</span><span class="sxs-lookup"><span data-stu-id="3c69e-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="3c69e-177">Clique duas vezes na opção **configurações de SSL** na janela exibição de **recursos** .</span><span class="sxs-lookup"><span data-stu-id="3c69e-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="3c69e-178">Marque a caixa de seleção **exigir SSL** e selecione o botão de opção **exigir** na seção **certificados do cliente** .</span><span class="sxs-lookup"><span data-stu-id="3c69e-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Configurações de certificado de cliente no IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="3c69e-180">Proxies da Web personalizados e do Azure</span><span class="sxs-lookup"><span data-stu-id="3c69e-180">Azure and custom web proxies</span></span>

<span data-ttu-id="3c69e-181">Consulte a [documentação de host e implantação](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) para saber como configurar o middleware de encaminhamento de certificado.</span><span class="sxs-lookup"><span data-stu-id="3c69e-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="3c69e-182">Usar autenticação de certificado em aplicativos Web do Azure</span><span class="sxs-lookup"><span data-stu-id="3c69e-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="3c69e-183">O método `AddCertificateForwarding` é usado para especificar:</span><span class="sxs-lookup"><span data-stu-id="3c69e-183">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="3c69e-184">O nome do cabeçalho do cliente.</span><span class="sxs-lookup"><span data-stu-id="3c69e-184">The client header name.</span></span>
* <span data-ttu-id="3c69e-185">Como o certificado deve ser carregado (usando a propriedade `HeaderConverter`).</span><span class="sxs-lookup"><span data-stu-id="3c69e-185">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="3c69e-186">Nos aplicativos Web do Azure, o certificado é passado como um cabeçalho de solicitação personalizado chamado `X-ARR-ClientCert`.</span><span class="sxs-lookup"><span data-stu-id="3c69e-186">In Azure Web Apps, the certificate is passed as a custom request header named `X-ARR-ClientCert`.</span></span> <span data-ttu-id="3c69e-187">Para usá-lo, configure o encaminhamento de certificado no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c69e-187">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-ARR-ClientCert";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="3c69e-188">O método `Startup.Configure`, em seguida, adiciona o middleware.</span><span class="sxs-lookup"><span data-stu-id="3c69e-188">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="3c69e-189">`UseCertificateForwarding` é chamado antes das chamadas para `UseAuthentication` e `UseAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="3c69e-189">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="3c69e-190">Uma classe separada pode ser usada para implementar a lógica de validação.</span><span class="sxs-lookup"><span data-stu-id="3c69e-190">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="3c69e-191">Como o mesmo certificado autoassinado é usado neste exemplo, certifique-se de que apenas seu certificado possa ser usado.</span><span class="sxs-lookup"><span data-stu-id="3c69e-191">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="3c69e-192">Valide se as impressões digitais do certificado do cliente e do certificado do servidor correspondem, caso contrário, qualquer certificado pode ser usado e será suficiente para autenticar.</span><span class="sxs-lookup"><span data-stu-id="3c69e-192">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="3c69e-193">Isso seria usado dentro do método `AddCertificate`.</span><span class="sxs-lookup"><span data-stu-id="3c69e-193">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="3c69e-194">Você também pode validar o assunto ou o emissor aqui se estiver usando certificados intermediários ou filhos.</span><span class="sxs-lookup"><span data-stu-id="3c69e-194">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate"></a><span data-ttu-id="3c69e-195">Implementar um HttpClient usando um certificado</span><span class="sxs-lookup"><span data-stu-id="3c69e-195">Implement an HttpClient using a certificate</span></span>

<span data-ttu-id="3c69e-196">O cliente da API Web usa um `HttpClient`, que foi criado usando uma instância de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="3c69e-196">The web API client uses an `HttpClient`, which was created using an `IHttpClientFactory` instance.</span></span> <span data-ttu-id="3c69e-197">Isso não fornece uma maneira de definir um manipulador para o `HttpClient`, portanto, use uma `HttpRequestMessage` para adicionar o certificado ao cabeçalho de solicitação do `X-ARR-ClientCert`.</span><span class="sxs-lookup"><span data-stu-id="3c69e-197">This doesn't provide a way to define a handler for the `HttpClient`, so use an `HttpRequestMessage` to add the certificate to the `X-ARR-ClientCert` request header.</span></span> <span data-ttu-id="3c69e-198">O certificado é adicionado como uma cadeia de caracteres usando o método `GetRawCertDataString`.</span><span class="sxs-lookup"><span data-stu-id="3c69e-198">The certificate is added as a string using the `GetRawCertDataString` method.</span></span> 

```csharp
private async Task<JsonDocument> GetApiDataAsync()
{
    try
    {
        // Do not hardcode passwords in production code
        // Use thumbprint or key vault
        var cert = new X509Certificate2(
            Path.Combine(_environment.ContentRootPath, 
                "sts_dev_cert.pfx"), "1234");
        var client = _clientFactory.CreateClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = new Uri("https://localhost:44379/api/values"),
            Method = HttpMethod.Get,
        };

        request.Headers.Add("X-ARR-ClientCert", cert.GetRawCertDataString());
        var response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            var responseContent = await response.Content.ReadAsStringAsync();
            var data = JsonDocument.Parse(responseContent);

            return data;
        }

        throw new ApplicationException(
            $"Status code: {response.StatusCode}, " +
            $"Error: {response.ReasonPhrase}");
    }
    catch (Exception e)
    {
        throw new ApplicationException($"Exception {e}");
    }
}
```

<span data-ttu-id="3c69e-199">Se o certificado correto for enviado ao servidor, os dados serão retornados.</span><span class="sxs-lookup"><span data-stu-id="3c69e-199">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="3c69e-200">Se nenhum certificado ou o certificado errado for enviado, um código de status HTTP 403 será retornado.</span><span class="sxs-lookup"><span data-stu-id="3c69e-200">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="3c69e-201">Criar certificados no PowerShell</span><span class="sxs-lookup"><span data-stu-id="3c69e-201">Create certificates in PowerShell</span></span>

<span data-ttu-id="3c69e-202">Criar os certificados é a parte mais difícil de configurar esse fluxo.</span><span class="sxs-lookup"><span data-stu-id="3c69e-202">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="3c69e-203">Um certificado raiz pode ser criado usando o cmdlet `New-SelfSignedCertificate` PowerShell.</span><span class="sxs-lookup"><span data-stu-id="3c69e-203">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="3c69e-204">Ao criar o certificado, use uma senha forte.</span><span class="sxs-lookup"><span data-stu-id="3c69e-204">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="3c69e-205">É importante adicionar o parâmetro `KeyUsageProperty` e o parâmetro `KeyUsage`, conforme mostrado.</span><span class="sxs-lookup"><span data-stu-id="3c69e-205">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="3c69e-206">Criar AC raiz</span><span class="sxs-lookup"><span data-stu-id="3c69e-206">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="3c69e-207">O valor do parâmetro `-DnsName` deve corresponder ao destino de implantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3c69e-207">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="3c69e-208">Por exemplo, "localhost" para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="3c69e-208">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="3c69e-209">Instalar na raiz confiável</span><span class="sxs-lookup"><span data-stu-id="3c69e-209">Install in the trusted root</span></span>

<span data-ttu-id="3c69e-210">O certificado raiz precisa ser confiável no sistema host.</span><span class="sxs-lookup"><span data-stu-id="3c69e-210">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="3c69e-211">Um certificado raiz que não foi criado por uma autoridade de certificação não será confiável por padrão.</span><span class="sxs-lookup"><span data-stu-id="3c69e-211">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="3c69e-212">O link a seguir explica como isso pode ser feito no Windows:</span><span class="sxs-lookup"><span data-stu-id="3c69e-212">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="3c69e-213">Certificado intermediário</span><span class="sxs-lookup"><span data-stu-id="3c69e-213">Intermediate certificate</span></span>

<span data-ttu-id="3c69e-214">Um certificado intermediário agora pode ser criado a partir do certificado raiz.</span><span class="sxs-lookup"><span data-stu-id="3c69e-214">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="3c69e-215">Isso não é necessário para todos os casos de uso, mas talvez seja necessário criar muitos certificados ou precisar ativar ou desativar grupos de certificados.</span><span class="sxs-lookup"><span data-stu-id="3c69e-215">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="3c69e-216">O parâmetro `TextExtension` é necessário para definir o comprimento do caminho nas restrições básicas do certificado.</span><span class="sxs-lookup"><span data-stu-id="3c69e-216">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="3c69e-217">O certificado intermediário pode ser adicionado ao certificado intermediário confiável no sistema de host do Windows.</span><span class="sxs-lookup"><span data-stu-id="3c69e-217">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="3c69e-218">Criar certificado filho do certificado intermediário</span><span class="sxs-lookup"><span data-stu-id="3c69e-218">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="3c69e-219">Um certificado filho pode ser criado a partir do certificado intermediário.</span><span class="sxs-lookup"><span data-stu-id="3c69e-219">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="3c69e-220">Esta é a entidade final e não precisa criar mais certificados filho.</span><span class="sxs-lookup"><span data-stu-id="3c69e-220">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="3c69e-221">Criar certificado filho do certificado raiz</span><span class="sxs-lookup"><span data-stu-id="3c69e-221">Create child certificate from root certificate</span></span>

<span data-ttu-id="3c69e-222">Um certificado filho também pode ser criado a partir do certificado raiz diretamente.</span><span class="sxs-lookup"><span data-stu-id="3c69e-222">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="3c69e-223">Exemplo de certificado raiz intermediário-certificado</span><span class="sxs-lookup"><span data-stu-id="3c69e-223">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="3c69e-224">Ao usar os certificados raiz, intermediário ou filho, os certificados podem ser validados usando a impressão digital ou o PublicKey, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="3c69e-224">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```
