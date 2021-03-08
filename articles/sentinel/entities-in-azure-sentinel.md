---
title: Klasyfikowanie i analizowanie danych na platformie Azure — użycie jednostek Microsoft Docs
description: Przypisywanie klasyfikacji jednostek (użytkowników, nazw hostów, adresów IP) do elementów danych w witrynie Azure wskaźnikowej i używanie ich do porównywania, analizowania i skorelowania danych z wielu źródeł.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 43da1af7a3001d7f8e000a878948428a3d63aa4e
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456302"
---
# <a name="classify-and-analyze-data-using-entities-in-azure-sentinel"></a>Klasyfikowanie i analizowanie danych przy użyciu jednostek na platformie Azure — wskaźnik

## <a name="what-are-entities"></a>Co to są jednostki?

Gdy alerty są wysyłane do lub generowane przez wskaźnik produktów platformy Azure, zawierają elementy danych, które wskaźnik może rozpoznać i sklasyfikować do kategorii jako **jednostki**. Gdy punkt kontrolny platformy Azure zdaje sobie sprawę, jaki rodzaj elementu danych reprezentuje, znane są odpowiednie pytania dotyczące tego elementu, a następnie można porównać informacje o tym elemencie w całym zakresie źródeł danych i łatwo śledzić go i odwoływać się do niego w całym środowisku badania kontrolnego — analiz, badań, korygowania, łowiectwa itp. Typowymi przykładami jednostek są użytkownicy, hosty, pliki, procesy, adresy IP i adresy URL.

### <a name="entity-identifiers"></a>Identyfikatory jednostek

System Azure Wskaźnikowanie obsługuje szeroką gamę typów jednostek. Każdy typ ma własne unikatowe atrybuty, w tym część, która może służyć do identyfikowania określonej jednostki. Te atrybuty są reprezentowane jako pola w jednostce i są nazywane **identyfikatorami**. Zapoznaj się z pełną listą obsługiwanych jednostek i ich identyfikatorów poniżej.

#### <a name="strong-and-weak-identifiers"></a>Silne i słabe identyfikatory

Jak wspomniano powyżej, dla każdego typu jednostki istnieją pola lub zestawy pól, które można zidentyfikować. Te pola lub zestawy pól mogą być nazywane **silnymi identyfikatorami** , jeśli mogą jednoznacznie identyfikować jednostkę bez niejednoznaczności lub jako **słabe identyfikatory** , jeśli mogą identyfikować jednostkę w pewnych okolicznościach, ale nie są gwarantowane w celu jednoznacznego zidentyfikowania jednostki we wszystkich przypadkach. W wielu przypadkach, chociaż wybór słabych identyfikatorów może być połączony w celu utworzenia silnego identyfikatora.

Na przykład konta użytkowników mogą być zidentyfikowane jako jednostki **konta** w więcej niż jeden sposób: przy użyciu pojedynczej **silnej identyfikator** , takiej jak identyfikator liczbowy konta usługi Azure AD (pole **GUID** ) lub jego wartość **głównej nazwy użytkownika (UPN)** lub alternatywnie, przy użyciu kombinacji **słabych identyfikatorów** , takich jak **nazwy** i pola **NTDomain** . Różne źródła danych mogą identyfikować tego samego użytkownika na różne sposoby. Za każdym razem, gdy wskaźnik na platformie Azure wykryje dwie jednostki, które mogą rozpoznać jako tę samą jednostkę na podstawie ich identyfikatorów, Scala dwie jednostki w jedną jednostkę, dzięki czemu może być prawidłowo obsługiwana i spójna.

Jeśli jednak jeden z dostawców zasobów tworzy alert, w którym jednostka nie jest wystarczająco identyfikowana — na przykład przy użyciu tylko jednego **słabego identyfikatora** , takiego jak nazwa użytkownika bez kontekstu nazwy domeny, jednostki użytkownika nie można scalić z innymi wystąpieniami tego samego konta użytkownika. Te inne wystąpienia byłyby identyfikowane jako osobne jednostki, a te dwie jednostki pozostaną osobne zamiast ujednolicone.

Aby zminimalizować ryzyko wystąpienia tego problemu, należy sprawdzić, czy wszyscy dostawcy alertów prawidłowo identyfikują jednostki w wygenerowanej przez nich alertach. Ponadto synchronizowanie jednostek kont użytkowników z Azure Active Directory może utworzyć katalog ujednolicania, który będzie mógł scalać jednostki konta użytkownika.

#### <a name="supported-entities"></a>Obsługiwane jednostki

Następujące typy jednostek są obecnie identyfikowane na platformie Azure — wskaźnik:

- Konto użytkownika
- Host
- Adres IP
- Złośliwe oprogramowanie
- Plik
- Proces
- Aplikacja w chmurze
- Nazwa domeny
- Zasób platformy Azure
- Wartość skrótu pliku
- Klucz rejestru
- Wartość rejestru
- Grupa zabezpieczeń
- Adres URL
- Urządzenie IoT
- Mailbox
- Klaster poczty
- Wiadomość e-mail
- Przesyłanie poczty

Identyfikatory jednostek i inne istotne informacje można wyświetlić w [odwołaniu do jednostek](entities-reference.md).

## <a name="entity-mapping"></a>Mapowanie jednostek

W jaki sposób platforma Azure — wskaźnik wykrywa dane w alercie jako identyfikujący jednostkę?

Przyjrzyjmy się, jak przetwarzanie danych odbywa się na platformie Azure — wskaźnik. Dane są pozyskiwane z różnych źródeł za pośrednictwem [łączników](connect-data-sources.md), niezależnie od tego, czy Usługa Service-to-Service, oparta na agencie, czy z usługą przesyłania dalej dzienników. Dane są przechowywane w tabelach w obszarze roboczym Log Analytics. W tych tabelach są następnie wykonywane zapytania w regularnych zaplanowanych interwałach przez zdefiniowane i włączone reguły analizy. Jedną z wielu akcji podejmowanych przez te reguły analizy jest Mapowanie pól danych w tabelach na jednostki rozpoznawane przez wskaźnik na platformie Azure. Zgodnie z mapowaniami definiowanymi w regułach analizy, wskaźnik na platformie Azure będzie przyjmować pola z wyników zwróconych przez zapytanie, rozpoznawać je według identyfikatorów określonych dla każdego typu jednostki i stosować do nich typ jednostki identyfikowany przez te identyfikatory.

Co to jest punkt?

Gdy wskaźnik produktów platformy Azure jest w stanie identyfikować jednostki w alertach pochodzących z różnych typów źródeł danych, a zwłaszcza jeśli może to zrobić przy użyciu silnych identyfikatorów wspólnych dla każdego źródła danych lub trzeciego schematu, można łatwo skorelować wszystkie te alerty i źródła danych. Te korelacje ułatwiają tworzenie obszernego magazynu informacji i szczegółowe informacje o jednostkach, zapewniając solidną podstawę dla operacji związanych z bezpieczeństwem.

Dowiedz się, jak [mapować pola danych do jednostek](map-data-fields-to-entities.md).

Dowiedz się [, które identyfikatory jednoznacznie identyfikują jednostkę](entities-reference.md).

## <a name="entity-pages"></a>Strony jednostki

W przypadku wystąpienia dowolnej jednostki (obecnie ograniczonej do użytkowników i hostów) w wyszukiwaniu, alercie lub zbadaniu można wybrać jednostkę i przetworzyć ją na **stronie jednostki**, a arkusz danych zapełnił przydatne informacje o tej jednostce. Typy informacji, które można znaleźć na tej stronie, obejmują podstawowe fakty dotyczące jednostki, oś czasu istotnych zdarzeń związanych z tą jednostką i szczegółowe informacje o zachowaniu działania jednostki.

Strony jednostki składają się z trzech części:

- Panel po lewej stronie zawiera informacje identyfikacyjne jednostki, zebrane ze źródeł danych, takich jak Azure Active Directory, Azure Monitor, Azure Security Center i Microsoft Defender.

- Panel centrum pokazuje graficzną i tekstową oś czasu dla istotnych zdarzeń związanych z jednostką, takich jak alerty, zakładki i działania. Działania są agregacją istotnych zdarzeń z Log Analytics. Zapytania, które wykrywają te działania, są opracowywane przez zespoły badawcze zabezpieczeń firmy Microsoft.

- Panel po prawej stronie przedstawia informacje o zachowaniu działania w jednostce. Te informacje ułatwiają szybkie identyfikowanie anomalii i zagrożeń bezpieczeństwa. Szczegółowe informacje są opracowywane przez zespoły badawcze zabezpieczeń firmy Microsoft i są oparte na modelach wykrywania anomalii.

### <a name="the-timeline"></a>Oś czasu

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Oś czasu stron jednostki":::

Oś czasu jest główną częścią udziału strony jednostki na potrzeby analiz zachowań na platformie Azure. Przedstawia historię zdarzeń związanych z jednostkami, ułatwiając zrozumienie aktywności jednostki w określonym przedziale czasowym.

Możesz wybrać **zakres czasu** spośród kilku opcji predefiniowanych (takich jak *ostatnie 24 godziny*) lub ustawić go na dowolny niestandardowy zakres czasu. Ponadto można ustawić filtry, które ograniczają informacje na osi czasu do określonych typów zdarzeń lub alertów.

Na osi czasu są uwzględniane następujące typy elementów:

- Alerty — wszystkie alerty, w których jednostka jest zdefiniowana jako **zamapowana jednostka**. Należy pamiętać, że jeśli organizacja utworzyła [niestandardowe alerty przy użyciu reguł analizy](./tutorial-detect-threats-custom.md), należy upewnić się, że mapowanie jednostek reguł zostało wykonane prawidłowo.

- Zakładki — wszystkie zakładki, które zawierają konkretną jednostkę widoczną na stronie.

- Działania — agregacja istotnych zdarzeń odnoszących się do jednostki.

### <a name="entity-insights"></a>Informacje o jednostce

Usługi Entity Insights to zapytania zdefiniowane przez badaczy zabezpieczeń firmy Microsoft, aby ułatwić analitykom badanie bardziej wydajne i efektywne. Szczegółowe informacje są prezentowane jako część strony jednostki i dostarczają cennych informacji o zabezpieczeniach hostów i użytkowników w postaci danych tabelarycznych i wykresów. Informacje o tym, że nie trzeba przeznaczyć na Log Analytics. Szczegółowe informacje obejmują dane dotyczące logowania, dodawania grup, zdarzeń nietypowych i innych, a także zawierają zaawansowane algorytmy ML do wykrywania nietypowego zachowania.

Szczegółowe informacje są oparte na następujących źródłach danych:

- Dziennik systemu (Linux)
- SecurityEvent (system Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- Office (Office 365)
- BehaviorAnalytics (Azure — wskaźnik UEBA)
- Puls (Agent Azure Monitor)
- CommonSecurityLog (wskaźnik platformy Azure)

### <a name="how-to-use-entity-pages"></a>Jak używać stron jednostki

Strony jednostki zostały zaprojektowane jako części wielu scenariuszy użycia i można uzyskać do nich dostęp z zarządzania zdarzeniami, wykresu badawczego, zakładek lub bezpośrednio ze strony wyszukiwania jednostek w obszarze **Analiza zachowań jednostek** w menu głównym wskaźnikiem na platformie Azure.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Przypadki użycia stron jednostki":::

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono informacje na temat pracy z jednostkami na platformie Azure — wskaźnikiem. Aby uzyskać praktyczne wskazówki dotyczące implementacji i korzystać z szczegółowych informacji, zobacz następujące artykuły:

- [Włącz analizę zachowań jednostek](./enable-entity-behavior-analytics.md) na platformie Azure.
- [W przypadku zagrożeń bezpieczeństwa](./hunting.md).
