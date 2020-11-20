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
ms.date: 10/07/2020
ms.openlocfilehash: c15750a7a63293994ff7c6b68cb85a5066acee90
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981140"
---
# <a name="creating-a-synapse-workspace"></a>Tworzenie obszaru roboczego Synapse

W tym samouczku dowiesz się, jak utworzyć obszar roboczy Synapse, dedykowaną pulę SQL i bezserwerową pulę Apache Spark. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki tego samouczka, musisz mieć dostęp do grupy zasobów, do której przypisano rolę **właściciela** . Utwórz obszar roboczy Synapse w tej grupie zasobów.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Utwórz obszar roboczy Synapse w Azure Portal

1. Otwórz [Azure Portal](https://portal.azure.com)i w górnej części Szukaj **Synapse**.
1. W wynikach wyszukiwania w obszarze **usługi** wybierz pozycję **Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)**.
1. Wybierz pozycję **Dodaj** , aby utworzyć obszar roboczy.
1. W obszarze **podstawowe** wpisz swoją preferowaną **subskrypcję**, **grupę zasobów**, **region**, a następnie wybierz nazwę obszaru roboczego. W tym samouczku użyjemy **obszaru roboczego**.
1. Przejdź do **opcji wybierz Data Lake Storage Gen 2**. 
1. Kliknij pozycję **Utwórz nową** i nadaj jej nazwę **contosolake**.
1. Kliknij pozycję **system plików** i nadaj jej nazwę **Użytkownicy**. Spowoduje to utworzenie kontenera o nazwie **Użytkownicy**
1. Obszar roboczy będzie używał tego konta magazynu jako konta magazynu "podstawowe" do tabel platformy Spark i dzienników aplikacji platformy Spark.
1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Obszar roboczy jest gotowy w ciągu kilku minut.

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

## <a name="create-a-serverless-apache-spark-pool"></a>Utwórz bezserwerową pulę Apache Spark

1. W programie Synapse Studio w okienku po lewej stronie wybierz pozycję **Zarządzaj**  >  **pulami Apache Spark**.
1. Wybierz pozycję **Nowy** 
1. W obszarze **Nazwa puli Apache Spark** wprowadź **Spark1**.
1. Dla **rozmiaru węzła** wprowadź **małe**.
1. Dla **liczby węzłów** ustaw wartość minimalną na 3 i wartość maksymalną na 3.
1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Pula Apache Spark będzie gotowa w ciągu kilku sekund.

Pula platformy Spark informuje platformę Azure Synapse, ile zasobów platformy Spark ma używać. Płacisz tylko za wykorzystane zasoby. Gdy aktywne zaprzestanie korzystania z puli, zasoby są automatycznie przekroczenia limitu czasu i są odtwarzane.

## <a name="the-built-in-serverless-sql-pool"></a>Wbudowana Pula SQL bezserwerowa

Każdy obszar roboczy jest dostarczany ze wstępnie utworzoną pulą o nazwie **wbudowane**. Nie można usunąć tej puli. Bezserwerowa Pula SQL umożliwia korzystanie z programu SQL bez konieczności tworzenia lub oceniania zarządzania bezserwerową pulą SQL na platformie Azure Synapse. W przeciwieństwie do dedykowanych pul SQL, rozliczanie dla bezserwerowej puli SQL jest zależne od ilości danych przeskanowanych w celu uruchomienia zapytania, a nie liczby zasobów używanych do wykonywania zapytania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie przy użyciu dedykowanej puli SQL](get-started-analyze-sql-pool.md)
