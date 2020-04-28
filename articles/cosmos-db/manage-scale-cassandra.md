---
title: Elastyczne skalowanie przy użyciu interfejs API Cassandra w Azure Cosmos DB
description: Dowiedz się więcej o opcjach dostępnych do skalowania Azure Cosmos DB konta interfejs API Cassandra i ich zalety/wady
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 94cdeff36553268d691fc968036c5264e77fddc2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188811"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Elastyczne skalowanie Azure Cosmos DB interfejs API Cassandra konta

Istnieje wiele opcji umożliwiających Eksplorowanie elastycznego charakteru interfejsu API Azure Cosmos DB Cassandra. Aby zrozumieć, jak efektywnie skalować w Azure Cosmos DB, ważne jest, aby zrozumieć, jak zapewnić odpowiednią ilość jednostek żądań (RU/s) w celu uwzględnienia wymagań dotyczących wydajności w systemie. Aby dowiedzieć się więcej na temat jednostek żądania, zobacz artykuł dotyczący [jednostek](request-units.md) żądania. 

Dla interfejs API Cassandra można pobrać opłaty jednostkowe żądania dla poszczególnych zapytań przy użyciu [zestawów SDK dla platformy .NET i języka Java](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api). Jest to pomocne w ustaleniu ilości jednostek RU/s, które należy udostępnić w usłudze.

![Operacje bazy danych zużywają jednostki żądań](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Ograniczanie szybkości obsługi (błędy 429)

W przypadku, gdy klienci zużywają więcej zasobów (RU/s) niż ilość, która została zainicjowana, Azure Cosmos DB będą zwracać błędy o szybkości ograniczonej (429). Interfejs API Cassandra w Azure Cosmos DB tłumaczy te wyjątki na przeciążone błędy w protokole natywnym Cassandra. 

Jeśli system nie jest wrażliwy na opóźnienia, może być wystarczające do obsłużenia ograniczenia przepływności przy użyciu ponownych prób. Zobacz [przykład kodu Java](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) , aby dowiedzieć się, jak obsługiwać ograniczanie szybkości w sposób niewidoczny przy użyciu [rozszerzenia Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions) dla [zasad ponawiania Cassandra](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) w języku Java. Można również użyć [rozszerzenia Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) , aby obsłużyć ograniczenie szybkości.

## <a name="manage-scaling"></a>Zarządzanie skalowaniem

Jeśli zachodzi potrzeba zminimalizowania opóźnień, istnieje szeroki zakres opcji zarządzania przepływem i skalowaniem (jednostek ru) w interfejs API Cassandra:

* [Ręczne przy użyciu Azure Portal](#use-azure-portal)
* [Programowo przy użyciu funkcji płaszczyzny kontroli](#use-control-plane)
* [Programowo przy użyciu poleceń CQL z określonym zestawem SDK](#use-cql-queries)
* [Dynamicznie przy użyciu funkcji automatycznego skalowania](#use-autoscale)

W poniższych sekcjach wyjaśniono zalety i wady poszczególnych rozwiązań. Następnie można określić najlepszą strategię, aby zrównoważyć wymagania dotyczące skalowania systemu, łączny koszt oraz potrzeby związane z wydajnością rozwiązania.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Korzystanie z witryny Azure Portal

Możesz skalować zasoby w Azure Cosmos DB interfejs API Cassandra koncie przy użyciu Azure Portal. Aby dowiedzieć się więcej, zobacz artykuł dotyczący [obsługi przepływności na kontenerach i bazach danych](set-throughput.md). W tym artykule wyjaśniono względne zalety ustawiania przepływności na poziomie [bazy danych](set-throughput.md#set-throughput-on-a-database) lub [kontenera](set-throughput.md#set-throughput-on-a-container) w Azure Portal. Warunki "Database" i "Container" wymienione w tych artykułach są mapowane na "przestrzeń kluczy" i "Tabela" odpowiednio dla interfejs API Cassandra.

Zaletą tej metody jest to, że jest to prosty gotowe sposób zarządzania wydajnością przepływności w bazie danych. Jednak Wadą jest to, że w wielu przypadkach podejście do skalowania może wymagać, aby niektóre poziomy automatyzacji były opłacalne i wysokie. W następnych sekcjach opisano odpowiednie scenariusze i metody.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Korzystanie z płaszczyzny kontroli

Interfejs API Azure Cosmos DB dla Cassandra umożliwia programistyczne Dostosowywanie przepływności przy użyciu różnych funkcji płaszczyzny kontroli. Zobacz artykuły dotyczące [Azure Resource Manager](manage-cassandra-with-resource-manager.md), [programu PowerShell](powershell-samples-cassandra.md)i [interfejsu wiersza polecenia platformy Azure](cli-samples-cassandra.md) , aby uzyskać wskazówki i przykłady.

Zaletą tej metody jest Automatyzacja skalowania w górę lub w dół zasobów na podstawie czasomierza w celu uwzględnienia aktywności szczytowej lub okresów niskiej aktywności. Zapoznaj się z naszym przykładem, aby dowiesz się, jak to [zrobić za pomocą](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) Azure Functions i programu PowerShell.

Wadą tego podejścia może być, że nie można odpowiedzieć na nieprzewidywalne potrzeby zmiany skalowania w czasie rzeczywistym. Zamiast tego może być konieczne użycie kontekstu aplikacji w systemie na poziomie klienta/zestawu SDK lub [Skalowanie automatyczne](provision-throughput-autoscale.md).

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Korzystanie z zapytań CQL z określonym zestawem SDK

Można dynamicznie skalować system przy użyciu kodu, wykonując [polecenia CQL ALTER](cassandra-support.md#keyspace-and-table-options) dla danej bazy danych lub kontenera.

Zaletą tego podejścia jest to, że pozwala to na dynamiczne reagowanie na potrzeby skalowania i w sposób niestandardowy, który jest przeznaczony dla aplikacji. Korzystając z tej metody, można nadal korzystać ze standardowych stawek za RU/s. Jeśli wymagania dotyczące skalowania systemu są w większości przewidywalne (około 70% lub więcej), korzystanie z zestawu SDK z CQL może być bardziej opłacalną metodą automatycznego skalowania, niż przy użyciu funkcji automatyczne skalowanie. Wadą tego podejścia jest to, że może być dość skomplikowane do wdrożenia ponownych prób, podczas gdy ograniczenie szybkości może zwiększyć opóźnienia.

## <a name="use-autoscale"></a><a id="use-autoscale"></a>Korzystanie ze skalowania automatycznego

Oprócz ręcznych i programistycznych metod aprowizacji można także skonfigurować kontenery usługi Azure Cosmos w trybie skalowania automatycznego. Tryb skalowania automatycznego będzie automatycznie i natychmiast skalowany na potrzeby użycia w określonych zakresach RU bez naruszenia umowy SLA. Aby dowiedzieć się więcej, zobacz artykuł [Tworzenie kontenerów i baz danych usługi Azure Cosmos w trybie skalowania automatycznego](provision-throughput-autoscale.md) .

Zaletą tego podejścia jest najprostszy sposób zarządzania potrzebami skalowania w systemie. Gwarantuje to, że nie ma zastosowania ograniczenia stawki **w skonfigurowanych zakresach ru**. Wadą jest to, że jeśli wymagania dotyczące skalowania w systemie są przewidywalne, Skalowanie automatyczne może być tańszym sposobem obsługi wymagań dotyczących skalowania, niż przy użyciu Bespoke płaszczyzny kontroli lub poziomów zestawu SDK wymienionych powyżej.

## <a name="next-steps"></a>Następne kroki

- Rozpocznij od [utworzenia konta, bazy danych i tabeli interfejsu API Cassandra](create-cassandra-api-account-java.md) przy użyciu aplikacji w języku Java
