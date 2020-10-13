---
title: Powiadomienia Reliable Services
description: Dokumentacja dotycząca pojęć związanych z Service Fabric Reliable Services powiadomieniami dotyczącymi niezawodnego menedżera stanu i niezawodnego słownika
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a336daf9bd7400d049233a22a04d64d561b42c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021956"
---
# <a name="reliable-services-notifications"></a>Powiadomienia Reliable Services
Powiadomienia umożliwiają klientom śledzenie zmian wprowadzonych w obiekcie, który Cię interesuje. Dwa typy obiektów obsługują powiadomienia: *niezawodnego menedżera stanu* i *niezawodnego słownika*.

Typowe przyczyny używania powiadomień to:

* Tworzenie widoków z materiałami, takich jak indeksy pomocnicze lub zagregowane widoki filtrowane stanu repliki. Przykładem jest posortowany indeks wszystkich kluczy w niezawodnym słowniku.
* Wysyłanie danych monitorowania, takich jak liczba użytkowników dodanych w ciągu ostatniej godziny.

Powiadomienia są uruchamiane w ramach stosowania operacji. Z tego powodu powiadomienia powinny być obsługiwane tak szybko, jak to możliwe, a zdarzenia synchroniczne nie powinny zawierać żadnych kosztownych operacji.

## <a name="reliable-state-manager-notifications"></a>Powiadomienia Menedżera niezawodnego stanu
Niezawodny Menedżer stanu udostępnia powiadomienia dla następujących zdarzeń:

* Transakcja
  * Zatwierdzenie
* Menedżer stanu
  * Ponowne kompilowanie
  * Dodanie niezawodnego stanu
  * Usuwanie niezawodnego stanu

Niezawodny Menedżer stanu śledzi bieżące transakcje numerów porządkowych określających. Jedyną zmianą stanu transakcji, która powoduje, że powiadomienie jest wyzwalane, jest zatwierdzenie transakcji.

Niezawodny Menedżer stanu przechowuje zbiór niezawodnych Stanów, takich jak niezawodny słownik i niezawodna kolejka. Niezawodny Menedżer stanu wyzwala powiadomienia, gdy ta kolekcja ulegnie zmianie: niezawodny stan jest dodawany lub usuwany albo cała kolekcja została odbudowana.
Kolekcja niezawodnego menedżera stanu jest ponownie skompilowana w trzech przypadkach:

* Odzyskiwanie: po uruchomieniu repliki odzyska poprzedni stan z dysku. Po zakończeniu odzyskiwania używa **NotifyStateManagerChangedEventArgs** do wyzwolenia zdarzenia, które zawiera zestaw odzyskanych niezawodnych Stanów.
* Pełna kopia: zanim replika będzie mogła dołączyć do zestawu konfiguracyjnego, musi być skompilowana. Czasami wymaga to pełnej kopii stanu niezawodnego menedżera stanu z repliki podstawowej, która ma zostać zastosowana do bezczynnej repliki pomocniczej. Niezawodny Menedżer stanu w replice pomocniczej używa **NotifyStateManagerChangedEventArgs** do uruchamiania zdarzenia, które zawiera zestaw niezawodnych Stanów uzyskanych z repliki podstawowej.
* Przywracanie: w scenariuszach odzyskiwania po awarii stan repliki można przywrócić z kopii zapasowej za pomocą **RestoreAsync**. W takich przypadkach niezawodny Menedżer stanu w replice podstawowej używa **NotifyStateManagerChangedEventArgs** do wyzwolenia zdarzenia, które zawiera zestaw niezawodnych Stanów, które zostały przywrócone z kopii zapasowej.

Aby zarejestrować się w celu otrzymywania powiadomień o transakcjach i/lub powiadomień menedżera stanu, należy zarejestrować się ze zdarzeniami **TransactionChanged** lub **StateManagerChanged** w Menedżerze niezawodnego stanu. Typowym miejscem na zarejestrowanie się w tych procedurach obsługi zdarzeń jest Konstruktor usługi stanowej. Po zarejestrowaniu się w konstruktorze nie zostaną pominięte żadne powiadomienia, które są spowodowane zmianą w okresie istnienia **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Procedura obsługi zdarzeń **TransactionChanged** używa **NotifyTransactionChangedEventArgs** w celu dostarczenia szczegółowych informacji o zdarzeniu. Zawiera właściwość akcji (na przykład **NotifyTransactionChangedAction. Commit**), która określa typ zmiany. Zawiera również Właściwość TRANSACTION, która zawiera odwołanie do transakcji, która została zmieniona.

> [!NOTE]
> Dzisiaj zdarzenia **TransactionChanged** są zgłaszane tylko wtedy, gdy transakcja jest zatwierdzona. Ta akcja jest następnie równa **NotifyTransactionChangedAction. Commit**. Jednak w przyszłości zdarzenia mogą być zgłaszane dla innych typów stanu transakcji. Zalecamy sprawdzenie akcji i przetwarzanie zdarzenia tylko wtedy, gdy jest to oczekiwane.
> 
> 

Poniżej znajduje się przykład programu obsługi zdarzeń **TransactionChanged** .

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

Procedura obsługi zdarzeń **StateManagerChanged** używa **NotifyStateManagerChangedEventArgs** w celu dostarczenia szczegółowych informacji o zdarzeniu.
**NotifyStateManagerChangedEventArgs** ma dwie podklasy: **NotifyStateManagerRebuildEventArgs** i **NotifyStateManagerSingleEntityChangedEventArgs**.
Właściwość Action w **NotifyStateManagerChangedEventArgs** służy do rzutowania **NotifyStateManagerChangedEventArgs** do poprawnej podklasy:

* **NotifyStateManagerChangedAction. Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction. Add** i **NotifyStateManagerChangedAction. Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Poniżej znajduje się przykład **StateManagerChanged** obsługi powiadomień.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Powiadomienia o niezawodnym słowniku
Niezawodny słownik zapewnia powiadomienia dla następujących zdarzeń:

* Rebuild: wywoływana, gdy **ReliableDictionary** odzyska swój stan z przywróconego lub skopiowanego stanu lokalnego lub kopii zapasowej.
* Clear: wywoływana, gdy stan elementu **ReliableDictionary** został wyczyszczony za pomocą metody **ClearAsync** .
* Dodaj: wywoływana, gdy element został dodany do **ReliableDictionary**.
* Update: wywoływana, gdy element w **IReliableDictionary** został zaktualizowany.
* Remove: wywoływana, gdy element w **IReliableDictionary** został usunięty.

Aby uzyskać niezawodne powiadomienia słowników, musisz zarejestrować się w programie obsługi zdarzeń **DictionaryChanged** na **IReliableDictionary**. Typowym miejscem do zarejestrowania się w tych procedurach obsługi zdarzeń jest dodanie powiadomienia **ReliableStateManager. StateManagerChanged** .
Rejestrowanie w przypadku dodania **IReliableDictionary** do **IReliableStateManager** gwarantuje, że nie zostaną pominięte żadne powiadomienia.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** to przykładowa Metoda wywołująca poprzedni przykład **OnStateManagerChangedHandler** .
> 
> 

Poprzedni kod ustawia interfejs **IReliableNotificationAsyncCallback** oraz **DictionaryChanged**. Ponieważ **NotifyDictionaryRebuildEventArgs** zawiera interfejs **IAsyncEnumerable** — który musi zostać wyliczony asynchronicznie — powiadomienia odbudowywania są wywoływane przez **RebuildNotificationAsyncCallback** zamiast **OnDictionaryChangedHandler**.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> W powyższym kodzie, w ramach przetwarzania powiadomienia o ponownym przeniesieniu, najpierw jest czyszczony stan "zagregowany". Ponieważ niezawodna kolekcja jest tworzona ponownie z nowym stanem, wszystkie wcześniejsze powiadomienia są nieistotne.
> 
> 

Procedura obsługi zdarzeń **DictionaryChanged** używa **NotifyDictionaryChangedEventArgs** w celu dostarczenia szczegółowych informacji o zdarzeniu.
**NotifyDictionaryChangedEventArgs** ma pięć podklas. Użyj właściwości Action w **NotifyDictionaryChangedEventArgs** , aby rzutować **NotifyDictionaryChangedEventArgs** do poprawnej podklasy:

* **NotifyDictionaryChangedAction. Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction. Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction. Add**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction. Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction. Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Zalecenia
* *Wykonaj* pełne zdarzenia powiadomień tak szybko, jak to możliwe.
* *Nie* wykonuj żadnych kosztownych operacji (na przykład operacji we/wy) w ramach zdarzeń synchronicznych.
* Przed przetworzeniem *zdarzenia Sprawdź typ* akcji. Nowe typy akcji mogą zostać dodane w przyszłości.

Poniżej przedstawiono niektóre kwestie, o których należy pamiętać:

* Powiadomienia są uruchamiane w ramach wykonywania operacji. Na przykład powiadomienie przywracania jest wyzwalane jako ostatni krok operacji przywracania. Przywracanie nie zostanie zakończone, dopóki nie zostanie przetworzone zdarzenie powiadomienia.
* Ponieważ powiadomienia są uruchamiane w ramach operacji zastosowania, klienci widzą tylko powiadomienia dla lokalnie zatwierdzonych operacji. I ponieważ operacje są gwarantowane tylko lokalnie zatwierdzane (innymi słowy, zarejestrowane), mogą one lub nie być cofnięte w przyszłości.
* W przypadku ponownej ścieżki wykonaj pojedyncze powiadomienie dla każdej zastosowanej operacji. Oznacza to, że jeśli transakcja T1 obejmuje tworzenie (X), Delete (X) i Create (X), otrzymasz jedno powiadomienie na potrzeby utworzenia X, jednego do usunięcia, a drugi do utworzenia ponownie, w tej kolejności.
* W przypadku transakcji zawierających wiele operacji operacje są stosowane w kolejności, w jakiej zostały odebrane w replice podstawowej od użytkownika.
* W ramach przetwarzania fałszywych postępów niektóre operacje mogą być cofnięte. Powiadomienia są zgłaszane dla takich operacji cofania, ale wycofywanie stanu repliki z powrotem do stabilnego punktu. Jedną z ważnych różnic między powiadomieniami o cofnięciu jest to, że agregowane są zdarzenia, które mają zduplikowane klucze. Na przykład jeśli transakcja T1 jest cofnięta, zobaczysz pojedyncze powiadomienie do usunięcia (X).

## <a name="next-steps"></a>Następne kroki
* [Elementy Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services Szybki Start](service-fabric-reliable-services-quick-start.md)
* [Reliable Services kopii zapasowej i przywracania (odzyskiwanie po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Dokumentacja dla deweloperów dla niezawodnych kolekcji](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)
