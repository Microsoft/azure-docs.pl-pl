---
title: Konfigurowanie raportów usługi Azure Backup
description: Konfigurowanie i wyświetlanie raportów dla Azure Backup przy użyciu usługi Log Analytics i skoroszytów platformy Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 0f3638e7649fc02f050c575ee621ce9dc237c24f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517270"
---
# <a name="configure-azure-backup-reports"></a>Konfigurowanie raportów usługi Azure Backup

Częstym wymaganiem dla administratorów kopii zapasowych jest uzyskiwanie szczegółowych informacji na temat kopii zapasowych na podstawie danych, które obejmują długi czas. Przypadki użycia takiego rozwiązania obejmują:

- Alokowanie i prognozowanie używanego magazynu w chmurze.
- Inspekcja kopii zapasowych i przywracania.
- Identyfikowanie kluczowych trendów na różnych poziomach szczegółowości.

Obecnie usługa Azure Backup rozwiązanie do raportowania, które używa [dzienników Azure Monitor i](../azure-monitor/logs/log-analytics-tutorial.md) [skoroszytów platformy Azure.](../azure-monitor/visualize/workbooks-overview.md) Te zasoby ułatwiają uzyskiwanie szczegółowych informacji na temat kopii zapasowych w całej kopii zapasowej. W tym artykule wyjaśniono, jak konfigurować i wyświetlać Azure Backup raportów.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

- Raporty kopii zapasowych są obsługiwane dla maszyn wirtualnych platformy Azure, bazy danych SQL na platformie Azure, usługi SAP HANA na maszynach wirtualnych platformy Azure, agenta usługi Microsoft Azure Recovery Services (MARS), serwera Microsoft Azure Backup Server (MABS) i System Center Data Protection Manager (DPM). W przypadku kopii zapasowej udziału plików platformy Azure dane są wyświetlane dla rekordów utworzonych w dniu 1 czerwca 2020 r. lub później.
- W przypadku kopii zapasowej udziału plików platformy Azure dane dotyczące chronionych wystąpień są wyświetlane dla rekordów utworzonych po 1 lutego 2021 r. (wartość domyślna to zero dla starszych rekordów).
- W przypadku obciążeń programu DPM raporty kopii zapasowych są obsługiwane w przypadku programu DPM w wersji 5.1.363.0 lub nowszej oraz agenta w wersji 2.0.9127.0 lub nowszej.
- W przypadku obciążeń USŁUGI MABS raporty dotyczące kopii zapasowych są obsługiwane w przypadku usługi MABS w wersji 13.0.415.0 lub nowszej oraz agenta w wersji 2.0.9170.0 lub nowszej.
- Raporty kopii zapasowej można wyświetlać we wszystkich elementach kopii zapasowej, magazynach, subskrypcjach i regionach, o ile ich dane są wysyłane do obszaru roboczego usługi Log Analytics, do których użytkownik ma dostęp. Aby wyświetlić raporty dla zestawu magazynów, wystarczy mieć dostęp czytelnika do obszaru roboczego usługi Log Analytics, do którego magazyny wysyłają swoje dane. Nie musisz mieć dostępu do poszczególnych magazynów.
- Jeśli jesteś użytkownikiem z [Azure Lighthouse](../lighthouse/index.yml) delegowanym dostępem do subskrypcji klientów, możesz używać tych raportów z usługą Azure Lighthouse do wyświetlania raportów we wszystkich dzierżawach.
- Obecnie dane można wyświetlać w Raporty usługi Backup maksymalnie 100 obszarów roboczych usługi Log Analytics (między dzierżawami).
- Dane dla zadań tworzenia kopii zapasowej dziennika nie są obecnie wyświetlane w raportach.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

Wykonaj następujące kroki, aby rozpocząć korzystanie z raportów.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Tworzenie obszaru roboczego usługi Log Analytics lub używanie istniejącego obszaru roboczego

Skonfiguruj co najmniej jeden obszar roboczy usługi Log Analytics do przechowywania danych raportowania kopii zapasowych. Lokalizacja i subskrypcja, w której można utworzyć ten obszar roboczy usługi Log Analytics, jest niezależna od lokalizacji i subskrypcji, w której istnieją magazyny.

Aby skonfigurować obszar roboczy usługi Log Analytics, zobacz Tworzenie obszaru roboczego [usługi Log Analytics w Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Domyślnie dane w obszarze roboczym usługi Log Analytics są przechowywane przez 30 dni. Aby wyświetlić dane przez dłuższy horyzont czasowy, zmień okres przechowywania obszaru roboczego usługi Log Analytics. Aby zmienić okres przechowywania, zobacz [Manage usage and costs with Azure Monitor logs (Zarządzanie użyciem i kosztami przy użyciu Azure Monitor dziennikach).](../azure-monitor/logs/manage-cost-storage.md)

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Konfigurowanie ustawień diagnostycznych dla magazynów

Azure Resource Manager zasobów, takich jak magazyny usługi Recovery Services, rejestruj informacje o zaplanowanych operacjach i operacjach wyzwalanych przez użytkownika jako dane diagnostyczne.

W sekcji monitorowania magazynu usługi Recovery  Services wybierz pozycję Ustawienia diagnostyczne i określ element docelowy danych diagnostycznych magazynu usługi Recovery Services. Aby dowiedzieć się więcej na temat korzystania ze zdarzeń diagnostycznych, zobacz Używanie ustawień diagnostycznych dla [magazynów usługi Recovery Services.](./backup-azure-diagnostic-events.md)

![Okienko ustawień diagnostycznych](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup udostępnia również wbudowaną definicję Azure Policy, która automatyzuje konfigurację ustawień diagnostycznych dla wszystkich magazynów w danym zakresie. Aby dowiedzieć się, jak używać tych zasad, zobacz [Konfigurowanie ustawień diagnostyki magazynu na dużą skalę.](./azure-policy-configure-diagnostics.md)

> [!NOTE]
> Po skonfigurowaniu diagnostyki początkowe wypchanie danych może potrwać do 24 godzin. Gdy dane zaczną przepływać do obszaru roboczego usługi Log Analytics, dane mogą nie być widoczne w raportach natychmiast, ponieważ dane z bieżącego częściowego dnia nie są wyświetlane w raportach. Aby uzyskać więcej informacji, zobacz [Konwencje używane w raportach kopii zapasowych.](#conventions-used-in-backup-reports) Zalecamy rozpoczęcie wyświetlania raportów dwa dni po skonfigurowaniu magazynów do wysyłania danych do usługi Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Wyświetlanie raportów w Azure Portal

Po skonfigurowaniu magazynów do wysyłania danych do usługi Log Analytics wyświetl raporty kopii zapasowej, przechodząc do okienka dowolnego magazynu i wybierając pozycję **Raporty usługi Backup**.

![Pulpit nawigacyjny magazynu](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Wybierz ten link, aby otworzyć skoroszyt raportu kopii zapasowej.

> [!NOTE]
>
> - Obecnie początkowe ładowanie raportu może potrwać do 1 minuty.
> - Magazyn usługi Recovery Services jest tylko punktem wejścia dla raportów kopii zapasowych. Gdy skoroszyt raportu kopii zapasowej zostanie otwarty w okienku magazynu, wybierz odpowiedni zestaw obszarów roboczych usługi Log Analytics, aby wyświetlić dane zagregowane we wszystkich magazynach.

Raport zawiera różne karty:

##### <a name="summary"></a>Podsumowanie

Użyj tej karty, aby uzyskać ogólne omówienie majątku kopii zapasowych. Możesz szybko sprawdzić łączną liczbę elementów kopii zapasowej, łączną ilość zużytego magazynu w chmurze, liczbę chronionych wystąpień i wskaźnik powodzenia zadań na typ obciążenia. Aby uzyskać bardziej szczegółowe informacje o określonym typie artefaktu kopii zapasowej, przejdź do odpowiednich kart.

   ![Karta Summary (Podsumowanie)](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>Elementy kopii zapasowej

Ta karta umożliwia wyświetlanie informacji i trendów dotyczących używanego magazynu w chmurze na poziomie elementu kopii zapasowej. Jeśli na przykład używasz bazy danych SQL w kopii zapasowej maszyny wirtualnej platformy Azure, możesz zobaczyć magazyn w chmurze zużyty dla każdej bazy danych SQL, dla których jest dawana kopia zapasowa. Można również wybrać wyświetlanie danych elementów kopii zapasowej o określonym stanie ochrony. Na przykład wybranie kafelka **Zatrzymana** ochrona w górnej części karty filtruje wszystkie widżety poniżej, aby wyświetlić tylko dane dla elementów kopii zapasowej w stanie Zatrzymana ochrona.

   ![Karta Elementy kopii zapasowej](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>Użycie

Użyj tej karty, aby wyświetlić kluczowe parametry rozliczeniowe dla kopii zapasowych. Informacje wyświetlane na tej karcie są na poziomie jednostki rozliczeniowej (chronionego kontenera). Na przykład jeśli kopię zapasową serwera programu DPM jest dawana na platformie Azure, można wyświetlić trend wystąpień chronionych i magazynu w chmurze używanego przez serwer programu DPM. Podobnie jeśli używasz języka SQL w programie Azure Backup lub SAP HANA w programie Azure Backup, ta karta zawiera informacje dotyczące użycia na poziomie maszyny wirtualnej, na której znajdują się te bazy danych.

   ![Karta Użycie](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> W przypadku obciążeń programu DPM użytkownicy mogą zobaczyć niewielką różnicę (o kolejności 20 MB na serwer PROGRAMU DPM) między wartościami użycia  wyświetlanymi w raportach w porównaniu z zagregowaną wartością użycia, jak pokazano na karcie Przegląd magazynu usługi Recovery Services. Ta różnica wynika z faktu, że każdy serwer DPM zarejestrowany do tworzenia kopii zapasowej ma skojarzone źródło danych "metadata", które nie jest ujmowane jako artefakt do raportowania.

##### <a name="jobs"></a>Stanowiska

Ta karta służy do wyświetlania długotrwałych trendów zadań, takich jak liczba nieudanych zadań dziennie i najważniejsze przyczyny niepowodzenia zadania. Te informacje można wyświetlić zarówno na poziomie agregacji, jak i na poziomie elementu kopii zapasowej. Wybierz konkretny element Kopii zapasowej w siatce, aby wyświetlić szczegółowe informacje dotyczące każdego zadania, które zostało wyzwolone dla tego elementu kopii zapasowej w wybranym zakresie czasu.

   ![Karta Zadania](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>Zasady

Ta karta służy do wyświetlania informacji dotyczących wszystkich aktywnych zasad, takich jak liczba skojarzonych elementów i łączna ilość miejsca w chmurze używanego przez elementy, których kopię zapasową dano w ramach danych zasad. Wybierz konkretne zasady, aby wyświetlić informacje o poszczególnych skojarzonych z nimi elementach kopii zapasowej.

   ![Karta Zasady](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>Optymalizacja

Użyj tej karty, aby uzyskać wgląd w potencjalne możliwości optymalizacji kosztów dla kopii zapasowych. Poniżej przedstawiono scenariusze, dla których karta Optymalizacja obecnie udostępnia szczegółowe informacje:

###### <a name="inactive-resources"></a>Nieaktywne zasoby

Korzystając z tego widoku, można zidentyfikować te elementy kopii zapasowej, które nie miały pomyślnej kopii zapasowej przez dłuższy czas. Może to oznaczać, że maszyna bazowa, dla których jest dawana kopia zapasowa, już nie istnieje (i dlatego tworzenie kopii zapasowych zakończyło się niepowodzeniem) lub występuje problem z maszyną, która uniemożliwia niezawodne wykonywanie kopii zapasowych.

Aby wyświetlić nieaktywne zasoby, przejdź do karty **Optymalizacja** i wybierz **kafelek Nieaktywne** zasoby. Wybranie tego kafelka powoduje wyświetlenie siatki zawierającej szczegóły wszystkich nieaktywnych zasobów, które istnieją w wybranym zakresie. Domyślnie siatka przedstawia elementy, które nie mają punktu odzyskiwania w ciągu ostatnich siedmiu dni. Aby znaleźć nieaktywne zasoby dla innego zakresu czasu, możesz dostosować filtr **Zakres** czasu w górnej części karty.

Po zidentyfikowaniu nieaktywnego zasobu możesz dokładniej zbadać problem, przechodząc do pulpitu nawigacyjnego elementu kopii zapasowej lub okienka zasobów platformy Azure dla tego zasobu (wszędzie tam, gdzie ma to zastosowanie). W zależności od scenariusza można zatrzymać tworzenie kopii zapasowej maszyny (jeśli już nie istnieje) i usunąć niepotrzebne kopie zapasowe, co pozwala zaoszczędzić koszty, lub rozwiązać problemy na maszynie, aby zapewnić niezawodne wykonywanie kopii zapasowych.

![Karta Optymalizacja — nieaktywne zasoby](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>Elementy kopii zapasowych z długim czasem przechowywania

Korzystając z tego widoku, można zidentyfikować te elementy, które mają kopie zapasowe przechowywane przez dłuższy czas niż jest to wymagane przez organizację.

Wybranie **kafelka Optymalizacje** zasad, a następnie kafelka Optymalizacje przechowywania powoduje wyświetlenie siatki zawierającej wszystkie elementy kopii zapasowej, dla których przechowywanie dziennego, tygodniowego, miesięcznego lub miesięcznego punktu przechowywania jest większe niż określona wartość.  Domyślnie siatka wyświetla wszystkie elementy kopii zapasowej w wybranym zakresie. Możesz użyć filtrów dla dziennego, tygodniowego, miesięcznego i miesięcznego przechowywania jednostki magazynowej, aby dalej filtrować siatkę i identyfikować elementy, dla których przechowywanie może potencjalnie zostać ograniczone, aby zaoszczędzić na kosztach magazynu kopii zapasowych.

W przypadku obciążeń baz danych, takich jak SQL i SAP HANA, okresy przechowywania wyświetlane w siatce odpowiadają okresom przechowywania pełnych punktów kopii zapasowej, a nie różnicowym punktom kopii zapasowej. To samo dotyczy również filtrów przechowywania.  

![Karta Optymalizacja — Optymalizacje przechowywania](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>Bazy danych skonfigurowane do codziennego wykonywania pełnej kopii zapasowej

Za pomocą tego widoku można zidentyfikować obciążenia bazy danych, które zostały skonfigurowane do codziennego tworzenia pełnej kopii zapasowej. Często używanie codziennej różnicowej kopii zapasowej wraz z cotygodniową pełną kopią zapasową jest bardziej ekonomiczne.

Wybranie **kafelka Optymalizacje zasad,** a następnie kafelka **Optymalizacje** harmonogramu tworzenia kopii zapasowych powoduje wyświetlenie siatki zawierającej wszystkie bazy danych z dziennymi pełnymi zasadami tworzenia kopii zapasowych. Możesz przejść do określonego elementu kopii zapasowej i zmodyfikować zasady, aby używać codziennej różnicowej kopii zapasowej z cotygodniową pełną kopią zapasową.

Filtr **Typ zarządzania kopiami** zapasowymi w górnej części karty powinien mieć wybrane elementy SQL na maszynie wirtualnej platformy **Azure** i pozycję SAP HANA na maszynie wirtualnej platformy **Azure, aby** siatka mogła wyświetlać obciążenia bazy danych zgodnie z oczekiwaniami.

![Karta Optymalizacja — optymalizacje harmonogramu tworzenia kopii zapasowych](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

###### <a name="policy-adherence"></a>Zgodność z zasadami

Za pomocą tej karty można określić, czy wszystkie wystąpienia kopii zapasowych miały co najmniej jedną pomyślną kopię zapasową każdego dnia. W przypadku elementów z cotygodniową zasadą tworzenia kopii zapasowej można użyć tej karty, aby określić, czy wszystkie wystąpienia kopii zapasowej miały co najmniej jedną pomyślną kopię zapasową w tygodniu.

Dostępne są dwa typy widoków zgodności zasad:

* **Zgodność zasad według okresu:** korzystając z tego widoku, można określić, ile elementów miało co najmniej jedną pomyślną kopię zapasową w danym dniu, a ile elementów nie miało pomyślnej kopii zapasowej w danym dniu. Możesz kliknąć wiersz, aby wyświetlić szczegóły wszystkich zadań tworzenia kopii zapasowej, które zostały wyzwolone w wybranym dniu. Należy pamiętać, że w przypadku zwiększenia zakresu czasu do większej wartości, takiej jak ostatnie 60 dni, siatka jest renderowana w widoku tygodniowym i wyświetla liczbę wszystkich elementów, które miały co najmniej jedną pomyślną kopię zapasową każdego dnia w danym tygodniu. Podobnie istnieje widok miesięczny dla większych zakresów czasu.

W przypadku elementów, których kopie zapasowe są co tydzień, ta siatka ułatwia zidentyfikowanie wszystkich elementów, które w danym tygodniu miały co najmniej jedną pomyślną kopię zapasową. W przypadku większego zakresu czasu, takiego jak ostatnie 120 dni, siatka jest renderowana w widoku miesięcznym i wyświetla liczbę wszystkich elementów, które miały co najmniej jedną pomyślną kopię zapasową w każdym tygodniu w danym miesiącu. Zapoznaj [się z konwencjami używanymi w Raporty usługi Backup,](#conventions-used-in-backup-reports) aby uzyskać więcej informacji na temat widoków dziennych, cotygodniowych i miesięcznych.

![Przestrzeganie zasad według okresu](./media/backup-azure-configure-backup-reports/policy-adherence-by-time-period.png)

* **Zgodność zasad według wystąpienia kopii zapasowej:** przy użyciu tego widoku można określić szczegóły zgodności zasad na poziomie wystąpienia kopii zapasowej. Komórka, która jest zielona, oznacza, że wystąpienie kopii zapasowej miało co najmniej jedną pomyślną kopię zapasową w danym dniu. Czerwona komórka oznacza, że wystąpienie kopii zapasowej nie ma nawet jednej pomyślnej kopii zapasowej w danym dniu. Agregacje codziennie, co tydzień i co miesiąc są zgodne z zachowaniem widoku Zgodność zasad według okresu. Możesz kliknąć dowolny wiersz, aby wyświetlić wszystkie zadania tworzenia kopii zapasowej w danym wystąpieniu kopii zapasowej w wybranym zakresie czasu.

![Zgodność zasad według wystąpienia kopii zapasowej](./media/backup-azure-configure-backup-reports/policy-adherence-by-backup-instance.png)

###### <a name="email-azure-backup-reports"></a>Wysyłanie raportów Azure Backup e-mail

Funkcja Raport **e-mail** dostępna w Raporty usługi Backup umożliwia tworzenie zautomatyzowanych zadań w celu otrzymywania okresowych raportów za pośrednictwem poczty e-mail. Ta funkcja działa przez wdrożenie aplikacji logiki w środowisku platformy Azure, która wysyła zapytania o dane z wybranych obszarów roboczych usługi Log Analytics (LA) na podstawie udostępnionych danych wejściowych.

Po utworzeniu aplikacji logiki należy autoryzować połączenia do dzienników Azure Monitor i usługi Office 365. W tym celu przejdź **do Logic Apps** w Azure Portal i wyszukaj nazwę utworzonego zadania. Wybranie elementu menu Połączenia interfejsu **API** powoduje otwarcie listy połączeń interfejsu API, które należy autoryzować. [Dowiedz się więcej na temat konfigurowania wiadomości e-mail i rozwiązywania problemów.](backup-reports-email.md)

###### <a name="customize-azure-backup-reports"></a>Dostosowywanie Azure Backup raportów

Raporty usługi Backup używa [funkcji systemowych w Azure Monitor dziennikach programu](backup-reports-system-functions.md). Te funkcje działają na danych w pierwotnych tabelach usługi Azure Backup w la i zwracają sformatowane dane, które pomagają łatwo pobierać informacje o wszystkich jednostkach związanych z kopiami zapasowymi przy użyciu prostych zapytań. 

Aby utworzyć własne skoroszyty raportowania przy użyciu usługi Raporty usługi Backup jako bazy,  możesz przejść do strony Raporty usługi Backup, kliknąć pozycję Edytuj w górnej części raportu i wyświetlić/edytować zapytania używane w raportach. Zapoznaj się z [dokumentacją skoroszytów platformy Azure,](../azure-monitor/visualize/workbooks-overview.md) aby dowiedzieć się więcej na temat tworzenia raportów niestandardowych. 

## <a name="export-to-excel"></a>Eksportuj do programu Excel

Wybierz przycisk strzałki w dół w prawym górnym rogu dowolnego widżetu, takiego jak tabela lub wykres, aby wyeksportować zawartość tego widżetu jako arkusz programu Excel w stanie, w który zastosowano istniejące filtry. Aby wyeksportować więcej wierszy tabeli do programu Excel, możesz zwiększyć liczbę wierszy wyświetlanych na stronie przy użyciu strzałki listy rozwijanej **Wiersze** na stronę w górnej części każdej siatki.

## <a name="pin-to-dashboard"></a>Przypnij do pulpitu nawigacyjnego

Wybierz przycisk przypinania w górnej części każdego widżetu, aby przypiąć widżet do pulpitu Azure Portal nawigacyjnego. Ta funkcja ułatwia tworzenie dostosowanych pulpitów nawigacyjnych dostosowanych do wyświetlania najważniejszych potrzebnych informacji.

## <a name="cross-tenant-reports"></a>Raporty między dzierżawami

Jeśli używasz subskrypcji [Azure Lighthouse](../lighthouse/index.yml) delegowanym dostępem do subskrypcji w wielu środowiskach dzierżawy, możesz użyć domyślnego filtru subskrypcji. Wybierz przycisk filtru w prawym górnym rogu okna Azure Portal, aby wybrać wszystkie subskrypcje, dla których chcesz wyświetlić dane. Umożliwia to wybranie obszarów roboczych usługi Log Analytics w dzierżawach w celu wyświetlenia wielodostępnych raportów.

## <a name="conventions-used-in-backup-reports"></a>Konwencje używane w raportach kopii zapasowych

- Filtry działają od lewej do prawej i od góry do dołu na każdej karcie. Oznacza to, że każdy filtr ma zastosowanie tylko do wszystkich widżetów, które znajdują się po prawej stronie tego filtru lub poniżej tego filtru.
- Wybranie kolorowego kafelka filtruje widżety poniżej kafelka pod rekordami, które odnoszą się do wartości tego kafelka. Na przykład wybranie kafelka **Zatrzymana** ochrona na karcie Elementy kopii zapasowej filtruje siatki i wykresy poniżej, aby wyświetlić dane dla elementów kopii zapasowej w stanie Zatrzymana ochrona. 
- Kafelków, które nie mają koloru, nie można wybierać.
- Dane bieżącego częściowego dnia nie są wyświetlane w raportach. Dlatego gdy wybraną wartością zakres **czasu** jest **Ostatnie 7 dni,** raport pokazuje rekordy z ostatnich siedmiu ukończonych dni. Bieżący dzień nie jest uwzględniony.
- Raport przedstawia szczegóły zadań (z wyjątkiem zadań dziennika), które zostały *wyzwolone* w wybranym zakresie czasu.
- Wartości wyświetlane dla usług **Cloud Storage** i  **Protected Instances** znajdują się na końcu wybranego zakresu czasu.
- Elementy kopii zapasowej wyświetlane w raportach to te elementy, które istnieją *na* końcu wybranego zakresu czasu. Elementy kopii zapasowej, które zostały usunięte w trakcie wybranego zakresu czasu, nie są wyświetlane. Ta sama konwencja dotyczy również zasad tworzenia kopii zapasowych.
- Jeśli wybrany zakres czasu obejmuje okres krótszy niż 30 dni, wykresy są renderowane w widoku dziennym, w którym codziennie istnieje jeden punkt danych. Jeśli zakres czasu obejmuje okres dłuższy niż 30 dni i krótszy (lub równy) 90 dni, wykresy są renderowane w widoku tygodniowym. W przypadku większych zakresów czasu wykresy są renderowane w widoku miesięcznym. Agregowanie danych co tydzień lub co miesiąc pomaga w lepszej wydajności zapytań i łatwiejszej czytelności danych na wykresach.
- Siatki zgodności zasad również mają podobną logikę agregacji, jak opisano powyżej. Istnieje jednak kilka drobnych różnic. Pierwsza różnica polega na tym, że w przypadku elementów z zasadami tworzenia kopii zapasowych cotygodniowych nie ma widoku dziennego (dostępne są tylko widoki tygodniowe i miesięczne). Ponadto w siatkach elementów z cotygodniową zasadą tworzenia kopii zapasowej "miesiąc" jest traktowany jako okres 4 tygodni (28 dni), a nie 30 dni, aby wyeliminować częściowe tygodnie z uwzględnieniem.

## <a name="query-load-times"></a>Czasy ładowania zapytań

Widżety w raporcie kopii zapasowej są obsługiwane przez zapytania usługi Kusto, które są uruchamiane w obszarach roboczych usługi Log Analytics użytkownika. Te zapytania zwykle obejmują przetwarzanie dużych ilości danych z wieloma sprzężeniami w celu umożliwienia bardziej szczegółowych informacji. W związku z tym widżety mogą nie ładować się natychmiast, gdy użytkownik wyświetla raporty dotyczące dużej ilości kopii zapasowych. Ta tabela zawiera przybliżony szacowany czas ładowania różnych widżetów na podstawie liczby elementów kopii zapasowej i zakresu czasu, dla którego raport jest przeglądany.

| **# Źródła danych**                         | **Horyzont czasowy** | **Przybliżone czasy ładowania**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| Ok. 5 tys.                       | 1 miesiąc          | Kafelki: 5–10 s <br> Siatki: 5–10 s <br> Wykresy: 5–10 s <br> Filtry na poziomie raportu: 5–10 s|
| Ok. 5 tys.                       | 3 miesiące          | Kafelki: 5–10 s <br> Siatki: 5–10 s <br> Wykresy: 5–10 s <br> Filtry na poziomie raportu: 5–10 s|
| Ok. 10 tys.                       | 3 miesiące          | Kafelki: 15–20 s <br> Siatki: 15–20 s <br> Wykresy: 1–2 min <br> Filtry na poziomie raportu: 25–30 s|
| Ok. 15 tys.                       | 1 miesiąc          | Kafelki: 15–20 s <br> Siatki: 15–20 s <br> Wykresy: 50–60 s <br> Filtry na poziomie raportu: 20–25 sek.|
| Ok. 15 tys.                       | 3 miesiące          | Kafelki: 20–30 s <br> Siatki: 20–30 s <br> Wykresy: 2–3 min <br> Filtry na poziomie raportu: 50–60 s |

## <a name="what-happened-to-the-power-bi-reports"></a> Co się stało z raportami usługi Power BI? 

- Wcześniejsza Power BI szablonu do raportowania, która pozyowała dane z konta usługi Azure Storage, znajduje się na ścieżce cofania przetwarzania. Zalecamy rozpoczęcie wysyłania danych diagnostycznych magazynu do usługi Log Analytics w celu wyświetlenia raportów.

- Ponadto schemat w [wersji 1](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) wysyłania danych diagnostycznych do konta magazynu lub obszaru roboczego usługi LA jest również w ścieżce cofania. Oznacza to, że jeśli zostały napisane jakiekolwiek niestandardowe zapytania lub automatyzacje oparte na schemacie V1, zaleca się zaktualizowanie tych zapytań w celu użycia aktualnie obsługiwanego schematu w wersji 2.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o monitorowaniu i raportowaniu za pomocą Azure Backup](./backup-azure-monitor-alert-faq.yml)