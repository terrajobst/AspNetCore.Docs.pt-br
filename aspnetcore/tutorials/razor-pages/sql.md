---
title: Trabalhar com um banco de dados e o ASP.NET Core
author: rick-anderson
description: Explica como trabalhar com um banco de dados e o ASP.NET Core.
ms.author: riande
ms.date: 12/07/2017
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 6cef55382d8c77e95280ea4eea2dbc2af1c81987
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64885201"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="b4974-103">Trabalhar com um banco de dados e o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4974-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="b4974-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="b4974-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="b4974-105">O objeto `RazorPagesMovieContext` cuida da tarefa de se conectar ao banco de dados e mapear objetos `Movie` para registros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b4974-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="b4974-106">O contexto de banco de dados é registrado com o contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection) no método `ConfigureServices` em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b4974-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4974-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4974-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4974-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4974-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4974-109">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b4974-109">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="b4974-110">Para obter mais informações sobre os métodos usados em `ConfigureServices`, veja:</span><span class="sxs-lookup"><span data-stu-id="b4974-110">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="b4974-111">[Suporte ao RGPD (Regulamento Geral sobre a Proteção de Dados) da UE no ASP.NET Core](xref:security/gdpr) para `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="b4974-111">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="b4974-112">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="b4974-112">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="b4974-113">O sistema de [Configuração](xref:fundamentals/configuration/index) do ASP.NET Core lê a `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="b4974-113">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="b4974-114">Para o desenvolvimento local, ele obtém a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b4974-114">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4974-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4974-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b4974-116">O valor do nome do banco de dados (`Database={Database name}`) será diferente para o seu código gerado.</span><span class="sxs-lookup"><span data-stu-id="b4974-116">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="b4974-117">O valor do nome é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="b4974-117">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4974-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4974-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4974-119">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b4974-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="b4974-120">Quando o aplicativo é implantado em um servidor de teste ou de produção, uma variável de ambiente pode ser usada para definir a cadeia de conexão como um servidor de banco de dados real.</span><span class="sxs-lookup"><span data-stu-id="b4974-120">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="b4974-121">Consulte [Configuração](xref:fundamentals/configuration/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="b4974-121">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4974-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4974-122">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="b4974-123">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="b4974-123">SQL Server Express LocalDB</span></span>

<span data-ttu-id="b4974-124">O LocalDB é uma versão leve do mecanismo de banco de dados do SQL Server Express direcionada para o desenvolvimento de programas.</span><span class="sxs-lookup"><span data-stu-id="b4974-124">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="b4974-125">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="b4974-125">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="b4974-126">Por padrão, o banco de dados LocalDB cria arquivos `*.mdf` no diretório `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="b4974-126">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="b4974-127">No menu **Exibir**, abra **SSOX** (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="b4974-127">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu de exibição](sql/_static/ssox.png)

* <span data-ttu-id="b4974-129">Clique com o botão direito do mouse na tabela `Movie` e selecione **Designer de exibição**:</span><span class="sxs-lookup"><span data-stu-id="b4974-129">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menu contextual aberto na tabela Movie](sql/_static/design.png)

  ![Tabela Movie aberta no Designer](sql/_static/dv.png)

<span data-ttu-id="b4974-132">Observe o ícone de chave ao lado de `ID`.</span><span class="sxs-lookup"><span data-stu-id="b4974-132">Note the key icon next to `ID`.</span></span> <span data-ttu-id="b4974-133">Por padrão, o EF cria uma propriedade chamada `ID` para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="b4974-133">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="b4974-134">Clique com o botão direito do mouse na tabela `Movie` e selecione **Exibir dados**:</span><span class="sxs-lookup"><span data-stu-id="b4974-134">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabela Movie aberta mostrando os dados da tabela](sql/_static/vd22.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4974-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4974-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4974-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b4974-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="b4974-138">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="b4974-138">Seed the database</span></span>

<span data-ttu-id="b4974-139">Crie uma classe chamada `SeedData` na pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="b4974-139">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="b4974-140">Se houver um filme no BD, o inicializador de semeadura será retornado e nenhum filme será adicionado.</span><span class="sxs-lookup"><span data-stu-id="b4974-140">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="b4974-141">Adicionar o inicializador de semeadura</span><span class="sxs-lookup"><span data-stu-id="b4974-141">Add the seed initializer</span></span>

<span data-ttu-id="b4974-142">Em *Program.cs*, modifique o método `Main` para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b4974-142">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="b4974-143">Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="b4974-143">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="b4974-144">Chame o método de semente passando a ele o contexto.</span><span class="sxs-lookup"><span data-stu-id="b4974-144">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="b4974-145">Descarte o contexto quando o método de semente for concluído.</span><span class="sxs-lookup"><span data-stu-id="b4974-145">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="b4974-146">O código a seguir mostra o arquivo *Program.cs* atualizado.</span><span class="sxs-lookup"><span data-stu-id="b4974-146">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="b4974-147">Um aplicativo de produção não chamaria `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="b4974-147">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="b4974-148">Ele é adicionado ao código anterior para evitar a exceção a seguir quando `Update-Database` não foi executado:</span><span class="sxs-lookup"><span data-stu-id="b4974-148">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="b4974-149">SqlException: não pode abrir o banco de dados "RazorPagesMovieContext-21" solicitado pelo logon.</span><span class="sxs-lookup"><span data-stu-id="b4974-149">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="b4974-150">O logon falhou.</span><span class="sxs-lookup"><span data-stu-id="b4974-150">The login failed.</span></span>
<span data-ttu-id="b4974-151">O logon falhou para o usuário 'user name'.</span><span class="sxs-lookup"><span data-stu-id="b4974-151">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b4974-152">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="b4974-152">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4974-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4974-153">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b4974-154">Exclua todos os registros no BD.</span><span class="sxs-lookup"><span data-stu-id="b4974-154">Delete all the records in the DB.</span></span> <span data-ttu-id="b4974-155">Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="b4974-155">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="b4974-156">Force o aplicativo a ser inicializado (chame os métodos na classe `Startup`) para que o método de semeadura seja executado.</span><span class="sxs-lookup"><span data-stu-id="b4974-156">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="b4974-157">Para forçar a inicialização, o IIS Express deve ser interrompido e reiniciado.</span><span class="sxs-lookup"><span data-stu-id="b4974-157">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="b4974-158">Faça isso com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="b4974-158">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="b4974-159">Clique com botão direito do mouse no ícone de bandeja do sistema do IIS Express na área de notificação e toque em **Sair** ou **Parar site**:</span><span class="sxs-lookup"><span data-stu-id="b4974-159">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Ícone de bandeja do sistema do IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextual](sql/_static/stopIIS.png)

    * <span data-ttu-id="b4974-162">Se você estiver executando o VS no modo sem depuração, pressione F5 para executar no modo de depuração.</span><span class="sxs-lookup"><span data-stu-id="b4974-162">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="b4974-163">Se você estiver executando o VS no modo de depuração, pare o depurador e pressione F5.</span><span class="sxs-lookup"><span data-stu-id="b4974-163">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4974-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4974-164">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b4974-165">Exclua todos os registros no BD (para que o método de semeadura seja executado).</span><span class="sxs-lookup"><span data-stu-id="b4974-165">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="b4974-166">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b4974-166">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="b4974-167">O aplicativo mostra os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="b4974-167">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4974-168">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b4974-168">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b4974-169">Exclua todos os registros no BD (para que o método de semeadura seja executado).</span><span class="sxs-lookup"><span data-stu-id="b4974-169">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="b4974-170">Interrompa e inicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b4974-170">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="b4974-171">O aplicativo mostra os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="b4974-171">The app shows the seeded data.</span></span>

---

<span data-ttu-id="b4974-172">O aplicativo mostra os dados propagados:</span><span class="sxs-lookup"><span data-stu-id="b4974-172">The app shows the seeded data:</span></span>

![Aplicativo de filme aberto no Chrome mostrando os dados do filme](sql/_static/m55.png)

<span data-ttu-id="b4974-174">O próximo tutorial limpará a apresentação dos dados.</span><span class="sxs-lookup"><span data-stu-id="b4974-174">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4974-175">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b4974-175">Additional resources</span></span>

* [<span data-ttu-id="b4974-176">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="b4974-176">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="b4974-177">[Anterior: Razor Pages geradas por scaffolding](xref:tutorials/razor-pages/page)
> [A seguir: atualização de páginas](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="b4974-177">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>
