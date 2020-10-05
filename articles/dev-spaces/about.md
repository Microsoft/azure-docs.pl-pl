---
title: Co to jest usługa Azure Dev Spaces?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Dowiedz się, jak Azure Dev Spaces zapewnia szybkie, iteracyjne środowisko programistyczne Kubernetes dla zespołów w klastrach usługi Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, polecenia kubectl, k8s
manager: gwallace
ms.openlocfilehash: ad884d5e054f23269b238b791e250c732d70a9b8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88210637"
---
# <a name="what-is-azure-dev-spaces"></a>Co to jest usługa Azure Dev Spaces?

Azure Dev Spaces zapewnia szybkie, iteracyjne środowisko programistyczne Kubernetes dla zespołów w klastrach usługi Azure Kubernetes Service (AKS). Azure Dev Spaces umożliwia również debugowanie i testowanie wszystkich składników aplikacji w AKS z minimalną konfiguracją maszyny deweloperskiej bez replikowania lub tworzenia zależności.

![Diagram przedstawia dwie wersje aplikacji opracowane niezależnie. Następnie są one połączone w jeden w środowisku deweloperskim Azure Dev Spaces.](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>W jaki sposób usługa Azure Dev Spaces upraszcza opracowywanie kontenerów Kubernetes

Azure Dev Spaces ułatwia zespołom skoncentrowanie się na opracowywaniu i szybkiej iteracji aplikacji mikrousług przez umożliwienie zespołom pracy bezpośrednio z całą architekturą mikrousług lub aplikacją działającą w AKS. Azure Dev Spaces również umożliwia niezależne aktualizowanie części architektury mikrousług w izolacji bez wpływu na resztę klastra AKS lub innych deweloperów. Azure Dev Spaces jest przeznaczony do tworzenia i testowania w środowiskach deweloperskich i testowych niższego poziomu i nie jest przeznaczony do uruchamiania w produkcyjnych klastrach AKS.

Ponieważ zespoły mogą współdziałać z całą aplikacją i współpracować bezpośrednio w AKS, Azure Dev Spaces:

* Minimalizuje konfigurację komputera lokalnego
* Skraca czas instalacji dla nowych deweloperów w zespole
* Zwiększa prędkość zespołu przez szybszą iterację
* Zmniejsza liczbę nadmiarowych środowisk programistycznych i integracji, ponieważ członkowie zespołu mogą współużytkować klaster
* Eliminuje konieczność replikowania lub makietowania zależności
* Usprawnia współpracę między zespołami programistycznymi, a także z zespołami, z którymi pracują, takich jak zespoły DevOps

Azure Dev Spaces udostępnia narzędzia do generowania zasobów platformy Docker i Kubernetes dla projektów. To narzędzie umożliwia łatwe dodawanie nowych i istniejących aplikacji do przestrzeni deweloperskiej i innych klastrów AKS.

Aby uzyskać więcej informacji na temat działania Azure Dev Spaces, zobacz [How to Azure dev Spaces działa i jest skonfigurowana][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Obsługiwane regiony i konfiguracje

Azure Dev Spaces jest obsługiwana tylko przez klastry AKS w [niektórych regionach][supported-regions]. Azure Dev Spaces obsługuje używanie [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub [Visual Studio Code](https://code.visualstudio.com/download) z [rozszerzeniem Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) zainstalowanym w systemie Linux, macOS lub Windows 8 lub nowszym w celu kompilowania i uruchamiania aplikacji na AKS. Obsługuje ona również korzystanie z [programu Visual Studio 2019](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) w systemie Windows przy użyciu obciążeń programistycznych platformy Azure.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak działa Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Jak działa usługa Azure Dev Spaces](how-dev-spaces-works.md)

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
