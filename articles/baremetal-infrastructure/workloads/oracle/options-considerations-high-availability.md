---
title: Opcje lub serwery oracle BareMetal Infrastructure
description: Dowiedz się więcej o opcjach i zagadnieniach dotyczących serwerów infrastruktury Oracle BareMetal.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: e8a2dece11884e3a659f14b30bce51e7f0244924
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590304"
---
# <a name="options-for-oracle-baremetal-infrastructure-servers"></a>Opcje dla serwerów infrastruktury Oracle BareMetal

W tym artykule rozważymy opcje i rekomendacje, aby uzyskać najwyższy poziom ochrony i wydajności działania programu Oracle na serwerach infrastruktury BareMetal. 

## <a name="data-guard-protection-modes"></a>Tryby ochrony ochrony ochrony ochrony danych

Ochrona za pomocą usługi Data Guard jest niedostępna wyłącznie za pośrednictwem klastra Oracle Real Applications Cluster (RAC), replikacji logicznej (takiej jak GoldenGate) i replikacji opartej na magazynie. 

| Tryb ochrony | Opis |
| --- | --- |
| **Maksymalna wydajność** | Domyślny tryb ochrony. Zapewnia ona najwyższy poziom ochrony bez wpływu na wydajność podstawowej bazy danych. Dane są uznawane za zatwierdzone natychmiast po ich napisano w strumieniu ponownego ponownego przesyłania do podstawowej bazy danych. Jest on następnie replikowany asynchronicznie do rezerwowej bazy danych. Ogólnie rzecz biorąc, rezerwowa baza danych odbiera ją w ciągu kilku sekund, ale nie ma gwarancji, że tak się stanie. Ten tryb zwykle spełnia wymagania biznesowe (oprócz monitorowania opóźnień) bez konieczności łączności sieciowej o małych opóźnieniach między lokacjami podstawowymi i rezerwowymi.<br /><br />Zapewnia najlepszą trwałość operacyjną; Jednak nie gwarantuje to utraty danych.   |
| **Maksymalna dostępność** | Zapewnia najwyższy poziom ochrony bez wpływu na dostępność podstawowej bazy danych. Dane nigdy nie są uznawane za zatwierdzone w podstawowej bazie danych, dopóki nie zostały zatwierdzone w co najmniej jednej rezerwowej bazie danych. Jeśli podstawowa baza danych nie może zapisać zmian ponownego wykonania w co najmniej jednej rezerwowej bazie danych, wróci do trybu maksymalnej wydajności, a nie stanie się niedostępna. <br /><br />Umożliwia usłudze kontynuowanie działania w przypadku niedostępności lokacji rezerwowej. Jeśli działa tylko jedna lokacja, zostanie zachowana tylko jedna kopia danych, dopóki druga lokacja nie będzie w trybie online i synchronizacja zostanie ponownie ustanowiona. |
| **Maksymalna ochrona** | Zapewnia poziom ochrony podobny do maksymalnej dostępności. Podstawowa baza danych jest zamykana z dodaną funkcją, jeśli nie może zapisać zmian ponownego zapisu w co najmniej jednej rezerwowej bazie danych. Gwarantuje to, że utrata danych nie może wystąpić, ale kosztem bardziej niestabilnej dostępności. |

>[!IMPORTANT]
>Jeśli potrzebujesz celu punktu odzyskiwania (RPO) o wartości zero, zalecamy konfigurację maksymalnej dostępności. Wtedy można zagwarantować RPO nawet wtedy, gdy wystąpi wiele awarii. Na przykład nawet w przypadku 3000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 00

### <a name="data-guard-deployment-patterns"></a>Wzorce wdrażania usługi Data Guard

Oracle umożliwia skonfigurowanie wielu miejsc docelowych do ponownego generowania, co umożliwia tworzenie wielu rezerwowych baz danych. Najbardziej typowa konfiguracja jest pokazana na poniższej ilustracji: pojedyncza rezerwowa baza danych w innym regionie.

:::image type="content" source="media/oracle-high-availability/default-data-guard-deployment.png" alt-text="Diagram przedstawiający domyślne wdrożenie programu Data Guard w programie Oracle.":::

Opcja Data Guard jest skonfigurowana w trybie maksymalnej wydajności dla wdrożenia domyślnego. Ta konfiguracja zapewnia replikację danych niemal w czasie rzeczywistym za pośrednictwem asynchronicznego transportu ponownego uruchomienia. Rezerwowa baza danych nie musi działać wewnątrz wdrożenia RAC, ale zalecamy, aby spełniała wymagania dotyczące wydajności lokacji głównej.

Zalecamy wdrożenie podobne do pokazanego na poniższej ilustracji dla środowisk, które wymagają ścisłego czasu pracy lub celu punktu wdrożenia o wartości zero. Konfiguracja maksymalnej dostępności składa się z lokalnej rezerwowej bazy danych stosującej ponowne uruchomienie w trybie synchronicznym i drugiej rezerwowej bazy danych uruchomionej w regionie zdalnym.

:::image type="content" source="media/oracle-high-availability/max-availability-data-guard-deployment.png" alt-text="Diagram przedstawiający wdrożenie funkcji Data Guard o maksymalnej dostępności.":::

Możesz utworzyć lokalną rezerwową bazę danych, gdy wydajność aplikacji będzie się niosła, uruchamiając bazę danych i serwery aplikacji w oddzielnych regionach. W tej konfiguracji rezerwowa lokalna baza danych jest używana, gdy w klastrze podstawowym jest potrzebna planowana lub nieplanowana konserwacja. Te bazy danych można uruchamiać z replikacją synchroniczną, ponieważ są one w tym samym regionie, co gwarantuje, że żadne dane nie utracą danych między nimi.

### <a name="data-guard-configuration-considerations"></a>Zagadnienia dotyczące konfiguracji usługi Data Guard

Broker ochrony danych powinien być implementowany, ponieważ upraszcza implementowanie konfiguracji ochrony danych i zapewnia przestrzeganie najlepszych rozwiązań. Zapewnia funkcję monitorowania wydajności i znacznie upraszcza procedury przełączania, pracy w trybie failover i ponownego antiacji.

Funkcja Data Guard umożliwia uruchomienie procesu obserwatora, który monitoruje wszystkie bazy danych w konfiguracji funkcji Data Guard w celu określenia dostępności bazy danych. Jeśli podstawowa baza danych ulegnie awarii, obserwator funkcji Data Guard może automatycznie uruchomić tryb failover do rezerwowej bazy danych w konfiguracji. Obserwator ochrony danych można zaimplementować z wieloma obserwatorami na podstawie liczby lokacji fizycznych (maksymalnie trzech). 

Ten obserwator powinien być zlokalizowany w infrastrukturze, która będzie obsługiwać warstwę aplikacji. Podstawowy obserwator powinien zawsze istnieć w lokacji fizycznej, w której nie znajduje się podstawowa baza danych. Zalecamy zachowanie ostrożności podczas automatyzowania operacji trybu failover wyzwalanych przez obserwatora ochrony danych. Najpierw upewnij się, że aplikacje zostały zaprojektowane i przetestowane w celu zapewnienia akceptowalnej usługi, gdy baza danych działa w oddzielnej lokalizacji.

Jeśli aplikacja może działać tylko lokalnie, tryb failover do lokacji dodatkowej musi być ręczny. Środowiska wymagające wysokiej dostępności (99,99% lub 99,999% czasu pracy) powinny korzystać zarówno z lokalnej, jak i zdalnej rezerwowej bazy danych, jak pokazano na poprzedniej ilustracji. W takich przypadkach parametr FastStartFailoverTarget zostanie ustawiony tylko na lokalną rezerwową bazę danych.

W przypadku wszystkich aplikacji, które obsługują dostęp do aplikacji/baz danych między witrynami, wartość FastStartFailoverTarget jest ustawiona na wszystkie rezerwowe bazy danych w konfiguracji funkcji Data Guard.

### <a name="active-data-guard"></a>Active Data Guard

Oracle Active Data Guard (ADG) to najadłowy podstawowy zestaw podstawowych funkcji Data Guard dołączonych do Oracle Database Enterprise Edition. Udostępnia ona następujące dodane funkcje, które będą używane we wdrożeniu oracle Exadata:

- Wykrywanie unikatowych uszkodzeń i automatyczna naprawa.
- Szybkie tryb failover do zsynchronizowanej repliki produkcyjnej — ręcznej lub automatycznej.
- Odciążanie obciążenia produkcyjnego do zsynchronizowanej rezerwy otwartej tylko do odczytu.
- Uaktualnienia stopniowe bazy danych i stan wstrzymania. Pierwsze stosowanie poprawek przy użyciu stanu wstrzymania fizycznego.
- Odciążanie przyrostowych kopii zapasowych do stanu wstrzymania.
- Ochrona odzyskiwania danych bez utraty danych na dowolnej odległości bez wpływu na wydajność.

Oficjalny dokument dostępny na [https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf](https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf) stronie zawiera dobre omówienie poprzednich funkcji, jak pokazano na poniższej ilustracji.

:::image type="content" source="media/oracle-high-availability/active-data-guard-features.png" alt-text="Diagram przedstawiający przegląd funkcji Active Data Guard firmy Oracle.":::

## <a name="backup-recommendations"></a>Zalecenia dotyczące kopii zapasowych

Pamiętaj, aby wrócić do kopii zapasowej baz danych. Użyj funkcji przywracania i odzyskiwania, aby przywrócić bazę danych do tego samego lub innego systemu albo odzyskać pliki bazy danych.

Ważne jest, aby utworzyć strategię odzyskiwania kopii zapasowych, aby chronić bazy Oracle Database urządzenia przed utratą danych. Taka utrata może wynikać z fizycznego problemu z dyskiem, który powoduje niepowodzenie odczytu lub zapisu w pliku dysku wymaganym do uruchomienia bazy danych. Błąd użytkownika może również spowodować utratę danych. Funkcja tworzenia kopii zapasowej zapewnia możliwość przywracania do punktu w czasie (PITR) w celu przywrócenia bazy danych, odzyskiwania numeru zmiany **systemu (SCN) i najnowszego odzyskiwania.** Zasady tworzenia kopii zapasowych można utworzyć w oknie Interfejs użytkownika lub za pomocą interfejsu wiersza polecenia.

Dostępne są następujące opcje tworzenia kopii zapasowych:

- Kopii zapasowej na woluminie magazynu NFS (Fast Recovery Area-FRA-/u98).
- Używanie Azure NetApp Files SnapCenter — migawki.

Proces, który należy wziąć pod uwagę:

- Ręczne lub automatyczne tworzenie kopii zapasowych.
- Automatyczne kopie zapasowe są zapisywane w woluminach magazynu NFS (na przykład /u98).
- Kopie zapasowe są uruchamiane między 12:00 a 6:00 w strefie czasowej systemu bazy danych.
- Obecne okresy przechowywania: 7, 15, 30, 45 i 60 dni.

- Odzyskiwanie bazy danych z kopii zapasowej przechowywanej w magazynie obiektów:
  - Do ostatniego znanego dobrego stanu z najmniejszą możliwą utratą danych.
  - Przy użyciu znacznika czasu określony.
  - Przy użyciu określonej scN.
  - BackupReport — używa _scN z raportu kopii zapasowej zamiast określonej scn._

:::image type="content" source="media/oracle-high-availability/customer-db-backup-to-fra.png" alt-text="Diagram przedstawiający kopię zapasową bazy danych klienta w usługach FRA (/u98) i innych niż FRA (/u95).":::

### <a name="backup-policy"></a>Zasady tworzenia kopii zapasowej

Zasady tworzenia kopii zapasowych definiują szczegóły kopii zapasowej. Podczas tworzenia zasad kopii zapasowych należy zdefiniować miejsce docelowe kopii zapasowych bazy danych FRA (lokalizacja NFS) i zdefiniować okno odzyskiwania.

Domyślnie używany jest algorytm kompresji BASIC. W przypadku używania algorytmów kompresji LOW, MEDIUM lub HIGH dla zasad tworzenia kopii zapasowych dysków lub NFS należy wziąć pod uwagę kwestie związane z licencją.

### <a name="backup-levels"></a>Poziomy kopii zapasowych

Określ poziom kopii zapasowej podczas tworzenia kopii zapasowej.

- Poziom 0 — pełny
- Poziom 1 — przyrostowy
- LongTerm/Archievelog — z wyjątkiem zasad przechowywania kopii zapasowych użyj lokalizacji spoza usługi FRA (na przykład /u95).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak odzyskać bazę danych Oracle w przypadku wystąpienia awarii:

> [!div class="nextstepaction"]
> [Odzyskiwanie bazy danych Oracle w infrastrukturze Azure BareMetal](oracle-high-availability-recovery.md)
