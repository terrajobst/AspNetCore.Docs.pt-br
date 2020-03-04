---
title: LibMan de uso com o ASP.NET Core no Visual Studio
author: scottaddie
description: Saiba como usar LibMan em um projeto do ASP.NET Core com o Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: ebfb405516d968bf5d5b8cff956a9892457027f2
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813466"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>LibMan de uso com o ASP.NET Core no Visual Studio

Por [Scott Addie](https://twitter.com/Scott_Addie)

O Visual Studio tem suporte interno para [LibMan](xref:client-side/libman/index) em projetos do ASP.NET Core, incluindo:

* Suporte para configurar e executar operações de restauração LibMan no build.
* Itens de menu para o disparo de operações de limpeza e LibMan restauração.
* Caixa de diálogo de pesquisa para localizar bibliotecas e adicionar os arquivos a um projeto.
* Suporte de edição para *libman.json*&mdash;o arquivo de manifesto LibMan.

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(como fazer o download)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Prerequisites

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com carga de trabalho **ASP.NET e desenvolvimento Web**

## <a name="add-library-files"></a>Adicione arquivos de biblioteca

Arquivos de biblioteca podem ser adicionados a um projeto ASP.NET Core de duas maneiras diferentes:

1. [Use a caixa de diálogo Adicionar biblioteca de cliente](#use-the-add-client-side-library-dialog)
1. [Configurar manualmente as entradas do arquivo de manifesto LibMan](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Use a caixa de diálogo Adicionar biblioteca de cliente

Siga estas etapas para instalar uma biblioteca de cliente:

* Na **Gerenciador de soluções**, clique na pasta de projeto no qual os arquivos devem ser adicionados. Escolher **adicione** > **biblioteca de cliente**. O **adicionar a biblioteca do lado do cliente** caixa de diálogo aparece:

  ![Adicionar caixa de diálogo de biblioteca do lado do cliente](_static/add-library-dialog.png)

* Selecione o provedor de biblioteca do **provedor** lista suspensa. CDNJS é o provedor padrão.
* Digite o nome da biblioteca para buscar na **biblioteca** caixa de texto. IntelliSense fornece uma lista de bibliotecas, começando com o texto fornecido.
* Selecione a biblioteca da lista do IntelliSense. Observe que o nome da biblioteca é sufixado com o `@` símbolo e a versão estável mais recente conhecido para o provedor selecionado.
* Decida quais arquivos a serem incluídos:
  * Selecione o **incluir todos os arquivos de biblioteca** botão de opção para incluir todos os arquivos da biblioteca.
  * Selecione o **escolher arquivos específicos** botão de opção para incluir um subconjunto dos arquivos da biblioteca. Quando o botão de opção é selecionado, a árvore do seletor de arquivos está habilitada. Marque as caixas à esquerda dos nomes de arquivo para baixar.
* Especifique a pasta de projeto para armazenar os arquivos a **local de destino** caixa de texto. Como uma recomendação, armazene cada biblioteca em uma pasta separada.

  Sugeridas **local de destino** pasta baseia-se no local a partir do qual a caixa de diálogo iniciado:

  * Se iniciado a partir da raiz do projeto:
    * *wwwroot/lib* será usado se *wwwroot* existe.
    * *lib* será usado se *wwwroot* não existe.
  * Se iniciado em uma pasta de projeto, o nome da pasta correspondente será usado.

  A sugestão de pasta é sufixada com o nome da biblioteca. A tabela a seguir ilustra as sugestões de pasta durante a instalação do jQuery em um projeto de páginas do Razor.
  
  |Inicie o local                           |Pasta sugerida      |
  |------------------------------------------|----------------------|
  |raiz do projeto (se *wwwroot* existe)        |*wwwroot/lib/jquery/* |
  |raiz do projeto (se *wwwroot* não existe) |*lib/jquery/*         |
  |*Páginas* pasta do projeto                 |*Pages/jquery/*       |

* Clique o **instale** botão para baixar os arquivos, de acordo com a configuração no *libman.json*.
* Examine a **Gerenciador de biblioteca** feed da **saída** janela para obter detalhes de instalação. Por exemplo:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>Configurar manualmente as entradas do arquivo de manifesto LibMan

Todas as operações de LibMan no Visual Studio são baseadas no conteúdo do manifesto de LibMan da raiz do projeto (*libman.json*). Você pode editar manualmente *libman.json* para configurar arquivos de biblioteca do projeto. O Visual Studio restaura todos os arquivos de biblioteca uma vez *libman.json* é salvo.

Para abrir *libman.json* para edição, existem as seguintes opções:

* Clique duas vezes o *libman.json* arquivo no **Gerenciador de soluções**.
* Clique com botão direito no projeto no **Gerenciador de soluções** e selecione **gerenciar bibliotecas de cliente**. **&#8224;**
* Selecione **gerenciar bibliotecas do lado do cliente** do Visual Studio **projeto** menu. **&#8224;**

**&#8224;** Se o *libman.json* arquivo ainda não existir na raiz do projeto, ele será criado com o conteúdo do modelo de item padrão.

Visual Studio oferece um rico JSON, suporte, como colorização de edição, formatação, IntelliSense e validação de esquema. Esquema JSON do manifesto LibMan é encontrada em [ https://json.schemastore.org/libman ](https://json.schemastore.org/libman).

Com o seguinte arquivo de manifesto, LibMan recupera arquivos de acordo com a configuração definida no `libraries` propriedade. Obter uma explicação dos literais de objeto definidos dentro `libraries` segue:

* Um subconjunto de [jQuery](https://jquery.com/) versão 3.3.1 é recuperado do provedor CDNJS. O subconjunto é definido na `files` propriedade&mdash;*jQuery*, *obtive*, e *jquery.min.map*. Os arquivos são colocados no projeto do *wwwroot/lib/jquery* pasta.
* Na íntegra [Bootstrap](https://getbootstrap.com/) versão 4.1.3 é recuperado e colocado em um *wwwroot/lib/bootstrap* pasta. O literal de objeto `provider` substituições de propriedades de `defaultProvider` valor da propriedade. LibMan recupera os arquivos de inicialização do provedor de unpkg.
* Um subconjunto de [Lodash](https://lodash.com/) foi aprovada por um corpo regulador dentro da organização. O *lodash.js* e *lodash.min.js* arquivos são recuperados do sistema de arquivos local no *c:\\temp\\lodash\\* . Os arquivos são copiados para o projeto *wwwroot/lib/lodash* pasta.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan só dá suporte a uma versão de cada biblioteca de cada provedor. O *libman.json* arquivo Falha na validação de esquema, se ele contiver duas bibliotecas com o mesmo nome de biblioteca para determinado provedor.

## <a name="restore-library-files"></a>Restaurar arquivos de biblioteca

Para restaurar os arquivos de biblioteca de dentro do Visual Studio, deve haver um válido *libman.json* arquivo na raiz do projeto. Arquivos restaurados são colocados no projeto no local especificado para cada biblioteca.

Arquivos de biblioteca podem ser restaurados em um projeto do ASP.NET Core de duas maneiras:

1. [Restaurar os arquivos durante a compilação](#restore-files-during-build)
1. [Restaurar os arquivos manualmente](#restore-files-manually)

### <a name="restore-files-during-build"></a>Restaurar os arquivos durante a compilação

LibMan pode restaurar os arquivos de biblioteca definidas como parte do processo de compilação. Por padrão, o *restauração no build* comportamento está desabilitado.

Para ativar e testar o comportamento de restauração no build:

* Clique com botão direito *libman.json* na **Gerenciador de soluções** e selecione **habilitar bibliotecas de cliente restaurar no Build** no menu de contexto.
* Clique o **Sim** botão quando for solicitado a instalar um pacote do NuGet. O [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) pacote do NuGet é adicionado ao projeto:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Compile o projeto para confirmar a restauração de arquivo LibMan ocorre. O `Microsoft.Web.LibraryManager.Build` pacote injeta um destino do MSBuild que executa LibMan durante a operação de compilação do projeto.
* Examine a **compilar** feed da **saída** janela para um log de atividades LibMan:

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

Quando o comportamento de restauração no build está habilitado, o *libman.json* menu de contexto exibe um **desabilitar bibliotecas de cliente restaurar no Build** opção. Esta opção remove o `Microsoft.Web.LibraryManager.Build` referência do arquivo de projeto de pacote. Consequentemente, as bibliotecas do lado do cliente não são restauradas em cada compilação.

Independentemente da configuração de restauração no build, você pode restaurar manualmente a qualquer momento do *libman.json* menu de contexto. Para obter mais informações, consulte [restaurar os arquivos manualmente](#restore-files-manually).

### <a name="restore-files-manually"></a>Restaurar os arquivos manualmente

Para restaurar manualmente os arquivos de biblioteca:

* Para todos os projetos na solução:
  * Clique com botão direito no nome da solução no **Gerenciador de soluções**.
  * Selecione o **bibliotecas de cliente restaurar** opção.
* Para um projeto específico:
  * Clique com botão direito do *libman.json* arquivo no **Gerenciador de soluções**.
  * Selecione o **bibliotecas de cliente restaurar** opção.

Enquanto a operação de restauração está em execução:

* O ícone do Centro de Status da tarefa (TSC) na barra de status do Visual Studio será animado e lerá *iniciada a operação de restauração*. Clicando no ícone abre uma dica de ferramenta listando as tarefas em segundo plano conhecidos.
* As mensagens serão enviadas à barra de status e o **Gerenciador de biblioteca** feed da **saída** janela. Por exemplo:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>Excluir arquivos de biblioteca

Para executar o *limpa* operação, que exclui os arquivos de biblioteca restaurados anteriormente no Visual Studio:

* Clique com botão direito do *libman.json* arquivo no **Gerenciador de soluções**.
* Selecione o **bibliotecas de cliente limpa** opção.

Para evitar a remoção não intencional de arquivos não seja de biblioteca, a operação de limpeza não exclui diretórios inteiros. Ela remove somente os arquivos que foram incluídos na restauração anterior.

Enquanto a operação de limpeza é executado:

* O ícone TSC na barra de status do Visual Studio será animado e lerá *operação de bibliotecas de cliente iniciada*. Clicando no ícone abre uma dica de ferramenta listando as tarefas em segundo plano conhecidos.
* As mensagens são enviadas para a barra de status e o **Gerenciador de biblioteca** feed da **saída** janela. Por exemplo:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

A operação de limpeza exclui somente os arquivos do projeto. Arquivos de biblioteca permanecem no cache para uma recuperação mais rápida em operações de restauração futuras. Para gerenciar arquivos de biblioteca armazenados no cache do computador local, use o [LibMan CLI](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Desinstalar os arquivos de biblioteca

Para desinstalar os arquivos de biblioteca:

* Abra *libman.json*.
* Posicione o cursor dentro correspondente `libraries` literal de objeto.
* Clique no ícone de lâmpada que aparece na margem esquerda e selecione **Uninstall \<nome_da_biblioteca > @\<library_version >** :

  ![Desinstalar a opção de menu de contexto de biblioteca](_static/uninstall-menu-option.png)

Como alternativa, você pode editar e salvar o manifesto LibMan manualmente (*libman.json*). O [operação de restauração](#restore-library-files) é executado quando o arquivo é salvo. Arquivos de biblioteca que não estão definidos na *libman.json* são removidas do projeto.

## <a name="update-library-version"></a>Atualizar a versão da biblioteca

Para verificar se há uma versão atualizada de biblioteca:

* Abra *libman.json*.
* Posicione o cursor dentro correspondente `libraries` literal de objeto.
* Clique no ícone de lâmpada que aparece na margem esquerda. Passe o mouse sobre **verificar se há atualizações**.

LibMan verifica se há uma versão mais recente do que a versão instalada da biblioteca. Podem ocorrer os seguintes resultados:

* Um **nenhuma atualização encontrada** mensagem será exibida se a versão mais recente já está instalada.
* A versão estável mais recente é exibida se não estiver instalada.

  ![Procure a opção de menu de contexto de atualizações](_static/update-menu-option.png)

* Se houver uma versão de pré-lançamento mais recente do que a versão instalada, a versão de pré-lançamento é exibida.

Para fazer o downgrade para uma versão mais antiga da biblioteca, edite manualmente o *libman.json* arquivo. Quando o arquivo é salvo, o LibMan [operação de restauração](#restore-library-files):

* Remove arquivos redundantes da versão anterior.
* Adiciona arquivos novos e atualizados da nova versão.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:client-side/libman/libman-cli>
* [Repositório do GitHub do LibMan](https://github.com/aspnet/LibraryManager)
