---
title: Rozgałęzianie działań i łączenie łańcuchów w potoku przy użyciu Azure Portal
description: Dowiedz się, jak sterować przepływem danych w potoku Azure Data Factory przy użyciu Azure Portal.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: 9d8d9af4892001ff1f8f60a92f28aedad14c17cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100378608"
---
# <a name="branching-and-chaining-activities-in-an-azure-data-factory-pipeline-using-the-azure-portal"></a>Rozgałęzianie działań i łączenie łańcuchów w potoku Azure Data Factory przy użyciu Azure Portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku pokazano, jak utworzyć potok usługi Data Factory przedstawiający niektóre funkcje przepływu sterowania. Ten potok tworzy prostą kopię z kontenera w usłudze Azure Blob Storage w innym kontenerze na tym samym koncie magazynu. Jeśli działanie kopiowania zakończy się powodzeniem, potok wysyła szczegóły zakończonej pomyślnie operacji kopiowania (takie jak ilość zapisanych danych) w wiadomości e-mail z informacją o powodzeniu. W przypadku niepowodzenia działania kopiowania potok wysyła szczegóły błędu kopiowania (np. komunikat o błędzie) w wiadomości e-mail z informacją o niepowodzeniu. W samouczku pokazano, jak przekazać parametry.

Ogólne omówienie scenariusza: ![ diagram przedstawia BLOB Storage platformy Azure, która jest celem kopii, która po powodzeniu wysyła wiadomość e-mail ze szczegółami lub, w przypadku niepowodzenia, wysyła wiadomość e-mail z informacjami o błędzie.](media/tutorial-control-flow-portal/overview.png)

Ten samouczek obejmuje następujące procedury:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie połączonej usługi Azure Storage
> * Tworzenie zestawu danych obiektów blob platformy Azure
> * Tworzenie potoku zawierającego działanie kopiowania i działanie internetowe
> * Wysyłanie danych wyjściowych działań do kolejnych działań
> * Korzystanie z przekazywania parametrów i ze zmiennych systemowych
> * Uruchamianie potoku
> * Monitorowanie uruchomień działań i potoku

W tym samouczku jest używana witryna Azure Portal. Aby uzyskać informacje o innych mechanizmach interakcji z usługą Azure Data Factory, przejdź do przewodników Szybki start w spisie treści.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.
* **Konto usługi Azure Storage**. Magazyn obiektów blob jest używany jako **źródłowy** magazyn danych. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../storage/common/storage-account-create.md).
* **Azure SQL Database**. Baza danych jest używana jako magazyn danych **ujścia**. Jeśli nie masz bazy danych w Azure SQL Database, zapoznaj się z artykułem [Tworzenie bazy danych w programie Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) .

### <a name="create-blob-table"></a>Tworzenie tabeli obiektów blob

1. Uruchom program Notatnik. Skopiuj poniższy tekst i zapisz go na dysku jako plik **input.txt**.

    ```
    John,Doe
    Jane,Doe
    ```
2. Użyj narzędzia takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/), aby wykonać następujące czynności:
    1. Utwórz kontener **adfv2branch**.
    2. Utwórz folder **input** w kontenerze **adfv2branch**.
    3. Przekaż plik **input.txt** do kontenera.

## <a name="create-email-workflow-endpoints"></a>Tworzenie punktów końcowych przepływu pracy poczty e-mail
Aby wyzwolić wysyłanie wiadomości e-mail z potoku, zdefiniuj przepływ pracy przy użyciu usługi [Logic Apps](../logic-apps/logic-apps-overview.md). Aby uzyskać szczegółowe informacje na temat tworzenia przepływu pracy aplikacji logiki, zobacz [Jak utworzyć aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Przepływ pracy wiadomości e-mail z informacją o powodzeniu
Utwórz przepływ pracy aplikacji logiki o nazwie `CopySuccessEmail`. Zdefiniuj wyzwalacz przepływu pracy jako `When an HTTP request is received` i dodaj akcję `Office 365 Outlook – Send an email`.

![Przepływ pracy wiadomości e-mail z informacją o powodzeniu](media/tutorial-control-flow-portal/success-email-workflow.png)

W wyzwalaczu żądania wypełnij pole `Request Body JSON Schema` przy użyciu następującego kodu JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Żądanie w projektancie aplikacji logiki powinno wyglądać jak na poniższym obrazie:

![Projektant aplikacji logiki — żądanie](media/tutorial-control-flow-portal/logic-app-designer-request.png)

W przypadku akcji **Wyślij wiadomość e-mail** dostosuj formatowanie wiadomości e-mail przy użyciu właściwości przekazywanych w schemacie JSON w treści żądania. Oto przykład:

![Projektant aplikacji logiki — Akcja Wyślij wiadomość e-mail](media/tutorial-control-flow-portal/send-email-action-2.png)

Zapisz przepływ pracy. Zapisz adres URL żądania HTTP POST dla przepływu pracy wiadomości e-mail z informacją o powodzeniu:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

### <a name="fail-email-workflow"></a>Przepływ pracy wiadomości e-mail z informacją o niepowodzeniu
Wykonaj te same kroki, aby utworzyć inny przepływ pracy usługi Logic Apps o nazwie **CopyFailEmail**. W wyzwalaczu żądania element `Request Body JSON schema` jest taki sam. Zmień format wiadomości e-mail, na przykład element `Subject`, aby przekształcić wiadomość e-mail w wiadomość z informacją o niepowodzeniu. Oto przykład:

![Projektant aplikacji logiki — przepływ pracy wiadomości e-mail z informacją o niepowodzeniu](media/tutorial-control-flow-portal/fail-email-workflow-2.png)

Zapisz przepływ pracy. Zapisz adres URL żądania HTTP POST dla przepływu pracy wiadomości e-mail z informacją o niepowodzeniu:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Teraz powinny być zapisane dwa adresy URL przepływów pracy:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **dane + analiza**  >  **Data Factory**:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** wprowadź wartość **ADFTutorialDataFactory** w polu **Nazwa**.

     ![Strona Nowa fabryka danych](./media/tutorial-control-flow-portal/new-azure-data-factory.png)

   Nazwa fabryki danych Azure musi być **globalnie unikatowa**. Jeśli wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.

   *Nazwa fabryki danych "ADFTutorialDataFactory" jest niedostępna.*

3. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych.
4. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:

      - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.
      - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
        Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
4. Wybierz opcję **V2** w obszarze **Wersja**.
5. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.
6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
7. Kliknij pozycję **Utwórz**.      
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **wdrażanie fabryki danych**.

    ![kafelek Wdrażanie fabryki danych](media/tutorial-control-flow-portal/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.

   ![Strona główna fabryki danych](./media/tutorial-control-flow-portal/data-factory-home-page.png)
10. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory.


## <a name="create-a-pipeline"></a>Tworzenie potoku
W tym kroku jest tworzony potok z jednym działaniem kopiowania i dwoma działaniami internetowymi. Do utworzenia potoku zostaną użyte następujące funkcje:

- Parametry dla potoku, do których uzyskują dostęp zestawy danych.
- Działania internetowe w celu wywołania przepływów pracy aplikacji logiki wysyłających wiadomości e-mail z informacją o powodzeniu/niepowodzeniu.
- Łączenie jednego działania z innym (w przypadku powodzenia i niepowodzenia)
- Używanie danych wyjściowe działania jako danych wejściowych kolejnego działania

1. Na stronie **Wprowadzenie** interfejsu użytkownika usługi Data Factory kliknij kafelek **Utwórz potok**.  

   ![Strona Wprowadzenie](./media/tutorial-control-flow-portal/get-started-page.png)
3. W oknie właściwości potoku przejdź do karty **Parametry** i użyj przycisku **Nowy**, aby dodać następujące trzy parametry typu String: sourceBlobContainer, sinkBlobContainer oraz receiver.

    - **sourceBlobContainer** — parametr w potoku używany przez zestaw danych obiektu blob źródła.
    - **sinkBlobContainer** — parametr w potoku używany przez zestaw danych obiektu blob ujścia.
    - **odbiornik** — ten parametr jest używany przez dwa działania sieci Web w potoku, które wysyłają wiadomości e-mail o powodzeniu lub niepowodzeniu do odbiorcy, którego adres e-mail jest określony przez ten parametr.

   ![Menu Nowy potok](./media/tutorial-control-flow-portal/pipeline-parameters.png)
4. W przyborniku **Działania** rozwiń pozycję **Przepływ danych**, a następnie przeciągnij działanie **Kopiowanie** i upuść je na powierzchni projektanta potoku.

   ![Przeciąganie i upuszczanie działania kopiowania](./media/tutorial-control-flow-portal/drag-drop-copy-activity.png)
5. W oknie **Właściwości** dla działania **Kopiowanie** znajdującego się na dole przejdź do karty **Źródło**, a następnie kliknij pozycję **+ Nowy**. W tym kroku utworzysz zestaw danych źródłowych dla działania kopiowania.

   ![Zrzut ekranu pokazujący sposób tworzenia źródłowego zestawu danych dla działania kopiowania.](./media/tutorial-control-flow-portal/new-source-dataset-button.png)
6. W oknie **Nowy zestaw danych** wybierz pozycję **Azure Blob Storage** i kliknij przycisk **Zakończ**.

   ![Wybieranie pozycji Azure Blob Storage](./media/tutorial-control-flow-portal/select-azure-blob-storage.png)
7. Zostanie wyświetlona nowa **karta** zatytułowana **AzureBlob1**. Zmień nazwę zestawu danych na **SourceBlobDataset**.

   ![Ustawienia ogólne zestawu danych](./media/tutorial-control-flow-portal/dataset-general-page.png)
8. Przejdź do karty **Połączenie** w oknie **Właściwości**, a następnie kliknij przycisk Nowy dla pozycji **Połączona usługa**. W tym kroku utworzysz połączoną usługę służącą do łączenia konta usługi Azure Storage z fabryką danych.

   ![Połączenie zestawu danych — nowa połączona usługa](./media/tutorial-control-flow-portal/dataset-connection-new-button.png)
9. W oknie **Nowa połączona usługa** wykonaj następujące czynności:

    1. Wprowadź wartość **AzureStorageLinkedService** w polu **Nazwa**.
    2. Wybierz swoje konto usługi Azure Storage w polu **Nazwa konta magazynu**.
    3. Kliknij pozycję **Zapisz**.

   ![Nowa połączona usługa Azure Storage](./media/tutorial-control-flow-portal/new-azure-storage-linked-service.png)
12. Wprowadź wartość `@pipeline().parameters.sourceBlobContainer` jako folder oraz `emp.txt` jako nazwę pliku. Parametr potoku sourceBlobContainer umożliwia ustawienie ścieżki folderu dla zestawu danych.

    ![Ustawienia zestawu danych źródłowych](./media/tutorial-control-flow-portal/source-dataset-settings.png)

13. Przejdź do karty **potoku** lub kliknij potok w widoku drzewa. Upewnij się, że dla ustawienia **Zestaw danych źródłowych** wybrano wartość **SourceBlobDataset**.
      
   ![Zestaw danych źródłowych](./media/tutorial-control-flow-portal/pipeline-source-dataset-selected.png)

13. W oknie Właściwości przejdź do karty **Ujście**, a następnie kliknij pozycję **+ Nowy** dla elementu **Zestaw danych ujścia**. W tym kroku utworzysz zestaw danych ujścia dla działania kopiowania w podobny sposób do tworzenia zestawu danych źródłowych.

    ![Przycisk Nowy zestaw danych ujścia](./media/tutorial-control-flow-portal/new-sink-dataset-button.png)
14. W oknie **Nowy zestaw danych** wybierz pozycję **Azure Blob Storage** i kliknij przycisk **Zakończ**.
15. Na stronie ustawień **Ogólne** dla zestawu danych wprowadź wartość **SinkBlobDataset** w polu **Nazwa**.
16. Przejdź do karty **Połączenie** i wykonaj następujące czynności:

    1. Wybierz pozycję **AzureStorageLinkedService** dla elementu **LinkedService**.
    2. Wprowadź wartość `@pipeline().parameters.sinkBlobContainer` jako folder.
    1. Wprowadź wartość `@CONCAT(pipeline().RunId, '.txt')` jako nazwę pliku. Wyrażenie wykorzystuje identyfikator bieżącego uruchomienia potoku dla nazwy pliku. Aby uzyskać listę obsługiwanych zmiennych systemowych i wyrażeń, zobacz [Zmienne systemowe](control-flow-system-variables.md) i [Język wyrażeń](control-flow-expression-language-functions.md).

        ![Ustawienia zestawu danych ujścia](./media/tutorial-control-flow-portal/sink-dataset-settings.png)
17. Przejdź do karty **potoku** u góry. W przyborniku **Działania** rozwiń pozycję **Ogólne**, a następnie przeciągnij działanie **Internet** i upuść je na powierzchni projektanta potoku. Ustaw nazwę działania na wartość **SendSuccessEmailActivity**. Działanie internetowe umożliwia wywołanie dowolnego punktu końcowego REST. Aby uzyskać więcej informacji na temat działania, zobacz [Działanie internetowe](control-flow-web-activity.md). Ten potok używa działania internetowego w celu wywołania przepływu pracy poczty e-mail usługi Logic Apps.

    ![Przeciąganie i upuszczanie pierwszego działania internetowego](./media/tutorial-control-flow-portal/success-web-activity-general.png)
18. Z karty **Ogólne** przejdź do karty **Ustawienia** i wykonaj następujące czynności:
    1. W pozycji **Adres URL** określ adres URL dla przepływu pracy aplikacji logiki, który wysyła wiadomość e-mail z informacją o powodzeniu.  
    2. Wybierz wartość **POST** dla pozycji **Metoda**.
    3. Kliknij link **+ Dodaj nagłówek** w sekcji **Nagłówki**.
    4. Dodaj nagłówek **Content-Type** i ustaw go na wartość **application/json**.
    5. Podaj następujący kod JSON w pozycji **Treść**.

        ```json
        {
            "message": "@{activity('Copy1').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```
        Treść wiadomości zawiera następujące właściwości:

       - Wiadomość — przekazywanie wartości elementu `@{activity('Copy1').output.dataWritten`. Uzyskuje dostęp do właściwości poprzedniego działania kopiowania i przekazuje wartość elementu dataWritten. W przypadku wiadomości dotyczącej niepowodzenia przekaż dane wyjściowe błędu zamiast elementu `@{activity('CopyBlobtoBlob').error.message`.
       - Nazwa fabryki danych — przekazywanie wartości elementu `@{pipeline().DataFactory}`. Jest to zmienna systemowa, która umożliwia dostęp do odpowiedniej nazwy fabryki danych. Lista zmiennych systemowych jest dostępna w artykule [Zmienne systemowe](control-flow-system-variables.md).
       - Nazwa potoku — przekazywanie wartości elementu `@{pipeline().Pipeline}`. Jest to również zmienna systemowa, która umożliwia dostęp do odpowiedniej nazwy potoku.
       - Odbiorca — przekazywanie wartości elementu \@pipeline().parameters.receiver). Uzyskuje dostęp do parametrów potoku.

         ![Ustawienia dla pierwszego działania internetowego](./media/tutorial-control-flow-portal/web-activity1-settings.png)         
19. Połącz działanie **Kopiowanie** z działaniem **Internet**, przeciągając zielony przycisk znajdujący się obok działania kopiowania i upuszczając go na działaniu internetowym.

    ![Łączenie działania kopiowania z pierwszym działaniem internetowym](./media/tutorial-control-flow-portal/connect-copy-web-activity1.png)
20. Przeciągnij kolejne działanie **Internet** z przybornika Działania i upuść je na powierzchni projektanta potoku, a następnie ustaw **nazwę** na wartość **SendFailureEmailActivity**.

    ![Nazwa drugiego działania internetowego](./media/tutorial-control-flow-portal/web-activity2-name.png)
21. Przejdź do karty **Ustawienia** i wykonaj następujące czynności:

    1. W pozycji **Adres URL** określ adres URL dla przepływu pracy aplikacji logiki, który wysyła wiadomość e-mail z informacją o niepowodzeniu.  
    2. Wybierz wartość **POST** dla pozycji **Metoda**.
    3. Kliknij link **+ Dodaj nagłówek** w sekcji **Nagłówki**.
    4. Dodaj nagłówek **Content-Type** i ustaw go na wartość **application/json**.
    5. Podaj następujący kod JSON w pozycji **Treść**.

        ```json
        {
            "message": "@{activity('Copy1').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```

        ![Ustawienia dla drugiego działania internetowego](./media/tutorial-control-flow-portal/web-activity2-settings.png)         
22. Wybierz działanie **Kopiowanie** w projektancie potoku, a następnie kliknij przycisk **+->** i zaznacz pozycję **Błąd**.  

    ![Zrzut ekranu pokazujący sposób wybierania błędu w działaniu kopiowania w projektancie potoku.](./media/tutorial-control-flow-portal/select-copy-failure-link.png)
23. Przeciągnij **czerwony** przycisk znajdujący się obok działania kopiowania do drugiego działania internetowego **SendFailureEmailActivity**. Działania można przenosić, tak aby potok wyglądał jak na poniższym obrazie:

    ![Pełny potok ze wszystkimi działaniami](./media/tutorial-control-flow-portal/full-pipeline.png)
24. Aby zweryfikować potok, kliknij przycisk **Weryfikuj** na pasku narzędzi. Zamknij okno **Dane wyjściowe weryfikacji potoku**, klikając przycisk **>>**.

    ![Weryfikowanie potoku](./media/tutorial-control-flow-portal/validate-pipeline.png)
24. Aby opublikować jednostki (zestawy danych, potoki itp.) w usłudze Data Factory, kliknij przycisk **Opublikuj wszystko**. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano**.

    ![Publikowanie](./media/tutorial-control-flow-portal/publish-button.png)

## <a name="trigger-a-pipeline-run-that-succeeds"></a>Wyzwalanie pomyślnego uruchomienia potoku
1. Aby **wyzwolić** uruchomienie potoku, kliknij pozycję **Wyzwól** na pasku narzędzi, a następnie kliknij polecenie **Wyzwól teraz**.

    ![Wyzwalanie uruchomienia potoku](./media/tutorial-control-flow-portal/trigger-now-menu.png)
2. W oknie **Uruchomienie potoku** wykonaj następujące czynności:

    1. Wprowadź wartość **adftutorial/adfv2branch/input** dla parametru **sourceBlobContainer**.
    2. Wprowadź wartość **adftutorial/adfv2branch/output** dla parametru **sinkBlobContainer**.
    3. Wprowadź **adres e-mail** dla parametru **receiver**.
    4. Kliknij przycisk **Zakończ** .

        ![Parametry uruchomienia potoku](./media/tutorial-control-flow-portal/pipeline-run-parameters.png)

## <a name="monitor-the-successful-pipeline-run"></a>Monitorowanie pomyślnego uruchomienia potoku

1. Aby monitorować uruchomienie potoku, przejdź do karty **Monitorowanie** po lewej stronie. Zostanie wyświetlone uruchomienie potoku, które zostało wyzwolone ręcznie przez Ciebie. Kliknij przycisk **Odśwież**, aby odświeżyć listę.

    ![Pomyślne uruchomienie potoku](./media/tutorial-control-flow-portal/monitor-success-pipeline-run.png)
2. Aby **wyświetlić uruchomienia działań** skojarzone z tym uruchomieniem potoku, kliknij pierwszy link w kolumnie **Akcje**. Do poprzedniego widoku można wrócić, klikając pozycję **Potoki** u góry. Kliknij przycisk **Odśwież**, aby odświeżyć listę.

    ![Zrzut ekranu pokazujący sposób wyświetlania listy uruchomień działania.](./media/tutorial-control-flow-portal/activity-runs-success.png)

## <a name="trigger-a-pipeline-run-that-fails"></a>Wyzwalanie niepomyślnego uruchomienia potoku
1. Przejdź do karty **Edycja** po lewej stronie.
2. Aby **wyzwolić** uruchomienie potoku, kliknij pozycję **Wyzwól** na pasku narzędzi, a następnie kliknij polecenie **Wyzwól teraz**.
3. W oknie **Uruchomienie potoku** wykonaj następujące czynności:

    1. Wprowadź wartość **adftutorial/dummy/input** dla parametru **sourceBlobContainer**. Upewnij się, że folder „dummy” nie istnieje w kontenerze adftutorial.
    2. Wprowadź wartość **adftutorial/dummy/output** dla parametru **sinkBlobContainer**.
    3. Wprowadź **adres e-mail** dla parametru **receiver**.
    4. Kliknij przycisk **Finish** (Zakończ).

## <a name="monitor-the-failed-pipeline-run"></a>Monitorowanie zakończonego niepowodzeniem uruchomienia potoku

1. Aby monitorować uruchomienie potoku, przejdź do karty **Monitorowanie** po lewej stronie. Zostanie wyświetlone uruchomienie potoku, które zostało wyzwolone ręcznie przez Ciebie. Kliknij przycisk **Odśwież**, aby odświeżyć listę.

    ![Zakończone niepowodzeniem uruchomienie potoku](./media/tutorial-control-flow-portal/monitor-failure-pipeline-run.png)
2. Kliknij link **Błąd** dla uruchomienia potoku, aby wyświetlić szczegóły dotyczące błędu.

    ![Błąd potoku](./media/tutorial-control-flow-portal/pipeline-error-message.png)
2. Aby **wyświetlić uruchomienia działań** skojarzone z tym uruchomieniem potoku, kliknij pierwszy link w kolumnie **Akcje**. Kliknij przycisk **Odśwież**, aby odświeżyć listę. Zwróć uwagę, że działanie kopiowania w potoku nie powiodło się. Działanie internetowe powiodło się i do określonego odbiorcy została wysłana wiadomość o niepowodzeniu.

    ![Uruchomienia działania](./media/tutorial-control-flow-portal/activity-runs-failure.png)
4. Kliknij link **Błąd** w kolumnie **Akcje**, aby wyświetlić szczegóły dotyczące błędu.

    ![Błąd uruchamiania działania](./media/tutorial-control-flow-portal/activity-run-error.png)

## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka wykonano następujące procedury:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie połączonej usługi Azure Storage
> * Tworzenie zestawu danych obiektów blob platformy Azure
> * Tworzenie potoku zawierającego działanie kopiowania i działanie internetowe
> * Wysyłanie danych wyjściowych działań do kolejnych działań
> * Korzystanie z przekazywania parametrów i ze zmiennych systemowych
> * Uruchamianie potoku
> * Monitorowanie uruchomień działań i potoku

Teraz możesz przejść do sekcji Pojęcia, aby uzyskać więcej informacji na temat usługi Azure Data Factory.
> [!div class="nextstepaction"]
>[Potoki i działania](concepts-pipelines-activities.md)
