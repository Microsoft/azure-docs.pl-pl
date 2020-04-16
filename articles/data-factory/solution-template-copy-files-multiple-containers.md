---
title: Kopiowanie plików z wielu kontenerów
description: Dowiedz się, jak używać szablonu rozwiązania do kopiowania plików z wielu kontenerów przy użyciu usługi Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 383b70bbb02e7a200c7ec0a994f7cf11e9b9520e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414814"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopiowanie plików z wielu kontenerów za pomocą usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano szablon rozwiązania, którego można użyć do kopiowania plików z wielu kontenerów między magazynami plików. Na przykład można go użyć do migracji aplikacji data lake z usługi AWS S3 do usługi Azure Data Lake Store. Lub można użyć szablonu do replikowania wszystkiego z jednego konta magazynu obiektów Blob platformy Azure do innego.

> [!NOTE]
> Jeśli chcesz skopiować pliki z jednego kontenera, bardziej efektywne jest użycie [narzędzia Kopiowanie danych](copy-data-tool.md) do utworzenia potoku z działaniem pojedynczej kopii. Szablon w tym artykule jest więcej niż potrzebujesz dla tego prostego scenariusza.

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon wylicza kontenery z magazynu magazynu źródłowego. Następnie kopiuje te kontenery do magazynu docelowego.

Szablon zawiera trzy działania:
- **GetMetadata** skanuje magazyn magazynu źródłowego i pobiera listę kontenerów.
- **ForEach** pobiera listę kontenerów z **GetMetadata** działania, a następnie iteruje na liście i przekazuje każdy kontener do copy działania.
- **Kopiowanie** kopiuje każdy kontener z magazynu źródłowego do magazynu docelowego.

Szablon definiuje następujące parametry:
- *SourceFileFolder* jest ścieżką folderu magazynu źródła danych, gdzie można uzyskać listę kontenerów. Ścieżka jest katalogiem głównym, który zawiera wiele folderów kontenera. Domyślną wartością tego `sourcefolder`parametru jest .
- *SourceFileDirectory* jest ścieżką podfolderu w katalogu głównym magazynu źródeł danych. Domyślną wartością tego `subfolder`parametru jest .
- *DestinationFileFolder* to ścieżka folderu, do której pliki zostaną skopiowane do magazynu docelowego. Domyślną wartością tego `destinationfolder`parametru jest .
- *DestinationFileDirectory* to ścieżka podfolderu, do której pliki zostaną skopiowane do magazynu docelowego. Domyślną wartością tego `subfolder`parametru jest .

## <a name="how-to-use-this-solution-template"></a>Jak korzystać z tego szablonu rozwiązania

1. Przejdź do **szablonu Kopiuj wiele plików między szablonami Magazyny plików.** Utwórz **nowe** połączenie z magazynem magazynu źródłowego. Magazyn magazynu źródłowego jest, gdzie chcesz skopiować pliki z wielu kontenerów z.

    ![Tworzenie nowego połączenia ze źródłem](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Utwórz **nowe** połączenie z docelowym magazynem magazynowym.

    ![Tworzenie nowego połączenia z miejscem docelowym](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Wybierz pozycję **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Zobaczysz potok, jak w poniższym przykładzie:

    ![Pokaż potok](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Wybierz **debugowanie**, wprowadź **parametry**, a następnie wybierz **zakończ**.

    ![Uruchamianie potoku](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Przejrzyj wynik.

    ![Przejrzyj wynik](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie zbiorcze z bazy danych przy użyciu tabeli sterowania z usługą Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopiowanie plików z wielu kontenerów za pomocą usługi Azure Data Factory](solution-template-copy-files-multiple-containers.md)
