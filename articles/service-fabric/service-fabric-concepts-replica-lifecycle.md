---
title: Repliki i wystąpienia w usłudze Azure Service Fabric
description: Informacje o replikach i wystąpieniach w Service Fabric, w tym omówienie ich cyklów życia i funkcji.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84710749"
---
# <a name="replicas-and-instances"></a>Repliki i wystąpienia 
Ten artykuł zawiera omówienie cyklu życia replik usług stanowych i wystąpień usług bezstanowych.

## <a name="instances-of-stateless-services"></a>Wystąpienia usług bezstanowych
Wystąpienie usługi bezstanowej to kopia logiki usługi, która jest uruchamiana na jednym z węzłów klastra. Wystąpienie w ramach partycji jest jednoznacznie identyfikowane przez jego **Identyfikator InstanceId**. Cykl życia wystąpienia jest modelowany na poniższym diagramie:

![Cykl życia wystąpienia](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>Kompilacja (IB)
Gdy klaster Menedżer zasobów określi położenie wystąpienia, przechodzi do tego stanu cyklu życia. Wystąpienie jest uruchamiane w węźle. Host aplikacji został uruchomiony, wystąpienie zostanie utworzone, a następnie otwarte. Po zakończeniu uruchamiania wystąpienie przechodzi do stanu gotowości. 

Jeśli host lub węzeł aplikacji dla tego wystąpienia ulegnie awarii, przechodzi do stanu porzucenia.

### <a name="ready-rd"></a>Gotowe (RD)
W stanie gotowe wystąpienie jest uruchomione w węźle. Jeśli to wystąpienie jest niezawodne, **RunAsync** został wywołany. 

Jeśli host lub węzeł aplikacji dla tego wystąpienia ulegnie awarii, przechodzi do stanu porzucenia.

### <a name="closing-cl"></a>Zamykanie (CL)
W stanie zamykania usługa Azure Service Fabric jest w trakcie zamykania wystąpienia w tym węźle. To zamknięcie może wynikać z wielu powodów — na przykład uaktualnienie aplikacji, równoważenie obciążenia lub usunięcie usługi. Po zakończeniu zamykania przechodzi do stanu porzucenia.

### <a name="dropped-dd"></a>Porzucone (DD)
W stanie opuszczone wystąpienie nie jest już uruchomione w węźle. W tym momencie Service Fabric utrzymuje metadane dotyczące tego wystąpienia, które ostatecznie również zostały usunięte.

> [!NOTE]
> Istnieje możliwość przejścia z dowolnego stanu do stanu porzuconego przy użyciu opcji **ForceRemove** w systemie `Remove-ServiceFabricReplica` .
>

## <a name="replicas-of-stateful-services"></a>Repliki usług stanowych
Replika usługi stanowej to kopia logiki usługi działającej na jednym z węzłów klastra. Ponadto replika zachowuje kopię stanu tej usługi. Dwa powiązane koncepcje opisują cykl życia i zachowanie replik stanowych:
- Cykl życia repliki
- Rola repliki

W poniższej dyskusji opisano trwałe usługi stanowe. W przypadku nietrwałych (lub w pamięci) usług stanowych, Stany Down i rzucony są równoważne.

![Cykl życia repliki](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>Kompilacja (IB)
Replika inbuild to replika utworzona lub przygotowana do przyłączenia do zestawu replik. W zależności od roli repliki IB ma inną semantykę. 

Jeśli host aplikacji lub węzeł dla repliki inbuild ulegnie awarii, przechodzi do stanu Down.

   - **Repliki podstawowej kompilacji**: podstawowa kompilacja jest pierwszą repliką partycji. Ta replika zwykle występuje, gdy tworzona jest partycja. Repliki podstawowej kompilacji powstają również wtedy, gdy wszystkie repliki partycji zostaną ponownie uruchomione lub usunięte.

   - **Repliki IdleSecondary inbuild**: są to nowe repliki, które są tworzone przez klaster Menedżer zasobów, lub istniejące repliki, które pozostały i muszą zostać dodane z powrotem do zestawu. Te repliki są umieszczane lub tworzone przez podstawowy przed dołączeniem ich do zestawu replik jako ActiveSecondary i uczestniczenia w potwierdzeniu kworum operacji.

   - **Repliki ActiveSecondary inbuild**: ten stan jest zaobserwowany w niektórych zapytaniach. Jest to Optymalizacja, w której zestaw replik nie ulega zmianie, ale należy utworzyć replikę. Replika jest zgodna z normalnymi przejściami stanu komputera (zgodnie z opisem w sekcji role repliki).

### <a name="ready-rd"></a>Gotowe (RD)
Replika jest przygotowana do repliki, która uczestniczy w replikacji oraz potwierdzania kworum operacji. Stan gotowości dotyczy podstawowych i aktywnych replik pomocniczych.

Jeśli host aplikacji lub węzeł dla gotowej repliki ulegnie awarii, przechodzi do stanu Down.

### <a name="closing-cl"></a>Zamykanie (CL)
Replika przechodzi w stan zamknięcia w następujących scenariuszach:

- **Zamykanie kodu repliki**: Service Fabric może zajść konieczność zamknięcia uruchomionego kodu dla repliki. To zamknięcie może się nie powieść z wielu powodów. Na przykład może się to zdarzyć ze względu na uaktualnienie aplikacji, sieci szkieletowej lub infrastruktury lub z powodu błędu zgłoszonego przez replikę. Po zakończeniu zamknięcia repliki następuje przejście do stanu Down. Utrwalony stan skojarzony z tą repliką przechowywaną na dysku nie został wyczyszczony.

- **Usuwanie repliki z klastra**: Service Fabric może być konieczne usunięcie stanu trwałego i zamknięcie uruchomionego kodu dla repliki. To zamknięcie może mieć wiele przyczyn, na przykład Równoważenie obciążenia.

### <a name="dropped-dd"></a>Porzucone (DD)
W stanie opuszczone wystąpienie nie jest już uruchomione w węźle. W węźle nie ma również żadnego stanu. W tym momencie Service Fabric utrzymuje metadane dotyczące tego wystąpienia, które ostatecznie również zostały usunięte.

### <a name="down-d"></a>W dół (D)
W stanie down kod repliki nie jest uruchomiony, ale trwały stan dla tej repliki istnieje w tym węźle. Replika może być nieprzerwana z wielu powodów — na przykład, w którym węźle jest wyłączony, awaria w kodzie repliki, uaktualnienie aplikacji lub błędy replik.

Replika w dół jest otwierana przez Service Fabric zgodnie z potrzebami, na przykład po zakończeniu uaktualniania w węźle.

Rola repliki nie jest odpowiednia w stanie down.

### <a name="opening-op"></a>Otwieranie (OP)
Replika w dół przechodzi do stanu otwarcia, gdy Service Fabric musi ponownie wykonać kopię zapasową repliki. Na przykład ten stan może być po zakończeniu uaktualniania kodu dla aplikacji na węźle. 

Jeśli host aplikacji lub węzeł dla repliki otwierającej ulegnie awarii, przechodzi do stanu Down.

Rola repliki nie jest istotna w stanie otwarcia.

### <a name="standby-sb"></a>Wstrzymanie (SB)
Replika rezerwa to replika utrwalonej usługi, która uległa i została otwarta. Ta replika może być używana przez Service Fabric, jeśli musi dodać kolejną replikę do zestawu replik (ponieważ replika ma już część stanu i proces kompilacji jest szybszy). Po wygaśnięciu StandByReplicaKeepDuration replika wstrzymania zostanie odrzucona.

Jeśli host aplikacji lub węzeł dla repliki zapasowej ulegnie awarii, przechodzi do stanu Down.

Rola repliki nie jest odpowiednia w stanie wstrzymania.

> [!NOTE]
> Każda replika, która nie została wyłączona ani porzucona, jest traktowana jako *nieaktualna*.
>

> [!NOTE]
> Istnieje możliwość przejścia z dowolnego stanu do stanu porzuconego przy użyciu opcji **ForceRemove** w systemie `Remove-ServiceFabricReplica` .
>

## <a name="replica-role"></a>Rola repliki 
Rola repliki określa jej funkcję w zestawie replik:

- **Podstawowe (P)**: w zestawie replik istnieje jeden podstawowy, który jest odpowiedzialny za wykonywanie operacji odczytu i zapisu. 
- **ActiveSecondary**: są to repliki, które odbierają aktualizacje stanu z podstawowego, stosują je, a następnie wysyłają potwierdzenia zwrotne. Zestaw replik zawiera wiele aktywnych elementów pomocniczych. Liczba aktywnych elementów pomocniczych określa liczbę błędów, które może obsłużyć usługa.
- **IdleSecondary (I)**: te repliki są kompilowane przez podstawowy. Są one wysyłane z poziomu podstawowego, zanim będą mogły zostać podwyższone do aktywnej pomocniczej. 
- **Brak (N)**: te repliki nie mają odpowiedzialności w zestawie replik.
- **Nieznane (U)**: jest to początkowa Rola repliki przed odebraniem dowolnego wywołania interfejsu API **ChangeRole** z Service Fabric.

Na poniższym diagramie przedstawiono przejście roli repliki oraz kilka przykładowych scenariuszy, w których mogą one wystąpić:

![Rola repliki](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U-> P: Tworzenie nowej repliki podstawowej.
- U-> I: Tworzenie nowej repliki bezczynnej.
- U-> N: usuwanie repliki w stanie wstrzymania.
- I-> S: podwyższanie poziomu bezczynności do aktywnej pomocniczej, tak aby jej potwierdzenia miały wpływ na kworum.
- I-> P: Promocja bezczynnej wartości dodatkowej do wartości podstawowej. Może się to zdarzyć w przypadku specjalnych ponownych konfiguracji, gdy bezczynna wartość pomocnicza jest poprawnym kandydatem do podstawowego.
- I-> N: usuwanie repliki pomocniczej bezczynnej.
- S-> P: Promocja aktywnego elementu pomocniczego do podstawowego. Może to być spowodowane przełączeniem w tryb failover podstawowego lub podstawowego przepływu zainicjowanego przez klaster Menedżer zasobów. Na przykład może to być w odpowiedzi na uaktualnienie aplikacji lub Równoważenie obciążenia.
- S-> N: usuwanie aktywnej repliki pomocniczej.
- P-> S: obniżanie repliki podstawowej. Może to być spowodowane przemieszczeniem podstawowym zainicjowanym przez klaster Menedżer zasobów. Na przykład może to być w odpowiedzi na uaktualnienie aplikacji lub Równoważenie obciążenia.
- P-> N: usuwanie repliki podstawowej.

> [!NOTE]
> Modele programowania wyższego poziomu, takie jak [Reliable Actors](service-fabric-reliable-actors-introduction.md) i [Reliable Services](service-fabric-reliable-services-introduction.md), ukrywają koncepcję ról repliki od dewelopera. W aktorach pojęcie roli jest niepotrzebne. W usługach usługa jest w dużym stopniu uproszczona w przypadku większości scenariuszy.
>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pojęć Service Fabric, zobacz następujący artykuł:

[Cykl życia interfejsu Reliable Services — C#](service-fabric-reliable-services-lifecycle.md)

