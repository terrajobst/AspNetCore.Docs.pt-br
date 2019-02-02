---
title: Usar o modelo de projeto do React com o ASP.NET Core
author: SteveSandersonMS
description: Saiba como começar a trabalhar com o modelo de projeto do SPA (Aplicativo de Página Única) do ASP.NET Core para React e criar-aplicativo-do-React.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/21/2018
uid: spa/react
ms.openlocfilehash: d83bff8abcd5b59d8bc4a51a101510755394f0c4
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667681"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="d3938-103">Usar o modelo de projeto do React com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3938-103">Use the React project template with ASP.NET Core</span></span>

::: moniker range="= aspnetcore-2.0"

> [!NOTE]
> <span data-ttu-id="d3938-104">Esta documentação não é sobre o modelo de projeto do React incluído no ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="d3938-104">This documentation isn't about the React project template included in ASP.NET Core 2.0.</span></span> <span data-ttu-id="d3938-105">Ela é sobre o modelo React mais recente, para o qual você pode atualizar manualmente.</span><span class="sxs-lookup"><span data-stu-id="d3938-105">It's about the newer React template to which you can update manually.</span></span> <span data-ttu-id="d3938-106">O modelo está incluído no ASP.NET Core 2.1 por padrão.</span><span class="sxs-lookup"><span data-stu-id="d3938-106">The template is included in ASP.NET Core 2.1 by default.</span></span>

::: moniker-end

<span data-ttu-id="d3938-107">O modelo de projeto do React atualizado fornece um ponto inicial conveniente para aplicativos do ASP.NET Core usando convenções do React e de CRA [(criar-aplicativo-do-React)](https://github.com/facebookincubator/create-react-app) para implementar uma IU (interface do usuário) avançada do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="d3938-107">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="d3938-108">O modelo é equivalente à criação de dois projetos: um projeto do ASP.NET Core, para atuar como um back-end de API, e um projeto do React CRA padrão, para atuar como uma interface do usuário, mas com a praticidade de hospedar ambos em um único projeto de aplicativo que pode ser criado e publicado como uma única unidade.</span><span class="sxs-lookup"><span data-stu-id="d3938-108">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="d3938-109">Criar um novo aplicativo</span><span class="sxs-lookup"><span data-stu-id="d3938-109">Create a new app</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="d3938-110">Se usar o ASP.NET Core 2.0, verifique se você [instalou o modelo de projeto do React atualizado](xref:spa/index#installation).</span><span class="sxs-lookup"><span data-stu-id="d3938-110">If using ASP.NET Core 2.0, ensure you've [installed the updated React project template](xref:spa/index#installation).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="d3938-111">Se você tiver o ASP.NET Core 2.1 instalado, não será necessário instalar o modelo de projeto React.</span><span class="sxs-lookup"><span data-stu-id="d3938-111">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

::: moniker-end

<span data-ttu-id="d3938-112">Crie um novo projeto de um prompt de comando usando o comando `dotnet new react` em um diretório vazio.</span><span class="sxs-lookup"><span data-stu-id="d3938-112">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="d3938-113">Por exemplo, os seguintes comandos criam o aplicativo em um diretório *my-new-app* e mudam para esse diretório:</span><span class="sxs-lookup"><span data-stu-id="d3938-113">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```console
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="d3938-114">Execute o aplicativo do Visual Studio ou da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="d3938-114">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d3938-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3938-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d3938-116">Abra o arquivo *.csproj* gerado e execute o aplicativo normalmente de lá.</span><span class="sxs-lookup"><span data-stu-id="d3938-116">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="d3938-117">O processo de build restaura dependências npm na primeira execução, o que pode levar vários minutos.</span><span class="sxs-lookup"><span data-stu-id="d3938-117">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="d3938-118">Builds subsequentes são muito mais rápidos.</span><span class="sxs-lookup"><span data-stu-id="d3938-118">Subsequent builds are much faster.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d3938-119">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d3938-119">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d3938-120">Verifique se você tem uma variável de ambiente chamada `ASPNETCORE_Environment` com valor `Development`.</span><span class="sxs-lookup"><span data-stu-id="d3938-120">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="d3938-121">No Windows (em prompts que não são do PowerShell), execute `SET ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="d3938-121">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="d3938-122">No Linux ou macOS, execute `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="d3938-122">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="d3938-123">Execute [dotnet build](/dotnet/core/tools/dotnet-build) para verificar se seu aplicativo compila corretamente.</span><span class="sxs-lookup"><span data-stu-id="d3938-123">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="d3938-124">O processo de build restaura dependências npm na primeira execução, o que pode levar vários minutos.</span><span class="sxs-lookup"><span data-stu-id="d3938-124">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="d3938-125">Builds subsequentes são muito mais rápidos.</span><span class="sxs-lookup"><span data-stu-id="d3938-125">Subsequent builds are much faster.</span></span>

<span data-ttu-id="d3938-126">Execute [dotnet run](/dotnet/core/tools/dotnet-run) para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3938-126">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="d3938-127">O modelo de projeto cria um aplicativo ASP.NET Core e um aplicativo do React.</span><span class="sxs-lookup"><span data-stu-id="d3938-127">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="d3938-128">O aplicativo ASP.NET Core destina-se a ser usado para acesso a dados, autorização e outras questões do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="d3938-128">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="d3938-129">O aplicativo do React, que reside no subdiretório *ClientApp*, destina-se a ser usado para todas as questões de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="d3938-129">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="d3938-130">Adicione páginas, imagens, estilos, módulos, etc.</span><span class="sxs-lookup"><span data-stu-id="d3938-130">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="d3938-131">O diretório *ClientApp* é um aplicativo do React CRA padrão.</span><span class="sxs-lookup"><span data-stu-id="d3938-131">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="d3938-132">Veja a [documentação oficial do CRA](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d3938-132">See the official [CRA documentation](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) for more information.</span></span>

<span data-ttu-id="d3938-133">Há pequenas diferenças entre o aplicativo do React criado por este modelo e um criado pelo CRA propriamente dito; no entanto, os recursos do aplicativo são inalterados.</span><span class="sxs-lookup"><span data-stu-id="d3938-133">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="d3938-134">O aplicativo criado pelo modelo contém um layout com base em [Bootstrap](https://getbootstrap.com/) e um exemplo básico de roteamento.</span><span class="sxs-lookup"><span data-stu-id="d3938-134">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="d3938-135">Instalar pacotes npm</span><span class="sxs-lookup"><span data-stu-id="d3938-135">Install npm packages</span></span>

<span data-ttu-id="d3938-136">Para instalar pacotes npm de terceiros, use um prompt de comando no subdiretório *ClientApp*.</span><span class="sxs-lookup"><span data-stu-id="d3938-136">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="d3938-137">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d3938-137">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="d3938-138">Publicar e implantar</span><span class="sxs-lookup"><span data-stu-id="d3938-138">Publish and deploy</span></span>

<span data-ttu-id="d3938-139">No desenvolvimento, o aplicativo é executado de um modo otimizado para conveniência do desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="d3938-139">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="d3938-140">Por exemplo, pacotes JavaScript incluem mapas de origem (de modo que durante a depuração, você pode ver o código-fonte original).</span><span class="sxs-lookup"><span data-stu-id="d3938-140">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="d3938-141">O aplicativo observa alterações em arquivos JavaScript, HTML e CSS no disco e recompila e recarrega automaticamente quando as detecta.</span><span class="sxs-lookup"><span data-stu-id="d3938-141">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="d3938-142">Em produção, atende a uma versão de seu aplicativo que é otimizada para desempenho.</span><span class="sxs-lookup"><span data-stu-id="d3938-142">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="d3938-143">Isso é configurado para ocorrer automaticamente.</span><span class="sxs-lookup"><span data-stu-id="d3938-143">This is configured to happen automatically.</span></span> <span data-ttu-id="d3938-144">Quando você publica, a configuração de build emite um build minificado e transpilado do seu código do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="d3938-144">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="d3938-145">Diferentemente do build de desenvolvimento, o build de produção não requer que o Node.js esteja instalado no servidor.</span><span class="sxs-lookup"><span data-stu-id="d3938-145">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="d3938-146">Você pode usar os [métodos padrão de implantação e hospedagem do ASP.NET Core](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="d3938-146">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="d3938-147">Executar o servidor CRA independentemente</span><span class="sxs-lookup"><span data-stu-id="d3938-147">Run the CRA server independently</span></span>

<span data-ttu-id="d3938-148">O projeto está configurado para iniciar sua própria instância do Development Server do CRA em segundo plano quando o aplicativo ASP.NET Core é iniciado no modo de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="d3938-148">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="d3938-149">Isso é conveniente, porque significa que você não precisa executar um servidor separado manualmente.</span><span class="sxs-lookup"><span data-stu-id="d3938-149">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="d3938-150">Há uma desvantagem nessa configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="d3938-150">There's a drawback to this default setup.</span></span> <span data-ttu-id="d3938-151">Cada vez que você modificar seu código C# e o aplicativo ASP.NET Core precisar ser reiniciado, o servidor CRA será reiniciado também.</span><span class="sxs-lookup"><span data-stu-id="d3938-151">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="d3938-152">São necessários alguns segundos para iniciar um backup.</span><span class="sxs-lookup"><span data-stu-id="d3938-152">A few seconds are required to start back up.</span></span> <span data-ttu-id="d3938-153">Se você estiver fazendo edições frequentes de código C# e não quiser esperar o servidor CRA reiniciar, execute o servidor CRA externamente, independentemente do processo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3938-153">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="d3938-154">Para fazer isso:</span><span class="sxs-lookup"><span data-stu-id="d3938-154">To do so:</span></span>

1. <span data-ttu-id="d3938-155">Adicionar um *. env* do arquivo para o *ClientApp* subdiretório com a seguinte configuração:</span><span class="sxs-lookup"><span data-stu-id="d3938-155">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```
    
    <span data-ttu-id="d3938-156">Isso impedirá o navegador da web seja aberto ao iniciar o servidor CRA externamente.</span><span class="sxs-lookup"><span data-stu-id="d3938-156">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="d3938-157">Em um prompt de comando, vá para o subdiretório *ClientApp* e inicie o Development Server do CRA:</span><span class="sxs-lookup"><span data-stu-id="d3938-157">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="d3938-158">Modifique o aplicativo ASP.NET Core para, em vez de iniciar uma instância do servidor CRA própria, usar a externa.</span><span class="sxs-lookup"><span data-stu-id="d3938-158">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="d3938-159">Na classe *Startup*, substitua a invocação `spa.UseReactDevelopmentServer` pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="d3938-159">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="d3938-160">Quando você iniciar seu aplicativo ASP.NET Core, ele não inicializará um servidor CRA.</span><span class="sxs-lookup"><span data-stu-id="d3938-160">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="d3938-161">Em vez disso, a instância que você iniciou manualmente é usada.</span><span class="sxs-lookup"><span data-stu-id="d3938-161">The instance you started manually is used instead.</span></span> <span data-ttu-id="d3938-162">Isso permite a ele iniciar e reiniciar mais rapidamente.</span><span class="sxs-lookup"><span data-stu-id="d3938-162">This enables it to start and restart faster.</span></span> <span data-ttu-id="d3938-163">Ele não aguarda mais que o aplicativo do React seja recompilado a cada vez.</span><span class="sxs-lookup"><span data-stu-id="d3938-163">It's no longer waiting for your React app to rebuild each time.</span></span>
