---
title: Usar o LibMan com ASP.NET Core no Visual Studio
author: scottaddie
description: Saiba como usar o LibMan em um projeto ASP.NET Core com o Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658308"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Usar o LibMan com ASP.NET Core no Visual Studio

Por [Scott Addie](https://twitter.com/Scott_Addie)

O Visual Studio tem suporte interno para [LibMan](xref:client-side/libman/index) em projetos ASP.NET Core, incluindo:

* Suporte para configurar e executar operações de restauração do LibMan no Build.
* Itens de menu para disparar operações de restauração LibMan e limpeza.
* Caixa de diálogo de pesquisa para localizar bibliotecas e adicionar os arquivos a um projeto.
* O suporte de edição para *Libman. json*&mdash;o arquivo de manifesto Libman.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a carga de trabalho de **desenvolvimento Web e do ASP.NET**

## <a name="add-library-files"></a>Adicionar arquivos de biblioteca

Os arquivos de biblioteca podem ser adicionados a um projeto ASP.NET Core de duas maneiras diferentes:

1. [Usar a caixa de diálogo Adicionar biblioteca do lado do cliente](#use-the-add-client-side-library-dialog)
1. [Configurar manualmente as entradas do arquivo de manifesto LibMan](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Usar a caixa de diálogo Adicionar biblioteca do lado do cliente

Siga estas etapas para instalar uma biblioteca do lado do cliente:

* Em **Gerenciador de soluções**, clique com o botão direito do mouse na pasta do projeto na qual os arquivos devem ser adicionados. Escolha **adicionar** > **biblioteca do lado do cliente**. A caixa de diálogo **Adicionar biblioteca do lado do cliente** é exibida:

  ![Caixa de diálogo Adicionar biblioteca do lado do cliente](_static/add-library-dialog.png)

* Selecione o provedor de biblioteca na lista suspensa **provedor** . CDNJS é o provedor padrão.
* Digite o nome da biblioteca a ser buscada na caixa de texto **biblioteca** . O IntelliSense fornece uma lista de bibliotecas que começam com o texto fornecido.
* Selecione a biblioteca na lista do IntelliSense. Observe que o nome da biblioteca é sufixado com o símbolo de `@` e a versão estável mais recente conhecida pelo provedor selecionado.
* Decida quais arquivos incluir:
  * Selecione o botão de opção **incluir todos os arquivos de biblioteca** para incluir todos os arquivos da biblioteca.
  * Selecione o botão de opção **escolher arquivos específicos** para incluir um subconjunto dos arquivos da biblioteca. Quando o botão de opção é selecionado, a árvore seletor de arquivo é habilitada. Marque as caixas à esquerda dos nomes de arquivo para download.
* Especifique a pasta do projeto para armazenar os arquivos na caixa de texto **local de destino** . Como recomendação, armazene cada biblioteca em uma pasta separada.

  A pasta de **local de destino** sugerida é baseada no local do qual a caixa de diálogo é iniciada:

  * Se for iniciado da raiz do projeto:
    * *wwwroot/lib* será usado se *wwwroot* existir.
    * *lib* será usado se *wwwroot* não existir.
  * Se for iniciado a partir de uma pasta de projeto, o nome de pasta correspondente será usado.

  A sugestão de pasta é sufixada com o nome da biblioteca. A tabela a seguir ilustra as sugestões de pasta ao instalar o jQuery em um projeto Razor Pages.
  
  |Local de inicialização                           |Pasta sugerida      |
  |------------------------------------------|----------------------|
  |raiz do projeto (se *wwwroot* existir)        |*wwwroot/lib/jQuery/* |
  |raiz do projeto (se *wwwroot* não existir) |*lib/jQuery/*         |
  |Pasta *páginas* no projeto                 |*Páginas/jQuery/*       |

* Clique no botão **instalar** para baixar os arquivos, de acordo com a configuração em *Libman. JSON*.
* Examine o feed do **Gerenciador de biblioteca** da janela **saída** para obter detalhes da instalação. Por exemplo:

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

Todas as operações de LibMan no Visual Studio se baseiam no conteúdo do manifesto LibMan da raiz do projeto (*LibMan. JSON*). Você pode editar manualmente *Libman. JSON* para configurar arquivos de biblioteca para o projeto. O Visual Studio restaura todos os arquivos de biblioteca quando o *Libman. JSON* é salvo.

Para abrir o *Libman. JSON* para edição, existem as seguintes opções:

* Clique duas vezes no arquivo *Libman. JSON* em **Gerenciador de soluções**.
* Clique com o botão direito do mouse no projeto no **Gerenciador de soluções** e selecione **gerenciar bibliotecas do lado do cliente**. **&#8224;**
* Selecione **gerenciar bibliotecas do lado do cliente** no menu do **projeto** do Visual Studio. **&#8224;**

**&#8224;** Se o arquivo *Libman. JSON* ainda não existir na raiz do projeto, ele será criado com o conteúdo do modelo de item padrão.

O Visual Studio oferece suporte avançado para edição de JSON, como colorização, formatação, IntelliSense e validação de esquema. O esquema JSON do manifesto LibMan é encontrado em [https://json.schemastore.org/libman](https://json.schemastore.org/libman).

Com o arquivo de manifesto a seguir, LibMan recupera arquivos de acordo com a configuração definida na propriedade `libraries`. Uma explicação dos literais de objeto definidos em `libraries` segue:

* Um subconjunto do [jQuery](https://jquery.com/) versão 3.3.1 é recuperado do provedor CDNJS. O subconjunto é definido na propriedade `files`&mdash;*jQuery. min. js*, *jQuery. js*e *jQuery. min. map*. Os arquivos são colocados na pasta *wwwroot/lib/jQuery* do projeto.
* A totalidade da versão de [Bootstrap](https://getbootstrap.com/) 4.1.3 é recuperada e colocada em uma pasta *wwwroot/lib/Bootstrap* . A propriedade `provider` do literal do objeto substitui o valor da propriedade `defaultProvider`. LibMan recupera os arquivos de inicialização do provedor unpkg.
* Um subconjunto de [Lodash](https://lodash.com/) foi aprovado por um corpo governado dentro da organização. Os arquivos *lodash. js* e *lodash. min. js* são recuperados do sistema de arquivos local em *C:\\Temp\\lodash\\* . Os arquivos são copiados para a pasta *wwwroot/lib/lodash* do projeto.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> O LibMan dá suporte apenas a uma versão de cada provedor de cada fornecedor. O arquivo *Libman. JSON* falhará na validação do esquema se ele contiver duas bibliotecas com o mesmo nome de biblioteca para um determinado provedor.

## <a name="restore-library-files"></a>Restaurar arquivos de biblioteca

Para restaurar arquivos de biblioteca de dentro do Visual Studio, deve haver um arquivo *Libman. JSON* válido na raiz do projeto. Os arquivos restaurados são colocados no projeto no local especificado para cada biblioteca.

Os arquivos de biblioteca podem ser restaurados em um projeto ASP.NET Core de duas maneiras:

1. [Restaurar arquivos durante a compilação](#restore-files-during-build)
1. [Restaurar arquivos manualmente](#restore-files-manually)

### <a name="restore-files-during-build"></a>Restaurar arquivos durante a compilação

LibMan pode restaurar os arquivos de biblioteca definidos como parte do processo de compilação. Por padrão, o comportamento de *restauração no Build* é desabilitado.

Para habilitar e testar o comportamento de Restore-on-Build:

* Clique com o botão direito do mouse em *Libman. JSON* em **Gerenciador de soluções** e selecione **habilitar restaurar bibliotecas do lado do cliente no Build** no menu de contexto.
* Clique no botão **Sim** quando for solicitado a instalar um pacote NuGet. O pacote NuGet [Microsoft. Web. LibraryManager. Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) é adicionado ao projeto:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Compile o projeto para confirmar se a restauração do arquivo LibMan ocorre. O pacote de `Microsoft.Web.LibraryManager.Build` injeta um destino do MSBuild que executa o LibMan durante a operação de compilação do projeto.
* Examine o feed de **Build** da janela de **saída** para um log de atividades do LibMan:

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

Quando o comportamento de Restore-on-Build é habilitado, o menu de contexto *Libman. JSON* exibe uma opção **desabilitar restaurar bibliotecas do lado do cliente no Build** . A seleção dessa opção remove a referência de pacote `Microsoft.Web.LibraryManager.Build` do arquivo de projeto. Consequentemente, as bibliotecas do lado do cliente não são mais restauradas em cada compilação.

Independentemente da configuração de Restore-on-Build, você pode restaurar manualmente a qualquer momento no menu de contexto *Libman. JSON* . Para obter mais informações, consulte [restaurar arquivos manualmente](#restore-files-manually).

### <a name="restore-files-manually"></a>Restaurar arquivos manualmente

Para restaurar manualmente os arquivos de biblioteca:

* Para todos os projetos na solução:
  * Clique com o botão direito do mouse no nome da solução em **Gerenciador de soluções**.
  * Selecione a opção **restaurar bibliotecas do lado do cliente** .
* Para um projeto específico:
  * Clique com o botão direito do mouse no arquivo *Libman. JSON* em **Gerenciador de soluções**.
  * Selecione a opção **restaurar bibliotecas do lado do cliente** .

Enquanto a operação de restauração está em execução:

* O ícone de Central de Status de Tarefas (TSC) na barra de status do Visual Studio será animado e a operação de restauração de leitura será *iniciada*. Clicar no ícone abre uma dica de ferramenta listando as tarefas em segundo plano conhecidas.
* As mensagens serão enviadas à barra de status e ao feed do **Gerenciador de bibliotecas** da janela de **saída** . Por exemplo:

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

Para executar a operação de *limpeza* , que exclui os arquivos de biblioteca restaurados anteriormente no Visual Studio:

* Clique com o botão direito do mouse no arquivo *Libman. JSON* em **Gerenciador de soluções**.
* Selecione a opção **limpar bibliotecas do lado do cliente** .

Para evitar a remoção não intencional de arquivos que não são da biblioteca, a operação de limpeza não exclui diretórios inteiros. Ele remove apenas os arquivos que foram incluídos na restauração anterior.

Enquanto a operação de limpeza está em execução:

* O ícone de TSC na barra de status do Visual Studio será animado e a operação de leitura de *bibliotecas do cliente será iniciada*. Clicar no ícone abre uma dica de ferramenta listando as tarefas em segundo plano conhecidas.
* As mensagens são enviadas à barra de status e ao feed do **Gerenciador de bibliotecas** da janela de **saída** . Por exemplo:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

A operação de limpeza somente exclui arquivos do projeto. Os arquivos de biblioteca permanecem no cache para uma recuperação mais rápida em operações futuras de restauração. Para gerenciar arquivos de biblioteca armazenados no cache do computador local, use a [CLI do LibMan](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Desinstalar arquivos de biblioteca

Para desinstalar os arquivos de biblioteca:

* Abra *Libman. JSON*.
* Posicione o cursor dentro do literal de objeto `libraries` correspondente.
* Clique no ícone de lâmpada que aparece na margem esquerda e selecione **desinstalar \<library_name > @\<library_version >** :

  ![Opção de menu de contexto desinstalar biblioteca](_static/uninstall-menu-option.png)

Como alternativa, você pode editar e salvar manualmente o manifesto LibMan (*LibMan. JSON*). A [operação de restauração](#restore-library-files) é executada quando o arquivo é salvo. Os arquivos de biblioteca que não são mais definidos no *Libman. JSON* são removidos do projeto.

## <a name="update-library-version"></a>Atualizar versão da biblioteca

Para verificar se há uma versão atualizada da biblioteca:

* Abra *Libman. JSON*.
* Posicione o cursor dentro do literal de objeto `libraries` correspondente.
* Clique no ícone de lâmpada que aparece na margem esquerda. Passe o mouse sobre **verificar se há atualizações**.

LibMan verifica se há uma versão de biblioteca mais recente do que a versão instalada. Os seguintes resultados podem ocorrer:

* Uma mensagem **nenhuma atualização encontrada** será exibida se a versão mais recente já estiver instalada.
* A versão estável mais recente será exibida se ainda não estiver instalada.

  ![Opção de menu de contexto verificar se há atualizações](_static/update-menu-option.png)

* Se um pré-lançamento mais recente do que a versão instalada estiver disponível, o pré-lançamento será exibido.

Para fazer o downgrade para uma versão de biblioteca mais antiga, edite manualmente o arquivo *Libman. JSON* . Quando o arquivo for salvo, a [operação de restauração](#restore-library-files)do LibMan:

* Remove arquivos redundantes da versão anterior.
* Adiciona arquivos novos e atualizados da nova versão.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:client-side/libman/libman-cli>
* [Repositório do GitHub do LibMan](https://github.com/aspnet/LibraryManager)
