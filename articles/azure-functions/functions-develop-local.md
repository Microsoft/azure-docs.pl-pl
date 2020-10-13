---
title: Lokalne opracowywanie i uruchamianie usługi Azure Functions
description: Dowiedz się, jak kodować i testować usługi Azure Functions na komputerze lokalnym przed uruchomieniem ich na Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 2362fcd27f1b5f7c0b3411eae5b3b94c7dc6cf4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86540183"
---
# <a name="code-and-test-azure-functions-locally"></a>Kodowanie i testowanie usługi Azure Functions lokalnie

Chociaż można opracowywać i testować Azure Functions w [Azure Portal], wielu deweloperów preferuje lokalne środowisko programistyczne. Funkcje ułatwiają Używanie ulubionych edytorów kodu i narzędzi programistycznych do tworzenia i testowania funkcji na komputerze lokalnym. Funkcje lokalne mogą łączyć się z aktywnymi usługami platformy Azure i debugować je na komputerze lokalnym za pomocą środowiska uruchomieniowego Full Functions.

## <a name="local-development-environments"></a>Lokalne środowiska programistyczne

Sposób tworzenia funkcji na komputerze lokalnym zależy od preferencji [języka](supported-languages.md) i narzędzi. Środowiska w poniższej tabeli obsługują programowanie lokalne:

|Środowisko                              |Języki         |Opis|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (Biblioteka klas)](functions-dotnet-class-library.md), [skrypt C# (. CSX)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](./functions-create-first-function-vs-code.md?pivots=programming-language-powershell), [Python](functions-reference-python.md) | [Rozszerzenie Azure Functions dla vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dodaje obsługę funkcji do vs Code. Wymaga podstawowych narzędzi. Program obsługuje programowanie w systemach Linux, MacOS i Windows w przypadku korzystania z wersji 2. x podstawowych narzędzi. Aby dowiedzieć się więcej, zobacz [Tworzenie pierwszej funkcji przy użyciu Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Wiersz polecenia lub terminal](functions-run-local.md) | [C# (Biblioteka klas)](functions-dotnet-class-library.md), [skrypt C# (. CSX)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Azure Functions Core Tools] udostępnia podstawowe środowisko uruchomieniowe i szablony służące do tworzenia funkcji, które umożliwiają programowanie lokalne. Wersja 2. x obsługuje programowanie w systemach Linux, MacOS i Windows. Wszystkie środowiska są zależne od podstawowych narzędzi do środowiska uruchomieniowego funkcji lokalnych. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (biblioteka klas)](functions-dotnet-class-library.md) | Narzędzia Azure Functions są dołączone do obciążeń **programistycznych platformy Azure** dla programu [Visual Studio 2019](https://www.visualstudio.com/vs/) i jego nowszych wersji. Umożliwia kompilowanie funkcji w bibliotece klas i publikowanie biblioteki. dll na platformie Azure. Obejmuje podstawowe narzędzia do testowania lokalnego. Aby dowiedzieć się więcej, zobacz temat [programowanie Azure Functions przy użyciu programu Visual Studio](functions-develop-vs.md). |
| [Maven](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java&tabs=bash,browser) (różne) | [Java](functions-reference-java.md) | Integruje się z podstawowymi narzędziami, aby umożliwić programowanie funkcji języka Java. Wersja 2. x obsługuje programowanie w systemach Linux, MacOS i Windows. Aby dowiedzieć się więcej, zobacz [Tworzenie pierwszej funkcji przy użyciu języka Java i Maven](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java&tabs=bash,browser). Obsługuje także Programowanie przy użyciu narzędzia [zaćmienie](functions-create-maven-eclipse.md) i [IntelliJ](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Każdy z tych lokalnych środowisk programistycznych umożliwia tworzenie projektów aplikacji funkcji i używanie wstępnie zdefiniowanych szablonów funkcji do tworzenia nowych funkcji. Każdy z nich korzysta z podstawowych narzędzi, dzięki czemu można testować i debugować funkcje w środowisku uruchomieniowym Real Functions na własnym komputerze tak samo jak w przypadku każdej innej aplikacji. Możesz również opublikować projekt aplikacji funkcji z dowolnego z tych środowisk na platformie Azure.

## <a name="next-steps"></a>Następne kroki

+ Aby dowiedzieć się więcej na temat lokalnego tworzenia skompilowanych funkcji języka C# przy użyciu programu Visual Studio 2019, zobacz temat [programowanie Azure Functions przy użyciu programu Visual Studio](functions-develop-vs.md).
+ Aby dowiedzieć się więcej na temat lokalnego opracowywania funkcji przy użyciu VS Code na komputerze Mac, Linux lub Windows, zobacz [Deploy Azure Functions from vs Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01).
+ Aby dowiedzieć się więcej na temat opracowywania funkcji z poziomu wiersza polecenia lub terminalu, zobacz [Work with Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
