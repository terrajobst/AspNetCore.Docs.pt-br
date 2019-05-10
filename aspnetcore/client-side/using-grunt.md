---
title: Usar o Grunt no ASP.NET Core
author: rick-anderson
description: ''
ms.author: riande
ms.date: 10/14/2016
uid: client-side/using-grunt
ms.openlocfilehash: fc912974fb6ed3c65bb46a7d616d9e531587d946
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894633"
---
# <a name="use-grunt-in-aspnet-core"></a>Usar o Grunt no ASP.NET Core

Por [Noel arroz](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)

Grunt é um executor de tarefas JavaScript que automatiza a minimização de script, compilação TypeScript, ferramentas de "lint" qualidade do código, processadores de pré-lançamento do CSS e praticamente qualquer tarefa repetitiva que precisa fazer para dar suporte ao desenvolvimento de cliente. Grunt é totalmente suportado no Visual Studio, embora os modelos de projeto do ASP.NET usam o Gulp por padrão (consulte [usar o Gulp](using-gulp.md)).

Este exemplo usa um projeto vazio do ASP.NET Core como ponto de partida, para mostrar como automatizar o processo de compilação do cliente do zero.

O exemplo concluído limpa o diretório de implantação de destino, combina arquivos JavaScript, verificações de qualidade do código, condensa o conteúdo do arquivo JavaScript e implanta na raiz do seu aplicativo web. Usaremos os seguintes pacotes:

* **grunt**: O pacote de executor de tarefa do Grunt.

* **grunt-contrib-clean**: Um plug-in que remove os arquivos ou diretórios.

* **grunt-contrib-jshint**: Um plug-in que analisa a qualidade do código JavaScript.

* **grunt-contrib-concat**: Um plug-in que une os arquivos em um único arquivo.

* **grunt-contrib-uglify**: Um plug-in que minimiza o JavaScript para reduzir o tamanho.

* **grunt-contrib-watch**: Um plug-in que observa a atividade de arquivos.

## <a name="preparing-the-application"></a>Preparando o aplicativo

Para começar, configure um novo aplicativo web vazio e adicionar arquivos de exemplo do TypeScript. Arquivos TypeScript são automaticamente compilados em JavaScript usando as configurações do Visual Studio padrão e serão nosso matéria-prima para processar usando o Grunt.

1. No Visual Studio, crie um novo `ASP.NET Web Application`.

2. No **novo projeto ASP.NET** caixa de diálogo, selecione o ASP.NET Core **vazia** modelo e clique no botão Okey.

3. No Gerenciador de soluções, examine a estrutura do projeto. O `\src` pasta inclui vazia `wwwroot` e `Dependencies` nós.

    ![solução de web vazio](using-grunt/_static/grunt-solution-explorer.png)

4. Adicionar uma nova pasta chamada `TypeScript` no diretório do projeto.

5. Antes de adicionar todos os arquivos, certifique-se de que o Visual Studio tem a opção ' Compilar ao salvar ' para arquivos TypeScript check. Navegue até **ferramentas** > **opções** > **Editor de texto** > **Typescript**  >  **Projeto**:

    ![Opções de configuração de compilação automática de arquivos TypeScript](using-grunt/_static/typescript-options.png)

6. Clique com botão direito do `TypeScript` diretório e selecione **Adicionar > Novo Item** no menu de contexto. Selecione o **arquivo JavaScript** de item e nomeie o arquivo *Tastes.ts* (Observe o \*extensão. TS). Copie a linha de código do TypeScript abaixo para o arquivo (quando você salva, um novo *Tastes.js* arquivo aparecerá com a fonte do JavaScript).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Adicionar um segundo arquivo para o **TypeScript** diretório e nomeie-o `Food.ts`. Copie o código a seguir para o arquivo.

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

## <a name="configuring-npm"></a>Configuração do NPM

Em seguida, configure o NPM para baixar o grunt e tarefas do grunt.

1. No Gerenciador de soluções, clique com botão direito no projeto e selecione **Adicionar > Novo Item** no menu de contexto. Selecione o **arquivo de configuração do NPM** item, deixe o nome padrão, *Package. JSON*e clique no **Add** botão.

2. No *Package. JSON* do arquivo, dentro de `devDependencies` chaves de objeto, digite "grunt". Selecione `grunt` do Intellisense lista e pressione a tecla Enter. Visual Studio será colocada entre aspas no nome do pacote grunt e adicione dois-pontos. À direita dos dois pontos, selecione a versão estável mais recente do pacote na parte superior da lista do Intellisense (pressione `Ctrl-Space` se o Intellisense não aparece).

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > Usa NPM [controle de versão semântico](http://semver.org/) para organizar as dependências. Controle de versão semântico, também conhecido como SemVer, identifica os pacotes com o esquema de numeração \<principal >.\< secundária >. \<patch >. IntelliSense simplifica o controle de versão semântico, mostrando apenas algumas opções comuns. O item superior na lista do Intellisense (0.4.5 no exemplo acima) é considerado a versão estável mais recente do pacote. O símbolo de acento circunflexo (^) corresponde à versão principal mais recente e o til (~) corresponde a versão secundária mais recente. Consulte a [referência do analisador de versão do NPM semver](https://www.npmjs.com/package/semver) como um guia para a expressividade completa que fornece SemVer.

3. Adicionar mais dependências ao carregar o grunt-contrib -\* pacotes de *limpa*, *jshint*, *concat*, *tarefa uglify*e *inspeção* conforme mostrado no exemplo a seguir. As versões não precisam coincidir com o exemplo.

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

4. Salvar a *Package. JSON* arquivo.

Baixarão os pacotes para cada item devDependencies, juntamente com todos os arquivos que requer que cada pacote. Você pode encontrar os arquivos de pacote a `node_modules` diretório, permitindo a **Show All Files** botão no Gerenciador de soluções.

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> Se necessário, você pode restaurar manualmente as dependências no Gerenciador de soluções clicando em `Dependencies\NPM` e selecionando os **restaurar pacotes** opção de menu.

![Restaurar pacotes](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Configurando o Grunt

Grunt é configurado usando um manifesto chamado *gruntfile* que define, carrega e registra as tarefas que podem ser executadas manualmente ou configuradas para ser executado automaticamente com base em eventos no Visual Studio.

1. Clique com botão direito no projeto e selecione **Adicionar > Novo Item**. Selecione o **arquivo de configuração do Grunt** opção, deixe o nome padrão, *gruntfile*e clique no **Add** botão.

   O código inicial inclui uma definição de módulo e o `grunt.initConfig()` método. O `initConfig()` é usado para definir opções para cada pacote, e o restante do módulo carregará e registrar tarefas.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

2. Dentro de `initConfig()` método, adicionar opções para o `clean` conforme mostrado no exemplo de tarefa *gruntfile* abaixo. A tarefa limpar aceita uma matriz de cadeias de caracteres de diretório. Essa tarefa remove os arquivos do wwwroot/lib e remove o diretório temp/inteiro.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

3. Abaixo do método initConfig(), adicione uma chamada para `grunt.loadNpmTasks()`. Isso tornará a tarefa executável do Visual Studio.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

4. Salve *gruntfile*. O arquivo deve ser semelhante a captura de tela abaixo.

    ![gruntfile inicial](using-grunt/_static/gruntfile-js-initial.png)

5. Clique com botão direito *gruntfile* e selecione **Task Runner Explorer** no menu de contexto. A janela do Task Runner Explorer será aberta.

    ![menu do Gerenciador de executor de tarefas](using-grunt/_static/task-runner-explorer-menu.png)

6. Verifique `clean` mostra sob **tarefas** no Gerenciador de executor de tarefas.

    ![lista de tarefas do Gerenciador de executor de tarefas](using-grunt/_static/task-runner-explorer-tasks.png)

7. A tarefa limpar com o botão direito e selecione **executar** no menu de contexto. Uma janela de comando exibe o progresso da tarefa.

    ![tarefa de limpeza runner explorer executar tarefa](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Não há nenhum arquivo ou diretório para limpar ainda. Se desejar, você pode criá-las manualmente no Gerenciador de soluções e, em seguida, executar a tarefa de limpeza como um teste.

8. No método initConfig(), adicione uma entrada para `concat` usando o código a seguir.

    O `src` matriz de propriedade lista arquivos combinar, na ordem em que eles devem ser combinados. O `dest` propriedade atribui o caminho para o arquivo combinado que é produzido.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > O `all` propriedade no código acima é o nome de um destino. Destinos são usados em algumas tarefas do Grunt para permitir que vários ambientes de compilação. Você pode exibir os destinos internos usando o Intellisense ou atribuir seu próprio.

9. Adicionar o `jshint` de tarefas usando o código a seguir.

    O utilitário de qualidade do código jshint é executado em todos os arquivos JavaScript encontrado no diretório temporário.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > A opção "-W069" é um erro gerado pela jshint quando o JavaScript usa a sintaxe para atribuir uma propriedade em vez de notação de ponto, ou seja, de colchete `Tastes["Sweet"]` em vez de `Tastes.Sweet`. A opção desliga o aviso para permitir que o restante do processo para continuar.

10. Adicionar o `uglify` de tarefas usando o código a seguir.

    A tarefa minimiza a *combined.js* arquivo encontrado no diretório temp e cria o arquivo de resultado no seguindo a convenção de nomenclatura padrão wwwroot/lib  *\<nome do arquivo\>. Min*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

11. Em grunt.loadNpmTasks() a chamada que carrega o grunt-contrib-clean, inclua a mesma chamada para jshint, concat e tarefa uglify usando o código a seguir.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

12. Salve *gruntfile*. O arquivo deve ser algo parecido com o exemplo a seguir.

    ![exemplo de arquivo completo do grunt](using-grunt/_static/gruntfile-js-complete.png)

13. Observe que a lista de tarefas do Gerenciador de executor de tarefas inclui `clean`, `concat`, `jshint` e `uglify` tarefas. Executar cada tarefa na ordem e observar os resultados no Gerenciador de soluções. Cada tarefa deve ser executado sem erros.

    ![Gerenciador de executor executar cada tarefa](using-grunt/_static/task-runner-explorer-run-each-task.png)

    A tarefa concat cria um novo *combined.js* de arquivo e o coloca no diretório temporário. A tarefa jshint simplesmente é executado e não produz a saída. A tarefa uglify cria um novo *combined.min.js* de arquivo e o coloca na wwwroot/lib. Após a conclusão, a solução deve ser algo parecido com a captura de tela abaixo:

    ![Depois que todas as tarefas de Gerenciador de soluções](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Para obter mais informações sobre as opções para cada pacote, visite [ https://www.npmjs.com/ ](https://www.npmjs.com/) e pesquisa o nome do pacote na caixa de pesquisa na página principal. Por exemplo, você pode procurar o pacote grunt-contrib-clean para obter um link de documentação que explica todos os seus parâmetros.

### <a name="all-together-now"></a>Todos juntos agora

Usar o Grunt `registerTask()` método para executar uma série de tarefas em uma sequência específica. Por exemplo, para executar o exemplo etapas acima na ordem limpa -> concat -> jshint -> tarefa uglify, adicione o código a seguir para o módulo. O código deve ser adicionado ao mesmo nível que as chamadas loadNpmTasks(), fora initConfig.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

A nova tarefa aparece no Gerenciador do executor de tarefas em tarefas de Alias. Pode-se com o botão direito e executá-lo exatamente como faria com outras tarefas. O `all` tarefa será executada `clean`, `concat`, `jshint` e `uglify`, na ordem.

![tarefas do grunt de alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Monitorando alterações

Um `watch` tarefa fica de olho em arquivos e diretórios. A inspeção dispara tarefas automaticamente se detectar alterações. Adicione o código abaixo ao initConfig observar alterações para \*arquivos. js no diretório do TypeScript. Se um arquivo JavaScript for alterado, `watch` executará o `all` tarefa.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Adicione uma chamada para `loadNpmTasks()` para mostrar o `watch` tarefa em Task Runner Explorer.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

A tarefa de inspeção do Task Runner Explorer com o botão direito e selecione Executar no menu de contexto. A janela de comando que mostra a tarefa de inspeção em execução exibirá um "Aguardando..." Mensagem. Abra um dos arquivos TypeScript, adicione um espaço e, em seguida, salve o arquivo. Isso disparar a tarefa de inspeção e disparar as outras tarefas executadas em ordem. Captura de tela abaixo mostra uma exemplo de execução.

![executar tarefas de saída](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Associação a eventos do Visual Studio

A menos que você deseja iniciar manualmente suas tarefas toda vez que você trabalha no Visual Studio, você pode vincular tarefas a serem **antes da compilação**, **depois de compilar**, **Clean**, e  **Projeto aberto** eventos.

Vamos associar `watch` para que ele seja executado sempre que o Visual Studio abre. No Gerenciador de executor, a tarefa de inspeção com o botão direito e selecione **associações > Abrir projeto** no menu de contexto.

![associar uma tarefa para a abertura de projeto](using-grunt/_static/bindings-project-open.png)

Descarregue e recarregue o projeto. Quando o projeto for carregado novamente, a tarefa de inspeção iniciará a execução automática.

## <a name="summary"></a>Resumo

Grunt é um executor de tarefas poderosa que pode ser usado para automatizar a maioria das tarefas de build do cliente. Grunt aproveita o NPM para fornecer seus pacotes, recursos e ferramentas de integração com o Visual Studio. Gerenciador de executor de tarefas do Visual Studio detecta alterações nos arquivos de configuração e fornece uma interface conveniente para executar tarefas, exibir tarefas em execução e vincular tarefas a eventos do Visual Studio.

## <a name="additional-resources"></a>Recursos adicionais

* [Usar o Gulp](using-gulp.md)
