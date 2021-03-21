---
title: Omówienie cyklu życia aktora usługi Azure Service Fabric
description: Wyjaśniono Service Fabric niezawodnego cyklu życia aktora, wyrzucania elementów bezużytecznych i ręcznego usuwania uczestników i ich stanu
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 88db4bb2376cbc418d6954e274a18a6c18a280d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576047"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Cykl życia aktora, automatyczne odzyskiwanie pamięci i usuwanie ręczne
Aktor jest uaktywniany podczas pierwszego wywołania każdej z jego metod. Aktor jest dezaktywowany (elementy bezużyteczne zbierane przez środowisko uruchomieniowe aktorów), jeśli nie jest używany przez konfigurowalny okres czasu. Aktor i jego stan można również usunąć ręcznie w dowolnym momencie.

## <a name="actor-activation"></a>Aktywacja aktora
Po aktywowaniu aktora następuje:

* Gdy wywołanie pochodzi z aktora, a jeden z nich nie jest już aktywny, tworzony jest nowy aktor.
* Stan aktora jest ładowany, jeśli jest on konserwowany.
* `OnActivateAsync`Wywoływana jest metoda (C#) lub `onActivateAsync` (Java) (która może zostać przesłonięta w implementacji aktora).
* Aktor jest teraz uznawany za aktywny.

## <a name="actor-deactivation"></a>Dezaktywacja aktora
Gdy aktor zostanie zdezaktywowany, następuje:

* Gdy aktor nie jest używany przez jakiś czas, jest usuwany z tabeli aktywnych aktorów.
* `OnDeactivateAsync`Wywoływana jest metoda (C#) lub `onDeactivateAsync` (Java) (która może zostać przesłonięta w implementacji aktora). Czyści wszystkie czasomierze aktora. Operacji aktora, takich jak zmiany stanu, nie należy wywoływać z tej metody.

> [!TIP]
> Środowisko uruchomieniowe aktorów sieci szkieletowych emituje niektóre [zdarzenia związane z aktywacją aktora i dezaktywacją](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Są one przydatne w przypadku diagnostyki i monitorowania wydajności.
>
>

### <a name="actor-garbage-collection"></a>Zbieranie elementów bezużytecznych aktora
Gdy aktor zostanie zdezaktywowany, odwołania do obiektu aktora są zwalniane i może być nieelementowo gromadzony w normalny sposób przez moduł JVMer środowiska uruchomieniowego języka wspólnego (CLR) lub maszyny wirtualnej Java. Wyrzucanie elementów bezużytecznych czyści obiekt aktora; **nie usuwa stanu** przechowywanego w Menedżerze stanu aktora. Następnym razem, gdy aktor zostanie uaktywniony, zostanie utworzony nowy obiekt aktora i jego stan zostanie przywrócony.

Co jest traktowane jako "używane" na potrzeby dezaktywacji i wyrzucania elementów bezużytecznych?

* Otrzymywanie wywołania
* `IRemindable.ReceiveReminderAsync` wywoływana metoda (ma zastosowanie tylko wtedy, gdy aktor używa przypomnień)

> [!NOTE]
> Jeśli aktor wykorzystuje czasomierze i wywołanie zwrotne czasomierza jest wywoływane, **nie** jest liczone jako "używane".
>
>

Przed przejściem do szczegółów dezaktywacji należy zdefiniować następujące warunki:

* *Interwał skanowania*. Jest to interwał, w którym uczestnicy środowiska uruchomieniowego skanuje swoją aktywną tabelę aktorów dla aktorów, które można dezaktywować i wyrzucać elementy bezużyteczne. Wartość domyślna tego ustawienia to 1 minutę.
* *Limit czasu bezczynności*. Jest to czas, przez który aktor musi pozostać nieużywany (bezczynny), zanim będzie można go dezaktywować i wyrzucać elementy bezużyteczne. Wartość domyślna to 60 min.

Zazwyczaj nie trzeba zmieniać tych wartości domyślnych. Jednak w razie potrzeby te interwały mogą być zmieniane `ActorServiceSettings` podczas rejestrowania [usługi aktora](service-fabric-reliable-actors-platform.md):

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
Dla każdego aktywnego aktora środowisko uruchomieniowe aktora śledzi ilość czasu, przez który był bezczynny (tj. nie jest używany). Środowisko wykonawcze aktora sprawdza każdy aktory w `ScanIntervalInSeconds` celu sprawdzenia, czy może być odzyskiwane jako elementy bezużyteczne i oznacza, że jest bezczynny dla `IdleTimeoutInSeconds` .

Gdy aktor jest używany, jego czas bezczynności jest resetowany do wartości 0. Po wykonaniu tej czynności aktor może być odzyskiwany tylko wtedy, gdy ponowna wartość jest bezczynna dla `IdleTimeoutInSeconds` . Odwołaj, że aktor jest uznawany za używany, jeśli jest wykonywana metoda interfejsu aktora lub wywołanie zwrotne przypomnienia aktora. Aktor **nie** jest uważany za używany, jeśli jest wykonywane wywołanie zwrotne czasomierza.

Na poniższym diagramie przedstawiono cykl życia pojedynczego aktora ilustrującego te koncepcje.

![Przykład czasu bezczynności][1]

W przykładzie pokazano wpływ wywołań metod aktorów, przypomnień i czasomierzy w okresie istnienia aktora. Następujące punkty dotyczące przykładu są wymieniane:

* ScanInterval i IdleTimeout są odpowiednio ustawione na 5 i 10. (Jednostki nie mają tu znaczenia, ponieważ naszym celem jest tylko zilustrowanie koncepcji).
* Skanowanie dla aktorów, które mają być odzyskiwane, ma miejsce w T = 0, 5, 10, 15, 20, 25, zgodnie z definicją w interwale skanowania równym 5.
* Okresowe czasomierze są generowane w T = 4, 8, 12, 16, 20, 24, a jego wywołanie zwrotne jest wykonywane. Nie ma to wpływu na czas bezczynności aktora.
* Wywołanie metody aktora w T = 7 resetuje czas bezczynności do 0 i opóźnia wyrzucanie elementów bezużytecznych aktora.
* Wywołanie zwrotne przypomnień aktora jest wykonywane o godzinie T = 14 i dalsze opóźnia wyrzucanie elementów bezużytecznych aktora.
* Podczas skanowania wyrzucania elementów bezużytecznych na poziomie T = 25 czas bezczynności aktora poza limitem czasu bezczynności wynoszącym 10 i aktora jest odzyskiwana.

Aktor nigdy nie zostanie pobrany jako bezużyteczny podczas wykonywania jednej z jej metod, niezależnie od tego, ile czasu zajmuje się wykonywaniem tej metody. Jak wspomniano wcześniej, wykonywanie metod interfejsu aktora i wywołania zwrotne przypomnień uniemożliwia wyrzucanie elementów bezużytecznych przez zresetowanie czasu bezczynności aktora do wartości 0. Wykonanie wywołania zwrotnego czasomierza nie powoduje zresetowania czasu bezczynności do wartości 0. Jednak wyrzucanie elementów bezużytecznych aktora jest odroczone do momentu zakończenia wykonywania wywołania zwrotnego czasomierza.

## <a name="manually-deleting-actors-and-their-state"></a>Ręczne usuwanie aktorów i ich Stanów
Wyrzucanie elementów bezużytecznych nieaktywowanych aktorów jedynie czyści obiekt aktora, ale nie usuwa danych przechowywanych w Menedżerze stanu aktora. Po ponownym aktywowaniu aktora jego dane zostaną ponownie udostępnione za pomocą menedżera stanu. W przypadkach, gdy aktory przechowują dane w Menedżerze stanu i są dezaktywowane, ale nigdy nie są ponownie aktywowane, może być konieczne wyczyszczenie danych.  Przykłady usuwania aktorów, odczytu [i ich stanu](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Następne kroki
* [Czasomierze aktora i przypomnienia](service-fabric-reliable-actors-timers-reminders.md)
* [Zdarzenia aktora](service-fabric-reliable-actors-events.md)
* [Współużytkowania wątkowości aktora](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostyka aktora i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentacja interfejsu API aktora](/previous-versions/azure/dn971626(v=azure.100))
* [Przykładowy kod w języku C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowy kod w języku Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
