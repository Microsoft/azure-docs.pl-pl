---
title: Harmonogramy konserwacji dla puli SQL Synapse
description: Planowanie konserwacji umożliwia klientom zaplanowanie niezbędnych zaplanowanych zdarzeń konserwacji, których usługa Azure Synapse Analytics używa do tworzenia nowych funkcji, uaktualnień i poprawek.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 886e99d6f56a008c694b3bcb366cfb4049f3528e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590207"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Korzystanie z harmonogramów konserwacji do zarządzania aktualizacjami i konserwacją usług

Funkcja harmonogramu konserwacji integruje Service Health planowane powiadomienia o konserwacji, Resource Health monitorowanie i usługę planowania konserwacji dla puli SQL Synapse (magazyn danych) w ramach usługi Azure Synapse Analytics.

Należy użyć funkcji planowanie konserwacji, aby wybrać przedział czasu, gdy jest wygodne do otrzymywania nowych funkcji, uaktualnień i poprawek. Musisz wybrać podstawowe i pomocnicze okno obsługi w ciągu siedmiu dni, każde okno musi znajdować się w różnych zakresach dni.

Na przykład można zaplanować podstawowe okno soboty Sobota 22:00 do niedzieli 01:00, a następnie zaplanować okno pomocnicze w środę 19:00 do 22:00. Jeśli nie można wykonać konserwacji w podstawowym oknie obsługi, spróbuje przeprowadzić konserwację ponownie w oknie konserwacji dodatkowej. Obsługa usługi może się odbywać w obu oknach podstawowych i pomocniczych. Aby zapewnić szybkie zakończenie wszystkich operacji konserwacyjnych, DW400c i niższe warstwy magazynu danych mogą zakończyć konserwację poza wydzielonym oknem obsługi.

Wszystkie nowo utworzone wystąpienia magazynu danych będą miały zdefiniowany przez system harmonogram konserwacji stosowany podczas wdrażania. Harmonogram można edytować zaraz po zakończeniu wdrażania.

Mimo że okno obsługi może być od trzech do ośmiu godzin nie oznacza to, że magazyn danych będzie w trybie offline przez czas trwania. Konserwacja może wystąpić w dowolnym momencie w tym oknie i należy oczekiwać pojedynczego rozłączenia w tym okresie przez trwałą 5 -6 minut, ponieważ usługa wdraża nowy kod w magazynie danych. DW400c i Lower mogą mieć wiele krótkich strat w łączności w różnym czasie w oknie obsługi. Po rozpoczęciu konserwacji wszystkie aktywne sesje zostaną anulowane, a transakcje niezatwierdzone zostaną wycofane. Aby zminimalizować przestoje wystąpienia, upewnij się, że magazyn danych nie ma długotrwałych transakcji przed wybranym okresem konserwacji.

Wszystkie operacje konserwacji powinny zakończyć się w określonych oknach obsługi, chyba że wymagane jest wdrożenie aktualizacji poufnej czasu. Jeśli magazyn danych jest wstrzymany podczas zaplanowanej konserwacji, zostanie on zaktualizowany podczas operacji wznawiania. Po zakończeniu konserwacji magazynu danych zostanie wyświetlone powiadomienie.

## <a name="alerts-and-monitoring"></a>Alerty i monitorowanie

Integracja z Service Health powiadomieniami i monitorowaniem Resource Health kontrola pozwala klientom na uzyskanie informacji o zbliżającej się aktywności. Ta Automatyzacja wykorzystuje Azure Monitor. Możesz zdecydować, jak chcesz otrzymywać powiadomienia o zbliżającym się zdarzeniu konserwacji. Ponadto możesz wybrać, które automatyczne przepływy będą pomocne w zarządzaniu przestojami i minimalizacji wpływu operacyjnego.

Powiadomienie z góry 24-godzinne poprzedza wszystkie zdarzenia konserwacji, które nie są używane w przypadku DWC400c i niższych warstw.

> [!NOTE]
> W przypadku, gdy wymagane jest wdrożenie aktualizacji krytycznej czasu, zaawansowane czasy powiadomień mogą być znacząco ograniczone. Może się to zdarzyć poza zidentyfikowanym oknem obsługi z powodu krytycznego charakteru aktualizacji.

Jeśli otrzymasz z wyprzedzeniem powiadomienie, że konserwacja zostanie przeprowadzona, ale nie będzie można przeprowadzić konserwacji w okresie powiadomienia, otrzymasz powiadomienie o anulowaniu. Następnie konserwacja zostanie wznowiona w następnym zaplanowanym okresie konserwacji.

Wszystkie aktywne zdarzenia konserwacji są wyświetlane w sekcji **konserwacja Service Health-planowana** . Historia Service Health obejmuje pełny zapis przeszłych zdarzeń. Konserwację można monitorować za pomocą pulpitu nawigacyjnego portalu Azure Service Health Check podczas aktywnego zdarzenia.

### <a name="maintenance-schedule-availability"></a>Dostępność harmonogramu konserwacji

Nawet jeśli planowanie konserwacji nie jest dostępne w wybranym regionie, można w dowolnym momencie wyświetlać i edytować harmonogram konserwacji. Gdy planowanie konserwacji stanie się dostępne w Twoim regionie, określony harmonogram natychmiast stanie się aktywny w puli SQL Synapse.

## <a name="view-a-maintenance-schedule"></a>Wyświetlanie harmonogramu konserwacji

Domyślnie wszystkie nowo utworzone wystąpienia magazynu danych mają osiem godzin podstawowe i pomocnicze okno obsługi zastosowane podczas wdrażania. Jak wspomniano powyżej, można zmienić system Windows po zakończeniu wdrażania. Żadne czynności konserwacyjne nie będą przeprowadzane poza oknami obsługi bez wcześniejszego powiadomienia.

Aby wyświetlić harmonogram konserwacji, który został zastosowany do puli SQL Synapse, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pulę SQL Synapse, którą chcesz wyświetlić.
3. Wybrana Pula Synapse SQL zostanie otwarta w bloku przegląd. Harmonogram konserwacji, który jest stosowany do magazynu danych, zostanie wyświetlony poniżej **harmonogramu konserwacji**.

![Blok przegląd](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Zmiana harmonogramu konserwacji

Harmonogram konserwacji można aktualizować lub zmieniać w dowolnym momencie. Jeśli wybrane wystąpienie przechodzi przez aktywny cykl konserwacji, ustawienia zostaną zapisane. Staną się one aktywne w następnym określonym okresie konserwacji. [Dowiedz się więcej](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o monitorowaniu magazynu danych podczas aktywnego zdarzenia konserwacji.

## <a name="identifying-the-primary-and-secondary-windows"></a>Identyfikowanie podstawowych i pomocniczych okien

Podstawowe i pomocnicze okna muszą mieć oddzielne zakresy dni. Przykładem jest główne okno wtorek – czwartek i dodatkowe okno soboty — niedziela.

Aby zmienić harmonogram konserwacji dla puli SQL Synapse, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pulę SQL Synapse, którą chcesz zaktualizować. Strona zostanie otwarta w bloku przegląd.
Otwórz stronę ustawienia harmonogramu konserwacji, wybierając łącze **Podsumowanie harmonogramu konserwacji** w bloku przegląd. Lub wybierz opcję **harmonogram konserwacji** w menu zasobów po lewej stronie.

    ![Opcje bloku przegląd](./media/maintenance-scheduling/maintenance-change-option.png)

3. Określ preferowany zakres dni dla głównego okna obsługi przy użyciu opcji w górnej części strony. Wybór ten określa, czy okno podstawowe będzie miało miejsce w dniu tygodnia, czy w weekendy. Wybór spowoduje zaktualizowanie wartości listy rozwijanej.
W trakcie okresu zapoznawczego niektóre regiony mogą jeszcze nie obsługiwać pełny zestaw dostępnych opcji **dnia** .

   ![Blok ustawień konserwacji](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Wybierz preferowane główne i pomocnicze okna obsługi przy użyciu pól listy rozwijanej:
   - **Dzień**: preferowany dzień przeprowadzenia konserwacji w wybranym oknie.
   - **Godzina rozpoczęcia**: preferowany czas rozpoczęcia okna obsługi.
   - **Przedział czasu**: preferowany czas trwania przedziału czasu.

   Obszar **Podsumowanie harmonogramu** w dolnej części bloku zostanie zaktualizowany na podstawie wybranych wartości.
  
5. Wybierz pozycję **Zapisz**. Zostanie wyświetlony komunikat z potwierdzeniem, że nowy harmonogram jest teraz aktywny.

   W przypadku zapisywania harmonogramu w regionie, który nie obsługuje planowania konserwacji, zostanie wyświetlony następujący komunikat. Twoje ustawienia są zapisywane i stają się aktywne, gdy funkcja stanie się dostępna w wybranym regionie.

   ![Komunikat o dostępności regionu](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej](../../azure-monitor/alerts/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) na temat tworzenia i wyświetlania alertów oraz zarządzania nimi przy użyciu Azure monitor.
- [Dowiedz się więcej](../..//azure-monitor/alerts/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o akcjach elementu webhook dla reguł alertów dziennika.
- [Dowiedz się więcej](../..//azure-monitor/alerts/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Tworzenie grup akcji i zarządzanie nimi.
- [Dowiedz się więcej](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o Azure Service Health.
