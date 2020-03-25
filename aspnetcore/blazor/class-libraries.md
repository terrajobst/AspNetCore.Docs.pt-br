---
title: ASP.NET Core bibliotecas de classes de componentes Razor
author: guardrex
description: Descubra como os componentes podem ser incluídos em Blazor aplicativos de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218760"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core bibliotecas de classes de componentes Razor

Por [Simon Timms](https://github.com/stimms)

Os componentes podem ser compartilhados em uma [biblioteca de classes Razor (RCL)](xref:razor-pages/ui-class) em projetos. Uma *biblioteca de classes de componentes Razor* pode ser incluída em:

* Outro projeto na solução.
* Um pacote NuGet.
* Uma biblioteca .NET referenciada.

Assim como os componentes são tipos .NET regulares, os componentes fornecidos por um RCL são assemblies normais do .NET.

## <a name="create-an-rcl"></a>Criar um RCL

Siga as orientações no artigo <xref:blazor/get-started> para configurar seu ambiente para um mais incrivelmente.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Crie um novo projeto.
1. Selecione **biblioteca de classes Razor**. Selecione **Avançar**.
1. Na caixa de diálogo **criar uma nova biblioteca de classes Razor** , selecione **criar**.
1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Os exemplos neste tópico usam o nome do projeto `MyComponentLib1`. Selecione **Criar**.
1. Adicione o RCL a uma solução:
   1. Clique com o botão direito do mouse na solução. Selecione **adicionar** > **projeto existente**.
   1. Navegue até o arquivo de projeto do RCL.
   1. Selecione o arquivo de projeto do RCL ( *. csproj*).
1. Adicione uma referência ao RCL do aplicativo:
   1. Clique com o botão direito do mouse no projeto do aplicativo. Selecione **Adicionar** **referência**de > .
   1. Selecione o projeto RCL. Selecione **OK**.

> [!NOTE]
> Se a caixa de seleção **páginas de suporte e exibições** estiver marcada ao gerar o RCL do modelo, adicione também um arquivo *_Imports. Razor* à raiz do projeto gerado com o seguinte conteúdo para habilitar a criação de componentes Razor:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Adicione manualmente o arquivo a raiz do projeto gerado.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

1. Use o modelo de **biblioteca de classes Razor** (`razorclasslib`) com o comando [dotnet New](/dotnet/core/tools/dotnet-new) em um shell de comando. No exemplo a seguir, um RCL é criado com o nome `MyComponentLib1`. A pasta que contém `MyComponentLib1` é criada automaticamente quando o comando é executado:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Se a opção `-s|--support-pages-and-views` for usada ao gerar o RCL do modelo, adicione também um arquivo *_Imports. Razor* à raiz do projeto gerado com o seguinte conteúdo para habilitar a criação de componentes do Razor:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Adicione manualmente o arquivo a raiz do projeto gerado.

1. Para adicionar a biblioteca a um projeto existente, use o comando [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) em um shell de comando. No exemplo a seguir, o RCL é adicionado ao aplicativo. Execute o seguinte comando na pasta do projeto do aplicativo com o caminho para a biblioteca:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Consumir um componente de biblioteca

Para consumir os componentes definidos em uma biblioteca em outro projeto, use uma das seguintes abordagens:

* Use o nome completo do tipo com o namespace.
* Use a\@do Razor [usando](xref:mvc/views/razor#using) a diretiva. Componentes individuais podem ser adicionados por nome.

Nos exemplos a seguir, `MyComponentLib1` é uma biblioteca de componentes que contém um componente `SalesReport`.

O componente `SalesReport` pode ser referenciado usando seu nome de tipo completo com namespace:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

O componente também poderá ser referenciado se a biblioteca for colocada no escopo com uma diretiva de `@using`:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Inclua a diretiva `@using MyComponentLib1` no arquivo de nível superior *_Import. Razor* para disponibilizar os componentes da biblioteca para um projeto inteiro. Adicione a diretiva a um arquivo *_Import. Razor* em qualquer nível para aplicar o namespace a uma única página ou a um conjunto de páginas dentro de uma pasta.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Criar uma biblioteca de classes de componentes Razor com ativos estáticos

Um RCL pode incluir ativos estáticos. Os ativos estáticos estão disponíveis para qualquer aplicativo que consuma a biblioteca. Para obter mais informações, consulte <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>Compilar, empacotar e enviar para o NuGet

Como as bibliotecas de componentes são bibliotecas padrão do .NET, o empacotamento e o envio deles para o NuGet não é diferente do empacotamento e do envio de nenhuma biblioteca para o NuGet. O empacotamento é executado usando o comando [dotnet Pack](/dotnet/core/tools/dotnet-pack) em um shell de comando:

```dotnetcli
dotnet pack
```

Carregue o pacote para o NuGet usando o comando [dotnet NuGet Push](/dotnet/core/tools/dotnet-nuget-push) em um shell de comando.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/ui-class>
* [Adicionar um arquivo de configuração do vinculador XML a uma biblioteca](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
