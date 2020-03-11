---
title: Proteção de dados do ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o conceito de proteção de dados e os princípios de design do ASP.NET Core APIs de proteção de dados.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/introduction
ms.openlocfilehash: 37f170a3e8a46ef2215b0999358d46dd402636df
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664440"
---
# <a name="aspnet-core-data-protection"></a>Proteção de dados do ASP.NET Core

Os aplicativos Web geralmente precisam armazenar dados sensíveis à segurança. O Windows fornece DPAPI para aplicativos de área de trabalho, mas isso não é adequado para aplicativos Web. A ASP.NET Core pilha de proteção de dados fornece uma API criptográfica simples e fácil de usar que um desenvolvedor pode usar para proteger dados, incluindo o gerenciamento e a rotação de chaves.

O ASP.NET Core pilha de proteção de dados foi projetado para servir como a substituição de longo prazo para o elemento de&gt; &lt;machineKey no ASP.NET 1. x-4. x. Ele foi projetado para resolver muitas das deficiências da pilha criptográfica antiga, fornecendo uma solução pronta para uso para a maioria dos casos de utilização que os aplicativos modernos provavelmente encontrarão.

## <a name="problem-statement"></a>Declaração do problema

A declaração geral do problema pode ser disparada de forma sucinta em uma única frase: preciso manter informações confiáveis para recuperação posterior, mas não confio no mecanismo de persistência. Em termos da Web, isso pode ser escrito como "preciso fazer ida e volta em estado confiável por meio de um cliente não confiável".

O exemplo canônico disso é um cookie de autenticação ou um token de portador. O servidor gera um token "Eu sou Groot e tem permissões XYZ" e o passa para o cliente. Em alguma data futura, o cliente apresentará esse token de volta ao servidor, mas o servidor precisa de algum tipo de garantia de que o cliente não tenha forjado o token. Portanto, o primeiro requisito: autenticidade (também conhecido como integridade, prova de adulteração).

Como o estado persistente é confiável pelo servidor, antecipamos que esse Estado pode conter informações específicas para o ambiente operacional. Isso pode estar na forma de um caminho de arquivo, uma permissão, um identificador ou outra referência indireta ou alguma outra parte dos dados específicos do servidor. Essas informações geralmente não devem ser divulgadas para um cliente não confiável. Portanto, o segundo requisito: confidencialidade.

Por fim, como os aplicativos modernos são componented, o que vimos é que os componentes individuais desejarão aproveitar esse sistema sem considerar outros componentes do sistema. Por exemplo, se um componente de token de portador estiver usando essa pilha, ele deverá operar sem interferência de um mecanismo CSRF que também pode estar usando a mesma pilha. Portanto, o requisito final: isolamento.

Podemos fornecer mais restrições para restringir o escopo de nossos requisitos. Supomos que todos os serviços que operam dentro do cryptosystem são igualmente confiáveis e que os dados não precisam ser gerados ou consumidos fora dos serviços sob nosso controle direto. Além disso, exigimos que as operações sejam o mais rápido possível, já que cada solicitação para o serviço Web pode passar pelo cryptosystem uma ou mais vezes. Isso torna a criptografia simétrica ideal para nosso cenário, e podemos descontar a criptografia assimétrica até o momento em que for necessário.

## <a name="design-philosophy"></a>Filosofia de design

Começamos identificando problemas com a pilha existente. Depois que tivemos isso, pesquisamos o panorama das soluções existentes e concluíram que nenhuma solução existente tinha tido os recursos que buscamos. Depois, projetamos uma solução com base em vários princípios de orientação.

* O sistema deve oferecer simplicidade de configuração. O ideal é que o sistema seja zero de configuração e os desenvolvedores pudessem atingir o solo em execução. Em situações em que os desenvolvedores precisam configurar um aspecto específico (como o repositório de chaves), deve-se considerar a consideração para tornar essas configurações específicas simples.

* Ofereça uma API simples voltada para o consumidor. As APIs devem ser fáceis de usar corretamente e difíceis de usar incorretamente.

* Os desenvolvedores não devem aprender os principais princípios de gerenciamento. O sistema deve lidar com a seleção de algoritmos e o tempo de vida da chave em nome do desenvolvedor. O ideal é que o desenvolvedor nunca tenha acesso ao material de chave bruto.

* As chaves devem ser protegidas em repouso quando possível. O sistema deve descobrir um mecanismo de proteção padrão apropriado e aplicá-lo automaticamente.

Com esses princípios em mente, desenvolvemos uma pilha de proteção de dados simples e [fácil de usar](xref:security/data-protection/using-data-protection) .

O ASP.NET Core APIs de proteção de dados não se destina principalmente à persistência indefinida de cargas confidenciais. Outras tecnologias como o [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) e o [Azure Rights Management](/rights-management/) são mais adequadas para o cenário de armazenamento indefinido e têm recursos de gerenciamento de chaves de alta segurança. Dito isso, não há nada proibindo um desenvolvedor de usar o ASP.NET Core APIs de proteção de dados para proteção de longo prazo de dados confidenciais.

## <a name="audience"></a>Público-alvo

O sistema de proteção de dados é dividido em cinco pacotes principais. Vários aspectos dessas APIs visam três públicos principais;

1. O aplicativo de destino [visão geral das APIs de consumidor](xref:security/data-protection/consumer-apis/overview) e os desenvolvedores de estrutura.

   "Não quero saber mais sobre como a pilha opera ou sobre como ela está configurada. Eu simplesmente quero executar alguma operação da maneira mais simples possível com uma alta probabilidade de usar as APIs com êxito. "

2. As [APIs de configuração](xref:security/data-protection/configuration/overview) visam os desenvolvedores de aplicativos e administradores de sistema.

   "Preciso dizer ao sistema de proteção de dados que meu ambiente requer caminhos ou configurações não padrão."

3. As APIs de extensibilidade visam os desenvolvedores responsáveis pela implementação de uma política personalizada. O uso dessas APIs seria limitado a situações raras e a desenvolvedores experientes com reconhecimento de segurança.

   "Preciso substituir um componente inteiro no sistema porque tenho requisitos de comportamento verdadeiramente exclusivos. Quero aprender partes de uma superfície de API que são usadas de forma incomum para criar um plug-in que atenda aos meus requisitos. "

## <a name="package-layout"></a>Layout do pacote

A pilha de proteção de dados consiste em cinco pacotes.

* [Microsoft. AspNetCore. dataprotection. abstrações](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) contém as interfaces <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> e <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> para criar serviços de proteção de dados. Ele também contém métodos de extensão úteis para trabalhar com esses tipos (por exemplo, [IDataProtector. Protect](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*)). Se o sistema de proteção de dados for instanciado em outro lugar e você estiver consumindo a API, `Microsoft.AspNetCore.DataProtection.Abstractions`de referência.

* O [Microsoft. AspNetCore.](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) dataprotection contém a implementação principal do sistema de proteção de dados, incluindo operações essenciais de criptografia, gerenciamento de chaves, configuração e extensibilidade. Para instanciar o sistema de proteção de dados (por exemplo, adicioná-lo a um <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>) ou modificar ou estender seu comportamento, `Microsoft.AspNetCore.DataProtection`de referência.

* [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contém APIs adicionais que os desenvolvedores podem encontrar úteis, mas que não pertencem ao pacote principal. Por exemplo, esse pacote contém métodos de fábrica para instanciar o sistema de proteção de dados para armazenar chaves em um local no sistema de arquivos sem injeção de dependência (consulte <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>). Ele também contém métodos de extensão para limitar o tempo de vida de cargas protegidas (consulte <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector>).

* [Microsoft. AspNetCore. dataprotection. SystemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) pode ser instalado em um aplicativo ASP.NET 4. x existente para redirecionar suas operações de `<machineKey>` para usar a nova pilha de proteção de dados do ASP.NET Core. Para obter mais informações, consulte <xref:security/data-protection/compatibility/replacing-machinekey>.

* [Microsoft. AspNetCore. Cryptography. Keyderivation](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) fornece uma implementação da rotina de hash de senha do PBKDF2 e pode ser usada por sistemas que devem tratar senhas de usuário com segurança. Para obter mais informações, consulte <xref:security/data-protection/consumer-apis/password-hashing>.

## <a name="additional-resources"></a>Recursos adicionais

<xref:host-and-deploy/web-farm>
