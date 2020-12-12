---
title: Łączniki Video Indexer z aplikacją logiki i samouczekem automatyzacji.
description: W tym samouczku pokazano, jak odblokować nowe środowiska i zysków szanse Video Indexer łączniki z aplikacją logiki i automatyzacją.
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 5c545fa7d58d925dfcb9fa98b301c3bb0a20833e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358575"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Samouczek: używanie Video Indexer z aplikacją logiki i automatyzacją

[Interfejs API REST usługi Azure Media Services Video Indexer v2](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) obsługuje komunikację między serwerami i klientami z serwerem oraz pozwala Video Indexer użytkownikom na łatwe Integrowanie danych wideo i audio z logiką aplikacji, odblokowywanie nowych środowisk i możliwości zysków.

Aby ułatwić integrację, obsługujemy łączniki [Logic Apps](https://azure.microsoft.com/services/logic-apps/)   i [automatyzacji](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/),   które są zgodne z naszym interfejsem API. Łączników można użyć do skonfigurowania niestandardowych przepływów pracy w celu efektywnego indeksowania i wyodrębnienia szczegółowych informacji z dużej ilości plików wideo i audio, bez konieczności pisania jednego wiersza kodu. Ponadto przy użyciu łączników integracji zapewnia lepszy wgląd w kondycję przepływu pracy oraz łatwą możliwość jego debugowania.  

Aby pomóc szybko rozpocząć pracę z łącznikami Video Indexer, zajmiemy się przykładową aplikacją logiki i rozwiązaniem automatyzacji, które można skonfigurować. W tym samouczku pokazano, jak skonfigurować przepływy przy użyciu Logic Apps. Jednak edytory i możliwości są prawie identyczne w obu rozwiązaniach, więc diagramy i wyjaśnienia dotyczą zarówno Logic Apps, jak i automatyzacji.

Scenariusz "Przekaż i Indeksuj wideo automatycznie", który został omówiony w tym samouczku, składa się z dwóch różnych przepływów, które współpracują ze sobą. 
* Pierwszy przepływ jest wyzwalany po dodaniu lub zmodyfikowaniu obiektu BLOB na koncie usługi Azure Storage. Przekazuje nowy plik do Video Indexer przy użyciu adresu URL wywołania zwrotnego, aby wysłać powiadomienie po zakończeniu operacji indeksowania. 
* Drugi przepływ jest wyzwalany na podstawie adresu URL wywołania zwrotnego i zapisuje wyodrębnione informacje z powrotem do pliku JSON w usłudze Azure Storage. To dwa podejście przepływu służy do obsługi asynchronicznego przesyłania i indeksowania większych plików. 

Ten samouczek korzysta z aplikacji logiki, aby pokazać, jak:

> [!div class="checklist"]
> * Konfigurowanie przepływu przekazywania plików
> * Konfigurowanie przepływu wyodrębniania JSON

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aby rozpocząć pracę z usługą, musisz mieć konto Video Indexer oraz [uzyskać dostęp do interfejsów API za pomocą klucza interfejsu API](video-indexer-use-apis.md). 
* Konieczne będzie również konto usługi Azure Storage. Zanotuj klucz dostępu do konta magazynu. Utwórz dwa kontenery — jeden do przechowywania filmów wideo w usłudze i jeden do przechowywania szczegółowych danych generowanych przez Video Indexer w programie.  
* Następnie konieczne będzie otwarcie dwóch oddzielnych przepływów na Logic Apps lub w trybie automatyzowania (w zależności od tego, które są używane). 

## <a name="set-up-the-first-flow---file-upload"></a>Skonfiguruj przekazywanie pierwszego pliku przepływu   

Pierwszy przepływ jest wyzwalany za każdym razem, gdy obiekt BLOB zostanie dodany do kontenera usługi Azure Storage. Po wyzwoleniu zostanie utworzony identyfikator URI sygnatury dostępu współdzielonego, który służy do przekazywania i indeksowania wideo w Video Indexer. W tej sekcji utworzysz następujący przepływ. 

![Przepływ przekazywania plików](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Aby skonfigurować pierwszy przepływ, należy podać klucz interfejsu API Video Indexer i poświadczenia usługi Azure Storage. 

![Azure Blob Storage](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Nazwa połączenia i klucz interfejsu API](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> Jeśli wcześniej połączono konto usługi Azure Storage lub konto Video Indexer z aplikacją logiki, szczegóły połączenia są przechowywane i nastąpi automatyczne połączenie. <br/>Połączenie można edytować, klikając pozycję **Zmień połączenie** u dołu usługi Azure Storage (okno magazyn) lub Video Indexer (okno odtwarzacza).

Po nawiązaniu połączenia z usługą Azure Storage i kontami Video Indexer można znaleźć i wybrać wyzwalacz "gdy obiekt BLOB zostanie dodany lub zmodyfikowany" w **projektancie Logic Apps**.

Wybierz kontener, w którym zostaną umieszczone pliki wideo. 

![Zrzut ekranu przedstawia okno dialogowe gdy obiekt BLOB jest dodawany lub modyfikowany, w którym można wybrać kontener.](./media/logic-apps-connector-tutorial/container.png)

Następnie Znajdź i wybierz akcję "Utwórz identyfikator URI SAS według ścieżki". W oknie dialogowym akcji wybierz pozycję Lista plików ścieżka z opcji zawartość dynamiczna.  

Ponadto Dodaj nowy parametr "Protokół dostępu współdzielonego". Wybierz HttpsOnly dla wartości parametru.

![Identyfikator URI SYGNATURy dostępu współdzielonego według ścieżki](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Wypełnij [lokalizację konta](regions.md) i [Identyfikator konta](./video-indexer-use-apis.md#account-id),   Aby uzyskać token konta Video Indexer.

![Uzyskiwanie tokenu dostępu do konta](./media/logic-apps-connector-tutorial/account-access-token.png)

W polu "Przekaż wideo i Indeksuj" Wprowadź wymagane parametry i adres URL wideo. Wybierz pozycję "Dodaj nowy parametr" i wybierz pozycję adres URL wywołania zwrotnego. 

![Przekazywanie i indeksowanie](./media/logic-apps-connector-tutorial/upload-and-index.png)

Adres URL wywołania zwrotnego pozostanie pusty dla tej pory. Dodasz ją dopiero po zakończeniu drugiego przepływu, w którym tworzony jest adres URL wywołania zwrotnego. 

Można użyć wartości domyślnej dla innych parametrów lub ustawić je zgodnie z potrzebami. 

Kliknij przycisk **Zapisz** i przejdźmy, aby skonfigurować drugi przepływ, aby wyodrębnić szczegółowe informacje po zakończeniu przekazywania i indeksowania. 

## <a name="set-up-the-second-flow---json-extraction"></a>Konfigurowanie drugiego wyciągania przepływu — JSON  

Zakończenie przekazywania i indeksowania z pierwszego przepływu spowoduje wysłanie żądania HTTP z poprawnym adresem URL wywołania zwrotnego w celu wyzwolenia drugiego przepływu. Spowoduje to pobranie szczegółowych informacji generowanych przez Video Indexer. W tym przykładzie dane wyjściowe zadania indeksowania będą przechowywane w usłudze Azure Storage.  Jednak można to zrobić z danymi wyjściowymi.  

Utwórz drugi przepływ niezależnie od pierwszego. 

![Przepływ ekstrakcji JSON](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

Aby skonfigurować ten przepływ, należy ponownie podać klucz interfejsu API Video Indexer i poświadczenia usługi Azure Storage. Należy zaktualizować te same parametry, co w przypadku pierwszego przepływu. 

W przypadku wyzwalacza zostanie wyświetlone pole adres URL HTTP POST. Adres URL nie zostanie wygenerowany do momentu zapisania przepływu; Jednak adres URL będzie potrzebny w końcu. Powrócimy do tego. 

Wypełnij [lokalizację konta](regions.md) i [Identyfikator konta](./video-indexer-use-apis.md#account-id),   Aby uzyskać token konta Video Indexer.  

Przejdź do akcji "Pobierz indeks wideo" i Wypełnij wymagane parametry. Dla identyfikatora wideo wpisz następujące wyrażenie: triggerOutputs () ["zapytania"] ["ID"] 

![Informacje o akcji indeksatora wideo](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

To wyrażenie nakazuje programowi connecter uzyskanie identyfikatora wideo z danych wyjściowych wyzwalacza. W takim przypadku dane wyjściowe wyzwalacza będą danymi wyjściowymi "Przekaż wideo i indeks" w pierwszym wyzwalaczu. 

Przejdź do akcji "Utwórz obiekt BLOB" i wybierz ścieżkę do folderu, w którym będziesz zapisywać szczegółowe dane. Ustaw nazwę tworzonego obiektu BLOB. W przypadku zawartości obiektu BLOB Umieść w następującym wyrażeniu: Body ("Get_Video_Index") 

![Akcja tworzenia obiektu BLOB](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

To wyrażenie pobiera dane wyjściowe akcji "Pobierz indeks wideo" z tego przepływu. 

Kliknij pozycję **Zapisz przepływ**. 

Po zapisaniu przepływu w wyzwalaczu zostanie utworzony adres URL POST protokołu HTTP. Skopiuj adres URL z wyzwalacza. 

![Zapisz wyzwalacz adresu URL](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Teraz wróć do pierwszego przepływu i wklej adres URL w akcji "Przekaż wideo i indeks" dla parametru adresu URL wywołania zwrotnego. 

Upewnij się, że oba przepływy są zapisane i że wszystko jest gotowe! 

Wypróbuj nowo utworzoną aplikację logiki lub rozwiązanie do automatyzowania, dodając wideo do kontenera obiektów blob platformy Azure i wróć kilka minut później, aby zobaczyć, że szczegółowe dane pojawiają się w folderze docelowym. 

## <a name="generate-captions"></a>Generuj podpisy

Zapoznaj się z poniższym blogiem dotyczącym kroków, które pokazują, [jak generować podpisy z video Indexer i Logic Apps](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198). 

W tym artykule pokazano również, jak indeksować wideo automatycznie, kopiując je do usługi OneDrive i jak przechowywać podpisy generowane przez Video Indexer w usłudze OneDrive.
 
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym samouczkiem możesz korzystać z tej aplikacji logiki lub rozwiązania do automatyzowania i działania w razie potrzeby. Jeśli jednak nie chcesz zachować tego działania i nie chcesz otrzymywać opłat, Wyłącz oba przepływy, jeśli używasz automatyzacji. Wyłącz oba przepływy, jeśli używasz Logic Apps. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano przykład tylko jednego Video Indexer łączników. Łączników Video Indexer można użyć dla dowolnego wywołania interfejsu API dostarczonego przez Video Indexer. Na przykład: przekazywanie i pobieranie szczegółowych informacji, tłumaczenie wyników, pobieranie elementów widget, a nawet Dostosowywanie modeli. Ponadto można wyzwolić te akcje na podstawie różnych źródeł, takich jak aktualizacje repozytoriów plików lub wysyłanych wiadomości e-mail. Następnie możesz zdecydować się na aktualizację wyników do odpowiedniej infrastruktury lub aplikacji lub wygenerować dowolną liczbę elementów akcji.  

> [!div class="nextstepaction"]
> [Korzystanie z interfejsu API usługi Video Indexer](video-indexer-use-apis.md)

Dodatkowe zasoby można znaleźć w tym dokumencie dotyczącym [indeksatora wideo.](https://docs.microsoft.com/connectors/videoindexer-v2/)
