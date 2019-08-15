---
title: ASP.NET Core bibliotecas de classes de componentes Razor
author: guardrex
description: Descubra como os componentes podem ser incluídos em aplicativos mais podestas de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/class-libraries
ms.openlocfilehash: 6e93d48bbc684845952c3db8935ccc8b190044b7
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030349"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core bibliotecas de classes de componentes Razor

Por [Simon Timms](https://github.com/stimms)

Os componentes podem ser compartilhados em uma [biblioteca de classes Razor (RCL)](xref:razor-pages/ui-class) em projetos. Uma *biblioteca de classes de componentes Razor* pode ser incluída em:

* Outro projeto na solução.
* Um pacote NuGet.
* Uma biblioteca .NET referenciada.

Assim como os componentes são tipos .NET regulares, os componentes fornecidos por um RCL são assemblies normais do .NET.

## <a name="create-an-rcl"></a>Criar um RCL

Siga as orientações no <xref:blazor/get-started> artigo para configurar seu ambiente para um mais incrivelmente.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Crie um novo projeto.
1. Selecione **biblioteca de classes Razor**. Selecione **Avançar**.
1. Na caixa de diálogo **criar uma nova biblioteca de classes Razor** , selecione **criar**.
1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Os exemplos neste tópico usam o nome `MyComponentLib1`do projeto. Selecione **Criar**.
1. Adicione o RCL a uma solução:
   1. Clique com o botão direito do mouse na solução. Selecione **Adicionar** > **projeto existente**.
   1. Navegue até o arquivo de projeto do RCL.
   1. Selecione o arquivo de projeto do RCL ( *. csproj*).
1. Adicione uma referência ao RCL do aplicativo:
   1. Clique com o botão direito do mouse no projeto do aplicativo. Selecione **Adicionar** > **referência**.
   1. Selecione o projeto RCL. Selecione **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

1. Use o modelo de **biblioteca de classes Razor** (`razorclasslib`) com o comando [dotnet New](/dotnet/core/tools/dotnet-new) em um shell de comando. No exemplo a seguir, um RCL é criado com `MyComponentLib1`o nome. A pasta que contém `MyComponentLib1` é criada automaticamente quando o comando é executado:

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. Para adicionar a biblioteca a um projeto existente, use o comando [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) em um shell de comando. No exemplo a seguir, o RCL é adicionado ao aplicativo. Execute o seguinte comando na pasta do projeto do aplicativo com o caminho para a biblioteca:

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a>RCLs não tem suporte para aplicativos do lado do cliente

Na versão prévia atual do ASP.NET Core 3,0, as bibliotecas de classes do Razor não são compatíveis com aplicativos de alto nível do cliente. Para aplicativos do lado do cliente mais incrivelmente, use uma biblioteca de componentes mais incrivelmente criada `blazorlib` pelo modelo em um shell de comando:

```console
dotnet new blazorlib -o MyComponentLib1
```

As bibliotecas de componentes `blazorlib` que usam o modelo podem incluir arquivos estáticos, como imagens, JavaScript e folhas de estilo. No momento da compilação, os arquivos estáticos são inseridos no arquivo de assembly compilado ( *. dll*), que permite o consumo dos componentes sem precisar se preocupar com a inclusão de seus recursos. Todos os arquivos incluídos no `content` diretório são marcados como um recurso incorporado.

## <a name="consume-a-library-component"></a>Consumir um componente de biblioteca

Para consumir os componentes definidos em uma biblioteca em outro projeto, use uma das seguintes abordagens:

* Use o nome completo do tipo com o namespace.
* Use a diretiva [ \@using](xref:mvc/views/razor#using) do Razor. Componentes individuais podem ser adicionados por nome.

Nos exemplos a seguir, `MyComponentLib1` é uma biblioteca de componentes que `SalesReport` contém um componente.

O `SalesReport` componente pode ser referenciado usando seu nome de tipo completo com namespace:

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

O componente também poderá ser referenciado se a biblioteca for colocada no escopo com `@using` uma diretiva:

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Inclua a `@using MyComponentLib1` diretiva no arquivo *_Import. Razor* de nível superior para disponibilizar os componentes da biblioteca para um projeto inteiro. Adicione a diretiva a um arquivo *_Import. Razor* em qualquer nível para aplicar o namespace a uma única página ou a um conjunto de páginas dentro de uma pasta.

## <a name="build-pack-and-ship-to-nuget"></a>Compilar, empacotar e enviar para o NuGet

Como as bibliotecas de componentes são bibliotecas padrão do .NET, o empacotamento e o envio deles para o NuGet não é diferente do empacotamento e do envio de nenhuma biblioteca para o NuGet. O empacotamento é executado usando o comando [dotnet Pack](/dotnet/core/tools/dotnet-pack) em um shell de comando:

```console
dotnet pack
```

Carregue o pacote no NuGet usando o comando [dotnet NuGet Publish](/dotnet/core/tools/dotnet-nuget-push) em um shell de comando:

```console
dotnet nuget publish
```

Ao usar o `blazorlib` modelo, os recursos estáticos são incluídos no pacote NuGet. Os consumidores de biblioteca recebem scripts e folhas de estilo automaticamente, para que os consumidores não sejam solicitados a instalar os recursos manualmente.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Criar uma biblioteca de classes de componentes Razor com ativos estáticos

Um RCL pode incluir ativos estáticos. Os ativos estáticos estão disponíveis para qualquer aplicativo que consuma a biblioteca. Para obter mais informações, consulte <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/ui-class>
