---
title: Konfigurowanie raportów usługi Azure Backup
description: Konfigurowanie i wyświetlanie raportów dla Azure Backup przy użyciu Log Analytics i skoroszytów platformy Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 11893488c59781bb78cf913a30069e920c66bc71
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172460"
---
# <a name="configure-azure-backup-reports"></a>Konfigurowanie raportów usługi Azure Backup

Typowym wymaganiem dla administratorów kopii zapasowych jest uzyskanie wglądu w kopie zapasowe w oparciu o dane, które zajmują długi czas. W przypadku takich rozwiązań należy uwzględnić następujące przypadki:

- Przydzielanie i prognozowanie zużytego magazynu w chmurze.
- Inspekcja kopii zapasowych i przywracania.
- Identyfikowanie kluczowych trendów na różnych poziomach szczegółowości.

Obecnie Azure Backup udostępnia rozwiązanie do raportowania, które korzysta z [dzienników Azure monitor](../azure-monitor/log-query/get-started-portal.md) i [skoroszytów platformy Azure](../azure-monitor/platform/workbooks-overview.md). Te zasoby ułatwiają uzyskiwanie szczegółowych informacji o kopiach zapasowych w całej kopii zapasowej. W tym artykule opisano sposób konfigurowania i wyświetlania raportów Azure Backup.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

- Raporty kopii zapasowych są obsługiwane na maszynach wirtualnych platformy Azure, na maszynach wirtualnych platformy Azure, SAP HANA na maszynach wirtualnych platformy Azure, w Microsoft Azure Recovery Services (MARS) Agent, Microsoft Azure Backup Server (serwera usługi MAB) i System Center Data Protection Manager (DPM). W przypadku kopii zapasowej udziału plików platformy Azure dane są wyświetlane dla wszystkich rekordów utworzonych w dniu lub po 1 czerwca 2020.
- W przypadku kopii zapasowej udziału plików platformy Azure dane na wystąpieniach chronionych nie są obecnie wyświetlane w raportach (wartość domyślna to zero dla wszystkich elementów kopii zapasowej).
- W przypadku obciążeń programu DPM raporty kopii zapasowych są obsługiwane w programie DPM w wersji 5.1.363.0 i nowszych oraz w wersji agent 2.0.9127.0 i nowszych.
- W przypadku obciążeń serwera usługi MAB raporty kopii zapasowych są obsługiwane dla serwera usługi MAB wersji 13.0.415.0 i nowszych oraz do wersji agenta 2.0.9170.0 i nowszych.
- Raporty kopii zapasowych można wyświetlać w ramach wszystkich elementów kopii zapasowych, magazynów, subskrypcji i regionów, o ile ich dane są wysyłane do obszaru roboczego Log Analytics, do którego użytkownik ma dostęp. Aby wyświetlić raporty dla zbioru magazynów, musisz mieć dostęp do czytnika do obszaru roboczego Log Analytics, do którego magazyny wysyłają swoje dane. Nie musisz mieć dostępu do poszczególnych magazynów.
- Jeśli jesteś użytkownikiem [usługi Azure Lighthouse](../lighthouse/index.yml) z delegowanym dostępem do subskrypcji klientów, możesz użyć tych raportów za pomocą usługi Azure Lighthouse do wyświetlania raportów dla wszystkich dzierżawców.
- Obecnie dane można wyświetlać w raportach dotyczących kopii zapasowych w maksymalnie 100 Log Analytics obszarach roboczych (między dzierżawcami).
- Dane dla zadań kopii zapasowej dzienników nie są obecnie wyświetlane w raportach.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="get-started"></a>Wprowadzenie

Wykonaj następujące kroki, aby rozpocząć korzystanie z raportów.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Utwórz obszar roboczy Log Analytics lub Użyj istniejącego

Skonfiguruj co najmniej jeden obszar roboczy Log Analytics do przechowywania danych raportowania kopii zapasowych. Lokalizację i subskrypcję, w której można utworzyć ten obszar roboczy Log Analytics, są niezależne od lokalizacji i subskrypcji, w której istnieją Twoje magazyny.

Aby skonfigurować obszar roboczy Log Analytics, zobacz [tworzenie log Analytics obszaru roboczego w Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Domyślnie dane w obszarze roboczym Log Analytics są przechowywane przez 30 dni. Aby wyświetlić dane przez dłuższy czas, należy zmienić okres przechowywania obszaru roboczego Log Analytics. Aby zmienić okres przechowywania, zobacz [Zarządzanie użyciem i kosztami za pomocą dzienników Azure monitor](../azure-monitor/platform/manage-cost-storage.md).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Skonfiguruj ustawienia diagnostyki dla swoich magazynów

Azure Resource Manager zasobów, takich jak magazyny Recovery Services, rejestruje informacje o operacjach zaplanowanych i operacjach wyzwalanych przez użytkownika jako danych diagnostycznych.

W sekcji monitorowanie magazynu Recovery Services wybierz pozycję **Ustawienia diagnostyczne** i określ cel dla danych diagnostycznych magazynu Recovery Services. Aby dowiedzieć się więcej o korzystaniu z zdarzeń diagnostycznych, zobacz [Używanie ustawień diagnostycznych dla magazynów Recovery Services](./backup-azure-diagnostic-events.md).

![Okienko ustawień diagnostycznych](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup również zawiera wbudowaną definicję Azure Policy, która automatyzuje konfigurację ustawień diagnostycznych dla wszystkich magazynów w danym zakresie. Aby dowiedzieć się, jak korzystać z tych zasad, zobacz [Konfigurowanie ustawień diagnostyki magazynu w odpowiedniej skali](./azure-policy-configure-diagnostics.md).

> [!NOTE]
> Po skonfigurowaniu diagnostyki zakończenie wypychania danych może potrwać do 24 godzin. Gdy dane zaczynają przepływać do obszaru roboczego Log Analytics, dane w raportach mogą nie być natychmiast widoczne, ponieważ dane bieżącego częściowego dnia nie są wyświetlane w raportach. Aby uzyskać więcej informacji, zobacz [konwencje używane w raportach kopii zapasowych](#conventions-used-in-backup-reports). Zalecamy rozpoczęcie wyświetlania raportów dwa dni po skonfigurowaniu magazynów w celu wysyłania danych do Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Wyświetl raporty w Azure Portal

Po skonfigurowaniu magazynów w celu wysyłania danych do Log Analytics Wyświetl raporty dotyczące kopii zapasowych, przechodząc do okienka dowolnego magazynu i wybierając pozycję **raporty kopii zapasowych**.

![Pulpit nawigacyjny magazynu](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Wybierz ten link, aby otworzyć skoroszyt raportu kopii zapasowej.

> [!NOTE]
>
> - Obecnie początkowe ładowanie raportu może potrwać do 1 minuty.
> - Magazyn Recovery Services jest tylko punktem wejścia dla raportów kopii zapasowych. Po otwarciu skoroszytu raportu z kopii zapasowej w okienku magazynu wybierz odpowiedni zestaw obszarów roboczych Log Analytics, aby wyświetlić dane zagregowane we wszystkich magazynach.

Raport zawiera różne karty:

##### <a name="summary"></a>Podsumowanie

Użyj tej karty, aby uzyskać ogólne omówienie funkcji tworzenia kopii zapasowych. Możesz uzyskać szybki wgląd w łączną liczbę elementów kopii zapasowych, łączną ilość używanych magazynów w chmurze, liczbę chronionych wystąpień oraz Częstotliwość powodzeń zadań dla każdego typu obciążenia. Aby uzyskać szczegółowe informacje na temat określonego typu artefaktu kopii zapasowej, przejdź do odpowiedniej karty.

   ![Karta Summary (Podsumowanie)](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>Elementy kopii zapasowej

Ta karta służy do wyświetlania informacji i trendów w magazynie w chmurze zużywanych na poziomie elementu kopii zapasowej. Na przykład jeśli używasz SQL w kopii zapasowej maszyny wirtualnej platformy Azure, możesz zobaczyć magazyn w chmurze zużyty dla każdej bazy danych SQL, której kopia zapasowa jest tworzona. Możesz również wyświetlić dane dotyczące elementów kopii zapasowej określonego stanu ochrony. Na przykład wybranie kafelka **zatrzymana ochrona** w górnej części karty filtruje wszystkie widżety poniżej, aby wyświetlić dane tylko dla elementów kopii zapasowych w stanie zatrzymania ochrony.

   ![Karta elementy kopii zapasowej](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>Użycie

Za pomocą tej karty można wyświetlać kluczowe parametry rozliczania dla kopii zapasowych. Informacje wyświetlane na tej karcie znajdują się na poziomie jednostki rozliczeń (chronionych kontenerów). Na przykład w przypadku tworzenia kopii zapasowej serwera programu DPM na platformie Azure można wyświetlić trend chronionych wystąpień i magazynu w chmurze zużytego dla serwera programu DPM. Podobnie, jeśli używasz SQL w Azure Backup lub SAP HANA w Azure Backup, na tej karcie znajdują się informacje dotyczące użycia na poziomie maszyny wirtualnej, w której znajdują się te bazy danych.

   ![Karta Użycie](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> W przypadku obciążeń programu DPM użytkownicy mogą zobaczyć niewielką różnicę (kolejność 20 MB na serwer DPM) między wartościami użycia wyświetlanymi w raportach w porównaniu do zagregowanej wartości użycia, jak pokazano na karcie **Przegląd** magazynu Recovery Services. Różnica polega na tym, że każdy serwer DPM zarejestrowany dla kopii zapasowej ma skojarzone źródło danych "Metadata", które nie jest uwzględniane jako artefakt na potrzeby raportowania.

##### <a name="jobs"></a>Stanowiska

Ta karta umożliwia wyświetlanie długotrwałych trendów dotyczących zadań, takich jak liczba zadań zakończonych niepowodzeniem dziennie i Najczęstsze przyczyny niepowodzenia zadania. Te informacje można wyświetlić zarówno na poziomie zagregowanym, jak i na poziomie elementu kopii zapasowej. Wybierz konkretny element kopii zapasowej w siatce, aby wyświetlić szczegółowe informacje na temat każdego zadania, które zostało wyzwolone dla tego elementu kopii zapasowej w wybranym zakresie czasu.

   ![Karta zadania](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>Zasady

Ta karta umożliwia wyświetlenie informacji na temat wszystkich aktywnych zasad, takich jak liczba skojarzonych elementów i łączny magazyn w chmurze zużyty przez elementy, których kopia zapasowa jest wykonywana w ramach danej zasady. Wybierz konkretne zasady, aby wyświetlić informacje dotyczące wszystkich skojarzonych z nią elementów kopii zapasowej.

   ![Karta zasady](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>Optymalizacja

Skorzystaj z tej karty, aby uzyskać wgląd w potencjalne możliwości optymalizacji kosztów dla kopii zapasowych. Poniżej przedstawiono scenariusze, dla których karta Optymalizacja obecnie udostępnia szczegółowe informacje:

###### <a name="inactive-resources"></a>Zasoby nieaktywne

Korzystając z tego widoku, można zidentyfikować te elementy kopii zapasowej, które nie miały pomyślnej kopii zapasowej przez znaczny czas. Może to oznaczać, że maszyna źródłowa, której kopia zapasowa jest tworzona, już nie istnieje (i w efekcie powoduje utworzenie kopii zapasowej) lub występuje problem z maszyną uniemożliwiającą niezawodne wykonywanie kopii zapasowych.

Aby wyświetlić zasoby nieaktywne, przejdź do karty **Optymalizacja** i wybierz kafelek **zasoby nieaktywne** . Wybierz ten kafelek wyświetla siatkę zawierającą szczegóły wszystkich nieaktywnych zasobów istniejących w wybranym zakresie. Domyślnie siatka pokazuje elementy, które nie mają punktu odzyskiwania w ciągu ostatnich siedmiu dni. Aby znaleźć nieaktywne zasoby dla innego przedziału czasu, można dostosować filtr **zakresu czasu** w górnej części karty.

Po zidentyfikowaniu nieaktywnego zasobu można dokładniej zbadać ten problem, przechodząc do pulpitu nawigacyjnego elementu kopii zapasowej lub z okienka zasobów platformy Azure dla tego zasobu (o ile ma to zastosowanie). W zależności od danego scenariusza można wybrać opcję zatrzymania tworzenia kopii zapasowej maszyny (jeśli nie istnieje) i usunąć niepotrzebne kopie zapasowe, co powoduje zaoszczędzenie kosztów, lub rozwiązać problemy z maszyną, aby zapewnić niezawodne wykonywanie kopii zapasowych.

![Optymalizowanie karty — zasoby nieaktywne](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>Elementy kopii zapasowych z długim czasem przechowywania

Korzystając z tego widoku, można zidentyfikować te elementy, których kopie zapasowe są przechowywane przez dłuższy czas, niż jest to wymagane przez organizację.

Wybranie kafelka **Optymalizacja zasad** , a następnie kafelka **Optymalizacja przechowywania** wyświetla siatkę zawierającą wszystkie elementy kopii zapasowej, dla których przechowywanie dziennego, cotygodniowego, miesięcznego lub rocznego punktu przechowywania (RP) jest większe niż określona wartość. Domyślnie Siatka wyświetla wszystkie elementy kopii zapasowej w wybranym zakresie. Można użyć filtrów dziennie, co tydzień, co miesiąc i coroczne przechowywanie RP, aby dodatkowo odfiltrować siatkę i zidentyfikować te elementy, dla których może zostać zredukowane oszczędności, aby zaoszczędzić na kosztach magazynowania kopii zapasowych.

W przypadku obciążeń bazy danych, takich jak SQL i SAP HANA, okresy przechowywania wyświetlane w siatce odpowiadają okresom przechowywania pełnych punktów kopii zapasowych, a nie z różnicowymi punktami kopii zapasowych. Ta sama zasada dotyczy również filtrów przechowywania.  

![Optymalizacja optymalizacji przechowywania kart](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>Bazy danych skonfigurowane do codziennego wykonywania pełnej kopii zapasowej

Korzystając z tego widoku, można identyfikować obciążenia bazy danych skonfigurowane do codziennej pełnej kopii zapasowej. Często użycie codziennej różnicowej kopii zapasowej wraz z tygodniową pełną kopią zapasową jest tańsze.

Po wybraniu kafelka **optymalizacje zasad** , a następnie kafelka **Optymalizacja harmonogramu kopii zapasowych** , zostanie wyświetlona siatka zawierająca wszystkie bazy danych z codziennymi zasadami pełnej kopii zapasowej. Możesz przejść do określonego elementu kopii zapasowej i zmodyfikować zasady, aby używać różnicowej kopii zapasowej codziennej kopii zapasowej.

Filtr **Typ zarządzania kopiami zapasowymi** w górnej części karty powinien zawierać elementy **SQL na maszynie wirtualnej platformy Azure** i **SAP HANA na maszynie wirtualnej platformy Azure** , aby siatka mogła wyświetlić obciążenia bazy danych zgodnie z oczekiwaniami.

![Optymalizowanie kart — Optymalizacja harmonogramu tworzenia kopii zapasowych](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

## <a name="export-to-excel"></a>Eksportuj do programu Excel

Wybierz przycisk strzałki w dół w prawym górnym rogu dowolnego elementu widget, takiego jak tabela lub wykres, aby wyeksportować zawartość tego widżetu jako arkusz programu Excel z zastosowaniem istniejących filtrów. Aby wyeksportować więcej wierszy tabeli do programu Excel, można zwiększyć liczbę wierszy wyświetlanych na stronie przy użyciu strzałki listy rozwijanej **wiersze na stronę** w górnej części każdej siatki.

## <a name="pin-to-dashboard"></a>Przypnij do pulpitu nawigacyjnego

Wybierz przycisk pinezki w górnej części każdego widżetu, aby przypiąć widżet do pulpitu nawigacyjnego Azure Portal. Ta funkcja ułatwia tworzenie dostosowanych pulpitów nawigacyjnych, które są dostosowane do wyświetlania najważniejszych informacji, które są potrzebne.

## <a name="cross-tenant-reports"></a>Raporty obejmujące wiele dzierżawców

Jeśli używasz [usługi Azure Lighthouse](../lighthouse/index.yml) z delegowanym dostępem do subskrypcji w wielu środowiskach dzierżawców, możesz użyć domyślnego filtru subskrypcji. Wybierz przycisk Filtr w prawym górnym rogu Azure Portal, aby wybrać wszystkie subskrypcje, dla których chcesz wyświetlić dane. Dzięki temu można wybrać Log Analytics obszary robocze dla dzierżawców w celu wyświetlenia wielodostępnych raportów.

## <a name="conventions-used-in-backup-reports"></a>Konwencje używane w raportach kopii zapasowych

- Filtry działają od lewej do prawej i od góry do dołu na każdej karcie. Oznacza to, że dowolny filtr ma zastosowanie tylko do wszystkich tych elementów widget, które są rozmieszczone z prawej strony filtru lub poniżej tego filtru.
- Wybranie kolorowego kafelka filtruje widżety poniżej kafelka dla rekordów, które odnoszą się do wartości tego kafelka. Na przykład po wybraniu kafelka **zatrzymana ochrona** na karcie **elementy kopii zapasowej** filtry siatki i wykresy poniżej, aby wyświetlić dane dla elementów kopii zapasowej w stanie zatrzymania ochrony.
- Kafelki, które nie są kolorowe, nie są wybierane.
- Dane bieżącego częściowego dnia nie są wyświetlane w raportach. Dlatego w przypadku wybrania wartości **zakres czasu** w **ciągu ostatnich 7 dni**raport przedstawia rekordy z ostatnich siedmiu dni. Bieżący dzień nie jest uwzględniony.
- Raport przedstawia szczegóły zadań (poza zadaniami dziennika), które zostały *wyzwolone* w wybranym przedziale czasu.
- Wartości wyświetlane dla **magazynu w chmurze** i **chronionych wystąpień** są na *końcu* wybranego zakresu czasu.
- Elementy kopii zapasowej wyświetlane w raportach to elementy, które istnieją na *końcu* wybranego zakresu czasu. Elementy kopii zapasowej, które zostały usunięte w środku wybranego zakresu czasu, nie są wyświetlane. Ta sama Konwencja dotyczy również zasad tworzenia kopii zapasowych.

## <a name="query-load-times"></a>Czasy ładowania zapytania

Widżety w raporcie kopii zapasowych są obsługiwane przez zapytania Kusto, które działają w obszarze roboczym Log Analytics użytkownika. Te zapytania zazwyczaj obejmują przetwarzanie dużych ilości danych z wieloma sprzężeniami w celu zapewnienia bogatszego wglądu w szczegółowe dane. W związku z tym Widżety mogą nie ładować się natychmiast, gdy użytkownik wyświetli raporty w ramach dużej ilości kopii zapasowych. Ta tabela zawiera przybliżone oszacowanie czasu, przez który mogą trwać różne widżety, na podstawie liczby elementów kopii zapasowej i zakresu czasu, w którym raport jest wyświetlany.

| **Liczba źródeł danych**                         | **Horyzont czasu** | **Przybliżone czasy ładowania**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 miesiąc          | Kafelki: 5-10 s <br> Siatki: 5-10 s <br> Wykresy: 5-10 s <br> Filtry na poziomie raportu: 5-10 s|
| ~ 5 K                       | 3 miesiące          | Kafelki: 5-10 s <br> Siatki: 5-10 s <br> Wykresy: 5-10 s <br> Filtry na poziomie raportu: 5-10 s|
| ~ 10 K                       | 3 miesiące          | Kafelki: 15-20 s <br> Siatki: 15-20 s <br> Wykresy: 1-2 minut <br> Filtry na poziomie raportu: 25-30 s|
| ~ 15 K                       | 1 miesiąc          | Kafelki: 15-20 s <br> Siatki: 15-20 s <br> Wykresy: 50-60 s <br> Filtry na poziomie raportu: 20-25 s|
| ~ 15 K                       | 3 miesiące          | Kafelki: 20-30 s <br> Siatki: 20-30 s <br> Wykresy: 2-3 minut <br> Filtry na poziomie raportu: 50-60 s |

## <a name="what-happened-to-the-power-bi-reports"></a> Co się stało z raportami usługi Power BI? 

- Wcześniejsza aplikacja Power BI Template for Reporting, która zawiera dane źródłowe z konta usługi Azure Storage, znajduje się na ścieżce przestarzałej. Zalecamy, aby rozpocząć wysyłanie danych diagnostycznych magazynu do Log Analytics, aby wyświetlić raporty.

- Ponadto [schemat wersji 1](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) służący do wysyłania danych diagnostycznych na konto magazynu lub w obszarze roboczym La znajduje się również na ścieżce przestarzałej. Oznacza to, że w przypadku zapisania niestandardowych zapytań lub automatyzacji opartych na schemacie V1 zaleca się zaktualizowanie tych zapytań, aby używały obecnie obsługiwanego schematu v2.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o monitorowaniu i raportowaniu za pomocą Azure Backup](./backup-azure-monitor-alert-faq.md)
