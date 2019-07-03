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
# <a name="overview"></a><span data-ttu-id="e0793-103">Visão geral</span><span class="sxs-lookup"><span data-stu-id="e0793-103">Overview</span></span>

<span data-ttu-id="e0793-104">`Microsoft.AspNetCore.Authentication.Certificate` contém uma implementação semelhante à [autenticação de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0793-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="e0793-105">Autenticação de certificado ocorre no nível de TLS, longa antes que ela nunca seja ao ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0793-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="e0793-106">Mais precisamente, isso é um manipulador de autenticação que valida o certificado e, em seguida, lhe dá um evento em que você pode resolver esse certificado para um `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="e0793-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="e0793-107">[Configurar o host](#configure-your-host-to-require-certificates) para autenticação de certificado, seja ele IIS, o Kestrel, aplicativos Web do Azure ou qualquer outra coisa que você está usando.</span><span class="sxs-lookup"><span data-stu-id="e0793-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="get-started"></a><span data-ttu-id="e0793-108">Introdução</span><span class="sxs-lookup"><span data-stu-id="e0793-108">Get started</span></span>

<span data-ttu-id="e0793-109">Adquirir um certificado HTTPS, aplicá-la, e [configurar o host](#configure-your-host-to-require-certificates) para exigir certificados.</span><span class="sxs-lookup"><span data-stu-id="e0793-109">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="e0793-110">No seu aplicativo web, adicione uma referência para o `Microsoft.AspNetCore.Authentication.Certificate` pacote.</span><span class="sxs-lookup"><span data-stu-id="e0793-110">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="e0793-111">Em seguida, nos `Startup.Configure` método, chame `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` com suas opções, fornecendo um delegado para `OnCertificateValidated` fazer nenhuma validação suplementar no certificado do cliente enviado com solicitações.</span><span class="sxs-lookup"><span data-stu-id="e0793-111">Then in the `Startup.Configure` method, call `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="e0793-112">Ativar essas informações em um `ClaimsPrincipal` e defini-lo sobre a `context.Principal` propriedade.</span><span class="sxs-lookup"><span data-stu-id="e0793-112">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="e0793-113">Se a autenticação falhar, esse manipulador retorna um `403 (Forbidden)` resposta em vez disso, um `401 (Unauthorized)`, como você poderia esperar.</span><span class="sxs-lookup"><span data-stu-id="e0793-113">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="e0793-114">O raciocínio é que a autenticação deve acontecer durante a conexão inicial do TLS.</span><span class="sxs-lookup"><span data-stu-id="e0793-114">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="e0793-115">No momento em que ele atingir o manipulador, é tarde demais.</span><span class="sxs-lookup"><span data-stu-id="e0793-115">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="e0793-116">Não é possível atualizar a conexão de uma conexão anônima para um com um certificado.</span><span class="sxs-lookup"><span data-stu-id="e0793-116">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="e0793-117">Também adicione `app.UseAuthentication();` no `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="e0793-117">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="e0793-118">Caso contrário, o HttpContext não será definido como `ClaimsPrincipal` criado no certificado.</span><span class="sxs-lookup"><span data-stu-id="e0793-118">Otherwise, the HttpContext.User will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="e0793-119">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e0793-119">For example:</span></span>

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

<span data-ttu-id="e0793-120">O exemplo anterior demonstra a maneira padrão para adicionar a autenticação de certificado.</span><span class="sxs-lookup"><span data-stu-id="e0793-120">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="e0793-121">O manipulador constrói uma entidade de usuário usando as propriedades comuns de certificado.</span><span class="sxs-lookup"><span data-stu-id="e0793-121">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="e0793-122">Configurar a validação do certificado</span><span class="sxs-lookup"><span data-stu-id="e0793-122">Configure certificate validation</span></span>

<span data-ttu-id="e0793-123">O `CertificateAuthenticationOptions` manipulador tem algumas validações internas que são as validações mínimo, você deve executar em um certificado.</span><span class="sxs-lookup"><span data-stu-id="e0793-123">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="e0793-124">Cada uma dessas configurações é habilitada por padrão.</span><span class="sxs-lookup"><span data-stu-id="e0793-124">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="e0793-125">AllowedCertificateTypes = encadeadas, SelfSigned ou All (encadeadas | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="e0793-125">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="e0793-126">Essa verificação valida que apenas o tipo de certificado apropriado é permitido.</span><span class="sxs-lookup"><span data-stu-id="e0793-126">This check validates that only the appropriate certificate type is allowed.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="e0793-127">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="e0793-127">ValidateCertificateUse</span></span>

<span data-ttu-id="e0793-128">Essa verificação valida o certificado apresentado pelo cliente tem a autenticação de cliente estendido do uso da chave (EKU) ou nenhum EKU em todos os.</span><span class="sxs-lookup"><span data-stu-id="e0793-128">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="e0793-129">Conforme as especificações que, se nenhum EKU for especificado, todos os EKUs são considerados válidos.</span><span class="sxs-lookup"><span data-stu-id="e0793-129">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="e0793-130">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="e0793-130">ValidateValidityPeriod</span></span>

<span data-ttu-id="e0793-131">Essa verificação valida o certificado está dentro do período de validade.</span><span class="sxs-lookup"><span data-stu-id="e0793-131">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="e0793-132">Em cada solicitação, o manipulador garante que um certificado era válido quando ele foi apresentado expirou durante sua sessão atual.</span><span class="sxs-lookup"><span data-stu-id="e0793-132">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="e0793-133">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="e0793-133">RevocationFlag</span></span>

<span data-ttu-id="e0793-134">Um sinalizador que especifica quais certificados na cadeia é verificado para revogação.</span><span class="sxs-lookup"><span data-stu-id="e0793-134">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="e0793-135">Verificações de revogação são executadas somente quando o certificado está encadeado a um certificado raiz.</span><span class="sxs-lookup"><span data-stu-id="e0793-135">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="e0793-136">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="e0793-136">RevocationMode</span></span>

<span data-ttu-id="e0793-137">Um sinalizador que especifica como as verificações de revogação são executadas.</span><span class="sxs-lookup"><span data-stu-id="e0793-137">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="e0793-138">Especificar uma verificação on-line pode resultar em um longo atraso enquanto a autoridade de certificação é contatada.</span><span class="sxs-lookup"><span data-stu-id="e0793-138">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="e0793-139">Verificações de revogação são executadas somente quando o certificado está encadeado a um certificado raiz.</span><span class="sxs-lookup"><span data-stu-id="e0793-139">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="e0793-140">Posso configurar meu aplicativo para exigir um certificado somente em determinados caminhos?</span><span class="sxs-lookup"><span data-stu-id="e0793-140">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="e0793-141">Não é possível.</span><span class="sxs-lookup"><span data-stu-id="e0793-141">This isn't possible.</span></span> <span data-ttu-id="e0793-142">Lembre-se de que a troca de certificados é feita para que o início da conversa HTTPS, ele é feito pelo servidor antes que a primeira solicitação é recebida nessa conexão para que não seja possível ao escopo com base em quaisquer campos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="e0793-142">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="e0793-143">Eventos de manipulador</span><span class="sxs-lookup"><span data-stu-id="e0793-143">Handler events</span></span>

<span data-ttu-id="e0793-144">O manipulador tem dois eventos:</span><span class="sxs-lookup"><span data-stu-id="e0793-144">The handler has two events:</span></span>

* <span data-ttu-id="e0793-145">`OnAuthenticationFailed` &ndash; Chamado se uma exceção ocorre durante a autenticação e permite reagir.</span><span class="sxs-lookup"><span data-stu-id="e0793-145">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="e0793-146">`OnCertificateValidated` &ndash; Chamado depois que o certificado tiver sido validado, passou na validação e uma entidade de segurança padrão foi criada.</span><span class="sxs-lookup"><span data-stu-id="e0793-146">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="e0793-147">Esse evento permite que você execute sua própria validação e incrementar ou substituir a entidade de segurança.</span><span class="sxs-lookup"><span data-stu-id="e0793-147">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="e0793-148">Para obter exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="e0793-148">For examples include:</span></span>
  * <span data-ttu-id="e0793-149">Determinando se o certificado é conhecido por seus serviços.</span><span class="sxs-lookup"><span data-stu-id="e0793-149">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="e0793-150">Construindo sua própria entidade.</span><span class="sxs-lookup"><span data-stu-id="e0793-150">Constructing your own principal.</span></span> <span data-ttu-id="e0793-151">Considere o exemplo a seguir em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e0793-151">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e0793-152">Se você encontrar o certificado de entrada não atende à sua validação extra, chamada `context.Fail("failure reason")` com um motivo da falha.</span><span class="sxs-lookup"><span data-stu-id="e0793-152">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="e0793-153">Funcionalidade de verdade, você provavelmente desejará chamar um serviço registrado na injeção de dependência que se conecta a um banco de dados ou outro tipo de repositório do usuário.</span><span class="sxs-lookup"><span data-stu-id="e0793-153">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="e0793-154">Acesse seu serviço usando o contexto passado para o seu representante.</span><span class="sxs-lookup"><span data-stu-id="e0793-154">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="e0793-155">Considere o exemplo a seguir em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e0793-155">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e0793-156">Conceitualmente, a validação do certificado é uma preocupação de autorização.</span><span class="sxs-lookup"><span data-stu-id="e0793-156">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="e0793-157">Adicionando uma verificação, por exemplo, um emissor ou impressão digital em uma política de autorização, em vez de inside `OnCertificateValidated`, é perfeitamente aceitável.</span><span class="sxs-lookup"><span data-stu-id="e0793-157">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="e0793-158">Configurar o host para exigir certificados</span><span class="sxs-lookup"><span data-stu-id="e0793-158">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="e0793-159">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e0793-159">Kestrel</span></span>

<span data-ttu-id="e0793-160">Na *Program.cs*, configure o Kestrel da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="e0793-160">In *Program.cs*, configure Kestrel as follows:</span></span>

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

### <a name="iis"></a><span data-ttu-id="e0793-161">IIS</span><span class="sxs-lookup"><span data-stu-id="e0793-161">IIS</span></span>

<span data-ttu-id="e0793-162">Conclua as etapas a seguir no Gerenciador do IIS:</span><span class="sxs-lookup"><span data-stu-id="e0793-162">Complete the following steps In IIS Manager:</span></span>

1. <span data-ttu-id="e0793-163">Selecione o seu site do **conexões** guia.</span><span class="sxs-lookup"><span data-stu-id="e0793-163">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="e0793-164">Clique duas vezes o **configurações de SSL** opção a **exibição de recursos** janela.</span><span class="sxs-lookup"><span data-stu-id="e0793-164">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="e0793-165">Verificar a **exigir SSL** caixa de seleção e selecione o **exigem** botão de opção a **certificados de cliente** seção.</span><span class="sxs-lookup"><span data-stu-id="e0793-165">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Configurações de certificado de cliente no IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="e0793-167">Azure e proxies da web personalizado</span><span class="sxs-lookup"><span data-stu-id="e0793-167">Azure and custom web proxies</span></span>

<span data-ttu-id="e0793-168">Consulte a [hospedar e implantar documentação](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) para saber como configurar o encaminhamento de middleware de certificado.</span><span class="sxs-lookup"><span data-stu-id="e0793-168">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>
