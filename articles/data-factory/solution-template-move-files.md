---
title: Przenoszenie plików między magazynem opartym na plikach
description: Dowiedz się, jak używać szablonu rozwiązania do przenoszenia plików między magazynem opartym na plikach przy użyciu usługi Azure Data Factory.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414748"
---
# <a name="move-files-with-azure-data-factory"></a>Przenoszenie plików za pomocą usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano szablon rozwiązania, za pomocą którego można przenosić pliki z jednego folderu do drugiego między magazynami opartymi na plikach. Jeden z typowych scenariuszy korzystania z tego szablonu: Pliki są stale upuszczane do folderu docelowego magazynu źródłowego. Tworząc wyzwalacz harmonogramu, potok ADF może okresowo przenosić te pliki ze źródła do magazynu docelowego.  Sposób, w jaki potok ADF osiąga "ruchome pliki", polega na wyjęciem plików z folderu docelowego, skopiowaniu każdego z nich do innego folderu w magazynie docelowym, a następnie usunięciu tych samych plików z folderu docelowego w magazynie źródłowym.

> [!NOTE]
> Należy pamiętać, że ten szablon jest przeznaczony do przenoszenia plików, a nie przenoszenia folderów.  Jeśli chcesz przenieść folder, zmieniając zestaw danych, aby zawierał tylko ścieżkę folderu, a następnie używając działania kopiowania i usuwania w odniesieniu do tego samego zestawu danych reprezentującego folder, należy zachować szczególną ostrożność. To dlatego, że trzeba upewnić się, że nie będzie nowych plików przybywających do folderu między kopiowania operacji i usuwania operacji. Jeśli do folderu pojawiają się nowe pliki w momencie, gdy działanie kopiowania zostało właśnie wykonane, ale działanie Usuń nie zostało werione, możliwe jest, że działanie Usuń usunie ten nowy plik przychodzący, który NIE został skopiowany do miejsca docelowego, usuwając cały folder.

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon pobiera pliki ze magazynu opartego na plikach źródłowych. Następnie przenosi każdy z nich do magazynu docelowego.

Szablon zawiera pięć działań:
- **GetMetadata** pobiera listę obiektów, w tym pliki i podfoldery z folderu w magazynie źródłowym. Nie będzie pobierać obiektów rekursywnie. 
- **Filtruj** listę obiektów z działania **GetMetadata,** aby wybrać tylko pliki. 
- **ForEach** pobiera listę plików z **działania Filtr,** a następnie iteruje nad listą i przekazuje każdy plik do działania Kopiuj i Usuń.
- **Kopiuj** jeden plik ze źródła do magazynu docelowego.
- **Polecenie Usuń** usuwa ten sam plik z magazynu źródłowego.

Szablon definiuje cztery parametry:
- *SourceStore_Location* jest ścieżką folderu magazynu źródłowego, z której chcesz przenieść pliki. 
- *SourceStore_Directory* jest ścieżką podfolderową magazynu źródłowego, z której chcesz przenieść pliki.
- *DestinationStore_Location* jest ścieżką folderu w magazynie docelowym, do której chcesz przenieść pliki. 
- *DestinationStore_Directory* jest ścieżką podfolderową magazynu docelowego, do której chcesz przenieść pliki.

## <a name="how-to-use-this-solution-template"></a>Jak korzystać z tego szablonu rozwiązania

1. Przejdź do szablonu **Przenieś pliki.** Wybierz istniejące połączenie lub utwórz **nowe** połączenie ze źródłowym magazynem plików, z którego chcesz przenieść pliki. Należy pamiętać, że **DataSource_Folder** i **DataSource_File** są odwołania do tego samego połączenia magazynu plików źródłowych.

    ![Tworzenie nowego połączenia ze źródłem](media/solution-template-move-files/move-files1.png)

2. Wybierz istniejące połączenie lub utwórz **nowe** połączenie z docelowym magazynem plików, do którego chcesz przenieść pliki.

    ![Tworzenie nowego połączenia z miejscem docelowym](media/solution-template-move-files/move-files2.png)

3. Wybierz **pozycję Użyj tej** karty szablonu.
    
4. Zobaczysz potok, jak w poniższym przykładzie:

    ![Pokaż potok](media/solution-template-move-files/move-files4.png)

5. Wybierz **debugowanie**, wprowadź **parametry**, a następnie wybierz **zakończ**.   Parametry są ścieżką folderu, z której chcesz przenieść pliki, oraz ścieżką folderu, do której chcesz przenieść pliki. 

    ![Uruchamianie potoku](media/solution-template-move-files/move-files5.png)

6. Przejrzyj wynik.

    ![Przejrzyj wynik](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie nowych i zmienionych plików przez LastModifiedDate za pomocą usługi Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Kopiowanie plików z wielu kontenerów za pomocą usługi Azure Data Factory](solution-template-copy-files-multiple-containers.md)