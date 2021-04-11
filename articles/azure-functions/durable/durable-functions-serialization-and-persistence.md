---
title: Trwałość i Serializacja danych w Durable Functions — Azure
description: Dowiedz się, jak rozszerzenie Durable Functions dla Azure Functions utrzymuje dane
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 91c04b3943af5eee436bb4a18a946000b76cff3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057987"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Trwałość i Serializacja danych w Durable Functions (Azure Functions)

Durable Functions automatycznie utrzymuje parametry funkcji, wartości zwracane i inne stany w trwałej zapleczu w celu zapewnienia niezawodnego wykonania. Jednak ilość i częstotliwość danych utrwalonych w magazynie trwałym może mieć wpływ na koszty związane z wydajnością i transakcjami magazynu. W zależności od typu danych, które są przechowywane w aplikacji, można również rozważyć konieczność przechowywania danych i zasad ochrony prywatności.

## <a name="azure-storage"></a>Azure Storage

Domyślnie Durable Functions utrzymuje dane do kolejek, tabel i obiektów BLOB na koncie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) , które określisz.

### <a name="queues"></a>Kolejki

Durable Functions używa kolejek usługi Azure Storage do niezawodnego zaplanowania wszystkich wykonań funkcji. Te komunikaty kolejki zawierają dane wejściowe lub wyjściowe funkcji, w zależności od tego, czy komunikat jest używany do zaplanowania wykonania, czy zwrócenia wartości z powrotem do funkcji wywołującej. Te komunikaty w kolejce zawierają również dodatkowe metadane, których Durable Functions używa do celów wewnętrznych, takich jak Routing i kompleksowa korelacja. Po zakończeniu wykonywania funkcji w odpowiedzi na odebraną wiadomość zostanie on usunięty, a wynik wykonania może być również utrwalany w tabelach usługi Azure Storage lub w obiektach Blob usługi Azure Storage.

W ramach jednego [centrum zadań](durable-functions-task-hubs.md)Durable Functions tworzy i dodaje komunikaty do kolejki *elementów roboczych* o nazwie `<taskhub>-workitem` do planowania funkcji działania oraz co najmniej jedną *kolejkę kontroli* o nazwie `<taskhub>-control-##` do zaplanowania lub wznowienia funkcji programu Orchestrator i jednostek. Liczba kolejek sterowania jest równa liczbie partycji skonfigurowanych dla aplikacji. Więcej informacji o kolejkach i partycjach znajduje się w dokumentacji dotyczącej [wydajności i skalowalności](durable-functions-perf-and-scale.md).

### <a name="tables"></a>tabelami

Po pomyślnym przeprowadzeniu przez aranżacji komunikatów procesy są utrwalane w tabeli *historii* o nazwie `<taskhub>History` . Dane wejściowe i wyjściowe aranżacji są również utrwalane w tabeli *wystąpień* o nazwie `<taskhub>Instances` .

### <a name="blobs"></a>Obiekty blob

W większości przypadków Durable Functions nie używają obiektów BLOB usługi Azure Storage do utrwalania danych. Kolejki i tabele mają jednak [limity rozmiaru](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits) , które mogą uniemożliwiać Durable Functions zachowywanie wszystkich wymaganych danych w wierszu lub wiadomości kolejki magazynu. Na przykład jeśli dane, które muszą zostać utrwalone w kolejce, są większe niż 45 KB podczas serializacji, Durable Functions kompresuje dane i zapisze je w obiekcie blob. Podczas utrwalania danych w magazynie obiektów BLOB w ten sposób trwała funkcja przechowuje odwołanie do tego obiektu BLOB w wierszu tabeli lub w komunikacie kolejki. Gdy Durable Functions muszą pobrać dane, zostanie automatycznie pobrane z obiektu BLOB. Te obiekty blob są przechowywane w kontenerze obiektów BLOB `<taskhub>-largemessages` .

> [!NOTE]
> Dodatkowe czynności w zakresie kompresji i obiektów BLOB dla dużych komunikatów mogą być kosztowne w odniesieniu do kosztów opóźnienia procesora i operacji we/wy. Ponadto Durable Functions wymaga załadowania utrwalonych danych w pamięci i może to zrobić dla wielu różnych wykonań funkcji jednocześnie. W związku z tym utrwalanie dużych ładunków danych może spowodować również duże użycie pamięci. Aby zminimalizować obciążenie pamięci, należy rozważyć ręczne utrwalanie dużych ładunków danych (na przykład w magazynie obiektów BLOB), a zamiast tego przekazać odwołania do tych danych. W ten sposób kod może ładować dane tylko wtedy, gdy jest to konieczne, aby uniknąć nadmiarowych obciążeń podczas [odtwarzania funkcji programu Orchestrator](durable-functions-orchestrations.md#reliability). Przechowywanie ładunków na dysku *nie* jest jednak zalecane, ponieważ stan dysku nie jest gwarantowany, ponieważ funkcje mogą być wykonywane na różnych maszynach wirtualnych w ciągu ich okresów istnienia.

### <a name="types-of-data-that-is-serialized-and-persisted"></a>Typy danych, które są serializowane i utrwalane
Poniżej znajduje się lista różnych typów danych, które zostaną zserializowane i utrwalone podczas korzystania z funkcji Durable Functions:

- Wszystkie dane wejściowe i wyjściowe funkcji Orchestrator, Activity i Entity, w tym wszystkie identyfikatory i Nieobsłużone wyjątki
- Nazwy funkcji Orchestrator, Activity i Entity
- Zewnętrzne nazwy i ładunki zdarzeń
- Niestandardowe ładunki stanu aranżacji
- Komunikaty o zakończeniu aranżacji
- Trwałe ładunki czasomierza
- Trwałe żądania HTTP i adresy URL odpowiedzi, nagłówki i ładunki
- Wywołania jednostki i ładunki sygnałów
- Ładunki stanu jednostki

### <a name="working-with-sensitive-data"></a>Praca z danymi poufnymi
W przypadku korzystania z usługi Azure Storage wszystkie dane są automatycznie szyfrowane. Jednak każda osoba mająca dostęp do konta magazynu może odczytywać dane w postaci niezaszyfrowanej. Jeśli potrzebujesz silniejszej ochrony danych poufnych, rozważ pierwsze szyfrowanie danych przy użyciu własnych kluczy szyfrowania, tak aby Durable Functions utrwalać dane w postaci wstępnie zaszyfrowanej.

Alternatywnie Użytkownicy platformy .NET mają możliwość implementowania niestandardowych dostawców serializacji, które zapewniają automatyczne szyfrowanie. Przykład serializacji niestandardowej z szyfrowaniem można znaleźć w tym przykładzie usługi [GitHub](https://github.com/charleszipp/azure-durable-entities-encryption).

> [!NOTE]
> Jeśli zdecydujesz się zaimplementować szyfrowanie na poziomie aplikacji, należy pamiętać, że aranżacje i jednostki mogą istnieć przez czas nieokreślony. W ten sposób można obrócić klucze szyfrowania, ponieważ organizacja lub jednostki mogą działać dłużej niż zasady rotacji kluczy. Jeśli nastąpi rotacja kluczy, klucz używany do szyfrowania danych może nie być już dostępny do odszyfrowania przy następnym zakończeniu aranżacji lub jednostce. Dlatego szyfrowanie klienta jest zalecane tylko wtedy, gdy nie będzie można uruchamiać aranżacji i jednostek dla stosunkowo krótkich okresów czasu.

## <a name="customizing-serialization-and-deserialization"></a>Dostosowywanie serializacji i deserializacji

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>Domyślna logika serializacji

Durable Functions wewnętrznie używa [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) do serializacji danych aranżacji i jednostek do formatu JSON. Ustawienia domyślne Durable Functions użycia dla Json.NET są następujące:

**Dane wejściowe, wyjściowe i stan:**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**Wyłączenia**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

Przeczytaj więcej szczegółowej dokumentacji na ten temat `JsonSerializerSettings` [](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm).

## <a name="customizing-serialization-with-net-attributes"></a>Dostosowywanie serializacji przy użyciu atrybutów platformy .NET

Podczas serializowania danych Json.NET szuka [różnych atrybutów](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm) klas i właściwości, które kontrolują sposób serializacji i deserializacji danych z formatu JSON. Jeśli jesteś właocicielem kodu źródłowego dla typu danych przesyłanego do Durable Functions interfejsów API, rozważ dodanie tych atrybutów do typu w celu dostosowania serializacji i deserializacji.

## <a name="customizing-serialization-with-dependency-injection"></a>Dostosowywanie serializacji z iniekcją zależności

Aplikacje funkcji przeznaczone dla platformy .NET i uruchamiane w środowisku uruchomieniowym funkcji v3 mogą używać [iniekcji zależności (di)](../functions-dotnet-dependency-injection.md) do dostosowywania sposobu serializacji danych i wyjątków. Poniższy przykładowy kod przedstawia sposób użycia funkcji DI w celu zastąpienia domyślnych ustawień serializacji Json.NET przy użyciu niestandardowych implementacji `IMessageSerializerSettingsFactory` `IErrorSerializerSettingsFactory` interfejsów i.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingsFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>Serializacja i deserializacja logiki

Aplikacje węzła Azure Functions są używane [ `JSON.stringify()` do serializacji](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) i [ `JSON.Parse()` deserializacji](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse). Większość typów ma bezproblemowe serializacji i deserializacji. W przypadkach, gdy domyślna logika jest niewystarczająca, zdefiniowanie `toJSON()` metody na obiekcie spowoduje przejęcie kontroli nad logiką serializacji. Jednak nie istnieje analogowa dla deserializacji obiektu.

W celu pełnego dostosowania potoku serializacji/deserializacji należy rozważyć obsługę serializacji i deserializacji z własnym kodem i przekazaniem danych jako ciągów.


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>Serializacja i deserializacja logiki

Zdecydowanie zaleca się użycie adnotacji typu w celu zapewnienia poprawnego Durable Functions serializacji i deserializacji danych. Chociaż wiele wbudowanych typów jest obsługiwanych automatycznie, niektóre wbudowane typy danych wymagają adnotacji typu w celu zachowania typu podczas deserializacji.

W przypadku niestandardowych typów danych należy zdefiniować serializacji JSON i deserializacji typu danych przez wyeksportowanie statycznej `to_json` `from_json` metody i z klasy.

---
