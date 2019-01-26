---
title: Habilitar a geração de código QR TOTP para aplicativos de autenticador no ASP.NET Core
author: rick-anderson
description: Descubra como habilitar a geração de código QR para aplicativos de autenticador TOTP que funcionam com autenticação de dois fatores de núcleo do ASP.NET.
ms.author: riande
ms.date: 08/14/2018
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: cf99cc21a7a1bb4d01c7cc092106d23375a1a76f
ms.sourcegitcommit: ca5f03210bedc61c6639a734ae5674bfe095dee8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55073121"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="b9d0a-103">Habilitar a geração de código QR TOTP para aplicativos de autenticador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9d0a-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="b9d0a-104">Códigos QR exige o ASP.NET Core 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b9d0a-105">ASP.NET Core é fornecido com suporte para aplicativos de autenticador para autenticação individual.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="b9d0a-106">Dois aplicativos de autenticador 2FA (autenticação) fator, usando uma baseada em tempo avulso senha algoritmo TOTP (), são o setor de abordagem 2fa recomendado.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="b9d0a-107">2FA usar TOTP é preferencial para SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="b9d0a-108">Um aplicativo autenticador fornece um código de 6 a 8 dígitos que os usuários devem inserir depois de confirmar seu nome de usuário e senha.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="b9d0a-109">Normalmente, um aplicativo autenticador é instalado em um Smartphone.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="b9d0a-110">Os modelos de aplicativo web ASP.NET Core autenticadores de suporte, mas não fornecem suporte para a geração de QRCode.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="b9d0a-111">Geradores de QRCode facilitam a configuração do 2FA.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="b9d0a-112">Este documento o orientará durante a adição [código QR](https://wikipedia.org/wiki/QR_code) geração para a página de configuração 2FA.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="b9d0a-113">Autenticação de dois fatores não acontece usando um provedor de autenticação externa, como [Google](xref:security/authentication/google-logins) ou [Facebook](xref:security/authentication/facebook-logins).</span><span class="sxs-lookup"><span data-stu-id="b9d0a-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="b9d0a-114">Logons externos são protegidos por qualquer mecanismo fornece o provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="b9d0a-115">Considere, por exemplo, o [Microsoft](xref:security/authentication/microsoft-logins) provedor de autenticação requer uma chave de hardware ou outra abordagem 2FA.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="b9d0a-116">Se os modelos padrão imposta 2FA "local", em seguida, os usuários seriam necessária para atender às duas abordagens 2FA, que não é um cenário de uso geral.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="b9d0a-117">Adicionar códigos QR para a página de configuração 2FA</span><span class="sxs-lookup"><span data-stu-id="b9d0a-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="b9d0a-118">Usam estas instruções *qrcode.js* do https://davidshimjs.github.io/qrcodejs/ repositório.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="b9d0a-119">Baixe o [biblioteca de javascript qrcode.js](https://davidshimjs.github.io/qrcodejs/) para o `wwwroot\lib` pasta em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="b9d0a-120">Siga as instruções em [identidade de Scaffold](xref:security/authentication/scaffold-identity) para gerar */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>
* <span data-ttu-id="b9d0a-121">Na */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, localize o `Scripts` seção no final do arquivo:</span><span class="sxs-lookup"><span data-stu-id="b9d0a-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="b9d0a-122">Na *Pages/Account/Manage/EnableAuthenticator.cshtml* (páginas do Razor) ou *Views/Manage/EnableAuthenticator.cshtml* (MVC), localize o `Scripts` seção no final do arquivo:</span><span class="sxs-lookup"><span data-stu-id="b9d0a-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="b9d0a-123">Atualizar o `Scripts` seção para adicionar uma referência para o `qrcodejs` biblioteca que você adicionou e uma chamada para gerar o código QR.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="b9d0a-124">Ele deve ser da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="b9d0a-124">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="b9d0a-125">Exclua o parágrafo que links para essas instruções.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="b9d0a-126">Executar seu aplicativo e certifique-se de que você pode digitalizar o código QR e validar o código que comprova o autenticador.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="b9d0a-127">Alterar o nome do site em que o código QR</span><span class="sxs-lookup"><span data-stu-id="b9d0a-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b9d0a-128">O nome do site em que o código QR é obtido do nome do projeto que você escolher ao criar inicialmente o seu projeto.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="b9d0a-129">Você pode alterá-lo procurando os `GenerateQrCodeUri(string email, string unformattedKey)` método na */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b9d0a-130">O nome do site em que o código QR é obtido do nome do projeto que você escolher ao criar inicialmente o seu projeto.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="b9d0a-131">Você pode alterá-lo procurando a `GenerateQrCodeUri(string email, string unformattedKey)` método na *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* arquivo (páginas do Razor) ou o *Controllers/ManageController.cs* arquivo (MVC).</span><span class="sxs-lookup"><span data-stu-id="b9d0a-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b9d0a-132">O código padrão do modelo será semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="b9d0a-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenicatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="b9d0a-133">O segundo parâmetro na chamada para `string.Format` é o nome do site, obtido do nome da solução.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="b9d0a-134">Ele pode ser alterado para qualquer valor, mas ele sempre deve ser codificado por URL.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="b9d0a-135">Usando uma biblioteca diferente do código QR</span><span class="sxs-lookup"><span data-stu-id="b9d0a-135">Using a different QR Code library</span></span>

<span data-ttu-id="b9d0a-136">Você pode substituir a biblioteca de código QR com sua biblioteca preferencial.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="b9d0a-137">O HTML não contém um `qrCode` fornece de sua biblioteca de elemento no qual você pode colocar um código QR por qualquer mecanismo.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="b9d0a-138">A URL formatada corretamente para o código QR está disponível na:</span><span class="sxs-lookup"><span data-stu-id="b9d0a-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="b9d0a-139">`AuthenticatorUri` propriedade do modelo.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="b9d0a-140">`data-url` propriedade no `qrCodeData` elemento.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="b9d0a-141">TOTP cliente e servidor distorção de tempo</span><span class="sxs-lookup"><span data-stu-id="b9d0a-141">TOTP client and server time skew</span></span>

<span data-ttu-id="b9d0a-142">Autenticação de TOTP (senha de uso único baseados em tempo) depende do dispositivo o servidor e o autenticador, tendo a hora correta.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="b9d0a-143">Tokens duram por 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="b9d0a-144">Se os logons de 2FA TOTP estiverem falhando, verifique se a hora do servidor é precisos e preferencialmente sincronizado a um serviço NTP preciso.</span><span class="sxs-lookup"><span data-stu-id="b9d0a-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
