---
title: Przetwarzanie bazy danych bez serwera z użyciem Azure Cosmos DB i Azure Functions
description: Dowiedz się, jak Azure Cosmos DB i Azure Functions mogą być używane razem do tworzenia aplikacji komputerowych opartych na zdarzeniach.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 73a34cc27eaba33d04f4d31585c7f494f58e7274
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334076"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Przetwarzanie baz danych bezserwerowe przy użyciu Azure Cosmos DB i Azure Functions
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Operacje obliczeniowe bez użycia serwera to wszystko, co umożliwia skoncentrowanie się na poszczególnych fragmentach logiki, które są powtarzalne i bezstanowe. Te elementy nie wymagają zarządzania infrastrukturą i zużywają zasoby tylko przez sekundy lub milisekundy, w których są uruchamiane dla. Na początku przenoszenia obliczeniowego bez serwera są funkcje, które są dostępne w ekosystemie platformy Azure przez [Azure Functions](https://azure.microsoft.com/services/functions). Aby dowiedzieć się więcej o innych środowiskach wykonywania bezserwerowego na platformie Azure, zobacz [bezserwerowy na stronie platformy Azure](https://azure.microsoft.com/solutions/serverless/) . 

Za pomocą natywnej integracji między [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) i Azure Functions można tworzyć wyzwalacze bazy danych, powiązania wejściowe i powiązania wyjściowe bezpośrednio z konta Azure Cosmos DB. Korzystając z Azure Functions i Azure Cosmos DB, można tworzyć i wdrażać aplikacje bezserwerowe oparte na zdarzeniach z dostępem o małym opóźnieniu do danych bogatych dla globalnej bazy użytkowników.

## <a name="overview"></a>Omówienie

Azure Cosmos DB i Azure Functions umożliwiają integrację baz danych i aplikacji bezserwerowych w następujący sposób:

* Utwórz **wyzwalacz Azure Functions oparty na zdarzeniach dla Cosmos DB**. Ten wyzwalacz opiera się na strumieniach [źródeł zmian](change-feed.md) do monitorowania kontenera usługi Azure Cosmos pod kątem zmian. Po wprowadzeniu jakichkolwiek zmian do kontenera strumień strumieniowego źródła danych jest wysyłany do wyzwalacza, który wywołuje funkcję platformy Azure.
* Alternatywnie można powiązać funkcję platformy Azure z kontenerem usługi Azure Cosmos przy użyciu **powiązania danych wejściowych**. Powiązania wejściowe odczytują dane z kontenera, gdy zostanie wykonana funkcja.
* Powiąż funkcję z kontenerem usługi Azure Cosmos za pomocą **powiązania danych wyjściowych**. Powiązania wyjściowe zapisują dane do kontenera po zakończeniu działania funkcji.

> [!NOTE]
> Obecnie Azure Functions wyzwalacza, powiązania wejściowe i powiązania wyjściowe dla Cosmos DB są obsługiwane tylko w przypadku korzystania z interfejsu API SQL. Dla wszystkich innych Azure Cosmos DB interfejsów API należy uzyskać dostęp do bazy danych z funkcji przy użyciu klienta statycznego dla interfejsu API.


Na poniższym diagramie przedstawiono każdą z tych trzech integracji: 

:::image type="content" source="./media/serverless-computing-database/cosmos-db-azure-functions-integration.png" alt-text="Jak integracja Azure Cosmos DB i Azure Functions" border="false":::

Wyzwalacz Azure Functions, powiązania wejściowe i powiązania wyjściowe dla Azure Cosmos DB mogą być używane w następujących kombinacjach:

* Wyzwalacz Azure Functions dla Cosmos DB może być używany z powiązaniem wyjściowym z innym kontenerem usługi Azure Cosmos. Po wykonaniu przez funkcję akcji dotyczącej elementu w strumieniu zmian można zapisać go w innym kontenerze (zapisanie go do tego samego kontenera, z którego pochodzi, może efektywnie utworzyć pętlę cykliczną). Można też użyć wyzwalacza Azure Functions, aby Cosmos DB efektywnie migrować wszystkie zmienione elementy z jednego kontenera do innego kontenera przy użyciu powiązania danych wyjściowych.
* Powiązania wejściowe i powiązania wyjściowe dla Azure Cosmos DB mogą być używane w tej samej funkcji platformy Azure. Jest to dobre rozwiązanie w przypadkach, gdy chcesz znaleźć pewne dane z powiązaniem wejściowym, zmodyfikować je w funkcji platformy Azure, a następnie zapisać je w tym samym kontenerze lub innym kontenerze po modyfikacji.
* Powiązanie danych wejściowych z kontenerem usługi Azure Cosmos może być używane w tej samej funkcji co wyzwalacz Azure Functions dla Cosmos DB i może być używane z również powiązaniem wyjściowym lub bez niego. Tej kombinacji można użyć do zastosowania aktualnych informacji wymiany waluty (ściąganych przy użyciu powiązania danych wejściowych z kontenerem programu Exchange) do źródła zmian nowych zamówień w ramach usługi koszyka zakupów. Zaktualizowany kurs zakupów łącznie z zastosowanymi konwersjami waluty można zapisać do trzeciego kontenera przy użyciu powiązania danych wyjściowych.

## <a name="use-cases"></a>Przypadki zastosowań

Poniższe przypadki użycia pokazują kilka sposobów, aby maksymalnie wykorzystać dane Azure Cosmos DB — łącząc dane z Azure Functions sterowane zdarzeniami.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Przypadek użycia IoT — Azure Functions wyzwalacza i powiązania danych wyjściowych dla Cosmos DB

W implementacjach IoT można wywołać funkcję, gdy lampa Check Engine jest wyświetlana w połączonym samochodowym.

**Implementacja:** Użyj wyzwalacza Azure Functions i powiązania wyjściowego dla Cosmos DB

1. **Wyzwalacz Azure Functions dla Cosmos DB** służy do wyzwalania zdarzeń związanych z alertami dotyczącymi samochodu, takich jak oświetlenie aparatu Check w podłączonym samochodie.
2. Gdy lampka Check Engine zostanie połączona, dane czujnika są wysyłane do Azure Cosmos DB.
3. Azure Cosmos DB tworzy lub aktualizuje nowe dokumenty danych czujników, a następnie te zmiany są przesyłane strumieniowo do wyzwalacza Azure Functions w celu Cosmos DB.
4. Wyzwalacz jest wywoływany dla każdej zmiany danych w kolekcji danych czujników, ponieważ wszystkie zmiany są przesyłane strumieniowo za pośrednictwem źródła zmian.
5. Warunek progu jest używany w funkcji do wysyłania danych z czujnika do działu rękojmi.
6. Jeśli temperatura jest również nad określoną wartością, do właściciela jest również wysyłany alert.
7. **Powiązanie danych wyjściowych** w funkcji aktualizuje rekord samochodu w innym kontenerze usługi Azure Cosmos do przechowywania informacji o zdarzeniu sprawdzania aparatu.

Na poniższej ilustracji przedstawiono kod zapisany w Azure Portal dla tego wyzwalacza.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-trigger-portal.png" alt-text="Utwórz wyzwalacz Azure Functions dla Cosmos DB w Azure Portal":::

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Przypadek użycia finansowego — wyzwalacz czasomierza i powiązanie wejściowe

W obszarze implementacje finansowe można wywołać funkcję, gdy saldo konta bankowego spadnie poniżej określonej kwoty.

**Implementacja:** Wyzwalacz czasomierza z powiązaniem wejściowym Azure Cosmos DB

1. Korzystając z [wyzwalacza czasomierza](../azure-functions/functions-bindings-timer.md), można pobrać informacje o saldzie konta bankowego przechowywane w kontenerze usługi Azure Cosmos w interwałach czasowych przy użyciu **powiązania danych wejściowych**.
2. Jeśli saldo jest poniżej progu niskiego salda ustawionego przez użytkownika, wykonaj akcję z akcją w funkcji platformy Azure.
3. Powiązanie danych wyjściowych może być [integracją SendGrid](../azure-functions/functions-bindings-sendgrid.md) , która wysyła wiadomość e-mail z konta usługi do adresów e-mail zidentyfikowanych dla każdego z kont niskiego salda.

Poniższe obrazy przedstawiają kod w Azure Portal w tym scenariuszu.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png" alt-text="Index.js pliku dla wyzwalacza czasomierza dla scenariusza finansowego":::

:::image type="content" source="./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png" alt-text="Uruchom plik. CSX dla wyzwalacza czasomierza dla scenariusza finansowego":::

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Przypadek użycia gier — Azure Functions wyzwalacza i powiązania danych wyjściowych dla Cosmos DB 

W grach podczas tworzenia nowego użytkownika można wyszukać innych użytkowników, którzy mogą je znać przy użyciu [interfejsu API Azure Cosmos DB Gremlin](graph-introduction.md). Następnie można napisać wyniki do Azure Cosmos DB lub bazy danych SQL, aby ułatwić ich pobieranie.

**Implementacja:** Użyj wyzwalacza Azure Functions i powiązania wyjściowego dla Cosmos DB

1. Przy użyciu [bazy danych](graph-introduction.md) programu Azure Cosmos DB Graph do przechowywania wszystkich użytkowników można utworzyć nową funkcję z wyzwalaczem Azure Functions dla Cosmos DB. 
2. Za każdym razem, gdy nowy użytkownik zostanie wstawiony, funkcja jest wywoływana, a następnie wynik jest przechowywany za pomocą **powiązania danych wyjściowych**.
3. Funkcja wysyła zapytanie do bazy danych grafu w celu wyszukania wszystkich użytkowników, którzy są bezpośrednio powiązani z nowym użytkownikiem i zwracają ten zestaw danych do funkcji.
4. Te dane są następnie przechowywane w Azure Cosmos DB, które można następnie łatwo pobrać przez dowolną aplikację frontonu, która pokazuje nowego użytkownika połączonego znajomych.

### <a name="retail-use-case---multiple-functions"></a>Przypadek użycia detalicznego — wiele funkcji

W przypadku implementacji detalicznych, gdy użytkownik dodaje element do koszyka, masz teraz elastyczność tworzenia i wywoływać funkcje dla opcjonalnych składników potoku biznesowego.

**Implementacja:** Wiele wyzwalaczy Azure Functions dla Cosmos DB nasłuchiwania jednego kontenera

1. Można utworzyć wiele Azure Functions, dodając wyzwalacze Azure Functions, aby Cosmos DB do każdego z nich, które nasłuchują tego samego źródła zmian danych koszyka zakupów. Należy pamiętać, że gdy wiele funkcji nasłuchuje na tym samym strumieniu zmian, dla każdej funkcji wymagana jest nowa kolekcja dzierżaw. Aby uzyskać więcej informacji na temat kolekcji dzierżaw, zobacz [Opis biblioteki procesora źródła zmian](change-feed-processor.md).
2. Za każdym razem, gdy nowy element zostanie dodany do koszyka użytkowników, każda funkcja jest wywoływana niezależnie przez źródło zmian z kontenera koszyka zakupów.
   * Jedna funkcja może użyć zawartości bieżącego koszyka, aby zmienić wyświetlanie innych elementów, które użytkownik może chcieć zainteresować.
   * Inna funkcja może zaktualizować sumę spisu.
   * Inna funkcja może wysyłać informacje o klientach dla niektórych produktów do działu marketingowego, który wysyła do nich promocyjny program do obsługi klienta. 

     Każdy dział może utworzyć Azure Functions dla Cosmos DB przez nasłuchiwanie kanału informacyjnego zmiany i upewnienie się, że nie będzie opóźniać krytycznych zdarzeń przetwarzania zamówień w procesie.

We wszystkich tych przypadkach użycia, ponieważ funkcja odłączyła się sama sama aplikacja, nie musisz cały czas uruchamiać nowych wystąpień aplikacji. Zamiast tego Azure Functions są ustawiane przez poszczególne funkcje w celu ukończenia procesów dyskretnych zgodnie z potrzebami.

## <a name="tooling"></a>Narzędzia

Natywna integracja między Azure Cosmos DB i Azure Functions jest dostępna w Azure Portal i w programie Visual Studio 2019.

* W portalu Azure Functions można utworzyć wyzwalacz. Instrukcje szybkiego startu znajdują się [w temacie Tworzenie wyzwalacza Azure Functions dla Cosmos DB w Azure Portal](../azure-functions/functions-create-cosmos-db-triggered-function.md).
* W portalu Azure Cosmos DB można dodać wyzwalacz Azure Functions dla Cosmos DB do istniejącej aplikacji funkcji platformy Azure w tej samej grupie zasobów.
* W programie Visual Studio 2019 można utworzyć wyzwalacz przy użyciu [narzędzi Azure Functions](../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Dlaczego warto wybrać Azure Functions integrację dla obliczeń bezserwerowych?

Azure Functions zapewnia możliwość tworzenia skalowalnych jednostek pracy lub zwięzłych fragmentów logiki, które mogą być uruchamiane na żądanie, bez aprowizacji i zarządzania infrastrukturą. Za pomocą Azure Functions nie trzeba tworzyć aplikacji rozwiniętą w celu reagowania na zmiany w bazie danych Azure Cosmos, można utworzyć małe funkcje wielokrotnego użytku dla konkretnych zadań. Ponadto można również użyć danych Azure Cosmos DB jako danych wejściowych lub wyjściowych do funkcji platformy Azure w odpowiedzi na zdarzenia, takie jak żądania HTTP lub wyzwalacz czasu.

Azure Cosmos DB jest zalecaną bazą danych dla architektury przetwarzania bezserwerowego z następujących powodów:

* **Natychmiastowy dostęp do wszystkich danych** : masz szczegółowy dostęp do każdej przechowywanej wartości, ponieważ Azure Cosmos DB [automatycznie domyślnie indeksuje](index-policy.md) wszystkie dane i sprawia, że te indeksy są dostępne natychmiast. Oznacza to, że można stale wysyłać zapytania, aktualizować i dodawać nowe elementy do bazy danych oraz uzyskiwać natychmiastowy dostęp za pośrednictwem Azure Functions.

* **Bezschematowe**. Azure Cosmos DB jest bezschematowa — w związku z tym jednoznacznie można obsłużyć wszelkie dane wyjściowe z funkcji platformy Azure. To podejście "dojście" umożliwia proste tworzenie różnych funkcji, które wszystkie dane wyjściowe będą Azure Cosmos DB.

* **Skalowalna przepływność**. Przepływność można skalować w górę i w dół w Azure Cosmos DB. Jeśli masz setki lub tysiące funkcji wysyłających zapytania i zapisu do tego samego kontenera, możesz skalować w górę jednostki [ru/s](request-units.md) , aby obsłużyć obciążenie. Wszystkie funkcje mogą działać równolegle przy użyciu przyznanych RU/s, a Twoje dane są gwarantowane [spójność](consistency-levels.md).

* **Replikacja globalna**. Możesz replikować Azure Cosmos DB danych na [całym świecie](distribute-data-globally.md) , aby ograniczyć opóźnienia, lokalizowanie danych w pobliżu miejsca, w którym znajdują się użytkownicy. Podobnie jak w przypadku wszystkich zapytań Azure Cosmos DB, dane z wyzwalaczy sterowanych zdarzeniami są odczytywane z Azure Cosmos DB najbliżej użytkownika.

Jeśli chcesz przeprowadzić integrację z usługą Azure Functions, aby przechowywać dane i nie potrzebować głębokiego indeksowania, lub jeśli zachodzi potrzeba przechowywania załączników i plików multimedialnych, [wyzwalacz BLOB Storage platformy Azure](../azure-functions/functions-bindings-storage-blob.md) może być lepszym rozwiązaniem.

Zalety Azure Functions: 

* **Sterowane zdarzeniami**. Azure Functions są sterowane zdarzeniami i mogą nasłuchiwać źródła zmian z Azure Cosmos DB. Oznacza to, że nie musisz tworzyć logiki nasłuchiwania, po prostu Zadbaj o zmiany, które nasłuchuje. 

* **Brak limitów**. Funkcje są wykonywane równolegle, a usługa jest uruchamiana na tyle, ile potrzebujesz. Parametry są ustawiane.

* **Dobre dla szybkich zadań**. Usługa uruchamia nowe wystąpienia funkcji za każdym razem, gdy zdarzenie zostanie wyzwolone i zamyka je zaraz po zakończeniu działania funkcji. Płacisz tylko za czas działania funkcji.

Jeśli nie masz pewności, czy przepływ, Logic Apps, Azure Functions lub zadania WebJob są najlepsze dla implementacji, zobacz [Wybieranie między przepływem, Logic Apps, funkcjami i zadaniami WebJob](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Następne kroki

Teraz przyjrzyjmy się Azure Cosmos DB i Azure Functions dla rzeczywistych: 

* [Utwórz wyzwalacz Azure Functions dla Cosmos DB w Azure Portal](../azure-functions/functions-create-cosmos-db-triggered-function.md)
* [Tworzenie wyzwalacza HTTP usługi Azure Functions przy użyciu powiązania danych wejściowych usługi Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb.md?tabs=csharp)
* [Azure Cosmos DB powiązania i wyzwalacze](../azure-functions/functions-bindings-cosmosdb-v2.md)
