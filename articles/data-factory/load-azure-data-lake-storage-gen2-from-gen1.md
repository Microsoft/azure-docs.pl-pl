---
title: Kopiuj dane z Azure Data Lake Storage Gen1 do Gen2
description: Użyj Azure Data Factory, aby skopiować dane z Azure Data Lake Storage Gen1 do Gen2
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 4a4bc0b55928fcaa40cd1cb03ca0522e611df476
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727723"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Skopiuj dane z Azure Data Lake Storage Gen1 do Gen2 z Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 to zestaw funkcji przeznaczony dla analizy danych Big Data, która jest wbudowana w [usługę Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Można ją wykorzystać do połączenia interfejsem z danymi przy użyciu zarówno paradygmatów systemu plików, jak i magazynu obiektów.

Jeśli obecnie używasz Azure Data Lake Storage Gen1, możesz oszacować Azure Data Lake Storage Gen2 kopiując dane z Data Lake Storage Gen1 do Gen2 przy użyciu Azure Data Factory.

Azure Data Factory to w pełni zarządzana usługa integracji danych oparta na chmurze. Możesz użyć usługi, aby wypełnić Lake danymi z bogatego zestawu lokalnych i opartych na chmurze magazynów danych, a także zaoszczędzić czas podczas budowania rozwiązań analitycznych. Aby uzyskać listę obsługiwanych łączników, zobacz tabelę [obsługiwanych magazynów danych](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory oferuje skalowalne w poziomie rozwiązanie do przenoszenia danych. Ze względu na skalowalną w poziomie architekturę Data Factory można pozyskać dane przy wysokiej przepływności. Aby uzyskać więcej informacji, zobacz [wydajność działania kopiowania](copy-activity-performance.md).

W tym artykule pokazano, jak za pomocą narzędzia do kopiowania danych Data Factory skopiować dane z Azure Data Lake Storage Gen1 do Azure Data Lake Storage Gen2. Możesz wykonać podobne kroki, aby skopiować dane z innych typów magazynów danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Azure Data Lake Storage Gen1 z danymi w tym koncie.
* Konto usługi Azure Storage z włączonym Data Lake Storage Gen2. Jeśli nie masz konta magazynu, [Utwórz konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **dane + analiza**  >  **Data Factory**.
   
   ![Data Factory wybór w nowym okienku](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** podaj wartości pól, które są wyświetlane na poniższej ilustracji: 
      
   ![Nowa strona fabryki danych](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nazwa**: wprowadź globalnie unikatową nazwę usługi Azure Data Factory. Jeśli zostanie wyświetlony komunikat o błędzie "Nazwa fabryki danych \" LoadADLSDemo \" jest niedostępna", wprowadź inną nazwę fabryki danych. Na przykład użyj nazwy _**twojanazwa**_**ADFTutorialDataFactory**. Ponownie utwórz fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów**: wybierz istniejącą grupę zasobów z listy rozwijanej. Możesz również wybrać opcję **Utwórz nową** , a następnie wprowadzić nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 
    * **Wersja**: Wybierz pozycję **Wersja 2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych, mogą znajdować się w innych lokalizacjach i regionach. 

3. Wybierz przycisk **Utwórz**.
4. Po zakończeniu tworzenia przejdź do fabryki danych. Zostanie wyświetlona strona główna **Data Factory** , jak pokazano na poniższej ilustracji: 
   
   ![Strona główna fabryki danych](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Wybierz kafelek **tworzenie & monitor** , aby uruchomić aplikację integracji danych w osobnej karcie.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Ładowanie danych do usługi Azure Data Lake Storage Gen2

1. Na stronie **wprowadzenie** wybierz kafelek **Kopiowanie danych** , aby uruchomić narzędzie do kopiowania danych. 

   ![Kafelek Narzędzia do kopiowania danych](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Na stronie **Właściwości** Określ **CopyFromADLSGen1ToGen2** dla pola **Nazwa zadania** . Wybierz opcję **Dalej**.

    ![Strona właściwości](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Na stronie **Magazyn danych źródłowych** wybierz pozycję **+ Utwórz nowe połączenie**.

    ![Strona Źródłowy magazyn danych](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Z galerii łączników wybierz pozycję **Azure Data Lake Storage Gen1**, a następnie przycisk **Kontynuuj**.
    
    ![Strona Azure Data Lake Storage Gen1 magazynu danych źródłowych](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Na stronie **Określanie połączenia Azure Data Lake Storage Gen1** wykonaj następujące kroki:

   a. Wybierz Data Lake Storage Gen1 dla nazwy konta, a następnie określ lub sprawdź poprawność **dzierżawy**.
  
   b. Wybierz **Test connection** , aby sprawdzić poprawność ustawień. Następnie wybierz pozycję **Zakończ**.
  
   c. Zobaczysz, że zostało utworzone nowe połączenie. Wybierz opcję **Dalej**.
   
   > [!IMPORTANT]
   > W tym przewodniku do uwierzytelniania Azure Data Lake Storage Gen1 służy tożsamość zarządzana dla zasobów platformy Azure. Aby udzielić zarządzanej tożsamości odpowiednich uprawnień w Azure Data Lake Storage Gen1, postępuj zgodnie z [tymi instrukcjami](connector-azure-data-lake-store.md#managed-identity).
   
   ![Określ konto Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Na stronie **Wybieranie pliku lub folderu wejściowego** przejdź do folderu i pliku, który chcesz skopiować. Wybierz folder lub plik, a następnie wybierz pozycję **Wybierz**.

    ![Wybieranie pliku lub folderu wejściowego](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Określ zachowanie kopiowania, wybierając opcje **kopiowania cyklicznie** i kopiowania plików **binarnych** . Wybierz opcję **Dalej**.

    ![Zrzut ekranu przedstawia plik lub folder wejściowy, w którym można wybrać opcję Kopiuj plik cyklicznie i kopię binarną.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Na stronie **docelowy magazyn danych** wybierz pozycję **+ Utwórz nowe połączenie**  >  **Azure Data Lake Storage Gen2**  >  **Kontynuuj**.

    ![Strona Docelowy magazyn danych](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Na stronie **Określanie połączenia Azure Data Lake Storage Gen2** wykonaj następujące kroki:

   a. Wybierz konto z możliwością Data Lake Storage Gen2 z listy rozwijanej **nazwa konta magazynu** .
   
   b. Wybierz pozycję **Zakończ**, aby utworzyć połączenie. Następnie wybierz przycisk **Dalej**.
   
   ![Określ konto Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Na stronie **Wybieranie pliku lub folderu wyjściowego** wprowadź **copyfromadlsgen1** jako nazwę folderu wyjściowego, a następnie wybierz przycisk **dalej**. Data Factory tworzy odpowiedni system plików Azure Data Lake Storage Gen2 i podfoldery Podczas kopiowania, jeśli nie istnieją.

    ![Zrzut ekranu przedstawia wprowadzaną ścieżkę folderu.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Na stronie **Ustawienia** wybierz przycisk **Dalej**, aby użyć ustawień domyślnych.

12. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz przycisk **dalej**.

    ![Strona podsumowania](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Na **stronie wdrażanie** wybierz pozycję **Monitoruj** , aby monitorować potok.

    ![Strona Wdrażanie](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Kolumna **Akcje** zawiera linki służące do wyświetlania szczegółów działań i ponownego uruchamiania potoku.

    ![Monitorowanie uruchomień potoku](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **Wyświetl uruchomienia działania** w kolumnie **Akcje** . W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby przełączyć się z powrotem do widoku uruchomienia potoków, wybierz link **potoki** u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 

    ![Monitorowanie uruchomień działania](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz link **szczegóły** (obraz okularów) w obszarze **Akcje** w widoku monitorowanie działania. Możesz monitorować szczegóły, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, etapy wykonywania z odpowiednim czasem trwania i używane konfiguracje.

    ![Monitoruj szczegóły uruchomienia działania](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Sprawdź, czy dane zostały skopiowane do konta Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Najlepsze rozwiązania

Aby dowiedzieć się, jak ogólnie można ocenić uaktualnienie z Azure Data Lake Storage Gen1 do Azure Data Lake Storage Gen2, zobacz [Uaktualnianie rozwiązań do analizy danych Big Data z Azure Data Lake Storage Gen1 do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). W poniższych sekcjach opisano najlepsze rozwiązania dotyczące korzystania z Data Factory uaktualniania danych z Data Lake Storage Gen1 do Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Partycja danych do kopiowania danych historycznych

- Jeśli całkowity rozmiar danych w Data Lake Storage Gen1 jest mniejszy niż 30 TB, a liczba plików jest mniejsza niż 1 000 000, można skopiować wszystkie dane w jednym przebiegu działania kopiowania.
- Jeśli masz większą ilość danych do skopiowania lub chcesz mieć elastyczność zarządzania migracją danych w partiach i wykonać każdy z nich w określonym przedziale czasu, Podziel dane. Partycjonowanie zmniejsza również ryzyko wystąpienia nieoczekiwanego problemu.

Aby sprawdzić kompleksowe rozwiązanie i przetestować przepływność kopiowania w środowisku, należy użyć weryfikacji koncepcji. Główne kroki weryfikacji koncepcji: 

1. Utwórz jeden Data Factory potok z jednym działaniem kopiowania, aby skopiować kilka danych usługi TBs z Data Lake Storage Gen1 do Data Lake Storage Gen2, aby pobrać linię bazową wydajności kopiowania. Zacznij od [jednostek integracji danych (DIUs)](copy-activity-performance-features.md#data-integration-units) jako 128. 
2. W oparciu o przepływność kopiowania uzyskaną w kroku 1 Oblicz szacowany czas wymagany do przeprowadzinia całej migracji danych. 
3. Obowiązkowe Utwórz tabelę formantów i zdefiniuj filtr plików, aby podzielić pliki do migracji. Sposób partycjonowania plików to: 

    - Podziel na partycje według nazwy folderu lub nazwy folderu z filtrem symboli wieloznacznych. Zalecamy wykonanie tej metody.
    - Podziel na partycje według czasu ostatniej modyfikacji pliku.

### <a name="network-bandwidth-and-storage-io"></a>Przepustowość sieci i we/wy magazynu 

Można kontrolować współbieżność zadań Data Factory kopiowania, które odczytują dane z Data Lake Storage Gen1 i zapisują dane w Data Lake Storage Gen2. W ten sposób można zarządzać użyciem tego magazynu we/wy, aby uniknąć wpływu na normalną pracę biznesową na Data Lake Storage Gen1 podczas migracji.

### <a name="permissions"></a>Uprawnienia 

W Data Factory [łącznik Data Lake Storage Gen1](connector-azure-data-lake-store.md) obsługuje jednostkę usługi i zarządzaną tożsamość dla uwierzytelniania zasobów platformy Azure. [Łącznik Data Lake Storage Gen2](connector-azure-data-lake-storage.md) obsługuje klucz konta, nazwę główną usługi i tożsamość zarządzaną dla uwierzytelniania zasobów platformy Azure. Aby Data Factory można było nawigować i kopiować wszystkie pliki lub listy kontroli dostępu (ACL), które są potrzebne, należy przyznać wystarczające uprawnienia dla konta, które zapewnia dostęp, odczytywać lub zapisywać wszystkie pliki i ustawiać listy ACL. Przyznaj mu rolę administratora lub właściciela w okresie migracji. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Zachowaj listy ACL z Data Lake Storage Gen1

Jeśli chcesz replikować listy ACL wraz z plikami danych podczas uaktualniania z Data Lake Storage Gen1 do Data Lake Storage Gen2, zobacz temat [zachowywanie list ACL z Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls). 

### <a name="incremental-copy"></a>Kopia przyrostowa 

Do załadowania tylko nowych lub zaktualizowanych plików z Data Lake Storage Gen1 można użyć kilku metod:

- Załaduj nowe lub zaktualizowane pliki według partycjonowanego folderu lub nazwy pliku. Przykładem jest/2019/05/13/*.
- Załaduj nowe lub zaktualizowane pliki przez LastModifiedDate.
- Zidentyfikuj nowe lub zaktualizowane pliki przez dowolne narzędzie lub rozwiązanie innej firmy. Następnie przekaż nazwę pliku lub folderu do potoku Data Factory za pośrednictwem parametru lub tabeli lub pliku. 

Właściwa częstotliwość ładowania przyrostowego zależy od łącznej liczby plików w Azure Data Lake Storage Gen1 i woluminu nowych lub zaktualizowanych plików do załadowania za każdym razem. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przegląd](copy-activity-overview.md) 
>  działania kopiowania [Łącznik Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) 
>  [Łącznik Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)