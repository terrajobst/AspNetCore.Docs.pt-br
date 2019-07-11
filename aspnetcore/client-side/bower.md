---
title: Gerenciar pacotes do lado do cliente com Bower no ASP.NET Core
author: rick-anderson
description: Gerenciar pacotes do lado do cliente com o Bower.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 08/09/2018
uid: client-side/bower
ms.openlocfilehash: 1292b75bdfe1cea56d9c8bc282b0dd90fc945a33
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813525"
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a>Gerenciar pacotes do lado do cliente com Bower no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel arroz](https://twitter.com/noelrice1), e [Scott Addie](https://scottaddie.com)

> [!IMPORTANT]
> Enquanto o Bower é mantido, seus mantenedores recomendam usando uma solução diferente. [Gerenciador de biblioteca](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/) (LibMan de forma abreviada) é a ferramenta de aquisição de biblioteca do lado do cliente novo do Visual Studio (Visual Studio 15,8 ou posterior). Para obter mais informações, consulte <xref:client-side/libman/index>. Bower tem suporte no Visual Studio versão 15.5.
>
> Yarn com Webpack é uma alternativa popular para a qual [instruções de migração](https://bower.io/blog/2017/how-to-migrate-away-from-bower/) estão disponíveis.

[Bower](https://bower.io/) chama a próprio "Um Gerenciador de pacotes para a web". Dentro do ecossistema do .NET, ele preenche essa lacuna deixado pela incapacidade do NuGet para entregar os arquivos de conteúdo estático. Para projetos do ASP.NET Core, esses arquivos estáticos são inerentes às bibliotecas do lado do cliente, como [jQuery](https://jquery.com/) e [Bootstrap](https://getbootstrap.com/). Para bibliotecas do .NET, você usar [NuGet](https://www.nuget.org/) Gerenciador de pacotes.

Processo de compilação de novos projetos criados com os modelos de projeto do ASP.NET Core configurar lado do cliente. [jQuery](https://jquery.com/) e [Bootstrap](https://getbootstrap.com/) estiverem instalados, e Bower é suportado.

Pacotes do lado do cliente são listados na *bower. JSON* arquivo. Os modelos de projeto do ASP.NET Core configura *bower. JSON* com o jQuery, validação do jQuery e Bootstrap.

Neste tutorial, vamos adicionar suporte para [Font Awesome](http://fontawesome.io). Pacotes do bower podem ser instalados com o **gerenciar pacotes do Bower** interface do usuário ou manualmente na *bower. JSON* arquivo.

### <a name="installation-via-manage-bower-packages-ui"></a>Instalação por meio de pacotes do Bower gerenciar da interface do usuário

* Criar um novo aplicativo Web do ASP.NET Core com o **aplicativo Web do ASP.NET Core (.NET Core)** modelo. Selecione **aplicativo Web** e **nenhuma autenticação**.

* Clique com botão direito no projeto no Gerenciador de soluções e selecione **gerenciar pacotes do Bower** (como alternativa, no menu principal, **Project** > **gerenciar pacotes Bower**).

* No **Bower: \<nome do projeto\>**  janela, clique na guia "Procurar" e, em seguida, filtre a lista de pacotes inserindo `font-awesome` na caixa de pesquisa:

  ![Gerenciar pacotes do bower](bower/_static/manage-bower-packages.png)

* Confirme se o "Salvar alterações *bower. JSON*" caixa de seleção está marcada. Selecione uma versão na lista suspensa e clique no **instalar** botão. O **saída** janela mostra os detalhes da instalação.

### <a name="manual-installation-in-bowerjson"></a>Instalação manual no bower. JSON

Abra o *bower. JSON* arquivo e adicione "font-incrível" para as dependências. O IntelliSense mostra os pacotes disponíveis. Quando um pacote é selecionado, as versões disponíveis são exibidas. As imagens abaixo são mais antigas e não corresponder ao que você vê.

![IntelliSense do Explorador de pacotes de bower](bower/_static/add-package.png)

![versão bower IntelliSense](bower/_static/version-intelliSense.png)

Usos para bower [controle de versão semântico](https://semver.org/) para organizar as dependências. Controle de versão semântico, também conhecido como SemVer, identifica os pacotes com o esquema de numeração \<principal >.\< secundária >. \<patch >. IntelliSense simplifica o controle de versão semântico, mostrando apenas algumas opções comuns. O item superior na lista do IntelliSense (4.6.3 no exemplo acima) é considerado a versão estável mais recente do pacote. O símbolo de acento circunflexo (^) corresponde à versão principal mais recente e o til (~) corresponde a versão secundária mais recente.

Salvar a *bower. JSON* arquivo. Visual Studio inspeciona as *bower. JSON* arquivo para que as alterações. Ao salvar, o *bower install* comando é executado. Consulte a janela de saída **npm/Bower** modo de exibição para o comando executado.

Abra o *. bowerrc* do arquivo sob *bower. JSON*. O `directory` estiver definida como *wwwroot/lib* que indica o local do Bower instalará os ativos do pacote.

```json
{
 "directory": "wwwroot/lib"
}
```

Você pode usar a caixa de pesquisa no Gerenciador de soluções para localizar e exibir o pacote font awesome.

Abra o *Views\Shared\_layout. cshtml* arquivo e adicione o arquivo CSS font awesome no ambiente [auxiliar de marca](xref:mvc/views/tag-helpers/intro) para `Development`. No Gerenciador de soluções, arraste e solte *fonte awesome.css* dentro de `<environment names="Development">` elemento.

[!code-html[](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

Um aplicativo de produção, você adicionaria *fonte awesome.min.css* para o auxiliar de marca de ambiente para `Staging,Production`.

Substitua o conteúdo do *Views\Home\About.cshtml* arquivo Razor com a seguinte marcação:

[!code-html[](bower/sample/About.cshtml)]

Execute o aplicativo e navegue até a exibição About sobre para verificar se o pacote font awesome funciona.

## <a name="exploring-the-client-side-build-process"></a>Explorando o processo de compilação do lado do cliente

A maioria dos modelos de projeto do ASP.NET Core já estão configurados para usar o Bower. Este passo a passo de próximo começa com um projeto vazio do ASP.NET Core e adiciona cada parte manualmente, portanto, você pode ter uma ideia de como o Bower é usado em um projeto. Você pode ver o que acontece com a estrutura do projeto e o tempo de execução de saída que cada alteração de configuração é feita.

As etapas gerais para usar o processo de compilação do lado do cliente com o Bower são:

* Defina pacotes usados em seu projeto. <!-- once defined, you don't need to download them, VS does -->
* Pacotes de referência de suas páginas da web.

### <a name="define-packages"></a>Definir pacotes

Depois que você listar pacotes na *bower. JSON* arquivo, o Visual Studio irá baixá-los. O exemplo a seguir usa o Bower para carregar o jQuery e Bootstrap para o *wwwroot* pasta.

* Criar um novo aplicativo Web do ASP.NET Core com o **aplicativo Web do ASP.NET Core (.NET Core)** modelo. Selecione o **vazio** modelo de projeto e clique em **Okey**.

* No Gerenciador de soluções, clique com botão direito no projeto > **Adicionar Novo Item** e selecione **arquivo de configuração Bower**. Observação: Um *. bowerrc* arquivo também é adicionado.

* Abra *bower. JSON*e adicionar o jquery e bootstrap para o `dependencies` seção. Resultante *bower. JSON* arquivo se parecerá com o exemplo a seguir. As versões serão alterado ao longo do tempo e podem não corresponder a imagem a seguir.

[!code-json[](bower/sample/bower.json?highlight=5,6)]

* Salvar a *bower. JSON* arquivo.

  Verifique se o projeto inclui o *bootstrap* e *jQuery* diretórios *wwwroot/lib*. Bower usa o *. bowerrc* arquivo para instalar os ativos na *wwwroot/lib*.

  Observação: A interface do usuário "Gerenciar pacotes do Bower" fornece uma alternativa à edição do arquivo manual.

### <a name="enable-static-files"></a>Habilitar arquivos estáticos

* Adicionar o `Microsoft.AspNetCore.StaticFiles` pacote NuGet ao projeto.
* Habilitar arquivos estáticos a serem atendidos com o [middleware de arquivo estático](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions). Adicione uma chamada para [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions) para o `Configure` método `Startup`.

[!code-csharp[](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a>Pacotes de referência

Nesta seção, você criará uma página HTML para verificar se que ele pode acessar os pacotes implantados.

* Adicionar uma nova página HTML chamada *index. HTML* para o *wwwroot* pasta. Observação: Você deve adicionar o arquivo HTML para o *wwwroot* pasta. Por padrão, o conteúdo estático não pode ser servido fora *wwwroot*. Ver [arquivos estáticos](xref:fundamentals/static-files) para obter mais informações.

  Substitua o conteúdo do *index. HTML* com a seguinte marcação:

[!code-html[](bower/sample/Index.html)]

* Execute o aplicativo e navegue até `http://localhost:<port>/Index.html`. Como alternativa, com *index. HTML* aberto, pressione `Ctrl+Shift+W`. Verifique se que o estilo de jumbotron é aplicado, o código jQuery responde quando o botão é clicado e que o Bootstrap botão muda de estado.

  ![estilo de jumbotron aplicado](bower/_static/jumbotron.png)
