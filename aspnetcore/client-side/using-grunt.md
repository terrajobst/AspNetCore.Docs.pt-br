---
title: Usar o Grunt no ASP.NET Core
author: rick-anderson
description: Usar o Grunt no ASP.NET Core
ms.author: riande
ms.date: 05/14/2019
uid: client-side/using-grunt
ms.openlocfilehash: 4d9b6cf6f9a0007e9722bc054f0d9a7608f1473b
ms.sourcegitcommit: 3ee6ee0051c3d2c8d47a58cb17eef1a84a4c46a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65620991"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="2ab6c-103">Usar o Grunt no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2ab6c-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="2ab6c-104">Por [Noel arroz](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)</span><span class="sxs-lookup"><span data-stu-id="2ab6c-104">By [Noel Rice](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)</span></span>

<span data-ttu-id="2ab6c-105">Grunt é um executor de tarefas JavaScript que automatiza a minimização de script, compilação TypeScript, ferramentas de "lint" qualidade do código, processadores de pré-lançamento do CSS e praticamente qualquer tarefa repetitiva que precisa fazer para dar suporte ao desenvolvimento de cliente.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-105">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="2ab6c-106">Grunt tem suporte total no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-106">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="2ab6c-107">Este exemplo usa um projeto vazio do ASP.NET Core como ponto de partida, para mostrar como automatizar o processo de compilação do cliente do zero.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-107">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="2ab6c-108">O exemplo concluído limpa o diretório de implantação de destino, combina arquivos JavaScript, verificações de qualidade do código, condensa o conteúdo do arquivo JavaScript e implanta na raiz do seu aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-108">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="2ab6c-109">Usaremos os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="2ab6c-109">We will use the following packages:</span></span>

* <span data-ttu-id="2ab6c-110">**grunt**: O pacote de executor de tarefa do Grunt.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-110">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="2ab6c-111">**grunt-contrib-clean**: Um plug-in que remove os arquivos ou diretórios.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-111">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="2ab6c-112">**grunt-contrib-jshint**: Um plug-in que analisa a qualidade do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-112">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="2ab6c-113">**grunt-contrib-concat**: Um plug-in que une os arquivos em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-113">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="2ab6c-114">**grunt-contrib-uglify**: Um plug-in que minimiza o JavaScript para reduzir o tamanho.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-114">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="2ab6c-115">**grunt-contrib-watch**: Um plug-in que observa a atividade de arquivos.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-115">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="2ab6c-116">Preparando o aplicativo</span><span class="sxs-lookup"><span data-stu-id="2ab6c-116">Preparing the application</span></span>

<span data-ttu-id="2ab6c-117">Para começar, configure um novo aplicativo web vazio e adicionar arquivos de exemplo do TypeScript.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-117">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="2ab6c-118">Arquivos TypeScript são automaticamente compilados em JavaScript usando as configurações do Visual Studio padrão e serão nosso matéria-prima para processar usando o Grunt.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-118">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="2ab6c-119">No Visual Studio, crie um novo `ASP.NET Web Application`.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-119">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="2ab6c-120">No **novo projeto ASP.NET** caixa de diálogo, selecione o ASP.NET Core **vazia** modelo e clique no botão Okey.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-120">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="2ab6c-121">No Gerenciador de soluções, examine a estrutura do projeto.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-121">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="2ab6c-122">O `\src` pasta inclui vazia `wwwroot` e `Dependencies` nós.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-122">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![solução de web vazio](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="2ab6c-124">Adicionar uma nova pasta chamada `TypeScript` no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-124">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="2ab6c-125">Antes de adicionar todos os arquivos, certifique-se de que o Visual Studio tem a opção ' Compilar ao salvar ' para arquivos TypeScript check.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-125">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="2ab6c-126">Navegue até **ferramentas** > **opções** > **Editor de texto** > **Typescript**  >  **Projeto**:</span><span class="sxs-lookup"><span data-stu-id="2ab6c-126">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![Opções de configuração de compilação automática de arquivos TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="2ab6c-128">Clique com botão direito do `TypeScript` diretório e selecione **Adicionar > Novo Item** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-128">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="2ab6c-129">Selecione o **arquivo JavaScript** de item e nomeie o arquivo *Tastes.ts* (Observe o \*extensão. TS).</span><span class="sxs-lookup"><span data-stu-id="2ab6c-129">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="2ab6c-130">Copie a linha de código do TypeScript abaixo para o arquivo (quando você salva, um novo *Tastes.js* arquivo aparecerá com a fonte do JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2ab6c-130">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="2ab6c-131">Adicionar um segundo arquivo para o **TypeScript** diretório e nomeie-o `Food.ts`.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-131">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="2ab6c-132">Copie o código a seguir para o arquivo.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-132">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="2ab6c-133">Configuração do NPM</span><span class="sxs-lookup"><span data-stu-id="2ab6c-133">Configuring NPM</span></span>

<span data-ttu-id="2ab6c-134">Em seguida, configure o NPM para baixar o grunt e tarefas do grunt.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-134">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="2ab6c-135">No Gerenciador de soluções, clique com botão direito no projeto e selecione **Adicionar > Novo Item** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-135">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="2ab6c-136">Selecione o **arquivo de configuração do NPM** item, deixe o nome padrão, *Package. JSON*e clique no **Add** botão.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-136">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="2ab6c-137">No *Package. JSON* do arquivo, dentro de `devDependencies` chaves de objeto, digite "grunt".</span><span class="sxs-lookup"><span data-stu-id="2ab6c-137">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="2ab6c-138">Selecione `grunt` do Intellisense lista e pressione a tecla Enter.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-138">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="2ab6c-139">Visual Studio será colocada entre aspas no nome do pacote grunt e adicione dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-139">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="2ab6c-140">À direita dos dois pontos, selecione a versão estável mais recente do pacote na parte superior da lista do Intellisense (pressione `Ctrl-Space` se o Intellisense não aparece).</span><span class="sxs-lookup"><span data-stu-id="2ab6c-140">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="2ab6c-142">Usa NPM [controle de versão semântico](http://semver.org/) para organizar as dependências.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-142">NPM uses [semantic versioning](http://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="2ab6c-143">Controle de versão semântico, também conhecido como SemVer, identifica os pacotes com o esquema de numeração \<principal >.\< secundária >. \<patch >.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-143">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="2ab6c-144">IntelliSense simplifica o controle de versão semântico, mostrando apenas algumas opções comuns.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-144">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="2ab6c-145">O item superior na lista do Intellisense (0.4.5 no exemplo acima) é considerado a versão estável mais recente do pacote.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-145">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="2ab6c-146">O símbolo de acento circunflexo (^) corresponde à versão principal mais recente e o til (~) corresponde a versão secundária mais recente.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-146">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="2ab6c-147">Consulte a [referência do analisador de versão do NPM semver](https://www.npmjs.com/package/semver) como um guia para a expressividade completa que fornece SemVer.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-147">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="2ab6c-148">Adicionar mais dependências ao carregar o grunt-contrib -\* pacotes de *limpa*, *jshint*, *concat*, *tarefa uglify*e *inspeção* conforme mostrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-148">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="2ab6c-149">As versões não precisam coincidir com o exemplo.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-149">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="2ab6c-150">Salvar a *Package. JSON* arquivo.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-150">Save the *package.json* file.</span></span>

<span data-ttu-id="2ab6c-151">Os pacotes para cada `devDependencies` item fará o download, juntamente com todos os arquivos que requer que cada pacote.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-151">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="2ab6c-152">Você pode encontrar os arquivos de pacote a *node_modules* diretório, permitindo a **Mostrar todos os arquivos** botão na **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-152">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="2ab6c-154">Se necessário, você pode restaurar manualmente as dependências no **Gerenciador de soluções** clicando em `Dependencies\NPM` e selecionando o **restaurar pacotes** opção de menu.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-154">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![Restaurar pacotes](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="2ab6c-156">Configurando o Grunt</span><span class="sxs-lookup"><span data-stu-id="2ab6c-156">Configuring Grunt</span></span>

<span data-ttu-id="2ab6c-157">Grunt é configurado usando um manifesto chamado *gruntfile* que define, carrega e registra as tarefas que podem ser executadas manualmente ou configuradas para ser executado automaticamente com base em eventos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-157">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="2ab6c-158">Clique com botão direito no projeto e selecione **Add** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-158">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="2ab6c-159">Selecione o **arquivo JavaScript** modelo de item, altere o nome a ser *gruntfile*e clique no **Add** botão.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-159">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="2ab6c-160">Adicione o seguinte código ao *gruntfile*.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-160">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="2ab6c-161">O `initConfig` função define opções para cada pacote, e o restante do módulo carrega e registra tarefas.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-161">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="2ab6c-162">Dentro de `initConfig` função, adicione as opções para o `clean` conforme mostrado no exemplo de tarefa *gruntfile* abaixo.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-162">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="2ab6c-163">O `clean` tarefa aceita uma matriz de cadeias de caracteres de diretório.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-163">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="2ab6c-164">Essa tarefa remove arquivos de *wwwroot/lib* e remove toda a */temp* directory.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-164">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="2ab6c-165">Abaixo de `initConfig` função, adicione uma chamada para `grunt.loadNpmTasks`.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-165">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="2ab6c-166">Isso tornará a tarefa executável do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-166">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="2ab6c-167">Salve *gruntfile*.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-167">Save *Gruntfile.js*.</span></span> <span data-ttu-id="2ab6c-168">O arquivo deve ser semelhante a captura de tela abaixo.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-168">The file should look something like the screenshot below.</span></span>

    ![gruntfile inicial](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="2ab6c-170">Clique com botão direito *gruntfile* e selecione **Task Runner Explorer** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-170">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="2ab6c-171">O **Task Runner Explorer** janela será aberta.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-171">The **Task Runner Explorer** window will open.</span></span>

    ![menu do Gerenciador de executor de tarefas](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="2ab6c-173">Verifique `clean` mostra sob **tarefas** no **Task Runner Explorer**.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-173">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![lista de tarefas do Gerenciador de executor de tarefas](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="2ab6c-175">A tarefa limpar com o botão direito e selecione **executar** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-175">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="2ab6c-176">Uma janela de comando exibe o progresso da tarefa.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-176">A command window displays progress of the task.</span></span>

    ![tarefa de limpeza runner explorer executar tarefa](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="2ab6c-178">Não há nenhum arquivo ou diretório para limpar ainda.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-178">There are no files or directories to clean yet.</span></span> <span data-ttu-id="2ab6c-179">Se desejar, você pode criá-las manualmente no Gerenciador de soluções e, em seguida, executar a tarefa de limpeza como um teste.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-179">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="2ab6c-180">No `initConfig` função, adicione uma entrada para `concat` usando o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-180">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="2ab6c-181">O `src` matriz de propriedade lista arquivos combinar, na ordem em que eles devem ser combinados.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-181">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="2ab6c-182">O `dest` propriedade atribui o caminho para o arquivo combinado que é produzido.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-182">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="2ab6c-183">O `all` propriedade no código acima é o nome de um destino.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-183">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="2ab6c-184">Destinos são usados em algumas tarefas do Grunt para permitir que vários ambientes de compilação.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-184">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="2ab6c-185">Você pode exibir os destinos internos usando o IntelliSense ou atribuir seu próprio.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-185">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="2ab6c-186">Adicionar o `jshint` de tarefas usando o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-186">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="2ab6c-187">O jshint `code-quality` utilitário é executado em relação a todos os arquivos JavaScript encontrado na *temp* directory.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-187">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="2ab6c-188">A opção "-W069" é um erro gerado pela jshint quando o JavaScript usa a sintaxe para atribuir uma propriedade em vez de notação de ponto, ou seja, de colchete `Tastes["Sweet"]` em vez de `Tastes.Sweet`.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-188">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="2ab6c-189">A opção desliga o aviso para permitir que o restante do processo para continuar.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-189">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="2ab6c-190">Adicionar o `uglify` de tarefas usando o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-190">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="2ab6c-191">A tarefa minimiza a *combined.js* arquivo encontrado no diretório temp e cria o arquivo de resultado no seguindo a convenção de nomenclatura padrão wwwroot/lib  *\<nome do arquivo\>. Min*.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-191">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="2ab6c-192">Sob a chamada para `grunt.loadNpmTasks` que carrega `grunt-contrib-clean`, incluir a mesma chamada para jshint, concat e tarefa uglify usando o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-192">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="2ab6c-193">Salve *gruntfile*.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-193">Save *Gruntfile.js*.</span></span> <span data-ttu-id="2ab6c-194">O arquivo deve ser algo parecido com o exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-194">The file should look something like the example below.</span></span>

    ![exemplo de arquivo completo do grunt](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="2ab6c-196">Observe que o **Task Runner Explorer** lista de tarefas inclui `clean`, `concat`, `jshint` e `uglify` tarefas.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-196">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="2ab6c-197">Executar cada tarefa na ordem e observar os resultados no **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-197">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="2ab6c-198">Cada tarefa deve ser executado sem erros.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-198">Each task should run without errors.</span></span>

    ![Gerenciador de executor executar cada tarefa](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="2ab6c-200">A tarefa concat cria um novo *combined.js* de arquivo e o coloca no diretório temporário.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-200">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="2ab6c-201">O `jshint` tarefa é executada apenas e não produz a saída.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-201">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="2ab6c-202">O `uglify` tarefa cria uma nova *combined.min.js* do arquivo e coloca-o na *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-202">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="2ab6c-203">Após a conclusão, a solução deve ser algo parecido com a captura de tela abaixo:</span><span class="sxs-lookup"><span data-stu-id="2ab6c-203">On completion, the solution should look something like the screenshot below:</span></span>

    ![Depois que todas as tarefas de Gerenciador de soluções](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="2ab6c-205">Para obter mais informações sobre as opções para cada pacote, visite [ https://www.npmjs.com/ ](https://www.npmjs.com/) e pesquisa o nome do pacote na caixa de pesquisa na página principal.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-205">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="2ab6c-206">Por exemplo, você pode procurar o pacote grunt-contrib-clean para obter um link de documentação que explica todos os seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-206">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="2ab6c-207">Todos juntos agora</span><span class="sxs-lookup"><span data-stu-id="2ab6c-207">All together now</span></span>

<span data-ttu-id="2ab6c-208">Usar o Grunt `registerTask()` método para executar uma série de tarefas em uma sequência específica.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-208">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="2ab6c-209">Por exemplo, para executar o exemplo etapas acima na ordem limpa -> concat -> jshint -> tarefa uglify, adicione o código a seguir para o módulo.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-209">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="2ab6c-210">O código deve ser adicionado ao mesmo nível que as chamadas loadNpmTasks(), fora initConfig.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-210">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="2ab6c-211">A nova tarefa aparece no Gerenciador do executor de tarefas em tarefas de Alias.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-211">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="2ab6c-212">Pode-se com o botão direito e executá-lo exatamente como faria com outras tarefas.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-212">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="2ab6c-213">O `all` tarefa será executada `clean`, `concat`, `jshint` e `uglify`, na ordem.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-213">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![tarefas do grunt de alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="2ab6c-215">Monitorando alterações</span><span class="sxs-lookup"><span data-stu-id="2ab6c-215">Watching for changes</span></span>

<span data-ttu-id="2ab6c-216">Um `watch` tarefa fica de olho em arquivos e diretórios.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-216">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="2ab6c-217">A inspeção dispara tarefas automaticamente se detectar alterações.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-217">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="2ab6c-218">Adicione o código abaixo ao initConfig observar alterações para \*arquivos. js no diretório do TypeScript.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-218">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="2ab6c-219">Se um arquivo JavaScript for alterado, `watch` executará o `all` tarefa.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-219">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="2ab6c-220">Adicione uma chamada para `loadNpmTasks()` para mostrar o `watch` tarefa em Task Runner Explorer.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-220">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="2ab6c-221">A tarefa de inspeção do Task Runner Explorer com o botão direito e selecione Executar no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-221">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="2ab6c-222">A janela de comando que mostra a tarefa de inspeção em execução exibirá um "Aguardando..." Mensagem.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-222">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="2ab6c-223">Abra um dos arquivos TypeScript, adicione um espaço e, em seguida, salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-223">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="2ab6c-224">Isso disparar a tarefa de inspeção e disparar as outras tarefas executadas em ordem.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-224">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="2ab6c-225">Captura de tela abaixo mostra uma exemplo de execução.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-225">The screenshot below shows a sample run.</span></span>

![executar tarefas de saída](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="2ab6c-227">Associação a eventos do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2ab6c-227">Binding to Visual Studio events</span></span>

<span data-ttu-id="2ab6c-228">A menos que você deseja iniciar manualmente suas tarefas toda vez que você trabalha no Visual Studio, você pode vincular tarefas a serem **antes da compilação**, **depois de compilar**, **Clean**, e  **Projeto aberto** eventos.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-228">Unless you want to manually start your tasks every time you work in Visual Studio, you can bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="2ab6c-229">Vamos associar `watch` para que ele seja executado sempre que o Visual Studio abre.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-229">Let’s bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="2ab6c-230">No Gerenciador de executor, a tarefa de inspeção com o botão direito e selecione **associações > Abrir projeto** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-230">In Task Runner Explorer, right-click the watch task and select **Bindings > Project Open** from the context menu.</span></span>

![associar uma tarefa para a abertura de projeto](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="2ab6c-232">Descarregue e recarregue o projeto.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-232">Unload and reload the project.</span></span> <span data-ttu-id="2ab6c-233">Quando o projeto for carregado novamente, a tarefa de inspeção iniciará a execução automática.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-233">When the project loads again, the watch task will start running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="2ab6c-234">Resumo</span><span class="sxs-lookup"><span data-stu-id="2ab6c-234">Summary</span></span>

<span data-ttu-id="2ab6c-235">Grunt é um executor de tarefas poderosa que pode ser usado para automatizar a maioria das tarefas de build do cliente.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-235">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="2ab6c-236">Grunt aproveita o NPM para fornecer seus pacotes, recursos e ferramentas de integração com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-236">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="2ab6c-237">Gerenciador de executor de tarefas do Visual Studio detecta alterações nos arquivos de configuração e fornece uma interface conveniente para executar tarefas, exibir tarefas em execução e vincular tarefas a eventos do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2ab6c-237">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
