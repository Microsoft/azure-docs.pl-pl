---
title: 'Samouczek: wprowadzenie do tworzenia obszaru roboczego Synapse'
description: W tym samouczku dowiesz się, jak utworzyć obszar roboczy Synapse, pulę SQL i pulę Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: d3a5f2bd4bf536c1bc5b3723b9b612beef6a647c
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812322"
---
# <a name="creating-a-synapse-workspace"></a>Tworzenie obszaru roboczego Synapse

W tym samouczku dowiesz się, jak utworzyć obszar roboczy Synapse, pulę SQL i pulę Apache Spark. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać wszystkie kroki tego samouczka, musisz mieć dostęp do grupy zasobów, do której przypisano rolę **właściciela** . Utwórz obszar roboczy Synapse w tej grupie zasobów.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Tworzenie obszaru roboczego usługi Synapse w witrynie Azure Portal

1. Otwórz [Azure Portal](https://portal.azure.com)i w górnej części Szukaj **Synapse**.
1. W wynikach wyszukiwania w obszarze **usługi**wybierz pozycję **Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)**.
1. Wybierz pozycję **Dodaj** , aby utworzyć obszar roboczy.
1. W obszarze **podstawowe**wprowadź wymagane pola i wybierz nazwę obszaru roboczego. W tym samouczku użyjemy **obszaru roboczego**.
1. Do utworzenia obszaru roboczego jest potrzebne konto ADLSGEN2. Najprostszy wybór, aby utworzyć nowy. Jeśli chcesz ponownie użyć istniejącego, musisz wykonać dodatkową konfigurację. 
1. Opcja 1 — Tworzenie nowego konta ADLSGEN2 
    1. Przejdź do **opcji wybierz Data Lake Storage Gen 2**. 
    1. Kliknij pozycję **Utwórz nową** i nadaj jej nazwę **contosolake**.
    1. Kliknij pozycję **system plików** i nadaj jej nazwę **Użytkownicy**. Spowoduje to utworzenie kontenera o nazwie **Użytkownicy**
1. Opcja 2 przy użyciu istniejącego konta ADLSGEN2. Zapoznaj się z instrukcjami dotyczącymi **przygotowywania konta magazynu ADLSGEN2** w dolnej części tego dokumentu.
1. Obszar roboczy usługi Azure Synapse będzie używać tego konta magazynu jako konta magazynu "podstawowe" i kontenera do przechowywania danych obszaru roboczego. Obszar roboczy przechowuje dane w tabelach Apache Spark. Przechowuje dzienniki aplikacji platformy Spark w folderze o nazwie **/Synapse/WorkspaceName**.
1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Obszar roboczy jest gotowy w ciągu kilku minut.

## <a name="open-synapse-studio"></a>Otwórz Synapse Studio

Po utworzeniu obszaru roboczego usługi Azure Synapse dostępne są dwa sposoby otwierania programu Synapse Studio:

* Otwórz obszar roboczy Synapse w [Azure Portal](https://portal.azure.com). W górnej części sekcji **Przegląd** wybierz pozycję Uruchom program **Synapse Studio**.
* Przejdź do `https://web.azuresynapse.net` obszaru roboczego i zaloguj się do niego.

## <a name="create-a-sql-pool"></a>Tworzenie puli SQL

1. W programie Synapse Studio w okienku po lewej stronie wybierz pozycję **Zarządzaj**  >  **pulami SQL**.
1. Wybierz pozycję **nowe** i wprowadź następujące ustawienia:

    |Ustawienie | Sugerowana wartość | 
    |---|---|---|
    |**Nazwa puli SQL**| **SQLDB1**|
    |**Poziom wydajności**|**DW100C**|
    |||

1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Pula SQL będzie gotowa w ciągu kilku minut. Pula SQL jest skojarzona z bazą danych puli SQL o nazwie **SQLDB1**.

Pula SQL zużywa zasoby do rozliczenia, o ile jest ona aktywna. Pulę można wstrzymać później, aby zmniejszyć koszty.

## <a name="create-an-apache-spark-pool"></a>Tworzenie puli Apache Spark

1. W programie Synapse Studio w okienku po lewej stronie wybierz pozycję **Zarządzaj**  >  **pulami Apache Spark**.
1. Wybierz pozycję **nowe** i wprowadź następujące ustawienia:

    |Ustawienie | Sugerowana wartość | 
    |---|---|---|
    |**Nazwa puli Apache Spark**|**Spark1**
    |**Rozmiar węzła**| **Mała**|
    |**Liczba węzłów**| Ustaw wartość minimalną na 3 i wartość maksymalną na 3.|

1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Pula Apache Spark będzie gotowa w ciągu kilku sekund.

> [!NOTE]
> Oprócz nazwy Pula Apache Spark nie jest taka sama jak Pula SQL. Jest to tylko kilka podstawowych metadanych, których można użyć do poinformowania obszaru roboczego usługi Azure Synapse, jak korzystać z platformy Spark.

Ponieważ są to metadane, nie można uruchomić ani zatrzymać pul platformy Spark.

Podczas wykonywania działania platformy Spark w usłudze Azure Synapse należy określić pulę platformy Spark do użycia. Pula informuje platformę Azure Synapse, ile zasobów platformy Spark ma używać. Płacisz tylko za wykorzystane zasoby. Gdy aktywne zaprzestanie korzystania z puli, zasoby są automatycznie przekroczenia limitu czasu i są odtwarzane.

> [!NOTE]
> Bazy danych Spark są tworzone niezależnie od pul platformy Spark. Obszar roboczy zawsze ma bazę danych Spark o nazwie **default**. Można utworzyć dodatkowe bazy danych platformy Spark.

## <a name="the-sql-on-demand-pool"></a>Pula na żądanie SQL

Każdy obszar roboczy jest dostarczany ze wstępnie utworzoną pulą o nazwie **SQL na żądanie**. Nie można usunąć tej puli. Pula na żądanie SQL umożliwia korzystanie z programu SQL bez konieczności tworzenia lub oceniania zarządzania pulą SQL w usłudze Azure Synapse.

W przeciwieństwie do innych rodzajów pul, rozliczenia dla SQL na żądanie bazują na ilości danych skanowanych w celu uruchomienia zapytania, a nie liczby zasobów używanych do wykonywania zapytania.

* SQL na żądanie ma własne bazy danych SQL na żądanie, które nie są dostępne niezależnie od puli na żądanie SQL.
* Obszar roboczy zawsze ma dokładnie jedną pulę na żądanie SQL o nazwie **SQL na żądanie**.

## <a name="preparing-a-adlsgen2-storage-account"></a>Przygotowywanie konta magazynu ADLSGEN2

### <a name="perform-the-following-steps-before-you-create-your-workspace"></a>PRZED utworzeniem obszaru roboczego wykonaj następujące czynności:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Przejdź do istniejącego konta magazynu
1. Wybierz pozycję **Kontrola dostępu (IAM)** w okienku po lewej stronie. 
1. Przypisz następujące role lub upewnij się, że są już przypisane:
    * Przypisz siebie do roli **właściciela** .
    * Przypisz siebie do roli **właściciela danych obiektu blob magazynu** .
1. W okienku po lewej stronie wybierz pozycję **kontenery** i Utwórz kontener.
1. Możesz nadać kontenerowi nazwę. W tym dokumencie używamy nazwy  **użytkowników**.
1. Zaakceptuj domyślne ustawienie **poziomu dostępu publicznego**, a następnie wybierz pozycję **Utwórz**.

### <a name="perform-the-following-steps-after-you-create-your-workspace"></a>Po utworzeniu obszaru roboczego wykonaj następujące czynności:

Skonfiguruj dostęp do konta magazynu z obszaru roboczego. Zarządzane tożsamości dla obszaru roboczego usługi Azure Synapse prawdopodobnie mają już dostęp do konta magazynu. Wykonaj następujące kroki, aby upewnić się, że:

1. Otwórz [Azure Portal](https://portal.azure.com) i konto magazynu podstawowego wybrane dla Twojego obszaru roboczego.
1. Wybierz pozycję **Kontrola dostępu (IAM)** w okienku po lewej stronie.
1. Przypisz poniższe role lub upewnij się, że są już przypisane. Używamy tej samej nazwy dla tożsamości obszaru roboczego i nazwy obszaru roboczego.
    * W przypadku roli **współautor danych obiektów blob magazynu** na koncie magazynu należy przypisać obszar **roboczy** jako tożsamość obszaru roboczego.
    * Przypisz **jako nazwę obszaru roboczego.**
1. Wybierz pozycję **Zapisz**.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie przy użyciu puli SQL](get-started-analyze-sql-pool.md)
