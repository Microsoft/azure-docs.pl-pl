---
title: Ładowanie danych z programu SAP Business Warehouse
description: Używanie Azure Data Factory do kopiowania danych z programu SAP Business Warehouse (BW)
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
ms.openlocfilehash: bad9a706c5289966334af26eacbfa41c418b7ab5
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360807"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Kopiowanie danych z programu SAP Business Warehouse przy użyciu Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule pokazano, jak za pomocą programu Azure Data Factory skopiować dane z programu SAP Business Warehouse (BW) za pośrednictwem usługi Open Hub do programu Azure Data Lake Storage Gen2. Podobny proces służy do kopiowania danych do innych [obsługiwanych magazynów danych ujścia](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Aby uzyskać ogólne informacje o kopiowaniu danych z SAP BW, w tym SAP BW otwartych rozwiązań do integracji i rozpakowywania, zobacz [Kopiowanie danych z programu SAP Business Warehouse za pośrednictwem usługi Open Hub przy użyciu Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Wymagania wstępne

- **Azure Data Factory**: Jeśli go nie masz, postępuj zgodnie z instrukcjami, aby [utworzyć fabrykę danych](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW Otwórz lokalizację docelową (OHD) z typem docelowym "tabela bazy danych"**: Aby utworzyć OHD lub sprawdzić, czy OHD jest prawidłowo skonfigurowane do integracji Data Factory, zobacz sekcję [SAP BW Otwórz konfigurację docelową](#sap-bw-open-hub-destination-configurations) w tym artykule.

- **Użytkownik SAP BW musi mieć następujące uprawnienia**:

  - Autoryzacja zdalnych wywołań funkcji (RFC) i SAP BW.
  - Uprawnienia do działania "Execute" obiektu autoryzacji **S_SDSAUTH** .

- ** [Własne środowisko Integration Runtime (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) z łącznikiem SAP .NET 3,0**. Wykonaj następujące kroki instalacji:

  1. Zainstaluj i zarejestruj własne środowisko Integration Runtime w wersji 3,13 lub nowszej. (Ten proces jest opisany w dalszej części tego artykułu).

  2. Pobierz [64-bitowy łącznik SAP dla Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) z witryny sieci Web SAP i zainstaluj go na tym samym komputerze, na którym znajduje się samoobsługowy interfejs IR. Podczas instalacji upewnij się, że wybrano opcję **Zainstaluj zestawy do GAC** w oknie dialogowym **opcjonalne kroki instalacji** , jak pokazano na poniższej ilustracji:

     ![Okno dialogowe Konfigurowanie łącznika SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Wykonaj pełną kopię z SAP BW otwartym centrum

W witrynie Azure Portal przejdź do swojej fabryki danych. Wybierz pozycję **utwórz & monitor** , aby otworzyć interfejs użytkownika Data Factory na oddzielnej karcie.

1. **Na stronie Wprowadzenie** wybierz pozycję **Kopiowanie danych** , aby otworzyć narzędzie kopiowanie danych.

2. Na stronie **Właściwości** Określ **nazwę zadania**, a następnie wybierz przycisk **dalej**.

3. Na stronie **Magazyn danych źródłowych** wybierz pozycję **+ Utwórz nowe połączenie**. Wybierz pozycję **SAP BW Otwórz Centrum** z galerii łączników, a następnie wybierz pozycję **Kontynuuj**. Aby filtrować łączniki, można wpisać **SAP** w polu wyszukiwania.

4. Na stronie **określanie SAP BW nawiązywanie połączenia** w usłudze Hub wykonaj następujące kroki, aby utworzyć nowe połączenie.

   ![Utwórz SAP BW Otwórz stronę usługi połączonej z centrum](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Z listy **Połącz za pośrednictwem środowiska Integration Runtime** Wybierz istniejące środowisko IR samoobsługowego. Możesz też wybrać opcję utworzenia jednego, jeśli jeszcze go nie masz.

      Aby utworzyć nowy samoobsługowy środowisko IR, wybierz pozycję **+ Nowy**, a następnie wybierz pozycję **własne**. Wprowadź **nazwę**, a następnie wybierz przycisk **dalej**. Wybierz pozycję **Instalacja ekspresowa** , aby zainstalować program na bieżącym komputerze, lub wykonaj kroki **ręcznej instalacji** , które są dostępne.

      Zgodnie z opisem w sekcji [wymagania wstępne](#prerequisites)upewnij się, że masz łącznik SAP dla Microsoft .NET 3,0 zainstalowany na tym samym komputerze, na którym działa środowisko IR samoobsługowego.

   2. Wprowadź wartość w polu **Nazwa serwera**SAP BW, **numer systemu**, **Identyfikator klienta,** **Język** (jeśli jest inny niż **EN**), **Nazwa użytkownika**i **hasło**.

   3. Wybierz **Test connection** , aby sprawdzić poprawność ustawień, a następnie wybierz pozycję **Zakończ**.

   4. Zostanie utworzone nowe połączenie. Wybierz opcję **Dalej**.

5. Na stronie **Wybierz miejscowe miejsca docelowe programu Open** Hub Przeglądaj lokalizacje dostępne w usłudze SAP BW. Wybierz OHD, z którego mają zostać skopiowane dane, a następnie wybierz przycisk **dalej**.

   ![Wybierz SAP BW otwarcie tabeli docelowej centrum](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Określ filtr, jeśli będzie potrzebny. Jeśli OHD zawiera tylko dane z pojedynczego procesu transferu danych (DTP) z pojedynczym IDENTYFIKATORem żądania, lub jeśli masz pewność, że DTP została zakończona i chcesz skopiować dane, usuń zaznaczenie pola wyboru **Wyklucz ostatnie żądanie** .

   Więcej informacji o tych ustawieniach znajduje się w sekcji [SAP BW Otwórz konfigurację docelową](#sap-bw-open-hub-destination-configurations) w tym artykule. Wybierz pozycję **Weryfikuj** , aby dokładnie sprawdzić, jakie dane zostaną zwrócone. Następnie wybierz pozycję **Dalej**.

   ![Konfigurowanie SAP BW Otwórz filtr centrum](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Na stronie **docelowy magazyn danych** wybierz pozycję **+ Utwórz nowe połączenie**  >  **Azure Data Lake Storage Gen2**  >  **Kontynuuj**.

8. Na stronie **Określanie połączenia Azure Data Lake Storage** wykonaj następujące kroki, aby utworzyć połączenie.

   ![Tworzenie strony połączonej usługi ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Wybierz konto z możliwością Data Lake Storage Gen2 z listy rozwijanej **Nazwa** .
   2. Wybierz pozycję **Zakończ**, aby utworzyć połączenie. Następnie wybierz pozycję **Dalej**.

9. Na stronie **Wybieranie pliku lub folderu wyjściowego** wprowadź **copyfromopenhub** jako nazwę folderu wyjściowego. Następnie wybierz pozycję **Dalej**.

   ![Strona wybierania folderu wyjściowego](media/load-sap-bw-data/choose-output-folder.png)

10. Na stronie **Ustawienia formatu pliku** wybierz pozycję **dalej** , aby użyć ustawień domyślnych.

    ![Strona Określanie formatu ujścia](media/load-sap-bw-data/specify-sink-format.png)

11. Na stronie **Ustawienia** rozwiń węzeł **Ustawienia wydajności**. Wprowadź wartość **stopnia równoległości kopiowania** , taką jak 5, aby załadować ją równolegle do SAP BW. Następnie wybierz pozycję **Dalej**.

    ![Konfiguruj ustawienia kopiowania](media/load-sap-bw-data/configure-copy-settings.png)

12. Na stronie **Podsumowanie** przejrzyj ustawienia. Następnie wybierz pozycję **Dalej**.

13. Na stronie **wdrażanie** wybierz pozycję **Monitoruj** , aby monitorować potok.

    ![Strona Wdrażanie](media/load-sap-bw-data/deployment.png)

14. Zauważ, że karta **monitor** po lewej stronie jest automatycznie zaznaczona. Kolumna **Actions** zawiera linki do wyświetlania szczegółów działania i ponownego uruchomienia potoku.

    ![Widok monitorowania potoku](media/load-sap-bw-data/pipeline-monitoring.png)

15. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz pozycję **Wyświetl uruchomienia działania** w kolumnie **Akcje** . W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby przełączyć się z powrotem do widoku uruchomienia potoków, wybierz link **potoki** u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę.

    ![Działanie — ekran monitorowania](media/load-sap-bw-data/activity-monitoring.png)

16. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz link **szczegóły** , który jest ikoną okularów poniżej **akcji** w widoku monitorowanie aktywności. Dostępne szczegóły obejmują ilość danych skopiowaną ze źródła do ujścia, przepływność danych, etapy wykonywania oraz używane konfiguracje.

    ![Szczegóły monitorowania działania](media/load-sap-bw-data/activity-monitoring-details.png)

17. Aby wyświetlić **Maksymalny identyfikator żądania**, Wróć do widoku działania — monitorowanie i wybierz pozycję **dane wyjściowe** w obszarze **Akcje**.

    ![Ekran danych wyjściowych działania](media/load-sap-bw-data/activity-output.png)

    ![Widok szczegółów danych wyjściowych działania](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Kopia przyrostowa z SAP BW Otwórz Centrum

> [!TIP]
> Aby dowiedzieć się, w jaki sposób SAP BW otworzyć łącznik usługi Hub w Data Factory kopiuje dane przyrostowe z SAP BW, zobacz [SAP BW otwieranie przepływu wyodrębniania różnicowego łącznika usługi Hub](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) . Ten artykuł może również pomóc w zrozumieniu podstawowej konfiguracji łącznika.

Teraz Kontynuuj, aby skonfigurować przyrostową kopię z poziomu SAP BW Otwórz Centrum.

Kopia przyrostowa używa mechanizmu "High-Limit", który jest oparty na **identyfikatorze żądania**. Ten identyfikator jest generowany automatycznie w SAP BW otwartym miejscu docelowym przez DTP. Na poniższym diagramie przedstawiono ten przepływ pracy:

![Wykres przepływu przepływu pracy kopiowania przyrostowego](media/load-sap-bw-data/incremental-copy-workflow.png)

Na **stronie Wprowadzenie do usługi Data Factory wybierz** pozycję **Utwórz potok na podstawie szablonu** , aby użyć wbudowanego szablonu.

1. Wyszukaj **SAP BW** , aby znaleźć i wybrać **kopię przyrostową z SAP BW do Azure Data Lake Storage Gen2** szablonu. Ten szablon kopiuje dane do Azure Data Lake Storage Gen2. Możesz użyć podobnego przepływu pracy do kopiowania do innych typów ujścia.

2. Na stronie głównej szablonu wybierz lub Utwórz następujące trzy połączenia, a następnie wybierz pozycję **Użyj tego szablonu** w prawym dolnym rogu okna.

   - **Magazyn obiektów blob platformy Azure**: w tym przewodniku usługa Azure Blob Storage jest używana do przechowywania górnego limitu, który jest *maksymalnym skopiowanym identyfikatorem żądania*.
   - **SAP BW otworzyć Centrum**: jest to źródło, z którego mają zostać skopiowane dane. Szczegółowe informacje na temat konfiguracji można znaleźć w poprzednim przewodniku pełnego kopiowania.
   - **Azure Data Lake Storage Gen2**: jest to ujścia, do którego mają zostać skopiowane dane. Szczegółowe informacje na temat konfiguracji można znaleźć w poprzednim przewodniku pełnego kopiowania.

   ![Kopia przyrostowa z szablonu SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Ten szablon generuje potok z następującymi trzema działaniami i sprawia, że są one powiązane z sukcesem: *Lookup*, *Kopiowanie danych*i *sieci Web*.

   Przejdź do karty **Parametry** potoku. Zobaczysz wszystkie konfiguracje, które należy podać.

   ![Przyrostowa kopia z konfiguracji SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Podaj nazwę otwartej tabeli centrum, z której mają zostać skopiowane dane.

   - **Data_Destination_Container**: określ kontener Azure Data Lake Storage Gen2 docelowy, do którego mają zostać skopiowane dane. Jeśli kontener nie istnieje, działanie Data Factory Copy Tworzy je podczas wykonywania.
  
   - **Data_Destination_Directory**: Określ ścieżkę folderu w kontenerze Azure Data Lake Storage Gen2, do której mają zostać skopiowane dane. Jeśli ścieżka nie istnieje, działanie kopiowania Data Factory tworzy ścieżkę podczas wykonywania.
  
   - **HighWatermarkBlobContainer**: Określ kontener do przechowywania wartości górnego limitu.

   - **HighWatermarkBlobDirectory**: Określ ścieżkę folderu w kontenerze do przechowywania wartości górnego limitu.

   - **HighWatermarkBlobName**: Określ nazwę obiektu BLOB do przechowywania wartości górnego limitu, na przykład `requestIdCache.txt` . W usłudze BLOB Storage przejdź do odpowiedniej ścieżki HighWatermarkBlobContainer + HighWatermarkBlobDirectory + HighWatermarkBlobName, takich jak *Container/Path/requestIdCache.txt*. Utwórz obiekt BLOB z zawartością 0.

      ![Zawartość obiektu blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: w tym szablonie używamy działania webactivity do wywołania Azure Logic Apps, aby ustawić wartość górnego limitu w magazynie obiektów BLOB. Można też użyć Azure SQL Database, aby go zapisać. Aby zaktualizować wartość, Użyj działania procedury składowanej.

      Najpierw należy utworzyć aplikację logiki, jak pokazano na poniższej ilustracji. Następnie wklej w **adresie URL post protokołu HTTP**.

      ![Konfiguracja aplikacji logiki](media/load-sap-bw-data/logic-app-config.png)

      1. Przejdź do witryny Azure Portal. Wybierz nową usługę **Logic Apps** . Wybierz pozycję **+ pusta aplikacja logiki** , aby przejść do narzędzia **Logic Apps Designer**.

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

      3. Dodaj akcję **Utwórz obiekt BLOB** . W polu **ścieżka folderu** i **Nazwa obiektu BLOB**Użyj tych samych wartości, które zostały wcześniej skonfigurowane w *HighWatermarkBlobContainer + HighWatermarkBlobDirectory* i *HighWatermarkBlobName*.

      4. Wybierz pozycję **Zapisz**. Następnie skopiuj wartość **adresu URL post protokołu HTTP** do użycia w potoku Data Factory.

4. Po podaniu parametrów potoku Data Factory wybierz pozycję **Debuguj**  >  **Zakończ** , aby wywołać przebieg, aby sprawdzić poprawność konfiguracji. Lub wybierz pozycję **Opublikuj** , aby opublikować wszystkie zmiany, a następnie wybierz pozycję **Dodaj wyzwalacz** , aby wykonać przebieg.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW otwieranie konfiguracji miejsca docelowego centrum

W tej części przedstawiono konfigurację strony SAP BW, która umożliwia używanie łącznika SAP BW Open Hub w Data Factory do kopiowania danych.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurowanie wyodrębniania różnicowego w SAP BW

Jeśli wymagana jest kopia historyczna oraz kopia przyrostowa lub tylko kopia przyrostowa, skonfiguruj wyodrębnianie zmian w SAP BW.

1. Utwórz miejsce docelowe otwierania centrum. Można utworzyć OHD w usłudze SAP Transaction RSA1, która automatycznie tworzy wymaganą transformację i transfer danych. Użyj następujących ustawień:

   - **ObjectType**: można użyć dowolnego typu obiektu. Oto przykład użycia **InfoCube** .
   - **Typ docelowy**: Wybierz **tabelę bazy danych**.
   - **Klucz tabeli**: Wybierz **klucz techniczny**.
   - **Wyodrębnianie**: wybierz pozycję **Zachowaj dane i Wstaw rekordy do tabeli**.

   ![Tworzenie okna dialogowego wyodrębnianie zmian SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Okno dialogowe tworzenia SAP BW OHD Delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Można zwiększyć liczbę równoległych uruchomionych procesów roboczych SAP dla DTP:

   ![Zrzut ekranu przedstawia ustawienia przetwarzania równoległego, w którym można wybrać liczbę procesów równoległych dla D T P.](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Zaplanuj DTP w łańcuchach procesów.

   DTP Delta dla modułu działa tylko wtedy, gdy niezbędne wiersze nie zostały skompresowane. Upewnij się, że kompresja modułu wagi nie jest uruchomiona przed DTP do otwartej tabeli centrum. Najprostszym sposobem, aby to zrobić, jest zintegrowanie DTP z istniejącymi łańcuchami procesów. W poniższym przykładzie DTP (do OHD) jest wstawiany do łańcucha procesów między krokami *Dostosowywanie* (agregowanie agregacji) i *zwijaniem* (kompresja modułu).

   ![Utwórz wykres przepływu łańcucha procesów SAP BW](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Skonfiguruj pełną wyodrębnianie w SAP BW

Oprócz ekstrakcji różnicowych można chcieć przeprowadzić pełną ekstrakcję tego samego SAP BW InfoProvider. Zwykle ma to zastosowanie w przypadku, gdy chcesz wykonać pełną kopię, ale nie przyrostową, lub chcesz ponownie [synchronizować wyodrębnianie różnicowe](#resync-delta-extraction).

Nie można mieć więcej niż jednego DTP dla tego samego OHDu. Dlatego należy utworzyć dodatkowe OHD przed rozpoczęciem ekstrakcji różnicowej.

![Utwórz pełne SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

W przypadku pełnego obciążenia OHD wybierz różne opcje niż w przypadku wyodrębniania różnicowego:

- W OHD: Ustaw opcję **wyodrębniania** , aby **usunąć dane i wstawić rekordy**. W przeciwnym razie dane zostaną wyodrębnione wielokrotnie podczas powtarzania DTP w łańcuchu procesów w sieci.

- W DTP: Ustaw **tryb wyodrębniania** na **pełny**. Należy zmienić automatycznie utworzone DTP od **delty** do **pełnego** natychmiast po utworzeniu OHD, jak pokazano w tym obrazie:

   ![Utwórz okno dialogowe SAP BW OHD skonfigurowane do ekstrakcji "Full"](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- W oknie łącznika z otwartym centrum w usłudze BW Data Factory: Wyłącz opcję **Wyklucz ostatnie żądanie**. W przeciwnym razie nic nie zostanie wyodrębnione.

Zwykle ręcznie uruchamiasz pełne DTP. Można też utworzyć łańcuch procesów dla pełnej DTP. Zwykle jest to oddzielny łańcuch, który jest niezależny od istniejących łańcuchów procesów. W obu przypadkach upewnij się, *że DTP jest zakończona przed rozpoczęciem wyodrębniania za pomocą kopii Data Factory*. W przeciwnym razie zostaną skopiowane tylko częściowe dane.

### <a name="run-delta-extraction-the-first-time"></a>Uruchom wyodrębnianie zmian po raz pierwszy

Pierwsza Ekstrakcja różnicowa jest technicznie *pełna*. Domyślnie łącznik programu SAP BW Open Hub wyklucza ostatnie żądanie, gdy kopiuje dane. W przypadku pierwszej ekstrakcji różnicowych żadne dane nie są wyodrębniane przez działanie Data Factory Copy, dopóki kolejne DTP nie generują danych różnicowych w tabeli z oddzielnym IDENTYFIKATORem żądania. Istnieją dwa sposoby, aby uniknąć tego scenariusza:

- Wyłącz opcję **Wyklucz ostatnie żądanie** dla pierwszej ekstrakcji różnicowej. Przed rozpoczęciem wyodrębniania zmian po raz pierwszy należy się upewnić, że pierwsze DTP Delta zostało zakończone.
-  Użyj procedury ponownej synchronizacji wyodrębniania różnicowego, zgodnie z opisem w następnej sekcji.

### <a name="resync-delta-extraction"></a>Ponowna synchronizacja ekstrakcji różnicowej

Poniższe scenariusze zmieniają dane w modułach SAP BW, ale nie są one uwzględniane przez DTP Delta:

- SAP BW selektywne usuwanie (wierszy przy użyciu dowolnego warunku filtru)
- Usuwanie żądania SAP BW (wadliwe żądania)

Miejsce docelowe typu "Open Hub" SAP nie jest celem danych kontrolowanym przez składnię danych (we wszystkich SAP BW pakietach pomocy technicznej od 2015). W związku z tym można usunąć dane z modułu bez zmiany danych w OHD. Następnie należy ponownie zsynchronizować dane modułu z Data Factory:

1. Uruchom pełną wyodrębnianie w Data Factory (przy użyciu pełnej DTP w oprogramowaniu SAP).
2. Usuń wszystkie wiersze z otwartej tabeli centrum DTP Delta.
3. Ustaw stan DTP Delta do **pobrania**.

Po wykonaniu tej czynności wszystkie kolejne DTPs różnicowe i Data Factory wyodrębnianie różnicowe działają zgodnie z oczekiwaniami.

Aby ustawić stan DTP Delta do **pobrania**, można użyć następującej opcji, aby ręcznie uruchomić różnicowe DTP:

*Brak Transfer danych; Stan Delta w źródle: pobrane*

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o SAP BW pomocy technicznej łącznika centrum:

> [!div class="nextstepaction"]
>[Łącznik centrum SAP Business Warehouse Open Hub](connector-sap-business-warehouse-open-hub.md)
