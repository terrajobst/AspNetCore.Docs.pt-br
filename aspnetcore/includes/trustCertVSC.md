---
ms.openlocfilehash: 260f774fdba4d16a4fcb00ac1c699acf4d1bf5b5
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615322"
---
* Confie no certificado de desenvolvimento HTTPS executando o seguinte comando:

  ```console
  dotnet dev-certs https --trust
  ```

  O comando anterior exibe a caixa de diálogo a seguir:

  ![Caixa de diálogo de aviso de segurança](~/getting-started/_static/cert.png)

* Selecione **Sim** se você concordar com confiar no certificado de desenvolvimento.

  Para obter mais informações, veja [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).
  
