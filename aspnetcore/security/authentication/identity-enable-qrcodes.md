---
title: Habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core
author: rick-anderson
description: Descubra como habilitar a geração de código QR para aplicativos do autenticador TOTP que funcionam com ASP.NET Core autenticação de dois fatores.
ms.author: riande
ms.date: 08/14/2018
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: a7fdc86b3fe94e714e5147c89a32fce13757d1c1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665308"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>Habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core

::: moniker range="<= aspnetcore-2.0"

Os códigos QR requerem ASP.NET Core 2,0 ou posterior.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

O ASP.NET Core é fornecido com suporte para aplicativos autenticadores para autenticação individual. Dois aplicativos de autenticador 2FA (autenticação) fator, usando uma baseada em tempo avulso senha algoritmo TOTP (), são o setor de abordagem 2fa recomendado. 2FA usar TOTP é preferencial para SMS 2FA. Um aplicativo autenticador fornece um código de 6 a 8 dígitos que os usuários devem inserir depois de confirmar seu nome de usuário e senha. Normalmente, um aplicativo autenticador é instalado em um smartphone.

Os modelos de aplicativo Web do ASP.NET Core dão suporte a autenticadores, mas não fornecem suporte para geração de QRCode. Os geradores de QRCode facilitam a configuração do 2FA. Este documento orientará você pela adição da geração de [código QR](https://wikipedia.org/wiki/QR_code) à página de configuração do 2FA.

A autenticação de dois fatores não acontece usando um provedor de autenticação externo, como o [Google](xref:security/authentication/google-logins) ou o [Facebook](xref:security/authentication/facebook-logins). Os logons externos são protegidos por qualquer mecanismo fornecido pelo provedor de logon externo. Considere, por exemplo, que o provedor de autenticação da [Microsoft](xref:security/authentication/microsoft-logins) requer uma chave de hardware ou outra abordagem de 2FA. Se os modelos padrão impuserem a 2FA "local", os usuários precisarão atender a duas abordagens de 2FA, que não é um cenário comumente usado.

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>Adicionando códigos QR à página de configuração do 2FA

Essas instruções usam o *qrcode. js* do repositório https://davidshimjs.github.io/qrcodejs/.

* Baixe a [biblioteca JavaScript qrcode. js](https://davidshimjs.github.io/qrcodejs/) para a pasta `wwwroot\lib` em seu projeto.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* Siga as instruções em [Scaffold Identity](xref:security/authentication/scaffold-identity) para gerar */areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.
* No */areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, localize a seção `Scripts` no final do arquivo:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Em *páginas/conta/gerenciar/EnableAuthenticator. cshtml* (Razor Pages) ou *views/Manage/EnableAuthenticator. cshtml* (MVC), localize a seção `Scripts` no final do arquivo:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* Atualize a seção `Scripts` para adicionar uma referência à biblioteca de `qrcodejs` que você adicionou e uma chamada para gerar o código QR. Ele deve ter a seguinte aparência:

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

* Exclua o parágrafo que vincula você a estas instruções.

Execute seu aplicativo e verifique se você pode digitalizar o código QR e validar o código que o autenticador comprova.

## <a name="change-the-site-name-in-the-qr-code"></a>Alterar o nome do site no código QR

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

O nome do site no código QR é extraído do nome do projeto que você escolher ao criar inicialmente seu projeto. Você pode alterá-lo procurando o método `GenerateQrCodeUri(string email, string unformattedKey)` no */areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

O nome do site no código QR é extraído do nome do projeto que você escolher ao criar inicialmente seu projeto. Você pode alterá-lo procurando o método `GenerateQrCodeUri(string email, string unformattedKey)` no arquivo *pages/Account/Manage/EnableAuthenticator. cshtml. cs* (Razor Pages) ou no arquivo *Controllers/ManageController. cs* (MVC).

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

O código padrão do modelo tem a seguinte aparência:

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

O segundo parâmetro na chamada para `string.Format` é o nome do seu site, extraído do nome da solução. Ele pode ser alterado para qualquer valor, mas deve sempre ser codificado por URL.

## <a name="using-a-different-qr-code-library"></a>Usando uma biblioteca de código QR diferente

Você pode substituir a biblioteca de códigos QR pela sua biblioteca preferida. O HTML contém um elemento `qrCode` no qual você pode posicionar um código QR por qualquer mecanismo que sua biblioteca fornece.

A URL formatada corretamente para o código QR está disponível no:

* `AuthenticatorUri` Propriedade do modelo.
* `data-url` Propriedade no elemento `qrCodeData`.

## <a name="totp-client-and-server-time-skew"></a>Distorção de tempo de cliente e servidor TOTP

A autenticação do TOTP (senha de uso único baseada em tempo) depende do servidor e do dispositivo autenticador ter um tempo preciso. Somente tokens por último 30 segundos. Se os logons TOTP 2FA estiverem falhando, verifique se o tempo do servidor é preciso e, preferencialmente, sincronizado com um serviço NTP preciso.

::: moniker-end
