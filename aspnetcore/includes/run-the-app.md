# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="119f9-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="119f9-101">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="119f9-102">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="119f9-102">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="119f9-103">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="119f9-103">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="119f9-104">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="119f9-104">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="119f9-105">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="119f9-105">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="119f9-106">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="119f9-106">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="119f9-107">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="119f9-107">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="119f9-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="119f9-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="119f9-109">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="119f9-109">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="119f9-110">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="119f9-110">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="119f9-111">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="119f9-111">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="119f9-112">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="119f9-112">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="119f9-113">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="119f9-113">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="119f9-114">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="119f9-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="119f9-115">No Visual Studio, pressione **opt-cmd-Return** para executar sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="119f9-115">From Visual Studio, press **Opt-Cmd-Return** to run without the debugger.</span></span> <span data-ttu-id="119f9-116">Como alternativa, navegue até a barra de menus e vá para **executar > iniciar sem depuração**.</span><span class="sxs-lookup"><span data-stu-id="119f9-116">Alternatively, navigate to the menu bar and go to **Run>Start Without Debugging**.</span></span>

  <span data-ttu-id="119f9-117">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="119f9-117">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---