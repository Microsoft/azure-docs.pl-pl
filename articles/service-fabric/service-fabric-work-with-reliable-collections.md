---
title: Praca z elementami Reliable Collections
description: Poznaj najlepsze rozwiązania dotyczące pracy z niezawodnymi kolekcjami w aplikacji sieci szkieletowej usług Azure.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94836a37a62e3eeffb94d891980cc02694bd973e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409803"
---
# <a name="working-with-reliable-collections"></a>Praca z elementami Reliable Collections
Sieć szkieletowa usług oferuje modelu programowania stanowego dostępne dla deweloperów platformy .NET za pośrednictwem niezawodnych kolekcji. W szczególności sieci szkieletowej usług zapewnia niezawodny słownik i niezawodne klasy kolejki. Podczas korzystania z tych klas, stan jest podzielony na partycje (dla skalowalności), replikowane (dla dostępności) i transakcje w ramach partycji (dla semantyki ACID). Przyjrzyjmy się typowe użycie niezawodnego obiektu słownika i zobaczmy, co faktycznie robi.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
}
```

Wszystkie operacje na niezawodnych obiektach słownika (z wyjątkiem ClearAsync, który nie jest cofnąć), wymagają ITransaction obiektu. Ten obiekt ma skojarzone z nim wszystkie zmiany, które próbujesz wprowadzić do dowolnego niezawodnego słownika i/lub niezawodne obiekty kolejki w ramach jednej partycji. Można uzyskać ITransaction obiektu wywołując partycji StateManager's CreateTransaction metody.

W powyższym kodzie ITransaction obiekt jest przekazywany do niezawodnego słownika AddAsync metody. Wewnętrznie metody słownika, które akceptują klucz, przyjmują blokadę czytnika/modułu zapisującego skojarzoną z kluczem. Jeśli metoda modyfikuje wartość klucza, metoda przyjmuje blokadę zapisu na klucz i jeśli metoda odczytuje tylko z wartości klucza, a następnie blokada odczytu jest pobierana na klucz. Ponieważ AddAsync modyfikuje wartość klucza do nowej, przekazanej wartości, blokada zapisu klucza jest podejmowana. Tak więc jeśli 2 (lub więcej) wątków próbuje dodać wartości z tym samym kluczem w tym samym czasie, jeden wątek uzyska blokadę zapisu, a inne wątki zablokują. Domyślnie metody blokują do 4 sekund, aby uzyskać blokadę; po 4 sekundach metody rzucają TimeoutException. Istnieją przeciążenia metody, co pozwala przekazać jawną wartość limitu czasu, jeśli wolisz.

Zazwyczaj piszesz kod, aby reagować na TimeoutException, przeładując go i ponowiając próbę całej operacji (jak pokazano w powyższym kodzie). W moim prostym kodzie po prostu dzwonię do Task.Delay przekazując za każdym razem 100 milisekund. Ale w rzeczywistości, może być lepiej przy użyciu jakiegoś wykładniczego opóźnienia back-off zamiast.

Po nabyciu blokady AddAsync dodaje odwołania do obiektu klucza i wartości do wewnętrznego słownika tymczasowego skojarzonego z obiektem ITransaction. Odbywa się to w celu zapewnienia ci semantyki odczytu własnego. Oznacza to, że po wywołaniu AddAsync, późniejsze wywołanie TryGetValueAsync (przy użyciu tego samego obiektu ITransaction) zwróci wartość, nawet jeśli nie zostały jeszcze zatwierdzone transakcji. Następnie AddAsync serializuje obiekty klucza i wartości do tablic bajtowych i dołącza te tablice bajtów do pliku dziennika w węźle lokalnym. Na koniec AddAsync wysyła tablice bajtów do wszystkich replik pomocniczych, dzięki czemu mają te same informacje o kluczu/wartości. Mimo że informacje o kluczu/wartości zostały zapisane w pliku dziennika, informacje nie są uważane za część słownika, dopóki transakcja, z którą są skojarzone, nie zostanie zatwierdzona.

W powyższym kodzie wywołanie CommitAsync zatwierdza wszystkie operacje transakcji. W szczególności dołącza informacje o zatwierdzeniu do pliku dziennika w węźle lokalnym, a także wysyła rekord zatwierdzenia do wszystkich replik pomocniczych. Gdy kworum (większość) replik odpowiedział, wszystkie zmiany danych są uważane za trwałe i wszelkie blokady skojarzone z kluczami, które zostały manipulowane za pośrednictwem ITransaction obiektu są zwalniane, więc inne wątki/transakcje mogą manipulować te same klucze i ich wartości.

Jeśli CommitAsync nie jest wywoływana (zwykle z powodu wyjątku są generowane), a następnie ITransaction obiekt zostanie usunięty. Podczas usuwania niezatwierdzonego obiektu ITransaction sieć szkieletowa usług dołącza informacje o przerwaniu do pliku dziennika węzła lokalnego i nic nie musi być wysyłane do żadnej z replik pomocniczych. A następnie wszystkie blokady skojarzone z kluczami, które zostały zmanipulowane za pośrednictwem transakcji są zwalniane.

## <a name="volatile-reliable-collections"></a>Niestabilne niezawodne kolekcje 
W niektórych obciążeniach, takich jak replikowanej pamięci podręcznej, na przykład sporadyczne utraty danych mogą być tolerowane. Unikanie trwałości danych na dysku może umożliwić lepsze opóźnienia i przepływności podczas zapisywania do słowników niezawodne. Kompromisem dla braku trwałości jest to, że jeśli wystąpi utrata kworum, nastąpi pełna utrata danych. Ponieważ utrata kworum jest rzadkim zjawiskiem, zwiększona wydajność może być warta rzadkiej możliwości utraty danych dla tych obciążeń.

Obecnie obsługa volatile jest dostępna tylko dla niezawodnych słowników i niezawodnych kolejek, a nie ReliableConcurrentQueues. Zobacz listę [zastrzeżeń,](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) aby poinformować o decyzji, czy używać kolekcji lotnych.

Aby włączyć nietrwałą obsługę ```HasPersistedState``` w usłudze, ```false```ustaw flagę w deklaracji typu usługi na tak:
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>Istniejące usługi utrwalone nie mogą być niestabilne i odwrotnie. Jeśli chcesz to zrobić, należy usunąć istniejącą usługę, a następnie wdrożyć usługę ze zaktualizowaną flagą. Oznacza to, że musisz być skłonny do poniesienia pełnej utraty danych, jeśli chcesz zmienić flagę. ```HasPersistedState``` 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Typowe pułapki i jak ich uniknąć
Teraz, gdy zrozumiesz, jak niezawodne kolekcje działają wewnętrznie, przyjrzyjmy się niektórym typowym nadużyciom. Zobacz poniższy kod:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Podczas pracy ze zwykłym słownikiem .NET można dodać klucz/wartość do słownika, a następnie zmienić wartość właściwości (na przykład LastLogin). Jednak ten kod nie będzie działać poprawnie z niezawodnego słownika. Pamiętaj z wcześniejszej dyskusji, wywołanie AddAsync serializes klucz/wartość obiektów do tablic bajtowych, a następnie zapisuje tablice do pliku lokalnego, a także wysyła je do replik pomocniczych. Jeśli później zmienisz właściwość, spowoduje to zmianę wartości właściwości tylko w pamięci; nie ma wpływu na plik lokalny ani na dane wysyłane do replik. Jeśli proces ulegnie awarii, co jest w pamięci jest odrzucany. Po uruchomieniu nowego procesu lub jeśli inna replika staje się podstawowa, to stara wartość właściwości jest dostępna.

Nie mogę wystarczająco podkreślić, jak łatwo jest popełnić taki błąd pokazany powyżej. I, dowiesz się tylko o błędzie, jeśli / gdy proces idzie w dół. Prawidłowym sposobem napisania kodu jest po prostu odwrócenie dwóch wierszy:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Oto kolejny przykład pokazujący typowy błąd:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Ponownie w przypadku zwykłych słowników platformy .NET powyższy kod działa poprawnie i jest typowym wzorcem: deweloper używa klucza do wyszukiwania wartości. Jeśli wartość istnieje, deweloper zmienia wartość właściwości. Jednak w niezawodnych kolekcji, ten kod wykazuje ten sam problem, jak już omówione: **nie należy modyfikować obiektu po nadadaniu go do niezawodnej kolekcji.**

Prawidłowym sposobem, aby zaktualizować wartość w kolekcji niezawodne, jest uzyskanie odwołania do istniejącej wartości i należy wziąć pod uwagę obiekt, o którym mowa przez to odwołanie niezmienne. Następnie utwórz nowy obiekt, który jest dokładną kopią oryginalnego obiektu. Teraz można zmodyfikować stan tego nowego obiektu i zapisać nowy obiekt w kolekcji, tak aby pobierał serializowane do tablic bajtów, dołączane do pliku lokalnego i wysyłane do replik. Po zapisaniu zmian obiekty w pamięci, plik lokalny i wszystkie repliki mają ten sam dokładny stan. Wszystko jest dobre!

Poniższy kod pokazuje poprawny sposób, aby zaktualizować wartość w niezawodnej kolekcji:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definiowanie niezmiennych typów danych w celu zapobiegania błędom programisty
W idealnym przypadku firma Kompilatora ma zgłaszać błędy podczas przypadkowego tworzenia kodu, który mutuje stan obiektu, który powinien być uznawany za niezmienny. Ale kompilator języka C# nie ma możliwości, aby to zrobić. Tak, aby uniknąć potencjalnych błędów programisty, zdecydowanie zaleca się zdefiniowanie typów używanych z kolekcji niezawodne być niezmienne typy. W szczególności oznacza to, że należy trzymać się podstawowych typów wartości (takich jak liczby [Int32, UInt64 itp.], DateTime, Guid, TimeSpan i tym podobne). Można również użyć String. Najlepiej jest unikać właściwości kolekcji, jak serializacji i deserializacji ich często boli wydajność. Jeśli jednak chcesz używać właściwości kolekcji, zdecydowanie zalecamy użycie programu . Biblioteka kolekcji niezmiennych net[(System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Ta biblioteka jest https://nuget.orgdostępna do pobrania z . Zalecamy również uszczelnienie zajęć i uczynienie pól tylko do odczytu, gdy tylko jest to możliwe.

Typ UserInfo poniżej pokazuje, jak zdefiniować typ niezmienne przy wykorzystaniu wyżej wymienionych zaleceń.

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

ItemId typ jest również typu niezmienne, jak pokazano poniżej:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Przechowywanie wersji schematu (uaktualnienia)
Wewnętrznie niezawodne kolekcje serializować obiekty za pomocą . Net DataContractSerializer. Obiekty serializowane są utrwalone na dysku lokalnym repliki podstawowej i są również przesyłane do replik pomocniczych. W miarę dojrzewania usługi prawdopodobnie będziesz chciał zmienić rodzaj danych (schematu) wymagana przez usługę. Podejdź do przechowywania wersji danych z wielką starannością. Przede wszystkim zawsze musi być w stanie deserialize starych danych. W szczególności oznacza to, że kod deserializacji musi być nieskończenie wsteczny zgodny: Wersja 333 kodu usługi musi być w stanie działać na danych umieszczonych w niezawodnej kolekcji przez wersję 1 kodu usługi 5 lat temu.

Ponadto kod usługi jest uaktualniany o jedną domenę uaktualnienia naraz. Tak, podczas uaktualniania masz dwie różne wersje kodu usługi uruchomione jednocześnie. Należy unikać konieczności nowej wersji kodu usługi używać nowego schematu, jak stare wersje kodu usługi może nie być w stanie obsłużyć nowy schemat. Jeśli to możliwe, należy zaprojektować każdą wersję usługi, aby była zgodna z jedną wersją. W szczególności oznacza to, że v1 kodu usługi powinny być w stanie zignorować wszystkie elementy schematu, który nie jawnie obsługiwać. Jednak musi być w stanie zapisać wszystkie dane, które nie jawnie wiedzieć o i zapisać go z powrotem podczas aktualizowania klucza słownika lub wartości.

> [!WARNING]
> Chociaż można zmodyfikować schemat klucza, należy upewnić się, że kod skrótu klucza i jest równa algorytmy są stabilne. Jeśli zmienisz sposób działania jednego z tych algorytmów, nie będzie można już nigdy wyszukać klucza w niezawodnym słowniku.
> Ciągi .NET mogą być używane jako klucz, ale używać samego ciągu jako klucza - nie należy używać wyniku String.GetHashCode jako klucz.

Alternatywnie można wykonać to, co jest zwykle określane jako dwa uaktualnienia. Po uaktualnieniu dwufazowym uaktualnienie usługi z wersji 1 do V2: V2 zawiera kod, który wie, jak radzić sobie ze zmianą nowego schematu, ale ten kod nie jest wykonywany. Gdy kod V2 odczytuje dane V1, działa na nim i zapisuje dane V1. Następnie po zakończeniu uaktualniania we wszystkich domenach uaktualnienia można w jakiś sposób zasygnalizować uruchomionym wystąpieniom v2, że uaktualnienie zostało zakończone. (Jednym ze sposobów sygnalizowania tego jest wdrożenie uaktualnienia konfiguracji; to właśnie sprawia, że jest to uaktualnienie dwufazowe). Teraz wystąpienia V2 mogą odczytywać dane V1, konwertować je na dane V2, działać na nim i zapisywać je jako dane V2. Gdy inne wystąpienia odczytują dane V2, nie trzeba ich konwertować, po prostu działają na nim i zapisują dane V2.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tworzeniu zgodnych kontraktów na dane do przodu, zobacz [Kontrakty danych zgodne z przyszłością](https://msdn.microsoft.com/library/ms731083.aspx)

Aby poznać najważniejsze wskazówki dotyczące przechowywania kontraktów na dane, zobacz [Przechowywanie wersji kontraktu danych](https://msdn.microsoft.com/library/ms731138.aspx)

Aby dowiedzieć się, jak zaimplementować kontrakty danych odporne na wersje, zobacz [Wywołania zwrotne serializacji odpornej na wersję](https://msdn.microsoft.com/library/ms733734.aspx)

Aby dowiedzieć się, jak zapewnić strukturę danych, która może współdziałać w wielu wersjach, zobacz [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)
