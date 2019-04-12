---
title: Bibliotecas de classes de componentes do Razor
author: guardrex
description: Descubra como os componentes podem ser incluídos em Razor componentes aplicativos de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/14/2019
uid: razor-components/class-libraries
ms.openlocfilehash: 1064ad60d90af15af483ba9bca5ed85fb63c2924
ms.sourcegitcommit: 10e14b85490f064395e9b2f423d21e3c2d39ed8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "59515323"
---
# <a name="razor-components-class-libraries"></a>Bibliotecas de classes de componentes do Razor

Por [Simon Timms](https://github.com/stimms)

Componentes podem ser compartilhados em bibliotecas de classes Razor entre projetos. Componentes podem ser incluídas nela:

* Outro projeto na solução.
* Um pacote do NuGet.
* Uma biblioteca referenciada do .NET.

Como componentes são tipos de .NET regulares, componentes fornecidos pelo bibliotecas de classes Razor são assemblies do .NET normais.

Use o `razorclasslib` modelo (biblioteca de classes Razor) com o [dotnet novo](/dotnet/core/tools/dotnet-new) comando:

```console
dotnet new razorclasslib -o MyComponentLib1
```

Adicionar arquivos de componente do Razor (*.razor*) para a biblioteca de classes Razor.

Para adicionar a biblioteca a um projeto existente, use o [dotnet sln](/dotnet/core/tools/dotnet-sln) comando:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```console
dotnet sln add .\MyComponentLib1
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```console
dotnet add WebApplication1 reference MyComponentLib1
```

---

> [!NOTE]
> Bibliotecas de classes Razor não são compatíveis com aplicativos de Blazor no ASP.NET Core da visualização 3.
>
> Para criar componentes em uma biblioteca que pode ser compartilhada com aplicativos Blazor e componentes do Razor, use uma biblioteca de classes Blazor criada pelo `blazorlib` modelo.
>
> Bibliotecas de classes Razor não dão suporte a ativos estáticos no ASP.NET Core da visualização 3. Bibliotecas de componentes usando o `blazorlib` modelo pode incluir arquivos estáticos, como imagens, JavaScript e folhas de estilo. No momento da compilação, arquivos estáticos são inseridos no arquivo de assembly compilado (*. dll*), que permite o consumo dos componentes sem precisar se preocupar sobre como incluir seus recursos. Todos os arquivos incluídos no `content` directory são marcados como um recurso inserido.

## <a name="consume-a-library-component"></a>Consumir um componente de biblioteca

Para consumir componentes definidos em uma biblioteca em outro projeto, o [ @addTagHelper ](xref:mvc/views/tag-helpers/intro#add-helper-label) diretiva deve ser usada. Componentes individuais podem ser adicionados por nome.

O formato geral da diretiva é:

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

Por exemplo, adiciona a seguinte diretiva `Component1` de `MyComponentLib1`:

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1
```

No entanto, é comum incluir todos os componentes de um assembly usando um caractere curinga (`*`):

```cshtml
@addTagHelper *, MyComponentLib1
```

O `@addTagHelper` diretiva pode ser incluída no *_ViewImport.cshtml* para tornar os componentes disponíveis para um projeto inteiro ou aplicadas a uma única página ou um conjunto de páginas dentro de uma pasta. Com o `@addTagHelper` diretiva em vigor, os componentes da biblioteca de componentes podem ser consumidos como se estivessem no mesmo assembly que o aplicativo.

## <a name="build-pack-and-ship-to-nuget"></a>Compilação, pacote e entregar para o NuGet

Como as bibliotecas de componentes são bibliotecas .NET padrão, empacotamento e enviá-los para o NuGet não é diferente de empacotamento e envio de qualquer biblioteca no NuGet. Empacotamento é realizado usando o [dotnet pack](/dotnet/core/tools/dotnet-pack) comando:

```console
dotnet pack
```

Carregue o pacote NuGet usando o [dotnet nuget publicar](/dotnet/core/tools/dotnet-nuget-push) comando:

```console
dotnet nuget publish
```

Ao usar o `blazorlib` modelo, recursos estáticos são incluídos no pacote do NuGet. Os clientes de biblioteca recebem automaticamente scripts e folhas de estilo, para que os consumidores não é necessários instalar manualmente os recursos.
