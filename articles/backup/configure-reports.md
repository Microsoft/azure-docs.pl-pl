---
title: Konfigurowanie raportów usługi Azure Backup
description: Konfigurowanie i wyświetlanie raportów dla usługi Azure Backup przy użyciu usługi Log Analytics i skoroszytów platformy Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: bcc87deb19190149329369ca58f54b45b62b41fe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617819"
---
# <a name="configure-azure-backup-reports"></a>Konfigurowanie raportów usługi Azure Backup

Typowym wymaganiem dla administratorów kopii zapasowych jest uzyskanie szczegółowych informacji na temat kopii zapasowych na podstawie danych, które obejmują długi okres czasu. Przypadki użycia dla takiego rozwiązania obejmują:

 - Przydzielanie i prognozowanie zużywanego magazynu w chmurze.
 - Inspekcja kopii zapasowych i przywracania.
 - Identyfikowanie kluczowych trendów na różnych poziomach szczegółowości.

Obecnie usługa Azure Backup udostępnia rozwiązanie do raportowania, które używa [dzienników usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) i [skoroszytów platformy Azure.](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) Te zasoby pomagają uzyskać szczegółowe informacje na temat kopii zapasowych w całej nieruchomości kopii zapasowych. W tym artykule wyjaśniono, jak skonfigurować i wyświetlić raporty usługi Azure Backup.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

* Raporty kopii zapasowych są obsługiwane dla maszyn wirtualnych platformy Azure, SQL w maszynach wirtualnych platformy Azure, SAP HANA/ASE na maszynach wirtualnych platformy Azure, agenta usług Microsoft Azure Recovery Services (MARS), programu Microsoft Azure Backup Server (MABS) i Menedżera ochrony danych (DPM) centrum systemu.
* W przypadku obciążeń programu DPM raporty kopii zapasowej są obsługiwane dla programu DPM w wersji 5.1.363.0 i wyższej oraz agenta w wersji 2.0.9127.0 i wyższej.
* W przypadku obciążeń mabs raporty kopii zapasowej są obsługiwane dla mabs w wersji 13.0.415.0 i wyższej oraz agenta w wersji 2.0.9170.0 i wyższej.
* Raporty kopii zapasowej mogą być wyświetlane we wszystkich elementach kopii zapasowej, magazynach, subskrypcjach i regionach, o ile ich dane są wysyłane do obszaru roboczego usługi Log Analytics, do których użytkownik ma dostęp. Aby wyświetlić raporty dla zestawu magazynów, wystarczy mieć dostęp czytelnika do obszaru roboczego usługi Log Analytics, do którego przechowalnia wysyłają swoje dane. Nie musisz mieć dostępu do poszczególnych magazynów.
* Jeśli jesteś użytkownikiem [latarni morskiej platformy Azure](https://docs.microsoft.com/azure/lighthouse/) z delegowanym dostępem do subskrypcji klientów, możesz użyć tych raportów za pomocą latarni morskiej platformy Azure, aby wyświetlić raporty we wszystkich dzierżawach.
* Dane dla zadań tworzenia kopii zapasowych dziennika obecnie nie są wyświetlane w raportach.

## <a name="get-started"></a>Rozpoczęcie pracy

Wykonaj następujące kroki, aby rozpocząć korzystanie z raportów.

#### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Utwórz obszar roboczy usługi Log Analytics lub użyj istniejącego

Skonfiguruj co najmniej jeden obszar roboczy usługi Log Analytics do przechowywania danych raportowania kopii zapasowej. Lokalizacja i subskrypcja, w której można utworzyć ten obszar roboczy usługi Log Analytics, jest niezależna od lokalizacji i subskrypcji, w której istnieją magazyny. 

Aby skonfigurować obszar roboczy usługi Log Analytics, zobacz [Tworzenie obszaru roboczego usługi Log Analytics w portalu Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Domyślnie dane w obszarze roboczym usługi Log Analytics są przechowywane przez 30 dni. Aby wyświetlić dane przez dłuższy horyzont czasowy, zmień okres przechowywania obszaru roboczego usługi Log Analytics. Aby zmienić okres przechowywania, zobacz [Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

#### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Konfigurowanie ustawień diagnostycznych dla magazynów

Zasoby usługi Azure Resource Manager, takie jak magazyny usług odzyskiwania, rejestrują informacje o zaplanowanych operacjach i operacjach wyzwalanych przez użytkownika jako dane diagnostyczne. 

W sekcji monitorowania magazynu usług odzyskiwania wybierz **ustawienia diagnostyki** i określ miejsce docelowe dla danych diagnostycznych magazynu usług recovery services. Aby dowiedzieć się więcej na temat używania zdarzeń diagnostycznych, zobacz [Używanie ustawień diagnostyki dla magazynów usług odzyskiwania](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Okienko ustawień diagnostycznych](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Usługa Azure Backup udostępnia również wbudowane zasady platformy Azure, które automatyzują konfigurację ustawień diagnostyki dla wszystkich magazynów w danym zakresie. Aby dowiedzieć się, jak korzystać z tych zasad, zobacz [Konfigurowanie ustawień diagnostyki przechowalni na dużą skalę](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics).

> [!NOTE]
> Po skonfigurowaniu diagnostyki może upłynąć do 24 godzin, aby zakończyć początkowe wypychanie danych. Po rozpoczęciu przepływu danych do obszaru roboczego usługi Log Analytics, może nie widzieć danych w raportach natychmiast, ponieważ dane dla bieżącego dnia częściowego nie jest wyświetlany w raportach. Aby uzyskać więcej informacji, zobacz [Konwencje używane w raportach kopii zapasowej](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports). Zaleca się rozpoczęcie wyświetlania raportów dwa dni po skonfigurowaniu magazynów do wysyłania danych do usługi Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Wyświetlanie raportów w witrynie Azure portal

Po skonfigurowaniu magazynów do wysyłania danych do usługi Log Analytics wyświetl raporty kopii zapasowej, przechodząc do dowolnego okienka magazynu i wybierając pozycję **Raporty kopii zapasowych**.

![Pulpit nawigacyjny przechowalni](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Wybierz to łącze, aby otworzyć skoroszyt raportu kopii zapasowej.

> [!NOTE]
> * Obecnie początkowe obciążenie raportu może potrwać do 1 minuty.
> * Magazyn usług odzyskiwania jest tylko punktem wejścia dla raportów kopii zapasowej. Po otwarciu skoroszytu raportu kopii zapasowej z okienka magazynu wybierz odpowiedni zestaw obszarów roboczych usługi Log Analytics, aby wyświetlić dane zagregowane we wszystkich magazynach.

Raport zawiera różne zakładki:

* **Podsumowanie**: Użyj tej karty, aby uzyskać ogólny przegląd zapasów. Można szybko spojrzeć na całkowitą liczbę elementów kopii zapasowej, całkowita ilość magazynu w chmurze, liczbę chronionych wystąpień i wskaźnik powodzenia zadania na typ obciążenia. Aby uzyskać bardziej szczegółowe informacje na temat określonego typu artefaktu kopii zapasowej, przejdź do odpowiednich kart.

   ![Karta Podsumowanie](./media/backup-azure-configure-backup-reports/summary.png)

* **Elementy kopii zapasowej:** Użyj tej karty, aby wyświetlić informacje i trendy dotyczące magazynu w chmurze używanego na poziomie elementu kopii zapasowej. Na przykład jeśli używasz SQL w kopii zapasowej maszyny Wirtualnej platformy Azure, można zobaczyć magazynu w chmurze używane dla każdej bazy danych SQL, który jest archiwizowane. Można również wybrać wyświetlanie danych dla elementów kopii zapasowej o określonym stanie ochrony. Na przykład wybranie kafelka **Ochrona zatrzymana** u góry karty filtruje wszystkie widżety pod spodem, aby wyświetlić dane tylko dla elementów kopii zapasowej w stanie Wstrzymana ochrona.

   ![Karta Elementy kopii zapasowej](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Użycie:** Ta karta służy do wyświetlania kluczowych parametrów rozliczeń dla kopii zapasowych. Informacje wyświetlane na tej karcie znajdują się na poziomie encji rozliczeniowej (kontener chroniony). Na przykład w przypadku serwera programu DPM, którego kopię zapasową jest na platformie Azure, można wyświetlić trend chronionych wystąpień i magazynu w chmurze używanego dla serwera programu DPM. Podobnie jeśli używasz sql w usłudze Azure Backup lub SAP HANA w usłudze Azure Backup, ta karta zawiera informacje związane z użyciem na poziomie maszyny wirtualnej, w którym te bazy danych są zawarte.

   ![Karta Użycie](./media/backup-azure-configure-backup-reports/usage.png)

* **Zadania:** Ta karta służy do wyświetlania długotrwałych trendów w zadaniach, takich jak liczba zadań, które nie powiodło się dziennie i najważniejsze przyczyny niepowodzenia zadania. Można wyświetlić te informacje zarówno na poziomie zagregowanym, jak i na poziomie elementu kopii zapasowej. Wybierz określony element kopii zapasowej w siatce, aby wyświetlić szczegółowe informacje o każdym zadaniu, które zostało wyzwolone na tym elemencie kopii zapasowej w wybranym zakresie czasu.

   ![Karta Zadania](./media/backup-azure-configure-backup-reports/jobs.png)

* **Zasady:** Ta karta służy do wyświetlania informacji o wszystkich aktywnych zasadach, takich jak liczba skojarzonych elementów i całkowita ilość miejsca w chmurze zużywanego przez elementy kopii zapasowej w ramach danej zasady. Wybierz określoną zasadę, aby wyświetlić informacje o każdym z skojarzonych elementów kopii zapasowej.

   ![Karta Zasady](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Eksportuj do programu Excel

Wybierz przycisk strzałki w dół w prawym górnym rogu dowolnego widżetu, takiego jak tabela lub wykres, aby wyeksportować zawartość tego widżetu jako arkusza programu Excel z zastosowanymi istniejącymi filtrami. Aby wyeksportować więcej wierszy tabeli do programu Excel, można zwiększyć liczbę wierszy wyświetlanych na stronie za pomocą strzałki listy rozwijanej **Wiersze na stronę** u góry każdej siatki.

## <a name="pin-to-dashboard"></a>Przypnij do pulpitu nawigacyjnego

Wybierz przycisk pinezki u góry każdego widżetu, aby przypiąć widżet do pulpitu nawigacyjnego portalu Azure. Ta funkcja ułatwia tworzenie niestandardowych pulpitów nawigacyjnych dostosowanych do wyświetlania najważniejszych potrzebnych informacji.

## <a name="cross-tenant-reports"></a>Raporty między dzierżawcami

Jeśli używasz [latarni morskiej platformy Azure](https://docs.microsoft.com/azure/lighthouse/) z delegowanym dostępem do subskrypcji w wielu środowiskach dzierżawy, możesz użyć domyślnego filtru subskrypcji. Wybierz przycisk filtru w prawym górnym rogu witryny Azure portal, aby wybrać wszystkie subskrypcje, dla których chcesz wyświetlić dane. W ten sposób można wybrać obszarów roboczych usługi Log Analytics w dzierżawach, aby wyświetlić raporty wielodostępne.

## <a name="conventions-used-in-backup-reports"></a>Konwencje używane w raportach kopii zapasowych

* Filtry działają od lewej do prawej i od góry do dołu na każdej karcie. Oznacza to, że każdy filtr ma zastosowanie tylko do wszystkich tych widżetów, które są umieszczone po prawej stronie tego filtru lub poniżej tego filtru. 
* Wybranie kolorowego kafelka powoduje filtrowanie widżetów pod kafelekem dla rekordów odnoszących się do wartości tego kafelka. Na przykład wybranie kafelka **Ochrona zatrzymana** na karcie **Elementy kopii zapasowej** filtruje siatki i wykresy poniżej, aby wyświetlić dane dla elementów kopii zapasowej w stanie Wstrzymana ochrona.
* Kafelki, które nie są kolorowe, nie można kliknięć.
* Dane dla bieżącego dnia częściowego nie są wyświetlane w raportach. Tak więc, gdy wybrana wartość **zakresu czasu** jest **Ostatnia 7 dni,** raport pokazuje rekordy z ostatnich siedmiu ukończonych dni. Bieżący dzień nie jest uwzględniony.
* Raport zawiera szczegółowe informacje o zadaniach (oprócz zadań dziennika), które zostały *wyzwolone* w wybranym zakresie czasu. 
* Wartości wyświetlane dla **magazynu w chmurze** i **wystąpienia chronione** znajdują się na *końcu* wybranego zakresu czasu.
* Elementy kopii zapasowej wyświetlane w raportach są te elementy, które istnieją na *końcu* wybranego zakresu czasu. Elementy kopii zapasowej, które zostały usunięte w środku wybranego zakresu czasu, nie są wyświetlane. Ta sama konwencja ma zastosowanie do zasad kopii zapasowej, jak również.

## <a name="query-load-times"></a>Czasy ładowania kwerendy

Widżety w raporcie Kopia zapasowa są obsługiwane przez zapytania Kusto, które działają w obszarach roboczych usługi Log Analytics użytkownika. Te zapytania zazwyczaj obejmują przetwarzanie dużych ilości danych, z wielu sprzężeń, aby umożliwić bogatsze spostrzeżenia. W rezultacie widżety mogą nie ładować się natychmiast, gdy użytkownik wyświetla raporty w dużej nieruchomości kopii zapasowej. Ta tabela zawiera przybliżone oszacowanie czasu ładowania różnych widżetów na podstawie liczby elementów kopii zapasowej i zakresu czasu, dla którego raport jest oglądany.

| **# Źródła danych**                         | **Horyzont czasowy** | **Przybliżone czasy ładowania**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 tys.                       | 1 miesiąc          | Płytki: 5-10 sek. <br> Siatki: 5-10 sekund <br> Wykresy: 5-10 sek. <br> Filtry na poziomie raportu: 5-10 sek.|
| ~5 tys.                       | 3 miesiące          | Płytki: 5-10 sek. <br> Siatki: 5-10 sekund <br> Wykresy: 5-10 sek. <br> Filtry na poziomie raportu: 5-10 sek.|
| ~10 tys.                       | 3 miesiące          | Płytki: 15-20 sek. <br> Siatki: 15-20 sek. <br> Wykresy: 1-2 min <br> Filtry na poziomie raportu: 25-30 sek.|
| ~15 tys.                       | 1 miesiąc          | Płytki: 15-20 sek. <br> Siatki: 15-20 sek. <br> Wykresy: 50-60 sek. <br> Filtry na poziomie raportu: 20-25 sek.|
| ~15 tys.                       | 3 miesiące          | Płytki: 20-30 sek. <br> Siatki: 20-30 sek. <br> Wykresy: 2-3 min <br> Filtry na poziomie raportu: 50-60 sek. |

## <a name="what-happened-to-the-power-bi-reports"></a> Co się stało z raportami usługi Power BI? 
* Wcześniejsza aplikacja szablonu usługi Power BI do raportowania, która pochodzi z konta magazynu platformy Azure, znajduje się na ścieżce deprecation. Zaleca się rozpoczęcie wysyłania danych diagnostycznych magazynu do usługi Log Analytics w celu wyświetlenia raportów.

* Ponadto schemat V1 wysyłania danych diagnostycznych do konta magazynu lub obszaru roboczego usługi Log Analytics jest również na ścieżce wycofania. Jeśli zostały napisane wszelkie niestandardowe zapytania lub automatyzacje na podstawie schematu V1, zaleca się zaktualizować te zapytania, aby użyć aktualnie obsługiwanego schematu V2.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o monitorowaniu i raportowaniu za pomocą usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
