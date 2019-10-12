---
title: Trabalhar com o SQL em um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Saiba mais sobre como usar o LocalDB ou o SQLite do SQL Server em um aplicativo ASP.NET Core MVC.
ms.author: riande
ms.date: 8/16/2019
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: de392f4220cf0182d02a20f387164d2f4b184b58
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2019
ms.locfileid: "72289087"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="f70a4-103">Trabalhar com o SQL no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f70a4-103">Work with SQL in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f70a4-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f70a4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f70a4-105">O objeto `MvcMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f70a4-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="f70a4-106">O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` no arquivo *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f70a4-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f70a4-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f70a4-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="f70a4-108">O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="f70a4-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="f70a4-109">Para o desenvolvimento local, ele obtém a cadeia de conexão do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f70a4-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f70a4-110">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f70a4-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="f70a4-111">O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="f70a4-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="f70a4-112">Para o desenvolvimento local, ele obtém a cadeia de conexão do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f70a4-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="f70a4-113">Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão como um SQL Server de produção.</span><span class="sxs-lookup"><span data-stu-id="f70a4-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="f70a4-114">Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="f70a4-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f70a4-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f70a4-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="f70a4-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="f70a4-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="f70a4-117">O LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express, que é direcionado para o desenvolvimento de programas.</span><span class="sxs-lookup"><span data-stu-id="f70a4-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="f70a4-118">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="f70a4-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="f70a4-119">Por padrão, o banco de dados LocalDB cria arquivos *.mdf* no diretório *C:/Users/{user}* .</span><span class="sxs-lookup"><span data-stu-id="f70a4-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="f70a4-120">No menu **Exibir**, abra **SSOX** (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="f70a4-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu de exibição](working-with-sql/_static/ssox.png)

* <span data-ttu-id="f70a4-122">Clique com o botão direito do mouse na tabela `Movie` **> Designer de Exibição**</span><span class="sxs-lookup"><span data-stu-id="f70a4-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menu contextual aberto na tabela Movie](working-with-sql/_static/design.png)

  ![Tabela Movie aberta no Designer](working-with-sql/_static/dv.png)

<span data-ttu-id="f70a4-125">Observe o ícone de chave ao lado de `ID`.</span><span class="sxs-lookup"><span data-stu-id="f70a4-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="f70a4-126">Por padrão, o EF tornará uma propriedade chamada `ID` a chave primária.</span><span class="sxs-lookup"><span data-stu-id="f70a4-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="f70a4-127">Clique com o botão direito do mouse na tabela `Movie` **> Dados de Exibição**</span><span class="sxs-lookup"><span data-stu-id="f70a4-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Menu contextual aberto na tabela Movie](working-with-sql/_static/ssox2.png)

  ![Tabela Movie aberta mostrando os dados da tabela](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f70a4-130">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f70a4-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="f70a4-131">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="f70a4-131">Seed the database</span></span>

<span data-ttu-id="f70a4-132">Crie uma nova classe chamada `SeedData` na pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="f70a4-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="f70a4-133">Substitua o código gerado pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="f70a4-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="f70a4-134">Se houver um filme no BD, o inicializador de semeadura será retornado e nenhum filme será adicionado.</span><span class="sxs-lookup"><span data-stu-id="f70a4-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="f70a4-135">Adicionar o inicializador de semeadura</span><span class="sxs-lookup"><span data-stu-id="f70a4-135">Add the seed initializer</span></span>

<span data-ttu-id="f70a4-136">Substitua o conteúdo de *Program.cs* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f70a4-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="f70a4-137">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="f70a4-137">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f70a4-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f70a4-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f70a4-139">Exclua todos os registros no BD.</span><span class="sxs-lookup"><span data-stu-id="f70a4-139">Delete all the records in the DB.</span></span> <span data-ttu-id="f70a4-140">Faça isso com os links Excluir no navegador ou no SSOX.</span><span class="sxs-lookup"><span data-stu-id="f70a4-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="f70a4-141">Force o aplicativo a ser inicializado (chame os métodos na classe `Startup`) para que o método de semeadura seja executado.</span><span class="sxs-lookup"><span data-stu-id="f70a4-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="f70a4-142">Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado.</span><span class="sxs-lookup"><span data-stu-id="f70a4-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="f70a4-143">Faça isso com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="f70a4-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="f70a4-144">Clique com botão direito do mouse no ícone de bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou **Parar Site**</span><span class="sxs-lookup"><span data-stu-id="f70a4-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Ícone de bandeja do sistema do IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="f70a4-147">Se você estiver executando o VS no modo sem depuração, pressione F5 para executar no modo de depuração</span><span class="sxs-lookup"><span data-stu-id="f70a4-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="f70a4-148">Se você estiver executando o VS no modo de depuração, pare o depurador e pressione F5</span><span class="sxs-lookup"><span data-stu-id="f70a4-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f70a4-149">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f70a4-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f70a4-150">Exclua todos os registros no BD (para que o método de semeadura seja executado).</span><span class="sxs-lookup"><span data-stu-id="f70a4-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="f70a4-151">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f70a4-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="f70a4-152">O aplicativo mostra os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="f70a4-152">The app shows the seeded data.</span></span>

![Aplicativo de filme MVC aberto no Microsoft Edge, mostrando os dados do filme](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="f70a4-154">[Anterior](adding-model.md)
> [Próximo](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="f70a4-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f70a4-155">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f70a4-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f70a4-156">O objeto `MvcMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f70a4-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="f70a4-157">O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` no arquivo *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f70a4-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f70a4-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f70a4-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="f70a4-159">O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="f70a4-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="f70a4-160">Para o desenvolvimento local, ele obtém a cadeia de conexão do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f70a4-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f70a4-161">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f70a4-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="f70a4-162">O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="f70a4-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="f70a4-163">Para o desenvolvimento local, ele obtém a cadeia de conexão do arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f70a4-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="f70a4-164">Quando você implanta o aplicativo em um servidor de teste ou de produção, você pode usar uma variável de ambiente ou outra abordagem para definir a cadeia de conexão como um SQL Server real.</span><span class="sxs-lookup"><span data-stu-id="f70a4-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="f70a4-165">Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="f70a4-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f70a4-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f70a4-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="f70a4-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="f70a4-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="f70a4-168">O LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express, que é direcionado para o desenvolvimento de programas.</span><span class="sxs-lookup"><span data-stu-id="f70a4-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="f70a4-169">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="f70a4-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="f70a4-170">Por padrão, o banco de dados LocalDB cria arquivos *.mdf* no diretório *C:/Users/{user}* .</span><span class="sxs-lookup"><span data-stu-id="f70a4-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="f70a4-171">No menu **Exibir**, abra **SSOX** (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="f70a4-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu de exibição](working-with-sql/_static/ssox.png)

* <span data-ttu-id="f70a4-173">Clique com o botão direito do mouse na tabela `Movie` **> Designer de Exibição**</span><span class="sxs-lookup"><span data-stu-id="f70a4-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menu contextual aberto na tabela Movie](working-with-sql/_static/design.png)

  ![Tabela Movie aberta no Designer](working-with-sql/_static/dv.png)

<span data-ttu-id="f70a4-176">Observe o ícone de chave ao lado de `ID`.</span><span class="sxs-lookup"><span data-stu-id="f70a4-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="f70a4-177">Por padrão, o EF tornará uma propriedade chamada `ID` a chave primária.</span><span class="sxs-lookup"><span data-stu-id="f70a4-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="f70a4-178">Clique com o botão direito do mouse na tabela `Movie` **> Dados de Exibição**</span><span class="sxs-lookup"><span data-stu-id="f70a4-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Menu contextual aberto na tabela Movie](working-with-sql/_static/ssox2.png)

  ![Tabela Movie aberta mostrando os dados da tabela](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f70a4-181">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f70a4-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="f70a4-182">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="f70a4-182">Seed the database</span></span>

<span data-ttu-id="f70a4-183">Crie uma nova classe chamada `SeedData` na pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="f70a4-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="f70a4-184">Substitua o código gerado pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="f70a4-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="f70a4-185">Se houver um filme no BD, o inicializador de semeadura será retornado e nenhum filme será adicionado.</span><span class="sxs-lookup"><span data-stu-id="f70a4-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="f70a4-186">Adicionar o inicializador de semeadura</span><span class="sxs-lookup"><span data-stu-id="f70a4-186">Add the seed initializer</span></span>

<span data-ttu-id="f70a4-187">Substitua o conteúdo de *Program.cs* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f70a4-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="f70a4-188">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="f70a4-188">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f70a4-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f70a4-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f70a4-190">Exclua todos os registros no BD.</span><span class="sxs-lookup"><span data-stu-id="f70a4-190">Delete all the records in the DB.</span></span> <span data-ttu-id="f70a4-191">Faça isso com os links Excluir no navegador ou no SSOX.</span><span class="sxs-lookup"><span data-stu-id="f70a4-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="f70a4-192">Force o aplicativo a ser inicializado (chame os métodos na classe `Startup`) para que o método de semeadura seja executado.</span><span class="sxs-lookup"><span data-stu-id="f70a4-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="f70a4-193">Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado.</span><span class="sxs-lookup"><span data-stu-id="f70a4-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="f70a4-194">Faça isso com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="f70a4-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="f70a4-195">Clique com botão direito do mouse no ícone de bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou **Parar Site**</span><span class="sxs-lookup"><span data-stu-id="f70a4-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Ícone de bandeja do sistema do IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="f70a4-198">Se você estiver executando o VS no modo sem depuração, pressione F5 para executar no modo de depuração</span><span class="sxs-lookup"><span data-stu-id="f70a4-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="f70a4-199">Se você estiver executando o VS no modo de depuração, pare o depurador e pressione F5</span><span class="sxs-lookup"><span data-stu-id="f70a4-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f70a4-200">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f70a4-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f70a4-201">Exclua todos os registros no BD (para que o método de semeadura seja executado).</span><span class="sxs-lookup"><span data-stu-id="f70a4-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="f70a4-202">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f70a4-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="f70a4-203">O aplicativo mostra os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="f70a4-203">The app shows the seeded data.</span></span>

![Aplicativo de filme MVC aberto no Microsoft Edge, mostrando os dados do filme](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="f70a4-205">[Anterior](adding-model.md)
> [Próximo](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="f70a4-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
