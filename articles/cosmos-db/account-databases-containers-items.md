---
title: Model zasobów usługi Azure Cosmos DB
description: W tym artykule opisano Azure Cosmos DB model zasobów obejmujący konto, bazę danych, kontener i elementy usługi Azure Cosmos. Obejmuje on również hierarchię tych elementów na koncie usługi Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 4ed881b74f240946d98d9868344c898d3e9a9dad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627352"
---
# <a name="azure-cosmos-db-resource-model"></a>Model zasobów usługi Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB to w pełni zarządzana platforma jako usługa (PaaS). Aby rozpocząć korzystanie z Azure Cosmos DB, należy najpierw utworzyć konto usługi Azure Cosmos w ramach subskrypcji i baz danych platformy Azure, kontenerów, elementów w tym obszarze. W tym artykule opisano model zasobów Azure Cosmos DB i różne jednostki w hierarchii modeli zasobów.

Konto usługi Azure Cosmos jest podstawową jednostką dystrybucji globalnej i wysokiej dostępności. Twoje konto usługi Azure Cosmos zawiera unikatową nazwę DNS, a konto można zarządzać przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure lub przy użyciu różnych zestawów SDK specyficznych dla języka. Aby uzyskać więcej informacji, zobacz [jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md). Aby globalnie rozpowszechnić dane i przepływność w wielu regionach platformy Azure, możesz w dowolnym momencie dodawać i usuwać regiony platformy Azure. Konto można skonfigurować tak, aby miało pojedynczy region lub wiele regionów zapisu. Aby uzyskać więcej informacji, zobacz [jak dodawać i usuwać regiony platformy Azure na koncie](how-to-manage-database-account.md). Można skonfigurować domyślny poziom [spójności](consistency-levels.md) na koncie.

## <a name="elements-in-an-azure-cosmos-account"></a>Elementy na koncie usługi Azure Cosmos

Kontener usługi Azure Cosmos jest podstawową jednostką skalowalności. Można praktycznie mieć nieograniczoną przepływność (RU/s) i magazyn w kontenerze. Azure Cosmos DB przezroczyste partycjonowanie kontenera przy użyciu klucza partycji logicznej, który można określić w celu elastycznego skalowania zainicjowanej przepływności i magazynu.

Obecnie można utworzyć maksymalnie 50 kont usługi Azure Cosmos w ramach subskrypcji platformy Azure (jest to limit elastyczny, który można zwiększyć za pośrednictwem żądania pomocy technicznej). Pojedyncze konto usługi Azure Cosmos może praktycznie zarządzać nieograniczoną ilością danych i zainicjowaną przepływność. Aby zarządzać danymi i elastyczną przepływność, można utworzyć co najmniej jedną bazę danych usługi Azure Cosmos na Twoim koncie i w ramach tej bazy danych. można utworzyć jeden lub więcej kontenerów. Na poniższej ilustracji przedstawiono hierarchię elementów na koncie usługi Azure Cosmos:

:::image type="content" source="./media/account-databases-containers-items/hierarchy.png" alt-text="Hierarchia konta usługi Azure Cosmos" border="false":::

Po utworzeniu konta w ramach subskrypcji platformy Azure Możesz zarządzać danymi na swoim koncie przez tworzenie baz danych, kontenerów i elementów. 

Na poniższej ilustracji przedstawiono hierarchię różnych jednostek w ramach konta Azure Cosmos DB:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Jednostki kont usługi Azure Cosmos" border="false":::

## <a name="azure-cosmos-databases"></a>Bazy danych usługi Azure Cosmos

Na koncie można utworzyć jedną lub wiele baz danych usługi Azure Cosmos. Baza danych jest analogiczna do przestrzeni nazw. Baza danych to jednostka zarządzania dla zestawu kontenerów usługi Azure Cosmos. W poniższej tabeli przedstawiono sposób mapowania bazy danych do różnych jednostek specyficznych dla interfejsu API:

| Jednostka usługi Azure Cosmos | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- |
|Baza danych Cosmos Azure | baza danych | Przestrzeń kluczy | baza danych | baza danych | NA |

> [!NOTE]
> Przy tworzeniu pierwszej tabeli przy użyciu kont interfejs API tabel domyślna baza danych zostanie automatycznie utworzona na koncie usługi Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operacje w bazie danych Azure Cosmos

Możesz korzystać z usługi Azure Cosmos Database za pomocą interfejsów API usługi Azure Cosmos, zgodnie z opisem w poniższej tabeli:

| Operacja | Interfejs wiersza polecenia platformy Azure | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- |
|Wylicz wszystkie bazy danych| Tak | Tak | Tak (baza danych jest zamapowana na przestrzeń kluczy) | Tak | Nie dotyczy | NA |
|Odczytaj bazę danych| Tak | Tak | Tak (baza danych jest zamapowana na przestrzeń kluczy) | Tak | Nie dotyczy | NA |
|Utwórz nową bazę danych| Tak | Tak | Tak (baza danych jest zamapowana na przestrzeń kluczy) | Tak | Nie dotyczy | NA |
|Aktualizowanie bazy danych| Tak | Tak | Tak (baza danych jest zamapowana na przestrzeń kluczy) | Tak | Nie dotyczy | NA |

## <a name="azure-cosmos-containers"></a>Kontenery usługi Azure Cosmos

Kontener usługi Azure Cosmos jest jednostką skalowalności dla zainicjowanej przepływności i magazynu. Kontener jest podzielony na partycje w poziomie, a następnie replikowany w wielu regionach. Elementy dodawane do kontenera są automatycznie pogrupowane na partycje logiczne, które są dystrybuowane między partycjami fizycznymi w oparciu o klucz partycji. Przepływność na kontenerze jest równomiernie rozłożona na partycje fizyczne. Aby dowiedzieć się więcej na temat partycjonowania i kluczy partycji, zobacz [dane partycji](partitioning-overview.md). 

Podczas tworzenia kontenera można skonfigurować przepływność w jednym z następujących trybów:

* **Dedykowany Tryb przepływności**: przepustowość zainicjowana w kontenerze jest zarezerwowana wyłącznie dla tego kontenera i jest obsługiwana przez umowy SLA. Aby dowiedzieć się więcej, zobacz [jak zainicjować przepływność na kontenerze](how-to-provision-container-throughput.md).

* **Udostępniony Tryb przepływności**: te kontenery udostępniają przepływność administracyjną z innymi kontenerami w tej samej bazie danych (z wyjątkiem kontenerów, które zostały skonfigurowane z dedykowaną elastyczną przepustowością). Innymi słowy, zainicjowana przepływność w bazie danych jest współużytkowana przez wszystkie kontenery "udostępnione przepływność". Aby dowiedzieć się więcej, zobacz [jak zainicjować przepływność w bazie danych](how-to-provision-database-throughput.md).

> [!NOTE]
> Można skonfigurować udostępnioną i dedykowaną przepływność tylko podczas tworzenia bazy danych i kontenera. Aby przełączyć się z trybu dedykowanej przepływności do trybu udostępnionej przepływności (i na odwrót) po utworzeniu kontenera, należy utworzyć nowy kontener i zmigrować dane do nowego kontenera. Dane można migrować za pomocą funkcji Azure Cosmos DB ze źródłem zmian.

Kontener usługi Azure Cosmos może być elastycznie skalowany, niezależnie od tego, czy tworzysz kontenery za pomocą dedykowanych, czy udostępnionych trybów przepływności.

Kontener jest kontenerem Schema-niezależny od elementów. Elementy w kontenerze mogą zawierać dowolne schematy. Na przykład element, który reprezentuje osobę i element reprezentujący samochód, można umieścić w tym *samym kontenerze*. Domyślnie wszystkie elementy dodawane do kontenera są automatycznie indeksowane bez konieczności jawnego indeksowania lub zarządzania schematem. Można dostosować zachowanie indeksowania przez skonfigurowanie [zasad indeksowania](index-overview.md) w kontenerze. 

Można ustawić [czas wygaśnięcia (TTL)](time-to-live.md) dla wybranych elementów w kontenerze lub dla całego kontenera, aby bezpiecznie przeczyścić te elementy z systemu. Azure Cosmos DB automatycznie usuwa elementy po ich wygaśnięciu. Gwarantuje również, że zapytanie wykonywane na kontenerze nie zwraca elementów wygasłych w ramach ustalonego powiązania. Aby dowiedzieć się więcej, zobacz [Konfigurowanie czasu wygaśnięcia w kontenerze](how-to-time-to-live.md).

Możesz użyć [kanału informacyjnego zmiany](change-feed.md) , aby subskrybować dziennik operacji, który jest zarządzany dla każdej partycji logicznej kontenera. Kanał informacyjny zmiany zapewnia dziennik wszystkich aktualizacji wykonanych w kontenerze wraz z obrazami przed i po. Aby uzyskać więcej informacji, zobacz [Tworzenie reaktywnych aplikacji przy użyciu źródła zmian](serverless-computing-database.md). Możesz również skonfigurować czas przechowywania dla kanału informacyjnego zmiany za pomocą zasad źródła zmian w kontenerze.

Można rejestrować [procedury składowane, wyzwalacze, funkcje zdefiniowane przez użytkownika (UDF)](stored-procedures-triggers-udfs.md)i [procedury scalania](how-to-manage-conflicts.md) dla kontenera.

Możesz określić [unikatowe ograniczenie klucza](unique-keys.md) w kontenerze usługi Azure Cosmos. Tworząc unikatowe Zasady kluczy, można zapewnić unikatowość jednej lub więcej wartości na klucz partycji logicznej. W przypadku utworzenia kontenera przy użyciu unikatowych zasad kluczy nie można utworzyć nowych lub zaktualizowanych elementów z wartościami, które duplikują wartości określone przez ograniczenie UNIQUE Key. Aby dowiedzieć się więcej, zobacz [unikalne ograniczenia klucza](unique-keys.md).

Kontener jest wyspecjalizowany dla jednostek specyficznych dla interfejsu API, jak pokazano w poniższej tabeli:

| Jednostka usługi Azure Cosmos | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- |
|Kontener usługi Azure Cosmos | Kontener | Tabela | Kolekcja | Graph | Tabela |

> [!NOTE]
> Podczas tworzenia kontenerów upewnij się, że nie utworzysz dwóch kontenerów o takiej samej nazwie, ale o innej wielkości liter. Wynika to z faktu, że niektóre części platformy Azure nie uwzględniają wielkości liter. może to spowodować pomylenie/kolizję danych telemetrycznych i akcji w kontenerach z takimi nazwami.

### <a name="properties-of-an-azure-cosmos-container"></a>Właściwości kontenera usługi Azure Cosmos

Kontener usługi Azure Cosmos ma zestaw właściwości zdefiniowanych przez system. W zależności od używanego interfejsu API niektóre właściwości mogą nie być bezpośrednio uwidocznione. W poniższej tabeli opisano listę właściwości zdefiniowanych przez system:

| Właściwość zdefiniowana przez system | Generowane przez system lub użytkownik — konfigurowalne | Przeznaczenie | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Generowane przez system | Unikatowy identyfikator kontenera | Tak | Nie | Nie | Nie | Nie |
|\_element ETag | Generowane przez system | Tag jednostki używany do optymistycznej kontroli współbieżności | Tak | Nie | Nie | Nie | Nie |
|\_TS | Generowane przez system | Ostatnia aktualizacja sygnatury czasowej kontenera | Tak | Nie | Nie | Nie | Nie |
|\_automatycznej | Generowane przez system | Adres URI adresu URL kontenera | Tak | Nie | Nie | Nie | Nie |
|identyfikator | Użytkownik — konfigurowalne | Unikatowa nazwa kontenera zdefiniowana przez użytkownika | Tak | Tak | Tak | Tak | Tak |
|indexingPolicy | Użytkownik — konfigurowalne | Zapewnia możliwość zmiany ścieżki indeksu, typu indeksu i trybu indeksowania | Tak | Nie | Nie | Nie | Tak |
|TimeToLive | Użytkownik — konfigurowalne | Zapewnia możliwość automatycznego usuwania elementów z kontenera po upływie określonego czasu. Aby uzyskać szczegółowe informacje, zobacz [Time to Live](time-to-live.md). | Tak | Nie | Nie | Nie | Tak |
|changeFeedPolicy | Użytkownik — konfigurowalne | Służy do odczytywania zmian wprowadzonych do elementów w kontenerze. Aby uzyskać szczegółowe informacje, zobacz [Zmienianie źródła danych](change-feed.md). | Tak | Nie | Nie | Nie | Tak |
|uniqueKeyPolicy | Użytkownik — konfigurowalne | Służy do zapewnienia unikatowości jednej lub więcej wartości w partycji logicznej. Aby uzyskać więcej informacji, zobacz [unikalne ograniczenia klucza](unique-keys.md). | Tak | Nie | Nie | Nie | Tak |

### <a name="operations-on-an-azure-cosmos-container"></a>Operacje na kontenerze usługi Azure Cosmos

Kontener usługi Azure Cosmos obsługuje następujące operacje, gdy używasz dowolnego z interfejsów API platformy Azure Cosmos:

| Operacja | Interfejs wiersza polecenia platformy Azure | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- |
| Wyliczanie kontenerów w bazie danych | Tak | Tak | Tak | Tak | Nie dotyczy | NA |
| Odczytywanie kontenera | Tak | Tak | Tak | Tak | Nie dotyczy | NA |
| Utwórz nowy kontener | Tak | Tak | Tak | Tak | Nie dotyczy | NA |
| Aktualizowanie kontenera | Tak | Tak | Tak | Tak | Nie dotyczy | NA |
| Usuwanie kontenera | Tak | Tak | Tak | Tak | Nie dotyczy | NA |

## <a name="azure-cosmos-items"></a>Elementy usługi Azure Cosmos

W zależności od używanego interfejsu API element platformy Azure Cosmos może reprezentować dokument w kolekcji, wiersz w tabeli lub węzeł lub krawędź wykresu. W poniższej tabeli przedstawiono mapowanie jednostek specyficznych dla interfejsu API do elementu usługi Azure Cosmos:

| Jednostka Cosmos | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- |
|Element usługi Azure Cosmos | Element | Wiersz | Dokument | Węzeł lub krawędź | Element |

### <a name="properties-of-an-item"></a>Właściwości elementu

Każdy element usługi Azure Cosmos ma następujące właściwości zdefiniowane przez system. W zależności od używanego interfejsu API niektóre z nich mogą nie być bezpośrednio uwidocznione.

| Właściwość zdefiniowana przez system | Generowane przez system lub użytkownik — konfigurowalne| Przeznaczenie | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Generowane przez system | Unikatowy identyfikator elementu | Tak | Nie | Nie | Nie | Nie |
|\_element ETag | Generowane przez system | Tag jednostki używany do optymistycznej kontroli współbieżności | Tak | Nie | Nie | Nie | Nie |
|\_TS | Generowane przez system | Sygnatura czasowa ostatniej aktualizacji elementu | Tak | Nie | Nie | Nie | Nie |
|\_automatycznej | Generowane przez system | Adres URI adresu dla elementu | Tak | Nie | Nie | Nie | Nie |
|identyfikator | Dowolny | Unikatowa nazwa zdefiniowana przez użytkownika w partycji logicznej. | Tak | Tak | Tak | Tak | Tak |
|Dowolne właściwości zdefiniowane przez użytkownika | Zdefiniowane przez użytkownika | Właściwości zdefiniowane przez użytkownika reprezentowane w reprezentacji natywnej interfejsu API (w tym JSON, BSON i CQL) | Tak | Tak | Tak | Tak | Tak |

> [!NOTE]
> Unikatowość `id` właściwości jest wymuszana tylko w obrębie każdej partycji logicznej. Wiele dokumentów może mieć taką samą `id` Właściwość z różnymi wartościami klucza partycji.

### <a name="operations-on-items"></a>Operacje na elementach

Elementy usługi Azure Cosmos obsługują następujące operacje. Aby wykonać operacje, można użyć dowolnego interfejsu API usługi Azure Cosmos.

| Operacja | Interfejs wiersza polecenia platformy Azure | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- |
| Wstawianie, zastępowanie, usuwanie, Upsert, odczyt | Nie | Tak | Tak | Tak | Tak | Tak |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać kontem usługi Azure Cosmos i innymi pojęciami:

* [Jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Dystrybucja globalna](distribute-data-globally.md)
* [Poziomy spójności](consistency-levels.md)
* [Punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos](how-to-configure-vnet-service-endpoint.md)
* [IP-Zapora dla konta usługi Azure Cosmos](how-to-configure-firewall.md)
* [Jak dodawać i usuwać regiony platformy Azure na koncie usługi Azure Cosmos](how-to-manage-database-account.md)
* [Azure Cosmos DB umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
