---
title: 'Szybki Start: wprowadzenie — Tworzenie obszaru roboczego Synapse'
description: W tym samouczku dowiesz się, jak utworzyć obszar roboczy Synapse, dedykowaną pulę SQL i bezserwerową pulę Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: f186acbe030dcbb0c2bad22586a8b2a5d1aa520d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259799"
---
# <a name="creating-a-synapse-workspace"></a>Tworzenie obszaru roboczego Synapse

W tym samouczku dowiesz się, jak utworzyć obszar roboczy Synapse, dedykowaną pulę SQL i bezserwerową pulę Apache Spark. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki tego samouczka, musisz mieć dostęp do grupy zasobów, do której przypisano rolę **właściciela** . Utwórz obszar roboczy Synapse w tej grupie zasobów.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Utwórz obszar roboczy Synapse w Azure Portal

### <a name="start-the-process"></a>Rozpocznij proces
1. Otwórz [Azure Portal](https://portal.azure.com), na pasku wyszukiwania wprowadź **Synapse** bez naciśnięcia klawisza ENTER.
1. W wynikach wyszukiwania w obszarze **usługi** wybierz pozycję **Azure Synapse Analytics**.
1. Wybierz pozycję **Dodaj** , aby utworzyć obszar roboczy.

## <a name="basics-tab--project-details"></a>Karta podstawy > szczegóły projektu
Wypełnij następujące pola:

1. **Subskrypcja** — wybierz dowolną subskrypcję.
1. **Grupa zasobów** — Użyj dowolnej grupy zasobów.
1. **Zarządzana Grupa zasobów** — pozostaw to pole puste.

## <a name="basics-tab--workspace-details"></a>Karta podstawy > szczegóły obszaru roboczego
Wypełnij następujące pola:

1. **Nazwa obszaru roboczego** — wybierz dowolną globalnie unikatową nazwę. W tym samouczku użyjemy **obszaru roboczego**.
1. **Region** — wybierz dowolny region.

W obszarze **wybierz Data Lake Storage Gen 2**:

1. Według **nazwy konta**, kliknij przycisk **Utwórz nowe** i Nazwij nowe konto magazynu **contosolake** lub podobną, ponieważ ta nazwa musi być unikatowa.
1. Według **nazwy systemu plików**, kliknij przycisk **Utwórz nowe** i nadaj nazwę **użytkownikom** IT. Spowoduje to utworzenie kontenera magazynu o nazwie **Użytkownicy**. Obszar roboczy będzie używał tego konta magazynu jako konta magazynu "podstawowe" do tabel platformy Spark i dzienników aplikacji platformy Spark.
1. Zaznacz pole wyboru "Przypisz samodzielnie rolę współautor danych obiektów blob magazynu" na koncie Data Lake Storage Gen2. 

## <a name="completing-the-process"></a>Ukończenie procesu
Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Obszar roboczy jest gotowy w ciągu kilku minut.

> [!NOTE]
> Aby włączyć funkcje obszaru roboczego z istniejącej dedykowanej puli SQL (dawniej SQL DW), zapoznaj się z [tematem Włączanie obszaru roboczego dla dedykowanej puli SQL (dawniej SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Otwórz Synapse Studio

Po utworzeniu obszaru roboczego usługi Azure Synapse dostępne są dwa sposoby otwierania programu Synapse Studio:

* Otwórz obszar roboczy Synapse w [Azure Portal](https://portal.azure.com)w sekcji **Omówienie** obszaru roboczego Synapse wybierz pozycję **Otwórz** w polu Otwórz Synapse Studio.
* Przejdź do `https://web.azuresynapse.net` obszaru roboczego i zaloguj się do niego.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Umieść przykładowe dane na podstawowym koncie magazynu
Zamierzamy użyć małego zestawu danych przykładowego wiersza 100 000 z danymi z kabiny NYX, aby zapoznać się z wieloma przykładami w tym przewodniku wprowadzającym. Zaczynamy od umieszczenia jej w podstawowym koncie magazynu utworzonym dla obszaru roboczego.

* Pobierz ten plik na komputer: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* W Synapse Studiio przejdź do centrum danych. 
* Kliknij pozycję **połączone**.
* W kategorii **Azure Data Lake Storae Gen2** zobaczysz element z nazwą, taką jak mój **obszar roboczy (Primary-contosolake)**
* Kliknij kontener o nazwie **Users (podstawowa)**
* Kliknij przycisk **Przekaż** i wybierz `NYCTripSmall.parquet` pobrany plik

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie przy użyciu bezserwerowej puli SQL](get-started-analyze-sql-on-demand.md)
