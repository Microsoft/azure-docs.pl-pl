---
title: Ładowanie danych do usługi Azure Data Lake Storage Gen2
description: Kopiowanie danych do usługi Azure Data Lake Storage Gen2 za pomocą usługi Azure Data Factory
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: 96674f059e9cbc21c5c8c64eff8c94c810c4aa32
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417780"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Ładowanie danych do usługi Azure Data Lake Storage Gen2 za pomocą usługi Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Usługa Azure Data Lake Storage Gen2 to zestaw funkcji dedykowanych do analizy dużych zbiorów danych wbudowanych w [magazyn obiektów Blob platformy Azure.](../storage/blobs/storage-blobs-introduction.md) Umożliwia interfejs danych przy użyciu zarówno systemu plików, jak i paradygmatów przechowywania obiektów.

Usługa Azure Data Factory (ADF) to w pełni zarządzana usługa integracji danych oparta na chmurze. Za pomocą tej usługi można wypełnić jezioro danymi z bogatego zestawu lokalnych i chmurowych magazynów danych oraz zaoszczędzić czas podczas tworzenia rozwiązań analitycznych. Aby uzyskać szczegółową listę obsługiwanych łączników, zobacz tabelę [obsługiwanych magazynów danych](copy-activity-overview.md#supported-data-stores-and-formats).

Usługa Azure Data Factory oferuje rozwiązanie do skalowania w poziomie, zarządzanego przenoszenia danych. Ze względu na architekturę skalowania w poziomie podajnika ADF, może pozyskiwania danych z dużą przepływnością. Aby uzyskać szczegółowe informacje, zobacz [Kopiowanie wydajności działania](copy-activity-performance.md).

W tym artykule pokazano, jak korzystać z narzędzia Data Factory Copy Data do ładowania danych z _usługi Amazon Web Services S3_ do usługi Azure Data Lake Storage _Gen2_. Możesz wykonać podobne kroki, aby skopiować dane z innych typów magazynów danych.

>[!TIP]
>Aby skopiować dane z usługi Azure Data Lake Storage Gen1 do gen2, zapoznaj się [z tym konkretnym instruktażem](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Konto usługi Azure Storage z włączoną usługą Data Lake Storage Gen2: jeśli nie masz konta magazynu, [utwórz konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Konto AWS z zasobnikiem S3 zawierającym dane: W tym artykule pokazano, jak kopiować dane z Amazon S3. Możesz użyć innych magazynów danych, wykonując podobne kroki.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz źródło** > **Data + Analytics** > **Data Factory**:
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** podaj wartości pól wyświetlanych na poniższym obrazie: 
      
   ![Strona Nowa fabryka danych](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Nazwa:** Wprowadź globalnie unikatową nazwę dla fabryki danych platformy Azure. Jeśli zostanie wyświetlony błąd "Nazwa \"fabryki danych LoadADLSDemo\" nie jest dostępna", wprowadź inną nazwę dla fabryki danych. Na przykład można użyć nazwy _**yourname**_**ADFTutorialDataFactory**. Spróbuj ponownie utworzyć fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: Wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów:** Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz opcję **Utwórz nową** i wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz **V2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych może znajdować się w innych lokalizacjach i regionach. 

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia przejdź do fabryki danych. Zostanie wyświetlona strona główna **usługi Data Factory,** jak pokazano na poniższej ilustracji: 
   
   ![Strona główna fabryki danych](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Wybierz kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić aplikację Integracja danych.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Ładowanie danych do usługi Azure Data Lake Storage Gen2

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiuj dane,** aby uruchomić narzędzie Kopiuj dane: 

   ![Kafelek narzędzia do kopiowania danych](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Na stronie **Właściwości** określ **polecenie CopyFromAmazonS3ToADLS** dla pola **Nazwa zadania** i wybierz pozycję **Dalej:**

    ![Strona właściwości](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Na stronie **Magazyn danych źródłowych** kliknij pozycję **+ Utwórz nowe połączenie:**

    ![Strona Źródłowy magazyn danych](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Wybierz **Amazon S3** z galerii łączników i wybierz pozycję **Kontynuuj**
    
    ![Źródło magazynu danych s3 strona](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Na stronie **Określ połączenie Amazon S3** wykonaj następujące czynności:

   1. Określ wartość **identyfikatora klucza dostępu.**
   2. Określ wartość **klucza tajnego dostępu.**
   3. Kliknij przycisk **Testuj połączenie**, aby zweryfikować ustawienia, a następnie wybierz pozycję **Zakończ**.
   4. Zostanie wyświetleni nowe połączenie zostanie utworzone. Wybierz opcję **Dalej**.
   
      ![Określ konto Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. Na stronie **Wybieranie pliku lub folderu wejściowego** przejdź do folderu i pliku, z którego ma zostać przeprowadzone kopiowanie. Wybierz folder/plik, wybierz pozycję **Wybierz**:

    ![Wybieranie pliku lub folderu wejściowego](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Określ zachowanie kopiowania, zaznaczając opcje **Kopiuj pliki rekursywnie** i **Kopiowanie binarne**. Wybierz **dalej**:

    ![Określ folder wyjściowy](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Na stronie **Docelowy magazyn danych** kliknij pozycję **+ Utwórz nowe połączenie**, a następnie wybierz pozycję Azure Data Lake Storage **Gen2**i wybierz pozycję **Kontynuuj:**

    ![Strona Docelowy magazyn danych](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Na stronie **Określ połączenie usługi Azure Data Lake Storage** wykonaj następujące czynności:

   1. Wybierz konto obsługujące usługę Data Lake Storage Gen2 z listy rozwijanej "Nazwa konta magazynu".
   2. Wybierz pozycję **Zakończ**, aby utworzyć połączenie. Następnie wybierz **przycisk Dalej**.
   
   ![Określanie konta usługi Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Na stronie **Wybierz plik wyjściowy lub folder** wprowadź nazwę folderu wyjściowego **copyfroms3** i wybierz pozycję **Dalej**. ADF utworzy odpowiedni system plików ADLS Gen2 i podfoldery podczas kopiowania, jeśli nie istnieje.

    ![Określ folder wyjściowy](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Na stronie **Ustawienia** wybierz pozycję **Dalej,** aby użyć ustawień domyślnych:

    ![Strona Ustawienia](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Na stronie **Podsumowanie** przejrzyj ustawienia i wybierz pozycję **Dalej:**

    ![Strona podsumowania](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Na **stronie Wdrażanie**wybierz **pozycję Monitor,** aby monitorować potok:

    ![Strona Wdrażanie](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Kolumna **Akcje** zawiera łącza do wyświetlania szczegółów uruchomienia działania i ponownego uruchamiania potoku:

    ![Monitorowanie uruchomień potoku](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Aby wyświetlić przebiegi aktywności skojarzone z uruchomieniem potoku, wybierz łącze **Wyświetl działanie uruchamiane** w kolumnie **Akcje.** W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby przełączyć się z powrotem do widoku przebiegów potoku, wybierz **łącze Potoki** u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 

    ![Monitorowanie uruchomień działania](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz **łącze Szczegóły** (obraz okularów) w obszarze **Akcje** w widoku monitorowania aktywności. Można monitorować szczegóły, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, kroki wykonywania o odpowiednim czasie trwania i używane konfiguracje:

    ![Monitorowanie szczegółów uruchomienia aktywności](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Sprawdź, czy dane są kopiowane do konta Data Lake Storage Gen2.

## <a name="next-steps"></a>Następne kroki

* [Omówienie działania kopiowania](copy-activity-overview.md)
* [Łącznik usługi Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
