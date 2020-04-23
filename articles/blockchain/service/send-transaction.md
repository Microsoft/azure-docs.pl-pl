---
title: Tworzenie, tworzenie, & wdrażanie inteligentnych umów — azure blockchain service
description: Samouczek dotyczący używania zestawu Azure Blockchain Development Kit for Ethereum w programie Visual Studio Code do tworzenia, tworzenia i wdrażania inteligentnego kontraktu w usłudze Azure Blockchain Service.
ms.date: 04/22/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: dc23c680dfb2ed33cae2a251af16e1b1f25c6ac7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086661"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Samouczek: Tworzenie, tworzenie, tworzenie i wdrażanie inteligentnych kontraktów w usłudze Azure Blockchain

W tym samouczku użyj rozszerzenia Azure Blockchain Development Kit for Ethereum w programie Visual Studio Code, aby utworzyć, skompilować i wdrożyć inteligentny kontrakt w usłudze Azure Blockchain. Zestaw deweloperski służy również do wykonywania funkcji inteligentnego kontraktu za pośrednictwem transakcji.

Program Azure Blockchain Development Kit for Ethereum umożliwia:

> [!div class="checklist"]
> * Tworzenie inteligentnego kontraktu
> * Wdrażanie inteligentnego kontraktu
> * Wykonywanie funkcji inteligentnego kontraktu za pośrednictwem transakcji

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [szybki start: łączenie się z siecią konsorcjum usługi Azure Blockchain Service za pomocą kodu programu Visual Studio](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Rozszerzenie Programu Azure Blockchain Development Kit dla Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x lub nowsza](https://nodejs.org/download)
* [Git 2.10.x lub wyższy](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Dodaj python.exe do swojej ścieżki. Python w wersji 2.7.15 w ścieżce jest wymagany dla zestawu Azure Blockchain Development Kit.
* [Trufla 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

W systemie Windows dla modułu node-gyp wymagany jest zainstalowany kompilator języka C++. Można użyć narzędzi MSBuild:

* Jeśli jest zainstalowany program Visual Studio 2017, skonfiguruj npm do używania narzędzi MSBuild z poleceniem`npm config set msvs_version 2017 -g`
* Jeśli program Visual Studio 2019 jest zainstalowany, ustaw ścieżkę narzędzi kompilacji MS dla npm. Na przykład: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* W przeciwnym razie zainstaluj autonomiczne `npm install --global windows-build-tools` narzędzia kompilacji vs przy użyciu w podwyższonej *osądy uruchom jako administratora* powłoki polecenia.

Aby uzyskać więcej informacji na temat node-gyp, zobacz [repozytorium node-gyp w usłudze GitHub](https://github.com/nodejs/node-gyp).

## <a name="create-a-smart-contract"></a>Tworzenie inteligentnego kontraktu

Zestaw Azure Blockchain Development Kit for Ethereum używa szablonów projektów i narzędzi trufli, aby ułatwić tworzenie, tworzenie i wdrażanie kontraktów. Przed rozpoczęciem należy ukończyć wstępny [przewodnik Szybki start: łączenie się z siecią konsorcjum usługi Azure Blockchain Service za pomocą programu Visual Studio Code.](connect-vscode.md) Przewodnik szybki start prowadzi użytkownika przez instalację i konfigurację zestawu Azure Blockchain Development Kit for Ethereum.

1. Z palety poleceń vs code wybierz pozycję **Azure Blockchain: New Solidity Project**.
1. Wybierz **pozycję Utwórz projekt podstawowy**.
1. Utwórz nowy `HelloBlockchain` folder o nazwie i **Wybierz nową ścieżkę projektu**.

Zestaw Azure Blockchain Development Kit tworzy i inicjuje nowy projekt Solidity dla Ciebie. Podstawowy projekt zawiera przykładowy inteligentny kontrakt **HelloBlockchain** i wszystkie pliki niezbędne do utworzenia i wdrożenia dla członka konsorcjum w usłudze Azure Blockchain Service. Utworzenie projektu może potrwać kilka minut. Postęp w panelu terminali programu VS Code można monitorować, wybierając dane wyjściowe dla narzędzia Azure Blockchain.

Struktura projektu wygląda następująco:

   ![Projekt solidności](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Zbuduj inteligentny kontrakt

Inteligentne kontrakty znajdują się w katalogu **kontraktów** projektu. Kompilujesz inteligentne kontrakty przed wdrożeniem ich w łańcuchu bloków. Użyj **build contracts** polecenia do kompilacji wszystkich inteligentnych umów w projekcie.

1. Na pasku bocznym eksploratora kodu VS rozwiń folder **kontraktów** w projekcie.
1. Kliknij prawym przyciskiem myszy **HelloBlockchain.sol** i wybierz polecenie **Buduj kontrakty** z menu.

    ![Menu Wybierz polecenie Buduj kontrakty ](./media/send-transaction/build-contracts.png)

Zestaw Azure Blockchain Development Kit używa trufli do kompilowania inteligentnych kontraktów.

![Dane wyjściowe kompilatora trufli](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Wdrażanie inteligentnego kontraktu

Trufla używa skryptów migracji do wdrażania kontraktów w sieci Ethereum. Migracje to pliki JavaScript **znajdujące** się w katalogu migracji projektu.

1. Aby wdrożyć umowę inteligentną, kliknij prawym przyciskiem myszy **HelloBlockchain.sol** i wybierz polecenie **Wdrażanie kontraktów** z menu.
1. Wybierz sieć konsorcjum platformy Azure Blockchain w palecie poleceń. Sieć blockchain konsorcjum została dodana do pliku konfiguracyjnego trufli projektu podczas tworzenia projektu.
1. Wybierz **pozycję Generuj mnemoniczną**. Wybierz nazwę pliku i zapisz plik mnemoniczny w folderze projektu. Na przykład `myblockchainmember.env`. Plik mnemonic służy do generowania klucza prywatnego Ethereum dla członka łańcucha bloków.

Zestaw Azure Blockchain Development Kit używa trufli do wykonania skryptu migracji w celu wdrożenia kontraktów w łańcuchu bloków.

![Pomyślnie wdrożony kontrakt](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Wywoływanie funkcji kontraktu

**HelloBlockchain** umowy **SendRequest** funkcja zmienia **requestmessage** zmiennej stanu. Zmiana stanu sieci blockchain odbywa się za pośrednictwem transakcji. Można użyć usługi Azure Blockchain Development Kit inteligentnej strony interakcji umowy do wywołania **SendRequest** funkcji za pośrednictwem transakcji.

1. Aby wchodzić w interakcje z inteligentnym kontraktem, kliknij prawym przyciskiem myszy **HelloBlockchain.sol** i wybierz polecenie **Pokaż stronę inteligentnej interakcji kontraktowej** z menu.

    ![Z menu wybierz polecenie Pokaż stronę inteligentnej interakcji z kontraktem](./media/send-transaction/contract-interaction.png)

1. Strona interakcji umożliwia wybranie wdrożonej wersji kontraktu, wywoływanie funkcji, wyświetlanie bieżącego stanu i wyświetlanie metadanych.

    ![Przykładowa strona interakcji z inteligentnym kontraktem](./media/send-transaction/interaction-page.png)

1. Aby wywołać funkcję inteligentnego kontraktu, wybierz akcję kontraktu i przekaż argumenty. Wybierz akcję umowy **SendRequest** i wprowadź **Hello, Blockchain!** dla parametru **requestMessage.** Wybierz **opcję Wykonaj,** aby wywołać funkcję **SendRequest** za pośrednictwem transakcji.

    ![Wykonaj akcję SendRequest](./media/send-transaction/sendrequest-action.png)

Po przetworzeniu transakcji sekcja interakcji odzwierciedla zmiany stanu.

![Zmiany stanu kontraktu](./media/send-transaction/contract-state.png)

Funkcja SendRequest ustawia pola **RequestMessage** i **State.** Bieżący stan **requestmessage** jest argument, który przeszedł **Hello, Blockchain**. Wartość pola **Stan** pozostaje **Żądanie**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, można usunąć `myResourceGroup` zasoby, usuwając grupę zasobów utworzoną w trybie szybkiego startu elementu *wstępnego Tworzenie łańcucha bloków.*

Aby usunąć grupę zasobów:

1. W witrynie Azure Portal przejdź do **grupy zasobów** w lewym okienku nawigacji i wybierz grupę zasobów, którą chcesz usunąć.
1. Wybierz pozycję **Usuń grupę zasobów**. Zweryfikuj usunięcie, wprowadzając nazwę grupy zasobów i wybierając pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono przykładowy projekt Solidity przy użyciu zestawu Azure Blockchain Development Kit. Został utworzony i wdrożony inteligentny kontrakt, a następnie wywołany funkcją za pośrednictwem transakcji w sieci konsorcjum blockchain hostowane w usłudze Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji blockchain przy użyciu usługi Azure Blockchain Service](develop.md)
