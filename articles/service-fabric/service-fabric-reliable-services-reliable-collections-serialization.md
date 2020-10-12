---
title: Serializacja niezawodnych obiektów kolekcji
description: Dowiedz się więcej na temat serializacji niezawodnych kolekcji obiektów Service Fabric platformy Azure, w tym domyślnej strategii i sposobu definiowania serializacji niestandardowej.
ms.topic: conceptual
ms.date: 5/8/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 1d29f5d3391bdea0b21997a392d4d2e053b7ec65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018624"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Serializacja niezawodnych obiektów kolekcji na platformie Azure Service Fabric
Niezawodne kolekcje "Replikuj i utrwalają swoje elementy", aby upewnić się, że są one trwałe przez awarie maszyn i przestoje.
Zarówno do replikowania, jak i utrwalania elementów, niezawodne kolekcje muszą być serializowane.

Niezawodne kolekcje "pobierają odpowiedni serializator dla danego typu z Menedżera niezawodnego stanu.
Niezawodny Menedżer stanu zawiera wbudowane serializatory i umożliwia zarejestrowanie niestandardowych serializatorów dla danego typu.

## <a name="built-in-serializers"></a>Wbudowane serializatory

Niezawodny Menedżer stanu zawiera wbudowany serializator dla niektórych typów wspólnych, dzięki czemu mogą one być efektywnie serializowane domyślnie. W przypadku innych typów niezawodny Menedżer stanu powraca do korzystania z [DataContractSerializer](/dotnet/api/system.runtime.serialization.datacontractserializer?view=netcore-3.1).
Wbudowane serializatory są bardziej wydajne, ponieważ wiedzą, że ich typy nie mogą ulec zmianie i nie muszą zawierać informacji o typie, takim jak nazwa jego typu.

Niezawodny Menedżer stanu ma wbudowany serializator dla następujących typów: 
- Guid (identyfikator GUID)
- bool
- byte
- sbyte
- Byte []
- char
- ciąg
- decimal
- double
- float
- int
- uint
- długi
- ulong
- short
- ushort

## <a name="custom-serialization"></a>Serializacja niestandardowa

Niestandardowe serializatory są często używane do zwiększania wydajności lub szyfrowania danych za pośrednictwem sieci i na dysku. Z tego względu niestandardowe serializatory są zwykle bardziej wydajne niż serializator ogólny, ponieważ nie muszą serializować informacji o typie. 

[IReliableStateManager. TryAddStateSerializer \<T> ](/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) służy do rejestrowania niestandardowego serializatora dla danego typu T. Ta rejestracja powinna wystąpić w przygotowaniu StatefulServiceBase, aby upewnić się, że przed rozpoczęciem odzyskiwania wszystkie niezawodne kolekcje mają dostęp do odpowiedniego serializatora w celu odczytania danych utrwalonych.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Serializatory niestandardowe mają pierwszeństwo przed wbudowanymi serializatorami. Na przykład po zarejestrowaniu niestandardowego serializatora dla elementu int jest on używany do serializacji liczb całkowitych zamiast wbudowanego serializatora dla int.

### <a name="how-to-implement-a-custom-serializer"></a>Jak zaimplementować serializator niestandardowy

Niestandardowy serializator musi implementować interfejs [IStateSerializer \<T> ](/dotnet/api/microsoft.servicefabric.data.istateserializer-1) .

> [!NOTE]
> IStateSerializer \<T> zawiera Przeciążenie do zapisu i odczytu, który przyjmuje dodatkową wartość T o nazwie bazowej. Ten interfejs API jest przeznaczony do serializacji różnicowej. Obecnie funkcja serializacji różnicowej nie jest udostępniona. W związku z tym te dwa przeciążenia nie są wywoływane, dopóki Serializacja różnicowa nie zostanie udostępniona i włączona.

Poniżej znajduje się przykładowy typ niestandardowy o nazwie OrderKey, który zawiera cztery właściwości

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Poniżej znajduje się Przykładowa implementacja IStateSerializer \<OrderKey> .
Należy pamiętać, że przeciążenia odczytu i zapisu, które są wykonywane w baseValue, wywołują odpowiednie przeciążenia w celu zapewnienia zgodności.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Możliwość przeklasyfikowania
Podczas [stopniowego uaktualniania aplikacji](service-fabric-application-upgrade.md)uaktualnienie jest stosowane do podzbioru węzłów, jednej domeny uaktualnienia w danym momencie. W trakcie tego procesu niektóre domeny uaktualnienia będą znajdować się w nowszej wersji aplikacji, a niektóre domeny uaktualnienia będą znajdować się w starszej wersji aplikacji. Podczas wdrażania Nowa wersja aplikacji musi mieć możliwość odczytywania starej wersji danych, a stara wersja aplikacji musi mieć możliwość odczytywania nowej wersji danych. Jeśli format danych nie jest zgodny z poprzednimi wersjami, uaktualnienie może się nie powieść lub gorszenie może spowodować utratę lub uszkodzenie danych.

Jeśli używasz wbudowanego serializatora, nie musisz martwić się o zgodność.
Jeśli jednak używasz niestandardowego serializatora lub DataContractSerializer, dane muszą być w nieskończony wstecz i przekazywać zgodne.
Innymi słowy, każda wersja serializatora musi mieć możliwość serializacji i deserializacji dowolnej wersji typu.

Użytkownicy kontraktów danych powinni przestrzegać dobrze zdefiniowanych zasad przechowywania wersji, aby dodawać, usuwać i zmieniać pola. Umowa o dane ma również obsługę w przypadku nieznanych pól, podłączania do procesu serializacji i deserializacji oraz w przypadku dziedziczenia klas. Aby uzyskać więcej informacji, zobacz [Korzystanie z kontraktu danych](/dotnet/framework/wcf/feature-details/using-data-contracts).

Użytkownicy korzystający z serializatorów niestandardowych powinni przestrzegać wytycznych dotyczących serializatora, które są używane w celu upewnienia się, że są zgodne i przenoszone dalej.
Typowym sposobem obsługi wszystkich wersji jest dodanie informacji o rozmiarze na początku i dodanie tylko właściwości opcjonalnych.
W ten sposób każda wersja może odczytywać tyle, ile może i przeskoczyć do pozostałej części strumienia.

## <a name="next-steps"></a>Następne kroki
  * [Serializacja i uaktualnienie](service-fabric-application-upgrade-data-serialization.md)
  * [Dokumentacja dla deweloperów dla niezawodnych kolekcji](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)
  * [Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu Visual Studio.
  * [Uaktualnianie aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu PowerShell.
  * Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).
  * Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [zaawansowanych tematów](service-fabric-application-upgrade-advanced.md).
  * Rozwiązywanie typowych problemów dotyczących uaktualnień aplikacji, w odniesieniu do kroków w [temacie Troubleshooting Upgrades Applications](service-fabric-application-upgrade-troubleshooting.md).
