---
title: Zarządzanie stanem Reliable Actors
description: Opisuje sposób, w jaki stan Reliable Actors jest zarządzany, trwały i replikowany w celu zapewnienia wysokiej dostępności.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: badfc490f26b71881e7970c2c0be3472abfec25a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575605"
---
# <a name="reliable-actors-state-management"></a>Zarządzanie stanem Reliable Actors
Reliable Actors są obiektami jednowątkowymi, które mogą hermetyzować zarówno logikę, jak i stan. Ze względu na to, że aktorzy działają na Reliable Services, mogą niezawodnie zachować stan przy użyciu tych samych mechanizmów trwałości i replikacji. W ten sposób aktory nie utracą swojego stanu po błędach, po ponownym aktywowaniu po wyrzucaniu elementów bezużytecznych lub gdy są one przenoszone między węzłami w klastrze z powodu zrównoważenia zasobów lub uaktualniania.

## <a name="state-persistence-and-replication"></a>Trwałość stanu i replikacja
Wszystkie Reliable Actors są uznawane za *stanowe* , ponieważ każde wystąpienie aktora mapuje na unikatowy identyfikator. Oznacza to, że powtórzone wywołania tego samego identyfikatora aktora są kierowane do tego samego wystąpienia aktora. W systemie bezstanowym, przez odróżnienia, wywołania klienta nie są przekazywane do tego samego serwera za każdym razem. Z tego powodu usługi aktora to zawsze stanowe usługi.

Mimo że aktorzy są uważane za stanowe, nie oznacza to, że muszą przechowywać stan niezawodnie. Aktory mogą wybrać poziom trwałości stanu i replikację w oparciu o ich wymagania dotyczące przechowywania danych:

* **Stan trwały**: stan jest utrwalany na dysku i jest replikowany do co najmniej trzech replik. Stan trwały to najbardziej trwała opcja magazynu Stanów, w której stan może być trwały przez pełną awarię klastra.
* **Stan nietrwały**: stan jest replikowany do co najmniej trzech replik i jest przechowywany tylko w pamięci. Nietrwały stan zapewnia odporność na awarie węzła i niepowodzenie aktora oraz podczas uaktualnień i równoważenia zasobów. Jednak stan nie jest utrwalony na dysku. Tak więc jeśli wszystkie repliki zostaną utracone jednocześnie, stan zostanie utracony.
* **Brak utrwalonego stanu**: stan nie jest replikowany lub nie Zapisano na dysku, tylko dla uczestników, którzy nie muszą prawidłowo zachować stanu.

Każdy poziom trwałości jest po prostu innym *dostawcą stanu* *i* konfiguracją usługi. Wskazuje, czy stan jest zapisywany na dysku, zależy od dostawcy stanu — składnika w niezawodnej usłudze, która przechowuje stan. Replikacja zależy od liczby replik, w których jest wdrażana usługa. Podobnie jak w przypadku Reliable Services, zarówno dostawca stanu, jak i liczba replik można łatwo ustawić ręcznie. Platforma aktora udostępnia atrybut, który, gdy jest używany w aktorze, automatycznie wybiera domyślnego dostawcę stanu i automatycznie generuje ustawienia dla liczby replik w celu osiągnięcia jednego z tych trzech ustawień trwałości. Atrybut StatePersistence nie jest dziedziczony przez klasę pochodną, każdy typ aktora musi zapewniać swój poziom StatePersistence.

### <a name="persisted-state"></a>Stan trwały
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
To ustawienie powoduje użycie dostawcy stanu, który przechowuje dane na dysku i automatycznie ustawia liczbę replik usługi na 3.

### <a name="volatile-state"></a>Nietrwały stan
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
To ustawienie powoduje użycie dostawcy stanu tylko w pamięci i ustawia liczbę replik na 3.

### <a name="no-persisted-state"></a>Brak utrwalonego stanu
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
To ustawienie powoduje użycie dostawcy stanu tylko w pamięci i ustawia liczbę replik na 1.

### <a name="defaults-and-generated-settings"></a>Ustawienia domyślne i wygenerowane
Gdy używasz `StatePersistence` atrybutu, dostawca stanu jest automatycznie wybierany w czasie wykonywania, gdy usługa aktora zostanie uruchomiona. Liczba replik jest jednak ustawiana w czasie kompilacji przez narzędzia do tworzenia aktorów programu Visual Studio. Narzędzia kompilacji automatycznie generują *domyślną usługę* dla usługi aktora w ApplicationManifest.xml. Parametry są tworzone dla **minimalnej wielkości zestawu replik** i **docelowego rozmiaru zestawu replik**.

Te parametry można zmienić ręcznie. Ale za każdym razem `StatePersistence` , gdy atrybut jest zmieniany, parametry są ustawiane na domyślne wartości rozmiaru zestawu replik dla wybranego `StatePersistence` atrybutu, zastępując wszystkie poprzednie wartości. Innymi słowy, wartości ustawione w ServiceManifest.xml są zastępowane w czasie kompilacji *tylko* wtedy, gdy zmienisz `StatePersistence` wartość atrybutu.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Menedżer stanu
Każde wystąpienie aktora ma swój własny Menedżer stanu: strukturę danych przypominającą słownik, która w niezawodny sposób przechowuje pary klucz/wartość. Menedżer stanu jest otoką wokół dostawcy stanu. Można jej użyć do przechowywania danych bez względu na to, które ustawienie trwałości jest używane. Nie zapewnia żadnych gwarancji, że uruchomioną usługę aktora można zmienić z ustawienia stanu nietrwałego (tylko w pamięci) na ustawienie trwałe w ramach uaktualnienia stopniowego podczas zachowywania danych. Istnieje jednak możliwość zmiany liczby replik dla działającej usługi.

Klucze menedżera stanu muszą być ciągami. Wartości są ogólne i mogą być dowolnego typu, w tym typów niestandardowych. Wartości przechowywane w Menedżerze Stanów muszą być serializowane kontraktu danych, ponieważ mogą być przesyłane przez sieć do innych węzłów podczas replikacji i mogą być zapisywane na dysku w zależności od ustawienia trwałości stanu aktora.

Menedżer stanu udostępnia typowe metody słownika do zarządzania stanem, podobnie jak te znajdujące się w niezawodnym słowniku.

Przykłady zarządzania stanem aktora, [dostępu do odczytu, zapisywania i usuwania stanu Reliable Actors](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Najlepsze rozwiązania
Poniżej przedstawiono niektóre sugerowane praktyki i wskazówki dotyczące rozwiązywania problemów związanych z zarządzaniem stanem aktora.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Ustaw stan aktora jako szczegółowy, jak to możliwe
Jest to kluczowe znaczenie dla wydajności i użycia zasobów aplikacji. Za każdym razem, gdy istnieje zapis/aktualizacja do "nazwanego stanu" aktora, cała wartość odpowiadająca typowi "nazwanego stanu" jest serializowana i wysyłana przez sieć do replik pomocniczych.  Serwery pomocnicze zapisują je na dysku lokalnym i odpowiedzą na replikę podstawową. Gdy podstawowy odbiera potwierdzenia z kworum replik pomocniczych, zapisuje stan na dysku lokalnym. Załóżmy na przykład, że wartość jest klasą, która ma 20 elementów członkowskich i rozmiar 1 MB. Nawet w przypadku modyfikacji tylko jednej z elementów członkowskich klasy o rozmiarze 1 KB można w pełni płacić koszt serializacji oraz zapisy w sieci i dysku dla pełnej 1 MB. Podobnie, jeśli wartość jest kolekcją (na przykład listą, tablicą lub Słownikiem), opłata za kompletną kolekcję jest uiszczana nawet w przypadku modyfikacji jednego z jej elementów członkowskich. Interfejs StateManager klasy aktora jest taki sam jak w przypadku słownika. Zawsze należy modelować strukturę danych reprezentującą stan aktora na początku tego słownika.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Prawidłowo Zarządzaj cyklem życia aktora
Należy wyczyścić zasady dotyczące zarządzania rozmiarem stanu w każdej partycji usługi aktora. Usługa aktora powinna mieć stałą liczbę aktorów i ponownie używać ich w miarę możliwości. Jeśli stale tworzysz nowe aktory, musisz je usunąć po zakończeniu pracy. W strukturze aktora są przechowywane pewne metadane dotyczące każdego aktora, który istnieje. Usunięcie całego stanu aktora nie powoduje usunięcia metadanych dotyczących tego aktora. Musisz usunąć aktora (zobacz [usuwanie aktorów i ich Stanów](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)), aby usunąć wszystkie informacje o nim przechowywane w systemie. Jako dodatkowe sprawdzenie należy wykonać zapytanie dotyczące usługi aktora (zobacz [Wyliczenie aktorów](service-fabric-reliable-actors-enumerate.md)) raz w czasie, aby upewnić się, że liczba aktorów mieści się w oczekiwanym zakresie.
 
Jeśli zobaczysz, że rozmiar pliku bazy danych usługi aktora rośnie poza oczekiwanym rozmiarem, upewnij się, że korzystasz z powyższych wytycznych. Jeśli przestrzegasz tych wytycznych i nadal występują problemy z rozmiarem plików bazy danych, należy [otworzyć bilet pomocy technicznej](service-fabric-support.md) z zespołem produktu, aby uzyskać pomoc.

## <a name="next-steps"></a>Następne kroki

Stan, który jest przechowywany w Reliable Actors musi być serializowany przed zapisem na dysku i replikowany w celu zapewnienia wysokiej dostępności. Dowiedz się więcej o [serializacji typu aktora](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Następnie Dowiedz się więcej na temat [diagnostyki aktora i monitorowania wydajności](service-fabric-reliable-actors-diagnostics.md).
