---
title: Łączenie się z usługą Azure Blockchain Service za pomocą kodu programu Visual Studio
description: Łączenie się z siecią konsorcjum usługi Azure Blockchain service przy użyciu rozszerzenia Azure Blockchain Development Kit for Ethereum w kodzie programu Visual Studio
ms.date: 04/22/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 8b502966317c5d07e89de4ae70ff72b899e963e6
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084842"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Szybki start: łączenie się z siecią konsorcjum usługi Azure Blockchain Service za pomocą kodu programu Visual Studio

W tym przewodniku Szybki start należy zainstalować i używać rozszerzenia Azure Blockchain Development Kit for Ethereum Visual Studio Code (VS Code) w celu dołączenia do konsorcjum w usłudze Azure Blockchain Service. Zestaw Azure Blockchain Development Kit upraszcza sposób tworzenia, łączenia, tworzenia i wdrażania inteligentnych kontraktów w księgach łańcucha bloków Ethereum.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [szybki start: Tworzenie członka łańcucha bloków przy użyciu portalu Azure](create-member.md) lub [przewodnika Szybki start: tworzenie członka łańcucha bloków usługi Azure Blockchain przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Rozszerzenie Programu Azure Blockchain Development Kit dla Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x lub nowsza](https://nodejs.org)
* [Git 2.10.x lub wyższy](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Dodaj python.exe do swojej ścieżki. Posiadanie języka Python w wersji 2.7.15 w ścieżce jest wymagane dla zestawu Azure Blockchain Development Kit.
* [Trufla 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

W systemie Windows dla modułu node-gyp wymagany jest zainstalowany kompilator języka C++. Można użyć narzędzi MSBuild:

* Jeśli jest zainstalowany program Visual Studio 2017, skonfiguruj npm do używania narzędzi MSBuild z poleceniem`npm config set msvs_version 2017 -g`
* Jeśli program Visual Studio 2019 jest zainstalowany, ustaw ścieżkę narzędzi kompilacji MS dla npm. Na przykład: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* W przeciwnym razie zainstaluj autonomiczne `npm install --global windows-build-tools` narzędzia kompilacji vs przy użyciu w podwyższonej *osądy uruchom jako administratora* powłoki polecenia.

Aby uzyskać więcej informacji na temat node-gyp, zobacz [repozytorium node-gyp w usłudze GitHub](https://github.com/nodejs/node-gyp).

### <a name="verify-azure-blockchain-development-kit-environment"></a>Weryfikowanie środowiska zestawu Azure Blockchain Development Kit

Zestaw Azure Blockchain Development Kit weryfikuje, że wymagania wstępne środowiska programistyczne zostały spełnione. Aby zweryfikować środowisko programistyczne:

Z palety poleceń vs code wybierz pozycję **Azure Blockchain: Show Welcome Page**.

Zestaw Azure Blockchain Development Kit uruchamia skrypt sprawdzania poprawności, który trwa około minuty. Dane wyjściowe można wyświetlić, wybierając **opcję Terminal > Nowy terminal**. Na pasku menu terminala wybierz kartę **Dane wyjściowe** i **azure blockchain** w rozwijanie. Pomyślne sprawdzenie poprawności wygląda następująco:

![Prawidłowe środowisko programistyczne](./media/connect-vscode/valid-environment.png)

 Jeśli brakuje wymaganego narzędzia, nowa karta o nazwie **Azure Blockchain Development Kit — podgląd** zawiera listę wymaganych narzędzi z łączami pobierania.

![Wymagane aplikacje zestawu deweloperów](./media/connect-vscode/required-apps.png)

Zainstaluj brakujące wymagania wstępne przed kontynuowaniem pracy z przewodnikiem Szybki start.

## <a name="connect-to-consortium-member"></a>Połącz się z członkiem konsorcjum

Możesz połączyć się z członkami konsorcjum przy użyciu rozszerzenia kodu programu Azure Blockchain Development Kit VS. Po połączeniu z konsorcjum można kompilować, tworzyć i wdrażać inteligentne kontrakty dla członka konsorcjum usługi Azure Blockchain Service.

Jeśli nie masz dostępu do członka konsorcjum usługi Azure Blockchain Service, wykonaj warunek wstępny [szybkiego startu: Utwórz członka łańcucha bloków przy użyciu witryny Azure portal](create-member.md) lub szybki [start: Utwórz członka łańcucha bloków usługi Azure Blockchain przy użyciu interfejsu wiersza polecenia platformy Azure.](create-member-cli.md)

1. W okienku eksploratora kodu VS rozwiń rozszerzenie **narzędzia Azure Blockchain.**
1. Wybierz **pozycję Połącz z siecią**.

   ![Łączenie się z siecią](./media/connect-vscode/connect-consortium.png)

    Jeśli zostanie wyświetlony monit o uwierzytelnienie platformy Azure, postępuj zgodnie z monitami o uwierzytelnienie przy użyciu przeglądarki.
1. Wybierz **usługę Azure Blockchain w** menu rozwijanym palety poleceń.
1. Wybierz grupę subskrypcji i zasobów skojarzoną z członkiem konsorcjum usługi Azure Blockchain Service.
1. Wybierz konsorcjum z listy.

Członkowie konsorcjum i łańcucha bloków są wymienione na pasku bocznym eksploratora kodu VS.

![Konsorcjum wyświetlane w eksploratorze](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto zestawu Azure Blockchain Development Kit for Ethereum VS Code, aby dołączyć do konsorcjum w usłudze Azure Blockchain Service. Wypróbuj następny samouczek, aby użyć zestawu Azure Blockchain Development Kit for Ethereum do tworzenia, tworzenia, wdrażania i wykonywania funkcji inteligentnego kontraktu za pośrednictwem transakcji.

> [!div class="nextstepaction"]
> [Tworzenie, tworzenie i wdrażanie inteligentnych kontraktów w usłudze Azure Blockchain Service](send-transaction.md)