---
title: Deduplikowanie wierszy i znajdowanie wartości null przy użyciu fragmentów danych przepływu
description: Dowiedz się, jak łatwo dekodować wiersze i znajdować wartości null przy użyciu fragmentów kodu w przepływach danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 1b49b2584c4cb462c7c0f520fe8d1b5bf69c8674
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393670"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>Deduplikowanie wierszy i znajdowanie wartości null przy użyciu fragmentów danych przepływu

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Korzystając z fragmentów kodu w mapowaniu przepływów danych, można łatwo wykonywać typowe zadania, takie jak deduplikacja danych i filtrowanie wartości null. W tym artykule wyjaśniono, jak łatwo dodawać te funkcje do potoków przy użyciu fragmentów skryptów przepływu danych.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Wybierz pozycję **Nowy potok**.

1. Dodaj działanie przepływu danych.

1. Wybierz kartę **Ustawienia źródła** , Dodaj transformację źródłową, a następnie połącz ją z jednym z zestawów danych.

    ![Zrzut ekranu przedstawiający okienko "ustawienia źródła" służące do dodawania typu źródła.](media/data-flow/snippet-adf-2.png)

    Fragmenty kodu deduplikowane i puste używają wzorców ogólnych, które wykorzystują dryfowanie schematu przepływu danych. Fragmenty kodu działają ze wszystkimi schematami z zestawu danych lub zestawami danych, które nie mają wstępnie zdefiniowanego schematu.

1. W sekcji "DISTINCT wiersz using wszystkie kolumny" [skryptu przepływu danych (DFS)](./data-flow-script.md#distinct-row-using-all-columns)skopiuj fragment kodu dotyczący DistinctRows.

1. [Przejdź do strony dokumentacji skryptu przepływu danych i skopiuj fragment kodu dotyczący odrębnych wierszy.](./data-flow-script.md#distinct-row-using-all-columns)

    ![Zrzut ekranu przedstawiający fragment kodu źródła.](media/data-flow/snippet-adf-3.png)

1. W skrypcie, po definicji `source1` , naciśnij klawisz ENTER, a następnie wklej fragment kodu.

1. Wykonaj jedną z następujących czynności:

   * Połącz ten wklejony fragment kodu do przekształcenia źródłowego utworzonego wcześniej na grafie, wpisując **source1** przed wklejanym kodem.

   * Alternatywnie można połączyć nową transformację w projektancie, wybierając strumień przychodzący z nowego węzła przekształcenia w grafie.

     ![Zrzut ekranu przedstawiający okienko "ustawienia podziału warunkowego".](media/data-flow/snippet-adf-4.png)

   Teraz przepływ danych spowoduje usunięcie zduplikowanych wierszy ze źródła przy użyciu transformacji agregacji, która grupuje wszystkie wiersze przy użyciu ogólnego skrótu dla wszystkich wartości kolumn.
    
1. Dodaj fragment kodu dotyczący dzielenia danych na jeden strumień zawierający wiersze z wartościami null i inny strumień bez wartości null. W tym celu:

1. [Wróć do biblioteki fragmentów i ten czas kopiuje kod dla testów o wartości NULL.](./data-flow-script.md#check-for-nulls-in-all-columns)

   b. W Projektancie przepływu danych wybierz pozycję **skrypt** ponownie, a następnie wklej nowy kod przekształcenia u dołu. Ta akcja łączy skrypt z poprzednią transformację przez umieszczenie nazwy tego przekształcenia przed wklejonym fragmentem kodu.

   Wykres przepływu danych powinien teraz wyglądać podobnie do tego:

    ![Zrzut ekranu przedstawiający wykres przepływu danych.](media/data-flow/snippet-adf-1.png)

  Utworzono przepływ danych roboczych z ogólnymi sprawdzeniami deduping i null, pobierając istniejące fragmenty kodu z biblioteki skryptów przepływu danych i dodając je do istniejącego projektu.

## <a name="next-steps"></a>Następne kroki

* Utwórz resztę logiki przepływu danych, korzystając z mapowania [przekształceń](concepts-data-flow-overview.md)przepływów danych.