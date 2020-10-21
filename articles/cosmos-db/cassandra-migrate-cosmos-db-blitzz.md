---
title: Migrowanie danych z Cassandra do interfejs API Cassandra Azure Cosmos DB przy użyciu Blitzz
description: Dowiedz się, jak migrować dane z bazy danych Apache Cassandra do Azure Cosmos DB interfejs API Cassandra przy użyciu Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 66314155a8de5036009b8e42bf84a8ae8860d0ea
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278955"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrowanie danych z usługi Cassandra do konta interfejs API Cassandra Azure Cosmos DB przy użyciu usługi Blitzz

Interfejs API Cassandra w Azure Cosmos DB został doskonały wybór dla obciążeń przedsiębiorstwa działających na platformie Apache Cassandra z różnych powodów, takich jak: 

* **Brak nakładów związanych z zarządzaniem i monitorowaniem:** Eliminuje to obciążenie związane z zarządzaniem i monitorowaniem wyposażono ustawień w plikach OS, JVM i YAML oraz ich interakcje.

* **Znaczne oszczędności kosztów:** Koszt można zaoszczędzić za pomocą Azure Cosmos DB, który obejmuje koszt maszyn wirtualnych, przepustowości i wszelkich odpowiednich licencji. Ponadto nie trzeba zarządzać centrami danych, serwerami, magazynem SSD, siecią i kosztami energii elektrycznej. 

* **Możliwość korzystania z istniejącego kodu i narzędzi:** Azure Cosmos DB zapewnia zgodność poziomu protokołu z istniejącymi zestawami SDK i narzędziami Cassandra. Dzięki tej zgodności można używać istniejącej bazy kodu z interfejsem API Cassandra usługi Azure Cosmos DB z prostymi zmianami.

Istnieją różne sposoby migrowania obciążeń bazy danych z jednej platformy do innej. [Blitzz](https://www.blitzz.io) to narzędzie, które oferuje bezpieczny i niezawodny sposób przeprowadzania migracji bez przestojów z różnych baz danych do Azure Cosmos DB. W tym artykule opisano kroki wymagane do migracji danych z bazy danych Apache Cassandra do Azure Cosmos DB interfejs API Cassandra przy użyciu Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Zalety korzystania z usługi Blitzz na potrzeby migracji

Rozwiązanie migracji Blitzz jest zgodne z podejściem krok po kroku w celu migrowania złożonych obciążeń operacyjnych. Poniżej przedstawiono niektóre kluczowe aspekty planu migracji Blitzz o zero przestoju:

* Oferuje automatyczną migrację logiki biznesowej (tabel, indeksów, widoków) z bazy danych Apache Cassandra do Azure Cosmos DB. Nie trzeba tworzyć schematów ręcznie.

* Blitzz oferuje wysoką i równoległą replikację bazy danych. Umożliwia ona synchronizację zarówno platformy źródłowej, jak i docelowej podczas migracji przy użyciu techniki o nazwie Change-Data-Capture (resourceing). Dzięki użyciu funkcji przechwytywania zmian Blitzz w sposób ciągły pobiera strumieniowe zmiany ze źródłowej bazy danych (Apache Cassandra) i stosuje je do docelowej bazy danych (Azure Cosmos DB).

* Jest odporny na uszkodzenia i zapewnia dokładne dostarczanie danych nawet w przypadku awarii sprzętu lub oprogramowania w systemie.

* Zabezpiecza dane podczas przesyłania przy użyciu różnych metod zabezpieczeń, takich jak TLS, szyfrowanie.

## <a name="steps-to-migrate-data"></a>Kroki migracji danych

W tej sekcji opisano kroki wymagane do skonfigurowania programu Blitzz i przeprowadzenia migracji danych z bazy danych Apache Cassandra do Azure Cosmos DB.

1. Na komputerze, na którym planujesz zainstalować Blitzz Replicant, Dodaj certyfikat zabezpieczeń. Ten certyfikat jest wymagany przez Blitzz Replicant w celu nawiązania połączenia TLS z określonym kontem Azure Cosmos DB. Możesz dodać certyfikat, wykonując następujące czynności:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. Instalację Blitzz i pliki binarne można uzyskać, żądając pokazu w [witrynie sieci Web Blitzz](https://www.blitzz.io). Alternatywnie można również wysłać [wiadomość e-mail](mailto:success@blitzz.io) do zespołu.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Pobieranie narzędzia Blitzz Replicant":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Pobieranie narzędzia Blitzz Replicant":::

1. W terminalu interfejsu wiersza polecenia Skonfiguruj konfigurację źródłowej bazy danych. Otwórz plik konfiguracji za pomocą **`vi conf/conn/cassandra.yml`** polecenia i Dodaj rozdzieloną przecinkami listę adresów IP węzłów Cassandra, numer portu, nazwę użytkownika, hasło i wszelkie inne wymagane szczegóły. Poniżej znajduje się przykład zawartości w pliku konfiguracji:

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png" alt-text="Pobieranie narzędzia Blitzz Replicant":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png" alt-text="Pobieranie narzędzia Blitzz Replicant":::

   Po wypełnieniu szczegółów konfiguracji Zapisz i zamknij plik.

1. Opcjonalnie można skonfigurować plik filtru źródłowej bazy danych. Plik filtru określa, które schematy lub tabele mają być migrowane. Otwórz plik konfiguracji za pomocą **`vi filter/cassandra_filter.yml`** polecenia i wprowadź następujące szczegóły konfiguracji:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   Po wypełnieniu szczegółów filtru bazy danych Zapisz i zamknij plik.

1. Następnie zostanie skonfigurowana docelowa Konfiguracja bazy danych. Przed zdefiniowaniem konfiguracji należy [utworzyć konto interfejs API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account) , a następnie utworzyć miejsce i tabelę do przechowywania zmigrowanych danych. Ze względu na to, że Migrowanie z platformy Apache Cassandra do interfejs API Cassandra w Azure Cosmos DB, można użyć tego samego klucza partycji, który był używany z usługą Apache Cassandra.

1. Przed przeprowadzeniem migracji danych Zwiększ przepływność kontenera do wartości wymaganej do szybkiej migracji aplikacji. Na przykład można zwiększyć przepływność do 100000 jednostek ru. Skalowanie przepływności przed rozpoczęciem migracji pomoże Ci w krótszym czasie migrować dane.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Pobieranie narzędzia Blitzz Replicant":::

   Zmniejsz przepływność po zakończeniu migracji. Na podstawie ilości danych przechowywanych i jednostek ru wymaganych dla każdej operacji można oszacować przepływność wymaganą po migracji danych. Aby dowiedzieć się więcej na temat oszacowania wymaganego jednostek ru, zobacz temat [zapewnianie przepływności na kontenerach i bazach danych](set-throughput.md) oraz [szacowanie ru/s przy użyciu artykułów planisty wydajności Azure Cosmos DB](estimate-ru-with-capacity-planner.md) .

1. Pobierz **punkt kontaktu, port, nazwę użytkownika**i **hasło podstawowe** konta Azure Cosmos z okienka **Parametry połączenia** . Te wartości będą używane w pliku konfiguracji.

1. W terminalu interfejsu wiersza polecenia Skonfiguruj docelową konfigurację bazy danych. Otwórz plik konfiguracji za pomocą **`vi conf/conn/cosmosdb.yml`** polecenia i Dodaj rozdzieloną przecinkami listę identyfikatorów URI hosta, numeru portu, nazwy użytkownika, hasła i innych wymaganych parametrów. Poniższy przykład pokazuje zawartość pliku konfiguracji:

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. Następnie Przeprowadź migrację danych przy użyciu Blitzz. Blizz Replicant można uruchomić w trybie **pełnej** lub **migawek** :

   * **Tryb pełny** — w tym trybie Replicant nadal działa po migracji i nasłuchuje wszelkich zmian w źródłowym systemie Apache Cassandra. Jeśli wykryje jakiekolwiek zmiany, zostaną one zreplikowane na docelowym koncie usługi Azure Cosmos w czasie rzeczywistym.

   * **Tryb migawek** — w tym trybie można przeprowadzić migrację schematu i jednorazową replikację danych. W przypadku tej opcji replikacja w czasie rzeczywistym nie jest obsługiwana.

   Za pomocą powyższych dwóch trybów migracja może być przeprowadzana bez przestojów. 

1. Aby przeprowadzić migrację danych z terminalu interfejsu wiersza polecenia Blitzz Replicant, uruchom następujące polecenie:

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   Interfejs użytkownika Replicant pokazuje postęp replikacji. Po wykonaniu operacji migracji schematu i migawki postęp pokazuje 100%. Po zakończeniu migracji można sprawdzić poprawność danych w docelowej bazie danych Azure Cosmos.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png" alt-text="Pobieranie narzędzia Blitzz Replicant":::


1. Ze względu na to, że w przypadku migracji użyto trybu pełnoekranowego, można wykonywać operacje, takie jak wstawianie, aktualizowanie lub usuwanie danych w źródłowej bazie danych Apache Cassandra. Później Sprawdź, czy są replikowane w czasie rzeczywistym w docelowej bazie danych Azure Cosmos. Po migracji należy zmniejszyć przepływność skonfigurowaną dla kontenera usługi Azure Cosmos.

1. Można zatrzymać Replicant dowolnego punktu i uruchomić go ponownie z przełącznikiem **--Resume** . Replikacja zostanie wznowiona od punktu, w którym została zatrzymana, bez naruszania spójności danych. Następujące polecenie pokazuje, jak używać przełącznika Resume.

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

Aby dowiedzieć się więcej na temat migracji danych do lokalizacji docelowej, należy zapoznać się z [prezentacją Blitzz Replicant](https://www.youtube.com/watch?v=fsUhF9LUZmM).

## <a name="next-steps"></a>Następne kroki

* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md) 
* [Najlepsze rozwiązania dotyczące kluczy partycji](partitioning-overview.md#choose-partitionkey)
* [Oszacowanie ru/s przy użyciu artykułów Azure Cosmos DB planisty wydajności](estimate-ru-with-capacity-planner.md)
