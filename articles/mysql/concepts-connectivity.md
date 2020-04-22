---
title: Błędy łączności przejściowej — usługa Azure Database dla mysql
description: Dowiedz się, jak radzić sobie z błędami łączności przejściowej i efektywnie łączyć się z usługą Azure Database for MySQL.
keywords: połączenie mysql,parametry połączenia,problemy z łącznością,błąd przejściowy,błąd połączenia,łączenie się wydajnie
author: jasonwhowell
ms.author: jasonh
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4f9101b4108f5512ee9779f4633845b34fdfad5a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767866"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Obsługa błędów przejściowych i efektywne łączenie się z usługą Azure Database for MySQL

W tym artykule opisano sposób obsługi błędów przejściowych i efektywnego łączenia się z usługą Azure Database for MySQL.

## <a name="transient-errors"></a>Błędy przejściowe

Błąd przejściowy, znany również jako błąd przejściowy, jest błędem, który sam się rozwiąże. Najczęściej te błędy manifestują się jako połączenie z serwerem bazy danych, który jest odrzucany. Nie można również otworzyć nowych połączeń z serwerem. Błędy przejściowe mogą wystąpić na przykład w przypadku awarii sprzętu lub sieci. Innym powodem może być nowa wersja usługi PaaS, która jest wdrażana. Większość z tych zdarzeń są automatycznie złagodzone przez system w mniej niż 60 sekund. Najlepszym rozwiązaniem w zakresie projektowania i tworzenia aplikacji w chmurze jest oczekiwanie błędów przejściowych. Załóżmy, że mogą się zdarzyć w dowolnym składniku w dowolnym momencie i mieć odpowiednią logikę w miejscu do obsługi tych sytuacji.

## <a name="handling-transient-errors"></a>Obsługa błędów przejściowych

Błędy przejściowe powinny być obsługiwane przy użyciu logiki ponawiania. Sytuacje, które należy wziąć pod uwagę:

* Podczas próby otwarcia połączenia występuje błąd
* Połączenie bezczynne jest upuszczane po stronie serwera. Podczas próby wydania polecenia nie można go wykonać
* Aktywne połączenie, które aktualnie wykonuje polecenie, zostanie przerwane.

Pierwszy i drugi przypadek są dość proste do przodu do obsługi. Spróbuj ponownie otworzyć połączenie. Po pomyślnym, błąd przejściowy został złagodzony przez system. Możesz ponownie użyć usługi Azure Database dla MySQL. Zalecamy czekanie przed ponowieniem próby połączenia. Wycofaj się, jeśli początkowe ponownych prób zakończyć się niepowodzeniem. W ten sposób system może wykorzystać wszystkie dostępne zasoby, aby przezwyciężyć sytuację błędu. Dobrym wzorem do naśladowania jest:

* Odczekaj 5 sekund przed pierwszym ponowieniem próby.
* Dla każdej następującej próby, zwiększyć oczekiwanie wykładniczo, do 60 sekund.
* Ustaw maksymalną liczbę ponownych prób, w którym momencie aplikacja uważa, że operacja nie powiodła się.

Gdy połączenie z aktywną transakcją nie powiedzie się, jest trudniejsze do poprawnego obsługi odzyskiwania poprawnie. Istnieją dwa przypadki: Jeśli transakcja miała charakter tylko do odczytu, można bezpiecznie ponownie otworzyć połączenie i ponowić próbę wykonania transakcji. Jeśli jednak transakcja również zapisuje do bazy danych, należy określić, czy transakcja została wycofana lub jeśli powiodło się przed wystąpieniem błędu przejściowego. W takim przypadku może po prostu nie otrzymał potwierdzenia zatwierdzenia z serwera bazy danych.

Jednym ze sposobów, aby to zrobić, jest wygenerowanie unikatowego identyfikatora na kliencie, który jest używany dla wszystkich ponownych prób. Ten unikatowy identyfikator należy przekazać jako część transakcji do serwera i przechowywać go w kolumnie z unikatowym ograniczeniem. W ten sposób można bezpiecznie ponowić próbę transakcji. Zakończy się pomyślnie, jeśli poprzednia transakcja została wycofana, a unikatowy identyfikator wygenerowany przez klienta nie istnieje jeszcze w systemie. Nie zakończy się niepowodzeniem, wskazując zduplikowane naruszenie klucza, jeśli unikatowy identyfikator był wcześniej przechowywany, ponieważ poprzednia transakcja została pomyślnie ukończona.

Gdy program komunikuje się z usługą Azure Database for MySQL za pośrednictwem oprogramowania pośredniczącego innych firm, zapytaj dostawcę, czy oprogramowanie pośredniczące zawiera logikę ponawiania błędów przejściowych.

Upewnij się, aby przetestować logikę ponawiania. Na przykład spróbuj wykonać kod podczas skalowania w górę lub w dół zasobów obliczeniowych usługi Azure Database dla serwera MySQL. Aplikacja powinna obsługiwać krótki czas przestoju, który napotkany podczas tej operacji bez żadnych problemów.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Efektywne łączenie się z usługą Azure Database for MySQL

Połączenia z bazą danych są ograniczonym zasobem, więc efektywne wykorzystanie buforowania połączeń w celu uzyskania dostępu do bazy danych Azure Database dla mysql optymalizuje wydajność. W poniższej sekcji wyjaśniono, jak używać buforowania połączeń lub połączeń trwałych, aby skuteczniej uzyskiwać dostęp do usługi Azure Database for MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Dostęp do baz danych przy użyciu buforowania połączeń (zalecane)

Zarządzanie połączeniami z bazą danych może mieć znaczący wpływ na wydajność aplikacji jako całości. Aby zoptymalizować wydajność aplikacji, celem powinno być zmniejszenie liczby połączeń są ustanawiane i czas na ustanawianie połączeń w ścieżkach kodu klucza. Zdecydowanie zaleca się używanie buforowania połączeń z bazą danych lub połączeń trwałych w celu nawiązania połączenia z usługą Azure Database for MySQL. Buforowanie połączeń z bazą danych obsługuje tworzenie, zarządzanie i alokację połączeń z bazą danych. Gdy program żąda połączenia z bazą danych, nadaje priorytet alokacji istniejących bezczynnych połączeń bazy danych, a nie tworzeniu nowego połączenia. Po zakończeniu korzystania z połączenia z bazą danych połączenie jest odzyskiwane w ramach przygotowań do dalszego użycia, a nie po prostu zamykane.

Aby uzyskać lepszą ilustrację, w tym artykule [przedstawiono przykładowy kod,](./sample-scripts-java-connection-pooling.md) który używa języka JAVA jako przykładu. Aby uzyskać więcej informacji, zobacz [Apache common DBCP](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> Serwer konfiguruje mechanizm limitu czasu, aby zamknąć połączenie, które było w stanie bezczynnym przez pewien czas, aby zwolnić zasoby. Należy skonfigurować system weryfikacji, aby zapewnić skuteczność trwałych połączeń podczas korzystania z nich. Aby uzyskać więcej informacji, zobacz [Konfigurowanie systemów weryfikacji po stronie klienta, aby zapewnić skuteczność połączeń trwałych](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Dostęp do baz danych przy użyciu połączeń trwałych (zalecane)

Pojęcie połączeń trwałych jest podobny do buforowania połączeń. Zastępowanie krótkich połączeń połączeniami trwałymi wymaga tylko niewielkich zmian w kodzie, ale ma duży wpływ na poprawę wydajności w wielu typowych scenariuszach aplikacji.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Dostęp do baz danych przy użyciu mechanizmu oczekiwania i ponawiania prób przy krótkich połączeniach

Jeśli masz ograniczenia zasobów, zdecydowanie zaleca się użycie buforowania bazy danych lub połączeń trwałych w celu uzyskania dostępu do baz danych. Jeśli aplikacja używa krótkich połączeń i występują błędy połączeń, gdy zbliżasz się do górnego limitu liczby równoczesnych połączeń, możesz spróbować poczekać i ponowić próbę. Można ustawić odpowiedni czas oczekiwania, z krótszym czasem oczekiwania po pierwszej próbie. Następnie możesz spróbować czekać na zdarzenia wiele razy.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Konfigurowanie mechanizmów weryfikacji u klientów w celu potwierdzenia skuteczności trwałych połączeń

Serwer konfiguruje mechanizm limitu czasu, aby zamknąć połączenie, które było w stanie bezczynnym przez pewien czas, aby zwolnić zasoby. Gdy klient ponownie uzyskuje dostęp do bazy danych, jest to równoważne tworzenie nowego żądania połączenia między klientem a serwerem. Aby zapewnić skuteczność połączeń podczas korzystania z nich, należy skonfigurować mechanizm weryfikacji na kliencie. Jak pokazano w poniższym przykładzie, można użyć tomcat JDBC buforowania połączeń, aby skonfigurować ten mechanizm weryfikacji.

Ustawiając TestOnBorrow parametr, gdy istnieje nowe żądanie, puli połączeń automatycznie weryfikuje skuteczność wszystkich dostępnych bezczynnych połączeń. Jeśli takie połączenie jest skuteczne, jego bezpośrednio zwrócona pula połączeń w przeciwnym razie wycofuje połączenie. Pula połączeń następnie tworzy nowe efektywne połączenie i zwraca go. Ten proces zapewnia, że baza danych jest dostępna wydajnie. 

Aby uzyskać informacje na temat określonych ustawień, zobacz [oficjalny dokument wprowadzający puli połączeń JDBC](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). Należy przede wszystkim ustawić następujące trzy parametry: TestOnBorrow (ustawiona na true), ValidationQuery (ustawiona na SELECT 1) i ValidationQueryTimeout (ustawiona na 1). Szczegółowy przykładowy kod jest pokazany poniżej:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z połączeniem z usługą Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)
