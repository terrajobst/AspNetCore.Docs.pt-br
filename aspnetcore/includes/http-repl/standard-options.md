* `-F|--no-formatting`

  <span data-ttu-id="0097f-101">Um sinalizador cuja presença suprime a formatação da resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="0097f-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="0097f-102">Define um cabeçalho para a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="0097f-102">Sets an HTTP request header.</span></span> <span data-ttu-id="0097f-103">Os dois formatos de valor a seguir são compatíveis:</span><span class="sxs-lookup"><span data-stu-id="0097f-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  <span data-ttu-id="0097f-104">Especifica um arquivo em que toda a resposta HTTP (incluindo cabeçalhos e corpo) deve ser gravada.</span><span class="sxs-lookup"><span data-stu-id="0097f-104">Specifies a file to which the entire HTTP response (including headers and body) should be written.</span></span> <span data-ttu-id="0097f-105">Por exemplo, `--response "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="0097f-105">For example, `--response "C:\response.txt"`.</span></span> <span data-ttu-id="0097f-106">Se ainda não existir, o arquivo será criado.</span><span class="sxs-lookup"><span data-stu-id="0097f-106">The file is created if it doesn't exist.</span></span>

* `--response:body`

  <span data-ttu-id="0097f-107">Especifica um arquivo em que o corpo da resposta HTTP deve ser gravado.</span><span class="sxs-lookup"><span data-stu-id="0097f-107">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="0097f-108">Por exemplo, `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="0097f-108">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="0097f-109">Se ainda não existir, o arquivo será criado.</span><span class="sxs-lookup"><span data-stu-id="0097f-109">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="0097f-110">Especifica um arquivo em que os cabeçalhos da resposta HTTP devem ser gravados.</span><span class="sxs-lookup"><span data-stu-id="0097f-110">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="0097f-111">Por exemplo, `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="0097f-111">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="0097f-112">Se ainda não existir, o arquivo será criado.</span><span class="sxs-lookup"><span data-stu-id="0097f-112">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="0097f-113">Um sinalizador cuja presença habilita o streaming da resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="0097f-113">A flag whose presence enables streaming of the HTTP response.</span></span>
