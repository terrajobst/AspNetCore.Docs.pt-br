---
ms.openlocfilehash: b1f7c306533e70f84e73a020c74756b91cae7ea7
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665743"
---
# <a name="error-handling-sample-application"></a><span data-ttu-id="e1621-101">Aplicativo de exemplo de tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="e1621-101">Error Handling Sample Application</span></span>

<span data-ttu-id="e1621-102">Este exemplo demonstra os cenários descritos no tópico [Tratar erros no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/error-handling).</span><span class="sxs-lookup"><span data-stu-id="e1621-102">This sample app demonstrates the scenarios described in the [Handle errors in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/error-handling) topic.</span></span>

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="e1621-103">Configurar uma página de tratamento de exceção personalizada</span><span class="sxs-lookup"><span data-stu-id="e1621-103">Configure a custom exception handling page</span></span>

<span data-ttu-id="e1621-104">Quando o aplicativo não estiver em execução no ambiente de desenvolvimento, o Middleware de tratamento de exceção:</span><span class="sxs-lookup"><span data-stu-id="e1621-104">When the app isn't running in the Development environment, Exception Handling Middleware:</span></span>

* <span data-ttu-id="e1621-105">Captura exceções.</span><span class="sxs-lookup"><span data-stu-id="e1621-105">Catches exceptions.</span></span>
* <span data-ttu-id="e1621-106">Registra exceções em log.</span><span class="sxs-lookup"><span data-stu-id="e1621-106">Logs exceptions.</span></span>
* <span data-ttu-id="e1621-107">Executa novamente a solicitação em um pipeline alternativo no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="e1621-107">Re-executes the request in an alternate pipeline at the path provided.</span></span>

## <a name="configure-custom-exception-handling-code"></a><span data-ttu-id="e1621-108">Configurar código de tratamento de exceção personalizada</span><span class="sxs-lookup"><span data-stu-id="e1621-108">Configure custom exception handling code</span></span>

<span data-ttu-id="e1621-109">Uma alternativa para atender a um ponto de extremidade de erros com uma página de tratamento de exceção personalizada é fornecer um lambda para `UseExceptionHandler`.</span><span class="sxs-lookup"><span data-stu-id="e1621-109">An alternative to serving an endpoint for errors with a custom exception handling page is to provide a lambda to `UseExceptionHandler`.</span></span> <span data-ttu-id="e1621-110">Usar um lambda com `UseExceptionHandler` permite acessar o erro antes de retornar a resposta.</span><span class="sxs-lookup"><span data-stu-id="e1621-110">Using a lambda with `UseExceptionHandler` allows access to the error before returning the response.</span></span>

<span data-ttu-id="e1621-111">O aplicativo de exemplo demonstra o código de tratamento de exceção personalizado em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="e1621-111">The sample app demonstrates custom exception handling code in `Startup.Configure`.</span></span> <span data-ttu-id="e1621-112">Siga as instruções na parte superior do arquivo *Startup.cs* (`LambdaErrorHandler`).</span><span class="sxs-lookup"><span data-stu-id="e1621-112">Follow the instructions at the top of the *Startup.cs* file (`LambdaErrorHandler`).</span></span> <span data-ttu-id="e1621-113">Depois que o aplicativo for iniciado, dispare uma exceção com o link **Gerar Exceção** na página de índice.</span><span class="sxs-lookup"><span data-stu-id="e1621-113">After the app starts, trigger an exception with the **Throw Exception** link on the Index page.</span></span>
