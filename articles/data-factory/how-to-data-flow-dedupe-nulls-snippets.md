---
title: Deduplikowanie wierszy i znajdowanie wartości null przy użyciu fragmentów danych przepływu
description: Dowiedz się, jak łatwo dekodować wiersze i znajdować wartości null przy użyciu fragmentów kodu w przepływach danych
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 841484a647d2737d621c75ebe63f65f2de829a26
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666504"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>Deduplikowanie wierszy i znajdowanie wartości null przy użyciu fragmentów danych przepływu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Za pomocą fragmentów kodu w mapowaniu przepływów danych, można bardzo łatwo wykonywać typowe zadania, takie jak deduplikacja danych i filtrowanie wartości null. W tym przewodniku zawarto wyjaśnienie, jak dodać te funkcje do potoków bardzo łatwo przy użyciu fragmentów skryptów przepływu danych.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Wybierz pozycję **+ Nowy potok** , aby utworzyć nowy potok.

2. Dodaj działanie przepływu danych.

3. Dodaj transformację źródłową i połącz ją z jednym z zestawów danych

    ![Fragment kodu źródłowego 2](media/data-flow/snippet-adf-2.png)

4. Fragmenty kodu deduplikowane i puste używają wzorców ogólnych wykorzystujących dryfowanie schematu przepływu danych, dzięki czemu będą współpracować z dowolnym schematem z zestawu danych lub zestawami danych, które nie mają żadnego wstępnie zdefiniowanego schematu.

5. [Przejdź do strony dokumentacji skryptu przepływu danych i skopiuj fragment kodu dotyczący odrębnych wierszy.](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. W interfejsie użytkownika projektanta przepływu danych kliknij przycisk skryptu w prawym górnym rogu, aby otworzyć Edytor skryptów za wykresem przepływu danych.

    ![Fragment kodu źródłowego 3](media/data-flow/snippet-adf-3.png)

7. Po definicji dla ```source1``` skryptu naciśnij klawisz ENTER, a następnie wklej w fragmencie kodu.

8. Ten wklejony fragment kodu zostanie połączony z poprzednią transformację źródłową utworzoną w grafie, wpisując ciąg "source1" przed wklejanym kodem.

9. Alternatywnie można połączyć nową transformację w projektancie, wybierając strumień przychodzący z nowego węzła przekształcenia w grafie.

    ![Fragment kodu źródłowego 4](media/data-flow/snippet-adf-4.png)

10. Teraz przepływ danych spowoduje usunięcie zduplikowanych wierszy ze źródła przy użyciu transformacji agregacji, która grupuje wszystkie wiersze przy użyciu ogólnego skrótu dla wszystkich wartości kolumn.
    
11. Następnie dodamy fragment kodu dotyczący dzielenia danych na strumień zawierający wiersze z wartościami NULL i strumień, który nie ma żadnych wartości NULL.

12. [Wróć do biblioteki fragmentów i ten czas kopiuje kod dla testów o wartości NULL.](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. W Projektancie przepływu danych ponownie kliknij pozycję skrypt i wklej ten nowy kod przekształcenia u dołu, łącząc go z poprzednią transformację, wpisując nazwę tego przekształcenia przed wklejonym fragmentem kodu.

14. Wykres przepływu danych powinien teraz wyglądać podobnie do tego:

    ![Fragment kodu źródłowego 1](media/data-flow/snippet-adf-1.png)

  Masz teraz działający przepływ danych z ogólnymi sprawdzeniami deduping i NULL, pobierając istniejące fragmenty kodu z biblioteki skryptów przepływu danych i dodając je do istniejącego projektu.

## <a name="next-steps"></a>Następne kroki

* Utwórz resztę logiki przepływu danych, korzystając z mapowania [przekształceń](concepts-data-flow-overview.md)przepływów danych.
