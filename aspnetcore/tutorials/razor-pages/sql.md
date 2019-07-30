---
title: Trabalhar com um banco de dados e o ASP.NET Core
author: rick-anderson
description: Explica como trabalhar com um banco de dados e o ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 197697f28e9faa45c1ac2b7f993bde15994957e5
ms.sourcegitcommit: 051f068c78931432e030b60094c38376d64d013e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440383"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="3658d-103">Trabalhar com um banco de dados e o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3658d-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="3658d-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="3658d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="3658d-105">O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3658d-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="3658d-106">O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3658d-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3658d-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3658d-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3658d-108">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3658d-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="3658d-109">O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="3658d-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="3658d-110">Para o desenvolvimento local, ele obtém a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3658d-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3658d-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3658d-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3658d-112">O valor do nome do banco de dados (`Database={Database name}`) será diferente para o seu código gerado.</span><span class="sxs-lookup"><span data-stu-id="3658d-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="3658d-113">O valor do nome é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="3658d-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3658d-114">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3658d-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="3658d-115">Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão como um servidor de banco de dados real.</span><span class="sxs-lookup"><span data-stu-id="3658d-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="3658d-116">Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="3658d-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3658d-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3658d-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="3658d-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="3658d-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="3658d-119">O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas.</span><span class="sxs-lookup"><span data-stu-id="3658d-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="3658d-120">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="3658d-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="3658d-121">Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="3658d-121">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="3658d-122">No menu **Exibir**, abra **SSOX** (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="3658d-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu de exibição](sql/_static/ssox.png)

* <span data-ttu-id="3658d-124">Clique com o botão direito do mouse na tabela `Movie` e selecione **Designer de exibição**:</span><span class="sxs-lookup"><span data-stu-id="3658d-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menus contextuais abertos na tabela Movie](sql/_static/design.png)

  ![Tabelas Movie abertas no Designer](sql/_static/dv.png)

<span data-ttu-id="3658d-127">Observe o ícone de chave ao lado de `ID`.</span><span class="sxs-lookup"><span data-stu-id="3658d-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="3658d-128">Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="3658d-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="3658d-129">Clique com o botão direito do mouse na tabela `Movie` e selecione **Exibir dados**:</span><span class="sxs-lookup"><span data-stu-id="3658d-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3658d-131">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3658d-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="3658d-132">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="3658d-132">Seed the database</span></span>

<span data-ttu-id="3658d-133">Crie uma classe chamada `SeedData` na pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3658d-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="3658d-134">Se houver um filme no BD, o inicializador de semeadura será retornado e nenhum filme será adicionado.</span><span class="sxs-lookup"><span data-stu-id="3658d-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="3658d-135">Adicionar o inicializador de semeadura</span><span class="sxs-lookup"><span data-stu-id="3658d-135">Add the seed initializer</span></span>

<span data-ttu-id="3658d-136">Em *Program.cs*, modifique o método `Main` para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="3658d-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="3658d-137">Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="3658d-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="3658d-138">Chame o método de semente passando a ele o contexto.</span><span class="sxs-lookup"><span data-stu-id="3658d-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="3658d-139">Descarte o contexto quando o método de semente for concluído.</span><span class="sxs-lookup"><span data-stu-id="3658d-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="3658d-140">O código a seguir mostra o arquivo *Program.cs* atualizado.</span><span class="sxs-lookup"><span data-stu-id="3658d-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="3658d-141">Um aplicativo de produção não chamaria `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="3658d-141">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="3658d-142">Ele é adicionado ao código anterior para evitar a exceção a seguir quando `Update-Database` não foi executado:</span><span class="sxs-lookup"><span data-stu-id="3658d-142">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="3658d-143">SqlException: não pode abrir o banco de dados "RazorPagesMovieContext-21" solicitado pelo logon.</span><span class="sxs-lookup"><span data-stu-id="3658d-143">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="3658d-144">O logon falhou.</span><span class="sxs-lookup"><span data-stu-id="3658d-144">The login failed.</span></span>
<span data-ttu-id="3658d-145">O logon falhou para o usuário 'user name'.</span><span class="sxs-lookup"><span data-stu-id="3658d-145">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="3658d-146">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="3658d-146">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3658d-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3658d-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3658d-148">Exclua todos os registros no BD.</span><span class="sxs-lookup"><span data-stu-id="3658d-148">Delete all the records in the DB.</span></span> <span data-ttu-id="3658d-149">Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="3658d-149">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="3658d-150">Force o aplicativo a ser inicializado (chame os métodos na classe `Startup`) para que o método de semeadura seja executado.</span><span class="sxs-lookup"><span data-stu-id="3658d-150">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="3658d-151">Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado.</span><span class="sxs-lookup"><span data-stu-id="3658d-151">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="3658d-152">Faça isso com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="3658d-152">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="3658d-153">Clique com botão direito do mouse no ícone de bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou **Parar site**:</span><span class="sxs-lookup"><span data-stu-id="3658d-153">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="3658d-156">Se você estiver executando o VS no modo sem depuração, pressione F5 para executar no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="3658d-156">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="3658d-157">Se você estiver executando o VS no modo de depuração, pare o depurador e pressione F5.</span><span class="sxs-lookup"><span data-stu-id="3658d-157">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3658d-158">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3658d-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3658d-159">Exclua todos os registros no BD (para que o método de semeadura seja executado).</span><span class="sxs-lookup"><span data-stu-id="3658d-159">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="3658d-160">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3658d-160">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="3658d-161">O aplicativo mostra os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="3658d-161">The app shows the seeded data.</span></span>

---

<span data-ttu-id="3658d-162">O próximo tutorial melhorará a apresentação dos dados.</span><span class="sxs-lookup"><span data-stu-id="3658d-162">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3658d-163">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3658d-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3658d-164">[Anterior: Razor Pages geradas por scaffolding](xref:tutorials/razor-pages/page)
> [A seguir: atualização de páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="3658d-164">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="3658d-165">O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3658d-165">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="3658d-166">O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3658d-166">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3658d-167">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3658d-167">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3658d-168">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3658d-168">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="3658d-169">Para obter mais informações sobre os métodos usados em `ConfigureServices`, veja:</span><span class="sxs-lookup"><span data-stu-id="3658d-169">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="3658d-170">[Suporte ao RGPD (Regulamento Geral sobre a Proteção de Dados) da UE no ASP.NET Core](xref:security/gdpr) para `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="3658d-170">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="3658d-171">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="3658d-171">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="3658d-172">O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="3658d-172">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="3658d-173">Para o desenvolvimento local, ele obtém a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3658d-173">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3658d-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3658d-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3658d-175">O valor do nome do banco de dados (`Database={Database name}`) será diferente para o seu código gerado.</span><span class="sxs-lookup"><span data-stu-id="3658d-175">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="3658d-176">O valor do nome é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="3658d-176">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3658d-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3658d-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3658d-178">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3658d-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="3658d-179">Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão como um servidor de banco de dados real.</span><span class="sxs-lookup"><span data-stu-id="3658d-179">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="3658d-180">Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="3658d-180">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3658d-181">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3658d-181">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="3658d-182">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="3658d-182">SQL Server Express LocalDB</span></span>

<span data-ttu-id="3658d-183">O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas.</span><span class="sxs-lookup"><span data-stu-id="3658d-183">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="3658d-184">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="3658d-184">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="3658d-185">Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="3658d-185">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="3658d-186">No menu **Exibir**, abra **SSOX** (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="3658d-186">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu de exibição](sql/_static/ssox.png)

* <span data-ttu-id="3658d-188">Clique com o botão direito do mouse na tabela `Movie` e selecione **Designer de exibição**:</span><span class="sxs-lookup"><span data-stu-id="3658d-188">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menu contextual aberto na tabela Movie](sql/_static/design.png)

  ![Tabela Movie aberta no Designer](sql/_static/dv.png)

<span data-ttu-id="3658d-191">Observe o ícone de chave ao lado de `ID`.</span><span class="sxs-lookup"><span data-stu-id="3658d-191">Note the key icon next to `ID`.</span></span> <span data-ttu-id="3658d-192">Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="3658d-192">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="3658d-193">Clique com o botão direito do mouse na tabela `Movie` e selecione **Exibir dados**:</span><span class="sxs-lookup"><span data-stu-id="3658d-193">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3658d-195">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3658d-195">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3658d-196">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3658d-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="3658d-197">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="3658d-197">Seed the database</span></span>

<span data-ttu-id="3658d-198">Crie uma classe chamada `SeedData` na pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3658d-198">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="3658d-199">Se houver um filme no BD, o inicializador de semeadura será retornado e nenhum filme será adicionado.</span><span class="sxs-lookup"><span data-stu-id="3658d-199">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="3658d-200">Adicionar o inicializador de semeadura</span><span class="sxs-lookup"><span data-stu-id="3658d-200">Add the seed initializer</span></span>

<span data-ttu-id="3658d-201">Em *Program.cs*, modifique o método `Main` para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="3658d-201">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="3658d-202">Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="3658d-202">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="3658d-203">Chame o método de semente passando a ele o contexto.</span><span class="sxs-lookup"><span data-stu-id="3658d-203">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="3658d-204">Descarte o contexto quando o método de semente for concluído.</span><span class="sxs-lookup"><span data-stu-id="3658d-204">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="3658d-205">O código a seguir mostra o arquivo *Program.cs* atualizado.</span><span class="sxs-lookup"><span data-stu-id="3658d-205">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="3658d-206">Um aplicativo de produção não chamaria `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="3658d-206">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="3658d-207">Ele é adicionado ao código anterior para evitar a exceção a seguir quando `Update-Database` não foi executado:</span><span class="sxs-lookup"><span data-stu-id="3658d-207">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="3658d-208">SqlException: não pode abrir o banco de dados "RazorPagesMovieContext-21" solicitado pelo logon.</span><span class="sxs-lookup"><span data-stu-id="3658d-208">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="3658d-209">O logon falhou.</span><span class="sxs-lookup"><span data-stu-id="3658d-209">The login failed.</span></span>
<span data-ttu-id="3658d-210">O logon falhou para o usuário 'user name'.</span><span class="sxs-lookup"><span data-stu-id="3658d-210">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="3658d-211">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="3658d-211">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3658d-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3658d-212">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3658d-213">Exclua todos os registros no BD.</span><span class="sxs-lookup"><span data-stu-id="3658d-213">Delete all the records in the DB.</span></span> <span data-ttu-id="3658d-214">Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="3658d-214">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="3658d-215">Force o aplicativo a ser inicializado (chame os métodos na classe `Startup`) para que o método de semeadura seja executado.</span><span class="sxs-lookup"><span data-stu-id="3658d-215">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="3658d-216">Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado.</span><span class="sxs-lookup"><span data-stu-id="3658d-216">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="3658d-217">Faça isso com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="3658d-217">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="3658d-218">Clique com botão direito do mouse no ícone na bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou em **Parar site**:</span><span class="sxs-lookup"><span data-stu-id="3658d-218">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="3658d-221">Se você estiver executando o VS no modo sem depuração, pressione F5 para executar no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="3658d-221">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="3658d-222">Se você estiver executando o VS no modo de depuração, pare o depurador e pressione F5.</span><span class="sxs-lookup"><span data-stu-id="3658d-222">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3658d-223">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3658d-223">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3658d-224">Exclua todos os registros no BD (para que o método de semeadura seja executado).</span><span class="sxs-lookup"><span data-stu-id="3658d-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="3658d-225">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3658d-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="3658d-226">O aplicativo mostra os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="3658d-226">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3658d-227">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3658d-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3658d-228">Exclua todos os registros no BD (para que o método de semeadura seja executado).</span><span class="sxs-lookup"><span data-stu-id="3658d-228">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="3658d-229">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3658d-229">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="3658d-230">O aplicativo mostra os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="3658d-230">The app shows the seeded data.</span></span>

---

<span data-ttu-id="3658d-231">O aplicativo mostra os dados propagados:</span><span class="sxs-lookup"><span data-stu-id="3658d-231">The app shows the seeded data:</span></span>

![Aplicativo de filme aberto no Chrome mostrando os dados do filme](sql/_static/m55.png)

<span data-ttu-id="3658d-233">O próximo tutorial limpará a apresentação dos dados.</span><span class="sxs-lookup"><span data-stu-id="3658d-233">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3658d-234">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3658d-234">Additional resources</span></span>

* [<span data-ttu-id="3658d-235">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="3658d-235">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="3658d-236">[Anterior: Razor Pages geradas por scaffolding](xref:tutorials/razor-pages/page)
> [A seguir: atualização de páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="3658d-236">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end