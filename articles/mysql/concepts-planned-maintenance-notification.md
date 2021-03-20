---
title: Powiadomienie o planowanej konserwacji — Azure Database for MySQL — pojedynczy serwer
description: W tym artykule opisano funkcję powiadomień o planowanej konserwacji w Azure Database for MySQL-pojedynczym serwerze
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: ff197f8add65782a594d64661ffecdaced4598c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94919628"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mysql---single-server"></a>Powiadomienie o planowanej konserwacji w Azure Database for MySQL-pojedynczym serwerze

Dowiedz się, jak przygotować się do zaplanowanych zdarzeń konserwacji na Azure Database for MySQL.

## <a name="what-is-a-planned-maintenance"></a>Co to jest planowana konserwacja?

Usługa Azure Database for MySQL wykonuje zautomatyzowane stosowanie poprawek podstawowego sprzętu, systemu operacyjnego i aparatu bazy danych. Poprawka obejmuje nowe funkcje usługi, zabezpieczenia i aktualizacje oprogramowania. W przypadku aparatu MySQL uaktualnienia wersji pomocniczej są automatyczne i uwzględniane jako część cyklu poprawek. Nie jest wymagane wykonanie jakiejkolwiek czynności przez użytkownika ani ustawień konfiguracji. Poprawka jest przeprowadzana w szerokim zakresie i przeprowadzona przy użyciu bezpiecznych praktyk wdrażania.

Planowana konserwacja to okno obsługi, gdy aktualizacje usługi są wdrażane na serwerach w danym regionie świadczenia usługi Azure. Podczas planowanej konserwacji jest tworzone zdarzenie powiadomienia informujące klientów o wdrożeniu aktualizacji usługi w regionie świadczenia platformy Azure, w którym serwery są hostowane. Minimalny czas trwania między dwoma planowanymi konserwacjami to 30 dni. 72 godziny wcześniej otrzymasz powiadomienie o następnym oknie obsługi.

## <a name="planned-maintenance---duration-and-customer-impact"></a>Planowana konserwacja — czas trwania i wpływ klienta

Planowana konserwacja dla danego regionu świadczenia usługi Azure zazwyczaj oczekuje na 15 godzin. W razie potrzeby okno zawiera również czas buforowania w celu wykonania planu wycofywania. Podczas planowanej konserwacji mogą wystąpić ponowne uruchomienia serwera bazy danych lub przełączenia w tryb failover, co może prowadzić do krótkich niedostępności serwerów baz danych dla użytkowników końcowych. Serwery Azure Database for MySQL są uruchomione w kontenerach, więc ponowne uruchomienia serwera bazy danych są zwykle szybkie, oczekiwane zwykle w ciągu 60-120 sekund. Całe planowane zdarzenie konserwacji, w tym każde ponowne uruchomienie serwera, jest starannie monitorowane przez Zespół inżynieryjny. Czas pracy awaryjnej serwera zależy od czasu odzyskiwania bazy danych, co może spowodować, że baza danych będzie przełączona w tryb online, jeśli na serwerze w momencie przejścia w tryb failover występuje intensywna aktywność transakcyjna. Aby uniknąć dłuższego czasu ponownego uruchomienia, zaleca się uniknięcie wolnych długotrwałych transakcji (ładowania zbiorczego) podczas planowanych zdarzeń konserwacji.

Podsumowując, podczas gdy planowane zdarzenie konserwacji działa przez 15 godzin, wpływ na serwer jest zazwyczaj trwa 60 sekund w zależności od aktywności transakcyjnej na serwerze. Powiadomienie jest wysyłane 72 godzin kalendarzowych przed rozpoczęciem zaplanowanej konserwacji i innym, gdy konserwacja jest w toku dla danego regionu.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>Jak mogę otrzymywać powiadomienia o planowanej konserwacji?

Możesz użyć funkcji powiadomień o planowanej konserwacji, aby otrzymywać alerty dla nadchodzącego zaplanowanego zdarzenia konserwacji. Po zakończeniu konserwacji dla danego regionu otrzymasz powiadomienie o nadchodzącej godzinie kalendarzowej konserwacji 72.

### <a name="planned-maintenance-notification"></a>Powiadomienie o planowanej konserwacji

> [!IMPORTANT]
> Powiadomienia o planowanej konserwacji są obecnie dostępne w wersji zapoznawczej we wszystkich regionach **z wyjątkiem** zachodnich Stanów Zjednoczonych

**Powiadomienia o planowanej konserwacji** umożliwiają otrzymywanie alertów dotyczących nadchodzącego zaplanowanego zdarzenia konserwacji do Azure Database for MySQL. Te powiadomienia są zintegrowane z zaplanowaną konserwacją [Service Health](../service-health/overview.md) i umożliwiają wyświetlanie całej zaplanowanej konserwacji subskrypcji w jednym miejscu. Pomaga również skalować powiadomienie do odpowiednich odbiorców dla różnych grup zasobów, ponieważ użytkownik może mieć różne kontakty odpowiedzialne za różne zasoby. Przed wydarzeniem otrzymasz powiadomienie o nadchodzących godzinach konserwacji 72.

Firma Microsoft podejmie próbę udostępnienia **powiadomienia o planowanej konserwacji** 72 godzin dla wszystkich zdarzeń. Jednak w przypadku poprawek krytycznych lub zabezpieczeń powiadomienia mogą być wysyłane bliżej zdarzenia lub zostać pominięte.

Możesz sprawdzić powiadomienie dotyczące planowanej konserwacji na Azure Portal lub skonfigurować alerty, aby otrzymywać powiadomienia. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Sprawdź powiadomienie o planowanej konserwacji z Azure Portal

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Service Health**.
2. Wybierz kartę **Planowana konserwacja**
3. Wybierz **subskrypcję**, **region** i **usługę** , dla której chcesz sprawdzić zaplanowane powiadomienie o konserwacji. 
   
### <a name="to-receive-planned-maintenance-notification"></a>Aby odebrać powiadomienie o planowanej konserwacji

1. W [portalu](https://portal.azure.com)wybierz pozycję **Service Health**.
2. W sekcji **alerty** wybierz pozycję **alerty dotyczące kondycji**.
3. Wybierz pozycję **+ Dodaj alert kondycji usługi** i wypełnij pola.
4. Wypełnij pola wymagane. 
5. Wybierz **Typ zdarzenia**, wybierz pozycję **Planowana konserwacja** lub **Zaznacz wszystko**
6. W obszarze **grupy akcji** Określ, w jaki sposób chcesz otrzymywać alert (Otrzymuj wiadomość e-mail, wyzwól aplikację logiki itp.).  
7. Upewnij się, że w momencie utworzenia reguły włączania zostanie ustawiona wartość tak.
8. Wybierz pozycję **Utwórz regułę alertu** , aby zakończyć alert

Szczegółowe instrukcje dotyczące tworzenia **alertów dotyczących kondycji usługi** można znaleźć w sekcji [tworzenie alertów dziennika aktywności w powiadomieniach dotyczących usług](../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>Czy mogę anulować lub odroczyć planowaną konserwację?

Aby zapewnić bezpieczeństwo i stabilność serwera, należy przeprowadzić konserwację. Nie można anulować lub odłożyć planowanego zdarzenia konserwacji. Po wysłaniu powiadomienia do danego regionu świadczenia usługi Azure nie można zmienić harmonogramu poprawek dla każdego serwera w tym regionie. Poprawka jest rzutowana na cały region jednocześnie. Azure Database for MySQL — usługa pojedynczego serwera została zaprojektowana dla natywnej aplikacji w chmurze, która nie wymaga szczegółowej kontroli ani dostosowywania usługi. Jeśli chcesz mieć możliwość zaplanowania konserwacji serwerów, zalecamy Rozważmy użycie [elastycznych serwerów](./flexible-server/overview.md).

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>Czy wszystkie regiony platformy Azure są poprawiane w tym samym czasie?

Nie. wszystkie regiony platformy Azure są poprawiane w czasie trwania okna wdrożenia. Przeważnie okno wdrożenia zazwyczaj rozciąga się od 5 PM-8 czasu lokalnego na dzień w danym regionie świadczenia usługi Azure. Wielowymiarowe regiony platformy Azure są poprawione w różnych dniach. Aby zapewnić wysoką dostępność i ciągłość działania serwerów baz danych, zaleca się korzystanie z [replik odczytu między regionami](./concepts-read-replicas.md#cross-region-replication) .

## <a name="retry-logic"></a>Logika ponowień

Błąd przejściowy, nazywany także błędem przejściowym, jest błędem, który zostanie rozwiązany. [Błędy przejściowe](./concepts-connectivity.md#transient-errors) mogą wystąpić podczas konserwacji. Większość z tych zdarzeń jest automatycznie zmniejszana przez system w mniej niż 60 sekund. Błędy przejściowe powinny być obsługiwane za pomocą [logiki ponawiania](./concepts-connectivity.md#handling-transient-errors).


## <a name="next-steps"></a>Następne kroki

- W przypadku pytań lub sugestii, które mogą mieć na celu pracę z Azure Database for MySQL, Wyślij wiadomość e-mail do zespołu Azure Database for MySQL w AskAzureDBforMySQL@service.microsoft.com
- Zobacz [jak skonfigurować alerty](howto-alert-on-metric.md) , aby uzyskać wskazówki dotyczące tworzenia alertu dotyczącego metryki.
- [Rozwiązywanie problemów z połączeniem do Azure Database for MySQL-pojedynczego serwera](howto-troubleshoot-common-connection-issues.md)
- [Obsługa błędów przejściowych i wydajne łączenie się z Azure Database for MySQL-pojedynczym serwerem](concepts-connectivity.md)
