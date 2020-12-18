---
title: Testowanie Azure Stream Analytics zapytań lokalnie w programie Visual Studio
description: W tym artykule opisano sposób testowania zapytań lokalnie za pomocą narzędzi Azure Stream Analytics Tools for Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: a921e51a3c63828602e7e21bfc96f507649236a5
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97682838"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Testowanie Stream Analytics zapytań lokalnie za pomocą programu Visual Studio

Za pomocą narzędzi Azure Stream Analytics Tools for Visual Studio można testować zadania Stream Analytics lokalnie z przykładowymi danymi lub [danymi dynamicznymi](stream-analytics-live-data-local-testing.md). 

Skorzystaj z tego [przewodnika Szybki Start](stream-analytics-quick-create-vs.md) , aby dowiedzieć się, jak utworzyć zadanie Stream Analytics przy użyciu programu Visual Studio.

## <a name="test-your-query"></a>Testowanie zapytania

W projekcie Azure Stream Analytics kliknij dwukrotnie pozycję **Script. asaql** , aby otworzyć skrypt w edytorze. Można skompilować zapytanie, aby sprawdzić, czy występują błędy składniowe. Edytor zapytań obsługuje funkcję IntelliSense, kolorowanie składni i znacznik błędu.

![Edytor zapytań](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Dodawanie lokalnych danych wejściowych

Aby sprawdzić poprawność zapytania względem lokalnych danych statycznych, kliknij prawym przyciskiem myszy dane wejściowe i wybierz polecenie **Dodaj lokalne dane wejściowe**.
   
![Zrzut ekranu, który podświetla opcję menu Dodaj dane wejściowe.](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
W oknie podręcznym wybierz pozycję dane przykładowe z ścieżki lokalnej i **Zapisz**.
   
![Dodawanie lokalnych danych wejściowych](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Plik o nazwie **local_EntryStream.json** jest automatycznie dodawany do folderu Inputs.
   
![Lista plików lokalnych folderów wejściowych](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Wybierz pozycję **Uruchom lokalnie** w edytorze zapytań. Możesz też nacisnąć klawisz F5.
   
![Uruchom lokalnie](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
Dane wyjściowe można wyświetlać w formacie tabeli bezpośrednio z programu Visual Studio.

![Dane wyjściowe w formacie tabeli](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Ścieżkę wyjściową można znaleźć z poziomu danych wyjściowych konsoli. Naciśnij dowolny klawisz, aby otworzyć folder wynik.
   
![Uruchamianie lokalne](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Sprawdź wyniki w folderze lokalnym.
   
![Wynik folderu lokalnego](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Przykładowe dane wejściowe
Możesz również zbierać przykładowe dane wejściowe ze źródeł danych wejściowych do pliku lokalnego. Kliknij prawym przyciskiem myszy plik konfiguracji wejściowej, a następnie wybierz polecenie **dane przykładowe**. 

![Dane przykładowe](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Można tylko próbkować strumieniowe przesyłanie danych z Event Hubs lub centrów IoT. Inne źródła danych wejściowych nie są obsługiwane. W podręcznym oknie dialogowym wprowadź ścieżkę lokalną, aby zapisać przykładowe dane, a następnie wybierz pozycję **przykład**.

![Konfiguracja przykładowych danych](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Postęp można zobaczyć w oknie **danych wyjściowych** . 

![Przykładowe dane wyjściowe danych](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
* [Lokalne testowanie danych na żywo przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio (wersja zapoznawcza)](stream-analytics-live-data-local-testing.md)
* [Ciągła integracja i ciągłe opracowywanie za pomocą narzędzi usługi Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
