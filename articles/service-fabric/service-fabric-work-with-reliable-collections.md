---
title: Praca z elementami Reliable Collections
description: Poznaj najlepsze rozwiązania dotyczące pracy z niezawodnymi kolekcjami w aplikacji Service Fabric platformy Azure.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 2d027dc432d1a0a20888bfca4f59bc41866e358d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651910"
---
# <a name="working-with-reliable-collections"></a>Praca z elementami Reliable Collections
Service Fabric oferuje model programowania stanowego dostępny dla deweloperów platformy .NET za pośrednictwem niezawodnych kolekcji. W Service Fabric udostępnia niezawodne słowniki i niezawodne klasy kolejek. W przypadku korzystania z tych klas stan jest partycjonowany (na potrzeby skalowalności), replikowany (na potrzeby dostępności) i transakcyjny w ramach partycji (dla semantyki KWASowej). Przyjrzyjmy się typowi użycia niezawodnego obiektu słownika i zobacz, co faktycznie robi.

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
   await Task.Delay(100);
}
```

Wszystkie operacje na obiektach niezawodnego słownika (z wyjątkiem ClearAsync, których nie można cofnąć) wymagają obiektu ITransaction. Ten obiekt jest skojarzony z nim i wszystkie zmiany, które próbujesz wprowadzić do dowolnego niezawodnego słownika i/lub niezawodnych obiektów kolejki w ramach jednej partycji. Pozyskasz obiekt ITransaction przez wywołanie metody StateManager's.

W powyższym kodzie obiekt ITransaction jest przesyłany do metody addasync w niezawodnym słowniku. Wewnętrznie metody słownika, które akceptują klucz, przyjmują blokadę czytnika/składnika zapisywania skojarzonego z kluczem. Jeśli Metoda modyfikuje wartość klucza, metoda przyjmuje blokadę zapisu klucza i jeśli metoda tylko odczytuje z wartości klucza, wówczas na kluczu jest wykonywana blokada odczytu. Ponieważ metoda addasync modyfikuje wartość klucza do nowej, przekazaną wartość, jest wykonywana blokada zapisu klucza. Tak więc, jeśli 2 (lub więcej) wątków próbuje dodać wartości z tym samym kluczem w tym samym czasie, jeden wątek uzyska blokadę zapisu, a pozostałe wątki zostaną zablokowane. Domyślnie metody blokują do 4 sekund, aby uzyskać blokadę; po upływie 4 sekund Metoda zgłasza wyjątek limitu czasu. Istnieją przeciążenia metod, które umożliwiają przekazywanie jawnej wartości limitu czasu, jeśli wolisz.

Zwykle napiszesz kod w celu reagowania na licznik limit czasu przez przechwycenie go i ponowienie całej operacji (jak pokazano w powyższym kodzie). W tym prostym kodzie właśnie wywołujemy zadanie. Opóźnij okresowe przekazywanie 100 milisekund. Jednak w rzeczywistości lepiej jest używać pewnego rodzaju wykładniczego opóźnienia wycofywania.

Po pobraniu blokady addasync dodaje odwołania do obiektu klucza i wartości do wewnętrznego słownika tymczasowego skojarzonego z obiektem ITransaction. Jest to gotowe do udostępnienia semantyki do odczytu i zapisu. Oznacza to, że po wywołaniu addasync, późniejsze wywołanie do TryGetValueAsync przy użyciu tego samego obiektu ITransaction zwróci wartość nawet wtedy, gdy transakcja nie została jeszcze zatwierdzona.

> [!NOTE]
> Wywołanie TryGetValueAsync z nową transakcją zwróci odwołanie do ostatniej przekazanej wartości. Nie należy modyfikować tego odwołania bezpośrednio, ponieważ pomija mechanizm utrwalania i replikowania zmian. Zalecamy wprowadzanie wartości tylko do odczytu, aby jedynym sposobem na zmianę wartości klucza jest użycie niezawodnych interfejsów API słownika.

Następnie funkcja addasync serializować obiekty klucza i wartości do tablic bajtowych i dołącza te tablice bajtowe do pliku dziennika w węźle lokalnym. Na koniec Metoda addasync wysyła tablice bajtów do wszystkich replik pomocniczych, dzięki czemu mają one te same informacje o kluczu/wartości. Mimo że informacje o kluczu/wartości są zapisywane w pliku dziennika, informacje nie są uważane za część słownika, dopóki transakcja, z którą są skojarzone, została zatwierdzona.

W powyższym kodzie, wywołanie do CommitAsync zatwierdza wszystkie operacje transakcji. W celu dołączenia informacji o zatwierdzeniu do pliku dziennika w węźle lokalnym, a także do wszystkich replik pomocniczych. Po odinstalowaniu kworum (większości) replik wszystkie zmiany danych są uznawane za trwałe i wszystkie blokady skojarzone z kluczami, które były manipulowane za pośrednictwem obiektu ITransaction, są uwalniane, więc inne wątki/transakcje mogą manipulować tymi samymi kluczami i ich wartościami.

Jeśli CommitAsync nie jest wywoływana (zazwyczaj z powodu zgłaszanego wyjątku), obiekt ITransaction zostanie usunięty. Podczas likwidowania niezatwierdzonego obiektu ITransaction Service Fabric dołącza informacje o przerwaniu do pliku dziennika węzła lokalnego i nic nie należy wysyłać do żadnej z replik pomocniczych. Następnie zostaną wydane wszystkie blokady skojarzone z kluczami, które były manipulowane za pośrednictwem transakcji.

## <a name="volatile-reliable-collections"></a>Niezawodne kolekcje nietrwałe 
W przypadku niektórych obciążeń, takich jak replikowana pamięć podręczna, można tolerować okazjonalną utratę danych. Uniknięcie trwałości danych na dysku może pozwolić na lepsze opóźnienia i przepływność podczas zapisywania do niezawodnych słowników. Wadą braku trwałości jest to, że jeśli nastąpi utrata danych kworum, nastąpi wypróbowanie pełnego stanu. Ze względu na to, że utrata kworum jest rzadkim wystąpieniem, zwiększona wydajność może być cenną sytuacją utraty danych dla tych obciążeń.

Obecnie obsługa nietrwałych jest dostępna tylko dla niezawodnych słowników i niezawodnych kolejek, a nie ReliableConcurrentQueues. Zapoznaj się z listą zarzucania [, aby poinformować](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) decyzję o tym, czy należy używać kolekcji nietrwałych.

Aby włączyć obsługę nietrwałą w usłudze, ustaw ```HasPersistedState``` flagę w deklaracji typu usługi na ```false``` , na przykład:
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>Istniejących utrwalonych usług nie można uczynić volatile i na odwrót. Jeśli chcesz to zrobić, należy usunąć istniejącą usługę, a następnie wdrożyć usługę ze zaktualizowaną flagą. Oznacza to, że przed zmianą flagi należy ponieść pełną utratę danych ```HasPersistedState``` . 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Typowe pułapek i sposoby ich unikania
Teraz, gdy zrozumiesz, jak niezawodne kolekcje pracują wewnętrznie, przyjrzyjmy się niektórym typowym nieprawidłowym objęciem. Zobacz poniższy kod:

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

Podczas pracy ze zwykłym słownikiem platformy .NET można dodać klucz/wartość do słownika, a następnie zmienić wartość właściwości (na przykład LastLogin). Jednak ten kod nie będzie działał poprawnie z niezawodnym słownikiem. Pamiętaj o wcześniejszej dyskusji, wywołanie metody addasync serializacji obiektów key/value do tablic bajtowych, a następnie zapisuje tablice do pliku lokalnego i wysyła je do replik pomocniczych. Jeśli później zmienisz właściwość, spowoduje to zmianę wartości właściwości tylko w pamięci. nie ma to wpływu na plik lokalny ani dane wysyłane do replik. Jeśli proces ulegnie awarii, zawartość pamięci jest wyrzucana. Gdy rozpocznie się nowy proces lub jeśli inna replika jest podstawowa, wówczas stara wartość właściwości jest dostępna.

Nie mogę obciążeniować wystarczającej ilości pokazanej powyżej błędu. Ponadto poznasz tylko błąd, jeśli/gdy proces ulegnie awarii. Prawidłowy sposób pisania kodu to po prostu odwrócenia dwóch wierszy:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Oto inny przykład przedstawiający często występujący błąd:

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

Ponownie dzięki zwykłym słownikom platformy .NET kod powyżej działa prawidłowo i jest wspólnym wzorcem: programista korzysta z klucza w celu wyszukania wartości. Jeśli wartość istnieje, deweloper zmieni wartość właściwości. Jednak w przypadku niezawodnych kolekcji ten kod wykazuje ten sam problem, który został już omówiony: **nie należy modyfikować obiektu po nadaniu go do niezawodnej kolekcji.**

Prawidłowy sposób aktualizowania wartości w niezawodnej kolekcji polega na uzyskaniu odwołania do istniejącej wartości i rozważenia niezmiennego obiektu, do którego odwołuje się to odwołanie. Następnie utwórz nowy obiekt, który jest dokładną kopią oryginalnego obiektu. Teraz można zmodyfikować stan tego nowego obiektu i zapisać nowy obiekt w kolekcji tak, aby był on serializowany do tablic bajtowych, dołączany do pliku lokalnego i wysyłany do replik. Po zatwierdzeniu zmian, obiektów znajdujących się w pamięci, pliku lokalnym i wszystkich replik ma ten sam stan. Wszystko jest dobre!

Poniższy kod pokazuje poprawny sposób aktualizowania wartości w niezawodnej kolekcji:

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

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Zdefiniuj niezmienne typy danych, aby zapobiec błędom programisty
Najlepiej, gdy chcemy, aby kompilator zgłaszał błędy w przypadku przypadkowego utworzenia kodu, który jest niezmiennym stanem obiektu. Jednak kompilator języka C# nie ma możliwości wykonania tej czynności. Aby uniknąć potencjalnych błędów programisty, zdecydowanie zalecamy zdefiniowanie typów używanych z niezawodnymi kolekcjami, które mają być niezmienne. W związku z tym oznacza to, że można nawiązać podstawowe typy wartości (takie jak liczby [Int32, UInt64, itp.], DateTime, GUID, TimeSpan i like). Można również użyć ciągu. Najlepszym rozwiązaniem jest uniknięcie właściwości kolekcji jako serializacji i deserializacji ich, co może często pogarszać wydajność. Jeśli jednak chcesz użyć właściwości kolekcji, zdecydowanie zalecamy korzystanie z programu. Biblioteka niemodyfikowalnych kolekcji w sieci ([System. Collections.](https://www.nuget.org/packages/System.Collections.Immutable/)). Ta biblioteka jest dostępna do pobrania z programu https://nuget.org . Zalecamy również opieczętowanie klas i udostępnianie pól tylko do odczytu, gdy tylko jest to możliwe.

Poniższy typ UserInfo przedstawia sposób definiowania niezmiennego typu wykorzystującego wymienione wcześniej zalecenia.

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

Typ elementu ItemId jest również niezmiennym typem, jak pokazano poniżej:

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
Wewnętrznie, niezawodne kolekcje serializacji obiektów przy użyciu. Obiekt DataContractSerializer sieci. Serializowane obiekty są utrwalane na lokalnym dysku repliki podstawowej i są również przesyłane do replik pomocniczych. W miarę dojrzewania usługi prawdopodobnie chcesz zmienić rodzaj danych (schematu) wymaganego przez usługę. Zadbaj o przechowywanie wersji swoich danych. Przede wszystkim należy zawsze mieć możliwość deserializacji starych danych. W związku z tym oznacza to, że kod deserializacji musi być niezgodny z poprzednimi wersjami: wersja 333 kodu usługi musi być w stanie pracować nad danymi umieszczonymi w niezawodnej kolekcji, w wersji 1 roku temu.

Ponadto kod usługi jest uaktualniany po jednej domenie uaktualnienia w danym momencie. W trakcie uaktualniania dostępne są dwie różne wersje kodu usługi jednocześnie. Należy unikać, aby nowa wersja kodu usługi korzystała z nowego schematu, ponieważ stare wersje kodu usługi mogą nie być w stanie obsłużyć nowego schematu. Jeśli to możliwe, należy zaprojektować każdą wersję usługi, aby była dalej zgodna z jedną wersją. W związku z tym oznacza to, że w wersji 1 kodu usługi powinna być możliwa ignorowanie wszelkich elementów schematu, które nie są jawnie obsługiwane. Jednak musi mieć możliwość zapisywania wszelkich danych, które nie są wyraźnie znane, i zapisywać je z powrotem podczas aktualizowania klucza lub wartości słownika.

> [!WARNING]
> Chociaż można modyfikować schemat klucza, należy upewnić się, że jego kod skrótu i algorytm Equals są stabilne. Jeśli zmienisz sposób działania jednego z tych algorytmów, nie będzie można ponownie wyszukiwać klucza w niezawodnym słowniku.
> Ciągi .NET mogą służyć jako klucz, ale jako klucz używają samego ciągu, ale jako klucza nie należy używać wyniku String. GetHashCode.

Alternatywnie można wykonać czynności, które zwykle są określane jako dwa uaktualnienia. W przypadku uaktualniania dwufazowego należy uaktualnić usługę z wersji od 1 do v2: V2 zawiera kod, który zna sposób postępowania z nową zmianą schematu, ale ten kod nie jest wykonywany. Gdy kod v2 odczytuje dane V1, działa na nim i zapisuje dane v1. Następnie po zakończeniu uaktualniania we wszystkich domenach uaktualnienia można w jakiś sposób sygnalizować sygnały z uruchomionym wystąpieniem v2, że uaktualnienie zostało ukończone. (Jeden ze sposobów na sygnalizowanie polega na przewróceniu uaktualnienia konfiguracji; jest to co sprawia, że jest to dwufazowe uaktualnienie). Teraz wystąpienia v2 mogą odczytywać dane w wersji 1, konwertować je na dane w wersji 2, wykonywać na nich operacje i zapisywać je jako dane w wersji 2. Gdy inne wystąpienia odczytują dane w wersji 2, nie wymagają ich konwersji, działają na nich i zapisują dane w wersji 2.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tworzeniu kontraktowych zgodnych danych, zobacz [Kontrakty danych zgodne z przekazywaniem dalej](/dotnet/framework/wcf/feature-details/forward-compatible-data-contracts)

Aby poznać najlepsze rozwiązania dotyczące wersji umów dotyczących danych, zobacz [przechowywanie wersji kontraktu danych](/dotnet/framework/wcf/feature-details/data-contract-versioning)

Aby dowiedzieć się, jak zaimplementować Kontrakty danych odporne na wersje, zobacz [wywołania zwrotne serializacji z odpornością na wersje](/dotnet/framework/wcf/feature-details/version-tolerant-serialization-callbacks)

Aby dowiedzieć się, jak zapewnić strukturę danych, która może współdziałać z wieloma wersjami, zobacz [IExtensibleDataObject](/dotnet/api/system.runtime.serialization.iextensibledataobject?view=netcore-3.1)
