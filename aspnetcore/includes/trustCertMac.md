---
ms.openlocfilehash: 2ec079606cb48670dbc3852482fd8d401e7db44b
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59472296"
---
* Confie no certificado de desenvolvimento HTTPS executando o seguinte comando:

    ```console
    dotnet dev-certs https --trust
    ```

* O comando anterior exibe o resultado a seguir:

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* Insira o nome do usuário administrador e a senha, se solicitado.  O certificado será instalado e se tornará confiável.

    Para obter mais informações, veja [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).