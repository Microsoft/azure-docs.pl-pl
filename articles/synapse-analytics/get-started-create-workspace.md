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
ms.date: 11/21/2020
ms.openlocfilehash: 949992418cb19c5c69ed8da92e9c2f0fc5f2f791
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445425"
---
# <a name="creating-a-synapse-workspace"></a>Tworzenie obszaru roboczego Synapse

W tym samouczku dowiesz się, jak utworzyć obszar roboczy Synapse, dedykowaną pulę SQL i bezserwerową pulę Apache Spark. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki tego samouczka, musisz mieć dostęp do grupy zasobów, do której przypisano rolę **właściciela** . Utwórz obszar roboczy Synapse w tej grupie zasobów.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Utwórz obszar roboczy Synapse w Azure Portal

1. Otwórz [Azure Portal](https://portal.azure.com)i w górnej części Szukaj **Synapse**.
1. W wynikach wyszukiwania w obszarze **usługi** wybierz pozycję **Azure Synapse Analytics**.
1. Wybierz pozycję **Dodaj** , aby utworzyć obszar roboczy.
1. W obszarze **podstawowe** wpisz swoją preferowaną **subskrypcję**, **grupę zasobów**, **region**, a następnie wybierz nazwę obszaru roboczego. W tym samouczku użyjemy **obszaru roboczego**.
1. Przejdź do **opcji wybierz Data Lake Storage Gen 2**. 
1. Kliknij pozycję **Utwórz nową** i nadaj jej nazwę **contosolake**.
1. Kliknij pozycję **system plików** i nadaj jej nazwę **Użytkownicy**. Spowoduje to utworzenie kontenera o nazwie **Użytkownicy**
1. Obszar roboczy będzie używał tego konta magazynu jako konta magazynu "podstawowe" do tabel platformy Spark i dzienników aplikacji platformy Spark.
1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Obszar roboczy jest gotowy w ciągu kilku minut.

> [!NOTE]
> Aby włączyć funkcje obszaru roboczego z istniejącej dedykowanej puli SQL (dawniej SQL DW), zapoznaj się z [tematem Włączanie obszaru roboczego dla dedykowanej puli SQL (dawniej SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Otwórz Synapse Studio

Po utworzeniu obszaru roboczego usługi Azure Synapse dostępne są dwa sposoby otwierania programu Synapse Studio:

* Otwórz obszar roboczy Synapse w [Azure Portal](https://portal.azure.com). W górnej części sekcji **Przegląd** wybierz pozycję Uruchom program **Synapse Studio**.
* Przejdź do `https://web.azuresynapse.net` obszaru roboczego i zaloguj się do niego.

## <a name="create-a-dedicated-sql-pool"></a>Tworzenie dedykowanej puli SQL

1. W programie Synapse Studio w okienku po lewej stronie wybierz pozycję **Zarządzaj**  >  **pulami SQL**.
1. Wybierz pozycję **Nowy**
1. W obszarze **Nazwa puli SQL** wybierz pozycję **SQLPOOL1**
1. Dla opcji **poziom wydajności** wybierz **DW100C**
1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Dedykowana Pula SQL będzie gotowa w ciągu kilku minut. Dedykowana Pula SQL jest skojarzona z dedykowaną bazą danych puli SQL o nazwie **SQLPOOL1**.

Dedykowana Pula SQL zużywa zasoby do rozliczenia, o ile jest ona aktywna. Pulę można wstrzymać później, aby zmniejszyć koszty.

> [!NOTE] 
> Podczas tworzenia nowej dedykowanej puli SQL (dawniej SQL DW) w obszarze roboczym zostanie otwarta strona dedykowana obsługa administracyjna puli SQL. Inicjowanie obsługi administracyjnej odbywa się na logicznym serwerze SQL Server.

## <a name="create-a-serverless-apache-spark-pool"></a>Utwórz bezserwerową pulę Apache Spark

1. W programie Synapse Studio w okienku po lewej stronie wybierz pozycję **Zarządzaj**  >  **pulami Apache Spark**.
1. Wybierz pozycję **Nowy** 
1. W obszarze **Nazwa puli Apache Spark** wprowadź **Spark1**.
1. Dla **rozmiaru węzła** wprowadź **małe**.
1. Dla **liczby węzłów** ustaw wartość minimalną na 3 i wartość maksymalną na 3.
1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Pula Apache Spark będzie gotowa w ciągu kilku sekund.

Pula platformy Spark informuje platformę Azure Synapse, ile zasobów platformy Spark ma używać. Płacisz tylko za wykorzystane zasoby. Gdy aktywne zaprzestanie korzystania z puli, zasoby są automatycznie przekroczenia limitu czasu i są odtwarzane.

## <a name="the-built-in-serverless-sql-pool"></a>Wbudowana Pula SQL bezserwerowa

Każdy obszar roboczy zawiera wstępnie zbudowaną pulę SQL bezserwerową o nazwie **wbudowane**. Nie można usunąć tej puli. Pule SQL bezserwerowe umożliwiają korzystanie z języka SQL bez konieczności rezerwowania pojemności z dedykowanymi pulami SQL. W przeciwieństwie do dedykowanych pul SQL, rozliczanie dla bezserwerowej puli SQL jest oparte na ilości danych przeskanowanych w celu uruchomienia zapytania, a nie liczby pojemności przydzielonej do puli.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie przy użyciu dedykowanej puli SQL](get-started-analyze-sql-pool.md)
