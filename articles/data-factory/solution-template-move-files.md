---
title: Przenoszenie plików między magazynem opartym na plikach
description: Informacje o sposobie przenoszenia plików między magazynem opartym na plikach przy użyciu szablonu rozwiązania Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: b36eb2615e98ee8ea7751c836fd43e81a5a0f4e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414748"
---
# <a name="move-files-with-azure-data-factory"></a>Przenoszenie plików z Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano szablon rozwiązania, którego można użyć do przenoszenia plików z jednego folderu do innego między magazynami opartymi na plikach. Jednym z typowych scenariuszy korzystania z tego szablonu: pliki są ciągle opuszczane do folderu wyładunkowego magazynu źródłowego. Tworząc wyzwalacz harmonogramu, potok ADF może okresowo przenosić te pliki ze źródła do magazynu docelowego.  Sposób, w jaki potok ADF osiąga "przeniesienie plików", pobiera pliki z folderu wyładunkowego, kopiując każdy z nich do innego folderu w magazynie docelowym, a następnie usuwając te same pliki z folderu wyładunkowego w magazynie źródłowym.

> [!NOTE]
> Należy pamiętać, że ten szablon jest przeznaczony do przenoszenia plików, a nie do przenoszenia folderów.  Jeśli chcesz przenieść folder, zmieniając zestaw danych tak, aby zawierał tylko ścieżkę do folderu, a następnie za pomocą działania kopiowania i usuwania, aby odwołać się do tego samego zestawu danych reprezentującego folder, musisz zachować ostrożność. Jest to spowodowane tym, że trzeba upewnić się, że nie będzie nowych plików dołączanych do folderu między operacją kopiowania a operacją usuwania. Jeśli w folderze znajdują się nowe pliki docierające do folderu, gdy działanie kopiowania po prostu zostało zakończone, ale działanie usuwania nie zostało jeszcze jaśniejsze, możliwe jest, że działanie usuwania usunie ten nowy plik, który nie został skopiowany do miejsca docelowego, usuwając cały folder.

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon pobiera pliki ze źródłowego magazynu opartego na plikach. Następnie każdy z nich przenosi do magazynu docelowego.

Szablon zawiera pięć działań:
- **GetMetadata** pobiera listę obiektów, w tym pliki i podfoldery z folderu w magazynie źródłowym. Obiekty nie zostaną cyklicznie pobrane. 
- **Filtr Filtruj** listę obiektów z działania **GetMetadata** , aby wybrać tylko pliki. 
- Instrukcja **foreach** pobiera listę plików z działania **filtru** , a następnie wykonuje iterację na liście i przekazuje każdy plik do działania kopiowania i usuwania działania.
- **Kopiuj** kopiuje jeden plik ze źródła do magazynu docelowego.
- **Delete** usuwa ten sam plik z magazynu źródłowego.

Szablon definiuje cztery parametry:
- *SourceStore_Location* to ścieżka folderu magazynu źródłowego, z której chcesz przenieść pliki. 
- *SourceStore_Directory* to ścieżka podfolderu magazynu źródłowego, z którego chcesz przenieść pliki.
- *DestinationStore_Location* to ścieżka folderu magazynu docelowego, do której chcesz przenieść pliki. 
- *DestinationStore_Directory* to ścieżka podfolderu magazynu docelowego, do którego chcesz przenieść pliki.

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Przejdź do szablonu **przenoszenie plików** . Wybierz istniejące połączenie lub Utwórz **nowe** połączenie ze źródłowym magazynem plików, z którego chcesz przenieść pliki. Należy pamiętać, że **DataSource_Folder** i **DataSource_File** odnoszą się do tego samego połączenia ze źródłowym magazynem plików.

    ![Utwórz nowe połączenie ze źródłem](media/solution-template-move-files/move-files1.png)

2. Wybierz istniejące połączenie lub Utwórz **nowe** połączenie z docelowym magazynem plików, do którego chcesz przenieść pliki.

    ![Utwórz nowe połączenie z miejscem docelowym](media/solution-template-move-files/move-files2.png)

3. Wybierz pozycję **Użyj tego szablonu** karty.
    
4. Zostanie wyświetlony potok, jak w poniższym przykładzie:

    ![Pokaż potok](media/solution-template-move-files/move-files4.png)

5. Wybierz pozycję **Debuguj**, wprowadź **Parametry**, a następnie wybierz pozycję **Zakończ**.   Parametry są ścieżka folderu, z którego chcesz przenieść pliki, oraz ścieżka folderu, do której chcesz przenieść pliki. 

    ![Uruchamianie potoku](media/solution-template-move-files/move-files5.png)

6. Przejrzyj wynik.

    ![Przejrzyj wynik](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Następne kroki

- [Kopiuj nowe i zmienione pliki przez LastModifiedDate z Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Kopiowanie plików z wielu kontenerów za pomocą Azure Data Factory](solution-template-copy-files-multiple-containers.md)