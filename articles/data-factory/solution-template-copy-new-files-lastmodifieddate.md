---
title: Kopiuj nowe i zmienione pliki według LastModifiedDate
description: Dowiedz się, jak używać szablonu rozwiązania do kopiowania nowych i zmienionych plików przez LastModifiedDate przy użyciu Azure Data Factory.
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
ms.openlocfilehash: 99d90e4d93f0e4a70350a5a33a65700c3e14acb4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000814"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Kopiuj nowe i zmienione pliki przez LastModifiedDate z Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano szablon rozwiązania, którego można użyć do kopiowania nowych i zmienionych plików tylko przez LastModifiedDate z magazynu opartego na plikach do magazynu docelowego. 

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon najpierw wybiera nowe i zmienione pliki tylko według ich atrybutów **LastModifiedDate**, a następnie kopiuje te wybrane pliki ze źródła danych do magazynu docelowego danych.

Szablon zawiera jedno działanie:
- **Kopiuj** , aby skopiować nowe i zmienione pliki tylko przez LastModifiedDate z magazynu plików do magazynu docelowego.

Szablon definiuje sześć parametrów:
-  *FolderPath_Source* to ścieżka folderu, w której można odczytać pliki z magazynu źródłowego. Należy zastąpić wartość domyślną własną ścieżką folderu.
-  *Directory_Source* to ścieżka podfolderu, w której można odczytać pliki z magazynu źródłowego. Należy zastąpić wartość domyślną własną ścieżką podfolderu.
-  *FolderPath_Destination* to ścieżka folderu, w której chcesz skopiować pliki do magazynu docelowego. Należy zastąpić wartość domyślną własną ścieżką folderu.
-  *Directory_Destination* to ścieżka podfolderu, w którym chcesz skopiować pliki do magazynu docelowego. Należy zastąpić wartość domyślną własną ścieżką podfolderu.
-  *LastModified_From* jest używany do wybierania plików, których atrybut LastModifiedDate ma wartość lub równą tej wartości daty/godziny.  W celu wybrania tylko nowych plików, które nie zostały ostatnio skopiowane, ta wartość daty i godziny może być czasem, w którym potok został wyzwolony jako ostatni. Wartość domyślną "2019-02-01T00:00:00Z" można zastąpić oczekiwaną LastModifiedDate w strefie czasowej UTC. 
-  *LastModified_To* służy do wybierania plików, których atrybut LastModifiedDate jest wcześniejszy niż ta wartość daty i godziny. W celu wybrania tylko nowych plików, które nie zostały ostatnio skopiowane, ta wartość daty i godziny może być obecna.  Wartość domyślną "2019-02-01T00:00:00Z" można zastąpić oczekiwaną LastModifiedDate w strefie czasowej UTC. 

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Przejdź do szablonu **Kopiuj nowe pliki tylko przez LastModifiedDate**. Utwórz **nowe** połączenie ze źródłowym magazynem magazynu. Źródłowy magazyn magazynów to miejsce, z którego mają zostać skopiowane pliki.

    ![Utwórz nowe połączenie ze źródłem](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Utwórz **nowe** połączenie z magazynem docelowym. Magazyn docelowy to miejsce, do którego chcesz skopiować pliki. 

    ![Utwórz nowe połączenie z miejscem docelowym](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Wybierz pozycję **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. Na panelu zostanie wyświetlony potok, jak pokazano w następującym przykładzie:

    ![Pokaż potok](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Wybierz pozycję **Debuguj**, Zapisz wartość **parametrów** i wybierz pozycję **Zakończ**.  Na poniższej ilustracji ustawimy parametry w następujący sposób.
   - **FolderPath_Source** = SourceFolder
   - **Directory_Source** = podfolder
   - **FolderPath_Destination** = destinationfolder
   - **Directory_Destination** = podfolder
   - **LastModified_From** = 2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    Przykład wskazuje, że pliki, które zostały ostatnio zmodyfikowane w przedziale czasu (**2019-02-01T00:00:00Z** do **2019-03-01T00:00:00Z**) zostaną skopiowane z ścieżki źródłowej **SourceFolder/** podfolderu do ścieżki docelowej **destinationfolder/podfolder**.  Można je zastąpić własnymi parametrami.

    ![Uruchamianie potoku](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Przejrzyj wyniki. Zobaczysz tylko te pliki, które ostatnio zmodyfikowano w skonfigurowanym przedziale czasu, zostały skopiowane do magazynu docelowego.

    ![Przejrzyj wynik](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Teraz można dodać wyzwalacz wirowania systemu Windows w celu zautomatyzowania tego potoku, aby potok mógł zawsze kopiować nowe i zmienione pliki tylko przez LastModifiedDate okresowo.  Wybierz pozycję **Dodaj wyzwalacz**, a następnie wybierz pozycję **Nowy/Edytuj**.

    ![Zrzut ekranu, który podświetla opcję menu Nowy/Edytuj, która pojawia się po wybraniu opcji Dodaj wyzwalacz.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. W oknie **Dodawanie wyzwalaczy** wybierz pozycję **+ Nowy**.

9. Wybierz **okno wirowania** dla typu wyzwalacza, ustaw **co 15 minut** jako cykl (można zmienić na dowolny czas interwału). Wybierz opcję **tak** dla aktywowanego pola, a następnie wybierz przycisk **OK**.

    ![Tworzenie wyzwalacza](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Ustaw wartość **parametrów uruchamiania wyzwalacza** w następujący sposób, a następnie wybierz pozycję **Zakończ**.
    - **FolderPath_Source**  =  **SourceFolder**.  Możesz zastąpić folder w źródłowym magazynie danych.
    - **Directory_Source**  =  **podfolder**.  Możesz zamienić na podfolder w źródłowym magazynie danych.
    - **FolderPath_Destination**  =  **destinationfolder**.  Możesz zamienić folder na docelowy magazyn danych.
    - **Directory_Destination**  =  **podfolder**.  Możesz zamienić na podfolder w docelowym magazynie danych.
    - **LastModified_From**  =   **\@ Trigger (). Output. windowStartTime**.  Jest to zmienna systemowa z wyzwalacza określająca czas ostatniego uruchomienia potoku.
    - **LastModified_To**  =  **\@ Trigger (). Output. windowEndTime**.  Jest to zmienna systemowa z wyzwalacza określająca czas, w którym potok jest wyzwalany w tym czasie.
    
    ![Parametry wejściowe](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Wybierz pozycję **Opublikuj wszystkie**.
    
    ![Publikuj wszystko](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Utwórz nowe pliki w folderze źródłowym magazynu źródła danych.  Trwa oczekiwanie na automatyczne wyzwolenie potoku, a tylko nowe pliki zostaną skopiowane do magazynu docelowego.

13. Wybierz kartę **monitor** w lewym panelu nawigacyjnym i odczekaj około 15 minut, jeśli cykl wyzwalacza został ustawiony na co 15 minut. 

14. Przejrzyj wyniki. Zobaczysz, że potok zostanie wyzwolony automatycznie co 15 minut, a tylko nowe lub zmienione pliki z magazynu źródłowego zostaną skopiowane do magazynu docelowego w każdym uruchomieniu potoku.

    ![Zrzut ekranu pokazujący wyniki zwracane podczas wyzwalania potoku.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)
