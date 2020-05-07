---
title: Łączniki Video Indexer z aplikacją logiki i samouczekem automatyzacji.
description: W tym samouczku pokazano, jak odblokować nowe środowiska i zysków szanse Video Indexer łączniki z aplikacją logiki i automatyzacją.
author: anikaz
manager: johndeu
ms.author: anzaman
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 05/01/2020
ms.openlocfilehash: 932f52aa694c13fd3696d82872135304a4e41bdc
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801131"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Samouczek: używanie Video Indexer z aplikacją logiki i automatyzacją

[Interfejs API REST usługi Azure Media Services Video Indexer v2](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) obsługuje komunikację między serwerami i klientami z serwerem oraz pozwala Video Indexer użytkownikom na łatwe Integrowanie danych wideo i audio z logiką aplikacji, odblokowywanie nowych środowisk i możliwości zysków.

Aby ułatwić integrację, obsługujemy łączniki [Logic Apps](https://azure.microsoft.com/services/logic-apps/) i [automatyzacji](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/) , które są zgodne z naszym interfejsem API. Łączników można użyć do skonfigurowania niestandardowych przepływów pracy w celu efektywnego indeksowania i wyodrębnienia szczegółowych informacji z dużej ilości plików wideo i audio, bez konieczności pisania jednego wiersza kodu. Ponadto przy użyciu łączników integracji zapewnia lepszy wgląd w kondycję przepływu pracy oraz łatwą możliwość jego debugowania.  

Aby pomóc szybko rozpocząć pracę z łącznikami Video Indexer, zajmiemy się przykładową aplikacją logiki i rozwiązaniem automatyzacji, które można skonfigurować. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Automatyczne przekazywanie i indeksowanie wideo
> * Konfigurowanie przepływu przekazywania plików
> * Konfigurowanie przepływu wyodrębniania JSON

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby zacząć korzystać z programu, konieczne będzie również konto Video Indexer i dostęp do interfejsów API za pomocą klucza interfejsu API. 

Konieczne będzie również konto usługi Azure Storage. Zanotuj klucz dostępu do konta magazynu. Utwórz dwa kontenery — jeden do przechowywania filmów wideo w usłudze i jeden do przechowywania szczegółowych danych generowanych przez Video Indexer w programie.  

Następnie konieczne będzie otwarcie dwóch oddzielnych przepływów na Logic Apps lub w trybie automatyzowania (w zależności od tego, które są używane).  

## <a name="upload-and-index-your-video-automatically"></a>Automatyczne przekazywanie i indeksowanie wideo 

Ten scenariusz składa się z dwóch różnych przepływów, które współpracują ze sobą. Pierwszy przepływ jest wyzwalany po dodaniu lub zmodyfikowaniu obiektu BLOB na koncie usługi Azure Storage. Przekazuje nowy plik do Video Indexer przy użyciu adresu URL wywołania zwrotnego, aby wysłać powiadomienie po zakończeniu operacji indeksowania. Drugi przepływ jest wyzwalany na podstawie adresu URL wywołania zwrotnego i zapisuje wyodrębnione informacje z powrotem do pliku JSON w usłudze Azure Storage. To dwa podejście przepływu służy do obsługi asynchronicznego przesyłania i indeksowania większych plików. 

### <a name="set-up-the-file-upload-flow"></a>Konfigurowanie przepływu przekazywania plików 

Pierwszy przepływ jest wyzwalany za każdym razem, gdy obiekt BLOB zostanie dodany do kontenera usługi Azure Storage. Po wyzwoleniu zostanie utworzony identyfikator URI sygnatury dostępu współdzielonego, który służy do przekazywania i indeksowania wideo w Video Indexer. Zacznij od utworzenia następującego przepływu. 

![Przepływ przekazywania plików](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Aby skonfigurować pierwszy przepływ, należy podać klucz interfejsu API Video Indexer i poświadczenia usługi Azure Storage. 

![Azure Blob Storage](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Nazwa połączenia i klucz interfejsu API](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

Po nawiązaniu połączenia z usługą Azure Storage i kontami Video Indexer przejdź do wyzwalacza "gdy obiekt BLOB zostanie dodany lub zmodyfikowany" i wybierz kontener, w którym zostaną umieszczone pliki wideo. 

![Kontener](./media/logic-apps-connector-tutorial/container.png)

Następnie przejdź do akcji "Tworzenie identyfikatora URI SAS według ścieżki" i wybierz pozycję Lista ścieżek plików z opcji zawartości dynamicznej.  

![Identyfikator URI SYGNATURy dostępu współdzielonego według ścieżki](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Podaj  [lokalizację i identyfikator konta,](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-use-apis#location)Aby uzyskać token konta Video Indexer.

![Uzyskiwanie tokenu dostępu do konta](./media/logic-apps-connector-tutorial/account-access-token.png)

W polu "Przekaż wideo i Indeksuj" Wprowadź wymagane parametry i adres URL wideo. Wybierz pozycję "Dodaj nowy parametr" i wybierz pozycję adres URL wywołania zwrotnego. 

![Przekazywanie i indeksowanie](./media/logic-apps-connector-tutorial/upload-and-index.png)

Adres URL wywołania zwrotnego pozostanie pusty dla tej pory. Dodasz ją dopiero po zakończeniu drugiego przepływu, w którym tworzony jest adres URL wywołania zwrotnego. 

Można użyć wartości domyślnej dla innych parametrów lub ustawić je zgodnie z potrzebami. 

Kliknij przycisk "Zapisz" i przejdźmy, aby skonfigurować drugi przepływ, aby wyodrębnić szczegółowe informacje po zakończeniu przekazywania i indeksowania. 

## <a name="set-up-the-json-extraction-flow"></a>Konfigurowanie przepływu wyodrębniania JSON 

Zakończenie przekazywania i indeksowania z pierwszego przepływu spowoduje wysłanie żądania HTTP z poprawnym adresem URL wywołania zwrotnego w celu wyzwolenia drugiego przepływu. Spowoduje to pobranie szczegółowych informacji generowanych przez Video Indexer. W tym przykładzie dane wyjściowe zadania indeksowania będą przechowywane w usłudze Azure Storage.  Jednak można to zrobić z danymi wyjściowymi.  

Utwórz drugi przepływ niezależnie od pierwszego. 

![Przepływ ekstrakcji JSON](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

Aby skonfigurować ten przepływ, należy ponownie podać klucz interfejsu API Video Indexer i poświadczenia usługi Azure Storage. Należy zaktualizować te same parametry, co w przypadku pierwszego przepływu. 

W przypadku wyzwalacza zostanie wyświetlone pole adres URL HTTP POST. Adres URL nie zostanie wygenerowany do momentu zapisania przepływu; Jednak adres URL będzie potrzebny w końcu. Powrócimy do tego. 

Podaj  [lokalizację i identyfikator konta,](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-use-apis#location)Aby uzyskać token konta Video Indexer.  

Przejdź do akcji "Pobierz indeks wideo" i Wypełnij wymagane parametry. Dla identyfikatora wideo wpisz następujące wyrażenie: triggerOutputs () ["zapytania"] ["ID"] 

![Informacje o akcji indeksatora wideo](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

To wyrażenie nakazuje programowi connecter uzyskanie identyfikatora wideo z danych wyjściowych wyzwalacza. W takim przypadku dane wyjściowe wyzwalacza będą danymi wyjściowymi "Przekaż wideo i indeks" w pierwszym wyzwalaczu. 

Przejdź do akcji "Utwórz obiekt BLOB" i wybierz ścieżkę do folderu, w którym będziesz zapisywać szczegółowe dane. Ustaw nazwę tworzonego obiektu BLOB. W przypadku zawartości obiektu BLOB Umieść w następującym wyrażeniu: Body ("Get_Video_Index") 

![Akcja tworzenia obiektu BLOB](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

To wyrażenie pobiera dane wyjściowe akcji "Pobierz indeks wideo" z tego przepływu. 

Kliknij pozycję "Zapisz przepływ". 

Po zapisaniu przepływu w wyzwalaczu zostanie utworzony adres URL POST protokołu HTTP. Skopiuj adres URL z wyzwalacza. 

![Zapisz wyzwalacz adresu URL](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Teraz wróć do pierwszego przepływu i wklej adres URL w akcji "Przekaż wideo i indeks" dla parametru adresu URL wywołania zwrotnego. 

Upewnij się, że oba przepływy są zapisane i że wszystko jest gotowe! 

Wypróbuj nowo utworzoną aplikację logiki lub rozwiązanie do automatyzowania, dodając wideo do kontenera obiektów blob platformy Azure i wróć kilka minut później, aby zobaczyć, że szczegółowe dane pojawiają się w folderze docelowym. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym samouczkiem możesz korzystać z tej aplikacji logiki lub rozwiązania do automatyzowania i działania w razie potrzeby. Jeśli jednak nie chcesz zachować tego działania i nie chcesz otrzymywać opłat, Wyłącz oba przepływy, jeśli używasz automatyzacji. Wyłącz oba przepływy, jeśli używasz Logic Apps. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano przykład tylko jednego Video Indexer łączników. Łączników Video Indexer można użyć dla dowolnego wywołania interfejsu API dostarczonego przez Video Indexer. Na przykład: przekazywanie i pobieranie szczegółowych informacji, tłumaczenie wyników, pobieranie elementów widget, a nawet Dostosowywanie modeli. Ponadto można wyzwolić te akcje na podstawie różnych źródeł, takich jak aktualizacje repozytoriów plików lub wysyłanych wiadomości e-mail. Następnie możesz zdecydować się na aktualizację wyników do odpowiedniej infrastruktury lub aplikacji lub wygenerować dowolną liczbę elementów akcji.  

> [!div class="nextstepaction"]
> [Korzystanie z interfejsu API usługi Video Indexer](video-indexer-use-apis.md)
