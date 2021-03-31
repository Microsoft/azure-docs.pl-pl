---
title: Możliwości polowania na platformie Azure — wskaźnikowa | Microsoft Docs
description: Zapoznaj się z wbudowanymi zapytaniami dotyczącymi polowań na platformie Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: b88185a09e69b1c7bd6c6c58b8e22190b4a2e394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576758"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>W przypadku zagrożeń związanych z platformą Azure — wskaźnikiem

Jeśli jesteś analitykiem, który chce być aktywny w sprawie wyszukania zagrożeń bezpieczeństwa, platforma Azure bada zaawansowane narzędzia wyszukiwania i zapytań w poszukiwaniu zagrożeń związanych z bezpieczeństwem w źródłach danych organizacji. Jednak systemy i urządzenia zabezpieczeń generują górach danych, które mogą być trudne do analizy i filtrowania do znaczących zdarzeń. Aby ułatwić analitykom zabezpieczeń odszukanie nowych anomalii, które nie zostały wykryte przez aplikacje zabezpieczające, wbudowane zapytania polowające dotyczące platformy Azure wskazują, że zażądają odpowiednich pytań, aby znaleźć problemy w danych, które już znajdują się w sieci. 

Na przykład jedno wbudowane zapytanie udostępnia dane dotyczące najbardziej nietypowych procesów uruchomionych w infrastrukturze — nie można utworzyć alertu dotyczącego każdego uruchomienia, ale mogą one zostać całkowicie nieszkodliwene, ale warto zajrzeć do zapytania, aby sprawdzić, czy występują jakieś sytuacje. 



Korzystając z łowiectwa wskaźnikowego platformy Azure, możesz korzystać z następujących możliwości:

- Wbudowane zapytania: Aby rozpocząć, Strona początkowa zawiera przykłady wstępnie załadowanych zapytań, które umożliwiają rozpoczęcie pracy i zapoznanie się z tabelami oraz językiem zapytań. Te wbudowane zapytania polowania są opracowywane przez badaczy zabezpieczeń firmy Microsoft w sposób ciągły, dodawanie nowych zapytań i dostosowywanie istniejących zapytań w celu udostępnienia punktu wejścia do wyszukiwania nowych wykryć i ustalenia miejsca, w którym należy zacząć polowanie na początku nowych ataków. 

- Zaawansowany język zapytań z technologią IntelliSense: wbudowaną na podstawie języka zapytań, który zapewnia elastyczność potrzebną do skorzystania z kolejnego poziomu.

- Tworzenie własnych zakładek: w trakcie procesu polowania mogą trafiać dopasowania lub wyniki, pulpity nawigacyjne lub działania, które wyglądają nierzadko lub podejrzane. Aby oznaczyć te elementy tak, aby można było je w przyszłości wrócić do nich, użyj funkcji Zakładka. Zakładki umożliwiają zapisywanie elementów w celu ich późniejszego użycia w celu utworzenia incydentu do badania. Aby uzyskać więcej informacji na temat zakładek, zobacz [Używanie zakładek w łowiectwie](hunting.md).
- Korzystaj z notesów do automatyzowania badań: Notesy przypominają elementy PlayBook krok po kroku, które można skompilować, aby zapoznać się z krokami badania i wyszukiwania.  Notesy hermetyzują wszystkie kroki polowania w element PlayBook wielokrotnego użytku, które mogą być współużytkowane z innymi osobami w organizacji. 
- Zbadaj przechowywane dane: dane są dostępne w tabelach, aby można było wykonywać zapytania. Można na przykład wykonywać zapytania dotyczące tworzenia procesów, zdarzeń DNS i wielu innych typów zdarzeń.

- Linki do społeczności: wykorzystanie mocy większej społeczności do znajdowania dodatkowych zapytań i źródeł danych.
 
## <a name="get-started-hunting"></a>Wprowadzenie do łowiectwa

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **łowiectwo**.
  ![Azure — wskaźnik startowy](media/tutorial-hunting/hunting-start.png)

2. Po otwarciu strony **polowania** wszystkie zapytania polowania są wyświetlane w pojedynczej tabeli. W tabeli wymieniono wszystkie zapytania, które zostały utworzone przez zespół analityków zabezpieczeń firmy Microsoft, a także wszelkie dodatkowe lub zmodyfikowane zapytania. Każde zapytanie zawiera opis tego, czego szuka i jakiego rodzaju dane są uruchamiane. Te szablony są pogrupowane według różnych taktykę — ikony po prawej stronie są typu zagrożenia, takie jak początkowy dostęp, trwałość i eksfiltracji. Te szablony zapytań polowania można filtrować przy użyciu dowolnych pól. Możesz zapisać dowolne zapytanie do ulubionych. Zapisanie zapytania do ulubionych spowoduje automatyczne uruchomienie zapytania przy każdej próbie uzyskania dostępu do strony **polowania** . Możesz utworzyć własne zapytanie polowania lub klonować i dostosowywać istniejący szablon zapytania polowania. 
 
2. Kliknij przycisk **Uruchom zapytanie** na stronie szczegółów zapytania polowania, aby uruchomić dowolne zapytanie bez opuszczania strony polowania.  W tabeli zostanie wyświetlona liczba dopasowań. Przejrzyj listę zapytań polowania i ich dopasowania. Sprawdź, który etap łańcucha kasowania jest skojarzony z.

3. Wykonaj szybkie przegląd bazowego zapytania w okienku szczegółów zapytania lub kliknij pozycję **Wyświetl wynik zapytania** , aby otworzyć zapytanie w log Analytics. W dolnej części zapoznaj się z dopasowaniem do zapytania.

4.    Kliknij wiersz i wybierz pozycję **Dodaj zakładkę** , aby dodać wiersze do zbadania — można to zrobić dla wszystkich elementów, które wyglądają podejrzanie. 

5. Następnie wróć do strony głównej **łowiectwa** i kliknij kartę **zakładki** , aby wyświetlić wszystkie podejrzane działania. 

6. Wybierz zakładkę, a następnie kliknij przycisk **Zbadaj** , aby otworzyć środowisko badania. Można filtrować zakładki. Na przykład, jeśli badasz kampanię, możesz utworzyć tag dla kampanii, a następnie filtrować wszystkie zakładki na podstawie kampanii.

1. Po wykryciu, które zapytanie polowające zapewnia wysoką wartość wglądu w możliwe ataki, można także utworzyć niestandardowe reguły wykrywania na podstawie zapytania i przedstawić te informacje jako alerty w odniesieniu do odpowiedzi na zdarzenia zabezpieczeń.

 

## <a name="query-language"></a>Język zapytań 

Łowiectwo na platformie Azure — na podstawie języka zapytań Kusto. Aby uzyskać więcej informacji na temat języka zapytań i obsługiwanych operatorów, zobacz [Dokumentacja języka zapytań](../azure-monitor/logs/get-started-queries.md).

## <a name="public-hunting-query-github-repository"></a>Zapytanie dotyczące publicznego polowania w repozytorium GitHub

Zapoznaj się z [repozytorium zapytań polowania](https://github.com/Azure/Orion). Współtworzenie i używanie przykładowych zapytań udostępnionych przez naszych klientów.

 

## <a name="sample-query"></a>Przykładowe zapytanie

Typowa kwerenda rozpoczyna się od nazwy tabeli, po której następuje seria operatorów rozdzielonych przez \| .

W powyższym przykładzie Zacznij od nazwy tabeli SecurityEvent i Dodaj elementy potokowe zgodnie z wymaganiami.

1. Zdefiniuj filtr czasu, aby przeglądać tylko rekordy z ostatnich siedmiu dni.

2. Dodaj filtr w zapytaniu, aby wyświetlić tylko zdarzenie o IDENTYFIKATORze 4688.

3. Dodaj filtr do zapytania w wierszu polecenia, aby zawierał tylko wystąpienia cscript.exe.

4. Projektuj tylko te kolumny, które interesują Cię, i Ogranicz wyniki do 1000, a następnie kliknij przycisk **Uruchom zapytanie**.
5. Kliknij zielony trójkąt i uruchom zapytanie. Możesz przetestować zapytanie i uruchomić je w celu wyszukania nietypowego zachowania.

## <a name="useful-operators"></a>Przydatne operatory

Język zapytań jest zaawansowany i ma wiele dostępnych operatorów, niektóre przydatne operatory są wymienione tutaj:

**WHERE** -filteruje tabelę do podzestawu wierszy, które spełniają predykat.

**Podsumowując** — tworzy tabelę, która agreguje zawartość tabeli wejściowej.

**Dołącz** — Scala wiersze dwóch tabel, aby utworzyć nową tabelę przez dopasowanie wartości określonych kolumn z każdej tabeli.

**Count** — zwraca liczbę rekordów w zestawie rekordów wejściowych.

**Top** — zwraca pierwsze N rekordów posortowane według określonych kolumn.

**Limit** — zwraca do określonej liczby wierszy.

**projekt** — wybierz kolumny do dołączenia, zmiany nazwy lub usunięcia oraz Wstaw nowe kolumny obliczane.

**Rozwiń** — Utwórz kolumny obliczeniowe i Dołącz je do zestawu wyników.

**MakeSet** — zwraca tablicę dynamiczną (JSON) zestawu unikatowych wartości, które wyrażenie ma w grupie

**Znajdź** — umożliwia znalezienie wierszy pasujących do predykatu w zestawie tabel.

## <a name="save-a-query"></a>Zapisz zapytanie

Możesz utworzyć lub zmodyfikować zapytanie i zapisać je jako własne zapytanie lub udostępnić je użytkownikom, którzy znajdują się w tej samej dzierżawie.

   ![Zapisz zapytanie](./media/tutorial-hunting/save-query.png)

Utwórz nowe zapytanie polowania:

1. Kliknij pozycję **nowe zapytanie** i wybierz pozycję **Zapisz**.
2. Wypełnij wszystkie puste pola i wybierz pozycję **Zapisz**.

   ![Nowe zapytanie](./media/tutorial-hunting/new-query.png)

Klonuj i Modyfikuj istniejące zapytanie polowania:

1. Wybierz zapytanie polowania w tabeli, którą chcesz zmodyfikować.
2. Wybierz wielokropek (...) w wierszu zapytania, które chcesz zmodyfikować, a następnie wybierz pozycję **Klonuj zapytanie**.

   ![Klonuj zapytanie](./media/tutorial-hunting/clone-query.png)
 

3. Zmodyfikuj zapytanie i wybierz pozycję **Utwórz**.

   ![zapytanie niestandardowe](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Następne kroki
W tym artykule wyjaśniono, jak uruchomić badanie polowania za pomocą oceny platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:


- [Korzystanie z notesów do uruchamiania zautomatyzowanych kampanii łowieckich](notebooks.md)
- [Korzystanie z zakładek do zapisywania interesujących informacji podczas polowania](bookmarks.md)