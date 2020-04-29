---
title: Ciągłość działania — Azure Database for MySQL
description: Dowiedz się więcej o ciągłości działania (przywracanie do punktu w czasie, awaria centrum danych, przywracanie geograficzne) podczas korzystania z usługi Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: af0069adc741cfc802c37c90c0c7ec3c3ba74bb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537231"
---
# <a name="understand-business-continuity-in-azure-database-for-mysql"></a>Zrozumienie ciągłości działania w Azure Database for MySQL

W tym artykule opisano możliwości, które Azure Database for MySQL zapewnia ciągłość działania i odzyskiwanie po awarii. Informacje o opcjach odzyskiwania zdarzeń powodujących zakłócenia, które mogą spowodować utratę danych lub spowodować, że baza danych i aplikacja staną się niedostępne. Dowiedz się, co zrobić, gdy błąd użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure ma awarię lub aplikacja wymaga konserwacji.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkcje, których można użyć w celu zapewnienia ciągłości biznesowej

Azure Database for MySQL zapewnia funkcje ciągłości biznesowej, które obejmują automatyczne kopie zapasowe, oraz możliwość inicjowania przywracania geograficznego przez użytkowników. Każdy z nich ma różne cechy szacowanego czasu odzyskiwania (ERT) i potencjalną utratę danych. Po zrozumieniu tych opcji można wybierać spośród nich i używać ich razem w różnych scenariuszach. Podczas opracowywania planu ciągłości biznesowej należy zrozumieć maksymalny akceptowalny czas, po upływie którego aplikacja zostanie w pełni odzyskana po wystąpieniu zdarzenia zakłócenia — jest to cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalną ilość najnowszych aktualizacji danych (przedział czasu), jaką aplikacja może tolerować podczas odzyskiwania po wystąpieniu zdarzenia zakłócenia — jest to cel punktu odzyskiwania (RPO).

W poniższej tabeli porównano ERT i cel punktu odzyskiwania dla dostępnych funkcji:

| **Możliwość** | **Podstawowy** | **Ogólnego przeznaczenia** | **Optymalizacja pod kątem pamięci** |
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

## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat [zautomatyzowanych kopii zapasowych znajduje się w Azure Database for MySQL](concepts-backup.md).
- Dowiedz się, jak przywrócić za pomocą [Azure Portal](howto-restore-server-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-restore-server-cli.md).
- Dowiedz się więcej [na temat odczytywania replik w Azure Database for MySQL](concepts-read-replicas.md).
