---
title: Automatyzacja obsługi zdarzeń na platformie Azure — Wskaźnikowanie | Microsoft Docs
description: W tym artykule wyjaśniono, jak za pomocą reguł automatyzacji zautomatyzować obsługę zdarzeń, aby zmaksymalizować wydajność i efektywność SOC w odpowiedzi na zagrożenia bezpieczeństwa.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 1ff9fbbb6cd4b8827555a6cb1b222ed4eb0a5299
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609812"
---
# <a name="automate-incident-handling-in-azure-sentinel-with-automation-rules"></a>Automatyzowanie obsługi zdarzeń na platformie Azure wskaźnikowej przy użyciu reguł automatyzacji

W tym artykule wyjaśniono, jakie są reguły automatyzacji kontroli wskaźnikowej platformy Azure oraz jak używać ich do implementowania operacji aranżacji, automatyzacji i reagowania w zabezpieczeniach (o), zwiększając efektywność SOC i oszczędność czasu i zasobów.

> [!IMPORTANT]
>
> - Funkcja **reguł automatyzacji** jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

## <a name="what-are-automation-rules"></a>Co to są reguły automatyzacji?

Reguły automatyzacji są nową koncepcją na platformie Azure — wskaźnikiem. Ta funkcja umożliwia użytkownikom centralne zarządzanie automatyzacją obsługi zdarzeń. Oprócz tego, że przypiszesz elementy PlayBook do zdarzeń (nie tylko dla alertów, jak poprzednio), reguły automatyzacji umożliwiają również Automatyzowanie odpowiedzi dla wielu reguł analizy jednocześnie, automatyczne oznaczanie tagów, przypisywanie lub zamykanie incydentów bez potrzeby elementy playbook i sterowanie kolejnością wykonywanych akcji. Reguły automatyzacji usprawnią korzystanie z usługi Automation na platformie Azure i umożliwią uproszczenie złożonych przepływów pracy dla procesów aranżacji zdarzeń.

## <a name="components"></a>Składniki

Reguły automatyzacji składają się z kilku składników:

### <a name="trigger"></a>Wyzwalacz

Reguły automatyzacji są wyzwalane przez utworzenie zdarzenia. 

Aby przeprowadzić przegląd — incydenty są tworzone na podstawie alertów według reguł analizy, dla których istnieją cztery typy, jak wyjaśniono w samouczku [wykrywanie zagrożeń przy użyciu wbudowanych reguł analizy na platformie Azure](tutorial-detect-threats-built-in.md).

### <a name="conditions"></a>Warunki

Złożone zestawy warunków można zdefiniować, aby określić, kiedy akcje (patrz poniżej) powinny być uruchamiane. Te warunki są zwykle oparte na Stanach lub wartościach atrybutów incydentów i ich jednostek oraz mogą zawierać `AND` / `OR` / `NOT` / `CONTAINS` operatory.

### <a name="actions"></a>Akcje

Akcje można definiować do uruchamiania, gdy spełnione są warunki (patrz powyżej). Można zdefiniować wiele akcji w regule i można wybrać kolejność ich uruchamiania (patrz poniżej). Poniższe akcje można definiować przy użyciu reguł automatyzacji, bez potrzeby [zaawansowanych funkcji element PlayBook](automate-responses-with-playbooks.md):

- Zmiana stanu zdarzenia, dzięki czemu przepływ pracy jest aktualny.

  - Gdy zmienia się na "zamknięte", określając [przyczynę zamknięcia](tutorial-investigate-cases.md#closing-an-incident) i dodając komentarz. Pozwala to śledzić wydajność i efektywność oraz dostrajać, aby zmniejszyć liczbę fałszywych dodatnich.

- Zmiana wagi zdarzenia — można ją sprawdzić i zmienić jej priorytety na podstawie obecności, nieobecności, wartości lub atrybutów jednostek uwzględnionych w zdarzeniu.

- Przypisywanie zdarzenia do właściciela — pomaga to w pokierowaniu typów incydentów do pracowników, które najlepiej pasują do ich działania lub do najbardziej dostępnego personelu.

- Dodawanie tagu do zdarzenia — jest to przydatne do klasyfikowania zdarzeń według podmiotu, osoby atakującej lub innego wspólnego mianownika.

Ponadto można zdefiniować akcję do uruchomienia [element PlayBook](tutorial-respond-threats-playbook.md), aby wykonać bardziej złożone akcje odpowiedzi, w tym takie, które obejmują systemy zewnętrzne. Tylko elementy PlayBook aktywowane przez [wyzwalacz zdarzenia](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts) są dostępne do użycia w regułach automatyzacji. Można zdefiniować akcję obejmującą wiele elementy playbooków lub kombinacji elementy playbook i innych akcji oraz kolejność, w jakiej będą uruchamiane.

### <a name="expiration-date"></a>Data wygaśnięcia

Można zdefiniować datę wygaśnięcia reguły automatyzacji. Reguła zostanie wyłączona po tej dacie. Jest to przydatne do obsługi (czyli zamykania) zdarzeń "szumów" spowodowanych planowanymi, ograniczonymi czasami działaniami, takimi jak testowanie penetracji.

### <a name="order"></a>Zamówienie

Można zdefiniować kolejność, w której będą uruchamiane reguły automatyzacji. Późniejsze reguły automatyzacji będą szacować warunki zdarzenia zgodnie z jego stanem po wykonaniu działania przez poprzednie reguły automatyzacji.

Na przykład, jeśli "Pierwsza reguła usługi Automation" zmieniła ważność zdarzenia z średniego na niski i "druga reguła automatyzacji" jest zdefiniowana do uruchamiania tylko w przypadku incydentów o średniej lub wyższej ważności, nie będzie ona działać w tym zdarzeniu.

## <a name="common-use-cases-and-scenarios"></a>Typowe przypadki użycia i scenariusze

### <a name="incident-triggered-automation"></a>Automatyzacja wyzwolona zdarzeniami

Do tej pory tylko alerty mogą wyzwolić automatyczną odpowiedź przy użyciu elementy PlayBook. Dzięki regułom automatyzacji zdarzenia mogą teraz wyzwalać zautomatyzowane łańcuchy odpowiedzi, które mogą obejmować nowe elementy PlayBook wyzwalane incydenty ([wymagane są uprawnienia specjalne](#permissions-for-automation-rules-to-run-playbooks)), gdy zdarzenie zostanie utworzone. 

### <a name="trigger-playbooks-for-microsoft-providers"></a>Wyzwalacz elementy PlayBook dla dostawców firmy Microsoft

Reguły automatyzacji umożliwiają automatyzację obsługi alertów zabezpieczeń firmy Microsoft przez zastosowanie tych reguł do zdarzeń utworzonych na podstawie alertów. Reguły automatyzacji mogą wywoływać elementy PlayBook ([wymagane są uprawnienia specjalne](#permissions-for-automation-rules-to-run-playbooks)) i przekazać do nich zdarzenia ze wszystkimi szczegółami, w tym alertami i jednostkami. Ogólnie rzecz biorąc, najlepsze rozwiązania dotyczące platformy Azure wskazują użycie kolejki zdarzeń jako punktu głównego operacji zabezpieczeń.

Alerty zabezpieczeń firmy Microsoft obejmują następujące elementy:

- Microsoft Cloud App Security (MCAS)
- Usługa Azure AD Identity Protection
- Azure Defender (ASC)
- Defender for IoT (dawniej ASC for IoT)
- Ochrona usługi Office 365 w usłudze Microsoft Defender (dawniej system Office 365 ATP)
- Usługa Microsoft Defender dla punktu końcowego (dawniej MDATP)
- Usługa Microsoft Defender dla tożsamości (dawniej Azure ATP)

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>Wiele elementy PlayBook/akcji z kolejnością w jednej regule

Teraz możesz mieć prawie pełną kontrolę nad kolejnością wykonywania akcji i elementy PlayBook w ramach jednej reguły automatyzacji. Można również kontrolować kolejność wykonywania reguł automatyzacji. Dzięki temu można znacznie uprościć elementy PlayBook, zredukować je do jednego zadania lub małej, prostej sekwencji zadań i połączyć te małe elementy PlayBook w różne kombinacje w różnych regułach automatyzacji.

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>Przypisywanie jednego element PlayBook do wielu reguł analizy jednocześnie

Jeśli masz zadanie, które chcesz zautomatyzować na wszystkich regułach analizy — powiedzmy, jak utworzyć bilet pomocy technicznej w zewnętrznym systemie biletów — możesz zastosować pojedynczy element PlayBook do dowolnych lub wszystkich reguł analizy, w tym wszystkich przyszłych reguł — w jednym zrzucie. Dzięki temu proste, ale powtarzające się konserwacje i dla gospodarstw domowych zadania są znacznie mniejsze.

### <a name="automatic-assignment-of-incidents"></a>Automatyczne przypisywanie zdarzeń

Zdarzenia można przypisywać do właściwego właściciela automatycznie. Jeśli SOC ma analityka, który jest wyspecjalizowany w określonej platformie, wszelkie incydenty związane z tą platformą mogą być automatycznie przypisywane do tego analityka.

### <a name="incident-suppression"></a>Pomijanie zdarzeń

Za pomocą reguł można automatycznie rozwiązywać zdarzenia, które są znane jako fałszywe/niegroźne, bez użycia elementy PlayBook. Na przykład w przypadku uruchamiania testów penetracji, przeprowadzania zaplanowanych czynności konserwacyjnych lub uaktualnień lub testowania procedur automatyzacji można utworzyć wiele zdarzeń fałszywie fałszywych, których SOC chce zignorować. Reguła automatyzacji ograniczona czasowo może automatycznie zamykać te zdarzenia podczas ich tworzenia, jednocześnie tagowanie ich przy użyciu deskryptora przyczyny ich generacji.

### <a name="time-limited-automation"></a>Automatyzacja ograniczona czasowo

Możesz dodać daty wygaśnięcia dla reguł automatyzacji. Mogą wystąpić przypadki inne niż pomijanie zdarzeń, które uzasadniają automatyzację ograniczoną czasowo. Możesz chcieć przypisać określony typ zdarzenia do określonego użytkownika (powiedzieć, InterNIC lub konsultanta) dla określonego przedziału czasu. Jeśli przedział czasu jest znany z wyprzedzeniem, możesz efektywnie spowodować wyłączenie reguły na końcu jej dokładnośću, bez konieczności Pamiętaj, aby to zrobić.

### <a name="automatically-tag-incidents"></a>Automatycznie Otaguj zdarzenia

Można automatycznie dodawać Tagi swobodne tekstu do zdarzeń, aby grupować lub klasyfikować je zgodnie z dowolnym kryterium wyboru.

## <a name="automation-rules-execution"></a>Wykonywanie reguł automatyzacji

Reguły automatyzacji są uruchamiane sekwencyjnie, zgodnie z określoną kolejnością. Każda reguła automatyzacji jest wykonywana po zakończeniu ostatniego uruchomienia. W ramach reguły automatyzacji wszystkie akcje są uruchamiane sekwencyjnie w kolejności, w której są zdefiniowane.

W przypadku akcji element PlayBook istnieje dwuminutowe opóźnienie między rozpoczęciem akcji element playbook i następną akcją na liście.

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>Uprawnienia dla reguł automatyzacji do uruchamiania elementy PlayBook

Gdy reguła automatyzacji Azure wskaźnikowego jest uruchamiana element PlayBook, używa specjalnego konta usługi Azure wskaźnikowego, specjalnie autoryzowanego dla tej akcji. Użycie tego konta (w przeciwieństwie do konta użytkownika) powoduje zwiększenie poziomu zabezpieczeń usługi.

Aby reguła automatyzacji mogła uruchamiać element PlayBook, to konto musi mieć przyznane jawne uprawnienia do grupy zasobów, w której znajduje się element PlayBook. W tym momencie Każda reguła automatyzacji będzie mogła uruchamiać dowolne element PlayBook w tej grupie zasobów.

Gdy konfigurujesz regułę automatyzacji i dodasz akcję **Uruchom element PlayBook** , zostanie wyświetlona lista rozwijana elementy PlayBook. Elementy PlayBook, do którego nie ma uprawnień na platformie Azure, będą wyświetlane jako niedostępne ("wygaszone"). Do grup zasobów elementy PlayBook "można udzielić na miejscu, wybierając łącze **Zarządzaj uprawnieniami element PlayBook** .

> [!NOTE]
> **Uprawnienia w architekturze z wieloma dzierżawcami**
>
> Reguły automatyzacji w pełni obsługują wdrożenia obejmujące wiele obszarów roboczych i wielu dzierżawców (w przypadku wielu dzierżawców przy użyciu [usługi Azure Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)).
>
> W związku z tym, jeśli wdrożenie usługi Azure MSSP korzysta z architektury wielodostępnej (na przykład), można mieć regułę automatyzacji w jednej dzierżawie, która znajduje się w innej dzierżawie, ale uprawnienia do działania wskaźnikowego do uruchomienia elementy PlayBook muszą być zdefiniowane w dzierżawie, w której znajduje się elementy PlayBook, a nie w dzierżawie, w której są zdefiniowane reguły automatyzacji.

## <a name="creating-and-managing-automation-rules"></a>Tworzenie reguł automatyzacji i zarządzanie nimi

Można tworzyć reguły automatyzacji i zarządzać nimi z różnych punktów w środowisku wskaźnikowym platformy Azure, w zależności od konkretnej potrzeby i przypadku użycia.

- **Blok automatyzacji**

    Reguły automatyzacji można centralnie zarządzać w nowym bloku **automatyzacji** (który zastępuje blok **elementy PlayBook** ), na karcie **reguły automatyzacji** . teraz możesz również zarządzać elementy PlayBook w tym bloku na karcie **elementy PlayBook** ). Z tego miejsca można utworzyć nowe reguły automatyzacji i edytować istniejące. Możesz również przeciągać reguły automatyzacji, aby zmienić kolejność wykonywania i włączyć je lub wyłączyć.

    W bloku **Automatyzacja** zostaną wyświetlone wszystkie reguły, które są zdefiniowane w obszarze roboczym, wraz z ich stanem (włączony/wyłączony) i regułami analizy, do których są stosowane.

    Jeśli potrzebujesz reguły automatyzacji, która będzie miała zastosowanie do wielu reguł analizy, utwórz ją bezpośrednio w bloku **Automatyzacja** . W górnym menu kliknij pozycję **Utwórz** i **Dodaj nową regułę**, która spowoduje otwarcie panelu **Utwórz nową regułę automatyzacji** . W tym miejscu masz pełną elastyczność konfigurowania reguły: można zastosować ją do dowolnych reguł analizy (w tym przyszłych) i zdefiniować szeroką gamę warunków i działań.

- **Kreator reguł analizy**

    Na karcie **automatyczne odpowiedzi** kreatora reguły analizy można wyświetlić reguły automatyzacji, które mają zastosowanie do określonej reguły analizy lub edytować w kreatorze, oraz zarządzać nimi.

    Po kliknięciu przycisku **Utwórz** i jednego z typów reguł (**zaplanowana reguła zapytania** lub **reguła tworzenia zdarzeń firmy Microsoft**) z górnego menu w bloku **Analiza** lub w przypadku wybrania istniejącej reguły analizy i kliknięcia przycisku **Edytuj** zostanie otwarty Kreator reguł. Po wybraniu karty **zautomatyzowanej odpowiedzi** zobaczysz sekcję o nazwie **Automatyzacja zdarzeń**, w której zostaną wyświetlone reguły automatyzacji, które są aktualnie stosowane dla tej reguły. Możesz wybrać istniejącą regułę automatyzacji do edycji lub kliknąć przycisk **Dodaj nową** , aby utworzyć nową.

    Należy zauważyć, że po utworzeniu reguły automatyzacji w tym miejscu panel **tworzenia nowej reguły automatyzacji** pokazuje warunek **reguły analizy** jako niedostępny, ponieważ ta reguła jest już ustawiona do zastosowania tylko do reguły analizy, którą edytujesz w kreatorze. Wszystkie inne opcje konfiguracji są nadal dostępne dla Ciebie.

- **Blok zdarzeń**

    Możesz również utworzyć regułę automatyzacji z bloku **incydenty** , aby odpowiedzieć na pojedyncze, cykliczne zdarzenie. Jest to przydatne podczas tworzenia [reguły pomijania](#incident-suppression) w celu automatycznego zamykania zdarzeń "szumów". Wybierz zdarzenie z kolejki, a następnie kliknij pozycję **Utwórz regułę automatyzacji** z górnego menu.

    Zobaczysz, że panel **Utwórz nową regułę automatyzacji** zakończył wszystkie pola z wartościami ze zdarzenia. Nazwa reguły jest taka sama jak nazwa zdarzenia, ma zastosowanie do reguły analizy, która wygenerowała zdarzenie, i używa wszystkich dostępnych jednostek w zdarzeniu jako warunków reguły. Sugeruje również akcję pomijania (zamykającą) i sugeruje datę wygaśnięcia reguły. Możesz dodawać lub usuwać warunki i akcje, a także zmieniać datę wygaśnięcia.

## <a name="auditing-automation-rule-activity"></a>Działanie reguły automatyzacji inspekcji

Możesz zainteresować się, co się stało z danym incydentem, i co może być nieukończone z określoną regułą automatyzacji. Masz pełny rekord Chronicles zdarzeń, który jest dostępny dla Ciebie w tabeli *SecurityIncident* w bloku **dzienniki** . Użyj następującego zapytania, aby wyświetlić wszystkie działania reguły automatyzacji:

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób korzystania z reguł usługi Automation do zarządzania kolejką zdarzeń związanych z platformą Azure wskaźnikowego i implementacji pewnej podstawowej automatyzacji obsługi zdarzeń.

- Aby dowiedzieć się więcej na temat zaawansowanych opcji automatyzacji, zobacz [Automatyzowanie odpowiedzi na zagrożenia z elementy PlayBook na platformie Azure](automate-responses-with-playbooks.md).
- Aby uzyskać pomoc w implementacji reguł i elementy PlayBook automatyzacji, zobacz [Samouczek: używanie elementy PlayBook do automatyzowania odpowiedzi na zagrożenia na platformie Azure](tutorial-respond-threats-playbook.md).
