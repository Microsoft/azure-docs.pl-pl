---
title: Ładowanie danych do Azure Data Lake Storage Gen1
description: Używanie Azure Data Factory do kopiowania danych do Azure Data Lake Storage Gen1
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/17/2018
ms.openlocfilehash: 0a111a1a44b508d721f16a038d0919334383d2c5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93125006"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Ładowanie danych do Azure Data Lake Storage Gen1 przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (wcześniej znany jako Azure Data Lake Store) jest repozytorium skalowania w całej firmie dla obciążeń analitycznych danych Big Data. Data Lake Storage Gen1 umożliwia przechwytywanie danych dowolnego rozmiaru, typu i szybkości pozyskiwania. Dane są przechwytywane w jednym miejscu na potrzeby analiz operacyjnych i poznawczych.

Azure Data Factory to w pełni zarządzana usługa integracji danych oparta na chmurze. Możesz użyć usługi, aby wypełnić Lake danymi z istniejącego systemu i zaoszczędzić czas podczas kompilowania rozwiązań analitycznych.

Azure Data Factory oferuje następujące korzyści związane z ładowaniem danych do Data Lake Storage Gen1:

* **Łatwa konfiguracja** : Intuicyjny kreator 5-etapowy bez konieczności wykonywania skryptów.
* **Obsługa rozbudowanych magazynów danych** : Wbudowana obsługa bogatego zestawu lokalnych i opartych na chmurze magazynów danych. Aby uzyskać szczegółową listę, zobacz tabelę [obsługiwanych magazynów danych](copy-activity-overview.md#supported-data-stores-and-formats).
* **Bezpieczeństwo i zgodność** : dane są przesyłane za pośrednictwem protokołu HTTPS lub ExpressRoute. Globalna obecność usługi gwarantuje, że dane nigdy nie opuszczą granicy geograficznej.
* **Wysoka wydajność** : szybkość ładowania danych do 1 GB/s na Data Lake Storage Gen1. Aby uzyskać szczegółowe informacje, zobacz [wydajność działania kopiowania](copy-activity-performance.md).

W tym artykule pokazano, jak za pomocą narzędzia Kopiowanie danych Data Factory _załadować dane z usługi Amazon S3 do programu Data Lake Storage Gen1_ . Możesz wykonać podobne kroki, aby skopiować dane z innych typów magazynów danych.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [Kopiowanie danych do lub z Data Lake Storage Gen1 przy użyciu Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Konto Data Lake Storage Gen1: Jeśli nie masz konta Data Lake Storage Gen1, zapoznaj się z instrukcjami w temacie [Tworzenie konta Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: w tym artykule przedstawiono sposób kopiowania danych z usługi Amazon S3. Możesz użyć innych magazynów danych, wykonując podobne kroki.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Z menu po lewej stronie wybierz pozycję **Utwórz**  >  **Analytics**  >  **Data Factory** analizy zasobów:
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** podaj wartości pól, które są wyświetlane na poniższej ilustracji: 
      
   ![Strona Nowa fabryka danych](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Nazwa** : wprowadź globalnie unikatową nazwę usługi Azure Data Factory. Jeśli zostanie wyświetlony komunikat o błędzie "Nazwa fabryki danych \" LoadADLSG1Demo \" jest niedostępna", wprowadź inną nazwę fabryki danych. Można na przykład _**użyć nazwy namename**_**ADFTutorialDataFactory** . Spróbuj ponownie utworzyć fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja** : wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów** : wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz opcję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja** : Wybierz pozycję **Wersja 2** .
    * **Lokalizacja** : Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych używane przez fabrykę danych mogą znajdować się w innych lokalizacjach i regionach. Te magazyny danych obejmują Azure Data Lake Storage Gen1, Azure Storage, Azure SQL Database i tak dalej.

3. Wybierz pozycję **Utwórz** .
4. Po zakończeniu tworzenia przejdź do fabryki danych. Zostanie wyświetlona strona główna **Data Factory** , jak pokazano na poniższej ilustracji: 
   
   ![Strona główna fabryki danych](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Wybierz kafelek **Tworzenie i monitorowanie** , aby w osobnej karcie uruchomić aplikację Integracja danych.

## <a name="load-data-into-data-lake-storage-gen1"></a>Ładowanie danych do Data Lake Storage Gen1

1. **Na stronie Wprowadzenie** wybierz kafelek **Kopiowanie danych** , aby uruchomić narzędzie kopiowanie danych: 

   ![Kafelek narzędzia do kopiowania danych](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Na stronie **Właściwości** Określ **CopyFromAmazonS3ToADLS** dla pola **Nazwa zadania** , a następnie wybierz przycisk **dalej** :

    ![Strona właściwości](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Na stronie **Magazyn danych źródłowych** kliknij pozycję **+ Utwórz nowe połączenie** :

    ![Strona Źródłowy magazyn danych](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Wybierz pozycję **Amazon S3** , a następnie wybierz pozycję **Kontynuuj** .
    
    ![Strona ze źródłowym magazynem danych S3](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Na stronie **Określanie połączenia usługi Amazon S3** wykonaj następujące czynności: 
   1. Określ wartość **identyfikatora klucza dostępu** .
   2. Określ wartość **klucza dostępu tajnego** .
   3. Wybierz pozycję **Zakończ** .
   
      ![Zrzut ekranu przedstawia okienko Nowa połączona usługa, w którym można wprowadzać wartości.](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Zostanie wyświetlone nowe połączenie. Wybierz opcję **Dalej** .
   
   ![Zrzut ekranu przedstawia nowe połączenie.](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Na stronie **Wybieranie pliku lub folderu wejściowego** przejdź do folderu i pliku, z którego ma zostać przeprowadzone kopiowanie. Wybierz folder/plik, wybierz pozycję **Wybierz** , a następnie wybierz pozycję **dalej** :

    ![Wybieranie pliku lub folderu wejściowego](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Wybierz zachowanie kopiowania, wybierając opcję **Kopiuj pliki cyklicznie** i **binarny** (Kopiuj pliki jako-is). Wybierz pozycję **dalej** :

    ![Zrzut ekranu przedstawia plik lub folder wejściowy, w którym można wybrać opcję Kopiuj plik cyklicznie i kopię binarną.](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Na stronie **docelowy magazyn danych** kliknij pozycję **+ Utwórz nowe połączenie** , a następnie wybierz pozycję **Azure Data Lake Storage Gen1** , a następnie wybierz pozycję **Kontynuuj** :

    ![Strona Docelowy magazyn danych](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Na stronie **Nowa połączona usługa (Azure Data Lake Storage Gen1)** wykonaj następujące czynności: 

   1. W polu **nazwa konta Data Lake Store** wybierz konto Data Lake Storage Gen1.
   2. Określ **dzierżawcę** i wybierz pozycję Zakończ.
   3. Wybierz opcję **Dalej** .
   
   > [!IMPORTANT]
   > W tym instruktażu użyjesz zarządzanej tożsamości dla zasobów platformy Azure w celu uwierzytelnienia konta Data Lake Storage Gen1. Upewnij się, że masz odpowiednie uprawnienia w programie Data Lake Storage Gen1, wykonując [te instrukcje](connector-azure-data-lake-store.md#managed-identity).
   
   ![Określ konto Data Lake Storage Gen1](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Na stronie **Wybieranie pliku lub folderu wyjściowego** wprowadź **copyfroms3** jako nazwę folderu wyjściowego, a następnie wybierz pozycję Next ( **dalej** ): 

    ![Zrzut ekranu przedstawia wprowadzaną ścieżkę folderu.](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Na stronie **Ustawienia** wybierz pozycję **dalej** :

    ![Strona Ustawienia](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz pozycję **dalej** :

    ![Strona podsumowania](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Na **stronie wdrażanie** wybierz pozycję **Monitoruj** , aby monitorować potok (zadanie):

    ![Strona Wdrażanie](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Kolumna **Actions** zawiera linki do wyświetlania szczegółów uruchomienia działania i ponownego uruchomienia potoku:

    ![Monitorowanie uruchomień potoku](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **Wyświetl uruchomienia działania** w kolumnie **Akcje** . W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby przełączyć się z powrotem do widoku uruchomienia potoków, wybierz link **potoki** u góry. Wybierz pozycję **Odśwież** , aby odświeżyć listę. 

    ![Monitorowanie uruchomień działania](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz link **szczegóły** w obszarze **Akcje** w widoku monitorowanie działania. Możesz monitorować szczegóły, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, etapy wykonywania z odpowiednim czasem trwania i używane konfiguracje:

    ![Monitoruj szczegóły uruchomienia działania](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Sprawdź, czy dane zostały skopiowane do konta Data Lake Storage Gen1: 

    ![Weryfikuj Data Lake Storage Gen1 dane wyjściowe](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Następne kroki

Przejdź do poniższego artykułu, aby dowiedzieć się więcej o pomocy technicznej Data Lake Storage Gen1: 

> [!div class="nextstepaction"]
>[Łącznik Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
