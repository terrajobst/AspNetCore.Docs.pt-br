---
title: Configurar a autenticação de certificado no ASP.NET Core
author: blowdart
description: Saiba como configurar a autenticação de certificado no ASP.NET Core para IIS e HTTP. sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 08/19/2019
uid: security/authentication/certauth
ms.openlocfilehash: bb375cf380175daf2399f3b56f543819ee5692b8
ms.sourcegitcommit: 07cd66e367d080acb201c7296809541599c947d1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71039249"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="4ec7e-103">Configurar a autenticação de certificado no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ec7e-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="4ec7e-104">`Microsoft.AspNetCore.Authentication.Certificate`contém uma implementação semelhante à [autenticação de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="4ec7e-105">A autenticação de certificado ocorre no nível de TLS, muito antes que ele chegue a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="4ec7e-106">Mais precisamente, esse é um manipulador de autenticação que valida o certificado e, em seguida, fornece um evento no qual você pode resolver esse certificado `ClaimsPrincipal`para um.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="4ec7e-107">[Configure seu host](#configure-your-host-to-require-certificates) para autenticação de certificado, seja ele IIS, Kestrel, aplicativos Web do Azure ou qualquer outra coisa que você esteja usando.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="4ec7e-108">Cenários de balanceador de carga e proxy</span><span class="sxs-lookup"><span data-stu-id="4ec7e-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="4ec7e-109">A autenticação de certificado é um cenário com monitoração de estado usado principalmente onde um proxy ou um balanceador de carga não trata o tráfego entre clientes e servidores.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="4ec7e-110">Se um proxy ou um balanceador de carga for usado, a autenticação de certificado só funcionará se o proxy ou o balanceador de carga:</span><span class="sxs-lookup"><span data-stu-id="4ec7e-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="4ec7e-111">Manipula a autenticação.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-111">Handles the authentication.</span></span>
* <span data-ttu-id="4ec7e-112">Passa as informações de autenticação do usuário para o aplicativo (por exemplo, em um cabeçalho de solicitação), que atua nas informações de autenticação.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="4ec7e-113">Uma alternativa à autenticação de certificado em ambientes em que proxies e balanceadores de carga são usados é Active Directory serviços federados (ADFS) com o OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="4ec7e-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="4ec7e-114">Introdução</span><span class="sxs-lookup"><span data-stu-id="4ec7e-114">Get started</span></span>

<span data-ttu-id="4ec7e-115">Adquira um certificado HTTPS, aplique-o e [configure seu host](#configure-your-host-to-require-certificates) para exigir certificados.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-115">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="4ec7e-116">Em seu aplicativo Web, adicione uma referência ao `Microsoft.AspNetCore.Authentication.Certificate` pacote.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-116">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="4ec7e-117">Em seguida, `Startup.Configure` no método, `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` chame com suas opções, fornecendo um delegado `OnCertificateValidated` para fazer qualquer validação suplementar no certificado do cliente enviado com solicitações.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-117">Then in the `Startup.Configure` method, call `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="4ec7e-118">Transforme essas informações em `ClaimsPrincipal` um e defina-as `context.Principal` na propriedade.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="4ec7e-119">Se a autenticação falhar, esse manipulador retornará `403 (Forbidden)` uma resposta em `401 (Unauthorized)`vez de a, como você pode esperar.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="4ec7e-120">O raciocínio é que a autenticação deve ocorrer durante a conexão de TLS inicial.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="4ec7e-121">No momento em que atinge o manipulador, é tarde demais.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="4ec7e-122">Não há como atualizar a conexão de uma conexão anônima para uma com um certificado.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="4ec7e-123">Além disso `app.UseAuthentication();` , adicione `Startup.Configure` o método.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="4ec7e-124">Caso contrário, o HttpContext. User não será definido como `ClaimsPrincipal` criado a partir do certificado.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-124">Otherwise, the HttpContext.User will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="4ec7e-125">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="4ec7e-125">For example:</span></span>

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

<span data-ttu-id="4ec7e-126">O exemplo anterior demonstra a maneira padrão de adicionar autenticação de certificado.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="4ec7e-127">O manipulador constrói uma entidade de usuário usando as propriedades comuns do certificado.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="4ec7e-128">Configurar validação de certificado</span><span class="sxs-lookup"><span data-stu-id="4ec7e-128">Configure certificate validation</span></span>

<span data-ttu-id="4ec7e-129">O `CertificateAuthenticationOptions` manipulador tem algumas validações internas que são as validações mínimas que você deve executar em um certificado.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="4ec7e-130">Cada uma dessas configurações é habilitada por padrão.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="4ec7e-131">AllowedCertificateTypes = encadeado, SelfSigned ou todos (encadeados | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="4ec7e-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="4ec7e-132">Essa verificação valida que apenas o tipo de certificado apropriado é permitido.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-132">This check validates that only the appropriate certificate type is allowed.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="4ec7e-133">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="4ec7e-133">ValidateCertificateUse</span></span>

<span data-ttu-id="4ec7e-134">Essa verificação valida que o certificado apresentado pelo cliente tem o EKU (uso estendido de chave) de autenticação de cliente ou nenhum EKUs.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-134">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="4ec7e-135">Como as especificações dizem, se nenhum EKU for especificado, todos os EKUs serão considerados válidos.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-135">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="4ec7e-136">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="4ec7e-136">ValidateValidityPeriod</span></span>

<span data-ttu-id="4ec7e-137">Essa verificação valida que o certificado está dentro do período de validade.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-137">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="4ec7e-138">Em cada solicitação, o manipulador garante que um certificado que era válido quando foi apresentado não tenha expirado durante sua sessão atual.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-138">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="4ec7e-139">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="4ec7e-139">RevocationFlag</span></span>

<span data-ttu-id="4ec7e-140">Um sinalizador que especifica quais certificados na cadeia são verificados para revogação.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-140">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="4ec7e-141">As verificações de revogação são executadas somente quando o certificado é encadeado a um certificado raiz.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-141">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="4ec7e-142">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="4ec7e-142">RevocationMode</span></span>

<span data-ttu-id="4ec7e-143">Um sinalizador que especifica como as verificações de revogação são executadas.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-143">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="4ec7e-144">A especificação de uma verificação online pode resultar em um longo atraso enquanto a autoridade de certificação é contatada.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-144">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="4ec7e-145">As verificações de revogação são executadas somente quando o certificado é encadeado a um certificado raiz.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-145">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="4ec7e-146">Posso configurar meu aplicativo para exigir um certificado somente em determinados caminhos?</span><span class="sxs-lookup"><span data-stu-id="4ec7e-146">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="4ec7e-147">Isso não é possível.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-147">This isn't possible.</span></span> <span data-ttu-id="4ec7e-148">Lembre-se de que a troca de certificado faz com que o início da conversa HTTPS seja feito pelo servidor antes que a primeira solicitação seja recebida nessa conexão, portanto, não é possível fazer o escopo com base em qualquer campo de solicitação.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-148">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="4ec7e-149">Eventos de manipulador</span><span class="sxs-lookup"><span data-stu-id="4ec7e-149">Handler events</span></span>

<span data-ttu-id="4ec7e-150">O manipulador tem dois eventos:</span><span class="sxs-lookup"><span data-stu-id="4ec7e-150">The handler has two events:</span></span>

* <span data-ttu-id="4ec7e-151">`OnAuthenticationFailed`&ndash; Chamado se ocorrer uma exceção durante a autenticação e permitir que você reaja.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-151">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="4ec7e-152">`OnCertificateValidated`&ndash; Chamado depois que o certificado foi validado, passou na validação e uma entidade de segurança padrão foi criada.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-152">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="4ec7e-153">Esse evento permite que você execute sua própria validação e aumente ou substitua a entidade de segurança.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-153">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="4ec7e-154">Para obter exemplos, inclua:</span><span class="sxs-lookup"><span data-stu-id="4ec7e-154">For examples include:</span></span>
  * <span data-ttu-id="4ec7e-155">Determinando se o certificado é conhecido por seus serviços.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-155">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="4ec7e-156">Construindo sua própria entidade de segurança.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-156">Constructing your own principal.</span></span> <span data-ttu-id="4ec7e-157">Considere o exemplo a seguir em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4ec7e-157">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="4ec7e-158">Se você achar que o certificado de entrada não atende à sua validação extra `context.Fail("failure reason")` , chame com um motivo de falha.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-158">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="4ec7e-159">Para a funcionalidade real, você provavelmente desejará chamar um serviço registrado na injeção de dependência que se conecta a um banco de dados ou a outro tipo de armazenamento de usuário.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-159">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="4ec7e-160">Acesse seu serviço usando o contexto passado para seu representante.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-160">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="4ec7e-161">Considere o exemplo a seguir em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4ec7e-161">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="4ec7e-162">Conceitualmente, a validação do certificado é um problema de autorização.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-162">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="4ec7e-163">A adição de um check-in, por exemplo, um emissor ou uma impressão digital em uma política de `OnCertificateValidated`autorização, em vez de dentro, é perfeitamente aceitável.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-163">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="4ec7e-164">Configurar o host para exigir certificados</span><span class="sxs-lookup"><span data-stu-id="4ec7e-164">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="4ec7e-165">Kestrel</span><span class="sxs-lookup"><span data-stu-id="4ec7e-165">Kestrel</span></span>

<span data-ttu-id="4ec7e-166">No *Program.cs*, configure o Kestrel da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="4ec7e-166">In *Program.cs*, configure Kestrel as follows:</span></span>

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

### <a name="iis"></a><span data-ttu-id="4ec7e-167">IIS</span><span class="sxs-lookup"><span data-stu-id="4ec7e-167">IIS</span></span>

<span data-ttu-id="4ec7e-168">Conclua as seguintes etapas no Gerenciador do IIS:</span><span class="sxs-lookup"><span data-stu-id="4ec7e-168">Complete the following steps In IIS Manager:</span></span>

1. <span data-ttu-id="4ec7e-169">Selecione seu site na guia **conexões** .</span><span class="sxs-lookup"><span data-stu-id="4ec7e-169">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="4ec7e-170">Clique duas vezes na opção **configurações de SSL** na janela exibição de **recursos** .</span><span class="sxs-lookup"><span data-stu-id="4ec7e-170">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="4ec7e-171">Marque a caixa de seleção **exigir SSL** e selecione o botão de opção **exigir** na seção **certificados do cliente** .</span><span class="sxs-lookup"><span data-stu-id="4ec7e-171">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Configurações de certificado de cliente no IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="4ec7e-173">Proxies da Web personalizados e do Azure</span><span class="sxs-lookup"><span data-stu-id="4ec7e-173">Azure and custom web proxies</span></span>

<span data-ttu-id="4ec7e-174">Consulte a [documentação de host e implantação](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) para saber como configurar o middleware de encaminhamento de certificado.</span><span class="sxs-lookup"><span data-stu-id="4ec7e-174">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>
