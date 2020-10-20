---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026734"
---
## <a name="transform-data-before-using-large-usage-files"></a>Przekształcanie danych przed użyciem dużych plików użycia

Czasami plik użycia lub plik uzgodnień jest zbyt duży, aby można go było otworzyć. Może też wystąpić sytuacja, że do rozwiązania problemu jest wymagana tylko część informacji. Na przykład mogą być potrzebne tylko informacje o konkretnym zasobie lub o zużyciu kilku usług lub grup zasobów. W takich przypadkach można przekształcić dane, aby je podsumować przed utworzeniem tabel przestawnych.

1. Otwórz pusty skoroszyt w programie Excel.
1. W górnym menu wybierz pozycję **Dane** > **Z pliku tekstowego lub CSV**, wybierz plik użycia, a następnie wybierz pozycję **Importuj**.
1. W dolnej części okna wybierz pozycję **Przekształć dane**. W nowym oknie zostanie wyświetlone podsumowanie danych.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="Przykładowe podsumowane dane" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. Jeśli masz Umowę z Klientem Microsoft, pomiń ten krok i przejdź do następnej sekcji, ponieważ pliki użycia MCA zazwyczaj mają tytuły kolumn w pierwszym wierszu. Przygotuj dane, tworząc tabelę. Usuń początkowe wiersze, pozostawiając tylko tytuły. Wybierz pozycję **Usuń wiersze** > **Usuń pierwsze wiersze**.  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="Przykładowe podsumowane dane" :::
1. W oknie Usuwanie pierwszych wierszy wprowadź liczbę wierszy do usunięcia z górnej części. W przypadku umowy EA zwykle należy wprowadzić liczbę 2, a w przypadku dostawcy CSP liczbę 1. Wybierz przycisk **OK**.
1. Wybierz pozycję **Użyj pierwszego wiersza jako nagłówków**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="Przykładowe podsumowane dane" :::
    
    W górnej części widoku tabeli widać tytuły kolumn.
1. Następnie dodaj filtr. Użyj strzałek selektora znajdujących się po prawej stronie tytułu każdej kolumny, aby zastosować filtrowanie. Sugerowane filtry to Subscription ID, Service Name (Meter category), Instance ID, resource group. W tym samym dokumencie można użyć wielu filtrów. Zalecamy zastosowanie wszystkich możliwych filtrów, aby zmniejszyć rozmiar dokumentu i ułatwić późniejszą pracę.
1. Po zastosowaniu filtrów wybierz pozycję **Zamknij i załaduj**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="Przykładowe podsumowane dane" :::

Plik zostanie załadowany i zostanie wyświetlona tabela z przefiltrowanymi danymi użycia. Teraz możesz utworzyć nową tabelę przestawną na potrzeby rozwiązywania problemów z użyciem.