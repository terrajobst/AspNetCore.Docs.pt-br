---
ms.openlocfilehash: 6246247788eefc8f094ec1a7f156cb36715edb53
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58210100"
---
# <a name="how-to-buildrun-secure-user-data-sample"></a>Como exemplo de dados de usuário segura de build/execução

* Definir a senha com a ferramenta Secret Manager:

  `dotnet user-secrets set SeedUserPW <pw>`

* Atualize o banco de dados:

  `dotnet ef database update`

* Habilitar o HTTPS no projeto
