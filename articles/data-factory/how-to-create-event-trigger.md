---
title: Tworzenie wyzwalaczy opartych na zdarzeniach w Azure Data Factory
description: Dowiedz się, jak utworzyć wyzwalacz w Azure Data Factory, który uruchamia potok w odpowiedzi na zdarzenie.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: 10f0079f47e5d2fd99b358fcc5cfb4c80aa9bd91
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84508900"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Tworzenie wyzwalacza uruchamiającego potok w odpowiedzi na zdarzenie
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano wyzwalacze oparte na zdarzeniach, które można utworzyć w potokach Data Factory.

Architektura sterowana zdarzeniami (EDA) to typowy wzorzec integracji danych, który obejmuje produkcję, wykrywanie, użycie i reagowanie na zdarzenia. Scenariusze integracji danych często wymagają Data Factory klientów do wyzwalania potoków w oparciu o zdarzenia, takie jak przyjęcie lub usunięcie pliku na koncie usługi Azure Storage. Data Factory jest teraz zintegrowana z [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), co umożliwia wyzwalanie potoków na zdarzeniu.

W przypadku 10-minutowego wprowadzenia i pokazania tej funkcji Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Integracja opisana w tym artykule zależy od [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Upewnij się, że subskrypcja została zarejestrowana przy użyciu dostawcy zasobów Event Grid. Aby uzyskać więcej informacji, zobacz [dostawcy zasobów i ich typy](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Musisz mieć możliwość wykonania akcji *Microsoft. EventGrid/eventSubscriptions/**. Ta akcja jest częścią wbudowanej roli współautor EventGrid EventSubscription.

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

W tej sekcji pokazano, jak utworzyć wyzwalacz zdarzeń w Azure Data Factory interfejsie użytkownika.

1. Przejdź do **kanwy tworzenia**

1. W lewym dolnym rogu kliknij przycisk **wyzwalacze**

1. Kliknij pozycję **+ Nowy** , aby otworzyć stronę Utwórz wyzwalacz nawigacyjny

1. Wybierz **zdarzenie** typu wyzwalacza

    ![Utwórz nowy wyzwalacz zdarzeń](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Wybierz konto magazynu z listy rozwijanej subskrypcja platformy Azure lub ręcznie przy użyciu identyfikatora zasobu konta magazynu. Wybierz kontener, w którym mają być wykonywane zdarzenia. Wybór kontenera jest opcjonalny, ale pamiętaj, że wybranie wszystkich kontenerów może prowadzić do dużej liczby zdarzeń.

   > [!NOTE]
   > Wyzwalacz zdarzenia obecnie obsługuje tylko konta magazynu Azure Data Lake Storage Gen2 i ogólnego przeznaczenia w wersji 2. Musisz mieć co najmniej dostęp *właściciela* na koncie magazynu.  Ze względu na ograniczenie Azure Event Grid Azure Data Factory obsługuje maksymalnie 500 wyzwalaczy zdarzeń na konto magazynu.

1. **Ścieżka obiektu BLOB rozpoczyna się od** , a **Ścieżka obiektu BLOB zostanie zakończona z** właściwościami, co pozwala na określenie kontenerów, folderów i nazw obiektów blob, dla których mają być odbierane zdarzenia. Wyzwalacz zdarzenia wymaga zdefiniowania co najmniej jednej z tych właściwości. Można użyć różnych wzorców dla **ścieżki obiektu BLOB zaczyna** się od, a **Ścieżka obiektu BLOB zostanie zakończona z** właściwościami, jak pokazano w przykładach w dalszej części tego artykułu.

    * **Ścieżka obiektu BLOB zaczyna się od:** Ścieżka obiektu BLOB musi rozpoczynać się od ścieżki folderu. Prawidłowe wartości to include `2018/` i `2018/april/shoes.csv` . Nie można wybrać tego pola, jeśli nie wybrano kontenera.
    * **Ścieżka obiektu BLOB zostanie zakończona z:** Ścieżka obiektu BLOB musi kończyć się nazwą pliku lub rozszerzeniem. Prawidłowe wartości to include `shoes.csv` i `.csv` . Nazwy kontenerów i folderów są opcjonalne, ale jeśli są określone, muszą być oddzielone `/blobs/` segmentami. Na przykład kontener o nazwie Orders może mieć wartość `/orders/blobs/2018/april/shoes.csv` . Aby określić folder w dowolnym kontenerze, Pomiń wiodący znak "/". Na przykład program `april/shoes.csv` wyzwoli zdarzenie na dowolnym pliku o nazwie `shoes.csv` w folderze a o nazwie "Kwiecień" w dowolnym kontenerze. 

1. Wybierz, czy wyzwalacz będzie reagować na zdarzenie **utworzone przez obiekt BLOB** , **usunięte zdarzenie obiektu BLOB** lub oba te elementy. W określonej lokalizacji przechowywania każde zdarzenie wywoła potoki Data Factory skojarzone z wyzwalaczem.

    ![Konfigurowanie wyzwalacza zdarzeń](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Wybierz, czy wyzwalacz ma ignorować obiekty blob bez bajtów.

1. Po skonfigurowaniu wyzwalacza kliknij przycisk **Dalej: Podgląd danych**. Na tym ekranie są wyświetlane istniejące obiekty blob dopasowane przez konfigurację wyzwalacza zdarzeń. Upewnij się, że masz określone filtry. Skonfigurowanie zbyt szerokich filtrów może być zgodne z dużą liczbą utworzonych/usuniętych plików i może znacząco wpłynąć na koszt. Po zweryfikowaniu warunków filtrowania kliknij przycisk **Zakończ**.

    ![Podgląd danych wyzwalacza zdarzeń](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Aby dołączyć potok do tego wyzwalacza, przejdź do kanwy potoku, a następnie kliknij pozycję **Dodaj wyzwalacz** i wybierz pozycję **Nowy/Edytuj**. Gdy zostanie wyświetlona strona nawigacji bocznej, kliknij pozycję **Wybierz wyzwalacz...** listy rozwijanej i wybierz utworzony wyzwalacz. Kliknij przycisk **Dalej: Podgląd danych** , aby potwierdzić, że konfiguracja jest poprawna, **a następnie sprawdź** , czy wersja zapoznawcza danych jest poprawna.

1. Jeśli potok zawiera parametry, można je określić dla wyzwalacza uruchamia po stronie parametru. Wyzwalacz zdarzenia przechwytuje ścieżkę folderu i nazwę pliku obiektu BLOB do właściwości `@trigger().outputs.body.folderPath` i `@trigger().outputs.body.fileName` . Aby użyć wartości tych właściwości w potoku, należy zmapować właściwości na parametry potoku. Po zamapowaniu właściwości na parametry można uzyskać dostęp do wartości przechwytywanych przez wyzwalacz za pomocą `@pipeline().parameters.parameterName` wyrażenia w ramach potoku. Po zakończeniu kliknij przycisk **Zakończ** .

    ![Mapowanie właściwości do parametrów potoku](media/how-to-create-event-trigger/event-based-trigger-image4.png)

W poprzednim przykładzie wyzwalacz jest skonfigurowany do uruchamiania, gdy ścieżka obiektu BLOB kończąca się w. csv zostanie utworzona w folderze Event-Tests w kontenerze przykład — dane. Właściwości **folderPath** i **filename** przechwytują lokalizację nowego obiektu BLOB. Na przykład po dodaniu MoviesDB.csv do przykładowej ścieżki-dane/testowanie zdarzeń `@trigger().outputs.body.folderPath` ma wartość `sample-data/event-testing` i `@trigger().outputs.body.fileName` ma wartość `moviesDB.csv` . Te wartości są mapowane w przykładzie do parametrów potoku `sourceFolder` , `sourceFile` które mogą być używane w całym potoku odpowiednio w zależności od siebie `@pipeline().parameters.sourceFolder` `@pipeline().parameters.sourceFile` .

## <a name="json-schema"></a>Schemat JSON

Poniższa tabela zawiera omówienie elementów schematu, które są powiązane z wyzwalaczami opartymi na zdarzeniach:

| **Element JSON** | **Opis** | **Typ** | **Dozwolone wartości** | **Wymagane** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Scope** | Identyfikator zasobu Azure Resource Manager konta magazynu. | Ciąg | Identyfikator Azure Resource Manager | Tak |
| **wydarzeniach** | Typ zdarzeń, które powodują uruchomienie tego wyzwalacza. | Tablica    | Microsoft. Storage. BlobCreated, Microsoft. Storage. BlobDeleted | Tak, dowolna kombinacja tych wartości. |
| **blobPathBeginsWith** | Ścieżka obiektu BLOB musi rozpoczynać się od wzorca dostarczonego dla wyzwalacza. Na przykład `/records/blobs/december/` wyzwala wyzwalacz dla obiektów BLOB w `december` folderze w `records` kontenerze. | Ciąg   | | Musisz podać wartość dla co najmniej jednej z następujących właściwości: `blobPathBeginsWith` lub `blobPathEndsWith` . |
| **blobPathEndsWith** | Ścieżka obiektu BLOB musi kończyć się wzorcem podanym dla wyzwalacza. Na przykład `december/boxes.csv` wyzwala wyzwalacz dla obiektów BLOB o nazwie `boxes` w `december` folderze. | Ciąg   | | Musisz podać wartość dla co najmniej jednej z następujących właściwości: `blobPathBeginsWith` lub `blobPathEndsWith` . |
| **ignoreEmptyBlobs** | Czy obiekty blob o zerowym bajcie będą wyzwalać uruchomienie potoku. Domyślnie jest to wartość true. | Boolean (wartość logiczna) | true lub false | Nie |

## <a name="examples-of-event-based-triggers"></a>Przykłady wyzwalaczy opartych na zdarzeniach

Ta sekcja zawiera przykłady ustawień wyzwalaczy opartych na zdarzeniach.

> [!IMPORTANT]
> Musisz dołączyć `/blobs/` segment ścieżki, jak pokazano w poniższych przykładach, po określeniu kontenera i folderu, kontenera i pliku lub kontenera, folderu i pliku. W przypadku **blobPathBeginsWith**interfejs użytkownika Data Factory automatycznie dodaje `/blobs/` między folderem a nazwą kontenera w kodzie JSON wyzwalacza.

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
Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [wykonywanie i wyzwalacze potoku](concepts-pipeline-execution-triggers.md#trigger-execution).
