---
title: Obsługa błędów łączności przejściowej — usługa Azure Database for PostgreSQL — single server
description: Dowiedz się, jak obsługiwać błędy łączności przejściowej dla usługi Azure Database dla postgreSql — pojedynczy serwer.
keywords: połączenie postgresql,parametry połączenia,problemy z łącznością,błąd przejściowy,błąd połączenia
author: jasonwhowell
ms.author: jasonh
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 8138512dfc893f3523c5ad78a93aef1bcdedfe70
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768038"
---
# <a name="handling-transient-connectivity-errors-for-azure-database-for-postgresql---single-server"></a>Obsługa błędów przejściowych łączności dla usługi Azure Database for PostgreSQL — pojedynczy serwer

W tym artykule opisano sposób obsługi błędów przejściowych łączących się z usługą Azure Database for PostgreSQL.

## <a name="transient-errors"></a>Błędy przejściowe

Błąd przejściowy, znany również jako błąd przejściowy, jest błędem, który sam się rozwiąże. Najczęściej te błędy manifestują się jako połączenie z serwerem bazy danych, który jest odrzucany. Nie można również otworzyć nowych połączeń z serwerem. Błędy przejściowe mogą wystąpić na przykład w przypadku awarii sprzętu lub sieci. Innym powodem może być nowa wersja usługi PaaS, która jest wdrażana. Większość z tych zdarzeń są automatycznie złagodzone przez system w mniej niż 60 sekund. Najlepszym rozwiązaniem w zakresie projektowania i tworzenia aplikacji w chmurze jest oczekiwanie błędów przejściowych. Załóżmy, że mogą się zdarzyć w dowolnym składniku w dowolnym momencie i mieć odpowiednią logikę w miejscu do obsługi tych sytuacji.

## <a name="handling-transient-errors"></a>Obsługa błędów przejściowych

Błędy przejściowe powinny być obsługiwane przy użyciu logiki ponawiania. Sytuacje, które należy wziąć pod uwagę:

* Podczas próby otwarcia połączenia występuje błąd
* Połączenie bezczynne jest upuszczane po stronie serwera. Podczas próby wydania polecenia nie można go wykonać
* Aktywne połączenie, które aktualnie wykonuje polecenie, zostanie przerwane.

Pierwszy i drugi przypadek są dość proste do przodu do obsługi. Spróbuj ponownie otworzyć połączenie. Po pomyślnym, błąd przejściowy został złagodzony przez system. Możesz ponownie użyć usługi Azure Database dla postgreSQL. Zalecamy czekanie przed ponowieniem próby połączenia. Wycofaj się, jeśli początkowe ponownych prób zakończyć się niepowodzeniem. W ten sposób system może wykorzystać wszystkie dostępne zasoby, aby przezwyciężyć sytuację błędu. Dobrym wzorem do naśladowania jest:

* Odczekaj 5 sekund przed pierwszym ponowieniem próby.
* Dla każdej następującej próby, zwiększyć oczekiwanie wykładniczo, do 60 sekund.
* Ustaw maksymalną liczbę ponownych prób, w którym momencie aplikacja uważa, że operacja nie powiodła się.

Gdy połączenie z aktywną transakcją nie powiedzie się, jest trudniejsze do poprawnego obsługi odzyskiwania poprawnie. Istnieją dwa przypadki: Jeśli transakcja miała charakter tylko do odczytu, można bezpiecznie ponownie otworzyć połączenie i ponowić próbę wykonania transakcji. Jeśli jednak transakcja również zapisuje do bazy danych, należy określić, czy transakcja została wycofana lub jeśli powiodło się przed wystąpieniem błędu przejściowego. W takim przypadku może po prostu nie otrzymał potwierdzenia zatwierdzenia z serwera bazy danych.

Jednym ze sposobów, aby to zrobić, jest wygenerowanie unikatowego identyfikatora na kliencie, który jest używany dla wszystkich ponownych prób. Ten unikatowy identyfikator należy przekazać jako część transakcji do serwera i przechowywać go w kolumnie z unikatowym ograniczeniem. W ten sposób można bezpiecznie ponowić próbę transakcji. Zakończy się pomyślnie, jeśli poprzednia transakcja została wycofana, a wygenerowany przez klienta unikatowy identyfikator nie istnieje jeszcze w systemie. Nie zakończy się niepowodzeniem, wskazując zduplikowane naruszenie klucza, jeśli unikatowy identyfikator był wcześniej przechowywany, ponieważ poprzednia transakcja została pomyślnie ukończona.

Gdy program komunikuje się z usługą Azure Database for PostgreSQL za pośrednictwem oprogramowania pośredniczącego innych firm, zapytaj dostawcę, czy oprogramowanie pośredniczące zawiera logikę ponawiania błędów przejściowych.

Upewnij się, aby przetestować logikę ponawiania. Na przykład spróbuj wykonać kod podczas skalowania w górę lub w dół zasobów obliczeniowych usługi Azure Database dla serwera PostgreSQL. Aplikacja powinna obsługiwać krótki czas przestoju, który napotkany podczas tej operacji bez żadnych problemów.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z połączeniem z usługą Azure Database for PostgreSQL](howto-troubleshoot-common-connection-issues.md)
