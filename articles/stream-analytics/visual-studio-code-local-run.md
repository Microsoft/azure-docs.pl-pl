---
title: Przetestuj zadanie Azure Stream Analytics lokalnie z przykładowymi danymi przy użyciu Visual Studio Code
description: W tym artykule opisano sposób testowania zapytań lokalnie z przykładowymi danymi przy użyciu narzędzi Azure Stream Analytics dla Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: how-to
ms.openlocfilehash: bbd83fb3ef3225fc19c48bb4c5962d6559cf32f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97708453"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Testowanie Stream Analytics zapytań lokalnie z przykładowymi danymi przy użyciu Visual Studio Code

Za pomocą Azure Stream Analytics narzędzi do Visual Studio Code można testować Stream Analytics zadania lokalnie z przykładowymi danymi. Wyniki zapytania można znaleźć w plikach JSON w folderze **LocalRunOutputs** projektu.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [zestaw SDK platformy .NET Core](https://dotnet.microsoft.com/download) i ponownie uruchom Visual Studio Code.

* Skorzystaj z tego [przewodnika Szybki Start](quick-create-visual-studio-code.md) , aby dowiedzieć się, jak utworzyć zadanie Stream Analytics przy użyciu Visual Studio Code.

## <a name="prepare-sample-data"></a>Przygotowywanie przykładowych danych

Najpierw należy przygotować przykładowe pliki danych wejściowych. Jeśli na maszynie masz już pliki danych przykładowych, możesz pominąć ten krok i przejść do kolejnego.

1. Kliknij pozycję **Podgląd danych** w pliku konfiguracyjnym danych wejściowych z górnego wiersza. Niektóre dane wejściowe będą pobierane z IoT Hub i wyświetlane w oknie podglądu. Należy pamiętać, że może to chwilę potrwać.

2. Po pobraniu danych kliknij pozycję **Zapisz jako** , aby zapisać dane w pliku lokalnym.

 ![Podgląd danych wejściowych na żywo](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Definiowanie lokalnego Wejścia

1. Kliknij pozycję **input.jsw** obszarze folder danych wejściowych w projekcie Stream Analytics. Następnie wybierz pozycję **Dodaj dane wejściowe lokalnego** z górnego wiersza.

    ![Dodaj lokalne dane wejściowe z projektu](./media/quick-create-visual-studio-code/add-input-from-project.png)

    Możesz również użyć **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń i wprowadzić **ASA: Dodaj dane wejściowe**.

   ![Dodawanie Stream Analytics danych wejściowych w VS Code](./media/quick-create-visual-studio-code/add-input.png)

2. Wybierz pozycję **lokalne dane wejściowe**.

    ![Dodawanie lokalnych danych wejściowych ASA w Visual Studio Code](./media/vscode-local-run/add-local-input.png)

3. Wybierz pozycję **+ nowe lokalne dane wejściowe**.

    ![Dodawanie nowych lokalnych danych wejściowych ASA w Visual Studio Code](./media/vscode-local-run/add-new-local-input.png)

4. Wprowadź ten sam alias wejściowy, który był używany w zapytaniu.

    ![Dodawanie nowego lokalnego aliasu wejściowego ASA](./media/vscode-local-run/new-local-input-alias.png)

5. W nowo wygenerowanym **LocalInput_Input.jsw** pliku wprowadź ścieżkę pliku, w którym znajduje się plik danych lokalnych.

    ![Wprowadź ścieżkę do lokalnego pliku w programie Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Wybierz pozycję **Podgląd danych** , aby wyświetlić podgląd danych wejściowych. Typ serializacji dla danych jest wykrywany automatycznie, jeśli jego kod JSON lub CSV. Użyj selektora, aby wyświetlić dane w formacie **tabeli** lub **nieprzetworzonym** . W poniższej tabeli przedstawiono przykład danych w **formacie tabeli**:

     ![Podgląd danych lokalnych w formacie tabeli](./media/vscode-local-run/local-file-preview-table.png)

    W poniższej tabeli przedstawiono przykład danych w **formacie nieprzetworzonym**:

    ![Podgląd danych lokalnych w formacie nieprzetworzonym](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Uruchom zapytania lokalnie

Wróć do edytora zapytań i wybierz pozycję **Uruchom lokalnie**. Następnie wybierz pozycję **Użyj lokalnych danych wejściowych** z listy rozwijanej.

![Wybierz pozycję Uruchom lokalnie w edytorze zapytań](./media/vscode-local-run/run-locally.png)

![Użyj lokalnych danych wejściowych](./media/vscode-local-run/run-locally-use-local-input.png)

Wynik jest wyświetlany w oknie po prawej stronie. Możesz kliknąć przycisk **Uruchom** , aby przetestować ponownie. Możesz również wybrać pozycję **Otwórz w folderze** , aby wyświetlić pliki wynikowe w Eksploratorze plików, a następnie otworzyć je z innymi narzędziami. Należy pamiętać, że pliki wynikowe są dostępne tylko w formacie JSON.

![Wyświetl wyniki lokalnego uruchomienia](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Następne kroki

* [Przetestuj Azure Stream Analytics zadania lokalnie z danymi wejściowymi na żywo przy użyciu Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Eksplorowanie Azure Stream Analytics zadań z Visual Studio Code (wersja zapoznawcza)](visual-studio-code-explore-jobs.md)
