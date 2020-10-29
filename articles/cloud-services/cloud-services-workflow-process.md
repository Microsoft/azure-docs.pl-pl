---
title: Przepływ pracy architektury VM systemu Windows Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie procesów przepływu pracy podczas wdrażania usługi.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: d29c98ecbbb6c9da18e6356a0e38122e253a34b6
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026466"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Przepływ pracy architektury klasycznej maszyny wirtualnej platformy Microsoft Azure 
Ten artykuł zawiera omówienie procesów przepływu pracy, które są wykonywane podczas wdrażania lub aktualizowania zasobu platformy Azure, takiego jak maszyna wirtualna. 

> [!NOTE]
>Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: Resource Manager i model klasyczny. Ten artykuł dotyczy klasycznego modelu wdrożenia.

Na poniższym diagramie przedstawiono architekturę zasobów platformy Azure.

:::image type="content" source="./media/cloud-services-workflow-process/workflow.jpg" alt-text="<Alt obrazu o przepływie pracy Azure>":::

## <a name="workflow-basics"></a>Podstawowe informacje o przepływie pracy
   
**A** . FRONTON/FFE to ścieżka komunikacji od użytkownika do sieci szkieletowej. Frontonu reddog (fronton RedDog) to publicznie uwidoczniony interfejs API, który stanowi fronton do portal zarządzania i interfejs API zarządzania usługami takich jak Visual Studio, Azure MMC i tak dalej.  Wszystkie żądania od użytkownika są kierowane do frontonu reddog. FFE (fronton sieci szkieletowej) to warstwa, która tłumaczy żądania z frontonu reddog na polecenia sieci szkieletowej. Wszystkie żądania z frontonu reddog przechodzą przez FFE, aby dotrzeć do kontrolerów sieci szkieletowej.

**B** . Kontroler sieci szkieletowej jest odpowiedzialny za utrzymywanie i monitorowanie wszystkich zasobów w centrum danych. Komunikuje się z agentami hosta sieci szkieletowej w systemie operacyjnym sieci szkieletowej wysyłającym informacje, takie jak wersja systemu operacyjnego gościa, pakiet usługi, konfiguracja usługi i stan usługi.

**C** . Agent hosta znajduje się w systemie operacyjnym hosta i jest odpowiedzialny za skonfigurowanie systemu operacyjnego gościa i komunikowanie się z agentem gościa (WindowsAzureGuestAgent) w celu zaktualizowania roli w celu zamierzonego stanu celu i sprawdzenia pulsu z agentem gościa. Jeśli Agent hosta nie odbiera odpowiedzi pulsu przez 10 minut, Agent hosta uruchamia ponownie system operacyjny gościa.

**C2** . WaAppAgent jest odpowiedzialny za Instalowanie, Konfigurowanie i aktualizowanie WindowsAzureGuestAgent.exe.

**D** .  WindowsAzureGuestAgent jest odpowiedzialny za następujące:

1. Konfigurowanie systemu operacyjnego gościa, w tym zapory, list ACL, zasobów LocalStorage, pakietu usługi i konfiguracji oraz certyfikatów.
2. Skonfigurowanie identyfikatora SID dla konta użytkownika, w ramach którego zostanie uruchomiona rola.
3. Komunikacja stanu roli z siecią szkieletową.
4. Uruchamianie WaHostBootstrapper i monitorowanie go w celu upewnienia się, że rola jest w stanie celu.

**E** . WaHostBootstrapper jest odpowiedzialny za:

1. Odczytywanie konfiguracji roli i uruchamianie wszystkich odpowiednich zadań i procesów w celu skonfigurowania i uruchomienia roli.
2. Monitorowanie wszystkich procesów podrzędnych.
3. Wywoływanie zdarzenia StatusCheck w procesie hosta roli.

**F** . IISConfigurator jest uruchamiany, Jeśli rola została skonfigurowana jako pełna rola sieci Web usług IIS. Jest on odpowiedzialny za:

1. Uruchamianie standardowych usług IIS
2. Konfigurowanie modułu ponownego zapisywania w konfiguracji sieci Web
3. Konfigurowanie puli aplikacji dla skonfigurowanej roli w modelu usług
4. Konfigurowanie rejestrowania usług IIS w celu wskazywania folderu DiagnosticStore LocalStorage
5. Konfigurowanie uprawnień i list ACL
6. Witryna sieci Web znajduje się w folderze% roleroot%: \sitesroot\0, a puli aplikacji wskazuje tę lokalizację, aby uruchomić usługi IIS. 

**G** . Zadania uruchamiania są definiowane przez model roli i uruchamiane przez WaHostBootstrapper. Zadania uruchamiania można skonfigurować tak, aby działały w tle asynchronicznie, a program inicjujący hosta uruchomi zadanie uruchamiania, a następnie przejdzie do innych zadań uruchamiania. Zadania uruchamiania można także skonfigurować tak, aby działały w prostym (domyślnym) trybie, w którym program inicjujący hosta poczeka na zakończenie działania zadania uruchamiania i zwróci powodzenie (0) kod zakończenia przed przejściem do następnego zadania uruchamiania.

**H** . Te zadania są częścią zestawu SDK i są zdefiniowane jako wtyczki w definicji usługi roli (. csdef). Po rozwinięciu do zadań uruchamiania, **DiagnosticsAgent** i **RemoteAccessAgent** są unikatowe w tym, że każdy z nich definiuje dwa zadania uruchamiania, jeden zwykły i jeden, który ma parametr **/blockStartup** . Normalne zadanie uruchamiania jest zdefiniowane jako zadanie uruchamiania w tle, dzięki czemu można je uruchomić w tle, podczas gdy sama rola jest uruchomiona. Zadanie uruchamiania **/blockStartup** jest zdefiniowane jako proste zadanie uruchamiania, dzięki czemu WaHostBootstrapper czeka na zakończenie przed kontynuowaniem. Zadanie **/blockStartup** oczekuje na zakończenie inicjowania przez zwykłe zadanie, a następnie opuszcza i umożliwi programowi inicjującemu hosta kontynuowanie. W tym celu można skonfigurować dostęp do diagnostyki i dostępu do protokołu RDP przed rozpoczęciem procesów ról (odbywa się to za pomocą zadania/blockStartup). Pozwala to również na kontynuowanie działania programów diagnostycznych i RDP po zakończeniu wykonywania zadań uruchamiania przez program inicjujący hosta (jest to wykonywane przez normalne zadanie).

**I** . WaWorkerHost to standardowy proces hosta dla normalnych ról procesów roboczych. Ten proces hosta obsługuje wszystkie biblioteki DLL i kod punktu wejścia roli, takie jak OnStart i Run.

**J** . WaIISHost to proces hosta dla kodu punktu wejścia roli dla ról sieci Web, które używają pełnych usług IIS. Ten proces ładuje pierwszą odnalezioną bibliotekę DLL, która używa klasy **RoleEntryPoint** i wykonuje kod z tej klasy (OnStart, Run, OnStop). Wszystkie zdarzenia **RoleEnvironment** (takie jak StatusCheck i zmieniony), które są tworzone w klasie RoleEntryPoint, są zgłaszane w tym procesie.

**K** . W3WP jest standardowym procesem roboczym usług IIS, który jest używany, Jeśli rola jest skonfigurowana do korzystania z pełnych usług IIS. Spowoduje to uruchomienie puli aplikacji, który jest skonfigurowany z IISConfigurator. Wszystkie zdarzenia RoleEnvironment (takie jak StatusCheck i zmieniony) utworzone w tym miejscu są zgłaszane w tym procesie. Należy pamiętać, że zdarzenia RoleEnvironment będą uruchamiane w obu lokalizacjach (WaIISHost i w3wp.exe), jeśli zasubskrybujesz zdarzenia w obu procesach.

## <a name="workflow-processes"></a>Procesy przepływu pracy

1. Użytkownik wysyła żądanie, takie jak przekazywanie plików ". cspkg" i ". cscfg", informujących o zatrzymywaniu lub wprowadzeniu zmiany konfiguracji itd. Można to zrobić za pomocą Azure Portal lub narzędzia korzystającego z interfejs API zarządzania usługami, takiego jak funkcja publikacji programu Visual Studio. To żądanie przechodzi przez fronton reddog do wszystkich zadań związanych z subskrypcją, a następnie przekazuje żądanie do FFE. Pozostałe kroki tych kroków przepływu pracy to wdrożenie nowego pakietu i jego uruchomienie.
2. FFE znajduje poprawną pulę maszyn (na podstawie danych wejściowych klienta, takich jak grupa koligacji lub lokalizacja geograficzna oraz dane wejściowe z sieci szkieletowej, takie jak dostępność maszyny) i komunikują się z kontrolerem głównej sieci szkieletowej w tej puli maszyn.
3. Kontroler sieci szkieletowej odnajduje hosta, który ma dostępne rdzenie procesora CPU (lub uruchamia nowy host). Pakiet i konfiguracja usługi są kopiowane do hosta, a kontroler sieci szkieletowej komunikuje się z agentem hosta w systemie operacyjnym hosta, aby wdrożyć pakiet (skonfigurować DIP, porty, system operacyjny gościa itd.).
4. Agent hosta uruchamia system operacyjny gościa i komunikuje się z agentem gościa (WindowsAzureGuestAgent). Host wysyła pulsy do gościa, aby upewnić się, że rola działa do stanu docelowego.
5. WindowsAzureGuestAgent konfiguruje system operacyjny gościa (Zapora, listy ACL, LocalStorage itd.), kopiuje nowy plik konfiguracyjny XML do c:\Config, a następnie uruchamia proces WaHostBootstrapper.
6. W przypadku pełnych ról sieci Web usług IIS WaHostBootstrapper uruchamia IISConfigurator i informuje go o usunięciu wszelkich istniejących pul aplikacji dla roli sieci Web z usług IIS.
7. WaHostBootstrapper odczytuje zadania **uruchamiania** z E:\RoleModel.xml i rozpoczyna wykonywanie zadań uruchamiania. WaHostBootstrapper czeka, aż wszystkie proste zadania uruchamiania zakończą działanie i zwróci komunikat "powodzenie".
8. W przypadku pełnych ról sieci Web usług IIS WaHostBootstrapper informuje IISConfigurator o konieczności skonfigurowania puli aplikacji usług IIS i wskazuje, że lokacja `E:\Sitesroot\<index>` `<index>` jest indeksem 0 na podstawie liczby `<Sites>` elementów zdefiniowanych dla usługi.
9. WaHostBootstrapper rozpocznie proces hosta w zależności od typu roli:
    1. **Rola procesu roboczego** : WaWorkerHost.exe jest uruchomiona. WaHostBootstrapper wykonuje metodę OnStart (). Po powrocie funkcja WaHostBootstrapper rozpoczyna wykonywanie metody Run (), a następnie jednocześnie oznacza rolę jako gotową i umieszcza ją w rotacji modułu równoważenia obciążenia (jeśli InputEndpoints są zdefiniowane). WaHostBootsrapper następnie przechodzi do pętli sprawdzania stanu roli.
    2. **Pełna rola sieci Web usług IIS** : aIISHost jest uruchomiona. WaHostBootstrapper wykonuje metodę OnStart (). Po powrocie rozpocznie się wykonywanie metody Run (), a następnie jednocześnie oznacza rolę jako gotową i umieszcza ją w rotacji modułu równoważenia obciążenia. WaHostBootsrapper następnie przechodzi do pętli sprawdzania stanu roli.
10. Przychodzące żądania sieci Web do pełnej roli sieci Web usług IIS wyzwalają usługi IIS w celu uruchomienia procesu W3WP i obsłużynia żądania, tak samo jak w lokalnym środowisku usług IIS.

## <a name="log-file-locations"></a>Lokalizacje plików dziennika

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Ten dziennik zawiera zmiany w usłudze, takie jak uruchamianie, zatrzymywanie i nowe konfiguracje. Jeśli usługa nie zostanie zmieniona, można oczekiwać, że w tym pliku dziennika występują duże przerwy czasu.
- C:\Logs\WaAppAgent.Log.  
Ten dziennik zawiera aktualizacje stanu i powiadomienia o pulsie i jest aktualizowany co 2-3 sekund.  Ten dziennik zawiera historyczny widok stanu wystąpienia i informuje, kiedy wystąpienie nie było w stanie gotowe.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**Dzienniki usług IIS**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Dzienniki zdarzeń systemu Windows**

`D:\Windows\System32\Winevt\Logs`
