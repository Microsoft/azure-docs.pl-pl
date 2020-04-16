---
title: Ładowanie danych z magazynu sap business
description: Kopiowanie danych z magazynu SAP Business Warehouse (BW) za pomocą usługi Azure Data Factory
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 96b23696164514ad2f16de72f0f76aa237ffce2e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415837"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Kopiowanie danych z magazynu SAP Business przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule pokazano, jak używać usługi Azure Data Factory do kopiowania danych z magazynu biznesowego SAP (BW) za pośrednictwem usługi Open Hub do usługi Azure Data Lake Storage Gen2. Podobny proces można użyć do skopiowania danych do innych [obsługiwanych magazynów danych ujścia.](copy-activity-overview.md#supported-data-stores-and-formats)

> [!TIP]
> Aby uzyskać ogólne informacje dotyczące kopiowania danych z sap BW, w tym integracji SAP BW Open Hub i przepływu ekstrakcji delta, zobacz [Kopiowanie danych z magazynu SAP Business za pośrednictwem usługi Open Hub przy użyciu usługi Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Wymagania wstępne

- **Usługa Azure Data Factory**: Jeśli jej nie masz, wykonaj kroki, aby [utworzyć fabrykę danych.](quickstart-create-data-factory-portal.md#create-a-data-factory)

- **SAP BW Open Hub Destination (OHD) z typem docelowym "Tabela bazy danych":** Aby utworzyć OHD lub sprawdzić, czy ohd jest poprawnie skonfigurowany do integracji fabryki danych, zobacz [SAP BW Open Hub Destination konfiguracji](#sap-bw-open-hub-destination-configurations) tego artykułu.

- **Użytkownik SAP BW potrzebuje następujących uprawnień:**

  - Autoryzacja dla wywołań funkcji zdalnych (RFC) i SAP BW.
  - Uprawnienia do działania "Wykonywanie" obiektu autoryzacji **S_SDSAUTH.**

- **Środowisko [uruchomieniowe integracji hostowanego samodzielnie (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) z łącznikiem SAP .NET 3.0**. Wykonaj następujące czynności konfiguracji:

  1. Zainstaluj i zarejestruj środowisko uruchomieniowe integracji hostowanego samodzielnie w wersji 3.13 lub nowszej. (Ten proces jest opisany w dalszej części tego artykułu).

  2. Pobierz [64-bitowy łącznik SAP dla firmy Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) z witryny firmy SAP i zainstaluj go na tym samym komputerze co samodzielnie hostowany podczerwony. Podczas instalacji upewnij się, że w oknie dialogowym **Ustawienia opcjonalne** wybrano opcję **Zainstaluj zestawy do plików GAC,** jak pokazano na poniższej ilustracji:

     ![Konfigurowanie okna dialogowego Łącznik SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Wykonaj pełną kopię z SAP BW Open Hub

W witrynie Azure Portal przejdź do swojej fabryki danych. Wybierz **pozycję Monitor & autora,** aby otworzyć interfejs użytkownika fabryki danych na osobnej karcie.

1. Na stronie **Rozpocznijmy pracę** wybierz pozycję **Kopiuj dane,** aby otworzyć narzędzie Kopiuj dane.

2. Na stronie **Właściwości** określ **nazwę zadania,** a następnie wybierz pozycję **Dalej**.

3. Na stronie **Magazyn danych źródłowych** wybierz pozycję **+Utwórz nowe połączenie**. Wybierz **SAP BW Open Hub** z galerii łączników, a następnie wybierz pozycję **Kontynuuj**. Aby filtrować łączniki, można wpisać **sap** w polu wyszukiwania.

4. Na stronie **Określ połączenie SAP BW Open Hub** wykonaj następujące kroki, aby utworzyć nowe połączenie.

   ![Tworzenie strony usługi połączonej z usługą SAP BW Open Hub](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Z listy **Connect via integration runtime** wybierz istniejącą, samodzielnie hostowane środowisko IR. Możesz też utworzyć, jeśli go jeszcze nie masz.

      Aby utworzyć nową, samodzielnie hostowane podczerwone podczerwone, wybierz **+Nowy**, a następnie wybierz pozycję **Samodzielne hostowane**. Wprowadź **nazwę**, a następnie wybierz pozycję **Dalej**. Wybierz **opcję Ekspresowa konfiguracja** do zainstalowania na bieżącym komputerze lub wykonaj **instrukcje konfiguracji ręczne,** które są dostarczane.

      Jak wspomniano w [wymagania wstępne,](#prerequisites)upewnij się, że masz SAP Connector dla microsoft .NET 3.0 zainstalowany na tym samym komputerze, na którym jest uruchomiony własny hostowany podczerwony.

   2. Wpisz **nazwę serwera**SAP BW , **numer systemu, identyfikator** **klienta,** **język** (jeśli inny niż **EN),** **nazwę użytkownika**i **hasło**.

   3. Wybierz **opcję Testuj połączenie,** aby sprawdzić poprawność ustawień, a następnie wybierz pozycję **Zakończ**.

   4. Zostanie utworzone nowe połączenie. Wybierz opcję **Dalej**.

5. Na stronie **Wybierz miejsca docelowe otwartego centrum** przejrzyj miejsca docelowe open hub, które są dostępne w systemie SAP BW. Wybierz OHD do skopiowania danych, a następnie wybierz **przycisk Dalej**.

   ![Wybierz tabelę SAP BW Open Hub Destination](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Określ filtr, jeśli jest potrzebny. Jeśli identyfikator OHD zawiera tylko dane z wykonania pojedynczego procesu transferu danych (DTP) z identyfikatorem pojedynczego żądania lub masz pewność, że protokół DTP jest zakończony i chcesz skopiować dane, wyczyść pole wyboru **Wyklucz ostatnie żądanie.**

   Dowiedz się więcej o tych ustawieniach w sekcji [Konfiguracje sap BW Open Hub Destination](#sap-bw-open-hub-destination-configurations) w tym artykule. Wybierz **opcję Sprawdź poprawność,** aby dokładnie sprawdzić, jakie dane zostaną zwrócone. Następnie wybierz **przycisk Dalej**.

   ![Konfigurowanie filtra SAP BW Open Hub](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Na stronie **Docelowy magazyn danych** wybierz pozycję **+Utwórz nowe połączenie** > **Usługi Azure Data Lake Storage Gen2** > **Continue**.

8. Na stronie **Określ połączenie usługi Azure Data Lake Storage** wykonaj następujące kroki, aby utworzyć połączenie.

   ![Tworzenie połączonej strony usługi ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Wybierz konto obsługujące usługę Data Lake Storage Gen2 z listy rozwijanej **Nazwa.**
   2. Wybierz pozycję **Zakończ**, aby utworzyć połączenie. Następnie wybierz **przycisk Dalej**.

9. Na stronie **Wybierz plik wyjściowy lub folder** wprowadź nazwę folderu **copyfromopenhub** jako nazwę folderu wyjściowego. Następnie wybierz **przycisk Dalej**.

   ![Wybierz stronę folderu wyjściowego](media/load-sap-bw-data/choose-output-folder.png)

10. Na stronie **ustawień Format pliku** wybierz pozycję **Dalej,** aby użyć ustawień domyślnych.

    ![Określanie strony formatu ujścia](media/load-sap-bw-data/specify-sink-format.png)

11. Na stronie **Ustawienia** rozwiń pozycję **Ustawienia wydajności**. Wprowadź wartość **stopnia równoległości kopiowania,** takich jak 5, aby załadować z SAP BW równolegle. Następnie wybierz **przycisk Dalej**.

    ![Konfigurowanie ustawień kopiowania](media/load-sap-bw-data/configure-copy-settings.png)

12. Na stronie **Podsumowanie** przejrzyj ustawienia. Następnie wybierz **przycisk Dalej**.

13. Na stronie **Wdrażanie** wybierz pozycję **Monitoruj,** aby monitorować potok.

    ![Strona Wdrażanie](media/load-sap-bw-data/deployment.png)

14. Należy zauważyć, że karta **Monitor** po lewej stronie jest automatycznie zaznaczona. Kolumna **Akcje** zawiera łącza do wyświetlania szczegółów przebiegu działania i ponownego uruchamiania potoku.

    ![Widok monitorowania potoku](media/load-sap-bw-data/pipeline-monitoring.png)

15. Aby wyświetlić przebiegi aktywności skojarzone z uruchomieniem potoku, wybierz pozycję **Wyświetl przebiegi działania** w kolumnie **Akcje.** W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby przełączyć się z powrotem do widoku przebiegów potoku, wybierz **łącze Potoki** u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę.

    ![Ekran monitorowania aktywności](media/load-sap-bw-data/activity-monitoring.png)

16. Aby monitorować szczegóły wykonania dla każdego działania **kopiowania,** wybierz łącze Szczegóły, które jest ikoną okularów poniżej **Akcji** w widoku monitorowania aktywności. Dostępne szczegóły obejmują wolumin danych skopiowany ze źródła do ujścia, przepływność danych, kroki wykonywania i czas trwania oraz używane konfiguracje.

    ![Szczegóły monitorowania aktywności](media/load-sap-bw-data/activity-monitoring-details.png)

17. Aby wyświetlić **maksymalny identyfikator żądania,** wróć do widoku monitorowania aktywności i wybierz pozycję **Dane wyjściowe** w obszarze **Akcje**.

    ![Ekran wyjścia aktywności](media/load-sap-bw-data/activity-output.png)

    ![Widok szczegółów danych wyjściowych działania](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Kopia przyrostowa z sap BW Open Hub

> [!TIP]
> Zobacz [przepływ ekstrakcji delta łącznika SAP BW Open Hub,](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) aby dowiedzieć się, jak łącznik SAP BW Open Hub w fabryce danych kopiuje dane przyrostowe z SAP BW. Ten artykuł może również pomóc w zrozumieniu podstawowej konfiguracji łącznika.

Teraz kontynuujmy konfigurowanie kopii przyrostowej z SAP BW Open Hub.

Kopia przyrostowa używa mechanizmu "wysokiego znaku wodnego", który jest oparty na **identyfikatorze żądania.** Ten identyfikator jest generowany automatycznie w sap BW Open Hub Destination przez DTP. Na poniższym diagramie przedstawiono ten przepływ pracy:

![Wykres blokowy przepływu pracy kopiowania przyrostowego](media/load-sap-bw-data/incremental-copy-workflow.png)

Na stronie **fabryka danych Rozpocznijmy** pracę wybierz pozycję **Utwórz potok z szablonu,** aby użyć wbudowanego szablonu.

1. Wyszukaj **szablon SAP BW** i wybierz **szablon Przyrostowa kopia z szablonu SAP BW do usługi Azure Data Lake Storage Gen2.** Ten szablon kopiuje dane do usługi Azure Data Lake Storage Gen2. Podobny przepływ pracy można użyć do skopiowania do innych typów ujścia.

2. Na stronie głównej szablonu zaznacz lub utwórz następujące trzy połączenia, a następnie wybierz pozycję **Użyj tego szablonu** w prawym dolnym rogu okna.

   - **Magazyn obiektów blob platformy Azure:** W tym instruktażu używamy magazynu obiektów Blob platformy Azure do przechowywania wysokiego znaku wodnego, który jest *maksymalnym identyfikatorem żądania skopiowanego.*
   - **SAP BW Open Hub**: Jest to źródło do kopiowania danych. Szczegółowe informacje można znaleźć w poprzednim instruktażu w pełnym copy kopiu.
   - **Usługa Azure Data Lake Storage Gen2:** Jest to ujście do kopiowania danych. Szczegółowe informacje można znaleźć w poprzednim instruktażu w pełnym copy kopiu.

   ![Kopia przyrostowa z szablonu SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Ten szablon generuje potok z następującymi trzema działaniami i sprawia, że są one połączone w sieci: *Odnośnik,* *Kopiowanie danych*i *Sieć Web*.

   Przejdź do karty **Parametry** potoku. Zobaczysz wszystkie konfiguracje, które należy podać.

   ![Kopia przyrostowa z konfiguracji SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Określ nazwę tabeli Otwórz centrum, z wyjęciu danych.

   - **Data_Destination_Container:** Określ docelowy kontener usługi Azure Data Lake Storage Gen2, do który chcesz skopiować dane. Jeśli kontener nie istnieje, działanie kopiowania fabryki danych tworzy jeden podczas wykonywania.
  
   - **Data_Destination_Directory**: Określ ścieżkę folderu w kontenerze usługi Azure Data Lake Storage Gen2, do który chcesz skopiować dane. Jeśli ścieżka nie istnieje, działanie kopiowania fabryki danych tworzy ścieżkę podczas wykonywania.
  
   - **HighWatermarkBlobContainer**: Określ kontener do przechowywania wartości znaku wodnego o wysokiej wartości.

   - **HighWatermarkBlobDirectory**: Określ ścieżkę folderu pod kontenerem, aby przechowywać wartość znaku wodnego o wysokiej wartości.

   - **HighWatermarkBlobName**: Określ nazwę obiektu blob do przechowywania `requestIdCache.txt`wysokiej wartości znaku wodnego, takiej jak . W magazynie obiektów Blob przejdź do odpowiedniej ścieżki HighWatermarkBlobContainer+HighWatermarkBlobDirectory+HighWatermarkBlobName, takiej jak *container/path/requestIdCache.txt*. Utwórz obiekt blob z zawartością 0.

      ![Zawartość obiektu blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL:** W tym szablonie używamy WebActivity do wywoływania usługi Azure Logic Apps, aby ustawić wartość znaku wodnego w magazynie obiektów Blob. Możesz też użyć usługi Azure SQL Database do jej przechowywania. Użyj działania procedury składowanej, aby zaktualizować wartość.

      Należy najpierw utworzyć aplikację logiki, jak pokazano na poniższej ilustracji. Następnie wklej **adres URL HTTP POST**.

      ![Konfiguracja aplikacji logiki](media/load-sap-bw-data/logic-app-config.png)

      1. Przejdź do witryny Azure Portal. Wybierz nową usługę **Aplikacje logiki.** Wybierz **+Pusta aplikacja logiki,** aby przejść do **Projektanta aplikacji logiki**.

      2. Utwórz wyzwalacz **po odebraniu żądania HTTP**. Określ treść żądania HTTP w następujący sposób:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Dodaj akcję **Utwórz obiekt blob.** W polu **Ścieżka folderu** i **nazwa obiektu Blob**należy używać tych samych wartości, które zostały wcześniej skonfigurowane w *programie HighWatermarkBlobContainer+HighWatermarkBlobDirectory* i *HighWatermarkBlobName*.

      4. Wybierz pozycję **Zapisz**. Następnie skopiuj wartość **adresu URL HTTP POST** do użycia w potoku Fabryka danych.

4. Po podasz parametry potoku fabryki danych wybierz polecenie > **Debugowanie Zakończ,** aby wywołać uruchomienie w celu sprawdzenia poprawności konfiguracji. **Debug** Możesz też wybrać **pozycję Publikuj,** aby opublikować wszystkie zmiany, a następnie wybierz pozycję **Dodaj wyzwalacz,** aby wykonać przebieg.

## <a name="sap-bw-open-hub-destination-configurations"></a>Konfiguracje sap BW Open Hub Destination

W tej sekcji przedstawiono konfigurację strony SAP BW, aby skopiować dane za pomocą łącznika SAP BW Open Hub w fabryce danych.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurowanie ekstrakcji delta w SAP BW

Jeśli potrzebujesz zarówno kopii historycznej, jak i kopii przyrostowej lub tylko kopii przyrostowej, skonfiguruj wyodrębnianie różnicowe w SAP BW.

1. Utwórz miejsce docelowe otwarte centrum. Ohd można utworzyć w SAP Transaction RSA1, który automatycznie tworzy wymagany proces transformacji i transferu danych. Użyj następujących ustawień:

   - **ObjectType**: Można użyć dowolnego typu obiektu. W tym miejscu używamy **InfoCube** jako przykład.
   - **Typ miejsca docelowego**: Wybierz **tabelę bazy danych**.
   - **Klucz tabeli**: Wybierz **klucz techniczny**.
   - **Wyodrębnianie**: Wybierz **opcję Zachowaj dane i wstaw rekordy do tabeli**.

   ![Okno dialogowe Tworzenie okna dialogowego wyodrębniania delta SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Tworzenie okna dialogowego wyodrębniania SYSTEMU SAP BW OHD delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Można zwiększyć liczbę równoległych uruchomionych procesów roboczych SAP dla DTP:

   ![tworzenie-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Zaplanuj DTP w łańcuchach procesów.

   Delta DTP dla modułu działa tylko wtedy, gdy niezbędne wiersze nie zostały skompresowane. Upewnij się, że kompresja modułu BW nie jest uruchomiona przed DTP do tabeli Otwórz centrum. Najprostszym sposobem, aby to zrobić, jest zintegrowanie DTP z istniejącymi łańcuchami procesów. W poniższym przykładzie DTP (do OHD) jest wstawiany do łańcucha procesu między *Dostosuj* (zbiorcze zestawienie) i *Collapse* (kompresja modułu).

   ![Tworzenie schematu blokowego łańcucha procesów SAP BW](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Konfigurowanie pełnego wyodrębniania w SAP BW

Oprócz ekstrakcji różnicowej można uzyskać pełne wyodrębnienie tego samego programu SAP BW InfoProvider. Zwykle ma to zastosowanie, jeśli chcesz wykonać pełną kopię, ale nie przyrostową, lub chcesz [ponownie zsynchronizować ekstrakcję delta](#resync-delta-extraction).

Nie można mieć więcej niż jeden DTP dla tego samego OHD. Tak, należy utworzyć dodatkowe OHD przed ekstrakcji delta.

![Tworzenie SAP BW OHD pełne](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Aby uzyskać pełne obciążenie OHD, wybierz inne opcje niż w przypadku ekstrakcji delta:

- W OHD: Ustaw opcję **Wyodrębnianie,** aby **usunąć dane i wstawić rekordy**. W przeciwnym razie dane zostaną wyodrębnione wiele razy podczas powtarzania DTP w łańcuchu procesu BW.

- W DTP: Ustaw **tryb ekstrakcji** na **Pełny**. Należy zmienić automatycznie utworzony DTP z **Delta** na **Full** natychmiast po utworzeniu OHD, jak pokazano na tym obrazku:

   ![Tworzenie okna dialogowego SAP BW OHD skonfigurowane do ekstrakcji "Pełna"](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- W łączniku BW Open Hub fabryki danych: wyłącz **wykluczenie ostatniego żądania**. W przeciwnym razie nic nie zostanie wyodrębnione.

Zazwyczaj pełną dtp można uruchomić ręcznie. Można też utworzyć łańcuch procesów dla pełnego protokołu DTP. Zazwyczaj jest to oddzielny łańcuch, który jest niezależny od istniejących łańcuchów procesów. W obu przypadkach *upewnij się, że DTP jest gotowy przed rozpoczęciem ekstrakcji przy użyciu kopii data factory*. W przeciwnym razie tylko częściowe dane zostaną skopiowane.

### <a name="run-delta-extraction-the-first-time"></a>Uruchom ekstrakcję delta po raz pierwszy

Pierwsze ekstrakcja delta jest technicznie *pełne wydobycie*. Domyślnie łącznik SAP BW Open Hub wyklucza ostatnie żądanie podczas kopiowania danych. W przypadku pierwszego wyodrębniania różnicowego żadne dane nie są wyodrębniane przez działanie kopiowania usługi Data Factory, dopóki kolejne dane DTP nie wygeneruje danych delta w tabeli z oddzielnym identyfikatorem żądania. Istnieją dwa sposoby uniknięcia tego scenariusza:

- Wyłącz opcję **Wyklucz ostatnie żądanie** dla pierwszego ekstrakcji różnicowej. Upewnij się, że pierwszy delta DTP jest zakończona przed rozpoczęciem ekstrakcji różnicowej po raz pierwszy.
-  Należy użyć procedury ponownego wyodrębniania delta, jak opisano w następnej sekcji.

### <a name="resync-delta-extraction"></a>Ekstrakcja delta Resync

Następujące scenariusze zmieniają dane w modułach SAP BW, ale nie są uwzględniane przez delta DTP:

- Selektywne usuwanie SAP BW (wierszy przy użyciu dowolnego warunku filtru)
- Usunięcie żądania SAP BW (wadliwych żądań)

Miejsce docelowe SAP Open Hub nie jest celem docelowym danych kontrolowanym przez dane (we wszystkich pakietach obsługi SAP BW od 2015 r.). Tak, można usunąć dane z modułu bez zmiany danych w OHD. Następnie należy ponownie zsynchronizuj dane modułu za pomocą usługi Data Factory:

1. Uruchom pełne wyodrębnianie w fabryce danych (przy użyciu pełnego DTP w SAP).
2. Usuń wszystkie wiersze w tabeli Otwórz centrum dla różnicy DTP.
3. Ustaw stan delta DTP na **Pobrane**.

Po tym, wszystkie kolejne różnicowe DTPs i data factory delta ekstrakcji pracy zgodnie z oczekiwaniami.

Aby ustawić stan delta DTP na **Pobrane,** można użyć następującej opcji, aby ręcznie uruchomić delta DTP:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o obsłudze łączników SAP BW Open Hub:

> [!div class="nextstepaction"]
>[Złącze sap business warehouse open hub](connector-sap-business-warehouse-open-hub.md)
