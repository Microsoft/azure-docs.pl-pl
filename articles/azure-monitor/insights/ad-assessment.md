---
title: Zoptymalizuj Środowisko Active Directory przy użyciu Azure Monitor | Microsoft Docs
description: Możesz użyć rozwiązania do sprawdzania kondycji Active Directory, aby ocenić ryzyko i kondycję środowisk w regularnych odstępach czasu.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: bff61ec9dfcb985ea0111ca58bfd58273e1fe432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723252"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Optymalizowanie środowiska usługi Active Directory za pomocą rozwiązania Active Directory Health Check w usłudze Azure Monitor

![Symbol kontroli kondycji usługi AD](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Możesz użyć rozwiązania do sprawdzania kondycji Active Directory, aby ocenić ryzyko i kondycję środowisk serwera w regularnych odstępach czasu. W tym artykule opisano sposób instalowania i używania rozwiązania, dzięki któremu można podejmować działania naprawcze w celu uzyskania potencjalnych problemów.

To rozwiązanie zapewnia priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera. Zalecenia są podzielone na cztery obszary koncentracji uwagi, które ułatwiają szybkie zrozumienie ryzyka i podejmowanie działań.

Zalecenia są oparte na wiedzy i doświadczeniu zdobytym przez inżynierów firmy Microsoft z tysięcy wizyt klientów. Każde zalecenie zawiera wskazówki dotyczące przyczyny problemu oraz sposobu implementacji sugerowanych zmian.

Możesz wybrać obszary koncentracji, które są najważniejsze dla Twojej organizacji, i śledzić postęp w kierunku działania wolnego i zdrowego środowiska.

Po dodaniu rozwiązania i zakończeniu sprawdzania informacje podsumowujące dotyczące obszarów koncentracji są wyświetlane na pulpicie nawigacyjnym **sprawdzania kondycji usługi AD** dla infrastruktury w środowisku. W poniższych sekcjach opisano, jak korzystać z informacji na pulpicie nawigacyjnym **sprawdzania kondycji usługi AD** , gdzie można wyświetlać i podejmować zalecane akcje dla infrastruktury serwera Active Directory.  

![obraz kafelka kontroli kondycji usługi AD](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![obraz pulpitu nawigacyjnego sprawdzania kondycji usługi AD](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Na każdym komputerze, na którym jest zainstalowany agent programu Log Analytics dla systemu Windows (określane również jako Microsoft Monitoring Agent (MMA), Active Directory .NET Framework rozwiązanie do sprawdzania kondycji.  Agent jest używany przez program System Center 2016 — Operations Manager, Operations Manager 2012 R2 i Azure Monitor.
* Rozwiązanie obsługuje kontrolery domeny z systemami Windows Server 2008 i 2008 R2, Windows Server 2012 i 2012 R2, Windows Server 2016 i Windows Server 2019.
* Log Analytics obszar roboczy, aby dodać rozwiązanie Active Directory Sprawdzanie kondycji z witryny Azure Marketplace w Azure Portal. Nie jest wymagana żadna dodatkowa konfiguracja.

  > [!NOTE]
  > Po dodaniu rozwiązania plik AdvisorAssessment.exe zostanie dodany do serwerów z agentami. Dane konfiguracji są odczytywane, a następnie wysyłane do Azure Monitor w chmurze w celu przetworzenia. Logika jest stosowana do odebranych danych, a usługa w chmurze rejestruje dane.
  >
  >

Aby przeprowadzić kontrolę kondycji na kontrolerach domeny należących do domeny, które mają zostać ocenione, każdy kontroler domeny w tej domenie wymaga agenta i łączności do Azure Monitor przy użyciu jednej z następujących obsługiwanych metod:

1. Zainstaluj [agenta log Analytics dla systemu Windows](../agents/agent-windows.md) , Jeśli kontroler domeny nie jest jeszcze monitorowany przez program System Center 2016 — Operations Manager lub Operations Manager 2012 R2.
2. Jeśli jest monitorowana z programem System Center 2016-Operations Manager lub Operations Manager 2012 R2, a grupa zarządzania nie jest zintegrowana z Azure Monitor, kontroler domeny może być wieloadresowy z Azure Monitor do zbierania danych i przekazywania ich do usługi i nadal monitorowany przez Operations Manager.  
3. W przeciwnym razie, jeśli grupa zarządzania Operations Manager jest zintegrowana z usługą, należy dodać kontrolery domeny dla zbierania danych przez usługę zgodnie z instrukcjami w obszarze [Dodawanie komputerów zarządzanych przez agentów](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) po włączeniu rozwiązania w obszarze roboczym.  

Agent na kontrolerze domeny, który raportuje do Operations Manager grupy zarządzania, zbiera dane, przekazuje do przypisanego do niego serwera zarządzania, a następnie jest wysyłany bezpośrednio z serwera zarządzania do Azure Monitor.  Dane nie są zapisywane w Operations Manager bazach danych.  

## <a name="active-directory-health-check-data-collection-details"></a>Szczegóły zbierania danych w rozwiązaniu Active Directory Health Check

Active Directory Sprawdzanie kondycji zbiera dane z następujących źródeł przy użyciu dostępnego agenta:

- Rejestr
- LDAP
- .NET Framework
- Dziennik zdarzeń
- Interfejsy usługi Active Directory (ADSI)
- Windows PowerShell
- Dane pliku
- Instrumentacja zarządzania Windows (WMI)
- Interfejs API narzędzia DCDIAG
- Interfejs API usługi replikacji plików (NTFRS)
- Niestandardowy kod C#

Dane są zbierane na kontrolerze domeny i przekazywane do Azure Monitor co siedem dni.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Opis priorytetyzacji zaleceń

Każda z zaleceń otrzymuje wartość ważenia, która identyfikuje względną ważność zalecenia. Wyświetlane są tylko 10 najważniejszych zaleceń.

### <a name="how-weights-are-calculated"></a>Sposób obliczania wag

Wagi są wartościami zagregowanymi opartymi na trzech kluczowych czynnikach:

* *Prawdopodobieństwo* zidentyfikowania problemu powoduje problemy. Wyższe prawdopodobieństwo jest równe większym ogólnym wynikom zalecenia.
* *Wpływ* problemu w organizacji, jeśli spowoduje to wystąpienie problemu. Wyższy wpływ jest równy większym ogólnym wynikom zalecenia.
* *Nakład pracy* wymagany do wdrożenia zalecenia. Wyższy nakład pracy odpowiada mniejszemu ogólnemu wynikowi zalecenia.

Waga dla każdego zalecenia jest wyrażona jako wartość procentowa łącznego wyniku dostępnego dla każdego obszaru koncentracji uwagi. Na przykład jeśli zalecenie w obszarze koncentracji zabezpieczenia i zgodność ma wynik 5%, implementacja tego zalecenia spowoduje zwiększenie ogólnej oceny zabezpieczeń i zgodności o 5%.

### <a name="focus-areas"></a>Obszary fokusu

**Bezpieczeństwo i zgodność** — w tym obszarze znajdują się zalecenia dotyczące potencjalnych zagrożeń bezpieczeństwa i naruszeń, zasad firmowych oraz wymagań dotyczących zgodności z przepisami technicznymi i prawnymi.

**Dostępność i ciągłość** działania — w tym obszarze przedstawiono zalecenia dotyczące dostępności usług, odporności infrastruktury i ochrony firmy.

**Wydajność i skalowalność** — ten obszar skupiania zawiera zalecenia ułatwiające rozwój infrastruktury IT w organizacji, upewnij się, że środowisko IT spełnia bieżące wymagania dotyczące wydajności i jest w stanie reagować na zmieniające się potrzeby związane z infrastrukturą.

**Uaktualnianie, migracja i wdrażanie** — w tym obszarze fokusu przedstawiono zalecenia ułatwiające uaktualnianie, migrowanie i wdrażanie Active Directory w istniejącej infrastrukturze.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Czy należy dążyć do osiągnięcia wyniku 100% w każdym obszarze fokusu?

Niekoniecznie. Zalecenia są oparte na wiedzy i doświadczeniach zdobytych przez inżynierów firmy Microsoft w tysiącach wizyt klientów. Jednak żadne dwie infrastruktury serwera nie są takie same, a konkretne zalecenia mogą być bardziej lub mniej odpowiednie dla użytkownika. Na przykład niektóre zalecenia dotyczące zabezpieczeń mogą być mniej istotne, jeśli maszyny wirtualne nie są dostępne w Internecie. Niektóre zalecenia dotyczące dostępności mogą być mniej istotne w przypadku usług zapewniających zbieranie i raportowanie danych ad hoc o niskim priorytecie. Problemy, które są ważne dla dorosłych firm, mogą być mniej ważne do uruchomienia. Możesz chcieć określić, które obszary koncentracji są priorytetami, a następnie sprawdzić, jak wyniki zmieniają się wraz z upływem czasu.

Każde zalecenie zawiera wskazówki dotyczące przyczyny tego znaczenia. Należy użyć tych wskazówek, aby sprawdzić, czy wdrożenie zalecenia jest odpowiednie dla Ciebie, ze względu na charakter usług IT i potrzeby biznesowe Twojej organizacji.

## <a name="use-health-check-focus-area-recommendations"></a>Użyj zaleceń dotyczących obszaru fokusu sprawdzania kondycji

Po zainstalowaniu programu można wyświetlić podsumowanie zaleceń przy użyciu kafelka Sprawdzanie kondycji na stronie rozwiązania w Azure Portal.

Wyświetl podsumowanie ocen zgodności dla infrastruktury, a następnie zapoznaj się z zaleceniami.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aby wyświetlić zalecenia dotyczące obszaru koncentracji uwagi i podjąć działania naprawcze

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Na stronie **Przegląd** kliknij kafelek **sprawdzanie kondycji Active Directory** .

2. Na stronie **Sprawdzanie kondycji** Przejrzyj informacje podsumowujące na jednym z bloków obszaru fokusu, a następnie kliknij jeden, aby wyświetlić zalecenia dotyczące tego obszaru.

3. Na dowolnej stronie obszaru fokusu można wyświetlić zalecenia z priorytetami wykonane dla danego środowiska. Kliknij zalecenie w obszarze powiązane **obiekty** , aby wyświetlić szczegóły dotyczące przyczyny tego zalecenia.

    ![obraz przedstawiający zalecenia dotyczące sprawdzania kondycji](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. W **sugerowanych akcjach** można wykonać akcje naprawcze. Gdy element został rozkierowany, w późniejszych wersjach oceny są uwzględniane zalecane akcje i zostanie zwiększony wynik oceny zgodności. Poprawione elementy są wyświetlane jako **obiekty zakończone**.

## <a name="ignore-recommendations"></a>Ignorowanie zaleceń

Jeśli masz zalecenia, które chcesz zignorować, możesz utworzyć plik tekstowy, który będzie używany przez Azure Monitor, aby zapobiec wyświetlaniu zaleceń w wynikach oceny.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Aby zidentyfikować rekomendacje, które zostaną zignorowane

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Użyj następującego zapytania, aby wyświetlić listę zaleceń, których nie powiodło się w przypadku komputerów w danym środowisku.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Oto zrzut ekranu przedstawiający zapytanie dziennika: <

![zalecenia zakończone niepowodzeniem](media/ad-assessment/ad-failed-recommendations.png)

Wybierz zalecenia, które chcesz zignorować. W następnej procedurze użyjesz wartości dla RecommendationId.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Aby utworzyć plik tekstowy IgnoreRecommendations.txt i używać go

1. Utwórz plik o nazwie IgnoreRecommendations.txt.

2. Wklej lub wpisz każdą RecommendationId każdego zalecenia, które Azure Monitor mają być ignorowane w osobnym wierszu, a następnie Zapisz i zamknij plik.

3. Umieść plik w następującym folderze na każdym komputerze, na którym ma Azure Monitor ignorowanie zaleceń.

   * Na komputerach z Microsoft Monitoring Agent (połączone bezpośrednio lub za pomocą Operations Manager)- *dysk_systemowy*: \Program Files\Microsoft monitoring Agent\Agent
   * Na Operations Manager 2012 R2 Management Server- *dysk_systemowy*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Na Operations Manager 2016 Management Server- *dysk_systemowy*: \Program Files\Microsoft System Center 2016 \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Aby sprawdzić, czy zalecenia są ignorowane

Po następnym zaplanowanym zaplanowaniu kondycji domyślnie co siedem dni określone zalecenia są oznaczone jako *ignorowane* i nie będą wyświetlane na pulpicie nawigacyjnym.

1. Aby wyświetlić listę wszystkich ignorowanych zaleceń, można użyć poniższych zapytań dzienników.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Jeśli zdecydujesz się później, aby zobaczyć zignorowane zalecenia, Usuń wszelkie IgnoreRecommendations.txt pliki lub Usuń z nich RecommendationIDs.

## <a name="ad-health-check-solutions-faq"></a>Rozwiązania AD Health Check — często zadawane pytania

*Jakie testy są wykonywane przez rozwiązanie AD Assessment?*

* Następujące zapytanie zawiera opis wszystkich aktualnie wykonanych testów:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Wyniki można następnie wyeksportować do programu Excel w celu dalszej analizy.

*Jak często jest uruchamiane Sprawdzanie kondycji?*

* Sprawdzanie jest przeprowadzane co siedem dni.

*Czy istnieje sposób skonfigurowania, jak często jest przeprowadzane sprawdzanie kondycji?*

* Nie w tej chwili.

*Jeśli po dodaniu rozwiązania do sprawdzania kondycji zostanie odnaleziony inny serwer dla programu, zostanie on sprawdzony*

* Tak, po jego znalezieniu jest on sprawdzany w dniu, co siedem dni.

*Jeśli serwer zostanie zlikwidowany, gdy zostanie usunięty ze sprawdzenia kondycji?*

* Jeśli serwer nie przesyła danych przez 3 tygodnie, zostanie usunięty.

*Jaka jest nazwa procesu, który wykonuje zbieranie danych?*

* AdvisorAssessment.exe

*Jak długo trwa zbieranie danych?*

* Rzeczywista zbieranie danych na serwerze trwa około 1 godziny. Korzystanie z dużej liczby serwerów Active Directory może zająć więcej czasu.

*Czy istnieje sposób skonfigurowania zbierania danych?*

* Nie w tej chwili.

*Dlaczego są wyświetlane tylko 10 najważniejszych zaleceń?*

* Zamiast zapewniać wyczerpującą listę zadań, zalecamy najpierw skupić się na rozwiązaniu zaleceń z priorytetami. Po wprowadzeniu dodatkowych rekomendacji staną się dostępne. Jeśli wolisz wyświetlić listę Szczegółowa, możesz wyświetlić wszystkie zalecenia przy użyciu zapytania dziennika.

*Czy istnieje sposób na ignorowanie rekomendacji?*

* Tak, zobacz sekcję [Ignorowanie rekomendacji](#ignore-recommendations) powyżej.

## <a name="next-steps"></a>Następne kroki

Użyj [zapytań dzienników Azure monitor](../logs/log-query-overview.md) , aby dowiedzieć się, jak analizować szczegółowe dane i zalecenia dotyczące kontroli kondycji usługi AD.

