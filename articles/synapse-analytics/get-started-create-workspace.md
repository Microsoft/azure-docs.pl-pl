---
title: 'Szybki start: wprowadzenie — tworzenie obszaru roboczego synapse'
description: Z tego samouczka dowiesz się, jak utworzyć obszar roboczy usługi Synapse, dedykowaną pulę SQL i bez serwera Apache Spark pulę.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: b22954edf4f3a5a935c470326aa43bd24ee2d708
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366066"
---
# <a name="creating-a-synapse-workspace"></a>Tworzenie obszaru roboczego synapse

W tym samouczku dowiesz się, jak utworzyć obszar roboczy usługi Synapse, dedykowaną pulę SQL i bez serwera Apache Spark pulę. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym samouczku, musisz mieć dostęp do grupy zasobów, do której masz przypisaną **rolę** Właściciel. Utwórz obszar roboczy synapse w tej grupie zasobów.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Tworzenie obszaru roboczego synapse w Azure Portal

### <a name="start-the-process"></a>Uruchamianie procesu
1. Otwórz adres [Azure Portal](https://portal.azure.com), na pasku wyszukiwania wprowadź **synapse** bez konieczności wprowadzania klawisza Enter.
1. W wynikach wyszukiwania w obszarze **Usługi** wybierz pozycję **Azure Synapse Analytics**.
1. Wybierz **pozycję Dodaj,** aby utworzyć obszar roboczy.

## <a name="basics-tab--project-details"></a>Karta Podstawowe > Szczegóły projektu
Wypełnij następujące pola:

1. **Subskrypcja** — wybierz dowolną subskrypcję.
1. **Grupa zasobów** — użyj dowolnej grupy zasobów.
1. **Zarządzana grupa zasobów** — pozostaw to pole puste.

## <a name="basics-tab--workspace-details"></a>Karta Podstawowe informacje > szczegóły obszaru roboczego
Wypełnij następujące pola:

1. **Nazwa obszaru roboczego** — wybierz dowolną globalnie unikatową nazwę. W tym samouczku użyjemy przestrzeni **myworkspace.**
1. **Region** — wybierz dowolny region.

W **obszarze Wybierz Data Lake Storage Gen 2:**

1. Według **nazwy konta** wybierz pozycję **Utwórz** nowe i nadaj nowemu kontu magazynu **nazwę contosolake** lub podobną, ponieważ nazwa musi być unikatowa.
1. Według **nazwy systemu plików** wybierz pozycję Utwórz **nowy** i nadaj jej **nazwę użytkownikom**. Spowoduje to utworzenie kontenera magazynu o nazwie **users**. Obszar roboczy będzie używać tego konta magazynu jako "podstawowego" konta magazynu w tabelach platformy Spark i dziennikach aplikacji platformy Spark.
1. Zaznacz pole "Przypisz sobie rolę współautora danych obiektu blob usługi Storage na Data Lake Storage Gen2 magazynu". 

## <a name="completing-the-process"></a>Kończenie procesu
Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Obszar roboczy będzie gotowy w ciągu kilku minut.

> [!NOTE]
> Aby włączyć funkcje obszaru roboczego z istniejącej dedykowanej puli SQL (dawniej SQL DW), zobacz Jak włączyć obszar roboczy dla dedykowanej puli [SQL (dawniej SQL DW).](./sql-data-warehouse/workspace-connected-create.md)


## <a name="open-synapse-studio"></a>Otwórz Synapse Studio

Po utworzeniu Azure Synapse obszar roboczy można otworzyć dwa Synapse Studio sposoby:

* Otwórz obszar roboczy synapse w Azure Portal [,](https://portal.azure.com)w sekcji Przegląd obszaru  roboczego synapse wybierz pozycję Otwórz w Synapse Studio aplikacji. 
* Przejdź do witryny `https://web.azuresynapse.net` i zaloguj się do obszaru roboczego.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Umieść przykładowe dane na podstawowym koncie magazynu
W tym przewodniku z wprowadzeniem użyjemy przykładowego zestawu danych dotyczących taksówek NYX o rozmiarze 100 tys. Zaczynamy od umieszczenia go na podstawowym koncie magazynu utworzonym dla obszaru roboczego.

* Pobierz ten plik na komputer: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* W Synapse Studio przejdź do centrum danych. 
* Wybierz pozycję **Połączone.**
* W kategorii **Azure Data Lake Storae Gen2** zostanie wyświetlony element o nazwie, na przykład **myworkspace (Podstawowa — contosolake).**
* Wybierz kontener o nazwie **users (Primary) (Użytkownicy podstawowi).**
* Wybierz **pozycję** Przekaż i `NYCTripSmall.parquet` wybierz pobrany plik.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie przy użyciu bez serwera puli SQL](get-started-analyze-sql-on-demand.md)
