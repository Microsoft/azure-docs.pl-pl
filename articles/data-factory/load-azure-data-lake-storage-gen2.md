---
title: Ładowanie danych do usługi Azure Data Lake Storage Gen2
description: Używanie Azure Data Factory do kopiowania danych do Azure Data Lake Storage Gen2
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 4e2bbe6f0c3b9ff1ffd913365ce21e534208123b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699729"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Załaduj dane do Azure Data Lake Storage Gen2 z Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 to zestaw funkcji przeznaczonych do analizy danych Big Data, wbudowanych w [usługę Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Pozwala ona na interfejsowanie danych przy użyciu zarówno odmian systemu plików, jak i magazynu obiektów.

Azure Data Factory (ADF) to w pełni zarządzana usługa integracji danych oparta na chmurze. Możesz użyć usługi, aby wypełnić Lake danymi z bogatego zestawu lokalnych i opartych na chmurze magazynów danych oraz zaoszczędzić czas podczas kompilowania rozwiązań analitycznych. Aby uzyskać szczegółową listę obsługiwanych łączników, zobacz tabelę [obsługiwanych magazynów danych](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory oferuje skalowalne w poziomie rozwiązanie do przenoszenia danych. Ze względu na skalowalną w poziomie architekturę ADF można pozyskać dane przy dużej przepływności. Aby uzyskać szczegółowe informacje, zobacz [wydajność działania kopiowania](copy-activity-performance.md).

W tym artykule pokazano, jak za pomocą narzędzia Kopiowanie danych Data Factory załadować dane z _usługi Amazon Web Services S3_ do _Azure Data Lake Storage Gen2_. Możesz wykonać podobne kroki, aby skopiować dane z innych typów magazynów danych.

>[!TIP]
>Aby skopiować dane z Azure Data Lake Storage Gen1 do programu Gen2, zapoznaj się z [tym konkretnym przewodnikiem](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Konto usługi Azure Storage z włączonym Data Lake Storage Gen2: Jeśli nie masz konta magazynu, [Utwórz konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Konto AWS z pakietem S3 zawierającym dane: w tym artykule przedstawiono sposób kopiowania danych z usługi Amazon S3. Możesz użyć innych magazynów danych, wykonując podobne kroki.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **integracja**  >  **Data Factory**:
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** podaj wartości następujących pól:
 
    * **Nazwa**: wprowadź globalnie unikatową nazwę usługi Azure Data Factory. Jeśli zostanie wyświetlony komunikat o błędzie "Nazwa fabryki danych *twojanazwafabrykidanych nazwapołączonejusługi* jest niedostępna", wprowadź inną nazwę fabryki danych. Można na przykład _**użyć nazwy namename**_**ADFTutorialDataFactory**. Spróbuj ponownie utworzyć fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów**: wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz opcję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz pozycję **Wersja 2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych używane przez fabrykę danych mogą znajdować się w innych lokalizacjach i regionach. 

3. Wybierz przycisk **Utwórz**.

4. Po zakończeniu tworzenia przejdź do fabryki danych. Zostanie wyświetlona strona główna **Data Factory** , jak pokazano na poniższej ilustracji: 
   
   ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)

   Wybierz kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić aplikację Integracja danych.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Ładowanie danych do usługi Azure Data Lake Storage Gen2

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych.

2. Na stronie **Właściwości** Określ **CopyFromAmazonS3ToADLS** dla pola **Nazwa zadania** , a następnie wybierz przycisk **dalej**.

    ![Strona właściwości](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Na stronie **Źródłowy magazyn danych** kliknij pozycję **+ Utwórz nowe połączenie**. Wybierz pozycję **Amazon S3** z galerii łączników, a następnie wybierz pozycję **Kontynuuj**.
    
    ![Strona ze źródłowym magazynem danych S3](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Na stronie **Nowa połączona usługa (Amazon S3)** wykonaj następujące czynności:

   1. Określ wartość **identyfikatora klucza dostępu** .
   2. Określ wartość **klucza dostępu tajnego** .
   3. Kliknij przycisk **Test connection** , aby sprawdzić poprawność ustawień, a następnie wybierz pozycję **Utwórz**.

      ![Określ konto Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. Zostanie wyświetlone nowe połączenie usługi AmazonS3. Wybierz opcję **Dalej**. 

5. Na stronie **Wybieranie pliku lub folderu wejściowego** przejdź do folderu i pliku, z którego ma zostać przeprowadzone kopiowanie. Wybierz folder/plik, a następnie wybierz pozycję **Wybierz**.

    ![Wybieranie pliku lub folderu wejściowego](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Określ zachowanie kopiowania, sprawdzając **cyklicznie** i binarne opcje **kopiowania** . Wybierz opcję **Dalej**.

    ![Zrzut ekranu przedstawia plik lub folder wejściowy, w którym można wybrać kopię binarną i rekursywnie.](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Na stronie **docelowy magazyn danych** kliknij pozycję **+ Utwórz nowe połączenie**, a następnie wybierz pozycję **Azure Data Lake Storage Gen2**, a następnie wybierz pozycję **Kontynuuj**.

    ![Strona Docelowy magazyn danych](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Na stronie **Nowa połączona usługa (Azure Data Lake Storage Gen2)** wykonaj następujące czynności:

   1. Z listy rozwijanej „Nazwa konta magazynu” wybierz swoje konto z możliwością obsługi usługi Data Lake Storage Gen2.
   2. Wybierz pozycję **Utwórz** , aby utworzyć połączenie. Następnie wybierz przycisk **Dalej**.   

        ![Określ konto Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. Na stronie **Wybieranie pliku lub folderu wyjściowego** wprowadź **copyfroms3** jako nazwę folderu wyjściowego, a następnie wybierz przycisk **dalej**. W przypadku, gdy plik ADF nie istnieje, zostanie utworzony odpowiedni system plików ADLS Gen2 i podfoldery.

    ![Zrzut ekranu przedstawia wprowadzaną ścieżkę folderu.](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Na stronie **Ustawienia** wybierz przycisk **Dalej**, aby użyć ustawień domyślnych.

    ![Strona Ustawienia](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz przycisk **dalej**.

    ![Strona podsumowania](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie). 
 
13. Po pomyślnym zakończeniu przebiegu potoku zobaczysz uruchomienie potoku, które jest wyzwalane przez wyzwalacz ręczny. Możesz użyć linków w kolumnie **Nazwa potoku** , aby wyświetlić szczegóły działania i ponownie uruchomić potok.

    ![Monitorowanie uruchomień potoku](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **CopyFromAmazonS3ToADLS** w kolumnie Nazwa potoku. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **szczegóły** (ikona okularów) w kolumnie Nazwa działania. Możesz monitorować szczegóły, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, etapy wykonywania z odpowiednim czasem trwania i używana konfiguracja.
 
    ![Monitorowanie uruchomień działania](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![Monitoruj szczegóły uruchomienia działania](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. Aby odświeżyć widok, wybierz pozycję Odśwież. Zaznacz opcję **wszystkie uruchomienia potoków** u góry, aby wrócić do widoku uruchomienia potoków.

16. Sprawdź, czy dane zostały skopiowane do konta Data Lake Storage Gen2.

## <a name="next-steps"></a>Następne kroki

* [Przegląd działania kopiowania](copy-activity-overview.md)
* [Łącznik usługi Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
