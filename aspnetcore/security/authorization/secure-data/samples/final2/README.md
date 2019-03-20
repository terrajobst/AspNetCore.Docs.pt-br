---
ms.openlocfilehash: 6246247788eefc8f094ec1a7f156cb36715edb53
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58210100"
---
# <a name="how-to-buildrun-secure-user-data-sample"></a><span data-ttu-id="8cf03-101">Como exemplo de dados de usuário segura de build/execução</span><span class="sxs-lookup"><span data-stu-id="8cf03-101">How to build/run Secure user data sample</span></span>

* <span data-ttu-id="8cf03-102">Definir a senha com a ferramenta Secret Manager:</span><span class="sxs-lookup"><span data-stu-id="8cf03-102">Set password with the Secret Manager tool:</span></span>

  `dotnet user-secrets set SeedUserPW <pw>`

* <span data-ttu-id="8cf03-103">Atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="8cf03-103">Update the database:</span></span>

  `dotnet ef database update`

* <span data-ttu-id="8cf03-104">Habilitar o HTTPS no projeto</span><span class="sxs-lookup"><span data-stu-id="8cf03-104">Enable HTTPS in the project</span></span>
