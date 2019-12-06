---
title: Usar Grunt no ASP.NET Core
author: rick-anderson
description: Usar Grunt no ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879787"
---
# <a name="use-grunt-in-aspnet-core"></a>Usar Grunt no ASP.NET Core

Grunt é um executor de tarefa JavaScript que automatiza o script minificação, compilação TypeScript, ferramentas de "pano" de qualidade de código, pré-processadores de CSS e praticamente qualquer tarefa repetitiva que precise fazer para dar suporte ao desenvolvimento de clientes. O Grunt tem suporte total no Visual Studio.

Este exemplo usa um projeto de ASP.NET Core vazio como ponto de partida, para mostrar como automatizar o processo de criação do cliente do zero.

O exemplo concluído limpa o diretório de implantação de destino, combina arquivos JavaScript, verifica a qualidade do código, condensa o conteúdo do arquivo JavaScript e implanta na raiz do seu aplicativo Web. Usaremos os seguintes pacotes:

* **Grunt**: o pacote do executor de tarefa do Grunt.

* **Grunt-contrib-Clean**: um plug-in que remove arquivos ou diretórios.

* **Grunt-contrib-jshint**: um plug-in que revisa a qualidade do código JavaScript.

* **Grunt-contrib-Concat**: um plug-in que une arquivos em um único arquivo.

* **Grunt-contrib-uglify**: um plug-in que minifies JavaScript para reduzir o tamanho.

* **Grunt-contrib-Watch**: um plug-in que observa a atividade do arquivo.

## <a name="preparing-the-application"></a>Preparando o aplicativo

Para começar, configure um novo aplicativo Web vazio e adicione arquivos de exemplo do TypeScript. Os arquivos TypeScript são compilados automaticamente em JavaScript usando as configurações padrão do Visual Studio e serão nosso material bruto para processar usando Grunt.

1. No Visual Studio, crie um novo `ASP.NET Web Application`.

2. Na caixa de diálogo **novo projeto ASP.net** , selecione o modelo ASP.NET Core **vazio** e clique no botão OK.

3. Na Gerenciador de Soluções, examine a estrutura do projeto. A pasta `\src` inclui os nós `wwwroot` e `Dependencies` vazios.

    ![solução Web vazia](using-grunt/_static/grunt-solution-explorer.png)

4. Adicione uma nova pasta chamada `TypeScript` ao diretório do projeto.

5. Antes de adicionar qualquer arquivo, verifique se o Visual Studio tem a opção ' Compilar ao salvar ' para arquivos TypeScript verificados. Navegue até **ferramentas** > **Opções** > **Editor de texto** > **typescript** > **projeto**:

    ![opções de configuração da compilação automática de arquivos TypeScript](using-grunt/_static/typescript-options.png)

6. Clique com o botão direito do mouse no diretório `TypeScript` e selecione **adicionar > novo item** no menu de contexto. Selecione o item de **arquivo JavaScript** e nomeie o arquivo *preferências. TS* (Observe a extensão \*. TS). Copie a linha do código TypeScript abaixo para o arquivo (quando você salvar, um novo arquivo *preferências. js* será exibido com a origem do JavaScript).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Adicione um segundo arquivo ao diretório **TypeScript** e nomeie-o `Food.ts`. Copie o código abaixo para o arquivo.

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

## <a name="configuring-npm"></a>Configurando o NPM

Em seguida, configure NPM para baixar Grunt e Grunt-Tasks.

1. Na Gerenciador de Soluções, clique com o botão direito do mouse no projeto e selecione **adicionar > novo item** no menu de contexto. Selecione o item de **arquivo de configuração NPM** , deixe o nome padrão, *Package. JSON*e clique no botão **Adicionar** .

2. No arquivo *Package. JSON* , dentro do `devDependencies` chaves do objeto, digite "Grunt". Selecione `grunt` na lista do IntelliSense e pressione a tecla Enter. O Visual Studio irá citar o nome do pacote Grunt e adicionará dois-pontos. À direita dos dois-pontos, selecione a versão estável mais recente do pacote na parte superior da lista do IntelliSense (pressione `Ctrl-Space` se o IntelliSense não aparecer).

    ![Grunt IntelliSense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > O NPM usa o [controle de versão semântico](https://semver.org/) para organizar dependências. O controle de versão semântico, também conhecido como SemVer, identifica pacotes com o esquema de numeração \<> principal.\<> secundárias. >\<patch. O IntelliSense simplifica o controle de versão semântico mostrando apenas algumas opções comuns. O item superior na lista do IntelliSense (0.4.5 no exemplo acima) é considerado a versão estável mais recente do pacote. O símbolo de acento circunflexo (^) corresponde à versão principal mais recente e o til (~) corresponde à versão secundária mais recente. Consulte a [referência do analisador de versão do NPM semver](https://www.npmjs.com/package/semver) como um guia para o expressividade completo que o semver fornece.

3. Adicione mais dependências para carregar pacotes Grunt-contrib-\* para *Clean*, *jshint*, *concat*, *uglify*e *Watch* , conforme mostrado no exemplo a seguir. As versões não precisam corresponder ao exemplo.

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

4. Salve o arquivo *Package. JSON* .

Os pacotes para cada item de `devDependencies` serão baixados, juntamente com todos os arquivos que cada pacote exigir. Você pode encontrar os arquivos de pacote no diretório *node_modules* habilitando o botão **Mostrar todos os arquivos** em **Gerenciador de soluções**.

![node_modules Grunt](using-grunt/_static/node-modules.png)

> [!NOTE]
> Se necessário, você pode restaurar manualmente as dependências no **Gerenciador de soluções** clicando com o botão direito do mouse em `Dependencies\NPM` e selecionando a opção de menu **restaurar pacotes** .

![restaurar pacotes](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Configurando o Grunt

O Grunt é configurado usando um manifesto chamado *Gruntfile. js* que define, carrega e registra tarefas que podem ser executadas manualmente ou configuradas para serem executadas automaticamente com base em eventos no Visual Studio.

1. Clique com o botão direito do mouse no projeto e selecione **adicionar** > **novo item**. Selecione o modelo de item de **arquivo JavaScript** , altere o nome para *Gruntfile. js*e clique no botão **Adicionar** .

1. Adicione o código a seguir a *Gruntfile. js*. A função `initConfig` define opções para cada pacote e o restante do módulo carrega e registra as tarefas.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. Dentro da função `initConfig`, adicione opções para a tarefa `clean`, conforme mostrado no exemplo *Gruntfile. js* abaixo. A tarefa `clean` aceita uma matriz de cadeias de caracteres de diretório. Essa tarefa remove arquivos de *wwwroot/lib* e remove todo o diretório */Temp* .

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Abaixo da função `initConfig`, adicione uma chamada para `grunt.loadNpmTasks`. Isso fará com que a tarefa seja executável do Visual Studio.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. Salve *Gruntfile. js*. O arquivo deve ser semelhante à captura de tela abaixo.

    ![gruntfile inicial](using-grunt/_static/gruntfile-js-initial.png)

1. Clique com o botão direito do mouse em *Gruntfile. js* e selecione **Task Runner Explorer** no menu de contexto. A janela **Gerenciador de executores de tarefas** será aberta.

    ![menu do Gerenciador de executor de tarefas](using-grunt/_static/task-runner-explorer-menu.png)

1. Verifique se `clean` mostra em **tarefas** no **Task Runner Explorer**.

    ![lista de tarefas do Gerenciador de executor de tarefas](using-grunt/_static/task-runner-explorer-tasks.png)

1. Clique com o botão direito do mouse na tarefa limpar e selecione **executar** no menu de contexto. Uma janela de comando exibe o progresso da tarefa.

    ![Gerenciador do executor de tarefas executar limpar tarefa](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Não há arquivos ou diretórios para limpar ainda. Se desejar, você pode criá-los manualmente no Gerenciador de Soluções e executar a tarefa de limpeza como um teste.

1. Na função `initConfig`, adicione uma entrada para `concat` usando o código abaixo.

    A matriz de propriedades `src` lista os arquivos a serem combinados, na ordem em que devem ser combinados. A propriedade `dest` atribui o caminho para o arquivo combinado que é produzido.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > A propriedade `all` no código acima é o nome de um destino. Os destinos são usados em algumas tarefas do Grunt para permitir vários ambientes de compilação. Você pode exibir os destinos internos usando o IntelliSense ou atribuir o seu próprio.

1. Adicione a tarefa de `jshint` usando o código abaixo.

    O utilitário jshint `code-quality` é executado em todos os arquivos JavaScript encontrados no diretório *temporário* .

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > A opção "-W069" é um erro produzido por jshint quando o JavaScript usa a sintaxe de colchete para atribuir uma propriedade em vez de uma notação de ponto, ou seja, `Tastes["Sweet"]` em vez de `Tastes.Sweet`. A opção desativa o aviso para permitir que o restante do processo continue.

1. Adicione a tarefa de `uglify` usando o código abaixo.

    A tarefa minifies o arquivo *. js combinado* encontrado no diretório temporário e cria o arquivo de resultado em wwwroot/lib seguindo a Convenção de nomenclatura padrão *\<nome do arquivo\>. min. js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. Na chamada para `grunt.loadNpmTasks` que carrega `grunt-contrib-clean`, inclua a mesma chamada para jshint, Concat e uglify usando o código a seguir.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. Salve *Gruntfile. js*. O arquivo deve ser semelhante ao exemplo abaixo.

    ![exemplo de arquivo Grunt completo](using-grunt/_static/gruntfile-js-complete.png)

1. Observe que a lista tarefas do **Gerenciador de executores de tarefas** inclui `clean`, `concat`, `jshint` e `uglify` tarefas. Execute cada tarefa na ordem e observe os resultados em **Gerenciador de soluções**. Cada tarefa deve ser executada sem erros.

    ![Explorador do executor de tarefas executar cada tarefa](using-grunt/_static/task-runner-explorer-run-each-task.png)

    A tarefa Concat cria um novo arquivo *. js combinado* e o coloca no diretório temporário. A `jshint` tarefa simplesmente é executada e não produz a saída. A tarefa `uglify` cria um novo arquivo *. min. js combinado* e o coloca em *wwwroot/lib*. Ao concluir, a solução deve ser semelhante à captura de tela abaixo:

    ![Gerenciador de soluções após todas as tarefas](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Para obter mais informações sobre as opções para cada pacote, visite [https://www.npmjs.com/](https://www.npmjs.com/) e pesquise o nome do pacote na caixa de pesquisa na página principal. Por exemplo, você pode pesquisar o pacote Grunt-contrib-Clean para obter um link de documentação que explica todos os seus parâmetros.

### <a name="all-together-now"></a>Todos juntos agora

Use o método Grunt `registerTask()` para executar uma série de tarefas em uma sequência específica. Por exemplo, para executar as etapas de exemplo acima no pedido Clean-> Concat-> jshint-> uglify, adicione o código abaixo ao módulo. O código deve ser adicionado ao mesmo nível que as chamadas loadNpmTasks (), fora de initConfig.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

A nova tarefa aparece no Gerenciador do executor de tarefas em tarefas de alias. Você pode clicar com o botão direito do mouse e executá-lo da mesma forma que faria com outras tarefas. A tarefa `all` será executada `clean`, `concat`, `jshint` e `uglify`, na ordem.

![tarefas de Grunt de alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Monitorando alterações

Uma tarefa `watch` fica atento aos arquivos e diretórios. O Watch acionará as tarefas automaticamente se detectar alterações. Adicione o código abaixo a initConfig para observar alterações em arquivos \*. js no diretório TypeScript. Se um arquivo JavaScript for alterado, `watch` executará a tarefa `all`.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Adicione uma chamada para `loadNpmTasks()` para mostrar a tarefa de `watch` no Task Runner Explorer.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Clique com o botão direito do mouse na tarefa de inspeção no Gerenciador do executor de tarefa e selecione executar no menu de contexto. A janela de comando que mostra a tarefa de inspeção em execução exibirá um "aguardando..." Mensagem. Abra um dos arquivos TypeScript, adicione um espaço e, em seguida, salve o arquivo. Isso disparará a tarefa de inspeção e disparará as outras tarefas a serem executadas na ordem. A captura de tela abaixo mostra uma execução de exemplo.

![saída de tarefas em execução](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Associação a eventos do Visual Studio

A menos que você queira iniciar manualmente suas tarefas sempre que trabalhar no Visual Studio, associe tarefas a **antes da compilação**, **após a compilação**, **limpeza**e eventos **abertos do projeto** .

Associe `watch` para que ele seja executado toda vez que o Visual Studio for aberto. No Gerenciador do executor de tarefas, clique com o botão direito do mouse na tarefa Watch e selecione **associações** > **projeto aberto** no menu de contexto.

![associar uma tarefa à abertura do projeto](using-grunt/_static/bindings-project-open.png)

Descarregue e recarregue o projeto. Quando o projeto é carregado novamente, a tarefa de inspeção começa a ser executada automaticamente.

## <a name="summary"></a>Resumo

O Grunt é um executor de tarefas poderoso que pode ser usado para automatizar a maioria das tarefas de compilação do cliente. O Grunt aproveita o NPM para fornecer seus pacotes e recursos de integração de ferramentas com o Visual Studio. O Task Runner Explorer do Visual Studio detecta alterações nos arquivos de configuração e fornece uma interface conveniente para executar tarefas, exibir tarefas em execução e associar tarefas a eventos do Visual Studio.
