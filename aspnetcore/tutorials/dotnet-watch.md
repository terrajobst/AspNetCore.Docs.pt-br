---
title: Desenvolver aplicativos ASP.NET Core usando um observador de arquivo
author: rick-anderson
description: Este tutorial demonstra como instalar e usar a ferramenta observador de arquivo (observação de dotnet) da CLI do .NET Core em um aplicativo do ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: bedb3e6a65839db915ca7bc821a267a14d34bf30
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667408"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Desenvolver aplicativos ASP.NET Core usando um observador de arquivo

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

a [inspeção dotnet](https://www.nuget.org/packages/dotnet-watch) é uma ferramenta que executa um comando [CLI do .NET Core](/dotnet/core/tools) quando os arquivos de origem são alterados. Por exemplo, uma alteração de arquivo pode disparar uma compilação, execução de teste ou uma implantação.

Este tutorial usa um aplicativo de API Web existente com dois pontos de extremidade: um que retorna uma soma e outro que retorna um produto. O método de produto tem um bug, que é corrigido neste tutorial.

Baixe o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Ele consiste em dois projetos: *WebApp* (uma API Web ASP.NET Core) e *WebAppTests* (testes de unidade para a API Web).

Em um shell de comando, navegue até a pasta *WebApp*. Execute o comando a seguir:

```dotnetcli
dotnet run
```

> [!NOTE]
> `dotnet run --project <PROJECT>` pode ser usado para especificar um projeto a ser executado. Por exemplo, a execução de `dotnet run --project WebApp` da raiz do aplicativo de exemplo também executará o projeto *WebApp*.

O resultado do console mostra mensagens semelhantes à seguinte (indicando que o aplicativo está em execução e aguarda solicitações):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Em um navegador web, navegue até `http://localhost:<port number>/api/math/sum?a=4&b=5`. Você deve ver o resultado de `9`.

Navegue para o API do produto (`http://localhost:<port number>/api/math/product?a=4&b=5`). Ele retorna `9`, não `20`, conforme o esperado. Esse problema é corrigido mais adiante no tutorial.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Adicionar `dotnet watch` a um projeto

A ferramenta de observador de arquivo `dotnet watch` está incluída com a versão 2.1.300 do SDK do .NET Core. As etapas a seguir são necessárias ao usar uma versão anterior do SDK do .NET Core.

1. Adicionar uma referência ao pacote de `Microsoft.DotNet.Watcher.Tools` para o arquivo *.csproj*:

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. Instale o pacote `Microsoft.DotNet.Watcher.Tools` executando o seguinte comando:

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Executar os comandos da CLI do .NET Core usando `dotnet watch`

Qualquer [comando da CLI do .NET Core](/dotnet/core/tools#cli-commands) pode ser executado com `dotnet watch`. Por exemplo:

| {1&gt;Comando&lt;1} | Comando com inspeção |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet run -f netcoreapp2.0 | dotnet watch run -f netcoreapp2.0 |
| dotnet run -f netcoreapp2.0 -- --arg1 | dotnet watch run -f netcoreapp2.0 -- --arg1 |
| dotnet test | dotnet watch test |

Executar `dotnet watch run` na pasta *WebApp*. O resultado do console indica que `watch` foi iniciado.

> [!NOTE]
> `dotnet watch --project <PROJECT>` pode ser usado para especificar um projeto a ser observado. Por exemplo, a execução de `dotnet watch --project WebApp run` da raiz do aplicativo de exemplo também executará e observará o projeto *WebApp*.

## <a name="make-changes-with-dotnet-watch"></a>Fazer alterações com `dotnet watch`

Verifique se `dotnet watch` está em execução.

Corrija o bug no método `Product` do *MathController.cs* para que ele retorne o produto e não a soma:

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Salve o arquivo. O resultado do console indica que o `dotnet watch` detectou uma alteração de arquivo e reiniciou o aplicativo.

Verifique se `http://localhost:<port number>/api/math/product?a=4&b=5` retorna o resultado correto.

## <a name="run-tests-using-dotnet-watch"></a>Executar testes usando o `dotnet watch`

1. Altere o método `Product` de *MathController.cs* de volta para retornar a soma. Salve o arquivo.
1. Em um shell de comando, navegue até a pasta *WebAppTests*.
1. Execute [dotnet restore](/dotnet/core/tools/dotnet-restore).
1. Execute `dotnet watch test`. Seu resultado indica que um teste falhou e que o observador está aguardando as alterações de arquivo:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Corrija o código do método `Product` para que ele retorne o produto. Salve o arquivo.

`dotnet watch` detecta a alteração de arquivo e executa os testes novamente. O resultado do console indica a aprovação nos testes.

## <a name="customize-files-list-to-watch"></a>Personalizar lista de arquivos a observar

Por padrão, o `dotnet-watch` controla todos os arquivos que correspondem aos seguintes padrões glob:

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

Mais itens podem ser adicionados à lista de inspeção editando o arquivo *.csproj*. Os itens podem ser especificados individualmente ou usando padrões glob.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>Recusa de arquivos a serem observados

`dotnet-watch` pode ser configurado para ignorar as configurações padrão. Para ignorar arquivos específicos, adicione o atributo `Watch="false"` à definição de um item no arquivo *.csproj*:

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a>Projetos de inspeção personalizados

`dotnet-watch` não é restrito a projetos C#. Projetos de inspeção personalizados podem ser criados para lidar com cenários diferentes. Considere o layout de projeto a seguir:

* **test/**
  * *UnitTests/UnitTests.csproj*
  * *IntegrationTests/IntegrationTests.csproj*

Se a meta é observar ambos os projetos, crie um arquivo de projeto personalizados configurado para observar os dois projetos:

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

Para iniciar a observação de arquivo em ambos os projetos, mude para a pasta de *teste*. Execute o comando a seguir:

```dotnetcli
dotnet watch msbuild /t:Test
```

O VSTest é executado quando há qualquer mudança de arquivo no projeto de teste.

## <a name="dotnet-watch-in-github"></a>`dotnet-watch` no GitHub

`dotnet-watch` faz parte do [repositório dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)do github.
