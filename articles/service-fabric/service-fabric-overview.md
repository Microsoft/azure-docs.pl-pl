---
title: Omówienie usługi Azure Service Fabric
description: Service Fabric to platforma systemów rozproszonych umożliwiająca tworzenie skalowalnych, niezawodnych i łatwych do zarządzania mikrousług.
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contentperfq1
ms.openlocfilehash: 3c282178decc1a07b2c0acc102b279688c42d52e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91300653"
---
# <a name="overview-of-azure-service-fabric"></a>Omówienie usługi Azure Service Fabric

Azure Service Fabric to [platforma systemów rozproszonych](#container-orchestration) ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług i kontenerów oraz zarządzanie nimi. Service Fabric również dotyczy znaczących wyzwań związanych z tworzeniem natywnych aplikacji w chmurze [i zarządzaniem nimi](#application-lifecycle-management) .

Kluczowy odróżniający Service Fabric jest jej silnym skoncentrowaniem się na tworzeniu usług stanowych. Można użyć Service Fabric [model programowania](#stateless-and-stateful-microservices) lub uruchomić kontenerowe usługi stanowe, które są zapisywane w dowolnym języku lub kodzie. Możesz tworzyć [klastry Service Fabric w dowolnym miejscu](#any-os-any-cloud), w tym z systemami Windows Server i Linux w lokale i innych chmurach publicznych, poza platformą Azure.

![Platforma Service Fabric zapewnia zarządzanie cyklem życia, dostępność, aranżację, modele programowania, kondycję i monitorowanie, narzędzia deweloperskie i Ops oraz skalowanie automatyczne — na platformie Azure, lokalnie i w innych chmurach oraz na komputerze deweloperskim][Image1]

Usługa Service Fabric jest obecnie podstawą działania wielu usług firmy Microsoft, takich jak Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, Skype dla firm i wiele kluczowych usług platformy Azure.

## <a name="container-orchestration"></a>Aranżacja kontenerów

Service Fabric jest [koordynatorem kontenerów](service-fabric-cluster-resource-manager-introduction.md) firmy Microsoft na potrzeby wdrażania mikrousług i zarządzania nimi w klastrze maszyn, z korzyścią z korzystania z usług firmy Microsoft na ogromną skalę. Service Fabric może wdrażać aplikacje w ciągu kilku sekund, z dużą gęstością w setkach lub tysiącach aplikacji lub kontenerów na maszynę. Za pomocą Service Fabric można mieszać obie usługi w ramach procesów i usług w kontenerach w tej samej aplikacji.

[Dowiedz się więcej na temat Service Fabric](service-fabric-content-roadmap.md) podstawowych pojęć, modeli programowania, cyklu życia aplikacji, testowania, klastrów i monitorowania kondycji.

## <a name="stateless-and-stateful-microservices"></a>Mikrousługi bezstanowe i stanowe

Service Fabric zapewnia zaawansowane, lekkie środowisko uruchomieniowe, które obsługuje mikrousługi bezstanowe i stanowe. Kluczowym rozróżnieniem Service Fabric jest jego niezawodna pomoc techniczna w zakresie tworzenia usług stanowych, z Service Fabric [wbudowanymi modelami programowania](service-fabric-choose-framework.md) lub kontenerami usługi stanowe.

Dowiedz się więcej na temat [scenariuszy aplikacji](service-fabric-application-scenarios.md) , które korzystają z Service Fabricych usług stanowych.

## <a name="application-lifecycle-management"></a>Zarządzanie cyklem życia aplikacji

Service Fabric zapewnia obsługę pełnego cyklu życia aplikacji oraz ciągłej integracji i ciągłego wdrażania aplikacji w chmurze, w tym kontenerów: opracowywanie, codzienne monitorowanie, zarządzanie i konserwacja, a także likwidowanie. Usługa Service Fabric jest zintegrowana z narzędziami do obsługi cyklu ciągłej integracja/ciągłego wdrażania, takimi jak [Azure Pipelines](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) czy [Octopus Deploy](https://octopus.com/), i współdziała też z innymi popularnymi narzędziami CI/CD.

Aby uzyskać więcej informacji o zarządzaniu cyklem życia aplikacji, zobacz [Cykl życia aplikacji](service-fabric-application-lifecycle.md). Aby wdrażać istniejące aplikacje do Service Fabric, zobacz [wdrażanie pliku wykonywalnego gościa](service-fabric-deploy-existing-app.md).

## <a name="any-os-any-cloud"></a>Dowolny system operacyjny, dowolna chmura

Możesz tworzyć klastry dla Service Fabric w wielu środowiskach, w tym na [platformie Azure lub lokalnie](service-fabric-deploy-anywhere.md), w [systemie Windows Server lub Linux](service-fabric-linux-windows-differences.md). Można nawet tworzyć klastry w innych chmurach publicznych. Środowisko programistyczne w zestawie SDK Service Fabric jest takie samo jak w środowisku produkcyjnym, bez emulatorów. Innymi słowy, które są uruchamiane w lokalnym klastrze programistycznym, są wdrażane w klastrach w innych środowiskach.

W przypadku [projektowania systemu Windows](service-fabric-get-started.md)zestaw SDK Service Fabric .NET jest zintegrowany z programem Visual Studio i PowerShell. W przypadku [opracowywania systemu Linux](service-fabric-get-started-linux.md)zestaw Service Fabric Java SDK jest zintegrowany z przezaćmieniem, a narzędzia Yeoman jest używany do generowania szablonów dla aplikacji Java, .NET Core i kontenerów.

## <a name="compliance"></a>Zgodność

Dostawca zasobów usługi Azure Service Fabric jest dostępny we wszystkich regionach platformy Azure i jest zgodny ze wszystkimi certyfikatami zgodności z platformą Azure, w tym: SOC, ISO, PCI DSS, HIPAA i Rodo. Aby uzyskać pełną listę, zobacz [oferty zgodności firmy Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Następne kroki

Utwórz i Wdróż pierwszą aplikację na platformie Azure Service Fabric:

> [!div class="nextstepaction"]
> [Service Fabric — Szybki Start][sf-quickstart]

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
[sf-quickstart]: ./service-fabric-quickstart-dotnet.md
