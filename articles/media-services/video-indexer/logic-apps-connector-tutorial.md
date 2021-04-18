---
title: Samouczek Video Indexer łączników za pomocą aplikacji logiki Power Automate samouczka.
description: W tym samouczku pokazano, jak odblokować nowe doświadczenia i możliwości zarabiania Video Indexer łącznikami za pomocą aplikacji logiki i Power Automate.
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: caff6a2496e907da1bdc140860c47476d1842df4
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600679"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Samouczek: używanie Video Indexer z aplikacją logiki i Power Automate

Interfejs API REST usługi Azure Media Services Video Indexer w wersji [2](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Video) obsługuje komunikację serwer-serwer i klient-serwer oraz umożliwia użytkownikom usługi Video Indexer łatwe integrowanie szczegółowych informacji o wideo i dźwięku z logiką aplikacji, co pozwala odblokować nowe doświadczenia i możliwości zarabiania.

Aby jeszcze bardziej ułatwić integrację, obsługujemy [łączniki Logic Apps](https://azure.microsoft.com/services/logic-apps/)i    [Power Automate,](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/)które są zgodne z   naszym interfejsem API. Łączników można użyć do skonfigurowania niestandardowych przepływów pracy w celu efektywnego indeksowania i wyodrębniania szczegółowych informacji z dużej ilości plików wideo i audio bez konieczności pisania jednego wiersza kodu. Ponadto użycie łączników do integracji zapewnia lepszy wgląd w kondycję przepływu pracy i łatwy sposób debugowania go.  

Aby szybko rozpocząć pracę z łącznikami Video Indexer, wykonajmy przewodnik po przykładowej aplikacji logiki i Power Automate rozwiązanie, które można skonfigurować. W tym samouczku pokazano, jak skonfigurować przepływy przy użyciu Logic Apps. Jednak edytory i możliwości są prawie identyczne w obu rozwiązaniach, dlatego diagramy i wyjaśnienia mają zastosowanie zarówno do Logic Apps, jak i Power Automate.

Scenariusz "automatyczne przekazywanie i indeksowanie wideo" w tym samouczku składa się z dwóch różnych przepływów, które współpracują ze sobą. 
* Pierwszy przepływ jest wyzwalany po dodaniu lub zmodyfikowaniu obiektu blob na koncie usługi Azure Storage. Nowy plik jest przesyłany do usługi Video Indexer z adresem URL wywołania zwrotnego w celu wysłania powiadomienia po zakończeniu operacji indeksowania. 
* Drugi przepływ jest wyzwalany na podstawie adresu URL wywołania zwrotnego i zapisuje wyodrębnione szczegółowe informacje z powrotem w pliku JSON w usłudze Azure Storage. Ta metoda dwóch przepływów jest używana do efektywnej obsługi asynchronicznego przekazywania i indeksowania większych plików. 

Ten samouczek używa aplikacji logiki, aby pokazać, jak:

> [!div class="checklist"]
> * Konfigurowanie przepływu przekazywania plików
> * Konfigurowanie przepływu wyodrębniania JSON

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aby rozpocząć, musisz mieć konto Video Indexer z dostępem do interfejsów [API za pośrednictwem klucza interfejsu API.](video-indexer-use-apis.md) 
* Potrzebne będzie również konto usługi Azure Storage. Zanotuj klucz dostępu dla konta usługi Storage. Utwórz dwa kontenery — jeden do przechowywania filmów wideo, a jeden do przechowywania szczegółowych informacji generowanych przez Video Indexer danych.  
* Następnie należy otworzyć dwa oddzielne przepływy na Logic Apps lub Power Automate (w zależności od tego, którego przepływu używasz). 

## <a name="set-up-the-first-flow---file-upload"></a>Konfigurowanie pierwszego przepływu — przekazywanie plików   

Pierwszy przepływ jest wyzwalany za każdym razem, gdy obiekt blob zostanie dodany do kontenera usługi Azure Storage. Po wyzwoleniu spowoduje to utworzenie sygnatury URI sygnatury dostępu współdzielonego, który umożliwia przekazywanie i indeksowanie wideo w Video Indexer. W tej sekcji utworzysz następujący przepływ. 

![Przepływ przekazywania plików](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Aby skonfigurować pierwszy przepływ, musisz podać klucz interfejsu API Video Indexer poświadczeń usługi Azure Storage. 

![Azure Blob Storage](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Nazwa połączenia i klucz interfejsu API](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> Jeśli wcześniej połączono konto usługi Azure Storage lub konto Video Indexer z aplikacją logiki, szczegóły połączenia są przechowywane i połączenie zostanie nawiązaniu automatycznie. <br/>Możesz edytować połączenie,  klikając pozycję Zmień połączenie w dolnej części okna usługi Azure Storage lub Video Indexer (okno odtwarzacza).

Po nawiązania połączenia z usługą Azure Storage i Video Indexer kont znajdź i wybierz wyzwalacz "Po dodaniu lub zmodyfikowaniu obiektu blob" w **programie Logic Apps Designer.**

Wybierz kontener, w którym będą umieszczane pliki wideo. 

![Zrzut ekranu przedstawia okno dialogowe Po dodaniu lub zmodyfikowaniu obiektu blob, w którym można wybrać kontener.](./media/logic-apps-connector-tutorial/container.png)

Następnie znajdź i wybierz akcję "Utwórz sygnaturę URI sygnatury dostępu współdzielonego według ścieżki". W oknie dialogowym akcji wybierz pozycję Lista ścieżek plików z opcji Zawartość dynamiczna.  

Ponadto dodaj nowy parametr "Shared Access Protocol". Jako wartość parametru wybierz HttpsOnly.

![Sygnatura dostępu współdzielonego według ścieżki](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Wypełnij [lokalizację konta i](regions.md) [identyfikator konta,](./video-indexer-use-apis.md#account-id)   aby uzyskać token Video Indexer konta.

![Uzyskiwanie tokenu dostępu do konta](./media/logic-apps-connector-tutorial/account-access-token.png)

W polu "Przekaż wideo i indeks" wypełnij wymagane parametry i adres URL wideo. Wybierz pozycję "Dodaj nowy parametr" i wybierz pozycję Adres URL wywołania zwrotnego. 

![Przekazywanie i indeksowanie](./media/logic-apps-connector-tutorial/upload-and-index.png)

Na razie adres URL wywołania zwrotnego będzie pusty. Dodasz go dopiero po zakończeniu drugiego przepływu, w którym zostanie utworzony adres URL wywołania zwrotnego. 

Możesz użyć wartości domyślnej dla innych parametrów lub ustawić je zgodnie z potrzebami. 

Kliknij **przycisk** Zapisz i przejdźmy do konfigurowania drugiego przepływu w celu wyodrębnienia szczegółowych informacji po zakończeniu przekazywania i indeksowania. 

## <a name="set-up-the-second-flow---json-extraction"></a>Konfigurowanie drugiego przepływu — wyodrębnianie danych JSON  

Ukończenie przekazywania i indeksowania z pierwszego przepływu spowoduje wysłanie żądania HTTP z poprawnym adresem URL wywołania zwrotnego w celu wyzwolenia drugiego przepływu. Następnie pobierze szczegółowe informacje wygenerowane przez Video Indexer. W tym przykładzie dane wyjściowe zadania indeksowania będą przechowywane w usłudze Azure Storage.  Jednak to Od Ciebie należy, co można zrobić z danych wyjściowych.  

Utwórz drugi przepływ oddzielnie od pierwszego. 

![Przepływ wyodrębniania JSON](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

Aby skonfigurować ten przepływ, należy ponownie podać klucz Video Indexer API i poświadczenia usługi Azure Storage. Musisz zaktualizować te same parametry co w przypadku pierwszego przepływu. 

W przypadku wyzwalacza zostanie wyświetlony adres URL żądania HTTP POST. Adres URL zostanie wygenerowany dopiero po zapisaniu przepływu. Adres URL będzie jednak potrzebny w końcu. Wrócimy do tego. 

Wypełnij [lokalizację konta i](regions.md) identyfikator [konta,](./video-indexer-use-apis.md#account-id)   aby uzyskać token Video Indexer konta.  

Przejdź do akcji "Pobierz indeks wideo" i podaj wymagane parametry. W przypadku identyfikatora wideo umieść następujące wyrażenie: triggerOutputs()['queries']['id'] 

![informacje o akcji indeksatora wideo](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

To wyrażenie nakazuje połącznikowi uzyskiwanie identyfikatora wideo z danych wyjściowych wyzwalacza. W takim przypadku dane wyjściowe wyzwalacza to dane wyjściowe "Przekaż wideo i indeks" w pierwszym wyzwalaczu. 

Przejdź do akcji "Utwórz obiekt blob" i wybierz ścieżkę do folderu, w którym będą zapisywane szczegółowe informacje. Ustaw nazwę obiektu blob, który tworzysz. W przypadku zawartości obiektu blob umieść w następującym wyrażeniu: body('Get_Video_Index') 

![Tworzenie akcji obiektu blob](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

To wyrażenie pobiera dane wyjściowe akcji "Pobierz indeks wideo" z tego przepływu. 

Kliknij **pozycję Zapisz przepływ.** 

Po zapisaniu przepływu w wyzwalaczu jest tworzony adres URL żądania HTTP POST. Skopiuj adres URL z wyzwalacza. 

![Wyzwalacz Zapisz adres URL](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Teraz wróć do pierwszego przepływu i wklej adres URL w akcji "Przekaż wideo i indeks" dla parametru adresu URL wywołania zwrotnego. 

Upewnij się, że oba przepływy zostały zapisane i że wszystko jest w dobrym stanie. 

Wypróbuj nowo utworzoną aplikację logiki lub rozwiązanie Power Automate, dodając wideo do kontenera obiektów blob platformy Azure i wróć kilka minut później, aby zobaczyć, że szczegółowe informacje są wyświetlane w folderze docelowym. 

## <a name="generate-captions"></a>Generowanie podpisów

Zapoznaj się z następującym blogiem, aby dowiedzieć się, jak generować podpisy za pomocą Video Indexer [i Logic Apps](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198). 

W tym artykule pokazano również, jak automatycznie indeksować wideo przez skopiowanie go do usługi OneDrive oraz jak przechowywać napisy wygenerowane przez program Video Indexer usłudze OneDrive.
 
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym samouczkiem możesz zachować tę aplikację logiki lub Power Automate działające, jeśli to konieczne. Jeśli jednak nie chcesz, aby to działało i nie chcesz być rozliczane, wyłącz oba przepływy, jeśli używasz Power Automate. Wyłącz oba przepływy, jeśli używasz Logic Apps. 

## <a name="next-steps"></a>Następne kroki

Ten samouczek pokazuje tylko jeden przykład Video Indexer łączników. Łączników interfejsu Video Indexer można używać dla dowolnego wywołania interfejsu API dostarczonego przez Video Indexer. Na przykład: przekazywanie i pobieranie szczegółowych informacji, tłumaczenie wyników, pobieranie osadzalnych widżetów, a nawet dostosowywanie modeli. Ponadto możesz wyzwolić te akcje na podstawie różnych źródeł, takich jak aktualizacje wysyłanych repozytoriów plików lub wiadomości e-mail. Następnie możesz zaktualizować wyniki do odpowiedniej infrastruktury lub aplikacji albo wygenerować dowolną liczbę elementów akcji.  

> [!div class="nextstepaction"]
> [Korzystanie z interfejsu API usługi Video Indexer](video-indexer-use-apis.md)

Aby uzyskać dodatkowe zasoby, zapoznaj się z tym dokumentem w [indeksatorze wideo.](/connectors/videoindexer-v2/)
