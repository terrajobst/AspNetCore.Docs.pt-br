---
ms.openlocfilehash: a9bdff481b1a72a9ee19f4e51fada177530c0cbb
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59472308"
---
*  Confie no certificado de desenvolvimento HTTPS executando o seguinte comando:

    ```console
    dotnet dev-certs https --trust
    ```

    O comando anterior exibe a caixa de diálogo a seguir:

    ![Caixa de diálogo de aviso de segurança](~/getting-started/_static/cert.png)

*    Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.

     Para obter mais informações, veja [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).