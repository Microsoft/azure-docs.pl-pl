---
title: 'Tworzenie pierwszej aplikacji Service Fabric w języku C #'
description: Wprowadzenie do tworzenia aplikacji Microsoft Azure Service Fabric przy użyciu usług bezstanowych i stanowych.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev, devx-track-csharp
ms.openlocfilehash: 45341c98a40cbcabfa8b96f2016f02f1755fe2b3
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791531"
---
# <a name="get-started-with-reliable-services"></a>Wprowadzenie do usług Reliable Services

> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-quick-start.md)
> * [Java w systemie Linux](service-fabric-reliable-services-quick-start-java.md)

Aplikacja Service Fabric platformy Azure zawiera co najmniej jedną usługę, w której uruchomiono kod. W tym przewodniku pokazano, jak utworzyć aplikacje bezstanowe i bezstanowe Service Fabric przy użyciu [Reliable Services](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Podstawowe pojęcia

Aby rozpocząć pracę z Reliable Services, musisz zrozumieć tylko kilka podstawowych pojęć:

* **Typ usługi**: to jest implementacja usługi. Jest on definiowany przez zapisanie klasy, która rozszerza `StatelessService` i wszelkie inne kod lub zależności używane w tym dokumencie wraz z nazwą i numerem wersji.
* **Nazwane wystąpienie usługi**: Aby uruchomić usługę, należy utworzyć nazwane wystąpienia typu usługi, podobnie jak w przypadku tworzenia wystąpień obiektów typu klasy. Wystąpienie usługi ma nazwę w postaci identyfikatora URI przy użyciu "sieci szkieletowej:/" schemat, taki jak "Sieć szkieletowa:/MojaApl/moje usługi".
* **Host usługi**: utworzone wystąpienia usługi nazwane muszą zostać uruchomione w ramach procesu hosta. Host usługi to tylko proces, w którym można uruchamiać wystąpienia usługi.
* **Rejestracja usługi**: Rejestracja umożliwia wszystko. Typ usługi musi być zarejestrowany w środowisku uruchomieniowym Service Fabric na hoście usługi, aby umożliwić Service Fabric tworzenia wystąpień do uruchomienia.  

## <a name="create-a-stateless-service"></a>Tworzenie usługi bezstanowej

Usługa bezstanowa to typ usługi, która jest obecnie normą w aplikacjach w chmurze. Jest uważana za bezstanową, ponieważ sama sama usługa nie zawiera danych, które muszą być przechowywane w sposób niezawodny lub w wysokiej dostępności. Jeśli wystąpienie usługi bezstanowej zostanie zamknięte, cały jej stan wewnętrzny zostanie utracony. W tym typie usługi stan musi być utrwalony w magazynie zewnętrznym, takim jak tabele lub SQL Database platformy Azure, aby zapewnić wysoką dostępność i niezawodność.

Uruchom program Visual Studio 2017 lub Visual Studio 2019 jako administrator i Utwórz nowy projekt aplikacji Service Fabric o nazwie *HelloWorld*:

![Użyj okna dialogowego Nowy projekt, aby utworzyć nową aplikację Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Następnie utwórz projekt usługi bezstanowej przy użyciu **programu .NET Core 2,0** o nazwie *HelloWorldStateless*:

![W drugim oknie dialogowym Utwórz bezstanowy projekt usługi](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Twoje rozwiązanie zawiera teraz dwa projekty:

* *HelloWorld*. Jest to projekt *aplikacji* , który zawiera Twoje *usługi*. Zawiera również manifest aplikacji opisujący aplikację oraz kilka skryptów programu PowerShell, które ułatwiają wdrażanie aplikacji.
* *HelloWorldStateless*. To jest projekt usługi. Zawiera ona implementację usługi bezstanowej.

## <a name="implement-the-service"></a>Implementowanie usługi

Otwórz plik **HelloWorldStateless.cs** w projekcie usługi. W Service Fabric usługa może uruchamiać dowolną logikę biznesową. Interfejs API usługi udostępnia dwa punkty wejścia dla kodu:

* Metoda otwartego punktu wejścia o nazwie *RunAsync*, która umożliwia rozpoczęcie wykonywania obciążeń, w tym długotrwałych obciążeń obliczeniowych.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Punkt wejścia komunikacji, w którym można podłączyć wybrany stos komunikacji, taki jak ASP.NET Core. Jest to miejsce, w którym można zacząć otrzymywać żądania od użytkowników i innych usług.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

W tym samouczku będziemy skupić się na `RunAsync()` metodzie wejścia. Jest to miejsce, w którym można od razu rozpocząć uruchamianie kodu.
Szablon projektu zawiera przykładową implementację `RunAsync()` , która zwiększa liczbę kroczącą.

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat pracy z stosem komunikacji, zobacz [komunikacja z usługą za pomocą ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### <a name="runasync"></a>RunAsync

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

Platforma wywołuje tę metodę, gdy wystąpienie usługi jest umieszczane i gotowe do wykonania. W przypadku usługi bezstanowej oznacza to, że po otwarciu wystąpienia usługi. Token anulowania jest dostarczany w celu koordynowania, gdy wystąpienie usługi musi zostać zamknięte. W Service Fabric ten cykl otwierania/zamykania wystąpienia usługi może wystąpić wiele razy w okresie istnienia usługi jako całości. Może się to zdarzyć z różnych powodów, takich jak:

* System przenosi wystąpienia usługi na potrzeby równoważenia zasobów.
* Błędy występują w kodzie.
* Aplikacja lub system zostały uaktualnione.
* Podstawowy sprzęt napotyka awarię.

Ta aranżacja jest zarządzana przez system w celu zapewnienia wysokiej dostępności i prawidłowego zrównoważenia usługi.

`RunAsync()` nie powinien blokować synchronicznie. Implementacja RunAsync powinna zwrócić zadanie lub oczekiwanie na wszystkie operacje długotrwałe lub blokujące, aby umożliwić kontynuowanie środowiska uruchomieniowego. Zwróć uwagę na `while(true)` pętlę w poprzednim przykładzie, która jest używana do zwracania zadań `await Task.Delay()` . Jeśli obciążenie musi blokować synchronicznie, należy zaplanować nowe zadanie przy użyciu `Task.Run()` w `RunAsync` implementacji.

Anulowanie obciążenia jest wysiłkiem w ramach współpracy zorganizowanej przy użyciu podanego tokenu anulowania. System poczeka na zakończenie zadania (przez pomyślne ukończenie, anulowanie lub błąd) przed przekazaniem. Ważne jest, aby honorować token anulowania, zakończyć pracę i zakończyć działanie `RunAsync()` tak szybko, jak to możliwe, gdy system żąda anulowania.

W tym przykładzie usługi bezstanowej liczba jest przechowywana w zmiennej lokalnej. Jednak ponieważ jest to usługa bezstanowa, przechowywana wartość istnieje tylko dla bieżącego cyklu życia wystąpienia usługi. Po przeniesieniu lub ponownym uruchomieniu usługi wartość zostanie utracona.

## <a name="create-a-stateful-service"></a>Tworzenie usługi stanowej

W Service Fabric wprowadzono nowy rodzaj usługi, która jest stanowa. Usługa stanowa może niezawodnie zachować stan w ramach samej usługi, w której znajduje się kod, który go używa. Stan zapewnia wysoką dostępność przez Service Fabric bez konieczności utrwalania stanu w sklepie zewnętrznym.

Do konwersji wartości licznika z bezstanowego na wysoko dostępne i trwałe, nawet gdy usługa jest przenoszona lub ponownie uruchamiana, potrzebna jest usługa stanowa.

W tej samej aplikacji *HelloWorld* możesz dodać nową usługę, klikając prawym przyciskiem myszy pozycję usługi w projekcie aplikacji i wybierając polecenie **dodaj > nowe Service Fabric usługi**.

![Dodawanie usługi do aplikacji Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Wybierz pozycję **.NET Core 2,0-> stanowej usługi** i nadaj jej nazwę *HelloWorldStateful*. Kliknij przycisk **OK**.

![Użyj okna dialogowego Nowy projekt, aby utworzyć nową Service Fabric stanową usługę](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Aplikacja powinna mieć teraz dwie usługi: *HelloWorldStateless* usługi bezstanowej i usługi stanowej *HelloWorldStateful*.

Usługa stanowa ma te same punkty wejścia co usługa bezstanowa. Główną różnicą jest dostępność *dostawcy stanu* , który może niezawodnie przechowywać stan. Service Fabric zawiera implementację dostawcy stanu o nazwie [niezawodne kolekcje](service-fabric-reliable-services-reliable-collections.md), które umożliwiają tworzenie replikowanych struktur danych za pomocą Menedżera niezawodnego stanu. Usługa bezstanowa domyślnie używa tego dostawcy stanu.

Otwórz **HelloWorldStateful.cs** w *HelloWorldStateful*, który zawiera następującą metodę RunAsync:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync

`RunAsync()` działa podobnie do usług stanowych i bezstanowych. Jednak w usłudze stanowej platforma wykonuje dodatkową prace w Twoim imieniu, zanim zostanie wykonana `RunAsync()` . To działanie może obejmować upewnienie się, że niezawodny Menedżer stanu i niezawodne kolekcje są gotowe do użycia.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Niezawodne kolekcje i Menedżer niezawodnego stanu

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2#microsoft_servicefabric_data_collections_ireliabledictionary_2) to implementacja słownika, której można użyć do niezawodnego przechowywania stanu usługi. Za pomocą Service Fabric i niezawodnych kolekcji można przechowywać dane bezpośrednio w usłudze bez potrzeby zewnętrznego magazynu trwałego. Niezawodne kolekcje sprawiają, że dane są wysoce dostępne. Service Fabric to osiągnąć przez utworzenie wielu *replik* usługi i zarządzanie nimi. Udostępnia również interfejs API, który stanowi streszczenie złożoności zarządzania tymi replikami i ich przejścia stanu.

Niezawodne kolekcje mogą przechowywać dowolny typ .NET, w tym typy niestandardowe, z kilkoma zastrzeżeniami:

* Service Fabric zapewnia wysoką dostępność stanu przez *replikowanie* stanu między węzłami, a niezawodne kolekcje przechowują dane na dysku lokalnym na każdej replice. Oznacza to, że wszystkie elementy, które są przechowywane w niezawodnych kolekcjach, muszą być *serializowane*. Domyślnie niezawodne kolekcje używają [schematu DataContract](/dotnet/api/system.runtime.serialization.datacontractattribute) do serializacji, dlatego ważne jest, aby upewnić się, że typy są [obsługiwane przez serializator kontraktu danych](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer) w przypadku użycia serializatora domyślnego.
* Obiekty są replikowane w celu zapewnienia wysokiej dostępności podczas zatwierdzania transakcji dla niezawodnych kolekcji. Obiekty przechowywane w niezawodnych kolekcjach są przechowywane w pamięci lokalnej w usłudze. Oznacza to, że masz lokalne odwołanie do obiektu.
  
   Należy pamiętać, że nie można zmodyfikować wystąpień lokalnych tych obiektów bez wykonywania operacji aktualizacji dla niezawodnej kolekcji transakcji. Dzieje się tak dlatego, że zmiany lokalnych wystąpień obiektów nie będą replikowane automatycznie. Należy ponownie wstawić obiekt do słownika lub użyć jednej z metod *aktualizacji* w słowniku.

Niezawodny Menedżer stanu zarządza niezawodnymi kolekcjami. Można po prostu zażądać niezawodnego menedżera stanu do niezawodnej kolekcji według nazwy w dowolnym momencie i w dowolnym miejscu usługi. Niezawodny Menedżer stanu zapewnia odwołanie do odwołania. Nie zalecamy zapisywania odwołań do niezawodnych wystąpień kolekcji w zmiennych lub właściwościach składowych klasy. Należy zwrócić szczególną uwagę, aby upewnić się, że odwołanie jest ustawione na wystąpienie przez cały czas w cyklu życia usługi. Niezawodny Menedżer stanu obsługuje tę czynność i jest zoptymalizowany pod kątem powtarzających się wizyt.

### <a name="transactional-and-asynchronous-operations"></a>Operacje transakcyjne i asynchroniczne

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Niezawodne kolekcje mają wiele operacji wykonywanych przez ich `System.Collections.Generic` i `System.Collections.Concurrent` odpowiedniki, z wyjątkiem języka Integrated Language Query (LINQ). Operacje dotyczące niezawodnych kolekcji są asynchroniczne. Dzieje się tak dlatego, że operacje zapisu z niezawodnymi kolekcjami wykonują operacje we/wy umożliwiające replikację i utrwalanie danych na dysku.

Niezawodne operacje zbierania danych są *transakcyjne*, dzięki czemu można zachować spójność stanu w wielu niezawodnych kolekcjach i operacjach. Na przykład można usunąć z kolejki element roboczy z niezawodnej kolejki, wykonać na nim operację i zapisać wynik w niezawodnym słowniku, a wszystko to w ramach jednej transakcji. Jest to traktowane jako operacja niepodzielna i gwarantuje, że cała operacja zakończy się powodzeniem lub cała operacja zostanie wycofana. Jeśli wystąpi błąd po cofnięciu kolejki elementu, ale przed zapisaniem wyniku, cała transakcja zostanie wycofana, a element pozostaje w kolejce do przetworzenia.

## <a name="run-the-application"></a>Uruchamianie aplikacji
Teraz powrócimy do aplikacji *HelloWorld* . Teraz możesz kompilować i wdrażać usługi. Po naciśnięciu klawisza **F5** aplikacja zostanie skompilowana i wdrożona w klastrze lokalnym.

Po uruchomieniu usług można wyświetlić wygenerowane zdarzenia śledzenia zdarzeń systemu Windows (ETW) w oknie **zdarzeń diagnostycznych** . Należy zauważyć, że wyświetlane zdarzenia pochodzą z usługi bezstanowej i usługi stanowej w aplikacji. Strumień można wstrzymać, klikając przycisk **Wstrzymaj** . Następnie można przeanalizować szczegóły komunikatu, rozszerzając ten komunikat.

> [!NOTE]
> Przed uruchomieniem aplikacji upewnij się, że masz uruchomiony lokalny klaster programistyczny. Zapoznaj się z [przewodnikiem wprowadzenie](service-fabric-get-started.md) , aby uzyskać informacje na temat konfigurowania środowiska lokalnego.
> 
> 

![Wyświetlanie zdarzeń diagnostycznych w programie Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Następne kroki
[Debugowanie aplikacji Service Fabric w programie Visual Studio](service-fabric-debugging-your-application.md)

[Wprowadzenie: Service Fabric usług interfejsu API sieci Web za pomocą samoobsługowego udostępniania usługi OWIN](./service-fabric-reliable-services-communication-aspnetcore.md)

[Dowiedz się więcej na temat niezawodnych kolekcji](service-fabric-reliable-services-reliable-collections.md)

[Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[Uaktualnienie aplikacji](service-fabric-application-upgrade.md)

[Dokumentacja dla deweloperów Reliable Services](/previous-versions/azure/dn706529(v=azure.100))
