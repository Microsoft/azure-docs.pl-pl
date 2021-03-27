---
title: Debugowanie aplikacji Service Fabric platformy Azure w systemie Windows
description: Dowiedz się, jak monitorować i diagnozować swoje usługi przy użyciu Microsoft Azure Service Fabric na lokalnym komputerze deweloperskim.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: a2502c24ef233c286872e2c265dcfdae6883f8ed
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628836"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitor and diagnose services in a local machine development setup (Monitorowanie i diagnozowanie usług w konfiguracji środowiska deweloperskiego na maszynie lokalnej)
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Monitorowanie, wykrywanie, diagnozowanie i rozwiązywanie problemów, dzięki czemu usługi mogą kontynuować minimalne zakłócenia środowiska użytkownika. Chociaż monitorowanie i Diagnostyka mają kluczowe znaczenie w rzeczywistym wdrożonym środowisku produkcyjnym, wydajność będzie zależeć od zastosowania podobnego modelu podczas tworzenia usług, aby zapewnić, że będą one działały po przejściu do rzeczywistego Instalatora. Service Fabric ułatwia deweloperom usług implementację diagnostyki, która może bezproblemowo współpracować zarówno z lokalnymi konfiguracjami programistycznymi na jednym komputerze, jak i rzeczywistymi konfiguracjami klastrów produkcyjnych.

## <a name="event-tracing-for-windows"></a>Śledzenie zdarzeń systemu Windows
[Śledzenie zdarzeń systemu Windows](/windows/win32/etw/event-tracing-portal) (ETW) jest zalecaną technologią śledzenia komunikatów w Service Fabric. Niektóre zalety korzystania z funkcji ETW to:

* **Funkcja ETW jest szybka.** Została skompilowana jako technologia śledzenia, która ma minimalny wpływ na czasy wykonywania kodu.
* **Śledzenie ETW działa bezproblemowo w lokalnych środowiskach programistycznych, a także w rzeczywistych konfiguracjach klastra.** Oznacza to, że nie trzeba ponownie pisać kodu śledzenia, gdy wszystko jest gotowe do wdrożenia kodu w rzeczywistym klastrze.
* **Kod systemu Service Fabric używa również funkcji ETW do śledzenia wewnętrznego.** Pozwala to na wyświetlanie śladów aplikacji z przeplotem Service Fabric śledzenia systemu. Pomaga również łatwiej zrozumieć sekwencje i relacje między kodem aplikacji a zdarzeniami w systemie bazowym.
* **W programie Service Fabric Visual Studio Tools jest wbudowana obsługa wyświetlania zdarzeń ETW.** Zdarzenia ETW są wyświetlane w widoku zdarzeń diagnostycznych programu Visual Studio, gdy program Visual Studio jest poprawnie skonfigurowany przy użyciu Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Wyświetlanie zdarzeń systemu Service Fabric w programie Visual Studio
Service Fabric emituje zdarzenia ETW, aby pomóc deweloperom aplikacji zrozumieć, co się dzieje na platformie. Jeśli jeszcze tego nie zrobiono, wykonaj kroki opisane w temacie [Tworzenie pierwszej aplikacji w programie Visual Studio](service-fabric-tutorial-create-dotnet-app.md). Te informacje pomogą Ci w rozpoczęciu pracy aplikacji z podglądem zdarzeń diagnostycznych wyświetlającym komunikaty śledzenia.

1. Jeśli okno zdarzenia diagnostyki nie zostanie automatycznie wyświetlone, przejdź do karty **Widok** w programie Visual Studio, wybierz **inne okna** , a następnie **Podgląd zdarzeń diagnostycznych**.
2. Każde zdarzenie ma standardowe informacje metadanych, które informują węzeł, aplikację i usługę, z której pochodzi zdarzenie. Możesz również filtrować listę zdarzeń przy użyciu pola **Filtruj zdarzenia** w górnej części okna zdarzenia. Na przykład można filtrować według **nazwy węzła** lub **nazwy usługi.** Gdy przeglądasz szczegóły zdarzeń, możesz również wstrzymywać, korzystając z przycisku **Wstrzymaj** w górnej części okna zdarzenia i wznawiać później bez utraty zdarzeń.
   
   ![Podgląd zdarzeń diagnostycznych programu Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Dodawanie własnych niestandardowych śladów do kodu aplikacji
Szablony projektów programu Service Fabric Visual Studio zawierają przykładowy kod. Kod przedstawia sposób dodawania niestandardowych śladów ETW kodu aplikacji, które są wyświetlane w podglądzie ETW programu Visual Studio, obok śladów systemu z Service Fabric. Zaletą tej metody jest to, że metadane są automatycznie dodawane do śladów, a Podgląd zdarzeń diagnostycznych programu Visual Studio jest już skonfigurowany do ich wyświetlania.

W przypadku projektów utworzonych na podstawie **szablonów usług** (bezstanowe lub stanowe) można wyszukać `RunAsync` implementację:

1. Wywołanie `ServiceEventSource.Current.ServiceMessage` `RunAsync` metody w metodzie pokazuje przykład NIESTANDARDOWEGO śledzenia ETW z kodu aplikacji.
2. W pliku **serviceeventsource. cs** znajdziesz Przeciążenie `ServiceEventSource.ServiceMessage` metody, która powinna być używana dla zdarzeń o wysokiej częstotliwości ze względu na wydajność.

W przypadku projektów utworzonych na podstawie **szablonów aktora** (bezstanowe lub stanowe):

1. Otwórz plik **"ProjectName". cs "** , gdzie *ProjectName* jest nazwą wybraną dla projektu programu Visual Studio.  
2. Znajdź kod `ActorEventSource.Current.ActorMessage(this, "Doing Work");` w metodzie *DoWorkAsync* .  Jest to przykład niestandardowego śledzenia ETW zarejestrowanego z kodu aplikacji.  
3. W pliku **ActorEventSource. cs** znajdziesz Przeciążenie `ActorEventSource.ActorMessage` metody, która powinna być używana dla zdarzeń o wysokiej częstotliwości ze względu na wydajność.

Po dodaniu niestandardowego śledzenia funkcji ETW do kodu usługi możesz ponownie skompilować, wdrożyć i uruchomić aplikację, aby zobaczyć swoje zdarzenia w Podglądzie zdarzeń diagnostycznych. Jeśli debugujesz aplikację przy użyciu klawisza **F5**, Podgląd zdarzeń diagnostycznych zostanie otwarty automatycznie.

## <a name="next-steps"></a>Następne kroki
Ten sam kod śledzenia, który został dodany do aplikacji powyżej dla lokalnej diagnostyki, będzie działać z narzędziami, których można użyć do wyświetlania tych zdarzeń podczas uruchamiania aplikacji w klastrze platformy Azure. Zapoznaj się z tymi artykułami, które omawiają różne opcje narzędzi i opisują, jak można je skonfigurować.

* [Jak zbierać dzienniki przy użyciu Diagnostyka Azure](./service-fabric-diagnostics-event-aggregation-wad.md)
* [Agregacja i zbieranie zdarzeń przy użyciu użyciu struktury eventflow](service-fabric-diagnostics-event-aggregation-eventflow.md)
