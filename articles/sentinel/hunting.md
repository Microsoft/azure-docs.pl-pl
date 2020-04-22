---
title: Możliwości polowania w usłudze Azure Sentinel| Dokumenty firmy Microsoft
description: W tym artykule opisano sposób korzystania z możliwości polowania usługi Azure Sentinel.
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
ms.openlocfilehash: 52af688917aa531d125f83844df29a988ed7cb7e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686628"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Polowanie na zagrożenia za pomocą usługi Azure Sentinel

Jeśli jesteś badaczem, który chce aktywnie wyszukiwać zagrożenia bezpieczeństwa, narzędzia wyszukiwania i wyszukiwania za pomocą zaawansowanego narzędzia do wyszukiwania i wyszukiwania aplikacji Azure Sentinel umożliwiają poszukiwanie zagrożeń bezpieczeństwa w źródłach danych organizacji. Jednak systemy i urządzenia zabezpieczające generują góry danych, które mogą być trudne do przeanalizowania i przefiltrowania na znaczące zdarzenia. Aby pomóc analitykom zabezpieczeń aktywnie szukać nowych anomalii, które nie zostały wykryte przez aplikacje zabezpieczeń, wbudowane zapytania myśliwskie usługi Azure Sentinel prowadzą użytkownika do zadawania właściwych pytań w celu znalezienia problemów w danych, które zostały już zainstalowane w sieci. 

Na przykład jedna wbudowana kwerenda zawiera dane o najbardziej nietypowych procesach uruchomionych w infrastrukturze — nie chcesz, aby alert za każdym razem, gdy są uruchamiane, może być całkowicie niewinny, ale możesz przyjrzeć się zapytaniu przy okazji, aby sprawdzić, czy jest coś niezwykłego. 



Za pomocą funkcji Azure Sentinel hunting można korzystać z następujących możliwości:

- Wbudowane zapytania: Aby rozpocząć, strona początkowa zawiera wstępnie załadowane przykłady zapytań zaprojektowanych w celu rozpoczęcia pracy i zapoznania się z tabelami i językiem zapytań. Te wbudowane zapytania myśliwskie są opracowywane przez badaczy zabezpieczeń firmy Microsoft w sposób ciągły, dodawanie nowych zapytań i dostosowywanie istniejących zapytań, aby zapewnić punkt wejścia, aby wyszukać nowe wykrycia i dowiedzieć się, gdzie rozpocząć polowanie na początki nowych ataków. 

- Zaawansowany język zapytań z IntelliSense: Zbudowany na górze języka zapytań, który zapewnia elastyczność, której potrzebujesz, aby przejść polowanie na wyższy poziom.

- Twórz własne zakładki: Podczas polowania możesz natknąć się na dopasowania lub wyniki, pulpity nawigacyjne lub działania, które wyglądają nietypowo lub podejrzanie. Aby oznaczyć te elementy, aby można było do nich wrócić w przyszłości, użyj funkcji zakładki. Zakładki umożliwiają zapisanie elementów na później, które mają być używane do tworzenia incydentu do zbadania. Aby uzyskać więcej informacji na temat zakładek, zobacz [Używanie zakładek podczas polowania](hunting.md).
- Użyj notesów, aby zautomatyzować badanie: notesy są jak podręczniki krok po kroku, które można tworzyć, aby przejść przez etapy dochodzenia i polowania.  Notesy hermetyzują wszystkie kroki polowania w podręczniku wielokrotnegoużytnika, który można udostępniać innym osobom w organizacji. 
- Zapytanie o przechowywane dane: dane są dostępne w tabelach, które można zbadać. Na przykład można wysyłać zapytania o tworzenie procesów, zdarzenia DNS i wiele innych typów zdarzeń.

- Linki do społeczności: wykorzystaj możliwości większej społeczności, aby znaleźć dodatkowe zapytania i źródła danych.
 
## <a name="get-started-hunting"></a>Rozpocznij polowanie

1. W portalu Azure Sentinel kliknij pozycję **Polowanie**.
  ![Azure Sentinel rozpoczyna polowanie](media/tutorial-hunting/hunting-start.png)

2. Po otwarciu strony **polowanie** wszystkie zapytania myśliwskie są wyświetlane w jednej tabeli. W tabeli wymieniono wszystkie zapytania napisane przez zespół analityków zabezpieczeń firmy Microsoft, a także wszelkie dodatkowe kwerendy utworzone lub zmodyfikowane. Każda kwerenda zawiera opis tego, na co poluje i na jakiego rodzaju dane są uruchamiane. Szablony te są pogrupowane według ich różnych taktyk - ikony po prawej kategoryzują typ zagrożenia, takie jak początkowy dostęp, trwałość i eksfiltracja. Można filtrować te szablony zapytań myśliwskich przy użyciu dowolnego z pól. Możesz zapisać dowolne zapytanie do ulubionych. Zapisując kwerendę do ulubionych, kwerenda jest automatycznie uruchamiana za każdym razem, gdy strona **polowanie** jest dostępna. Można utworzyć własne zapytanie łowieckie lub sklonować i dostosować istniejący szablon kwerendy myśliwskiej. 
 
2. Kliknij **przycisk Uruchom kwerendę** na stronie szczegółów kwerendy łowieckiej, aby uruchomić dowolną kwerendę bez opuszczania strony polowania.  Liczba dopasowań jest wyświetlana w tabeli. Przejrzyj listę zapytań myśliwskich i ich dopasowań. Sprawdź, z którym etapem w łańcuchu zabójstw jest skojarzony mecz.

3. Wykonaj szybki przegląd kwerendy podstawowej w okienku szczegółów kwerendy lub kliknij pozycję **Wyświetl wynik kwerendy,** aby otworzyć kwerendę w usłudze Log Analytics. U dołu przejrzyj dopasowania do kwerendy.

4.    Kliknij wiersz i wybierz **dodaj zakładkę,** aby dodać wiersze do zbadania - możesz to zrobić dla wszystkiego, co wygląda podejrzanie. 

5. Następnie wróć do głównej strony **polowania** i kliknij kartę **Zakładki,** aby wyświetlić wszystkie podejrzane działania. 

6. Wybierz zakładkę, a następnie kliknij przycisk **Zbadaj,** aby otworzyć środowisko dochodzenia. Zakładki można filtrować. Jeśli na przykład badasz kampanię, możesz utworzyć tag kampanii, a następnie przefiltrować wszystkie zakładki na podstawie kampanii.

1. Po wykryciu, które zapytanie myśliwskie zapewnia wysokiej wartości wgląd w możliwych ataków, można również utworzyć niestandardowe reguły wykrywania na podstawie zapytania i powierzchni tych spostrzeżeń jako alerty do odpowiedzi na zdarzenia zabezpieczeń.

 

## <a name="query-language"></a>Język zapytań 

Polowanie w usłudze Azure Sentinel opiera się na języku zapytań Kusto. Aby uzyskać więcej informacji na temat języka kwerendy i obsługiwanych operatorów, zobacz [Odwołanie do języka kwerendy](/azure/azure-monitor/log-query/get-started-queries).

## <a name="public-hunting-query-github-repository"></a>Publiczne zapytanie łowieckie Repozytorium GitHub

Zapoznaj się z [repozytorium zapytań myśliwskich](https://github.com/Azure/Orion). Współtworzenie przykładowych zapytań udostępnionych przez naszych klientów i używanie przykładów.

 

## <a name="sample-query"></a>Przykładowe zapytanie

Typowa kwerenda rozpoczyna się od nazwy tabeli, \|po której następuje seria operatorów oddzielonych .

W powyższym przykładzie należy rozpocząć od nazwy tabeli SecurityEvent i dodać elementy potokowe w razie potrzeby.

1. Zdefiniuj filtr czasu, aby przeglądać tylko rekordy z poprzednich siedmiu dni.

2. Dodaj filtr w kwerendzie, aby wyświetlić tylko identyfikator zdarzenia 4688.

3. Dodaj filtr w kwerendzie w wierszu polecenia, aby zawierał tylko wystąpienia cscript.exe.

4. Projektuj tylko kolumny, które chcesz eksplorować, i ogranicz wyniki do 1000, a następnie kliknij przycisk **Uruchom kwerendę**.
5. Kliknij zielony trójkąt i uruchom kwerendę. Można przetestować kwerendę i uruchomić go w celu wyszukynia nietypowego zachowania.

## <a name="useful-operators"></a>Przydatne operatory

Język zapytań jest potężny i ma wiele dostępnych operatorów, niektóre przydatne operatory są wymienione tutaj:

**gdzie** — filtrowanie tabeli do podzbioru wierszy, które spełniają predykatu.

**podsumuj** — twórz tabelę, która agreguje zawartość tabeli wprowadzania.

**sprzężenie** — scalanie wierszy dwóch tabel w celu utworzenia nowej tabeli przez dopasowanie wartości określonych kolumn z każdej tabeli.

**count** - Zwraca liczbę rekordów w zestawie rekordów wejściowych.

**top** - Zwraca pierwsze rekordy N posortowane według określonych kolumn.

**limit** - Powrót do określonej liczby wierszy.

**projekt** — wybierz kolumny, które mają być uwzględniane, zmieniane lub upuszczane, oraz wstawianie nowych kolumn obliczeniowych.

**extend** - Utwórz kolumny obliczeniowe i dołącz je do zestawu wyników.

**makeset** - Zwracanie dynamicznej tablicy zestawu odrębnych wartości, które expr przyjmuje w grupie

**find** - Znajdź wiersze pasujące do predykatu w zestawie tabel.

## <a name="save-a-query"></a>Zapisywanie kwerendy

Można utworzyć lub zmodyfikować kwerendę i zapisać ją jako własne zapytanie lub udostępnić ją użytkownikom, którzy znajdują się w tej samej dzierżawie.

   ![Zapisz kwerendę](./media/tutorial-hunting/save-query.png)

Utwórz nową kwerendę myśliwską:

1. Kliknij **pozycję Nowa kwerenda** i wybierz pozycję **Zapisz**.
2. Wypełnij wszystkie puste pola i wybierz pozycję **Zapisz**.

   ![Nowe zapytanie](./media/tutorial-hunting/new-query.png)

Klonuj i modyfikuj istniejącą kwerendę myśliwską:

1. Zaznacz kwerendę polującą w tabeli, którą chcesz zmodyfikować.
2. Zaznacz wielokropek (...) w wierszu kwerendy, którą chcesz zmodyfikować, a następnie wybierz opcję **Klonuj kwerendę**.

   ![kwerenda klonowania](./media/tutorial-hunting/clone-query.png)
 

3. Zmodyfikuj kwerendę i wybierz pozycję **Utwórz**.

   ![kwerenda niestandardowa](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się, jak uruchomić badanie polowania za pomocą usługi Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:


- [Uruchamianie automatycznych kampanii łowieckich za pomocą notesów](notebooks.md)
- [Użyj zakładek, aby zapisać interesujące informacje podczas polowania](bookmarks.md)
