# <a name="aspnet-core-url-rewriting-sample"></a><span data-ttu-id="112c6-101">Amostra de Reconfiguração de URL do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="112c6-101">ASP.NET Core URL Rewriting Sample</span></span>

<span data-ttu-id="112c6-102">Esta amostra ilustra o uso do Middleware de Reconfiguração de URL do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="112c6-102">This sample illustrates usage of ASP.NET Core URL Rewriting Middleware.</span></span> <span data-ttu-id="112c6-103">O aplicativo demonstra as opções de redirecionamento e de reconfiguração de URL.</span><span class="sxs-lookup"><span data-stu-id="112c6-103">The app demonstrates URL redirect and URL rewriting options.</span></span>

<span data-ttu-id="112c6-104">Durante a execução da amostra, as respostas que não são de arquivo retornam a URL reconfigurada ou redirecionada quando uma das regras é aplicada a uma URL de solicitação.</span><span class="sxs-lookup"><span data-stu-id="112c6-104">When running the sample, non-file responses return the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span> <span data-ttu-id="112c6-105">Para os exemplos de XML e arquivo de texto, o Middleware de Arquivo Estático fornece o arquivo depois que a URL de solicitação é reconfigurada pelo middleware.</span><span class="sxs-lookup"><span data-stu-id="112c6-105">For the XML and text file examples, Static File Middleware serves the file after the request URL is rewritten by the middleware.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="112c6-106">Exemplos desta amostra</span><span class="sxs-lookup"><span data-stu-id="112c6-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - <span data-ttu-id="112c6-107">Código de status de êxito: 302 (Encontrado)</span><span class="sxs-lookup"><span data-stu-id="112c6-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="112c6-108">Exemplo (redirecionamento): **/redirect-rule/{capture_group}** para **/redirected/{capture_group}**</span><span class="sxs-lookup"><span data-stu-id="112c6-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="112c6-109">Código de status de êxito: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="112c6-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="112c6-110">Exemplo (reconfiguração): **/rewrite-rule/{capture_group_1}/{capture_group_2}** para **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span><span class="sxs-lookup"><span data-stu-id="112c6-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="112c6-111">Código de status de êxito: 302 (Encontrado)</span><span class="sxs-lookup"><span data-stu-id="112c6-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="112c6-112">Exemplo (redirecionamento): **/apache-mod-rules-redirect/{capture_group}** para **/redirected?id={capture_group}**</span><span class="sxs-lookup"><span data-stu-id="112c6-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="112c6-113">Código de status de êxito: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="112c6-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="112c6-114">Exemplo (reconfiguração): **/iis-rules-rewrite/{capture_group}** para **/rewritten?id={capture_group}**</span><span class="sxs-lookup"><span data-stu-id="112c6-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXmlFileRequests)`
  - <span data-ttu-id="112c6-115">Código de status de êxito: 301 (Movido Permanentemente)</span><span class="sxs-lookup"><span data-stu-id="112c6-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="112c6-116">Exemplo (redirecionamento): **/file.xml** para **/xmlfiles/file.xml**</span><span class="sxs-lookup"><span data-stu-id="112c6-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(RewriteTextFileRequests)`
  - <span data-ttu-id="112c6-117">Código de status de êxito: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="112c6-117">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="112c6-118">Exemplo (reconfiguração): **/some_file.txt** para **/file.txt**</span><span class="sxs-lookup"><span data-stu-id="112c6-118">Example (rewrite): **/some_file.txt** to **/file.txt**</span></span>
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="112c6-119">Código de status de êxito: 301 (Movido Permanentemente)</span><span class="sxs-lookup"><span data-stu-id="112c6-119">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="112c6-120">Exemplo (redirecionamento): **/image.png** para **/png-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="112c6-120">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="112c6-121">Exemplo (redirecionamento): **/image.jpg** para **/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="112c6-121">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="use-a-physicalfileprovider"></a><span data-ttu-id="112c6-122">Usar um PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="112c6-122">Use a PhysicalFileProvider</span></span>

<span data-ttu-id="112c6-123">Também obtenha um `IFileProvider` criando um `PhysicalFileProvider` para passar para os métodos `AddApacheModRewrite()` e `AddIISUrlRewrite()`:</span><span class="sxs-lookup"><span data-stu-id="112c6-123">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a><span data-ttu-id="112c6-124">Proteger extensões de redirecionamento</span><span class="sxs-lookup"><span data-stu-id="112c6-124">Secure redirection extensions</span></span>

<span data-ttu-id="112c6-125">Esta amostra inclui a configuração `WebHostBuilder` para o aplicativo usar URLs (`https://localhost:5001`, `https://localhost`) e um certificado de teste (*testCert.pfx*) para ajudá-lo a explorar os métodos de redirecionamento seguros.</span><span class="sxs-lookup"><span data-stu-id="112c6-125">This sample includes `WebHostBuilder` configuration for the app to use URLs (`https://localhost:5001`, `https://localhost`) and a test certificate (*testCert.pfx*) to assist in exploring the secure redirect methods.</span></span> <span data-ttu-id="112c6-126">Se o servidor já tiver a porta 443 atribuída ou em uso, o exemplo `https://localhost` não funcionará, remova o `ListenOptions` para a porta 443 no método `CreateWebHostBuilder` do arquivo *Program.cs* ou desassocie a porta 443 no servidor para que o Kestrel possa usar a porta.</span><span class="sxs-lookup"><span data-stu-id="112c6-126">If the server already has port 443 assigned or in use, the `https://localhost` example doesn't work&mdash;remove the `ListenOptions` for port 443 in the `CreateWebHostBuilder` method of the *Program.cs* file or unbind port 443 on the server so that Kestrel can use the port.</span></span>

| <span data-ttu-id="112c6-127">Método</span><span class="sxs-lookup"><span data-stu-id="112c6-127">Method</span></span>                           | <span data-ttu-id="112c6-128">Código de status</span><span class="sxs-lookup"><span data-stu-id="112c6-128">Status Code</span></span> |    <span data-ttu-id="112c6-129">Porta</span><span class="sxs-lookup"><span data-stu-id="112c6-129">Port</span></span>    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     <span data-ttu-id="112c6-130">301</span><span class="sxs-lookup"><span data-stu-id="112c6-130">301</span></span>     | <span data-ttu-id="112c6-131">nulo (465)</span><span class="sxs-lookup"><span data-stu-id="112c6-131">null (465)</span></span> |
| `.AddRedirectToHttps()`          |     <span data-ttu-id="112c6-132">302</span><span class="sxs-lookup"><span data-stu-id="112c6-132">302</span></span>     | <span data-ttu-id="112c6-133">nulo (465)</span><span class="sxs-lookup"><span data-stu-id="112c6-133">null (465)</span></span> |
| `.AddRedirectToHttps(301)`       |     <span data-ttu-id="112c6-134">301</span><span class="sxs-lookup"><span data-stu-id="112c6-134">301</span></span>     | <span data-ttu-id="112c6-135">nulo (465)</span><span class="sxs-lookup"><span data-stu-id="112c6-135">null (465)</span></span> |
| `.AddRedirectToHttps(301, 5001)` |     <span data-ttu-id="112c6-136">301</span><span class="sxs-lookup"><span data-stu-id="112c6-136">301</span></span>     |    <span data-ttu-id="112c6-137">5001</span><span class="sxs-lookup"><span data-stu-id="112c6-137">5001</span></span>    |
