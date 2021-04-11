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
ms.openlocfilehash: fb30913d71df46f4c0afedd475d40205c1429258
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122207"
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
1. **Grupa zasobów** — pozostaw to pole puste.


## <a name="basics-tab--workspace-details"></a>Karta podstawy > szczegóły obszaru roboczego
Wypełnij następujące pola:

1. **Nazwa obszaru roboczego** — wybierz dowolną globalnie unikatową nazwę. W tym samouczku użyjemy **obszaru roboczego**.
1. **Region** — wybierz dowolny region.
1. **Wybieranie Data Lake Storage Gen 2**
1. Kliknij przycisk **z subskrypcji**.
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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie przy użyciu bezserwerowej puli SQL](get-started-analyze-sql-on-demand.md)
