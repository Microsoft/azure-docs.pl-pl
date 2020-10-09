---
title: Samouczek tworzenia, kompilowania, & wdrażania inteligentnych kontraktów — usługa Azure łańcucha bloków
description: Samouczek dotyczący sposobu korzystania z rozszerzenia Azure łańcucha bloków Development Kit for Ethereum w programie Visual Studio Code do tworzenia, kompilowania i wdrażania inteligentnego kontraktu w usłudze Azure łańcucha bloków Service.
ms.date: 04/22/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: dc23c680dfb2ed33cae2a251af16e1b1f25c6ac7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82086661"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Samouczek: Tworzenie, kompilowanie i wdrażanie inteligentnych kontraktów w usłudze Azure łańcucha bloków Service

W tym samouczku Użyj rozszerzenia Azure łańcucha bloków Development Kit for Ethereum w Visual Studio Code, aby utworzyć, skompilować i wdrożyć inteligentny kontrakt w usłudze Azure łańcucha bloków Service. Możesz również użyć zestawu deweloperskiego do wykonywania funkcji kontraktu inteligentnego za pośrednictwem transakcji.

Używasz zestawu Azure łańcucha bloków Development Kit dla Ethereum:

> [!div class="checklist"]
> * Tworzenie kontraktu inteligentnego
> * Wdrażanie kontraktu inteligentnego
> * Wykonywanie funkcji kontraktu inteligentnego za pośrednictwem transakcji

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Kończenie [szybkiego startu: użyj Visual Studio Code, aby nawiązać połączenie z siecią Azure łańcucha bloków Service Consortium](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Rozszerzenie Azure łańcucha bloków Development Kit dla rozszerzenia Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15. x lub wyższy](https://nodejs.org/download)
* [Git 2.10. x lub nowszy](https://git-scm.com)
* [2.7.15 Python](https://www.python.org/downloads/release/python-2715/) Dodaj python.exe do ścieżki. Wersja 2.7.15 języka Python w ścieżce jest wymagana w przypadku zestawu Azure łańcucha bloków Development Kit.
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Interfejs wiersza polecenia ganache 6.0.0](https://github.com/trufflesuite/ganache-cli)

W systemie Windows zainstalowany kompilator języka C++ jest wymagany dla modułu Node-GYP. Możesz użyć narzędzi MSBuild:

* Jeśli zainstalowano program Visual Studio 2017, należy skonfigurować npm do korzystania z narzędzi MSBuild przy użyciu polecenia `npm config set msvs_version 2017 -g`
* Jeśli zainstalowano program Visual Studio 2019, Ustaw ścieżkę Microsoft Build Tools for npm. Na przykład `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* W przeciwnym razie zainstaluj autonomiczne narzędzia programu VS Build przy użyciu `npm install --global windows-build-tools` programu w powłoce poleceń *Uruchom jako administrator* z podwyższonym poziomem uprawnień.

Aby uzyskać więcej informacji na temat węzła Node-GYP, zobacz [repozytorium Node-GYP w witrynie GitHub](https://github.com/nodejs/node-gyp).

## <a name="create-a-smart-contract"></a>Tworzenie kontraktu inteligentnego

Zestaw Azure łańcucha bloków Development Kit dla Ethereum używa szablonów projektów i narzędzi Truffle, aby pomóc w tworzeniu i wdrażaniu umów. Przed rozpoczęciem Wypełnij wymagania wstępne [przewodnika Szybki Start: użyj Visual Studio Code, aby nawiązać połączenie z siecią konsorcjum usługi Azure łańcucha bloków](connect-vscode.md). Przewodnik Szybki Start przeprowadzi Cię przez proces instalowania i konfigurowania zestawu Azure łańcucha bloków Development Kit dla Ethereum.

1. Z palety poleceń VS Code wybierz pozycję **Azure łańcucha bloków: nowy projekt o stałej**wypełniania.
1. Wybierz pozycję **Utwórz projekt podstawowy**.
1. Utwórz nowy folder o nazwie `HelloBlockchain` i **Wybierz pozycję Nowy projekt ścieżka**.

Zestaw Azure łańcucha bloków Development Kit tworzy i inicjuje nowy projekt o stałej wypełniania. Projekt podstawowy zawiera przykładową **HelloBlockchainą** umowę i wszystkie pliki niezbędne do kompilowania i wdrażania w składowej konsorcjum usługi Azure łańcucha bloków. Utworzenie projektu może potrwać kilka minut. Postęp można monitorować w panelu terminalu VS Code, wybierając dane wyjściowe dla usługi Azure łańcucha bloków.

Struktura projektu wygląda podobnie do poniższego przykładu:

   ![Projekt o stałej mocy](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Tworzenie kontraktu inteligentnego

Inteligentne kontrakty znajdują się w katalogu **kontraktów** projektu. Możesz kompilować inteligentne kontrakty przed wdrożeniem ich w usłudze łańcucha bloków. Użyj polecenia **Kompiluj kontrakty** , aby skompilować wszystkie inteligentne kontrakty w projekcie.

1. Na pasku bocznym Eksploratora VS Code rozwiń folder **kontrakty** w projekcie.
1. Kliknij prawym przyciskiem myszy **HelloBlockchain. peruwiański** i wybierz polecenie **Kompiluj kontrakty** z menu.

    ![Menu wybierz kontrakty kompilacji ](./media/send-transaction/build-contracts.png)

Usługa Azure łańcucha bloków Development Kit używa Truffle do kompilowania inteligentnych kontraktów.

![Dane wyjściowe kompilatora Truffle](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Wdrażanie kontraktu inteligentnego

Truffle używa skryptów migracji do wdrażania kontraktów w sieci Ethereum. Migracje to pliki JavaScript znajdujące się w katalogu **migracji** projektu.

1. Aby wdrożyć kontrakt inteligentny, kliknij prawym przyciskiem myszy **HelloBlockchain. peruwiański** i wybierz polecenie **Wdróż kontrakty** z menu.
1. Wybierz sieć Azure łańcucha bloków Consortium w palecie poleceń. Sieć konsorcjum łańcucha bloków została dodana do pliku konfiguracji Truffle projektu podczas tworzenia projektu.
1. Wybierz **Generuj**polecenie. Wybierz nazwę pliku i Zapisz plik w folderze projektu. Na przykład `myblockchainmember.env`. Ten plik jest używany do generowania klucza prywatnego Ethereum dla członka łańcucha bloków.

Usługa Azure łańcucha bloków Development Kit używa Truffle do wykonania skryptu migracji w celu wdrożenia umów w usłudze łańcucha bloków.

![Pomyślnie wdrożono kontrakt](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Wywoływanie funkcji kontraktu

Funkcja **SendRequest** kontraktu **HelloBlockchain** zmienia zmienną stanu **RequestMessage** . Zmiana stanu sieci łańcucha bloków odbywa się za pośrednictwem transakcji. Aby wywołać funkcję **SendRequest** za pośrednictwem transakcji, można użyć strony interakcji z pakietem Microsoft Azure łańcucha bloków Development Kit.

1. Aby móc korzystać z kontraktu inteligentnego, kliknij prawym przyciskiem myszy **HelloBlockchain. peruwiański** i wybierz opcję **Pokaż stronę interakcji z kontraktem inteligentnym** z menu.

    ![Wybierz pozycję Pokaż stronę interakcji z kontraktem inteligentnym z menu](./media/send-transaction/contract-interaction.png)

1. Strona interakcja umożliwia wybranie wdrożonej wersji kontraktu, wywołanie funkcji, wyświetlenie bieżącego stanu i wyświetlenie metadanych.

    ![Przykładowa strona interakcji z kontraktem inteligentnym](./media/send-transaction/interaction-page.png)

1. Aby wywołać funkcję kontraktu inteligentnego, wybierz akcję kontraktu i przekaż argumenty. Wybierz akcję kontraktu **SendRequest** i wprowadź **Hello, łańcucha bloków!** dla parametru **RequestMessage** . Wybierz polecenie **Wykonaj** , aby wywołać funkcję **SendRequest** za pośrednictwem transakcji.

    ![Wykonaj akcję SendRequest](./media/send-transaction/sendrequest-action.png)

Po przetworzeniu transakcji sekcja interakcji odzwierciedla zmiany stanu.

![Zmiany stanu kontraktu](./media/send-transaction/contract-state.png)

Funkcja SendRequest ustawia pola **RequestMessage** i **State** . Bieżący stan dla **RequestMessage** jest argumentem, który przeszedł do **Hello, łańcucha bloków**. Wartość pola **stan** jest **niepożądana**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby nie będą już potrzebne, można je usunąć przez usunięcie `myResourceGroup` grupy zasobów utworzonej w ramach przewodnika Szybki Start dotyczącego *tworzenia elementu członkowskiego łańcucha bloków* .

Aby usunąć grupę zasobów:

1. W Azure Portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć.
1. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usuwanie, wprowadzając nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono Tworzenie przykładowego projektu z jednolitością przy użyciu zestawu Azure łańcucha bloków Development Kit. Skompilowano i wdrożono inteligentny kontrakt o nazwie funkcja za pośrednictwem transakcji w sieci łańcucha bloków Consortium hostowanej w usłudze Azure łańcucha bloków Service.

> [!div class="nextstepaction"]
> [Opracowywanie aplikacji łańcucha bloków przy użyciu usługi Azure łańcucha bloków Service](develop.md)
