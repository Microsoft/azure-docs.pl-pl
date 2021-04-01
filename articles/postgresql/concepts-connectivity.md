---
title: Obsługa błędów łączności przejściowej — Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak obsługiwać błędy połączeń przejściowych dla Azure Database for PostgreSQL-pojedynczego serwera.
keywords: Połączenie PostgreSQL, parametry połączenia, problemy z łącznością, błąd przejściowy, błąd połączenia
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 7c70c82615df111f265604ff0984aa452d68565f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91704354"
---
# <a name="handling-transient-connectivity-errors-for-azure-database-for-postgresql---single-server"></a>Obsługa błędów przejściowych łączności dla usługi Azure Database for PostgreSQL — pojedynczy serwer

W tym artykule opisano sposób obsługi błędów przejściowych łączących się z Azure Database for PostgreSQL.

## <a name="transient-errors"></a>Błędy przejściowe

Błąd przejściowy, nazywany także błędem przejściowym, jest błędem, który zostanie rozwiązany. Zazwyczaj te błędy manifestuje jako połączenie z serwerem bazy danych, który jest usuwany. Nie można również otworzyć nowych połączeń z serwerem. Błędy przejściowe mogą wystąpić na przykład wtedy, gdy wystąpi awaria sprzętu lub sieci. Kolejną przyczyną może być Nowa wersja usługi PaaS, która jest wdrażana. Większość z tych zdarzeń jest automatycznie zmniejszana przez system w mniej niż 60 sekund. Najlepszym rozwiązaniem w przypadku projektowania i opracowywania aplikacji w chmurze jest oczekiwanie na błędy przejściowe. Załóżmy, że mogą wystąpić w dowolnym składniku w dowolnym momencie i mają odpowiednią logikę w celu obsługi takich sytuacji.

## <a name="handling-transient-errors"></a>Obsługa błędów przejściowych

Błędy przejściowe powinny być obsługiwane za pomocą logiki ponawiania. Sytuacje, które należy wziąć pod uwagę:

* Wystąpił błąd podczas próby otwarcia połączenia
* Połączenie bezczynne zostanie usunięte po stronie serwera. Gdy próbujesz wydać polecenie, którego nie można wykonać
* Aktywne połączenie, które aktualnie wykonuje polecenie, jest porzucane.

Pierwszy i drugi przypadek są stosunkowo proste do obsłużenia. Spróbuj ponownie otworzyć połączenie. Po pomyślnym zakończeniu Wystąpił błąd przejściowy przez system. Możesz użyć Azure Database for PostgreSQL ponownie. Zaleca się, aby przed ponowieniem próby nawiązać połączenie. Wycofaj, jeśli początkowe próby nie powiodą się. W ten sposób system może korzystać ze wszystkich dostępnych zasobów, aby przezwyciężyć sytuację błędu. Dobrym wzorcem do wykonania jest:

* Poczekaj 5 sekund przed pierwszym ponowieniem próby.
* Dla każdego kolejnego ponowienia próby Zwiększ czas oczekiwania na 60 sekund.
* Ustaw maksymalną liczbę ponownych prób, w których aplikacja uzna, że operacja nie powiodła się.

Jeśli połączenie z aktywną transakcją zakończy się niepowodzeniem, trudno jest prawidłowo obsłużyć odzyskiwanie. Istnieją dwa przypadki: Jeśli transakcja miała charakter tylko do odczytu, można bezpiecznie ponownie otworzyć połączenie i ponowić próbę wykonania transakcji. Jeśli jednak transakcja była również zapisywany w bazie danych, należy ustalić, czy transakcja została wycofana, czy też powiodła się przed wykonaniem przejściowego błędu. W takim przypadku użytkownik może dopiero nie otrzymać potwierdzenia zatwierdzenia z serwera bazy danych.

Jednym ze sposobów wykonania tej czynności jest wygenerowanie unikatowego identyfikatora na kliencie używanym do wszystkich ponownych prób. Ten unikatowy identyfikator zostanie przekazany jako część transakcji do serwera i zapisany w kolumnie z unikatowym ograniczeniem. W ten sposób można bezpiecznie ponowić próbę wykonania transakcji. Jeśli poprzednia transakcja została wycofana, a unikatowy identyfikator wygenerowany przez klienta nie istnieje jeszcze w systemie, zostanie wykonana pomyślnie. To nie powiedzie się, jeśli unikatowy identyfikator został wcześniej zapisany, ponieważ Poprzednia transakcja została ukończona pomyślnie.

Gdy program komunikuje się z Azure Database for PostgreSQL za pomocą oprogramowania pośredniczącego innej firmy, należy polecić dostawcę, czy oprogramowanie pośredniczące zawiera logikę ponawiania prób w przypadku błędów przejściowych.

Upewnij się, że test logiki ponawiania prób. Na przykład spróbuj wykonać swój kod podczas skalowania w górę lub w dół zasobów obliczeniowych Azure Database for PostgreSQL Server. Aplikacja powinna obsługiwać krótki czas przestoju, który został napotkany podczas tej operacji bez żadnych problemów.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z połączeniem z usługą Azure Database for PostgreSQL](howto-troubleshoot-common-connection-issues.md)
