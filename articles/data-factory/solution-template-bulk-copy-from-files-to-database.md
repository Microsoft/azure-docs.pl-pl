---
title: Kopiowanie zbiorcze z plików do bazy danych
description: Dowiedz się, jak używać szablonu rozwiązania do zbiorczego kopiowania danych z usługi Azure Data Lake Storage Gen2 do usługi Azure Synapse Analytics / Azure SQL Database.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 070b708f204006bc1ba90c4c3676696291fde902
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414868"
---
# <a name="bulk-copy-from-files-to-database"></a>Kopiowanie zbiorcze z plików do bazy danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano szablon rozwiązania, którego można użyć do zbiorczego kopiowania danych z usługi Azure Data Lake Storage Gen2 do usługi Azure Synapse Analytics / Azure SQL Database.

## <a name="about-this-solution-template"></a>Informacje o tym szablonie rozwiązania

Ten szablon pobiera pliki ze źródła usługi Azure Data Lake Storage Gen2. Następnie iteruje nad każdym plikiem w źródle i kopiuje plik do docelowego magazynu danych. 

Obecnie ten szablon obsługuje tylko kopiowanie danych w formacie **DelimitedText.** Pliki w innych formatach danych można również pobrać ze źródłowego magazynu danych, ale nie można ich skopiować do docelowego magazynu danych.  

Szablon zawiera trzy działania:
- Pobierz działanie **metadanych** pobiera pliki z usługi Azure Data Lake Storage Gen2 i przekazuje je do kolejnych *forEach* działania.
- **ForEach** działania pobiera pliki z *Get Metadata* działania i iteruje każdy plik do *copy* działania.
- **Działanie kopiowania** znajduje się w *forEach* działania, aby skopiować każdy plik z magazynu danych źródłowych do magazynu danych docelowych.

Szablon definiuje następujące dwa parametry:
- *SourceContainer* jest ścieżką kontenera głównego, z którego dane są kopiowane w usłudze Azure Data Lake Storage Gen2. 
- *SourceDirectory* to ścieżka katalogu w kontenerze głównym, z którego dane są kopiowane w usłudze Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Jak korzystać z tego szablonu rozwiązania

1. Przejdź do szablonu **Kopiowanie zbiorcze z plików do bazy danych.** Utwórz **nowe** połączenie ze źródłowym magazynem Gen2. Należy pamiętać, że "GetMetadataDataset" i "SourceDataset" są odwołania do tego samego połączenia magazynu plików źródłowych.

    ![Tworzenie nowego połączenia ze źródłowym magazynem danych](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Utwórz **nowe** połączenie z magazynem danych ujścia, do którego kopiujesz dane.

    ![Tworzenie nowego połączenia z magazynem danych ujścia](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Wybierz pozycję **Użyj tego szablonu**.

    ![Użyj tego szablonu](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Zostanie wyświetlony potok utworzony w sposób pokazany w poniższym przykładzie:

    ![Przejrzyj potok](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Jeśli wybrano **usługę Azure Synapse Analytics (dawniej SQL DW)** jako miejsce docelowe danych w **kroku 2** wymienionych powyżej, należy wprowadzić połączenie z magazynem obiektów Blob platformy Azure w celu przeprowadzenia przemieszczania, zgodnie z wymaganiami bazy danych SQL Data Warehouse. Jak pokazano na poniższym zrzucie ekranu, szablon automatycznie wygeneruje *ścieżkę magazynu* dla magazynu obiektów blob. Sprawdź, czy kontener został utworzony po uruchomieniu potoku.
        
    ![Ustawienie bazy polibałowej](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Wybierz **debugowanie**, wprowadź **parametry**, a następnie wybierz **zakończ**.

    ![Kliknij przycisk **Debugowanie**](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Po pomyślnym zakończeniu wykonywania uruchomienia potoku zostaną wyświetlne wyniki podobne do następującego przykładu:

    ![Przejrzyj wynik](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)