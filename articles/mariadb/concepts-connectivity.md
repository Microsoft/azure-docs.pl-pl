---
title: Błędy łączności przejściowej — usługa Azure Database dla bazy danych MariaDB
description: Dowiedz się, jak obsługiwać błędy łączności przejściowej dla usługi Azure Database dla mariadb.
keywords: połączenie mysql,parametry połączenia,problemy z łącznością,błąd przejściowy,błąd połączenia
author: jasonwhowell
ms.author: jasonh
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2d162b5123cdaabe17859863c148f6483175d1e1
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770232"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-mariadb"></a>Obsługa błędów łączności przejściowej dla usługi Azure Database dla mariadb

W tym artykule opisano sposób obsługi błędów przejściowych łączących się z usługą Azure Database dla mariadb.

## <a name="transient-errors"></a>Błędy przejściowe

Błąd przejściowy, znany również jako błąd przejściowy, jest błędem, który sam się rozwiąże. Najczęściej te błędy manifestują się jako połączenie z serwerem bazy danych, który jest odrzucany. Nie można również otworzyć nowych połączeń z serwerem. Błędy przejściowe mogą wystąpić na przykład w przypadku awarii sprzętu lub sieci. Innym powodem może być nowa wersja usługi PaaS, która jest wdrażana. Większość z tych zdarzeń są automatycznie złagodzone przez system w mniej niż 60 sekund. Najlepszym rozwiązaniem w zakresie projektowania i tworzenia aplikacji w chmurze jest oczekiwanie błędów przejściowych. Załóżmy, że mogą się zdarzyć w dowolnym składniku w dowolnym momencie i mieć odpowiednią logikę w miejscu do obsługi tych sytuacji.

## <a name="handling-transient-errors"></a>Obsługa błędów przejściowych

Błędy przejściowe powinny być obsługiwane przy użyciu logiki ponawiania. Sytuacje, które należy wziąć pod uwagę:

* Podczas próby otwarcia połączenia występuje błąd
* Połączenie bezczynne jest upuszczane po stronie serwera. Podczas próby wydania polecenia nie można go wykonać
* Aktywne połączenie, które aktualnie wykonuje polecenie, zostanie przerwane.

Pierwszy i drugi przypadek są dość proste do przodu do obsługi. Spróbuj ponownie otworzyć połączenie. Po pomyślnym, błąd przejściowy został złagodzony przez system. Możesz ponownie użyć usługi Azure Database dla MariaDB. Zalecamy czekanie przed ponowieniem próby połączenia. Wycofaj się, jeśli początkowe ponownych prób zakończyć się niepowodzeniem. W ten sposób system może wykorzystać wszystkie dostępne zasoby, aby przezwyciężyć sytuację błędu. Dobrym wzorem do naśladowania jest:

* Odczekaj 5 sekund przed pierwszym ponowieniem próby.
* Dla każdej następującej próby, zwiększyć oczekiwanie wykładniczo, do 60 sekund.
* Ustaw maksymalną liczbę ponownych prób, w którym momencie aplikacja uważa, że operacja nie powiodła się.

Gdy połączenie z aktywną transakcją nie powiedzie się, jest trudniejsze do poprawnego obsługi odzyskiwania poprawnie. Istnieją dwa przypadki: Jeśli transakcja miała charakter tylko do odczytu, można bezpiecznie ponownie otworzyć połączenie i ponowić próbę wykonania transakcji. Jeśli jednak transakcja również zapisuje do bazy danych, należy określić, czy transakcja została wycofana lub jeśli powiodło się przed wystąpieniem błędu przejściowego. W takim przypadku może nie odebrano potwierdzenia zatwierdzenia z serwera bazy danych.

Jednym ze sposobów, aby to zrobić, jest wygenerowanie unikatowego identyfikatora na kliencie, który jest używany dla wszystkich ponownych prób. Ten unikatowy identyfikator należy przekazać jako część transakcji do serwera i przechowywać go w kolumnie z unikatowym ograniczeniem. W ten sposób można bezpiecznie ponowić próbę transakcji. Zakończy się pomyślnie, jeśli poprzednia transakcja została wycofana, a wygenerowany przez klienta unikatowy identyfikator nie istnieje jeszcze w systemie. Nie zakończy się niepowodzeniem, wskazując zduplikowane naruszenie klucza, jeśli unikatowy identyfikator był wcześniej przechowywany, ponieważ poprzednia transakcja została pomyślnie ukończona.

Gdy program komunikuje się z usługą Azure Database for MariaDB za pośrednictwem oprogramowania pośredniczącego innych firm, zapytaj dostawcę, czy oprogramowanie pośredniczące zawiera logikę ponawiania błędów przejściowych.

Upewnij się, aby przetestować logikę ponawiania. Na przykład spróbuj wykonać kod podczas skalowania w górę lub w dół zasobów obliczeniowych usługi Azure Database dla serwera MariaDB. Aplikacja powinna obsługiwać krótki czas przestoju, który napotkany podczas tej operacji bez żadnych problemów.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z połączeniem z usługą Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)
