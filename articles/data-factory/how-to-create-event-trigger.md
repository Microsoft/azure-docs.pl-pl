---
title: Tworzenie wyzwalaczy opartych na zdarzeniach w usłudze Azure Data Factory
description: Dowiedz się, jak utworzyć wyzwalacz w usłudze Azure Data Factory, który uruchamia potok w odpowiedzi na zdarzenie.
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
ms.openlocfilehash: d697fb8afe3e92dfe54eb5d89a2ef59425cb0cde
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414925"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Tworzenie wyzwalacza uruchamiającego potok w odpowiedzi na zdarzenie
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano wyzwalacze oparte na zdarzeniach, które można utworzyć w potokach fabryki danych.

Architektura oparta na zdarzeniach (EDA) to typowy wzorzec integracji danych, który obejmuje produkcję, wykrywanie, zużycie i reakcję na zdarzenia. Scenariusze integracji danych często wymagają od klientów usługi Data Factory wyzwalania potoków na podstawie zdarzeń, takich jak nadejście lub usunięcie pliku na koncie usługi Azure Storage. Usługa Data Factory jest teraz zintegrowana z [usługą Azure Event Grid,](https://azure.microsoft.com/services/event-grid/)która umożliwia wyzwalanie potoków w przypadku zdarzenia.

Aby zapoznać się z dziesięciominutowym wprowadzeniem i prezentacją tej funkcji, obejrzyj następujący film:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Integracja opisana w tym artykule zależy od [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Upewnij się, że subskrypcja jest zarejestrowana u dostawcy zasobów usługi Event Grid. Aby uzyskać więcej informacji, zobacz [Dostawcy zasobów i typy](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

W tej sekcji pokazano, jak utworzyć wyzwalacz zdarzeń w interfejsie użytkownika usługi Azure Data Factory.

1. Przejdź do **kanwy autorskiej**

1. W lewym dolnym rogu kliknij przycisk **Wyzwalacze**

1. Kliknij **+ Nowy,** który otworzy nav stronie wyzwalacza

1. Wybierz **zdarzenie** typu wyzwalacza

    ![Tworzenie nowego wyzwalacza zdarzenia](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Wybierz konto magazynu z listy rozwijanej subskrypcji platformy Azure lub ręcznie przy użyciu identyfikatora zasobu konta magazynu. Wybierz kontener, na którym mają wystąpić zdarzenia. Wybór kontenera jest opcjonalny, ale należy pamiętać, że wybranie wszystkich kontenerów może prowadzić do dużej liczby zdarzeń.

   > [!NOTE]
   > Wyzwalacz zdarzeń obsługuje obecnie tylko konta usługi Azure Data Lake Storage Gen2 i ogólnego przeznaczenia w wersji 2 magazynu. Ze względu na ograniczenie usługi Azure Event Grid usługa Azure Data Factory obsługuje tylko maksymalnie 500 wyzwalaczy zdarzeń na konto magazynu.

1. **Ścieżka obiektu Blob rozpoczyna się** od i ścieżka obiektu **blob kończy się** właściwości pozwalają określić kontenery, foldery i nazwy obiektów blob, dla których chcesz otrzymywać zdarzenia. Wyzwalacz zdarzenia wymaga zdefiniowania co najmniej jednej z tych właściwości. Można użyć różnych wzorców dla ścieżki **obiektów blob zaczyna się** i ścieżka obiektu **blob kończy się** właściwości, jak pokazano w przykładach w dalszej części tego artykułu.

    * **Ścieżka obiektu blob rozpoczyna się od:** Ścieżka obiektu blob musi zaczynać się od ścieżki folderu. Prawidłowe `2018/` wartości `2018/april/shoes.csv`obejmują i . Tego pola nie można zaznaczyć, jeśli kontener nie jest zaznaczony.
    * **Ścieżka obiektu blob kończy się:** Ścieżka obiektu blob musi kończyć się nazwą pliku lub rozszerzeniem. Prawidłowe `shoes.csv` wartości `.csv`obejmują i . Nazwa kontenera i folderu są opcjonalne, ale `/blobs/` po określeniu muszą być oddzielone segmentem. Na przykład kontener o nazwie "zamówienia" `/orders/blobs/2018/april/shoes.csv`może mieć wartość . Aby określić folder w dowolnym kontenerze, pomiń wiodący znak "/". Na przykład `april/shoes.csv` wyzwoli zdarzenie w dowolnym `shoes.csv` pliku o nazwie w folderze o nazwie "april" w dowolnym kontenerze. 

1. Wybierz, czy wyzwalacz będzie reagować na zdarzenie **utworzone przez obiekt Blob,** **zdarzenie usunięte obiektu Blob,** czy oba te zdarzenia. W określonej lokalizacji magazynu każde zdarzenie wyzwoli potoki fabryki danych skojarzone z wyzwalaczem.

    ![Konfigurowanie wyzwalacza zdarzenia](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Wybierz, czy wyzwalacz ignoruje obiekty blob z zerowymi bajtami.

1. Po skonfigurowaniu wyzwalacza kliknij **przycisk Dalej: Podgląd danych**. Na tym ekranie przedstawiono istniejące obiekty blob dopasowane do konfiguracji wyzwalacza zdarzeń. Upewnij się, że masz określone filtry. Konfigurowanie filtrów, które są zbyt szerokie, może odpowiadać dużej liczbie utworzonych/usuniętych plików i może znacząco wpłynąć na koszt. Po zweryfikowaniu warunków filtru kliknij przycisk **Zakończ**.

    ![Podgląd danych wyzwalacza zdarzeń](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Aby dołączyć potok do tego wyzwalacza, przejdź do kanwy potoku i kliknij pozycję **Dodaj wyzwalacz** i wybierz pozycję **Nowy/Edytuj**. Gdy pojawi się boczna nawiga, kliknij pozycję **Wybierz wyzwalacz...** i wybierz utworzony wyzwalacz. Kliknij **przycisk Dalej: Podgląd danych,** aby potwierdzić, że konfiguracja jest poprawna, a następnie **przycisk Dalej,** aby sprawdzić poprawność podglądu danych, jest poprawna.

1. Jeśli potok ma parametry, można określić je na urządzeniu nawigacyjnym po stronie wyzwalacza uruchamia po stronie parametru. Wyzwalacz zdarzenia przechwytuje ścieżkę folderu i nazwę pliku `@triggerBody().folderPath` obiektu `@triggerBody().fileName`blob do właściwości i . Aby użyć wartości tych właściwości w potoku, należy mapować właściwości do parametrów potoku. Po mapowaniu właściwości do parametrów, można uzyskać dostęp `@pipeline().parameters.parameterName` do wartości przechwyconych przez wyzwalacz za pośrednictwem wyrażenia w potoku. Po zakończeniu kliknij przycisk **Zakończ.**

    ![Mapowanie właściwości do parametrów potoku](media/how-to-create-event-trigger/event-based-trigger-image4.png)

W poprzednim przykładzie wyzwalacz jest skonfigurowany do uruchamiania, gdy ścieżka obiektu blob kończąca się na .csv jest tworzony w folderze testowania zdarzeń w kontenerze danych próbki. Właściwości **folderPath** i **fileName** przechwytują lokalizację nowego obiektu blob. Na przykład, gdy plik MoviesDB.csv jest dodawany do `@triggerBody().folderPath` ścieżki próbkowania danych/testowania zdarzeń, ma wartość `sample-data/event-testing` i `@triggerBody().fileName` ma wartość `moviesDB.csv`. Te wartości są mapowane w `sourceFolder` przykładzie `sourceFile` do parametrów potoku `@pipeline().parameters.sourceFolder` i `@pipeline().parameters.sourceFile` które mogą być używane w potoku jako i odpowiednio.

## <a name="json-schema"></a>Schemat JSON

Poniższa tabela zawiera omówienie elementów schematu, które są związane z wyzwalaczy opartych na zdarzeniach:

| **JSON Element** | **Opis** | **Typ** | **Dozwolone wartości** | **Wymagane** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Zakres** | Identyfikator zasobu usługi Azure Resource Manager konta magazynu. | Ciąg | Identyfikator usługi Azure Resource Manager | Tak |
| **Zdarzenia** | Typ zdarzeń, które powodują, że ten wyzwalacz do ognia. | Tablica    | Microsoft.Storage.BlobTworzenie, Microsoft.Storage.BloBDeleted | Tak, dowolna kombinacja tych wartości. |
| **blobPathBeginsWith** | Ścieżka obiektu blob musi zaczynać się od wzorca przewidzianego dla wyzwalacza do ognia. Na przykład `/records/blobs/december/` tylko uruchamia wyzwalacz dla `december` obiektów `records` blob w folderze pod kontenerem. | Ciąg   | | Musisz podać wartość dla co najmniej jednej z `blobPathBeginsWith` `blobPathEndsWith`tych właściwości: lub . |
| **blobPathEndsWith** | Ścieżka obiektu blob musi kończyć się wzorem przewidzianym dla wyzwalacza do ognia. Na przykład `december/boxes.csv` tylko uruchamia wyzwalacz `boxes` dla `december` obiektów blob o nazwie w folderze. | Ciąg   | | Musisz podać wartość dla co najmniej jednej z `blobPathBeginsWith` `blobPathEndsWith`tych właściwości: lub . |
| **ignoreEmptyBlobs** | Określa, czy obiekty blob o zerowym bajtach wyzwoli uruchomienie potoku. Domyślnie jest to ustawiona na true. | Wartość logiczna | true lub false | Nie |

## <a name="examples-of-event-based-triggers"></a>Przykłady wyzwalaczy opartych na zdarzeniach

W tej sekcji przedstawiono przykłady ustawień wyzwalacza opartego na zdarzeniach.

> [!IMPORTANT]
> Należy uwzględnić `/blobs/` segment ścieżki, jak pokazano w poniższych przykładach, za każdym razem, gdy określasz kontener i folder, kontener i plik lub kontener, folder i plik. W przypadku **obiektu blobPathBeginsWith**interfejs użytkownika `/blobs/` fabryki danych zostanie automatycznie dodany między nazwą folderu i kontenera w wyzwalaczu JSON.

| Właściwość | Przykład | Opis |
|---|---|---|
| **Ścieżka obiektu blob zaczyna się od** | `/containername/` | Odbiera zdarzenia dla dowolnego obiektu blob w kontenerze. |
| **Ścieżka obiektu blob zaczyna się od** | `/containername/blobs/foldername/` | Odbiera zdarzenia dla wszystkich `containername` obiektów `foldername` blob w kontenerze i folderze. |
| **Ścieżka obiektu blob zaczyna się od** | `/containername/blobs/foldername/subfoldername/` | Można również odwoływać się do podfolderu. |
| **Ścieżka obiektu blob zaczyna się od** | `/containername/blobs/foldername/file.txt` | Odbiera zdarzenia dla obiektu `file.txt` blob o nazwie w folderze `foldername` pod kontenerem. `containername` |
| **Ścieżka obiektu blob kończy się** | `file.txt` | Odbiera zdarzenia dla obiektu `file.txt` blob o nazwie w dowolnej ścieżce. |
| **Ścieżka obiektu blob kończy się** | `/containername/blobs/file.txt` | Odbiera zdarzenia dla obiektu `file.txt` blob o nazwie w kontenerze `containername`. |
| **Ścieżka obiektu blob kończy się** | `foldername/file.txt` | Odbiera zdarzenia dla obiektu `file.txt` `foldername` blob o nazwie w folderze w dowolnym kontenerze. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [Wykonywanie potoku i wyzwalacze](concepts-pipeline-execution-triggers.md#trigger-execution).
