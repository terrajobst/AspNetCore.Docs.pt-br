---
ms.openlocfilehash: b1f7c306533e70f84e73a020c74756b91cae7ea7
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665743"
---
# <a name="error-handling-sample-application"></a>Aplicativo de exemplo de tratamento de erros

Este exemplo demonstra os cenários descritos no tópico [Tratar erros no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/error-handling).

## <a name="configure-a-custom-exception-handling-page"></a>Configurar uma página de tratamento de exceção personalizada

Quando o aplicativo não estiver em execução no ambiente de desenvolvimento, o Middleware de tratamento de exceção:

* Captura exceções.
* Registra exceções em log.
* Executa novamente a solicitação em um pipeline alternativo no caminho fornecido.

## <a name="configure-custom-exception-handling-code"></a>Configurar código de tratamento de exceção personalizada

Uma alternativa para atender a um ponto de extremidade de erros com uma página de tratamento de exceção personalizada é fornecer um lambda para `UseExceptionHandler`. Usar um lambda com `UseExceptionHandler` permite acessar o erro antes de retornar a resposta.

O aplicativo de exemplo demonstra o código de tratamento de exceção personalizado em `Startup.Configure`. Siga as instruções na parte superior do arquivo *Startup.cs* (`LambdaErrorHandler`). Depois que o aplicativo for iniciado, dispare uma exceção com o link **Gerar Exceção** na página de índice.
