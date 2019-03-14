---
title: Trabalhar com o SQL em um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Saiba mais sobre como usar o LocalDB ou o SQLite do SQL Server em um aplicativo ASP.NET Core MVC.
ms.author: riande
ms.date: 03/07/2017
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: a6b2026a43bda94a84a05749e9ea395308dec231
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841430"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="67cfe-103">Trabalhar com o SQL no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67cfe-103">Work with SQL in ASP.NET Core</span></span>

<span data-ttu-id="67cfe-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="67cfe-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="67cfe-105">O objeto `MvcMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="67cfe-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="67cfe-106">O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` no arquivo *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="67cfe-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="67cfe-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67cfe-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="67cfe-108">O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="67cfe-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="67cfe-109">Para o desenvolvimento local, ele obtém a cadeia de conexão do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="67cfe-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="67cfe-110">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="67cfe-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="67cfe-111">O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="67cfe-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="67cfe-112">Para o desenvolvimento local, ele obtém a cadeia de conexão do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="67cfe-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---  
<!-- End of VS tabs -->

<span data-ttu-id="67cfe-113">Quando você implanta o aplicativo em um servidor de teste ou de produção, você pode usar uma variável de ambiente ou outra abordagem para definir a cadeia de conexão como um SQL Server real.</span><span class="sxs-lookup"><span data-stu-id="67cfe-113">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="67cfe-114">Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="67cfe-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="67cfe-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67cfe-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="67cfe-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="67cfe-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="67cfe-117">O LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express, que é direcionado para o desenvolvimento de programas.</span><span class="sxs-lookup"><span data-stu-id="67cfe-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="67cfe-118">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="67cfe-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="67cfe-119">Por padrão, o banco de dados LocalDB cria arquivos *.mdf* no diretório *C:/Users/{user}*.</span><span class="sxs-lookup"><span data-stu-id="67cfe-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="67cfe-120">No menu **Exibir**, abra **SSOX** (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="67cfe-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu de exibição](working-with-sql/_static/ssox.png)

* <span data-ttu-id="67cfe-122">Clique com o botão direito do mouse na tabela `Movie` **> Designer de Exibição**</span><span class="sxs-lookup"><span data-stu-id="67cfe-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menu contextual aberto na tabela Movie](working-with-sql/_static/design.png)

  ![Tabela Movie aberta no Designer](working-with-sql/_static/dv.png)

<span data-ttu-id="67cfe-125">Observe o ícone de chave ao lado de `ID`.</span><span class="sxs-lookup"><span data-stu-id="67cfe-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="67cfe-126">Por padrão, o EF tornará uma propriedade chamada `ID` a chave primária.</span><span class="sxs-lookup"><span data-stu-id="67cfe-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="67cfe-127">Clique com o botão direito do mouse na tabela `Movie` **> Dados de Exibição**</span><span class="sxs-lookup"><span data-stu-id="67cfe-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Menu contextual aberto na tabela Movie](working-with-sql/_static/ssox2.png)

  ![Tabela Movie aberta mostrando os dados da tabela](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="67cfe-130">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="67cfe-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---  
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="67cfe-131">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="67cfe-131">Seed the database</span></span>

<span data-ttu-id="67cfe-132">Crie uma nova classe chamada `SeedData` na pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="67cfe-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="67cfe-133">Substitua o código gerado pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="67cfe-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="67cfe-134">Se houver um filme no BD, o inicializador de semeadura será retornado e nenhum filme será adicionado.</span><span class="sxs-lookup"><span data-stu-id="67cfe-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a><span data-ttu-id="67cfe-135">Adicionar o inicializador de semeadura</span><span class="sxs-lookup"><span data-stu-id="67cfe-135">Add the seed initializer</span></span>

<span data-ttu-id="67cfe-136">Substitua o conteúdo de *Program.cs* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="67cfe-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="67cfe-137">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="67cfe-137">Test the app</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="67cfe-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67cfe-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="67cfe-139">Exclua todos os registros no BD.</span><span class="sxs-lookup"><span data-stu-id="67cfe-139">Delete all the records in the DB.</span></span> <span data-ttu-id="67cfe-140">Faça isso com os links Excluir no navegador ou no SSOX.</span><span class="sxs-lookup"><span data-stu-id="67cfe-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="67cfe-141">Force o aplicativo a ser inicializado (chame os métodos na classe `Startup`) para que o método de semeadura seja executado.</span><span class="sxs-lookup"><span data-stu-id="67cfe-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="67cfe-142">Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado.</span><span class="sxs-lookup"><span data-stu-id="67cfe-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="67cfe-143">Faça isso com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="67cfe-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="67cfe-144">Clique com botão direito do mouse no ícone de bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou **Parar Site**</span><span class="sxs-lookup"><span data-stu-id="67cfe-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Ícone de bandeja do sistema do IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="67cfe-147">Se você estiver executando o VS no modo sem depuração, pressione F5 para executar no modo de depuração</span><span class="sxs-lookup"><span data-stu-id="67cfe-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="67cfe-148">Se você estiver executando o VS no modo de depuração, pare o depurador e pressione F5</span><span class="sxs-lookup"><span data-stu-id="67cfe-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="67cfe-149">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="67cfe-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="67cfe-150">Exclua todos os registros no BD (para que o método de semeadura seja executado).</span><span class="sxs-lookup"><span data-stu-id="67cfe-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="67cfe-151">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="67cfe-151">Stop and start the app to seed the database.</span></span>

---  
<!-- End of VS tabs -->

<span data-ttu-id="67cfe-152">O aplicativo mostra os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="67cfe-152">The app shows the seeded data.</span></span>

![Aplicativo de filme MVC aberto no Microsoft Edge, mostrando os dados do filme](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="67cfe-154">[Anterior](adding-model.md)
> [Próximo](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="67cfe-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>  
