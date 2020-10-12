---
title: Przykłady manifestu aplikacji Service Fabric platformy Azure
description: Dowiedz się, jak skonfigurować ustawienia manifestu aplikacji i usługi dla aplikacji Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6f44d2ddaac11545a3a3dc52fd20e02263c6ad67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75451646"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Service Fabric przykłady manifestów aplikacji i usług
Ta sekcja zawiera przykłady manifestów aplikacji i usług. Te przykłady nie są przeznaczone do wyświetlania ważnych scenariuszy, ale do wyświetlania różnych dostępnych ustawień i sposobu ich używania. 

Poniżej znajduje się indeks widocznych funkcji i przykładowe manifesty, których są częścią.

|Cecha|Manifest|
|---|---|
|[Nadzór nad zasobami](service-fabric-resource-governance.md)|[Manifest aplikacji Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Uruchamianie usługi jako konta administratora lokalnego](service-fabric-application-runas-security.md)|[Reliable Services manifest aplikacji](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Zastosuj zasady domyślne do wszystkich pakietów kodu usługi](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Reliable Services manifest aplikacji](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Tworzenie podmiotów nazw użytkowników i grup](service-fabric-application-runas-security.md)|[Reliable Services manifest aplikacji](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|Udostępnianie pakietu danych między wystąpieniami usługi|[Reliable Services manifest aplikacji](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Przesłoń punkty końcowe usługi](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Reliable Services manifest aplikacji](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Uruchamianie skryptu podczas uruchamiania usługi](service-fabric-run-script-at-service-startup.md)|[Manifest usługi VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definiowanie punktu końcowego HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[Manifest usługi VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Zadeklaruj pakiet konfiguracji](service-fabric-application-and-service-manifests.md)|[Manifest usługi VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Deklarowanie pakietu danych](service-fabric-application-and-service-manifests.md)|[Manifest usługi VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Przesłoń zmienne środowiskowe](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurowanie mapowania portów między hostami](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurowanie uwierzytelniania rejestru kontenerów](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ustaw tryb izolacji](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Określ obrazy kontenerów właściwych dla kompilacji systemu operacyjnego](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifest aplikacji kontenera](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ustawianie zmiennych środowiskowych](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest usługi FrontEndService kontenera](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [manifest usługi kontenera BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Konfigurowanie punktu końcowego](service-fabric-get-started-containers.md#configure-communication)|Manifest usługi [FrontEndService kontenera](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [manifest usługi kontenera BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [manifest usługi VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|przekazywanie poleceń do kontenera|[Manifest usługi FrontEndService kontenera](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Importowanie certyfikatu do kontenera](service-fabric-securing-containers.md)|[Manifest usługi FrontEndService kontenera](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Konfigurowanie sterownika woluminu](service-fabric-containers-volume-logging-drivers.md)|[Manifest usługi BackEndService kontenera](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

