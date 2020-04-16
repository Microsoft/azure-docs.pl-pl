---
title: Kopiowanie danych z usługi Azure Data Storage Gen1 do gen2
description: Kopiowanie danych z usługi Azure Data Storage Gen1 do Gen2 za pomocą usługi Azure Data Factory
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
ms.openlocfilehash: 6655510a4cfdb88e98319c7fc26c7ae83255bb6f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415820"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Kopiowanie danych z usługi Azure Data Lake Storage Gen1 do gen2 za pomocą usługi Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Usługa Azure Data Lake Storage Gen2 to zestaw funkcji dedykowanych do analizy dużych zbiorów danych wbudowanych w [magazyn obiektów Blob platformy Azure.](../storage/blobs/storage-blobs-introduction.md) Można go używać do interfejsu z danymi przy użyciu zarówno systemu plików, jak i paradygmatów przechowywania obiektów.

Jeśli obecnie używasz usługi Azure Data Lake Storage Gen1, możesz ocenić usługę Azure Data Lake Storage Gen2, kopiując dane z usługi Data Lake Storage Gen1 do Gen2 przy użyciu usługi Azure Data Factory.

Usługa Azure Data Factory to w pełni zarządzana usługa integracji danych oparta na chmurze. Za pomocą tej usługi można wypełnić jezioro danymi z bogatego zestawu lokalnych i chmurowych magazynów danych oraz zaoszczędzić czas podczas tworzenia rozwiązań analitycznych. Aby uzyskać listę obsługiwanych łączników, zobacz tabelę [obsługiwanych magazynów danych](copy-activity-overview.md#supported-data-stores-and-formats).

Usługa Azure Data Factory oferuje rozwiązanie do skalowania w poziomie, zarządzanego przenoszenia danych. Ze względu na architekturę skalowania w poziomie usługi Data Factory, można pozyskiwania danych z dużą przepływnością. Aby uzyskać więcej informacji, zobacz [Kopiowanie wydajności działania](copy-activity-performance.md).

W tym artykule pokazano, jak używać narzędzia do kopiowania danych w fabryce danych do kopiowania danych z usługi Azure Data Lake Storage Gen1 do usługi Azure Data Lake Storage Gen2. Możesz wykonać podobne kroki, aby skopiować dane z innych typów magazynów danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Konto usługi Azure Data Lake Storage Gen1 z danymi w nim.
* Konto usługi Azure Storage z włączoną funkcją Data Lake Storage Gen2. Jeśli nie masz konta Magazynu, [utwórz konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz źródło** > **Data + Analytics** > **Data Factory**.
   
   ![Wybór fabryki danych w okienku Nowy](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** podaj wartości pól wyświetlanych na poniższym obrazie: 
      
   ![Nowa strona fabryki danych](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nazwa:** Wprowadź globalnie unikatową nazwę dla fabryki danych platformy Azure. Jeśli zostanie wyświetlony błąd "Nazwa \"fabryki danych LoadADLSDemo\" nie jest dostępna", wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_**ADFTutorialDataFactory**. Ponownie utwórz fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: Wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów:** Wybierz istniejącą grupę zasobów z listy rozwijanej. Można również wybrać opcję **Utwórz nowy** i wprowadzić nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 
    * **Wersja**: Wybierz **V2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych, mogą znajdować się w innych lokalizacjach i regionach. 

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia przejdź do fabryki danych. Zostanie wyświetlona strona główna **usługi Data Factory,** jak pokazano na poniższej ilustracji: 
   
   ![Strona główna fabryki danych](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Wybierz kafelek **Monitor & Autora,** aby uruchomić aplikację Integracja danych na osobnej karcie.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Ładowanie danych do usługi Azure Data Lake Storage Gen2

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiuj dane,** aby uruchomić narzędzie kopiowania danych. 

   ![Kopiowanie kafelka narzędzia danych](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Na stronie **Właściwości** określ **polecenie CopyFromADLSGen1ToGen2** dla pola **Nazwa zadania.** Wybierz opcję **Dalej**.

    ![Strona właściwości](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Na stronie **Magazyn danych źródłowych** wybierz pozycję **+ Utwórz nowe połączenie**.

    ![Strona Źródłowy magazyn danych](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Z galerii łączników wybierz pozycję **Azure Data Lake Storage Gen1**, a następnie przycisk **Kontynuuj**.
    
    ![Strona Źródło magazynu danych Usługi Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Na stronie **Określ połączenie usługi Azure Data Lake Gen1,** wykonaj następujące kroki:

   a. Wybierz gen1 magazynu usługi Data Lake dla nazwy konta i określ lub sprawdź poprawność **dzierżawy**.
  
   b. Wybierz **opcję Testuj połączenie,** aby sprawdzić poprawność ustawień. Następnie wybierz pozycję **Zakończ**.
  
   d. Widać, że utworzono nowe połączenie. Wybierz opcję **Dalej**.
   
   > [!IMPORTANT]
   > W tym przejściu, użyj tożsamości zarządzanej dla zasobów platformy Azure do uwierzytelniania usługi Azure Data Lake Storage Gen1. Aby udzielić tożsamości zarządzanej odpowiednich uprawnień w usłudze Azure Data Lake Storage Gen1, wykonaj te [instrukcje.](connector-azure-data-lake-store.md#managed-identity)
   
   ![Określanie konta usługi Azure Data Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Na stronie **Wybierz plik wejściowy lub folder** przejdź do folderu i pliku, który chcesz skopiować. Zaznacz folder lub plik, a następnie wybierz pozycję **Wybierz**.

    ![Wybieranie pliku lub folderu wejściowego](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Określ zachowanie kopiowania, wybierając opcje **kopiowania plików cyklicznie** i **Binarne.** Wybierz opcję **Dalej**.

    ![Określ folder wyjściowy](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Na stronie **Docelowy magazyn danych** wybierz pozycję **+ Utwórz nowe połączenie** > **Usługi Azure Data Lake Storage Gen2** > **Continue**.

    ![Strona Docelowy magazyn danych](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Na stronie **Określ połączenie usługi Azure Data Lake Storage Gen2** wykonaj następujące kroki:

   a. Wybierz konto obsługujące usługę Data Lake Storage Gen2 z listy rozwijanej **Nazwa konta magazynu.**
   
   b. Wybierz pozycję **Zakończ**, aby utworzyć połączenie. Następnie wybierz **przycisk Dalej**.
   
   ![Określanie konta usługi Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Na stronie **Wybierz plik wyjściowy lub folder** wprowadź nazwę folderu wyjściowego **copyfromadlsgen1** i wybierz pozycję **Dalej**. Fabryka danych tworzy odpowiedni system plików usługi Azure Data Lake Storage Gen2 i podfoldery podczas kopiowania, jeśli nie istnieją.

    ![Określ folder wyjściowy](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Na stronie **Ustawienia** wybierz przycisk **Dalej**, aby użyć ustawień domyślnych.

12. Na stronie **Podsumowanie** przejrzyj ustawienia i wybierz pozycję **Dalej**.

    ![Strona podsumowania](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Na **stronie Wdrażanie**wybierz pozycję **Monitoruj,** aby monitorować potok.

    ![Strona Wdrażanie](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Kolumna **Akcje** zawiera linki służące do wyświetlania szczegółów działań i ponownego uruchamiania potoku.

    ![Monitorowanie uruchomień potoku](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Aby wyświetlić przebiegi aktywności skojarzone z uruchomieniem potoku, wybierz łącze **Wyświetl działanie uruchamiane** w kolumnie **Akcje.** W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby przełączyć się z powrotem do widoku przebiegów potoku, wybierz **łącze Potoki** u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 

    ![Monitorowanie uruchomień działania](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz **łącze Szczegóły** (obraz okularów) w obszarze **Akcje** w widoku monitorowania aktywności. Można monitorować szczegóły, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, kroki wykonywania z odpowiednim czasem trwania i używane konfiguracje.

    ![Monitorowanie szczegółów uruchomienia aktywności](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Sprawdź, czy dane są kopiowane do konta usługi Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Najlepsze rozwiązania

Aby ocenić uaktualnienie z usługi Azure Data Lake Storage Gen1 do usługi Azure Data Lake Storage Gen2 w ogóle, zobacz [Uaktualnianie rozwiązań do analizy dużych zbiorów danych z usługi Azure Data Lake Storage Gen1 do usługi Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) W poniższych sekcjach przedstawiono najlepsze rozwiązania dotyczące używania usługi Data Factory do uaktualnienia danych z usługi Data Lake Storage Gen1 do Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Partycja danych do kopiowania danych historycznych

- Jeśli całkowity rozmiar danych w umiaźnej wersji danych Usługi Data Lake Storage Gen1 jest mniejszy niż 30 TB, a liczba plików jest mniejsza niż 1 milion, można skopiować wszystkie dane w jednym uruchomieniu działania kopiowania.
- Jeśli masz większą ilość danych do skopiowania lub chcesz elastyczność zarządzania migracją danych w partiach i uczynić każdy z nich kompletny w określonym przedziale czasu, partycji danych. Partycjonowanie zmniejsza również ryzyko wystąpienia nieoczekiwanego problemu.

Użyj weryfikacji koncepcji, aby zweryfikować kompleksowe rozwiązanie i przetestować przepływność kopiowania w twoim środowisku. Główne kroki weryfikacji koncepcji: 

1. Utwórz jeden potok fabryki danych z pojedynczym działaniem kopiowania, aby skopiować kilka tb danych z usługi Data Lake Storage Gen1 do Data Lake Storage Gen2, aby uzyskać linię bazową wydajności kopiowania. Zacznij od [jednostek integracji danych (DIU)](copy-activity-performance-features.md#data-integration-units) jako 128. 
2. Na podstawie przepływności kopii, którą otrzymujesz w kroku 1, oblicz szacowany czas wymagany dla całej migracji danych. 
3. (Opcjonalnie) Utwórz tabelę formantów i zdefiniuj filtr plików, aby podzielić pliki do migracji. Sposobem na partycje plików jest: 

    - Partycja według nazwy folderu lub nazwy folderu z filtrem wieloznacznym. Zalecamy tę metodę.
    - Partycja przez ostatni zmodyfikowany czas pliku.

### <a name="network-bandwidth-and-storage-io"></a>Przepustowość sieci i we/wy pamięci masowej 

Można kontrolować współbieżność zadań kopiowania usługi Data Factory, które odczytują dane z usługi Data Lake Storage Gen1 i zapisują dane w układzie danych Lake Storage Gen2. W ten sposób można zarządzać użyciem we/wy magazynu, aby uniknąć wpływu na normalną pracę biznesową nad gen1 magazynu usługi Data Lake podczas migracji.

### <a name="permissions"></a>Uprawnienia 

W fabryce danych [łącznik Data Lake Storage Gen1](connector-azure-data-lake-store.md) obsługuje jednostkę usługi i tożsamość zarządzaną dla uwierzytelniania zasobów platformy Azure. [Łącznik Usługi Data Lake Storage Gen2](connector-azure-data-lake-storage.md) obsługuje klucz konta, jednostkę usługi i tożsamość zarządzaną dla uwierzytelniania zasobów platformy Azure. Aby usługa Data Factory mogła nawigować i kopiować wszystkie potrzebne pliki lub listy kontroli dostępu (ACL), należy udzielić wystarczających uprawnień dla konta, które udostępniasz, aby uzyskać dostęp, odczytać lub zapisać wszystkie pliki, a następnie ustawić listy ACL, jeśli użytkownik tego wyboru. Nadaj mu rolę superużytów lub właściciela w okresie migracji. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Zachowywanie list ACL z genu magazynu usługi Data Lake

Jeśli chcesz replikować listy ACL wraz z plikami danych podczas uaktualniania z usługi Data Lake Storage Gen1 do Data Lake Storage Gen2, zobacz [Zachowywanie list ACL z pamięci masowej Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls). 

### <a name="incremental-copy"></a>Kopia przyrostowa 

Można użyć kilku metod, aby załadować tylko nowe lub zaktualizowane pliki z Usługi Data Lake Storage Gen1:

- Załaduj nowe lub zaktualizowane pliki według czasu podzielonego na partycje folderu lub nazwy pliku. Przykładem jest /2019/05/13/*.
- Załaduj nowe lub zaktualizowane pliki przez LastModifiedDate.
- Identyfikowanie nowych lub zaktualizowanych plików za pomocą dowolnego narzędzia lub rozwiązania innej firmy. Następnie przekaż nazwę pliku lub folderu do potoku Data Factory za pomocą parametru lub tabeli lub pliku. 

Właściwa częstotliwość do wykonywania obciążenia przyrostowego zależy od całkowitej liczby plików w usłudze Azure Data Lake Storage Gen1 i woluminu nowych lub zaktualizowanych plików, które mają być ładowane za każdym razem. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Omówienie działania kopiowania](copy-activity-overview.md)
> [Łącznik usługi Azure Data Lake Gen1](connector-azure-data-lake-store.md)
> usługi Azure Data Lake Storage[Gen2](connector-azure-data-lake-storage.md)