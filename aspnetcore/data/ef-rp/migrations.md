---
title: Páginas Razor com o EF Core no ASP.NET Core – Migrações – 4 de 8
author: rick-anderson
description: Neste tutorial, você começa a usar o recurso de migrações do EF Core para gerenciar alterações do modelo de dados em um aplicativo ASP.NET Core MVC.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/migrations
ms.openlocfilehash: 73624f515e8089b5852864b60ec66ad79c7475c3
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914083"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a>Páginas Razor com o EF Core no ASP.NET Core – Migrações – 4 de 8

Por [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) e [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Este tutorial apresenta o recurso de migrações do EF Core para gerenciar alterações do modelo de dados.

Quando um novo aplicativo é desenvolvido, o modelo de dados é alterado com frequência. Sempre que o modelo é alterado, ele fica fora de sincronia com o banco de dados. Esta série de tutoriais começa configurando o Entity Framework para criar o banco de dados, caso ele não exista. Cada vez que o modelo de dados é alterado, você precisa remover o banco de dados. Na próxima vez em que o aplicativo for executado, a chamada para `EnsureCreated` recriará o banco de dados para que corresponda ao novo modelo de dados. A classe `DbInitializer` então é executada para propagar o novo banco de dados.

Essa abordagem para manter o banco de dados em sincronia com o modelo de dados funciona bem até que você implante o aplicativo em produção. Quando o aplicativo é executado em produção, normalmente, ele armazena dados que precisam ser mantidos. O aplicativo não pode começar com um banco de dados de teste sempre que uma alteração é feita (como a adição de uma nova coluna). O recurso Migrações do EF Core resolve esse problema, permitindo que o EF Core atualize o esquema de banco de dados em vez de criar um novo banco de dados.

Em vez de remover e recriar o banco de dados quando o modelo de dados é alterado, as migrações atualizam o esquema e retêm os dados existentes.

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>Remover o banco de dados

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Use o SSOX **(Pesquisador de Objetos do SQL Server)** para excluir o banco de dados ou execute o seguinte comando no PMC **(Console do Gerenciador de Pacotes)** :

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Execute o comando a seguir no prompt de comando para instalar as ferramentas da CLI do EF:

  ```console
  dotnet tool install --global dotnet-ef --version 3.0.0-*
  ```

* No prompt de comando, navegue até a pasta do projeto. A pasta do projeto contém o arquivo *ContosoUniversity.csproj*.

* Exclua o arquivo *CU.db* ou execute o seguinte comando:

  ```console
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>Criar uma migração inicial

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Executar os seguintes comandos no PMC:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Verifique se o prompt de comando está na pasta do projeto e execute os seguintes comandos:

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a>Métodos Para Cima e Para Baixo

O comando `migrations add` do EF Core gerou um código para criar o banco de dados. Esse código de migrações está localizado no arquivo *Migrations\<timestamp>_InitialCreate.cs*. O método `Up` da classe `InitialCreate` cria as tabelas de banco de dados que correspondem aos conjuntos de entidades do modelo de dados. O método `Down` exclui-os, conforme mostrado no seguinte exemplo:

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

O código anterior refere-se à migração inicial. O código:

* Foi gerado pelo comando `migrations add InitialCreate`. 
* É executado pelo comando `database update`.
* Cria um banco de dados para o modelo de dados especificado pela classe de contexto do banco de dados.

O parâmetro de nome da migração ("InitialCreate" no exemplo) é usado para o nome do arquivo. O nome da migração pode ser qualquer nome de arquivo válido. É melhor escolher uma palavra ou frase que resume o que está sendo feito na migração. Por exemplo, uma migração que adicionou uma tabela de departamento pode ser chamada "AddDepartmentTable".

## <a name="the-migrations-history-table"></a>A tabela de histórico de migrações

* Use SSOX ou sua ferramenta SQLite para inspecionar o banco de dados.
* Observe a adição de uma tabela `__EFMigrationsHistory`. A tabela `__EFMigrationsHistory` controla quais migrações foram aplicadas ao banco de dados.
* Exibir os dados na tabela `__EFMigrationsHistory`. Mostra uma linha para a primeira migração.

## <a name="the-data-model-snapshot"></a>O instantâneo do modelo de dados

As migrações criam um *instantâneo* do modelo de dados atual em *Migrations/SchoolContextModelSnapshot.cs*. Quando você adiciona uma migração, o EF determina o que foi alterado, comparando o modelo de dados atual com o arquivo de instantâneo.

Como o arquivo de instantâneo rastreia o estado do modelo de dados, não é possível excluir uma migração excluindo o arquivo `<timestamp>_<migrationname>.cs`. Para fazer backup da migração mais recente, você precisa usar o comando `migrations remove`. Esse comando exclui a migração e garante que o instantâneo seja redefinido corretamente. Para saber mais, confira [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

## <a name="remove-ensurecreated"></a>Remover EnsureCreated

Esta série de tutoriais começou usando o `EnsureCreated`. O `EnsureCreated` não cria uma tabela de histórico de migrações, portanto, não pode ser usada com migrações. Ele foi projetado para teste ou criação rápida de protótipos em que o banco de dados é removido e recriado com frequência.

Deste ponto em diante, os tutoriais usarão as migrações.

Em *data/DBInitializer.cs*, comente a seguinte linha:

```csharp
context.Database.EnsureCreated();
```
Execute o aplicativo e verifique se o banco de dados é propagado.

## <a name="applying-migrations-in-production"></a>Aplicando migrações na produção

Recomendamos que os aplicativos de produção **não** chamem [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) na inicialização do aplicativo. `Migrate` não deve ser chamado de um aplicativo implantado em um farm de servidores. Se o aplicativo for escalado horizontalmente para várias instâncias de servidor, será difícil garantir que as atualizações do esquema de banco de dados não ocorram de vários servidores ou que estejam em conflito com o acesso de leitura/gravação.

A migração de banco de dados deve ser feita como parte da implantação e de maneira controlada. Abordagens de migração de banco de dados de produção incluem:

* Uso de migrações para criar scripts SQL e uso dos scripts SQL na implantação.
* Execução de `dotnet ef database update` em um ambiente controlado.

## <a name="troubleshooting"></a>Solução de problemas

Se o aplicativo usar SQL Server LocalDB e exibir a seguinte exceção:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

A solução pode ser executar `dotnet ef database update` em um prompt de comando.

### <a name="additional-resources"></a>Recursos adicionais

* [CLI do EF Core](/ef/core/miscellaneous/cli/dotnet).
* [Console do Gerenciador de Pacotes (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>Próximas etapas

O próximo tutorial cria o modelo de dados adicionando propriedades da entidade e novas entidades.

> [!div class="step-by-step"]
> [Tutorial anterior](xref:data/ef-rp/sort-filter-page)
> [Próximo tutorial](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Neste tutorial, o recurso de migrações do EF Core para o gerenciamento de alterações do modelo de dados é usado.

Caso tenha problemas que não consiga resolver, baixe o [aplicativo concluído](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

Quando um novo aplicativo é desenvolvido, o modelo de dados é alterado com frequência. Sempre que o modelo é alterado, ele fica fora de sincronia com o banco de dados. Este tutorial começa configurando o Entity Framework para criar o banco de dados, caso ele não exista. Sempre que o modelo de dados é alterado:

* O BD é removido.
* O EF cria um novo que corresponde ao modelo.
* O aplicativo propaga o BD com os dados de teste.

Essa abordagem para manter o BD em sincronia com o modelo de dados funciona bem até que você implante o aplicativo em produção. Quando o aplicativo é executado em produção, normalmente, ele armazena dados que precisam ser mantidos. O aplicativo não pode começar com um BD de teste sempre que uma alteração é feita (como a adição de uma nova coluna). O recurso Migrações do EF Core resolve esse problema, permitindo que o EF Core atualize o esquema de BD em vez de criar um novo BD.

Em vez de remover e recriar o BD quando o modelo de dados é alterado, as migrações atualizam o esquema e retêm os dados existentes.

## <a name="drop-the-database"></a>Remover o banco de dados

Use o **SSOX** (Pesquisador de Objetos do SQL Server) ou o comando `database drop`:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No **PMC** (Console do Gerenciador de Pacotes), execute o seguinte comando:

```PMC
Drop-Database
```

Execute `Get-Help about_EntityFrameworkCore` no PMC para obter informações de ajuda.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Abra uma janela Comando e navegue para a pasta do projeto. A pasta do projeto contém o arquivo *Startup.cs*.

Insira o seguinte na janela Comando:

 ```console
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>Criar uma migração inicial e atualizar o BD

Crie o projeto e a primeira migração.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a>Examinar os métodos Up e Down

O comando `migrations add` do EF Core gerou um código para criar o BD. Esse código de migrações está localizado no arquivo *Migrations\<timestamp>_InitialCreate.cs*. O método `Up` da classe `InitialCreate` cria as tabelas de BD que correspondem aos conjuntos de entidades do modelo de dados. O método `Down` exclui-os, conforme mostrado no seguinte exemplo:

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

As migrações chamam o método `Up` para implementar as alterações do modelo de dados para uma migração. Quando você insere um comando para reverter a atualização, as migrações chamam o método `Down`.

O código anterior refere-se à migração inicial. Esse código foi criado quando o comando `migrations add InitialCreate` foi executado. O parâmetro de nome da migração ("InitialCreate" no exemplo) é usado para o nome do arquivo. O nome da migração pode ser qualquer nome de arquivo válido. É melhor escolher uma palavra ou frase que resume o que está sendo feito na migração. Por exemplo, uma migração que adicionou uma tabela de departamento pode ser chamada "AddDepartmentTable".

Se a migração inicial foi criada e o BD existe:

* O código de criação do BD é gerado.
* O código de criação do BD não precisa ser executado porque o BD já corresponde ao modelo de dados. Se o código de criação do BD for executado, ele não fará nenhuma alteração porque o BD já corresponde ao modelo de dados.

Quando o aplicativo é implantado em um novo ambiente, o código de criação do BD precisa ser executado para criar o BD.

Anteriormente, o BD foi removido, e não existe mais. Então, as migrações criam o novo BD.

### <a name="the-data-model-snapshot"></a>O instantâneo do modelo de dados

As migrações criam um *instantâneo* do esquema de banco de dados atual em *Migrations/SchoolContextModelSnapshot.cs*. Quando você adiciona uma migração, o EF determina o que foi alterado, comparando o modelo de dados com o arquivo de instantâneo.

Para excluir uma migração, use o seguinte comando:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Remove-Migration

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```console
dotnet ef migrations remove
```

Para saber mais, confira [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

---

O comando de exclusão de migrações exclui a migração e garante que o instantâneo seja redefinido corretamente.

### <a name="remove-ensurecreated-and-test-the-app"></a>Remover EnsureCreated e testar o aplicativo

Para o desenvolvimento inicial, `EnsureCreated` foi usado. Neste tutorial, as migrações são usadas. `EnsureCreated` tem as seguintes limitações:

* Ignora as migrações e cria o BD e o esquema.
* Não cria uma tabela de migrações.
* *Não* pode ser usado com migrações.
* Foi projetado para teste ou criação rápida de protótipos em que o BD é removido e recriado com frequência.

Remova `EnsureCreated`:

```csharp
context.Database.EnsureCreated();
```

Execute o aplicativo e verifique se o BD é propagado.

### <a name="inspect-the-database"></a>Inspecionar o banco de dados

Use o **Pesquisador de Objetos do SQL Server** para inspecionar o BD. Observe a adição de uma tabela `__EFMigrationsHistory`. A tabela `__EFMigrationsHistory` controla quais migrações foram aplicadas ao BD. Exiba os dados na `__EFMigrationsHistory` tabela; ela mostra uma linha para a primeira migração. O último log no exemplo de saída da CLI anterior mostra a instrução INSERT que cria essa linha.

Execute o aplicativo e verifique se tudo funciona.

## <a name="applying-migrations-in-production"></a>Aplicando migrações na produção

Recomendamos que os aplicativos de produção **não** chamem [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) na inicialização do aplicativo. `Migrate` não deve ser chamado em um aplicativo no farm de servidores. Por exemplo, se o aplicativo foi implantado na nuvem com escalabilidade horizontal (várias instâncias do aplicativo estão sendo executadas).

A migração de banco de dados deve ser feita como parte da implantação e de maneira controlada. Abordagens de migração de banco de dados de produção incluem:

* Uso de migrações para criar scripts SQL e uso dos scripts SQL na implantação.
* Execução de `dotnet ef database update` em um ambiente controlado.

O EF Core usa a tabela `__MigrationsHistory` para ver se uma migração precisa ser executada. Se o BD estiver atualizado, nenhuma migração será executada.

## <a name="troubleshooting"></a>Solução de problemas

Baixar o [aplicativo concluído](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).

O aplicativo gera a seguinte exceção:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Solução: Execute `dotnet ef database update`

### <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [CLI do .NET Core](/ef/core/miscellaneous/cli/dotnet).
* [Console do Gerenciador de Pacotes (Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [Anterior](xref:data/ef-rp/sort-filter-page)
> [Próximo](xref:data/ef-rp/complex-data-model)

::: moniker-end

