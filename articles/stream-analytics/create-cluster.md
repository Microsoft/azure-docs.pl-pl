---
title: Tworzenie klastra Azure Stream Analytics — Szybki Start
description: Dowiedz się, jak utworzyć klaster Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: a7be204c30d242be991fb9a57d239b69342ace97
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947474"
---
# <a name="quickstart-create-a-dedicated-azure-stream-analytics-cluster-using-azure-portal"></a>Szybki Start: tworzenie dedykowanego klastra Azure Stream Analytics przy użyciu Azure Portal

Użyj Azure Portal, aby utworzyć klaster Azure Stream Analytics. [Klaster Stream Analytics](cluster-overview.md) to wdrożenie z jedną dzierżawą, które może być używane na potrzeby złożonych i wymagających przypadków użycia przesyłania strumieniowego. W klastrze Stream Analytics można uruchamiać wiele zadań Stream Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Kończenie [szybkiego startu: Tworzenie zadania Stream Analytics przy użyciu Azure Portal](stream-analytics-quick-create-portal.md).

## <a name="create-a-stream-analytics-cluster"></a>Tworzenie klastra Stream Analytics

W tej sekcji utworzysz zasób klastra Stream Analytics.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób**. W polu Wyszukaj w *portalu Marketplace* wpisz i wybierz pozycję **klaster Stream Analytics**. Następnie wybierz pozycję **Dodaj**.

   :::image type="content" source="./media/create-cluster/search-result.png" alt-text="Stream Analytics wynik wyszukiwania w klastrze.":::

1. Na stronie **Tworzenie klastra Stream Analytics** wprowadź podstawowe ustawienia dla nowego klastra.

   |Ustawienie|Wartość|Opis |
   |---|---|---|
   |Subskrypcja|Nazwa subskrypcji|Wybierz subskrypcję platformy Azure, która ma być używana dla tego klastra Stream Analytics. |
   |Grupa zasobów|Nazwa grupy zasobów|Wybierz grupę zasobów lub wybierz pozycję **Utwórz nową**, a następnie wprowadź unikatową nazwę nowej grupy zasobów. |
   |Nazwa klastra|Unikatowa nazwa|Wprowadź nazwę identyfikującą klaster Stream Analytics.|
   |Lokalizacja|Region znajdujący się najbliżej źródeł danych i ujścia|Wybierz lokalizację geograficzną, aby hostować klaster Stream Analytics. Użyj lokalizacji znajdującej się najbliżej źródeł danych i ujściach w celu analizy małych opóźnień.|
   |Pojemność jednostki przesyłania strumieniowego|36 do 216 |Określ rozmiar klastra, szacując, ile zadania Stream Analytics planowane do uruchomienia, i łącznej liczby wymaganych zadań programu SUs. Możesz zacząć od 36 usług SUs i później skalować w górę lub w dół odpowiednio do potrzeb.|

   ![Tworzenie klastra](./media/create-cluster/create-cluster.png)

1. Wybierz pozycję **Przeglądanie + tworzenie**. Możesz pominąć sekcje **Tagi** .

1. Przejrzyj ustawienia klastra, a następnie wybierz pozycję **Utwórz**. Tworzenie klastra to długotrwała operacja, której ukończenie może potrwać około 60 minut. Poczekaj na wyświetlenie komunikatu **Wdrożenie zostało ukończone** na stronie portalu. W międzyczasie można tworzyć i opracowywać [zadania Stream Analytics](stream-analytics-quick-create-portal.md#create-a-stream-analytics-job) , które mają być uruchamiane w tym klastrze, jeśli nie zostały jeszcze.

1. Wybierz pozycję **Przejdź do zasobu** , aby przejść do strony klastra Stream Analytics.

## <a name="delete-your-cluster"></a>Usuwanie klastra

Możesz usunąć klaster Stream Analytics, jeśli nie planujesz uruchamiać żadnych Stream Analyticsych zadań. Usuń klaster, wykonując następujące czynności na Azure Portal:

1. Przejdź do obszaru **Stream Analytics zadania** w obszarze **Ustawienia** i Zatrzymaj wszystkie zadania, które są uruchomione.

1. Przejdź do **omówienia** klastra. Wybierz pozycję **Usuń** i postępuj zgodnie z instrukcjami, aby usunąć klaster.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia klastra Azure Stream Analytics. Przejdź do następnego artykułu, aby dowiedzieć się, jak uruchomić zadanie Stream Analytics w klastrze:

> [!div class="nextstepaction"]
> [Zarządzanie zadaniami Stream Analytics w klastrze Stream Analytics](manage-jobs-cluster.md)
