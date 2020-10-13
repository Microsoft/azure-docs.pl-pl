---
title: Event Hubs — przechwytywanie zdarzeń przesyłania strumieniowego przy użyciu Azure Portal
description: W tym artykule opisano, jak włączyć przechwytywanie zdarzeń przesyłanych strumieniowo za pomocą usługi Azure Event Hubs przy użyciu witryny Azure Portal.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 2381bfa627d00a78ed91af0ba81579588ee016ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613581"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Włączanie przechwytywania zdarzeń przesyłanych strumieniowo za pomocą usługi Azure Event Hubs

[Przechwytywanie Event Hubs][capture-overview] platformy Azure umożliwia automatyczne dostarczanie danych przesyłanych strumieniowo w Event Hubs do [magazynu obiektów blob platformy Azure](https://azure.microsoft.com/services/storage/blobs/) lub [Azure Data Lake Storage Gen1 lub generacji 2](https://azure.microsoft.com/services/data-lake-store/) .

Przy użyciu witryny [Azure Portal](https://portal.azure.com) można skonfigurować funkcję przechwytywania podczas tworzenia centrum zdarzeń. Możesz przechwytywać dane do kontenera usługi Azure [BLOB Storage](https://azure.microsoft.com/services/storage/blobs/) lub do konta [Azure Data Lake Storage generacji 1 lub generacji 2](https://azure.microsoft.com/services/data-lake-store/) .

Aby uzyskać więcej informacji, zobacz [omówienie funkcji przechwytywania usługi Event Hubs][capture-overview].

> [!IMPORTANT]
> Konto magazynu docelowego (Azure Storage lub Azure Data Lake Storage) musi znajdować się w tej samej subskrypcji co centrum zdarzeń.

## <a name="capture-data-to-azure-storage"></a>Przechwytywanie danych do usługi Azure Storage

Podczas tworzenia centrum zdarzeń można włączyć funkcję przechwytywania przez kliknięcie przycisku **Wł** na ekranie portalu **Tworzenie centrum zdarzeń**. Następnie można określić konto magazynu i kontener przez kliknięcie pozycji **Azure Storage** w polu **Dostawca przechwytywania**. Ponieważ do uwierzytelniania w magazynie funkcja przechwytywania usługi Event Hubs korzysta z uwierzytelniania między usługami, nie trzeba podawać parametrów połączenia z magazynem. Selektor zasobów automatycznie wybiera identyfikator URI zasobu dla konta magazynu. Jeśli używasz usługi Azure Resource Manager, musisz jawnie podać ten identyfikator URI jako ciąg.

Domyślny przedział czasu to 5 minut. Minimalna wartość to 1, a maksymalna — 15. Okno **Rozmiar** ma zakres 10–500 MB.

![Przedział czasu przechwytywania][1]

> [!NOTE]
> Emitowanie pustych plików przy braku zdarzeń podczas okna przechwytywania można włączyć lub wyłączyć. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Przechwytywanie danych do Azure Data Lake Storage generacji 2 

1. Postępuj zgodnie z artykułem [Tworzenie konta magazynu](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) , aby utworzyć konto usługi Azure Storage. Ustaw **hierarchiczną przestrzeń nazw** na wartość **włączone** na karcie **zaawansowane** , aby uczynić ją Azure Data Lake Storage kontem generacji 2.
2. Podczas tworzenia centrum zdarzeń wykonaj następujące czynności: 

    1. Wybierz pozycję **włączone** do **przechwycenia**. 
    2. Wybierz pozycję **Magazyn platformy Azure** jako dostawcę przechwytywania. Opcja **Azure Data Lake Store** wyświetlana dla **dostawcy przechwytywania** jest dla Azure Data Lake Storage generacji 1. Aby użyć generacji 2 Azure Data Lake Storage, należy wybrać **usługę Azure Storage**.
    2. Wybierz przycisk **Wybierz kontener** . 

        ![Włączanie funkcji przechwytywania do Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Wybierz z listy konto **Azure Data Lake Storage Gen 2** . 

    ![Wybieranie Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Wybierz **kontener** (system plików w Data Lake Storage Gen 2).

    ![Wybierz system plików w magazynie](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Na stronie **Tworzenie centrum zdarzeń** wybierz pozycję **Utwórz**. 

    ![Wybierz przycisk Utwórz](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > Kontener utworzony w Azure Data Lake Storage Gen 2 przy użyciu tego interfejsu użytkownika jest wyświetlany w obszarze **systemy plików** w **Eksplorator usługi Storage**. Podobnie system plików tworzony w ramach konta Data Lake Storage generacji 2 jest wyświetlany jako kontener w tym interfejsie użytkownika. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Przechwytywanie danych do Azure Data Lake Storage Gen 1 

Aby przechwytywać dane do Azure Data Lake Storage generacji 1, należy utworzyć konto Data Lake Storage Gen 1 i centrum zdarzeń:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Tworzenie konta i folderów Azure Data Lake Storage generacji 1

1. Utwórz konto Data Lake Storage, postępując zgodnie z instrukcjami w temacie [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen 1 przy użyciu Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md).
2. Postępuj zgodnie z instrukcjami w sekcji [przypisywanie uprawnień do Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) , aby utworzyć folder na koncie Data Lake Storage generacji 1, w którym chcesz przechwytywać dane z Event Hubs, a następnie przypisz uprawnienia do Event Hubs, aby umożliwić zapisanie danych na koncie Data Lake Storage generacji 1.  


### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

1. Centrum zdarzeń musi znajdować się w tej samej subskrypcji platformy Azure co utworzone konto Azure Data Lake Storage Gen 1. Aby utworzyć centrum zdarzeń, kliknij przycisk **Wł** w obszarze **Przechwytywanie** na stronie portalu **Tworzenie centrum zdarzeń**. 
2. Na stronie portalu **Tworzenie centrum zdarzeń** wybierz pozycję **Azure Data Lake Store** w polu **Dostawca przechwytywania**.
3. W obszarze **Wybierz sklep** obok listy rozwijanej **Data Lake Store** określ utworzone wcześniej konto Data Lake Storage generacji 1, a następnie w polu **ścieżka Data Lake** wprowadź ścieżkę do utworzonego folderu danych.

    ![Wybieranie konta usługi Data Lake Storage][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Dodawanie lub konfigurowanie funkcji przechwytywania w istniejącym centrum zdarzeń

Przechwytywanie można skonfigurować w istniejących centrach zdarzeń, które znajdują się w przestrzeniach nazw usługi Event Hubs. Aby włączyć funkcję przechwytywania w istniejącym centrum zdarzeń lub zmienić ustawienia przechwytywania, kliknij przestrzeń nazw w celu załadowania ekranu przeglądu, a następnie kliknij centrum zdarzeń, w którym chcesz włączyć przechwytywanie lub zmienić jego ustawienia. Na końcu kliknij opcję **Przechwyć** z lewej strony otwartej strony i zmodyfikuj ustawienia, jak pokazano na poniższych ilustracjach:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Konfigurowanie usługi Azure Blob Storage][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Konfigurowanie Azure Data Lake Storage generacji 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Konfigurowanie usługi Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat funkcji przechwytywania usługi Event Hubs, przeczytaj [omówienie funkcji przechwytywania usługi Event Hubs][capture-overview].
- Funkcję przechwytywania usługi Event Hubs możesz również skonfigurować za pomocą szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Enable Capture using an Azure Resource Manager template (Włączanie funkcji przechwytywania przy użyciu szablonu usługi Azure Resource Manager)](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Dowiedz się, jak utworzyć subskrypcję usługi Azure Event Grid przy użyciu przestrzeni nazw usługi Event Hubs jako źródła](store-captured-data-data-warehouse.md)
- [Wprowadzenie do Azure Data Lake Store przy użyciu Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
