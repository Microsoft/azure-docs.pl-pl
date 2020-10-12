---
title: ReliableConcurrentQueue na platformie Azure Service Fabric
description: ReliableConcurrentQueue to kolejka o wysokiej przepływności, która umożliwia równoległe enqueues i dequeues.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: 423ef3d1898176d7c25c596ad186a9c000108aa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257446"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Wprowadzenie do ReliableConcurrentQueue na platformie Azure Service Fabric
Niezawodna Kolejka współbieżna jest asynchroniczną, transakcyjną i replikowaną kolejką, która zapewnia wysoką współbieżność dla operacji na kolejce i w kolejce. Zaprojektowano w celu zapewnienia wysokiej przepływności i małych opóźnień dzięki złagodzeniu wymagań dotyczących ścisłej kolejności FIFO dostarczonej przez [niezawodną kolejkę](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1) , a zamiast tego zapewnia optymalną kolejność.

## <a name="apis"></a>Interfejsy API

|Kolejka współbieżna                |Niezawodna kolejka współbieżna                                         |
|--------------------------------|------------------------------------------------------------------|
| Anulowanie kolejki (element T)           | Task EnqueueAsync (ITransaction TX, T element)                       |
| bool — TryDequeue (wynik T)  | Zadanie< ConditionalValue < T > > TryDequeueAsync (ITransaction TX)  |
| Liczba int ()                    | Liczba długa ()                                                     |

## <a name="comparison-with-reliable-queue"></a>Porównanie z [niezawodną kolejką](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1)

Niezawodna Kolejka współbieżna jest oferowana jako alternatywa dla [niezawodnej kolejki](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1). Powinna być używana w przypadkach, gdy ścisła kolejność FIFO nie jest wymagana, ponieważ gwarantuje to, że funkcja FIFO wymaga kompromisu z współbieżnością.  [Niezawodna Kolejka](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1) używa blokad, aby wymusić kolejność FIFO, z co najwyżej jedną transakcją dopuszczalną do kolejki i co najwyżej jedną transakcję, która może zostać odrzucona jednocześnie. W porównaniu, niezawodna Kolejka współbieżna ogranicza ograniczenia kolejności i pozwala na równoczesne wykonywanie operacji w kolejce i usuwaniu ich z kolejki. Podano optymalny sposób porządkowania, ale względne porządkowanie dwóch wartości w niezawodnej kolejki współbieżnej nigdy nie jest gwarantowane.

Niezawodna Kolejka współbieżna zapewnia wyższą przepływność i mniejsze opóźnienia niż [niezawodna Kolejka](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1) , gdy istnieje wiele współbieżnych transakcji wykonujących enqueues i/lub wszystkie kolejki.

Przykładowy przypadek użycia dla ReliableConcurrentQueue jest scenariuszem [kolejki komunikatów](https://en.wikipedia.org/wiki/Message_queue) . W tym scenariuszu co najmniej jeden producent wiadomości tworzy i dodaje elementy do kolejki, a co najmniej jeden odbiorca wiadomości pobiera komunikaty z kolejki i przetwarza je. Wielu producentów i konsumenci mogą działać niezależnie, korzystając z współbieżnych transakcji w celu przetworzenia kolejki.

## <a name="usage-guidelines"></a>Zalecenia dotyczące użytkowania
* Kolejka oczekuje, że elementy w kolejce mają niski okres przechowywania. Oznacza to, że elementy nie pozostają w kolejce przez długi czas.
* Kolejka nie gwarantuje ścisłej kolejności FIFO.
* Kolejka nie odczytuje własnych zapisów. Jeśli element znajduje się w kolejce transakcji, nie będzie widoczny dla dequeueer w ramach tej samej transakcji.
* Dekolejki nie są od siebie odizolowane. Jeśli element *a* zostanie usunięty z kolejki w transakcji *txnA*, nawet jeśli *txnA* nie jest zadeklarowany, element *A* nie byłby widoczny dla współbieżnych transakcji *txnB*.  Jeśli *txnA* zostanie przerwana, staje się *od* razu widoczny do *txnB* .
* Zachowanie *TryPeekAsync* można zaimplementować przy użyciu *TryDequeueAsync* , a następnie przerwać transakcję. Przykład tego zachowania można znaleźć w sekcji wzorców programowania.
* Liczba nie jest transakcyjna. Może służyć do uzyskania pomysłu dotyczącego liczby elementów w kolejce, ale reprezentuje punkt w czasie i nie może być używany.
* Kosztowne przetwarzanie w oddziałach elementów nie powinno być wykonywane, gdy transakcja jest aktywna, aby uniknąć długotrwałych transakcji, które mogą mieć wpływ na wydajność systemu.

## <a name="code-snippets"></a>Wstawki kodu
Poczekaj, aż kilka fragmentów kodu i ich oczekiwanych danych wyjściowych. Obsługa wyjątków została zignorowana w tej sekcji.

### <a name="instantiation"></a>Tworzenie wystąpienia
Tworzenie wystąpienia niezawodnej kolejki współbieżnej jest podobne do innych niezawodnych kolekcji.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Oto kilka fragmentów kodu do użycia EnqueueAsync, a następnie ich oczekiwanych danych wyjściowych.

- *Przypadek 1: zadanie pojedynczej kolejki*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Załóżmy, że zadanie zostało ukończone pomyślnie oraz że nie wystąpiły żadne współbieżne transakcje modyfikujące kolejkę. Użytkownik może oczekiwać, aby Kolejka zawierała elementy w jednym z następujących zamówień:

> 10, 20
> 
> 20, 10


- *Przypadek 2: równoległe zadanie w kolejce*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Załóżmy, że zadania zostały ukończone pomyślnie, wykonane równolegle zadania i że nie było żadnych innych współbieżnych transakcji modyfikujących kolejkę. Nie można wykonać wnioskowania o kolejności elementów w kolejce. Dla tego fragmentu kodu elementy mogą pojawić się w dowolnym z 4! możliwe zamówienia.  Kolejka podejmie próbę zachowania elementów w pierwotnej (kolejkowanej) kolejności, ale może zostać wymuszone ich zmiana kolejności z powodu równoczesnych operacji lub błędów.


### <a name="dequeueasync"></a>DequeueAsync
Oto kilka fragmentów kodu do użycia TryDequeueAsync, a następnie oczekiwanych danych wyjściowych. Załóżmy, że kolejka została już wypełniona następującymi elementami w kolejce:
> 10, 20, 30, 40, 50, 60

- *Przypadek 1: pojedyncze zadanie usuwania z kolejki*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Załóżmy, że zadanie zostało ukończone pomyślnie oraz że nie wystąpiły żadne współbieżne transakcje modyfikujące kolejkę. Ponieważ nie można wywnioskować o kolejności elementów w kolejce, wszystkie trzy elementy mogą być dekolejkowane w dowolnej kolejności. Kolejka podejmie próbę zachowania elementów w pierwotnej (kolejkowanej) kolejności, ale może zostać wymuszone ich zmiana kolejności z powodu równoczesnych operacji lub błędów.  

- *Przypadek 2: równoległe zadanie usuwania z kolejki*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Załóżmy, że zadania zostały ukończone pomyślnie, wykonane równolegle zadania i że nie było żadnych innych współbieżnych transakcji modyfikujących kolejkę. Ponieważ nie można wywnioskować o kolejności elementów w kolejce, listy *dequeue1* i *dequeue2* będą zawierać dowolne dwa elementy w dowolnej kolejności.

Ten sam element *nie* będzie widoczny na obu listach. W związku z tym, jeśli dequeue1 ma *10*, *30*, wówczas dequeue2 byłaby *20*, *40*.

- *Przypadek 3: porządkowanie w kolejce z przerwaniem transakcji*

Przerwanie transakcji z odłożeniami w locie powoduje umieszczenie elementów z powrotem na początku kolejki. Kolejność, w której elementy są umieszczane na początku kolejki, nie jest gwarantowana. Poinformuj nas o następującym kodzie:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Załóżmy, że elementy zostały rozkolejkowane w następującej kolejności:
> 10, 20

Po przerwaniu transakcji elementy zostaną dodane z powrotem do szefa kolejki w jednym z następujących zamówień:
> 10, 20
> 
> 20, 10

Ta sama wartość dotyczy wszystkich przypadków, w których transakcja nie została pomyślnie *zatwierdzona*.

## <a name="programming-patterns"></a>Wzorce programowania
W tej sekcji poinformuj nas o kilku wzorcach programistycznych, które mogą być pomocne przy użyciu ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Dekolejki wsadowe
Zalecany wzorzec programistyczny służy do wykonywania zadania wsadowego w partiach, a nie do jednej dekolejki w danym momencie. Użytkownik może zdecydować się na ograniczenie opóźnień między wszystkimi partiami lub rozmiarem partii. Poniższy fragment kodu przedstawia ten model programowania. Należy pamiętać, że w tym przykładzie przetwarzanie jest wykonywane po zatwierdzeniu transakcji, więc jeśli wystąpi błąd podczas przetwarzania, nieprzetwarzane elementy zostaną utracone bez przetworzenia.  Alternatywnie przetwarzanie może odbywać się w zakresie transakcji, jednak może mieć negatywny wpływ na wydajność i wymaga obsługi już przetworzonych elementów.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Best-Effort Notification-Based przetwarzania
Inny interesujący wzór programistyczny używa interfejsu API Count. Tutaj można zaimplementować dla kolejki optymalne, oparte na powiadomieniach przetwarzanie. Liczba kolejek może być używana do ograniczania do kolejki lub zadania usuwania z kolejki.  Należy pamiętać, że tak jak w poprzednim przykładzie, ponieważ przetwarzanie następuje poza transakcją, elementy nieprzetworzone mogą zostać utracone, jeśli wystąpi błąd podczas przetwarzania.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Best-Effort opróżniania
Nie można zagwarantować opróżniania kolejki z powodu jednoczesnego charakteru struktury danych.  Istnieje możliwość, że nawet jeśli żadne operacje użytkownika w kolejce nie są wykonywane w locie, określone wywołanie TryDequeueAsync może nie zwracać elementu, który był wcześniej w kolejce i został przekazany.  Element znajdujący się w kolejce jest zagwarantowany, *aby stał się* widoczny do usuwania z kolejki, ale bez mechanizmu komunikacji poza pasmem, niezależny konsument nie może wiedzieć, że kolejka osiągnęła stan stabilny, nawet jeśli wszyscy producenci zostali zatrzymani i nie są dozwolone żadne nowe operacje w kolejce. W ten sposób operacja opróżniania jest optymalna dla implementacji poniżej.

Użytkownik powinien zatrzymać wszystkie pozostałe zadania producenta i konsumenta oraz poczekać na zatwierdzenie lub przerwanie transakcji w locie przed podjęciem próby opróżnienia kolejki.  Jeśli użytkownik zna oczekiwaną liczbę elementów w kolejce, może skonfigurować powiadomienie, które sygnalizuje, że wszystkie elementy zostały odkolejkowane.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Peek
ReliableConcurrentQueue nie udostępnia interfejsu API *TryPeekAsync* . Użytkownicy mogą uzyskać semantykę wglądu przy użyciu *TryDequeueAsync* , a następnie przerwać transakcję. W tym przykładzie dequeues są przetwarzane tylko wtedy, gdy wartość elementu jest większa niż *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Należy przeczytać
* [Reliable Services — Szybki Start](service-fabric-reliable-services-quick-start.md)
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Powiadomienia Reliable Services](service-fabric-reliable-services-notifications.md)
* [Reliable Services kopii zapasowej i przywracania (odzyskiwanie po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Konfiguracja niezawodnego menedżera stanu](service-fabric-reliable-services-configuration.md)
* [Wprowadzenie z usługami interfejsu API sieci Web Service Fabric](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Zaawansowane użycie modelu programowania Reliable Services](./service-fabric-reliable-services-lifecycle.md)
* [Dokumentacja dla deweloperów dla niezawodnych kolekcji](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)
