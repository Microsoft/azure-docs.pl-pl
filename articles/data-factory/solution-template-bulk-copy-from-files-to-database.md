---
title: Kopiowanie zbiorcze z plików do bazy danych
description: Dowiedz się, jak używać szablonu rozwiązania do zbiorczego kopiowania danych z Azure Data Lake Storage Gen2 do usługi Azure Synapse Analytics/Azure SQL Database.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 5f68c7b33a26bc8c01f3f413943351ff8d61b380
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376125"
---
# <a name="bulk-copy-from-files-to-database"></a>Kopiowanie zbiorcze z plików do bazy danych

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano szablon rozwiązania, którego można użyć do zbiorczego kopiowania danych z Azure Data Lake Storage Gen2 do usługi Azure Synapse Analytics/Azure SQL Database.

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon pobiera pliki ze źródła Azure Data Lake Storage Gen2. Następnie iteruje każdy plik w źródle i kopiuje go do docelowego magazynu danych. 

Obecnie ten szablon obsługuje tylko kopiowanie danych w formacie **DelimitedText** . Pliki w innych formatach danych można również pobrać ze źródłowego magazynu danych, ale nie można ich skopiować do docelowego magazynu danych.  

Szablon zawiera trzy działania:
- Działanie **Get Metadata** pobiera pliki z Azure Data Lake Storage Gen2 i przekazuje je do kolejnego działania *foreach* .
- Działanie **foreach** pobiera pliki z działania *Get Metadata* i iteruje każdy plik w działaniu *kopiowania* .
- Działanie **kopiowania** znajduje się w działaniu *foreach* , aby skopiować każdy plik ze źródłowego magazynu danych do docelowego magazynu danych.

Szablon definiuje następujące dwa parametry:
- *SourceContainer* to ścieżka do głównego kontenera, w którym dane są kopiowane z Azure Data Lake Storage Gen2. 
- *SourceDirectory* to ścieżka katalogu głównego kontenera, w którym dane są kopiowane z Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Jak używać tego szablonu rozwiązania

1. Przejdź do szablonu **kopiowania zbiorczego z plików do bazy danych** . Utwórz **nowe** połączenie ze źródłowym magazynem Gen2. Należy pamiętać, że "GetMetadataDataset" i "SourceDataset" są odwołaniami do tego samego połączenia z źródłowym magazynem plików.

    ![Utwórz nowe połączenie z magazynem danych źródłowych](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Utwórz **nowe** połączenie z magazynem danych ujścia, do którego kopiowane są dane.

    ![Utwórz nowe połączenie z magazynem danych ujścia](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Wybierz pozycję **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Zobaczysz utworzony potok, jak pokazano w następującym przykładzie:

    ![Przeglądanie potoku](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Jeśli wybierzesz pozycję **Azure Synapse Analytics** jako miejsce docelowe danych w **kroku 2** wymienionym powyżej, musisz wprowadzić połączenie z magazynem obiektów blob platformy Azure na potrzeby przemieszczania, zgodnie z wymaganiami platformy Azure Synapse Analytics Base. Jak pokazano na poniższym zrzucie ekranu, szablon automatycznie wygeneruje *ścieżkę magazynu* dla magazynu obiektów BLOB. Sprawdź, czy kontener został utworzony po uruchomieniu potoku.
        
    ![Ustawienie bazy](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Wybierz pozycję **Debuguj**, wprowadź **Parametry**, a następnie wybierz pozycję **Zakończ**.

    ![Kliknij pozycję * * Debuguj * *](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Po pomyślnym zakończeniu przebiegu potoku zobaczysz wyniki podobne do następującego przykładu:

    ![Przejrzyj wynik](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)