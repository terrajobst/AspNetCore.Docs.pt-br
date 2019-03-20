---
title: Use o Gulp no ASP.NET Core
author: rick-anderson
description: Saiba como usar o Gulp no ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 10/04/2018
uid: client-side/using-gulp
ms.openlocfilehash: 9f6d03a1e8a81bceca15cb1e1aa664c22c31e1d3
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209866"
---
# <a name="use-gulp-in-aspnet-core"></a>Use o Gulp no ASP.NET Core

By [Scott Addie](https://scottaddie.com), [Shayne Boyer](https://twitter.com/spboyer), and [David Pine](https://twitter.com/davidpine7)

Em um aplicativo web moderno típico, o processo de compilação pode:

* Agrupar e minificar arquivos JavaScript e CSS.
* Execute ferramentas para chamar as tarefas de empacotamento e minimização antes de cada compilação.
* Arquivos de compilar menos ou SASS para CSS.
* Compile arquivos CoffeeScript ou TypeScript para JavaScript.

Um *executor de tarefas* é uma ferramenta que automatiza a essas tarefas de rotina de desenvolvimento e muito mais. Visual Studio fornece suporte interno para dois executores de tarefas de baseados em JavaScript populares: [Gulp](https://gulpjs.com/) e [Grunt](using-grunt.md).

## <a name="gulp"></a>Gulp

O gulp é baseado em JavaScript streaming build Kit de ferramentas para o código do lado do cliente. Normalmente, ele é usado para transmitir arquivos do lado do cliente por meio de uma série de processos quando um evento específico é disparado em um ambiente de compilação. Por exemplo, o Gulp pode ser usado para automatizar [agrupamento e minificação](bundling-and-minification.md) ou a limpeza de um ambiente de desenvolvimento antes de uma nova compilação.

Um conjunto de tarefas do Gulp é definido em *gulpfile. js*. O seguinte JavaScript inclui módulos de Gulp e especifica os caminhos de arquivo a ser referenciado de tarefas disponível em breve:

```javascript
/// <binding Clean='clean' />
"use strict";

var gulp = require("gulp"),
    rimraf = require("rimraf"),
    concat = require("gulp-concat"),
    cssmin = require("gulp-cssmin"),
    uglify = require("gulp-uglify");

var paths = {
  webroot: "./wwwroot/"
};

paths.js = paths.webroot + "js/**/*.js";
paths.minJs = paths.webroot + "js/**/*.min.js";
paths.css = paths.webroot + "css/**/*.css";
paths.minCss = paths.webroot + "css/**/*.min.css";
paths.concatJsDest = paths.webroot + "js/site.min.js";
paths.concatCssDest = paths.webroot + "css/site.min.css";
```

O código acima Especifica quais módulos de nó são necessários. O `require` função importa cada módulo para que as tarefas dependentes podem utilizar seus recursos. Cada um dos módulos importados é atribuída a uma variável. Os módulos podem ser localizados por nome ou caminho. Neste exemplo, os módulos denominado `gulp`, `rimraf`, `gulp-concat`, `gulp-cssmin`, e `gulp-uglify` são recuperados por nome. Além disso, uma série de caminhos são criados para que os locais dos arquivos CSS e JavaScript podem ser reutilizados e referenciados dentro das tarefas. A tabela a seguir fornece descrições dos módulos do incluídos no *gulpfile. js*.

| Nome do módulo | Descrição |
| ----------- | ----------- |
| gulp        | O sistema de compilação streaming Gulp. Para obter mais informações, consulte [gulp](https://www.npmjs.com/package/gulp). |
| rimraf      | Um módulo de exclusão do nó. Para obter mais informações, consulte [rimraf](https://www.npmjs.com/package/rimraf). |
| gulp-concat | Um módulo que concatena arquivos com base em caractere de nova linha do sistema operacional. Para obter mais informações, consulte [gulp-concat](https://www.npmjs.com/package/gulp-concat). |
| gulp-cssmin | Um módulo que minimiza os arquivos CSS. Para obter mais informações, consulte [gulp cssmin](https://www.npmjs.com/package/gulp-cssmin). |
| gulp-uglify | Um módulo que minimiza *. js* arquivos. Para obter mais informações, consulte [tarefa uglify gulp](https://www.npmjs.com/package/gulp-uglify). |

Depois que o requisito os módulos são importados, as tarefas podem ser especificadas. Aqui, há seis tarefas registrado, representado pelo código a seguir:

```javascript
gulp.task("clean:js", done => rimraf(paths.concatJsDest, done));
gulp.task("clean:css", done => rimraf(paths.concatCssDest, done));
gulp.task("clean", gulp.series(["clean:js", "clean:css"]));

gulp.task("min:js", () => {
  return gulp.src([paths.js, "!" + paths.minJs], { base: "." })
    .pipe(concat(paths.concatJsDest))
    .pipe(uglify())
    .pipe(gulp.dest("."));
});

gulp.task("min:css", () => {
  return gulp.src([paths.css, "!" + paths.minCss])
    .pipe(concat(paths.concatCssDest))
    .pipe(cssmin())
    .pipe(gulp.dest("."));
});

gulp.task("min", gulp.series(["min:js", "min:css"]));

// A 'default' task is required by Gulp v4
gulp.task("default", gulp.series(["min"]));
```

---

A tabela a seguir fornece uma explicação sobre as tarefas especificadas no código acima:

|Nome da Tarefa|Descrição|
|--- |--- |
|clean:js|Uma tarefa que usa o módulo de exclusão do nó rimraf para remover a versão reduzida do arquivo js.|
|clean:css|Uma tarefa que usa o módulo de exclusão do nó rimraf para remover a versão reduzida do arquivo site CSS.|
|Limpar|Uma tarefa que chama o `clean:js` tarefa, seguida de `clean:css` tarefa.|
|min:js|Uma tarefa que minimiza e concatena todos os arquivos. js na pasta js. A. Min arquivos são excluídos.|
|min:css|Uma tarefa que minimiza e concatena todos os arquivos. CSS dentro da pasta de css. A. min.css arquivos são excluídos.|
|min|Uma tarefa que chama o `min:js` tarefa, seguida de `min:css` tarefa.|

## <a name="running-default-tasks"></a>Execução de tarefas padrão

Se você já não tiver criado um novo aplicativo Web, crie um novo projeto de aplicativo Web ASP.NET no Visual Studio.

1. Abra o *Package. JSON* arquivo (Adicionar se não existe) e adicione o seguinte.

    ```json
    {
      "devDependencies": {
        "gulp": "^4.0.0",
        "gulp-concat": "2.6.1",
        "gulp-cssmin": "0.2.0",
        "gulp-uglify": "3.0.0",
        "rimraf": "2.6.1"
      }
    }
    ```

2. Adicionar um novo arquivo JavaScript ao seu projeto e denomine *gulpfile. js*, em seguida, copie o código a seguir.

    ```javascript
    /// <binding Clean='clean' />
    "use strict";

    const gulp = require("gulp"),
          rimraf = require("rimraf"),
          concat = require("gulp-concat"),
          cssmin = require("gulp-cssmin"),
          uglify = require("gulp-uglify");

    const paths = {
      webroot: "./wwwroot/"
    };

    paths.js = paths.webroot + "js/**/*.js";
    paths.minJs = paths.webroot + "js/**/*.min.js";
    paths.css = paths.webroot + "css/**/*.css";
    paths.minCss = paths.webroot + "css/**/*.min.css";
    paths.concatJsDest = paths.webroot + "js/site.min.js";
    paths.concatCssDest = paths.webroot + "css/site.min.css";

    gulp.task("clean:js", done => rimraf(paths.concatJsDest, done));
    gulp.task("clean:css", done => rimraf(paths.concatCssDest, done));
    gulp.task("clean", gulp.series(["clean:js", "clean:css"]));

    gulp.task("min:js", () => {
      return gulp.src([paths.js, "!" + paths.minJs], { base: "." })
      .pipe(concat(paths.concatJsDest))
      .pipe(uglify())
      .pipe(gulp.dest("."));
    });

    gulp.task("min:css", () => {
      return gulp.src([paths.css, "!" + paths.minCss])
      .pipe(concat(paths.concatCssDest))
      .pipe(cssmin())
      .pipe(gulp.dest("."));
    });

    gulp.task("min", gulp.series(["min:js", "min:css"]));

    // A 'default' task is required by Gulp v4
    gulp.task("default", gulp.series(["min"]));
    ```

3. Na **Gerenciador de soluções**, clique com botão direito *gulpfile. js*e selecione **Task Runner Explorer**.

    ![Abra o Explorador do Executador de tarefas do Gerenciador de soluções](using-gulp/_static/02-SolutionExplorer-TaskRunnerExplorer.png)

    **Explorador do Executador de tarefas** mostra a lista de tarefas de Gulp. (Talvez você precise clicar o **Refresh** botão que aparece à esquerda do nome do projeto.)

    ![Task Runner Explorer](using-gulp/_static/03-TaskRunnerExplorer.png)

    > [!IMPORTANT]
    > O **Task Runner Explorer** item de menu de contexto será exibida apenas se *gulpfile. js* está no diretório do projeto raiz.

4. Sob **tarefas** na **Task Runner Explorer**, clique com botão direito **limpa**e selecione **executar** no menu pop-up.

    ![Tarefa de limpeza de Explorador do Executador de tarefas](using-gulp/_static/04-TaskRunner-clean.png)

    **Explorador do Executador de tarefas** criará uma nova guia chamada **limpa** e execute a tarefa limpar conforme definido na *gulpfile. js*.

5. Com o botão direito do **limpa** da tarefa e, em seguida, selecione **associações** > **antes da compilação**.

    ![Associação BeforeBuild do Task Runner Explorer](using-gulp/_static/05-TaskRunner-BeforeBuild.png)

    O **antes da compilação** associação configura a tarefa Limpar para serem executados automaticamente antes de cada compilação do projeto.

As associações que você configura o com **Task Runner Explorer** são armazenadas na forma de um comentário na parte superior da sua *gulpfile. js* e entrarão em vigor apenas no Visual Studio. Uma alternativa que não requer o Visual Studio é configurar a execução automática de tarefas de gulp em seu *. csproj* arquivo. Por exemplo, colocar isso no seu *. csproj* arquivo:

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
  <Exec Command="gulp clean" />
</Target>
```

Agora a tarefa de limpeza é executada quando você executar o projeto no Visual Studio ou de um prompt de comando usando o [execução dotnet](/dotnet/core/tools/dotnet-run) comando (executar `npm install` primeiro).

## <a name="defining-and-running-a-new-task"></a>Definir e executar uma nova tarefa

Para definir uma nova tarefa Gulp, modifique *gulpfile. js*.

1. Adicione o seguinte JavaScript ao final da *gulpfile. js*:

    ```javascript
    gulp.task('first', done => {
      console.log('first task! <-----');
      done(); // signal completion
    });
    ```

    Essa tarefa é denominada `first`, e ele simplesmente exibe uma cadeia de caracteres.

2. Salve *gulpfile. js*.

3. Na **Gerenciador de soluções**, clique com botão direito *gulpfile. js*e selecione *Task Runner Explorer*.

4. Na **Task Runner Explorer**, clique com botão direito **primeiro**e selecione **executar**.

    ![Execute a primeira tarefa do Task Runner Explorer](using-gulp/_static/06-TaskRunner-First.png)

    O texto de saída é exibido. Para obter exemplos com base em cenários comuns, consulte [receitas do Gulp](#gulp-recipes).

## <a name="defining-and-running-tasks-in-a-series"></a>Definir e executar tarefas em uma série

Quando você executa várias tarefas, as tarefas executadas simultaneamente por padrão. No entanto, se você precisar executar tarefas em uma ordem específica, você deve especificar quando cada tarefa é concluída, bem como quais tarefas dependem da conclusão de outra tarefa.

1. Para definir uma série de tarefas a serem executados em ordem, substitua a `first` que você adicionou acima na tarefa *gulpfile. js* com o seguinte:

    ```javascript
    gulp.task('series:first', done => {
      console.log('first task! <-----');
      done(); // signal completion
    });
    gulp.task('series:second', done => {
      console.log('second task! <-----');
        done(); // signal completion
    });

    gulp.task('series', gulp.series(['series:first', 'series:second']), () => { });

    // A 'default' task is required by Gulp v4
      gulp.task('default', gulp.series('series'));
    ```

    Agora você tem três tarefas: `series:first`, `series:second`, e `series`. O `series:second` tarefa inclui um segundo parâmetro que especifica uma matriz de tarefas para serem executados e concluídos antes do `series:second` tarefa será executada. Conforme especificado no código acima, somente o `series:first` tarefa deve ser concluída antes do `series:second` tarefa será executada.

2. Salve *gulpfile. js*.

3. Na **Gerenciador de soluções**, clique com botão direito *gulpfile. js* e selecione **Task Runner Explorer** se ainda não estiver aberto.

4. Na **Task Runner Explorer**, clique com botão direito **série** e selecione **executar**.

    ![Executar tarefa de série do Task Runner Explorer](using-gulp/_static/07-TaskRunner-Series.png)

## <a name="intellisense"></a>IntelliSense

IntelliSense fornece conclusão de código, descrições de parâmetro e outros recursos para aumentar a produtividade e diminuir a erros. As tarefas de gulp são escritas em JavaScript; Portanto, o IntelliSense pode fornecer assistência durante o desenvolvimento. Conforme você trabalha com JavaScript, o IntelliSense lista os objetos, funções, propriedades e parâmetros que estão disponíveis com base em seu contexto atual. Selecione uma opção de codificação na lista pop-up fornecida pelo IntelliSense para concluir o código.

![IntelliSense do gulp](using-gulp/_static/08-IntelliSense.png)

Para obter mais informações sobre o IntelliSense, consulte [JavaScript IntelliSense](/visualstudio/ide/javascript-intellisense).

## <a name="development-staging-and-production-environments"></a>Ambientes de desenvolvimento, preparo e produção

Quando o Gulp é usado para otimizar arquivos do lado do cliente para produção e preparo, os arquivos processados são salvos em um local de preparo e produção local. O *layout. cshtml* arquivo de usar o **ambiente** auxiliar para fornecer duas versões diferentes de arquivos CSS de marcação. Uma versão de arquivos CSS é para o desenvolvimento e a outra versão é otimizada para produção e preparo. No Visual Studio 2017, quando você altera a **ASPNETCORE_ENVIRONMENT** variável de ambiente `Production`, Visual Studio criará o aplicativo Web e um link para os arquivos CSS minimizados. A marcação a seguir mostra a **ambiente** que contém as marcações de link para os auxiliares de marca a `Development` CSS arquivos e o minificado `Staging, Production` arquivos CSS.

```html
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.2.0.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery"
            crossorigin="anonymous"
            integrity="sha384-K+ctZQ+LL8q6tP7I94W+qzQsfRV2a+AfHIi9k8z8l9ggpc8X+Ytst4yBo/hH+8Fk">
    </script>
    <script src="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/bootstrap.min.js"
            asp-fallback-src="~/lib/bootstrap/dist/js/bootstrap.min.js"
            asp-fallback-test="window.jQuery && window.jQuery.fn && window.jQuery.fn.modal"
            crossorigin="anonymous"
            integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa">
    </script>
    <script src="~/js/site.min.js" asp-append-version="true"></script>
</environment>
```

## <a name="switching-between-environments"></a>Alternância entre ambientes

Para alternar entre a compilação para ambientes diferentes, modifique a **ASPNETCORE_ENVIRONMENT** valor da variável de ambiente.

1. No **Task Runner Explorer**, verifique se que o **min** foi definida para executar tarefas **antes da compilação**.

2. Na **Gerenciador de soluções**, clique com botão direito no nome do projeto e selecione **propriedades**.

    A folha de propriedades para o aplicativo Web é exibida.

3. Clique na guia **Depurar**.

4. Definir o valor de **: ambiente de hospedagem** variável de ambiente `Production`.

5. Pressione **F5** para executar o aplicativo em um navegador.

6. Na janela do navegador, a página com o botão direito e selecione **Exibir código-fonte** para exibir o HTML da página.

    Observe que os links de folha de estilos apontam para os arquivos CSS minificados.

7. Feche o navegador para parar o aplicativo Web.

8. No Visual Studio, retorne para a folha de propriedades para o aplicativo Web e altere o **: ambiente de hospedagem** variável de ambiente de volta para `Development`.

9. Pressione **F5** para executar o aplicativo em um navegador novamente.

10. Na janela do navegador, a página com o botão direito e selecione **Exibir código-fonte** para ver o HTML da página.

    Observe que os links de folha de estilos apontam para as versões unminified dos arquivos CSS.

Para obter mais informações relacionadas aos ambientes no ASP.NET Core, consulte [usar vários ambientes](../fundamentals/environments.md).

## <a name="task-and-module-details"></a>Detalhes da tarefa e o módulo

Uma tarefa Gulp está registrada com um nome de função. É possível especificar dependências se outras tarefas devem ser executadas antes da tarefa atual. Funções adicionais permitem que você execute e assistir as tarefas de Gulp, bem como definir a origem (*src*) e de destino (*dest*) dos arquivos que está sendo modificados. Estas são as funções de API do Gulp primárias:

|Função gulp|Sintaxe|Descrição|
|---   |--- |--- |
|tarefa  |`gulp.task(name[, deps], fn) { }`|O `task` função cria uma tarefa. O `name` parâmetro define o nome da tarefa. O `deps` parâmetro contém uma matriz de tarefas a serem concluídas antes que essa tarefa é executada. O `fn` parâmetro representa uma função de retorno de chamada que executa as operações da tarefa.|
|Inspeção |`gulp.watch(glob [, opts], tasks) { }`|O `watch` function monitora arquivos e execuções de tarefas quando ocorre uma alteração de arquivo. O `glob` parâmetro é um `string` ou `array` que determina quais arquivos assistir. O `opts` parâmetro fornece monitoramento opções de arquivo adicional.|
|src   |`gulp.src(globs[, options]) { }`|O `src` função fornece os arquivos que correspondem os valores de glob. O `glob` parâmetro é um `string` ou `array` que determina quais arquivos para ler. O `options` parâmetro fornece opções de arquivo adicionais.|
|dest  |`gulp.dest(path[, options]) { }`|O `dest` função define um local para o qual os arquivos podem ser gravados. O `path` parâmetro é uma cadeia de caracteres ou uma função que determina a pasta de destino. O `options` parâmetro é um objeto que especifica as opções de pasta de saída.|

Para obter informações de referência de API Gulp, consulte [Gulp Docs API](https://gulpjs.org/API.html).

## <a name="gulp-recipes"></a>Receitas do gulp

A comunidade do Gulp fornece Gulp [receitas](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md). Essas receitas consistem em tarefas de Gulp para lidar com cenários comuns.

## <a name="additional-resources"></a>Recursos adicionais

* [Documentação do gulp](https://github.com/gulpjs/gulp/blob/master/docs/README.md)
* [Agrupamento e minificação no ASP.NET Core](bundling-and-minification.md)
* [Usar o Grunt no ASP.NET Core](using-grunt.md)
