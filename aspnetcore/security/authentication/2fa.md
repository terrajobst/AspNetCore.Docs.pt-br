---
title: Autenticação de dois fatores com SMS no ASP.NET Core
author: rick-anderson
description: Saiba como configurar a autenticação de dois fatores (2FA) com um aplicativo ASP.NET Core.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
uid: security/authentication/2fa
ms.openlocfilehash: 68219579be9b7a7b25da6e348054e1ff2015cf5f
ms.sourcegitcommit: e54672f5c493258dc449fac5b98faf47eb123b28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71248390"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="4a042-103">Autenticação de dois fatores com SMS no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a042-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="4a042-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [suíço desenvolvedores](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="4a042-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="4a042-105">Dois aplicativos de autenticador 2FA (autenticação) fator, usando uma baseada em tempo avulso senha algoritmo TOTP (), são o setor de abordagem 2fa recomendado.</span><span class="sxs-lookup"><span data-stu-id="4a042-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="4a042-106">2FA usar TOTP é preferencial para SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="4a042-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="4a042-107">Para obter mais informações, consulte [geração de código de QR habilitar TOTP para aplicativos de autenticador no ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) para ASP.NET Core 2.0 e versões posteriores.</span><span class="sxs-lookup"><span data-stu-id="4a042-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="4a042-108">Este tutorial mostra como configurar a autenticação de dois fatores (2FA) usando o SMS.</span><span class="sxs-lookup"><span data-stu-id="4a042-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="4a042-109">As instruções são fornecidas para [twilio](https://www.twilio.com/) e [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), mas você pode usar qualquer outro provedor SMS.</span><span class="sxs-lookup"><span data-stu-id="4a042-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="4a042-110">Recomendamos que você conclua [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm) antes de iniciar este tutorial.</span><span class="sxs-lookup"><span data-stu-id="4a042-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="4a042-111">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span><span class="sxs-lookup"><span data-stu-id="4a042-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="4a042-112">[Como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="4a042-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="4a042-113">Criar um projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a042-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="4a042-114">Criar um novo aplicativo web de ASP.NET Core chamado `Web2FA` com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="4a042-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="4a042-115">Siga as instruções em <xref:security/enforcing-ssl> para configurar e exigir HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4a042-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="4a042-116">Criar uma conta do SMS</span><span class="sxs-lookup"><span data-stu-id="4a042-116">Create an SMS account</span></span>

<span data-ttu-id="4a042-117">Criar uma conta SMS, por exemplo, no [twilio](https://www.twilio.com/) ou [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span><span class="sxs-lookup"><span data-stu-id="4a042-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="4a042-118">Registre as credenciais de autenticação (para twilio: AccountId e authToken, para ASPSMS: UserKey e senha).</span><span class="sxs-lookup"><span data-stu-id="4a042-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="4a042-119">Descobrir as credenciais do provedor de SMS</span><span class="sxs-lookup"><span data-stu-id="4a042-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="4a042-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="4a042-120">**Twilio:**</span></span>

<span data-ttu-id="4a042-121">Na guia painel da sua conta do twilio, copie o **SID da conta** e o **token de autenticação**.</span><span class="sxs-lookup"><span data-stu-id="4a042-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="4a042-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="4a042-122">**ASPSMS:**</span></span>

<span data-ttu-id="4a042-123">Em suas configurações de conta, navegue até **userKey** e copie-o junto com sua **senha**.</span><span class="sxs-lookup"><span data-stu-id="4a042-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="4a042-124">Posteriormente, armazenaremos esses valores com a ferramenta secret manager nas chaves `SMSAccountIdentification` e `SMSAccountPassword`.</span><span class="sxs-lookup"><span data-stu-id="4a042-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="4a042-125">Especificando SenderID / originador</span><span class="sxs-lookup"><span data-stu-id="4a042-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="4a042-126">**Twilio** Na guia números, copie o número de **telefone**twilio.</span><span class="sxs-lookup"><span data-stu-id="4a042-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="4a042-127">**ASPSMS:** No menu desbloquear originadores, desbloqueie um ou mais originadores ou escolha um originador alfanumérico (sem suporte em todas as redes).</span><span class="sxs-lookup"><span data-stu-id="4a042-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="4a042-128">Posteriormente, armazenaremos esse valor com a ferramenta secret manager na chave do `SMSAccountFrom`.</span><span class="sxs-lookup"><span data-stu-id="4a042-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="4a042-129">Forneça credenciais para o serviço SMS</span><span class="sxs-lookup"><span data-stu-id="4a042-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="4a042-130">Vamos usar o [padrão de opções](xref:fundamentals/configuration/options) para acessar as configurações de conta e chave de usuário.</span><span class="sxs-lookup"><span data-stu-id="4a042-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="4a042-131">Crie uma classe para buscar a chave segura do SMS.</span><span class="sxs-lookup"><span data-stu-id="4a042-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="4a042-132">Para este exemplo, o `SMSoptions` classe é criada na *Services/SMSoptions.cs* arquivo.</span><span class="sxs-lookup"><span data-stu-id="4a042-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="4a042-133">Defina as `SMSAccountIdentification`, `SMSAccountPassword` e `SMSAccountFrom` com o [ferramenta secret manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="4a042-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="4a042-134">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="4a042-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="4a042-135">Adicione o pacote NuGet do provedor de SMS.</span><span class="sxs-lookup"><span data-stu-id="4a042-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="4a042-136">Do Manager Console (PMC) execute:</span><span class="sxs-lookup"><span data-stu-id="4a042-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="4a042-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="4a042-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="4a042-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="4a042-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="4a042-139">Adicione código a *Services/MessageServices.cs* arquivo para habilitar o SMS.</span><span class="sxs-lookup"><span data-stu-id="4a042-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="4a042-140">Use o Twilio ou seção ASPSMS:</span><span class="sxs-lookup"><span data-stu-id="4a042-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="4a042-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="4a042-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="4a042-142">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="4a042-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="4a042-143">Configurar a inicialização para usar `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="4a042-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="4a042-144">Adicione `SMSoptions` ao contêiner de serviço na `ConfigureServices` método na *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a042-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="4a042-145">Habilitar a autenticação de dois fatores</span><span class="sxs-lookup"><span data-stu-id="4a042-145">Enable two-factor authentication</span></span>

<span data-ttu-id="4a042-146">Abra o arquivo de exibição do Razor views/ *Manage/index. cshtml* e remova os caracteres de comentário (portanto, nenhuma marcação é comentada).</span><span class="sxs-lookup"><span data-stu-id="4a042-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="4a042-147">Faça logon com a autenticação de dois fatores</span><span class="sxs-lookup"><span data-stu-id="4a042-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="4a042-148">Execute o aplicativo e registrar um novo usuário</span><span class="sxs-lookup"><span data-stu-id="4a042-148">Run the app and register a new user</span></span>

![Exibição do registro de aplicativo Web aberta no Microsoft Edge](2fa/_static/login2fa1.png)

* <span data-ttu-id="4a042-150">Toque em seu nome de usuário que ativa o `Index` método de ação no controlador de gerenciar.</span><span class="sxs-lookup"><span data-stu-id="4a042-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="4a042-151">Em seguida, toque no número de telefone **adicionar** link.</span><span class="sxs-lookup"><span data-stu-id="4a042-151">Then tap the phone number **Add** link.</span></span>

![Gerenciar o modo de exibição – tocar no link "Adicionar"](2fa/_static/login2fa2.png)

* <span data-ttu-id="4a042-153">Adicionar um número de telefone que receberá o código de verificação e toque **enviar código de verificação**.</span><span class="sxs-lookup"><span data-stu-id="4a042-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![Adicionar página de número de telefone](2fa/_static/login2fa3.png)

* <span data-ttu-id="4a042-155">Você receberá uma mensagem de texto com o código de verificação.</span><span class="sxs-lookup"><span data-stu-id="4a042-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="4a042-156">Insira-o e toque em **enviar**</span><span class="sxs-lookup"><span data-stu-id="4a042-156">Enter it and tap **Submit**</span></span>

![Verifique se a página de número de telefone](2fa/_static/login2fa4.png)

<span data-ttu-id="4a042-158">Se você não receber uma mensagem de texto, consulte a página de registro do twilio.</span><span class="sxs-lookup"><span data-stu-id="4a042-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="4a042-159">O modo de gerenciar mostra que o número de telefone foi adicionado com êxito.</span><span class="sxs-lookup"><span data-stu-id="4a042-159">The Manage view shows your phone number was added successfully.</span></span>

![Gerenciar o modo de exibição - número de telefone foi adicionado com êxito](2fa/_static/login2fa5.png)

* <span data-ttu-id="4a042-161">Toque **habilitar** para habilitar a autenticação de dois fatores.</span><span class="sxs-lookup"><span data-stu-id="4a042-161">Tap **Enable** to enable two-factor authentication.</span></span>

![Gerenciar o modo de exibição – habilitar a autenticação de dois fatores](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="4a042-163">Autenticação de dois fatores do teste</span><span class="sxs-lookup"><span data-stu-id="4a042-163">Test two-factor authentication</span></span>

* <span data-ttu-id="4a042-164">Faça logoff.</span><span class="sxs-lookup"><span data-stu-id="4a042-164">Log off.</span></span>

* <span data-ttu-id="4a042-165">Iniciar sessão.</span><span class="sxs-lookup"><span data-stu-id="4a042-165">Log in.</span></span>

* <span data-ttu-id="4a042-166">A conta de usuário tiver habilitado a autenticação de dois fatores, portanto, você precisa fornecer o segundo fator de autenticação.</span><span class="sxs-lookup"><span data-stu-id="4a042-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="4a042-167">Neste tutorial, você habilitou a verificação por telefone.</span><span class="sxs-lookup"><span data-stu-id="4a042-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="4a042-168">Os modelos internos permitem que você configure o email como o segundo fator.</span><span class="sxs-lookup"><span data-stu-id="4a042-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="4a042-169">Você pode configurar os fatores adicionais de segundo para autenticação, como códigos QR.</span><span class="sxs-lookup"><span data-stu-id="4a042-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="4a042-170">Toque **enviar**.</span><span class="sxs-lookup"><span data-stu-id="4a042-170">Tap **Submit**.</span></span>

![Enviar modo de exibição de código de verificação](2fa/_static/login2fa7.png)

* <span data-ttu-id="4a042-172">Insira o código que você pode obter na mensagem SMS.</span><span class="sxs-lookup"><span data-stu-id="4a042-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="4a042-173">Clicar na **lembrar deste navegador** caixa de seleção serão isentos da necessidade de usar 2FA para fazer logon usando o mesmo dispositivo e o navegador.</span><span class="sxs-lookup"><span data-stu-id="4a042-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="4a042-174">Habilitar a 2FA e clicando em **lembrar deste navegador** lhe fornecerá 2FA forte proteção contra usuários mal-intencionados que tentam acessar sua conta, desde que não tiverem acesso ao seu dispositivo.</span><span class="sxs-lookup"><span data-stu-id="4a042-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="4a042-175">Você pode fazer isso em qualquer dispositivo privado que você usa regularmente.</span><span class="sxs-lookup"><span data-stu-id="4a042-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="4a042-176">Definindo **lembrar deste navegador**, você obtém a segurança adicional de 2FA de dispositivos que você não usa regularmente, e você obtém a conveniência em não ter que passar por 2FA em seus próprios dispositivos.</span><span class="sxs-lookup"><span data-stu-id="4a042-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![Verifique se o modo de exibição](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="4a042-178">Bloqueio de conta para proteção contra ataques de força bruta</span><span class="sxs-lookup"><span data-stu-id="4a042-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="4a042-179">Bloqueio de conta é recomendado com 2FA.</span><span class="sxs-lookup"><span data-stu-id="4a042-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="4a042-180">Depois que um usuário faz logon por meio de uma conta local ou social, cada tentativa com falha no 2FA é armazenada.</span><span class="sxs-lookup"><span data-stu-id="4a042-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="4a042-181">Se o número máximo de tentativas de acesso com falha for atingido, o usuário será bloqueado (padrão: bloqueio de 5 minutos após 5 tentativas de acesso com falha).</span><span class="sxs-lookup"><span data-stu-id="4a042-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="4a042-182">Uma autenticação bem-sucedida redefine a contagem de tentativas de acesso com falha e redefine o relógio.</span><span class="sxs-lookup"><span data-stu-id="4a042-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="4a042-183">O máximo falhas em tentativas de acesso e tempo de bloqueio pode ser definido com [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) e [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span><span class="sxs-lookup"><span data-stu-id="4a042-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="4a042-184">O exemplo a seguir configura o bloqueio de conta por 10 minutos após 10 tentativas de acesso:</span><span class="sxs-lookup"><span data-stu-id="4a042-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="4a042-185">Confirme [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) define `lockoutOnFailure` para `true`:</span><span class="sxs-lookup"><span data-stu-id="4a042-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
