---
title: Optymalizacja pod kątem programowania i testowania w Azure Cosmos DB
description: W tym artykule wyjaśniono, jak Azure Cosmos DB oferuje wiele opcji tworzenia i testowania usługi bezpłatnie.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 3ddae808fbb2e3dcfe20909c8b3d0c5a20bb04bd
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247526"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optymalizacja kosztów tworzenia i testowania w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

W tym artykule opisano różne opcje umożliwiające korzystanie z Azure Cosmos DB na potrzeby tworzenia i testowania bez ponoszenia kosztów, a także technik optymalizacji kosztów na kontach deweloperskich i testowych.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulator Azure Cosmos DB (wersja do pobrania lokalnego)

[Azure Cosmos DB emulator](local-emulator.md) to lokalna wersja do pobrania, która naśladuje Azure Cosmos DB usługi w chmurze. Możesz pisać i testować kod, który używa interfejsów API Azure Cosmos DB, nawet jeśli nie masz połączenia sieciowego i nie poniesiesz żadnych kosztów. Emulator Azure Cosmos DB zapewnia środowisko lokalne do celów deweloperskich z wysoką dokładnością do usługi w chmurze. Możesz tworzyć i testować aplikację lokalnie, bez tworzenia subskrypcji platformy Azure. Gdy wszystko będzie gotowe do wdrożenia aplikacji w chmurze, zaktualizuj parametry połączenia w celu nawiązania połączenia z punktem końcowym Azure Cosmos DB w chmurze, a żadne inne modyfikacje nie są wymagane. Możesz również skonfigurować potok ciągłej integracji/ciągłego wdrażania [za pomocą zadania kompilacji emulatora Azure Cosmos DB](tutorial-setup-ci-cd.md) w usłudze Azure DevOps, aby uruchomić testy. Możesz zacząć od odwiedzenia artykułu [emulatora Azure Cosmos DB](local-emulator.md) .

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB warstwy Bezpłatna

Azure Cosmos DB bezpłatna warstwa ułatwia rozpoczęcie pracy, opracowywanie i testowanie aplikacji, a nawet uruchamianie niewielkich obciążeń produkcyjnych. Po włączeniu warstwy Bezpłatna na koncie uzyskasz pierwsze 400 RU/s i 5 GB miejsca do magazynowania w ramach konta. Istnieje również możliwość utworzenia udostępnionej bazy danych przepływności z 25 kontenerami, które współużytkują 400 RU/s na poziomie bazy danych, z uwzględnieniem warstwy Bezpłatna (ograniczenie 5 udostępnionych baz danych przepływności na koncie w warstwie Bezpłatna). W przypadku korzystania z warstwy Bezpłatna w przypadku aprowizacji udostępnionej bazy danych o minimalnej przepływności wynoszącej 400 RU/s wszystkie kontenery w tej bazie danych mogą współużytkować przepływność. Wszystkie nowe bazy danych o udostępnionej przepływności lub kontenerach o dedykowanej przepływności są rozliczane według zwykłych cen.

> [!NOTE]
> Warstwa Bezpłatna jest dostępna tylko w trybie przepływności aprowizacji.

Bezpłatna warstwa obowiązuje przez okres istnienia konta i zawiera wszystkie [zalety i funkcje](introduction.md#key-benefits) regularnego konta Azure Cosmos DB, w tym nieograniczony magazyn i przepływność (ru/s), umowy SLA, wysoką dostępność, gotowe Global Distribution we wszystkich regionach świadczenia usługi Azure i innych. W ramach każdej subskrypcji platformy Azure można korzystać z maksymalnie jednego konta warstwy bezpłatnej i musi on być w trakcie tworzenia konta. Aby rozpocząć, [Utwórz nowe konto w Azure Portal z włączoną bezpłatną warstwą](create-cosmosdb-resources-portal.md) lub Użyj [szablonu ARM](./manage-with-templates.md#free-tier). Więcej szczegółów można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="try-azure-cosmos-db-for-free"></a>Wypróbuj usługę Azure Cosmos DB bezpłatnie

Usługa [Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) jest bezpłatnym doświadczeniem, który pozwala na eksperymentowanie z Azure Cosmos DB w chmurze bez konieczności rejestrowania się w ramach konta platformy Azure lub korzystania z karty kredytowej. Konta usługi try Azure Cosmos DB są dostępne przez ograniczony czas, obecnie przez 30 dni. Można je odnowić w dowolnym momencie. Wypróbuj Azure Cosmos DB konta ułatwiają ocenę Azure Cosmos DB, kompilowanie i testowanie aplikacji oraz korzystanie z przewodników Szybki Start i samouczków. Możesz również utworzyć demonstrację, przeprowadzić testy jednostkowe, a nawet utworzyć konto wieloregionowe i uruchomić na nim aplikację bez ponoszenia kosztów. Na koncie try Azure Cosmos DB można mieć jedną udostępnioną bazę danych przepływności z maksymalnie 25 kontenerami i 20 000 RU/s przepływności lub jeden kontener z maksymalnie 5000 RU/s. Aby rozpocząć, zobacz sekcję [wypróbuj Azure Cosmos DB dla bezpłatnej](https://azure.microsoft.com/try/cosmosdb/) strony.

## <a name="azure-free-account"></a>Bezpłatne konto platformy Azure

Azure Cosmos DB jest uwzględniona na [koncie bezpłatnej platformy Azure](https://azure.microsoft.com/free), które oferuje bezpłatnie środki na korzystanie z platformy Azure i za pewien czas. W odniesieniu do Azure Cosmos DB to bezpłatne konto oferuje 25 GB miejsca do magazynowania i 400 jednostek ru przepływności w całym roku. Dzięki temu każdy deweloper może łatwo przetestować funkcje Azure Cosmos DB lub zintegrować ją z innymi usługami platformy Azure bez ponoszenia kosztów. Bezpłatne konto platformy Azure pozwala uzyskać środki na korzystanie z $200 USD za pierwsze 30 dni. Opłata nie zostanie naliczona, nawet jeśli zaczniesz korzystać z usług, dopóki nie zdecydujesz się na uaktualnienie. Aby rozpocząć, odwiedź stronę [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free) .

## <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB bezserwerowe

[Azure Cosmos DB bezserwerowe](serverless.md) umożliwia korzystanie z konta usługi Azure Cosmos w sposób, w którym opłata jest naliczana tylko za jednostki żądań używane przez operacje bazy danych i Magazyn używany przez dane. Użycie Azure Cosmos DB w trybie bezserwerowym nie wiąże się z opłatą minimalną. Ponieważ eliminuje to koncepcję zainicjowanej pojemności, najlepiej nadaje się do działań programistycznych i testowych, w przypadku których baza danych jest w większości czasu bezczynna.

## <a name="use-shared-throughput-databases"></a>Korzystanie z udostępnionych baz danych przepływności

W [udostępnionej bazie danych przepływności](set-throughput.md#set-throughput-on-a-database)wszystkie kontenery wewnątrz bazy danych współużytkują zainicjowaną przepływność (ru/s) bazy danych. Jeśli na przykład baza danych zostanie zainicjowana z 400 RU/s i ma cztery kontenery, wszystkie cztery kontenery będą współużytkować 400 RU/s. W środowisku deweloperskim lub testowym, w którym można uzyskać dostęp do każdego kontenera rzadziej i w ten sposób wymagać mniejszej niż minimum 400 RU/s, umieszczenie kontenerów w udostępnionej bazie danych przepływności może pomóc zoptymalizować koszt.

Załóżmy na przykład, że Twoje konto deweloperskie lub testowe ma cztery kontenery. Jeśli utworzysz cztery kontenery z dedykowaną przepływność (minimum 400 RU/s), Łączna liczba RU/s będzie 1600 RU/s. Natomiast w przypadku utworzenia udostępnionej bazy danych przepływności (minimum 400 RU/s) i umieszczenia w niej kontenerów łączna liczba jednostek RU/s będzie wynosić tylko 400 RU/s. Ogólnie udostępniona baza danych przepływności doskonale nadaje się do scenariuszy, w których nie jest wymagana gwarantowana przepływność na żadnym konkretnym kontenerze.  Dowiedz się więcej o [udostępnionych bazach danych przepływności.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Następne kroki

Możesz rozpocząć korzystanie z emulatora lub bezpłatnych kont Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej o [zrozumieniu Azure Cosmos DB rachunku](understand-your-bill.md)
* Dowiedz się więcej o [Azure Cosmos DB bezserwerowym](serverless.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów operacji odczytu i zapisu](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](./optimize-cost-reads-writes.md)
* Dowiedz się więcej [na temat optymalizowania kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)