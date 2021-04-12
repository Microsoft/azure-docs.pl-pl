---
title: Połącz z wystąpieniem obliczeniowym w Visual Studio Code (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak nawiązać połączenie z wystąpieniem obliczeniowym Azure Machine Learning w programie Visual Studio Code, aby uruchamiać interaktywne Jupyter Notebook i zdalne obciążenia programistyczne.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 04/08/2021
ms.openlocfilehash: 14f0d15d48193267c224f3497c24651ca3249b0b
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028594"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Nawiązywanie połączenia z wystąpieniem obliczeniowym Azure Machine Learning w Visual Studio Code (wersja zapoznawcza)

W tym artykule dowiesz się, jak nawiązać połączenie z wystąpieniem obliczeniowym Azure Machine Learning przy użyciu Visual Studio Code.

[Wystąpienie obliczeniowe Azure Machine Learning](concept-compute-instance.md) jest w pełni zarządzaną stacją roboczą opartą na chmurze dla analityków danych i udostępniają funkcje zarządzania i gotowości przedsiębiorstwa dla administratorów IT.

Istnieją dwa sposoby nawiązywania połączenia z wystąpieniem obliczeniowym z Visual Studio Code:

* Zdalne wystąpienie obliczeniowe. Ta opcja zapewnia w pełni funkcjonalne środowisko programistyczne do kompilowania projektów uczenia maszynowego.
* Zdalny serwer Jupyter Notebook. Ta opcja pozwala ustawić wystąpienie obliczeniowe jako serwer zdalny Jupyter Notebook.

## <a name="configure-a-remote-compute-instance"></a>Konfigurowanie zdalnego wystąpienia obliczeniowego

Aby skonfigurować zdalne wystąpienie obliczeniowe do celów deweloperskich, należy spełnić kilka wymagań wstępnych.

* Azure Machine Learning Visual Studio Code rozszerzenia. Aby uzyskać więcej informacji, zobacz [Przewodnik konfigurowania rozszerzenia Visual Studio Code Azure Machine Learning](tutorial-setup-vscode-extension.md).
* Azure Machine Learning obszar roboczy. [Użyj rozszerzenia Azure Machine Learning Visual Studio Code, aby utworzyć nowy obszar roboczy](how-to-manage-resources-vscode.md#create-a-workspace) , jeśli jeszcze go nie masz.
* Wystąpienie obliczeniowe Azure Machine Learning. [Użyj rozszerzenia Azure Machine Learning Visual Studio Code, aby utworzyć nowe wystąpienie obliczeniowe](how-to-manage-resources-vscode.md#create-compute-instance) , jeśli go nie masz.

Aby nawiązać połączenie ze zdalnym wystąpieniem obliczeniowym:

# <a name="vs-code"></a>[VS Code](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Azure Machine Learning rozszerzenie

1. W VS Code Uruchom rozszerzenie Azure Machine Learning.
1. Rozwiń węzeł **wystąpienia obliczeniowe** w rozszerzeniu.
1. Kliknij prawym przyciskiem myszy wystąpienie obliczeniowe, z którym chcesz nawiązać połączenie, i wybierz polecenie **Połącz z wystąpieniem obliczeniowym**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="Połącz z wystąpieniem obliczeniowym Visual Studio Code rozszerzenie Azure ML" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>Paleta poleceń

1. W VS Code Otwórz paletę poleceń, wybierając pozycję **wyświetl > paletę poleceń**.
1. Wprowadź tekst w polu tekstowym **Azure ml: Połącz z wystąpieniem obliczeniowym**.
1. Wybierz subskrypcję.
1. Wybierz obszar roboczy.
1. Wybierz wystąpienie obliczeniowe lub Utwórz nowe.

# <a name="studio"></a>[Studio](#tab/studio)

Przejdź do [ml.Azure.com](https://ml.azure.com)

> [!IMPORTANT]
> Aby nawiązać połączenie ze zdalnym wystąpieniem obliczeniowym z programu Visual Studio Code, upewnij się, że konto, na którym użytkownik jest zalogowany w programie Azure Machine Learning Studio, jest takie samo, jak używane w Visual Studio Code.

### <a name="compute"></a>Compute

1. Wybierz kartę **obliczenia**
1. W kolumnie *Identyfikator URI aplikacji* wybierz **vs Code** dla wystąpienia obliczeniowego, z którym chcesz nawiązać połączenie.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="Nawiązywanie połączenia z wystąpieniem obliczeniowym VS Code Azure ML Studio" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>Notes

1. Wybierz kartę **Notes**
1. Na karcie *Notes* wybierz plik, który chcesz edytować.
1. Wybierz pozycję **redaktorzy > Edytuj w vs Code (wersja zapoznawcza)**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="Połącz z wystąpieniem obliczeniowym VS Code notesem platformy Azure ML" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

---

Nowe okno zostanie uruchomione dla zdalnego wystąpienia obliczeniowego. Podczas próby nawiązania połączenia ze zdalnym wystąpieniem obliczeniowym wykonywane są następujące zadania:

1. Autoryzacja. Niektóre testy są wykonywane w celu upewnienia się, że użytkownik próbujący nawiązać połączenie może korzystać z wystąpienia obliczeniowego.
1. VS Code serwer zdalny jest zainstalowany w wystąpieniu obliczeniowym.
1. Połączenie protokołu WebSocket jest nawiązywane na potrzeby interakcji w czasie rzeczywistym.

Gdy połączenie zostanie nawiązane, zostanie utrwalone. Token jest wystawiany na początku sesji, która jest automatycznie odświeżana w celu utrzymania połączenia z wystąpieniem obliczeniowym.

Po nawiązaniu połączenia ze zdalnym wystąpieniem obliczeniowym Użyj edytora, aby:

* [Twórz pliki i zarządzaj nimi na zdalnym wystąpieniu obliczeniowym lub w udziale plików](https://code.visualstudio.com/docs/editor/codebasics).
* Użyj [zintegrowanego terminalu vs Code](https://code.visualstudio.com/docs/editor/integrated-terminal) do [uruchamiania poleceń i aplikacji na zdalnym wystąpieniu obliczeniowym](how-to-access-terminal.md).
* [Debugowanie skryptów i aplikacji](https://code.visualstudio.com/Docs/editor/debugging)
* [Zarządzanie repozytoriami Git za pomocą VS Code](concept-train-model-git-integration.md)

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Skonfiguruj wystąpienie obliczeniowe jako zdalny serwer notesu

Aby skonfigurować wystąpienie obliczeniowe jako serwer zdalny Jupyter Notebook należy spełnić kilka wymagań wstępnych:

* Azure Machine Learning Visual Studio Code rozszerzenia. Aby uzyskać więcej informacji, zobacz [Przewodnik konfigurowania rozszerzenia Visual Studio Code Azure Machine Learning](tutorial-setup-vscode-extension.md).
* Azure Machine Learning obszar roboczy. [Użyj rozszerzenia Azure Machine Learning Visual Studio Code, aby utworzyć nowy obszar roboczy](how-to-manage-resources-vscode.md#create-a-workspace) , jeśli jeszcze go nie masz.

Aby nawiązać połączenie z wystąpieniem obliczeniowym:

1. Otwórz Jupyter Notebook w Visual Studio Code.
1. Po załadowaniu zintegrowanego środowiska notesu wybierz opcję **serwer Jupyter**.

    > [!div class="mx-imgBorder"]
    > ![Lista rozwijana uruchamiania serwera zdalnego Jupyter Notebook Azure Machine Learning](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Alternatywnie można również użyć palety poleceń:

    1. Otwórz paletę poleceń, wybierając pozycję **wyświetl > paleta poleceń** na pasku menu.
    1. Wprowadź tekst w polu tekstowym `Azure ML: Connect to Compute instance Jupyter server` .

1. Wybierz `Azure ML Compute Instances` z listy Opcje serwera Jupyter.
1. Wybierz swoją subskrypcję z listy subskrypcji. Jeśli wcześniej skonfigurowano domyślny obszar roboczy Azure Machine Learning, ten krok zostanie pominięty.
1. Wybierz obszar roboczy.
1. Wybierz wystąpienie obliczeniowe z listy. Jeśli go nie masz, wybierz pozycję **Utwórz nowe wystąpienie usługi Azure środowisko obliczeniowe usługi ml** i postępuj zgodnie z monitami, aby go utworzyć.
1. Aby zmiany zaczęły obowiązywać, należy ponownie załadować Visual Studio Code.
1. Otwórz Jupyter Notebook i uruchom komórkę.

> [!IMPORTANT]
> Aby nawiązać połączenie, **należy** uruchomić komórkę.

W tym momencie można nadal uruchamiać komórki w Jupyter Notebook.

> [!TIP]
> Możesz również współpracować z plikami skryptów języka Python (. PR) zawierającymi komórki kodu podobne do Jupyter. Aby uzyskać więcej informacji, zobacz [dokumentację interaktywną języka Python Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu Visual Studio Code zdalnej, można użyć wystąpienia obliczeniowego jako zdalnego obliczenia z Visual Studio Code do [interaktywnego debugowania kodu](how-to-debug-visual-studio-code.md).

[Samouczek: uczenie swojego pierwszego modelu ml](tutorial-1st-experiment-sdk-train.md) pokazuje, jak używać wystąpienia obliczeniowego z zintegrowanym notesem.
