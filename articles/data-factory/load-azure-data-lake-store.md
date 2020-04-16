---
title: Ładowanie danych do usługi Azure Data Lake Storage Gen1
description: Kopiowanie danych do usługi Azure Data Lake Storage Gen1 za pomocą usługi Azure Data Factory
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
ms.openlocfilehash: 1b1b19814709451bdbbea97462c459149484e71f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415860"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Ładowanie danych do usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Usługa Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (wcześniej znana jako Azure Data Lake Store) to repozytorium hiperskalii dla dużych zbiorów danych w całej organizacji danych. Data Lake Storage Gen1 umożliwia przechwytywanie danych o dowolnym rozmiarze, typie i szybkości pozyskiwania. Dane są przechwytywane w jednym miejscu do analizy operacyjnej i badawczej.

Usługa Azure Data Factory to w pełni zarządzana usługa integracji danych oparta na chmurze. Za pomocą tej usługi można wypełnić jezioro danymi z istniejącego systemu i zaoszczędzić czas podczas tworzenia rozwiązań analitycznych.

Usługa Azure Data Factory oferuje następujące korzyści związane z ładowaniem danych do usługi Data Lake Storage Gen1:

* **Łatwy w konfiguracji:** intuicyjny 5-stopniowy kreator bez konieczności tworzenia skryptów.
* **Obsługa magazynu danych:** wbudowana obsługa bogatego zestawu lokalnych i chmurowych magazynów danych. Aby uzyskać szczegółową listę, zobacz tabelę [obsługiwanych magazynów danych](copy-activity-overview.md#supported-data-stores-and-formats).
* **Bezpieczne i zgodne**: Dane są przesyłane za pośrednictwem protokołu HTTPS lub usługi ExpressRoute. Globalna obecność usługi gwarantuje, że dane nigdy nie opuszczają granicy geograficznej.
* **Wysoka wydajność:** szybkość ładowania danych do 1 GB/s do usługi Data Lake Storage Gen1. Aby uzyskać szczegółowe informacje, zobacz [Kopiowanie wydajności działania](copy-activity-performance.md).

W tym artykule pokazano, jak korzystać z narzędzia Data Factory Copy Data do _ładowania danych z Amazon S3 do usługi Data Lake Storage Gen1_. Możesz wykonać podobne kroki, aby skopiować dane z innych typów magazynów danych.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [Kopiowanie danych do lub z usługi Data Lake Storage Gen1 przy użyciu usługi Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Konto Data Lake Storage Gen1: Jeśli nie masz konta Data Lake Storage Gen1, zobacz instrukcje w [grze Tworzenie konta Gen1 magazynu usługi Data Lake](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: Ten artykuł pokazuje, jak skopiować dane z Amazon S3. Możesz użyć innych magazynów danych, wykonując podobne kroki.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz fabrykę** > **danych****analizy** > zasobów:
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** podaj wartości pól wyświetlanych na poniższym obrazie: 
      
   ![Strona Nowa fabryka danych](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Nazwa:** Wprowadź globalnie unikatową nazwę dla fabryki danych platformy Azure. Jeśli zostanie wyświetlony błąd "Nazwa \"fabryki danych LoadADLSG1Demo\" nie jest dostępna", wprowadź inną nazwę dla fabryki danych. Na przykład można użyć nazwy _**yourname**_**ADFTutorialDataFactory**. Spróbuj ponownie utworzyć fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: Wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów:** Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz opcję **Utwórz nową** i wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz **V2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych może znajdować się w innych lokalizacjach i regionach. Te magazyny danych obejmują usługi Azure Data Lake Storage Gen1, Usługa Azure Storage, usługa Azure SQL Database i tak dalej.

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia przejdź do fabryki danych. Zostanie wyświetlona strona główna **usługi Data Factory,** jak pokazano na poniższej ilustracji: 
   
   ![Strona główna fabryki danych](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Wybierz kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić aplikację Integracja danych.

## <a name="load-data-into-data-lake-storage-gen1"></a>Ładowanie danych do usługi Data Lake Storage Gen1

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiuj dane,** aby uruchomić narzędzie Kopiuj dane: 

   ![Kafelek narzędzia do kopiowania danych](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Na stronie **Właściwości** określ **polecenie CopyFromAmazonS3ToADLS** dla pola **Nazwa zadania** i wybierz pozycję **Dalej:**

    ![Strona właściwości](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Na stronie **Magazyn danych źródłowych** kliknij pozycję **+ Utwórz nowe połączenie:**

    ![Strona Źródłowy magazyn danych](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Wybierz **Amazon S3**i wybierz przycisk **Kontynuuj**
    
    ![Źródło magazynu danych s3 strona](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Na stronie **Określ połączenie Amazon S3** wykonaj następujące czynności: 
   1. Określ wartość **identyfikatora klucza dostępu.**
   2. Określ wartość **klucza tajnego dostępu.**
   3. Wybierz **pozycję Zakończ**.
   
      ![Określ konto Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Zostanie wyświetlenie nowego połączenia. Wybierz opcję **Dalej**.
   
   ![Określ konto Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Na stronie **Wybieranie pliku lub folderu wejściowego** przejdź do folderu i pliku, z którego ma zostać przeprowadzone kopiowanie. Wybierz folder/plik, wybierz pozycję **Wybierz**, a następnie wybierz pozycję **Dalej:**

    ![Wybieranie pliku lub folderu wejściowego](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Wybierz zachowanie kopiowania, wybierając opcje **kopiowania plików cyklicznie** i **kopiuj binarne** (kopiuj pliki w stanie takim, w jakim są). Wybierz **dalej**:

    ![Określ folder wyjściowy](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Na stronie **Docelowy magazyn danych** kliknij pozycję **+ Utwórz nowe połączenie**, a następnie wybierz pozycję Azure Data Lake Storage **Gen1**i wybierz pozycję **Kontynuuj:**

    ![Strona Docelowy magazyn danych](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Na stronie **Nowa usługa połączona (Usługa Azure Data Lake Storage Gen1)** wykonaj następujące czynności: 

   1. Wybierz konto Data Lake Storage Gen1 dla **nazwy konta Data Lake Store**.
   2. Określ **dzierżawę**i wybierz pozycję Zakończ.
   3. Wybierz opcję **Dalej**.
   
   > [!IMPORTANT]
   > W tym instruktażu używasz tożsamości zarządzanej dla zasobów platformy Azure do uwierzytelniania konta Usługi Data Lake Storage Gen1. Należy przyznać MSI odpowiednie uprawnienia w umiasienia danych Lake Storage Gen1, postępując zgodnie z [tymi instrukcjami.](connector-azure-data-lake-store.md#managed-identity)
   
   ![Określanie konta Gen1 magazynu usługi Data Lake](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Na stronie **Wybierz plik wyjściowy lub folder** wprowadź nazwę folderu wyjściowego **copyfroms3** i wybierz pozycję **Dalej:** 

    ![Określ folder wyjściowy](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Na stronie **Ustawienia** wybierz pozycję **Dalej:**

    ![Strona Ustawienia](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Na stronie **Podsumowanie** przejrzyj ustawienia i wybierz pozycję **Dalej:**

    ![Strona podsumowania](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Na **stronie Wdrażanie**wybierz **pozycję Monitor,** aby monitorować potok (zadanie):

    ![Strona Wdrażanie](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Kolumna **Akcje** zawiera łącza do wyświetlania szczegółów uruchomienia działania i ponownego uruchamiania potoku:

    ![Monitorowanie uruchomień potoku](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Aby wyświetlić przebiegi aktywności skojarzone z uruchomieniem potoku, wybierz łącze **Wyświetl działanie uruchamiane** w kolumnie **Akcje.** W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby przełączyć się z powrotem do widoku przebiegów potoku, wybierz **łącze Potoki** u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 

    ![Monitorowanie uruchomień działania](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz **łącze Szczegóły** w obszarze **Akcje** w widoku monitorowania działań. Można monitorować szczegóły, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, kroki wykonywania o odpowiednim czasie trwania i używane konfiguracje:

    ![Monitorowanie szczegółów uruchomienia aktywności](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Sprawdź, czy dane są kopiowane do konta Data Lake Storage Gen1: 

    ![Weryfikowanie danych Lake Storage Gen1 wyjście](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Następne kroki

Przejdź do następującego artykułu, aby dowiedzieć się więcej o obsłudze usługi Data Lake Storage Gen1: 

> [!div class="nextstepaction"]
>[Łącznik usługi Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
