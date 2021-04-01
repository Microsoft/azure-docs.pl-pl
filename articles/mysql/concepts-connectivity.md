---
title: Błędy łączności przejściowej — Azure Database for MySQL
description: Dowiedz się, jak obsługiwać błędy połączeń przejściowych i wydajnie łączyć się z Azure Database for MySQL.
keywords: połączenie MySQL, parametry połączenia, problemy z łącznością, błąd przejściowy, błąd połączenia, wydajne łączenie
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89673c14c38947dc5aeb91cacde1eb2755e84138
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542613"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Obsługa przejściowych błędów i wydajne łączenie się z usługą Azure Database for MySQL

W tym artykule opisano, jak obsługiwać błędy przejściowe i wydajnie łączyć się z Azure Database for MySQL.

## <a name="transient-errors"></a>Błędy przejściowe

Błąd przejściowy, nazywany także błędem przejściowym, jest błędem, który zostanie rozwiązany. Zazwyczaj te błędy manifestuje jako połączenie z serwerem bazy danych, który jest usuwany. Nie można również otworzyć nowych połączeń z serwerem. Błędy przejściowe mogą wystąpić na przykład wtedy, gdy wystąpi awaria sprzętu lub sieci. Kolejną przyczyną może być Nowa wersja usługi PaaS, która jest wdrażana. Większość z tych zdarzeń jest automatycznie zmniejszana przez system w mniej niż 60 sekund. Najlepszym rozwiązaniem w przypadku projektowania i opracowywania aplikacji w chmurze jest oczekiwanie na błędy przejściowe. Załóżmy, że mogą wystąpić w dowolnym składniku w dowolnym momencie i mają odpowiednią logikę w celu obsługi takich sytuacji.

## <a name="handling-transient-errors"></a>Obsługa błędów przejściowych

Błędy przejściowe powinny być obsługiwane za pomocą logiki ponawiania. Sytuacje, które należy wziąć pod uwagę:

* Wystąpił błąd podczas próby otwarcia połączenia
* Połączenie bezczynne zostanie usunięte po stronie serwera. Gdy próbujesz wydać polecenie, którego nie można wykonać
* Aktywne połączenie, które aktualnie wykonuje polecenie, jest porzucane.

Pierwszy i drugi przypadek są stosunkowo proste do obsłużenia. Spróbuj ponownie otworzyć połączenie. Po pomyślnym zakończeniu Wystąpił błąd przejściowy przez system. Możesz użyć Azure Database for MySQL ponownie. Zaleca się, aby przed ponowieniem próby nawiązać połączenie. Wycofaj, jeśli początkowe próby nie powiodą się. W ten sposób system może korzystać ze wszystkich dostępnych zasobów, aby przezwyciężyć sytuację błędu. Dobrym wzorcem do wykonania jest:

* Poczekaj 5 sekund przed pierwszym ponowieniem próby.
* Dla każdego kolejnego ponowienia próby Zwiększ czas oczekiwania na 60 sekund.
* Ustaw maksymalną liczbę ponownych prób, w których aplikacja uzna, że operacja nie powiodła się.

Jeśli połączenie z aktywną transakcją zakończy się niepowodzeniem, trudno jest prawidłowo obsłużyć odzyskiwanie. Istnieją dwa przypadki: Jeśli transakcja miała charakter tylko do odczytu, można bezpiecznie ponownie otworzyć połączenie i ponowić próbę wykonania transakcji. Jeśli jednak transakcja była również zapisywany w bazie danych, należy określić, czy transakcja została wycofana, czy też powiodła się przed wykonaniem przejściowego błędu. W takim przypadku użytkownik może dopiero nie otrzymać potwierdzenia zatwierdzenia z serwera bazy danych.

Jednym ze sposobów wykonania tej czynności jest wygenerowanie unikatowego identyfikatora na kliencie używanym do wszystkich ponownych prób. Ten unikatowy identyfikator zostanie przekazany jako część transakcji do serwera i zapisany w kolumnie z unikatowym ograniczeniem. W ten sposób można bezpiecznie ponowić próbę wykonania transakcji. Jeśli poprzednia transakcja została wycofana, a unikatowy identyfikator wygenerowany przez klienta nie istnieje jeszcze w systemie, zostanie wykonana pomyślnie. To nie powiedzie się, jeśli unikatowy identyfikator został wcześniej zapisany, ponieważ Poprzednia transakcja została ukończona pomyślnie.

Gdy program komunikuje się z Azure Database for MySQL za pomocą oprogramowania pośredniczącego innej firmy, należy polecić dostawcę, czy oprogramowanie pośredniczące zawiera logikę ponawiania prób w przypadku błędów przejściowych.

Upewnij się, że test logiki ponawiania prób. Na przykład spróbuj wykonać swój kod podczas skalowania w górę lub w dół zasobów obliczeniowych serwera Azure Database for MySQL. Aplikacja powinna obsługiwać krótki czas przestoju, który został napotkany podczas tej operacji bez żadnych problemów.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Wydajna łączność z Azure Database for MySQL

Połączenia bazy danych są zasobami ograniczonymi, więc efektywne wykorzystanie puli połączeń do uzyskiwania dostępu Azure Database for MySQL optymalizuje wydajność. W poniższej sekcji opisano sposób używania puli połączeń lub połączeń trwałych w celu skuteczniejszego dostępu do Azure Database for MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Dostęp do baz danych za pomocą puli połączeń (zalecane)

Zarządzanie połączeniami z bazą danych może mieć znaczny wpływ na wydajność aplikacji jako całości. Aby zoptymalizować wydajność aplikacji, należy zastanowić się, aby ograniczyć liczbę połączeń i czas ustanawiania połączeń w kluczowych ścieżkach kodu. Zdecydowanie zalecamy użycie puli połączeń z bazą danych lub połączeń trwałych w celu nawiązania połączenia z usługą Azure Database for MySQL. Pule połączeń bazy danych obsługują tworzenie, zarządzanie i przydzielanie połączeń z bazą danych. Gdy program żąda połączenia z bazą danych, ustala priorytet przydziału istniejących bezczynnych połączeń bazy danych zamiast tworzenia nowego połączenia. Po zakończeniu działania programu przy użyciu połączenia z bazą danych połączenie zostanie odzyskane w celu późniejszego użycia, a nie po prostu zamknięte.

Aby uzyskać lepszą ilustrację, ten artykuł zawiera [fragment przykładowego kodu](./sample-scripts-java-connection-pooling.md) , który używa języka Java jako przykładu. Aby uzyskać więcej informacji, zobacz [Apache Common DBCP](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> Serwer konfiguruje mechanizm przekroczenia limitu czasu w celu zamknięcia połączenia, które było w stanie bezczynności przez jakiś czas, aby zwolnić zasoby. Należy pamiętać o skonfigurowaniu systemu weryfikacyjnego w celu zapewnienia skuteczności połączeń trwałych podczas ich używania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie systemów weryfikacyjnych po stronie klienta w celu zapewnienia skuteczności połączeń trwałych](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Dostęp do baz danych przy użyciu połączeń trwałych (zalecane)

Koncepcja połączeń trwałych jest podobna do tej puli połączeń. Zastępowanie krótkich połączeń z trwałymi połączeniami wymaga tylko drobnych zmian w kodzie, ale ma on istotny wpływ na poprawę wydajności w wielu typowych scenariuszach aplikacji.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Dostęp do baz danych przy użyciu mechanizmu oczekiwania i ponawiania prób z krótkimi połączeniami

W przypadku ograniczeń zasobów zdecydowanie zalecamy użycie puli baz danych lub połączeń trwałych w celu uzyskania dostępu do baz danych. Jeśli aplikacja korzysta z krótkich połączeń i wystąpią błędy połączeń, gdy zbliża się górny limit liczby jednoczesnych połączeń, możesz spróbować zaczekać i mechanizm ponawiania prób. Można ustawić odpowiedni czas oczekiwania, krótszy czas oczekiwania po pierwszej próbie. Później możesz spróbować czekać na zdarzenia wiele razy.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Konfigurowanie mechanizmów weryfikacji na klientach w celu potwierdzenia skuteczności połączeń trwałych

Serwer konfiguruje mechanizm przekroczenia limitu czasu w celu zamknięcia połączenia, które było w stanie bezczynności przez jakiś czas, aby zwolnić zasoby. Gdy klient uzyskuje dostęp do bazy danych ponownie, jest to równoznaczne z utworzeniem nowego żądania połączenia między klientem a serwerem. Aby zapewnić skuteczność połączeń podczas procesu ich używania, skonfiguruj mechanizm weryfikacji na kliencie. Jak pokazano w poniższym przykładzie, można użyć puli połączeń Tomcat JDBC, aby skonfigurować ten mechanizm weryfikacji.

Ustawienie parametru TestOnBorrow, gdy istnieje nowe żądanie, pula połączeń automatycznie weryfikuje skuteczność wszelkich dostępnych połączeń bezczynnych. Jeśli takie połączenie jest skuteczne, jego bezpośrednio zwrócone w przeciwnym razie pula połączeń wycofa połączenie. Następnie pula połączeń tworzy nowe, efektywne połączenie i zwraca je. Ten proces zapewnia wydajne uzyskiwanie dostępu do bazy danych. 

Aby uzyskać informacje dotyczące konkretnych ustawień, zobacz [oficjalny dokument dotyczący wprowadzenia puli połączeń JDBC](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). Przede wszystkim trzeba ustawić następujące trzy parametry: TestOnBorrow (wartość true), ValidationQuery (ustawienie to SELECT 1) i ValidationQueryTimeout (ustawienie 1). Konkretny przykładowy kod jest przedstawiony poniżej:

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
