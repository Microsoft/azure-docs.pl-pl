---
title: Wprowadzenie do usługi Azure Cosmos DB
description: Zapoznaj się z informacjami na temat usługi Azure Cosmos DB. Ta dostępna w skali światowej, wielomodelowa baza danych zapewnia małe opóźnienia, elastyczną skalowalność i wysoką dostępność oraz oferuje natywną obsługę danych NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2020
ms.openlocfilehash: 012df825f38d292d0b0549701ad90334e3e30bb0
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637017"
---
# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB — Zapraszamy!
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Współczesne aplikacje muszą zapewniać szybki czas reakcji i zawsze działać w trybie online. Aby zapewnić małe opóźnienia i wysoką dostępność, wystąpienia tych aplikacji powinny być wdrażane w centrach danych, które są blisko ich użytkowników. Aplikacje muszą reagować w czasie rzeczywistym na duże zmiany użycia w godzinach szczytu, przechowywać coraz większe ilości danych i udostępniać je użytkownikom w ciągu kilku milisekund.

Azure Cosmos DB to w pełni zarządzana baza danych NoSQL do tworzenia nowoczesnych aplikacji. Czas odpowiedzi z jedną cyfrą (w milisekundach) oraz automatyczna i szybka skalowalność — szybkość gwarancji w dowolnej skali. Ciągłość biznesowa jest zapewniana z zachowaniem [umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) i zabezpieczeniami klasy korporacyjnej. Tworzenie aplikacji jest szybsze i wydajniejsze dzięki gotowe dystrybucji danych z wieloma wzorcami w dowolnym miejscu na świecie, interfejsów API i zestawów SDK open source dla popularnych języków. Jako w pełni zarządzana usługa Azure Cosmos DB przeprowadzi administrację bazy danych z użyciem funkcji automatycznego zarządzania, aktualizacji i poprawek. Obsługuje ona również zarządzanie pojemnością przy użyciu ekonomicznych opcji bezserwerowych i skalowania automatycznego, które reagują na potrzeby aplikacji, aby pasowały do pojemności z zapotrzebowaniem.

Możesz bezpłatnie [Wypróbować Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure, bezpłatnie i zobowiązań lub korzystać z [bezpłatnej warstwy Azure Cosmos DB](optimize-dev-test.md#azure-cosmos-db-free-tier) , aby uzyskać konto z pierwszym 400 ru/s i 5 GB wolnego miejsca w magazynie.

> [!div class="nextstepaction"]
> [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB to w pełni zarządzana baza danych NoSQL do tworzenia nowoczesnych aplikacji." border="false":::

## <a name="key-benefits"></a>Najważniejsze korzyści

### <a name="guaranteed-speed-at-any-scale"></a>Gwarantowana szybkość na dowolnym poziomie

Uzyskaj niezrównaną umowę SLA — szybkość i przepływność [kopii zapasowych](https://azure.microsoft.com/support/legal/sla/cosmos-db) , szybki dostęp globalny i błyskawiczną elastyczność.

- Dostęp w czasie rzeczywistym z możliwością szybkiego odczytu i zapisu, globalnie i przepływność i spójność [umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)
- Zapisy wieloregionowe i dystrybucja danych do dowolnego regionu platformy Azure z kliknięciem przycisku.
- Niezależne i elastyczne skalowanie magazynu i przepływności w dowolnym regionie platformy Azure — nawet w przypadku nieprzewidywalnej serii ruchu — w przypadku nieograniczonego skalowania na całym świecie.

### <a name="simplified-application-development"></a>Uproszczone opracowywanie aplikacji

Szybkie tworzenie za pomocą interfejsów API Open Source, wielu zestawów SDK, danych bez schematu i analizy No-ETL dla danych operacyjnych.

- Głęboko zintegrowane z kluczowymi usługami platformy Azure używanymi w nowoczesnych aplikacjach do tworzenia aplikacji (w chmurze), takich jak Azure Functions, IoT Hub, AKS (Azure Kubernetes Service), App Service i innych.
- Wybieraj spośród wielu interfejsów API bazy danych, w tym interfejsu API Native Core (SQL), interfejsu API dla MongoDB, interfejs API Cassandra, Gremlin API i interfejs API tabel.
- Twórz aplikacje w oparciu o rdzeń (SQL) API przy użyciu wybranych przez siebie języków z zestawami SDK dla platform .NET, Java, Node.js i Python. Lub wybrane sterowniki dla dowolnego z innych interfejsów API bazy danych.
- Uruchom analizę No-ETL na danych operacyjnych niemal w czasie rzeczywistym przechowywanych w Azure Cosmos DB za pomocą usługi Azure Synapse Analytics.
- Źródło zmian ułatwia śledzenie i zarządzanie zmianami w kontenerach baz danych oraz Tworzenie zdarzeń wyzwalanych przy użyciu Azure Functions.
- Usługa bez schematu Azure Cosmos DB automatycznie indeksuje wszystkie dane, niezależnie od modelu danych, do dostarczania szybkich zapytań niezwykle.

### <a name="mission-critical-ready"></a>Gotowość na sytuacje krytyczne dla działalności firmy

Gwarantujej ciągłość działania, dostępność na 99,999% i zabezpieczenia na poziomie przedsiębiorstwa dla każdej aplikacji.

- Azure Cosmos DB oferuje kompleksowy pakiet [umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) , w tym na całym świecie dostępność wiodących w branży.
- Łatwo Dystrybuuj dane do dowolnego regionu platformy Azure z automatyczną replikacją danych. Korzystaj z nieprzerwanego przestoju przy użyciu zapisu w ramach wieloregionu lub celu punktu odzyskiwania 0 w przypadku używania silnej spójności
- Korzystaj z szyfrowania klasy korporacyjnej przy użyciu kluczy samoobsługowych.
- Kontrola dostępu oparta na rolach na platformie Azure zapewnia bezpieczeństwo danych i oferuje precyzyjną kontrolę.

### <a name="fully-managed-and-cost-effective"></a>W pełni zarządzane i ekonomiczne

Kompleksowe zarządzanie bazami danych, bez serwera i skalowanie automatyczne zgodne z wymaganiami aplikacji i całkowitymi kosztami posiadania

- W pełni zarządzana usługa bazy danych. Automatyczna, bez dotyku, konserwacji, poprawek i aktualizacji, oszczędność czasu deweloperów i pieniędzy.
- Ekonomiczne opcje dla nieprzewidywalnych lub sporadycznych obciążeń dowolnego rozmiaru i skali, dzięki czemu deweloperzy mogą szybko rozpocząć pracę bez konieczności planowania wydajności ani zarządzania nią.
- Model bezserwerowy oferuje obciążenia, które umożliwiają automatyczne i dynamiczne zarządzanie ruchem na żądanie.
- Automatyczne skalowanie automatycznej przepustowości i natychmiastowe skalowanie pojemności dla nieprzewidywalnych obciążeń przy zachowaniu [umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Rozwiązania, które korzystają z zalet usługi Azure Cosmos DB

Wszystkie [aplikacje sieci Web, mobilne, gry i IoT](use-cases.md) , które wymagają obsługi dużych ilości danych, odczytów i zapisów w [skali globalnej](distribute-data-globally.md) z niemal rzeczywistym czasem odpowiedzi dla różnych danych, będą korzystać Cosmos DB z [gwarantowanej wysokiej dostępności](https://azure.microsoft.com/support/legal/sla/cosmos-db/), wysokiej przepustowości, małych opóźnień i możliwość dostosowania spójności. Dowiedz się, jak można używać usługi Azure Cosmos DB do kompilowania rozwiązań z zakresu [IoT i telematyki](use-cases.md#iot-and-telematics), [sprzedaży i marketingu](use-cases.md#retail-and-marketing), [gier](use-cases.md#gaming) oraz [aplikacji mobilnych i internetowych](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z usługą Azure Cosmos DB, korzystając z jednego z naszych przewodników:

- [Rozpoczynanie pracy z interfejsem SQL usługi Azure Cosmos DB](create-sql-api-dotnet.md)
- [Rozpoczynanie pracy z interfejsem MongoDB usługi Azure Cosmos DB](create-mongodb-nodejs.md)
- [Rozpoczynanie pracy z interfejsem API rozwiązania Cassandra usługi Azure Cosmos DB](create-cassandra-dotnet.md)
- [Rozpoczynanie pracy z interfejsem API programu Gremlin usługi Azure Cosmos DB](create-graph-dotnet.md)
- [Rozpoczynanie pracy z interfejsem API tabeli usługi Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Wypróbuj usługę Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/)