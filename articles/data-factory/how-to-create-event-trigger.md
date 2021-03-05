---
title: Tworzenie wyzwalaczy opartych na zdarzeniach w Azure Data Factory
description: Dowiedz się, jak utworzyć wyzwalacz w Azure Data Factory, który uruchamia potok w odpowiedzi na zdarzenie.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: ff8c549f74b59706de5203f2d2e46867d6cb1d0a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177792"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>Tworzenie wyzwalacza uruchamiającego potok w odpowiedzi na zdarzenie magazynu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano wyzwalacze zdarzeń magazynu, które można utworzyć w potokach Data Factory.

Architektura sterowana zdarzeniami (EDA) to typowy wzorzec integracji danych, który obejmuje produkcję, wykrywanie, użycie i reagowanie na zdarzenia. Scenariusze integracji danych często wymagają Data Factory klienci mogą wyzwalać potoki na podstawie zdarzeń występujących na koncie magazynu, takich jak przyjęcie lub usunięcie pliku na koncie usługi Azure Blob Storage. Data Factory natywnie integruje się z [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), co umożliwia wyzwalanie potoków dla takich zdarzeń.

W przypadku 10-minutowego wprowadzenia i pokazania tej funkcji Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> Integracja opisana w tym artykule zależy od [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Upewnij się, że subskrypcja została zarejestrowana przy użyciu dostawcy zasobów Event Grid. Aby uzyskać więcej informacji, zobacz [dostawcy zasobów i ich typy](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Musisz mieć możliwość wykonania akcji *Microsoft. EventGrid/eventSubscriptions/**. Ta akcja jest częścią wbudowanej roli współautor EventGrid EventSubscription.

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

W tej sekcji pokazano, jak utworzyć wyzwalacz zdarzeń magazynu w Azure Data Factory interfejsie użytkownika.

1. Przejdź do karty **Edycja** , która jest wyświetlana z symbolem ołówka. 

1. Wybierz pozycję **wyzwalacz** w menu, a następnie wybierz pozycję **Nowy/Edytuj**. 

1. Na stronie **Dodawanie wyzwalaczy** wybierz pozycję **Wybierz wyzwalacz...**, a następnie wybierz pozycję **+ Nowy**. 

1. Wybierz typ wyzwalacza **zdarzenie magazynu**

    ![Utwórz nowy wyzwalacz zdarzenia magazynu](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Wybierz konto magazynu z listy rozwijanej subskrypcja platformy Azure lub ręcznie przy użyciu identyfikatora zasobu konta magazynu. Wybierz kontener, w którym mają być wykonywane zdarzenia. Wybór kontenera jest opcjonalny, ale pamiętaj, że wybranie wszystkich kontenerów może prowadzić do dużej liczby zdarzeń.

   > [!NOTE]
   > Wyzwalacz zdarzenia magazynu obsługuje obecnie tylko konta magazynu Azure Data Lake Storage Gen2 i ogólnego przeznaczenia w wersji 2. Ze względu na ograniczenie Azure Event Grid Azure Data Factory obsługuje maksymalnie 500 wyzwalaczy zdarzeń magazynu na konto magazynu.

   > [!NOTE]
   > Aby utworzyć i zmodyfikować nowy wyzwalacz zdarzeń magazynu, konto platformy Azure używane do logowania się do Data Factory musi mieć co najmniej uprawnienia *właściciela* na koncie magazynu. Nie są wymagane żadne dodatkowe uprawnienia: Nazwa główna usługi dla Azure Data Factory _nie wymaga specjalnego_ uprawnienia do konta magazynu lub Event Grid.

1. **Ścieżka obiektu BLOB rozpoczyna się od** , a **Ścieżka obiektu BLOB zostanie zakończona z** właściwościami, co pozwala na określenie kontenerów, folderów i nazw obiektów blob, dla których mają być odbierane zdarzenia. Wyzwalacz zdarzenia magazynu wymaga zdefiniowania co najmniej jednej z tych właściwości. Można użyć różnych wzorców dla **ścieżki obiektu BLOB zaczyna** się od, a **Ścieżka obiektu BLOB zostanie zakończona z** właściwościami, jak pokazano w przykładach w dalszej części tego artykułu.

    * **Ścieżka obiektu BLOB zaczyna się od:** Ścieżka obiektu BLOB musi rozpoczynać się od ścieżki folderu. Prawidłowe wartości to include `2018/` i `2018/april/shoes.csv` . Nie można wybrać tego pola, jeśli nie wybrano kontenera.
    * **Ścieżka obiektu BLOB zostanie zakończona z:** Ścieżka obiektu BLOB musi kończyć się nazwą pliku lub rozszerzeniem. Prawidłowe wartości to include `shoes.csv` i `.csv` . Nazwy kontenerów i folderów są opcjonalne, ale jeśli są określone, muszą być oddzielone `/blobs/` segmentami. Na przykład kontener o nazwie Orders może mieć wartość `/orders/blobs/2018/april/shoes.csv` . Aby określić folder w dowolnym kontenerze, Pomiń wiodący znak "/". Na przykład program `april/shoes.csv` wyzwoli zdarzenie na dowolnym pliku o nazwie `shoes.csv` w folderze a o nazwie "Kwiecień" w dowolnym kontenerze.
    * Uwaga: Ścieżka obiektu BLOB **zaczyna** się od i ma wartość **kończącą** się na są jedynymi dopasowaniem do wzorca w wyzwalaczu zdarzenia magazynu. Inne typy dopasowania z symbolami wieloznacznymi nie są obsługiwane dla typu wyzwalacza.

1. Wybierz, czy wyzwalacz będzie reagować na zdarzenie **utworzone przez obiekt BLOB** , **usunięte zdarzenie obiektu BLOB** lub oba te elementy. W określonej lokalizacji przechowywania każde zdarzenie wywoła potoki Data Factory skojarzone z wyzwalaczem.

    ![Konfigurowanie wyzwalacza zdarzeń magazynu](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Wybierz, czy wyzwalacz ma ignorować obiekty blob bez bajtów.

1. Po skonfigurowaniu wyzwalacza kliknij przycisk **Dalej: Podgląd danych**. Na tym ekranie są wyświetlane istniejące obiekty blob dopasowane przez konfigurację wyzwalacza zdarzenia magazynu. Upewnij się, że masz określone filtry. Skonfigurowanie zbyt szerokich filtrów może być zgodne z dużą liczbą utworzonych/usuniętych plików i może znacząco wpłynąć na koszt. Po zweryfikowaniu warunków filtrowania kliknij przycisk **Zakończ**.

    ![Podgląd danych wyzwalacza zdarzeń magazynu](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Aby dołączyć potok do tego wyzwalacza, przejdź do kanwy potoku, a następnie kliknij pozycję **Dodaj wyzwalacz** i wybierz pozycję **Nowy/Edytuj**. Gdy zostanie wyświetlona strona nawigacji bocznej, kliknij pozycję **Wybierz wyzwalacz...** listy rozwijanej i wybierz utworzony wyzwalacz. Kliknij przycisk **Dalej: Podgląd danych** , aby potwierdzić, że konfiguracja jest poprawna, **a następnie sprawdź** , czy wersja zapoznawcza danych jest poprawna.

1. Jeśli potok zawiera parametry, można je określić dla wyzwalacza uruchamia po stronie parametru. Wyzwalacz zdarzenia magazynu przechwytuje ścieżkę folderu i nazwę pliku obiektu BLOB do właściwości `@triggerBody().folderPath` i `@triggerBody().fileName` . Aby użyć wartości tych właściwości w potoku, należy zmapować właściwości na parametry potoku. Po zamapowaniu właściwości na parametry można uzyskać dostęp do wartości przechwytywanych przez wyzwalacz za pomocą `@pipeline().parameters.parameterName` wyrażenia w ramach potoku. Po zakończeniu kliknij przycisk **Zakończ** .

    ![Mapowanie właściwości do parametrów potoku](media/how-to-create-event-trigger/event-based-trigger-image4.png)

W poprzednim przykładzie wyzwalacz jest skonfigurowany do uruchamiania, gdy ścieżka obiektu BLOB kończąca się w. csv zostanie utworzona w folderze Event-Tests w kontenerze przykład — dane. Właściwości **folderPath** i **filename** przechwytują lokalizację nowego obiektu BLOB. Na przykład po dodaniu MoviesDB.csv do przykładowej ścieżki-dane/testowanie zdarzeń `@triggerBody().folderPath` ma wartość `sample-data/event-testing` i `@triggerBody().fileName` ma wartość `moviesDB.csv` . Te wartości są mapowane w przykładzie do parametrów potoku `sourceFolder` , `sourceFile` które mogą być używane w całym potoku odpowiednio w zależności od siebie `@pipeline().parameters.sourceFolder` `@pipeline().parameters.sourceFile` .

## <a name="json-schema"></a>Schemat JSON

Poniższa tabela zawiera omówienie elementów schematu, które są powiązane z wyzwalaczami zdarzeń magazynu:

| **Element JSON** | **Opis** | **Typ** | **Dozwolone wartości** | **Wymagane** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Scope** | Identyfikator zasobu Azure Resource Manager konta magazynu. | Ciąg | Identyfikator Azure Resource Manager | Tak |
| **wydarzeniach** | Typ zdarzeń, które powodują uruchomienie tego wyzwalacza. | Tablica    | Microsoft. Storage. BlobCreated, Microsoft. Storage. BlobDeleted | Tak, dowolna kombinacja tych wartości. |
| **blobPathBeginsWith** | Ścieżka obiektu BLOB musi rozpoczynać się od wzorca dostarczonego dla wyzwalacza. Na przykład `/records/blobs/december/` wyzwala wyzwalacz dla obiektów BLOB w `december` folderze w `records` kontenerze. | Ciąg   | | Musisz podać wartość dla co najmniej jednej z następujących właściwości: `blobPathBeginsWith` lub `blobPathEndsWith` . |
| **blobPathEndsWith** | Ścieżka obiektu BLOB musi kończyć się wzorcem podanym dla wyzwalacza. Na przykład `december/boxes.csv` wyzwala wyzwalacz dla obiektów BLOB o nazwie `boxes` w `december` folderze. | Ciąg   | | Musisz podać wartość dla co najmniej jednej z następujących właściwości: `blobPathBeginsWith` lub `blobPathEndsWith` . |
| **ignoreEmptyBlobs** | Czy obiekty blob o zerowym bajcie będą wyzwalać uruchomienie potoku. Domyślnie jest to wartość true. | Wartość logiczna | true lub false | Nie |

## <a name="examples-of-storage-event-triggers"></a>Przykłady wyzwalaczy zdarzeń magazynu

Ta sekcja zawiera przykłady ustawień wyzwalacza zdarzeń magazynu.

> [!IMPORTANT]
> Musisz dołączyć `/blobs/` segment ścieżki, jak pokazano w poniższych przykładach, po określeniu kontenera i folderu, kontenera i pliku lub kontenera, folderu i pliku. W przypadku **blobPathBeginsWith** interfejs użytkownika Data Factory automatycznie dodaje `/blobs/` między folderem a nazwą kontenera w kodzie JSON wyzwalacza.

| Właściwość | Przykład | Opis |
|---|---|---|
| **Ścieżka obiektu BLOB zaczyna się od** | `/containername/` | Odbiera zdarzenia dla dowolnego obiektu BLOB w kontenerze. |
| **Ścieżka obiektu BLOB zaczyna się od** | `/containername/blobs/foldername/` | Odbiera zdarzenia dla wszystkich obiektów BLOB w `containername` kontenerze i `foldername` folderze. |
| **Ścieżka obiektu BLOB zaczyna się od** | `/containername/blobs/foldername/subfoldername/` | Można również odwoływać się do podfolderu. |
| **Ścieżka obiektu BLOB zaczyna się od** | `/containername/blobs/foldername/file.txt` | Odbiera zdarzenia dla obiektu BLOB o nazwie w folderze znajdującym się `file.txt` w `foldername` `containername` kontenerze. |
| **Ścieżka obiektu BLOB zostanie zakończona z** | `file.txt` | Odbiera zdarzenia dla obiektu BLOB o nazwie `file.txt` w dowolnej ścieżce. |
| **Ścieżka obiektu BLOB zostanie zakończona z** | `/containername/blobs/file.txt` | Odbiera zdarzenia dla obiektu BLOB o nazwie `file.txt` w kontenerze `containername` . |
| **Ścieżka obiektu BLOB zostanie zakończona z** | `foldername/file.txt` | Odbiera zdarzenia dla obiektu BLOB o nazwie `file.txt` w `foldername` folderze w dowolnym kontenerze. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [wykonywanie i wyzwalacze potoku](concepts-pipeline-execution-triggers.md#trigger-execution).
* Dowiedz się, jak odwoływać się do metadanych wyzwalacza w potoku, zobacz [metadane wyzwalacza odwołań w uruchomieniach potoków](how-to-use-trigger-parameterization.md)
