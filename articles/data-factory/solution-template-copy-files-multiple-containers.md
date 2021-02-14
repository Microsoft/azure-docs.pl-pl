---
title: Kopiowanie plików z wielu kontenerów
description: Dowiedz się, jak używać szablonu rozwiązania do kopiowania plików z wielu kontenerów za pomocą Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: ec7af1e81e0b295491420597636c8443f4d36512
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376092"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Kopiuj wiele folderów za pomocą Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano szablon rozwiązania, którego można użyć do kopiowania kontenerów lub folderów między magazynami opartymi na plikach, gdzie każde działanie kopiowania ma mieć możliwość kopiowania jednego kontenera lub folderu. 

> [!NOTE]
> Jeśli chcesz skopiować pliki z jednego kontenera, bardziej wydajne jest użycie [narzędzia kopiowanie danych](copy-data-tool.md) , aby utworzyć potok z jednym działaniem kopiowania. Szablon w tym artykule jest bardziej niezbędny dla tego prostego scenariusza.

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon wylicza foldery z danego folderu nadrzędnego w źródłowym magazynie magazynu. Następnie kopiuje wszystkie foldery do magazynu docelowego.

Szablon zawiera trzy działania:
- **GetMetadata** skanuje źródłowy magazyn magazynu i pobiera listę podfolderów z danego folderu nadrzędnego.
- Instrukcja **foreach** pobiera listę podfolderów z działania **GetMetadata** , a następnie wykonuje iterację na liście i przekazuje poszczególne foldery do działania kopiowania.
- **Kopiuj** kopiuje wszystkie foldery ze źródłowego magazynu magazynu do magazynu docelowego.

Szablon definiuje następujące parametry:
- *SourceFileFolder* jest częścią ścieżki folderu nadrzędnego magazynu źródła danych: *SourceFileFolder/SourceFileDirectory*, gdzie można uzyskać listę podfolderów. 
- *SourceFileDirectory* jest częścią ścieżki folderu nadrzędnego magazynu źródła danych: *SourceFileFolder/SourceFileDirectory*, gdzie można uzyskać listę podfolderów. 
- *DestinationFileFolder* jest częścią ścieżki folderu nadrzędnego: *DestinationFileFolder/DestinationFileDirectory* , gdzie pliki zostaną skopiowane do magazynu docelowego. 
- *DestinationFileDirectory* jest częścią ścieżki folderu nadrzędnego: *DestinationFileFolder/DestinationFileDirectory* , gdzie pliki zostaną skopiowane do magazynu docelowego. 

Jeśli chcesz skopiować wiele kontenerów z folderów głównych między magazynami magazynów, możesz wprowadzić wszystkie cztery parametry jako */* . Dzięki temu będziesz replikować wszystkie elementy między magazynami.

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Przejdź do szablonu **kopiowanie wielu plików między szablonem magazyny plików** . Utwórz **nowe** połączenie ze źródłowym magazynem magazynu. Źródłowy magazyn magazynów to miejsce, w którym chcesz skopiować pliki z wielu kontenerów.

    ![Utwórz nowe połączenie ze źródłem](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Utwórz **nowe** połączenie z docelowym magazynem magazynu.

    ![Utwórz nowe połączenie z miejscem docelowym](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Wybierz pozycję **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Zostanie wyświetlony potok, jak w poniższym przykładzie:

    ![Pokaż potok](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Wybierz pozycję **Debuguj**, wprowadź **Parametry**, a następnie wybierz pozycję **Zakończ**.

    ![Uruchamianie potoku](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Przejrzyj wyniki.

    ![Przejrzyj wynik](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie masowe z bazy danych przy użyciu tabeli formantów z Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopiowanie plików z wielu kontenerów za pomocą Azure Data Factory](solution-template-copy-files-multiple-containers.md)
