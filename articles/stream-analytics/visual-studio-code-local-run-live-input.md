---
title: Przetestuj Azure Stream Analytics zapytań lokalnie względem danych wejściowych strumienia na żywo za pomocą Visual Studio Code
description: W tym artykule opisano sposób testowania zapytań lokalnie względem danych wejściowych strumienia na żywo przy użyciu Azure Stream Analytics narzędzi dla Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: how-to
ms.openlocfilehash: 944c1a8cc4606c5e614f17cd0590331826527a7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90892144"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Przetestuj Stream Analytics zapytań lokalnie względem danych wejściowych strumienia na żywo za pomocą Visual Studio Code

Za pomocą Azure Stream Analytics narzędzi do Visual Studio Code można testować swoje zadania Stream Analytics lokalnie w odniesieniu do danych wejściowych strumienia na żywo. Dane wejściowe mogą pochodzić ze źródła, takiego jak Azure Event Hubs lub Azure IoT Hub. Wyniki wyjściowe są wysyłane jako pliki JSON do folderu w projekcie o nazwie **LocalRunOutputs**.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj Visual Studio Code [zestaw .NET Core SDK](https://dotnet.microsoft.com/download) i ponownie uruchom program.

* Skorzystaj z [tego przewodnika Szybki Start](quick-create-visual-studio-code.md) , aby dowiedzieć się, jak utworzyć zadanie Stream Analytics przy użyciu Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Zdefiniuj dane wejściowe strumienia na żywo

1. Kliknij prawym przyciskiem myszy folder **Inputs** w projekcie Stream Analytics. Następnie wybierz pozycję **ASA: Dodaj dane wejściowe** z menu kontekstowego.

   ![Dodawanie danych wejściowych z folderu Inputs](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

   Możesz również wybrać **kombinację klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń i wprowadzić **ASA: Dodaj dane wejściowe**.

   ![Dodawanie Stream Analytics danych wejściowych w Visual Studio Code](./media/quick-create-visual-studio-code/add-input.png)

2. Z listy rozwijanej wybierz typ źródła danych wejściowych.

   ![Wybierz pozycję IoT Hub jako opcję wejściową](./media/quick-create-visual-studio-code/iot-hub.png)

3. Jeśli dodano dane wejściowe z palety poleceń, wybierz skrypt kwerendy Stream Analytics, który będzie używać danych wejściowych. Należy ją automatycznie wypełnić ścieżką pliku **myASAproj. asaql**.

   ![Wybierz skrypt Stream Analytics w Visual Studio Code](./media/quick-create-visual-studio-code/asa-script.png)

4. Wybierz **pozycję Wybierz z subskrypcji platformy Azure** z menu rozwijanego.

    ![Wybierz z subskrypcji](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Skonfiguruj nowo wygenerowany plik JSON. Możesz użyć funkcji CodeLens, aby pomóc wprowadzić ciąg, wybrać z listy rozwijanej lub zmienić tekst bezpośrednio w pliku. Poniższy zrzut ekranu przedstawia **wybór z subskrypcji** jako przykładu.

   ![Konfigurowanie danych wejściowych w Visual Studio Code](./media/quick-create-visual-studio-code/configure-input.png)

## <a name="preview-input"></a>Podgląd danych wejściowych

Aby upewnić się, że dane wejściowe są przychodzące, wybierz pozycję **Podgląd danych** w pliku konfiguracji danych wejściowych na żywo z górnego wiersza. Niektóre dane wejściowe pochodzą z Centrum IoT Hub i są wyświetlane w oknie podglądu. Wyświetlenie podglądu może potrwać kilka sekund.

 ![Podgląd danych wejściowych na żywo](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Uruchom zapytania lokalnie

Wróć do edytora zapytań i wybierz pozycję **Uruchom lokalnie**. Następnie wybierz pozycję **Użyj danych wejściowych na żywo** z listy rozwijanej.

![Wybierz pozycję "Uruchom lokalnie" w edytorze zapytań](./media/vscode-local-run/run-locally.png)

![Wybierz pozycję "Użyj danych wejściowych na żywo"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Wynik jest wyświetlany w oknie po prawej stronie i odświeżany co 3 sekundy. Możesz wybrać polecenie **Uruchom** , aby wykonać operację ponownie. Możesz również wybrać pozycję **Otwórz w folderze** , aby wyświetlić pliki wyników w Eksploratorze plików i otworzyć je za pomocą Visual Studio Code lub narzędzia, takiego jak program Excel. Pliki wynikowe są dostępne tylko w formacie JSON.

Domyślna godzina rozpoczęcia tworzenia danych wyjściowych dla zadania jest ustawiona na **teraz**. Możesz dostosować czas, wybierając przycisk **wyjściowy czas rozpoczęcia** w oknie wyników.

![Wyświetl wyniki lokalnego uruchomienia](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Następne kroki

* [Eksplorowanie Azure Stream Analytics zadań z Visual Studio Code (wersja zapoznawcza)](visual-studio-code-explore-jobs.md)

* [Konfigurowanie potoków ciągłej integracji/ciągłego wdrażania przy użyciu pakietu npm](setup-cicd-vs-code.md)
