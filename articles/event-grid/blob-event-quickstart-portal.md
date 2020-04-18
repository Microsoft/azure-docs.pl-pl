---
title: 'Szybki start: wysyłanie zdarzeń magazynu obiektów blob do punktu końcowego sieci Web — portal'
description: 'Szybki start: tworzenie konta magazynu obiektów Blob za pomocą usługi Azure Event Grid i witryny Azure Portal oraz subskrybowanie jego zdarzeń. Wyślij zdarzenia do elementu webhook.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 04/16/2020
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: ada451b6bb3578a2903e9bd832b98981d7029d1d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605791"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-with-the-azure-portal"></a>Szybki start: kierowanie zdarzeń magazynu obiektów Blob do punktu końcowego sieci Web za pomocą witryny Azure portal

Azure Event Grid to usługa obsługi zdarzeń dla chmury. W tym artykule użyjesz witryny Azure Portal do utworzenia konta usługi Blob Storage, subskrybowania zdarzeń dla tego magazynu obiektów blob oraz wyzwalania zdarzenia w celu wyświetlenia wyników. Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. Jednak aby uprościć ten artykuł, zdarzenia zostaną wysłane do aplikacji internetowej, która zbiera i wyświetla komunikaty.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Po zakończeniu przekonasz się, że dane zdarzenia zostały wysłane do aplikacji internetowej.

![Wyświetlanie wyników](./media/blob-event-quickstart-portal/view-results.png)

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).

1. Aby utworzyć magazyn Blob Storage, wybierz pozycję **Utwórz zasób**. 

1. Wybierz pozycję **Storage**, aby filtrować według dostępnych opcji, a następnie wybierz pozycję **Konta usługi Storage — Blob, File, Table, Queue**.

   ![Wybieranie magazynu](./media/blob-event-quickstart-portal/create-storage.png)

   Aby zasubskrybować zdarzenia, należy utworzyć konto usługi Storage ogólnego przeznaczenia w wersji 2 lub konto usługi Blob Storage.
   
1. Na stronie **Tworzenie konta magazynu** wykonaj następujące czynności:
    1. Wybierz swoją subskrypcję platformy Azure. 
    2. W przypadku **grupy zasobów**utwórz nową grupę zasobów lub wybierz istniejącą. 
    3. Wprowadź nazwę konta magazynu. 
    4. Wybierz pozycję **Przegląd + utwórz**. 

       ![Kroki początkowe](./media/blob-event-quickstart-portal/provide-blob-values.png)    
    5. Na stronie **Recenzja + utwórz** przejrzyj ustawienia i wybierz pozycję **Utwórz**. 

        >[!NOTE]
        > Tylko konta magazynu typu **StorageV2 (ogólnego przeznaczenia w wersji 2)** i integracji zdarzeń obsługi **BlobStorage.** **Magazyn (genral purpose v1)** *nie* obsługuje integracji z siatką zdarzeń.

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem zdarzeń w ramach usługi Blob Storage utwórzmy punkt końcowy dla komunikatów o zdarzeniach. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. Aby uprościć ten przewodnik Szybki Start, wdrożysz [wstępnie zbudowaną aplikację sieci Web](https://github.com/Azure-Samples/azure-event-grid-viewer), która będzie wyświetlać komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

1. Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. 

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. Na stronie **wdrożenia niestandardowego** wykonaj następujące czynności: 
    1. W przypadku **grupy zasobów**wybierz grupę zasobów utworzoną podczas tworzenia konta magazynu. Będzie łatwiej oczyścić po zakończeniu pracy z samouczka przez usunięcie grupy zasobów.  
    2. W **przypadku nazwy witryny**wprowadź nazwę aplikacji internetowej.
    3. W przypadku **nazwy planu hostingu**wprowadź nazwę planu usługi App Service, który ma być używany do obsługi aplikacji sieci web.
    4. Zaznacz pole wyboru **Zgadzam się z warunkami podanymi powyżej.** 
    5. Wybierz pozycję **Kup**. 

       ![Parametry wdrożenia](./media/blob-event-quickstart-portal/template-deploy-parameters.png)
1. Wdrożenie może potrwać kilka minut. Wybierz pozycję Alerty (ikona dzwonka) w portalu, a następnie wybierz pozycję **Przejdź do grupy zasobów**. 

    ![Alert — przejdź do grupy zasobów](./media/blob-event-quickstart-portal/navigate-resource-group.png)
4. Na stronie **Grupa zasobów na** liście zasobów wybierz utworzoną aplikację sieci web. Zobaczysz również plan usługi app service i konto magazynu na tej liście. 

    ![Wybierz witrynę sieci Web](./media/blob-event-quickstart-portal/resource-group-resources.png)
5. Na stronie **Usługi aplikacji** dla aplikacji sieci Web wybierz adres URL, aby przejść do witryny sieci Web. Adres URL powinien być `https://<your-site-name>.azurewebsites.net`w tym formacie: .
    
    ![Przejdź do witryny sieci Web](./media/blob-event-quickstart-portal/web-site.png)

6. Upewnij się, że widzisz witrynę, ale żadne zdarzenia nie zostały jeszcze opublikowane.

   ![Wyświetlanie nowej witryny](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-blob-storage"></a>Subskrybowanie usługi Blob Storage

Subskrybowanie tematu ma poinformować usługę Event Grid o tym, które zdarzenia chcesz śledzić i gdzie mają być one wysyłane.

1. W portalu przejdź do konta usługi Azure Storage utworzonego wcześniej. W menu po lewej stronie wybierz **pozycję Wszystkie zasoby** i wybierz konto magazynu. 
2. Na stronie **Konto magazynu** wybierz pozycję **Zdarzenia** w menu po lewej stronie.
1. Wybierz kolejno pozycje **Więcej opcji** i **Element webhook**. Wysyłasz zdarzenia do aplikacji przeglądarki przy użyciu haka sieci Web dla punktu końcowego. 

   ![Wybieranie elementu webhook](./media/blob-event-quickstart-portal/select-web-hook.png)
3. Na stronie **Tworzenie subskrypcji zdarzeń** wykonaj następujące czynności: 
    1. Wprowadź **nazwę** subskrypcji zdarzenia.
    2. Wybierz **pozycję Web Hook** dla typu punktu **końcowego**. 

       ![Wybieranie typu punktu końcowego haka sieci Web](./media/blob-event-quickstart-portal/select-web-hook-end-point-type.png)
4. W polu **Punkt końcowy**kliknij pozycję Wybierz **punkt końcowy**i `api/updates` wprowadź adres URL aplikacji internetowej `https://spegridsite.azurewebsites.net/api/updates`i dodaj go do adresu URL strony głównej (na przykład: ), a następnie wybierz pozycję **Potwierdź zaznaczenie**.

   ![Potwierdzanie wyboru punktu końcowego](./media/blob-event-quickstart-portal/confirm-endpoint-selection.png)
5. Teraz na stronie **Utwórz subskrypcję zdarzeń** wybierz pozycję **Utwórz,** aby utworzyć subskrypcję wydarzenia. 

   ![Wybieranie dzienników](./media/blob-event-quickstart-portal/create-subscription.png)

1. Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Aplikacja internetowa zawiera kod do sprawdzania poprawności subskrypcji.

   ![Wyświetlanie zdarzenia subskrypcji](./media/blob-event-quickstart-portal/view-subscription-event.png)

Teraz wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego.

## <a name="send-an-event-to-your-endpoint"></a>Wysyłanie zdarzenia do punktu końcowego

Zdarzenie magazynu Blob Storage jest wyzwalane przez przekazanie pliku. Plik nie wymaga żadnej określonej zawartości. W artykule założono, że masz plik o nazwie testfile.txt, ale możesz użyć dowolnego pliku.

1. W witrynie Azure portal przejdź do konta magazynu obiektów Blob i wybierz **kontenery** na stronie **Przegląd.**

   ![Wybieranie pozycji Obiekty blob](./media/blob-event-quickstart-portal/select-blobs.png)

1. Wybierz pozycję **+ Kontener**. Nadaj kontenerowi nazwę i użyj dowolnego poziomu dostępu, a następnie wybierz pozycję **Utwórz**. 

   ![Dodawanie kontenera](./media/blob-event-quickstart-portal/add-container.png)

1. Wybierz nowy kontener.

   ![Wybieranie kontenera](./media/blob-event-quickstart-portal/select-container.png)

1. Aby przekazać plik, wybierz pozycję **Przekaż**. Na stronie **Przekazywanie obiektów blob** przeglądaj i wybierz plik, który chcesz przekazać do testowania, a następnie wybierz pozycję **Przekaż** na tej stronie. 

   ![Wybieranie pozycji Przekaż](./media/blob-event-quickstart-portal/upload-file.png)

1. Przejdź do pliku testowego i przekaż go.

1. Zdarzenie zostało wyzwolone, a usługa Event Grid wysłała komunikat do punktu końcowego skonfigurowanego podczas subskrybowania. Wiadomość jest w formacie JSON i zawiera tablicę z co najmniej jednym zdarzeniami. W poniższym przykładzie komunikat JSON zawiera tablicę z jednym zdarzeniem. Wyświetl aplikację sieci web i zwróć uwagę, że odebrano zdarzenie **utworzone obiektu blob.** 

   ![Zdarzenie utworzone przez obiekt Blob](./media/blob-event-quickstart-portal/blob-created-event.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz kontynuować pracę z tym zdarzeniem, nie usuwaj zasobów utworzonych w tym artykule. W przeciwnym razie usuń zasoby utworzone w ramach tego artykułu.

Wybierz grupę zasobów, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak tworzyć tematy niestandardowe i subskrypcje zdarzeń. Dowiedz się więcej na temat tego, co może Ci ułatwić usługa Event Grid:

- [Event Grid — informacje](overview.md)
- [Kierowanie zdarzeń usługi Blob Storage do niestandardowego internetowego punktu końcowego](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorowanie zmian maszyn wirtualnych za pomocą usług Azure Event Grid i Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md)
