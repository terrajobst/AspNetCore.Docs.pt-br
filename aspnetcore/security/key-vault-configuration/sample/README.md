---
ms.openlocfilehash: 209b5c41e17897693962954b1e795bdbb41f9384
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59012728"
---
# <a name="key-vault-configuration-provider-sample-app"></a>Aplicativo de exemplo de provedor de configuração do Cofre de chaves

Este exemplo ilustra o uso do provedor de configuração da chave cofre do Azure.

O exemplo é executado em um dos dois modos, determinados pelo `#define` instrução na parte superior dos *Program.cs* arquivo. Para obter instruções, consulte [diretivas de pré-processador no código de exemplo](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):

* `Certificate` &ndash; Demonstra o uso de um certificado x. 509 e de ID do cliente do Azure Key Vault para os segredos de acesso armazenada no cofre de chaves do Azure. Esta versão do exemplo pode ser executado em qualquer local, implantado para o serviço de aplicativo do Azure ou qualquer host capaz de atender a um aplicativo ASP.NET Core.
* `Managed` &ndash; Demonstra como usar o do Azure [Managed Service Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo ao Azure Key Vault com autenticação do Azure AD sem credenciais no código ou a configuração do aplicativo. Uma ID de cliente do Azure AD e o segredo não são necessários para o aplicativo autenticar com o Azure Key Vault. Este exemplo deve ser implantado no serviço de aplicativo do Azure para explorar o scearnio identidade gerenciada.

Para obter mais informações, consulte [provedor de configuração do Azure Key Vault](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration).
