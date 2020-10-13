---
title: Debugowanie kontenerów systemu Windows przy użyciu usługi Service Fabric i programu VS
description: Dowiedz się, jak debugować kontenery systemu Windows na platformie Azure Service Fabric przy użyciu programu Visual Studio 2019.
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 3e6e7785278b182cebb21115a70f35ade52303c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247255"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Instrukcje: debugowanie kontenerów systemu Windows na platformie Azure Service Fabric przy użyciu programu Visual Studio 2019

Program Visual Studio 2019 umożliwia debugowanie aplikacji .NET w kontenerach jako usług Service Fabric. W tym artykule opisano sposób konfigurowania środowiska, a następnie debugowania aplikacji .NET w kontenerze działającym w lokalnym klastrze Service Fabric.

## <a name="prerequisites"></a>Wymagania wstępne

* W systemie Windows 10 Skorzystaj z tego przewodnika Szybki Start, aby [skonfigurować system Windows 10 do uruchamiania kontenerów systemu Windows](/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* W systemie Windows Server 2016 postępuj zgodnie z tym przewodnikiem Szybki Start, aby [skonfigurować system windows 2016 do uruchamiania kontenerów systemu Windows](/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Skonfiguruj lokalne środowisko Service Fabric, wykonując następujące czynności [przygotowując środowisko programistyczne w systemie Windows](./service-fabric-get-started.md)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Konfigurowanie środowiska deweloperskiego do debugowania kontenerów

1. Przed przejściem do następnego kroku upewnij się, że usługa Docker dla systemu Windows jest uruchomiona.

1. Aby zapewnić obsługę rozpoznawania nazw DNS między kontenerami, należy skonfigurować lokalny klaster programistyczny przy użyciu nazwy maszyny. Te kroki są również niezbędne, jeśli chcesz adresować usługi za pomocą zwrotnego serwera proxy.
   1. Otwórz program PowerShell jako administrator
   2. Przejdź do folderu konfiguracji klastra zestawu SDK, zazwyczaj `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup` .
   3. Uruchamianie skryptu `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > `-CreateOneNodeCluster`Aby skonfigurować klaster z jednym węzłem, można użyć programu. Domyślnie zostanie utworzony lokalny klaster z pięcioma węzłami.
      >

      Aby dowiedzieć się więcej na temat usługi DNS w Service Fabric, zobacz [Usługa DNS na platformie Azure Service Fabric](./service-fabric-dnsservice.md). Aby dowiedzieć się więcej o używaniu Service Fabric zwrotnego serwera proxy z usług uruchomionych w kontenerze, zobacz [odwracanie specjalnej obsługi serwera proxy dla usług uruchomionych w](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers)kontenerach.

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Znane ograniczenia dotyczące debugowania kontenerów w Service Fabric

Poniżej znajduje się lista znanych ograniczeń dotyczących kontenerów debugowania w Service Fabric i możliwych rozwiązaniach:

* Korzystanie z hosta lokalnego dla ClusterFQDNorIP nie obsługuje rozpoznawania nazw DNS w kontenerach.
    * Rozwiązanie: skonfiguruj klaster lokalny przy użyciu nazwy komputera (patrz powyżej)
* Uruchomienie Windows10 na maszynie wirtualnej nie spowoduje pobrania odpowiedzi DNS z powrotem do kontenera.
    * Rozwiązanie: Wyłącz Odciążanie sumy kontrolnej UDP dla protokołu IPv4 na karcie sieciowej Virtual Machines
    * Uruchomienie programu Windows10 spowoduje spadek wydajności sieci na komputerze.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Rozpoznawanie usług w tej samej aplikacji przy użyciu nazwy usługi DNS nie działa na Windows10, jeśli aplikacja została wdrożona przy użyciu Docker Compose
    * Rozwiązanie: Użyj ServiceName. ApplicationName, aby rozwiązać punkty końcowe usługi
    * https://github.com/Azure/service-fabric-issues/issues/1062
* W przypadku używania adresu IP dla ClusterFQDNorIP zmiana podstawowego adresu IP na hoście spowoduje przerwanie funkcjonalności usługi DNS.
    * Rozwiązanie: Utwórz ponownie klaster przy użyciu nowego podstawowego adresu IP na hoście lub użyj nazwy maszyny. To uszkodzenie jest zaprojektowane.
* Jeśli nazwa FQDN, przy użyciu której klaster został utworzony, nie jest rozpoznawana w sieci, usługa DNS zakończy się niepowodzeniem.
    * Rozwiązanie: Utwórz ponownie klaster lokalny przy użyciu podstawowego adresu IP hosta. Ten błąd jest zaprojektowany.
* Podczas debugowania kontenera dzienniki platformy Docker będą dostępne tylko w oknie danych wyjściowych programu Visual Studio, a nie za pomocą interfejsów API Service Fabric, w tym Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Debugowanie aplikacji .NET uruchomionej w kontenerach platformy Docker na Service Fabric

1. Uruchom program Visual Studio jako administrator.

1. Otwórz istniejącą aplikację platformy .NET lub Utwórz nową.

1. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Add-> Container Orchestrator support-> Service Fabric**

1. Naciśnij klawisz **F5** , aby rozpocząć debugowanie aplikacji.

    Program Visual Studio obsługuje typy projektów Console i ASP.NET dla platform .NET i .NET Core.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o możliwościach Service Fabric i kontenerów, zobacz [Omówienie kontenerów Service Fabric](service-fabric-containers-overview.md).
