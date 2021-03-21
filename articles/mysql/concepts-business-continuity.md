---
title: Ciągłość działania — Azure Database for MySQL
description: Dowiedz się więcej o ciągłości działania (przywracanie do punktu w czasie, awaria centrum danych, przywracanie geograficzne) podczas korzystania z usługi Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: 15fde6e7558c685537d36f45bcc7e3ff341544ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542498"
---
# <a name="understand-business-continuity-in-azure-database-for-mysql"></a>Zrozumienie ciągłości działania w Azure Database for MySQL

W tym artykule opisano możliwości, które Azure Database for MySQL zapewnia ciągłość działania i odzyskiwanie po awarii. Informacje o opcjach odzyskiwania zdarzeń powodujących zakłócenia, które mogą spowodować utratę danych lub spowodować, że baza danych i aplikacja staną się niedostępne. Dowiedz się, co zrobić, gdy błąd użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure ma awarię lub aplikacja wymaga konserwacji.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkcje, których można użyć w celu zapewnienia ciągłości biznesowej

Azure Database for MySQL zapewnia funkcje ciągłości biznesowej, które obejmują automatyczne kopie zapasowe, oraz możliwość inicjowania przywracania geograficznego przez użytkowników. Każdy z nich ma różne cechy szacowanego czasu odzyskiwania (ERT) i potencjalną utratę danych. Szacowany czas odzyskiwania (ERT) jest szacowany czas, przez jaki baza danych ma być w pełni funkcjonalna po żądaniu przywracania/przejścia w tryb failover. Po zrozumieniu tych opcji można wybierać spośród nich i używać ich razem w różnych scenariuszach. Podczas opracowywania planu ciągłości biznesowej należy zrozumieć maksymalny akceptowalny czas, po upływie którego aplikacja zostanie w pełni odzyskana po wystąpieniu zdarzenia zakłócenia — jest to cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalną ilość najnowszych aktualizacji danych (przedział czasu), jaką aplikacja może tolerować podczas odzyskiwania po wystąpieniu zdarzenia zakłócenia — jest to cel punktu odzyskiwania (RPO).

W poniższej tabeli porównano ERT i cel punktu odzyskiwania dla dostępnych funkcji:

| **Funkcja** | **Podstawowa** | **Ogólnego przeznaczenia** | **Optymalizacja pod kątem pamięci** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Przywracanie do punktu w czasie z kopii zapasowej | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania |
| Przywracanie geograficzne z kopii zapasowych replikowanych geograficznie | Nieobsługiwane | ERT < 12 h<br/>Cel punktu odzyskiwania < 1 h | ERT < 12 h<br/>Cel punktu odzyskiwania < 1 h |

> [!IMPORTANT]
> **Nie** można przywrócić usuniętych serwerów. Usunięcie serwera spowoduje również usunięcie wszystkich baz danych należących do serwera, których nie można odzyskać.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Odzyskiwanie serwera po błędzie użytkownika lub aplikacji

Można użyć kopii zapasowych usługi do odzyskania serwera z różnych zdarzeń zakłócających działanie. Użytkownik może przypadkowo usunąć niektóre dane, przypadkowo porzucić ważną tabelę lub nawet porzucić całą bazę danych. Aplikacja może przypadkowo zastąpić dobre dane nieprawidłowymi danymi z powodu wady aplikacji i tak dalej.

Można wykonać przywracanie do punktu w czasie, aby utworzyć kopię serwera do znanego dobrego punktu w czasie. Ten punkt w czasie musi przypadać w okresie przechowywania kopii zapasowej skonfigurowanym dla serwera. Po przywróceniu danych na nowy serwer można zastąpić oryginalny serwer nowym przywróconym serwerem lub skopiować potrzebne dane z przywróconego serwera na oryginalny serwer.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Odzyskiwanie po awarii regionalnego centrum danych platformy Azure

Sporadycznie centrum danych platformy Azure może mieć awarię. Gdy wystąpi awaria, powoduje to zakłócenia działania firmy, które może trwać tylko kilka minut, ale może trwać w godzinach.

Jedną z opcji jest poczekanie, aż serwer powróci do trybu online po zakończeniu awarii centrum danych. Działa to w przypadku aplikacji, które mogą zapewnić, że serwer jest w trybie offline przez pewien czas, na przykład w środowisku programistycznym. Gdy centrum danych ma awarie, nie wiesz, jak długo może trwać awaria, więc ta opcja działa tylko wtedy, gdy serwer nie jest potrzebny przez pewien czas.

Drugą opcją jest użycie funkcji przywracania geograficznego Azure Database for MySQL, która przywraca serwer przy użyciu geograficznie nadmiarowych kopii zapasowych. Te kopie zapasowe są dostępne nawet wtedy, gdy region, w którym znajduje się serwer, jest w trybie offline. Można przywrócić z tych kopii zapasowych do dowolnego innego regionu i przywrócić serwer do trybu online.

> [!IMPORTANT]
> Przywracanie geograficzne jest możliwe tylko w przypadku aprowizacji serwera z magazynem kopii zapasowych nadmiarowego. Aby przełączyć się z lokalnie nadmiarowego do geograficznie nadmiarowych kopii zapasowych na istniejącym serwerze, należy wykonać zrzut przy użyciu mysqldump istniejącego serwera i przywrócić go na nowo utworzony serwer skonfigurowany z użyciem geograficznie nadmiarowych kopii zapasowych.

## <a name="cross-region-read-replicas"></a>Repliki odczytu między regionami

Za pomocą replik odczytu między regionami można usprawnić planowanie ciągłości działania i odzyskiwania po awarii. Repliki odczytu są aktualizowane asynchronicznie przy użyciu technologii replikacji binarnych dzienników MySQL. Dowiedz się więcej na temat odczytywania replik, dostępnych regionów i sposobu przełączenia w tryb failover z [artykułu pojęć dotyczących replik](concepts-read-replicas.md). 

## <a name="faq"></a>Często zadawane pytania
### <a name="where-does-azure-database-for-mysql-store-customer-data"></a>Gdzie usługa Azure Database for MySQL przechowywać dane klienta?
Domyślnie Azure Database for MySQL nie przenosi ani nie zapisuje danych klienta poza regionem, w którym jest wdrożony. Jednak klienci mogą opcjonalnie wybrać, aby włączyć [geograficznie nadmiarowe kopie zapasowe](concepts-backup.md#backup-redundancy-options) lub utworzyć [replikę odczytu między regionami](concepts-read-replicas.md#cross-region-replication) do przechowywania danych w innym regionie.

## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat [zautomatyzowanych kopii zapasowych znajduje się w Azure Database for MySQL](concepts-backup.md).
- Dowiedz się, jak przywrócić za pomocą [Azure Portal](howto-restore-server-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-restore-server-cli.md).
- Dowiedz się więcej [na temat odczytywania replik w Azure Database for MySQL](concepts-read-replicas.md).
