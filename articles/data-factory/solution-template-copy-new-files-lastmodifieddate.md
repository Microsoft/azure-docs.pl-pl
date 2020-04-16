---
title: Kopiowanie nowych i zmienionych plików przez LastModifiedDate
description: Dowiedz się, jak używać szablonu rozwiązania do kopiowania nowych i zmienionych plików przez LastModifiedDate z usługą Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 979977b2dd2eb5742d4e488623c79cb91427f055
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414796"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Kopiowanie nowych i zmienionych plików przez LastModifiedDate za pomocą usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano szablon rozwiązania, którego można użyć do kopiowania nowych i zmienionych plików tylko przez LastModifiedDate z magazynu opartego na plikach do magazynu docelowego. 

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon najpierw wybiera nowe i zmienione pliki tylko przez ich atrybuty **LastModifiedDate**, a następnie kopiuje te wybrane pliki z magazynu źródeł danych do magazynu docelowego danych.

Szablon zawiera jedno działanie:
- **Kopiuj,** aby skopiować nowe i zmienione pliki tylko przez LastModifiedDate z magazynu plików do magazynu docelowego.

Szablon definiuje sześć parametrów:
-  *FolderPath_Source* jest ścieżka folderu, w której można odczytać pliki z magazynu źródłowego. Musisz zastąpić wartość domyślną własną ścieżką folderu.
-  *Directory_Source* jest ścieżką podfolderową, w której można odczytać pliki z magazynu źródłowego. Należy zastąpić wartość domyślną własną ścieżką podfolderu.
-  *FolderPath_Destination* jest ścieżką folderu, w której chcesz skopiować pliki do magazynu docelowego. Musisz zastąpić wartość domyślną własną ścieżką folderu.
-  *Directory_Destination* jest ścieżką podfolderową, w której chcesz skopiować pliki do magazynu docelowego. Należy zastąpić wartość domyślną własną ścieżką podfolderu.
-  *LastModified_From* służy do wybierania plików, których atrybut LastModifiedDate jest po lub równy tej wartości datetime.  Aby wybrać tylko nowe pliki, które nie zostały skopiowane ostatni raz, ta wartość datetime może być czas, kiedy potok został wyzwolony ostatni raz. Wartość domyślną "2019-02-01T00:00:00Z" można zastąpić oczekiwaną wartością LastModifiedDate w strefie czasowej UTC. 
-  *LastModified_To* służy do wybierania plików, których atrybut LastModifiedDate jest przed tą wartością datetime. Aby wybrać tylko nowe pliki, które nie zostały skopiowane po raz ostatni, ta wartość datetime może być aktualna.  Wartość domyślną "2019-02-01T00:00:00Z" można zastąpić oczekiwaną wartością LastModifiedDate w strefie czasowej UTC. 

## <a name="how-to-use-this-solution-template"></a>Jak korzystać z tego szablonu rozwiązania

1. Przejdź do **szablonu Kopiuj nowe pliki tylko przez LastModifiedDate**. Utwórz **nowe** połączenie z magazynem magazynu źródłowego. Magazyn magazynu źródłowego to miejsce, z którego chcesz skopiować pliki.

    ![Tworzenie nowego połączenia ze źródłem](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Utwórz **nowe** połączenie ze sklepem docelowym. Magazyn docelowy to miejsce, do którego chcesz skopiować pliki. 

    ![Tworzenie nowego połączenia z miejscem docelowym](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Wybierz pozycję **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. Potok będzie dostępny w panelu, jak pokazano w poniższym przykładzie:

    ![Pokaż potok](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Wybierz **debugowanie**, zapisz wartość **parametrów** i wybierz **zakończ**.  Na poniższym zdjęciu ustawiamy parametry w następujący sposób.
   - **FolderPath_Source** = źródło
   - **Directory_Source** = podfolder
   - **FolderPath_Destination** = miejsce docelowe
   - **Directory_Destination** = podfolder
   - **LastModified_From** = 2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    Przykład wskazuje, że pliki, które zostały ostatnio zmodyfikowane w przedziale czasowym (**2019-02-01T00:00:00Z** do **2019-03-01T00:00:00Z**) zostaną skopiowane ze źródła ścieżki źródłowejfolder/podfolder do docelowej ścieżki 2019-03-01T00/podfolder . **sourcefolder/subfolder** **destinationfolder/subfolder**  Można je zastąpić własnymi parametrami.

    ![Uruchamianie potoku](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Przejrzyj wynik. Zobaczysz tylko pliki ostatnio zmodyfikowane w skonfigurowanym przedziale czasu został skopiowany do magazynu docelowego.

    ![Przejrzyj wynik](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Teraz można dodać wyzwalacz okien tumbling zautomatyzować ten potok, tak aby potok zawsze można kopiować nowe i zmienione pliki tylko przez LastModifiedDate okresowo.  Wybierz **pozycję Dodaj wyzwalacz**i wybierz pozycję **Nowy/Edytuj**.

    ![Przejrzyj wynik](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. W oknie **Dodaj wyzwalacze** wybierz pozycję **+ Nowy**.

9. Wybierz **okno tumbling** dla typu wyzwalacza, ustaw **co 15 minut** jako cykl (można zmienić na dowolny interwał). Wybierz **pozycję Tak** dla pola Aktywowane, a następnie wybierz przycisk **OK**.

    ![Tworzenie wyzwalacza](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Ustaw wartość **parametrów uruchomienia wyzwalacza** w następujący sposób i wybierz pozycję **Zakończ**.
    - **FolderPath_Source****sourcefolder .** =   Folder można zastąpić w magazynie danych źródłowych.
    - **Directory_Source****podfolder .** =   Podfolder można zastąpić podfolderem w źródłowym magazynie danych.
    - **FolderPath_Destination****miejsce docelowe**. =   Folder można zastąpić w docelowym magazynie danych.
    - **Directory_Destination****podfolder .** =   Podfolder można zastąpić podfolderem w docelowym magazynie danych.
    - **LastModified_From** =  **\@trigger().outputs.windowStartTime**.  Jest to zmienna systemowa z wyzwalacza określająca czas, kiedy potok został wyzwolony po raz ostatni.
    - **LastModified_To** = **\@trigger().outputs.windowEndTime**.  Jest to zmienna systemowa z wyzwalacza określająca czas, kiedy potok jest wyzwalany tym razem.
    
    ![Parametry wejściowe](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Wybierz pozycję **Opublikuj wszystkie**.
    
    ![Opublikuj wszystko](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Tworzenie nowych plików w folderze źródłowym magazynu źródeł danych.  Teraz czekasz na automatyczne wyzwalanie potoku i tylko nowe pliki zostaną skopiowane do magazynu docelowego.

13. Wybierz kartę **Monitor** w lewym panelu nawigacyjnym i odczekaj około 15 minut, jeśli cykl wyzwalacza jest ustawiony na co 15 minut. 

14. Przejrzyj wynik. Zobaczysz, że potok zostanie wyzwalany automatycznie co 15 minut, a tylko nowe lub zmienione pliki z magazynu źródłowego zostaną skopiowane do magazynu docelowego w każdym uruchomieniu potoku.

    ![Przejrzyj wynik](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)
