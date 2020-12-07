---
title: Użyj Visual Studio Code, aby nawiązać połączenie z usługą Azure łańcucha bloków
description: Połącz się z siecią konsorcjum usługi Azure łańcucha bloków, korzystając z rozszerzenia Azure łańcucha bloków Development Kit w Visual Studio Code
ms.date: 12/04/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 6e94d93d91f25c15743c4c467e31de49fd9da41d
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763319"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Szybki Start: używanie Visual Studio Code do nawiązywania połączenia z siecią Azure łańcucha bloków Service Consortium

W tym przewodniku szybki start zainstalujesz rozszerzenie Azure łańcucha bloków Development Kit dla Ethereum Visual Studio Code (VS Code), aby dołączyć do konsorcjum w usłudze Azure łańcucha bloków. Zestaw Azure łańcucha bloków Development Kit upraszcza tworzenie, łączenie, kompilowanie i wdrażanie inteligentnych kontraktów w księgach Ethereum łańcucha bloków.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Kończenie [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków usługi Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Rozszerzenie Azure łańcucha bloków Development Kit dla rozszerzenia Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15. x lub wyższy](https://nodejs.org)
* [Git 2.10. x lub nowszy](https://git-scm.com)
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Interfejs wiersza polecenia ganache 6.0.0](https://github.com/trufflesuite/ganache-cli)

W systemie Windows zainstalowany kompilator języka C++ jest wymagany dla modułu Node-GYP. Możesz użyć narzędzi MSBuild:

* Jeśli zainstalowano program Visual Studio 2017, należy skonfigurować npm do korzystania z narzędzi MSBuild przy użyciu polecenia `npm config set msvs_version 2017 -g`
* Jeśli zainstalowano program Visual Studio 2019, Ustaw ścieżkę Microsoft Build Tools for npm. Na przykład `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* W przeciwnym razie zainstaluj autonomiczne narzędzia programu VS Build przy użyciu `npm install --global windows-build-tools` programu w powłoce poleceń *Uruchom jako administrator* z podwyższonym poziomem uprawnień.

Aby uzyskać więcej informacji na temat węzła Node-GYP, zobacz [repozytorium Node-GYP w witrynie GitHub](https://github.com/nodejs/node-gyp).

### <a name="verify-azure-blockchain-development-kit-environment"></a>Weryfikowanie środowiska zestawu Azure łańcucha bloków Development Kit

Zestaw Azure łańcucha bloków Development Kit sprawdza wymagania wstępne dotyczące środowiska deweloperskiego. Aby zweryfikować środowisko programistyczne:

Z palety poleceń VS Code wybierz pozycję **łańcucha bloków: Pokaż stronę powitalną**.

Pakiet Azure łańcucha bloków Development Kit uruchamia skrypt walidacji, który trwa około minuty. Możesz wyświetlić dane wyjściowe, wybierając pozycję **terminal > nowym terminalu**. Na pasku menu terminalu wybierz kartę Output ( **dane wyjściowe** ) i pozycję **Azure łańcucha bloków** na liście rozwijanej. Pomyślne sprawdzenie poprawności wygląda tak, jak na poniższym obrazie:

![Prawidłowe środowisko programistyczne](./media/connect-vscode/valid-environment.png)

 Jeśli brakuje wymaganego narzędzia, Nowa karta o nazwie **Azure łańcucha bloków Development Kit — wersja zapoznawcza** zawiera listę wymaganych narzędzi z linkami pobierania.

![Aplikacje wymagane przez zestaw dev Kit](./media/connect-vscode/required-apps.png)

Przed przejściem do przewodnika Szybki Start Zainstaluj wszystkie brakujące wymagania wstępne.

## <a name="connect-to-consortium-member"></a>Łączenie z członkiem konsorcjum

Można nawiązać połączenie z członkami konsorcjum przy użyciu rozszerzenia Azure łańcucha bloków Development VS Code Kit. Po nawiązaniu połączenia z konsorcjum można skompilować, skompilować i wdrożyć inteligentne kontrakty w składowej konsorcjum usługi Azure łańcucha bloków.

Jeśli nie masz dostępu do elementu członkowskiego konsorcjum usługi Azure łańcucha bloków, Ukończ wymagania wstępne [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków usługi Azure łańcucha bloków za pomocą interfejsu wiersza polecenia platformy Azure](create-member-cli.md).

1. W okienku Eksploratora VS Code rozwiń rozszerzenie **Azure łańcucha bloków** .
1. Wybierz pozycję **Połącz z siecią**.

   ![Połącz z siecią](./media/connect-vscode/connect-consortium.png)

    Jeśli zostanie wyświetlony monit o uwierzytelnienie platformy Azure, postępuj zgodnie z monitami, aby przeprowadzić uwierzytelnianie przy użyciu przeglądarki.
1. Wybierz pozycję **usługa Azure łańcucha bloków** na liście rozwijanej paleta poleceń.
1. Wybierz subskrypcję i grupę zasobów skojarzoną z elementem członkowskim konsorcjum usługi Azure łańcucha bloków.
1. Wybierz z listy swoją konsorcjum.

Członkowie konsorcjum i łańcucha bloków są wyświetlani na pasku bocznym Eksploratora VS Code.

![Konsorcjum wyświetlane w Eksploratorze](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto rozszerzenia łańcucha bloków Development Kit dla Ethereum VS Code do dołączenia do konsorcjum w usłudze Azure łańcucha bloków. Wypróbuj następny samouczek, aby użyć usługi Azure łańcucha bloków Development Kit dla Ethereum do tworzenia, kompilowania, wdrażania i wykonywania funkcji kontraktu inteligentnego za pośrednictwem transakcji.

> [!div class="nextstepaction"]
> [Twórz, Kompiluj i wdrażaj inteligentne kontrakty w usłudze Azure łańcucha bloków Service](send-transaction.md)