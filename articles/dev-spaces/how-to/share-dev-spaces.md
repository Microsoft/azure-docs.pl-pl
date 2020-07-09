---
title: Jak udostępnić Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Dowiedz się, jak za pomocą Azure Dev Spaces udostępniać miejsce deweloperskie w usłudze Azure Kubernetes Service innym członkom zespołu
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79474411"
---
# <a name="share-azure-dev-spaces"></a>Udostępnianie obszarów Azure Dev Spaces

Za pomocą Azure Dev Spaces możesz udostępnić swoje miejsce deweloperskie innym członkom zespołu. Każdy deweloper może pracować w swoim własnym miejscu bez obaw o rozrywanie innych. Ponadto współpraca w jednym miejscu może umożliwić przetestowanie kompleksu kodu bez konieczności tworzenia makiet lub symulowania zależności. Aby uzyskać więcej informacji, zobacz Przewodnik po [programowaniu zespołu](../team-development-nodejs.md) .

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Skonfiguruj miejsce dla deweloperów dla wielu deweloperów

1. Utwórz miejsce deweloperskie na platformie Azure. Wybierz pozycję [.NET Core i vs Code](../get-started-netcore.md), [.NET Core i Visual Studio](../get-started-netcore-visualstudio.md)albo [Node.js i vs Code](../get-started-nodejs.md). Musisz mieć dostęp właściciela lub współautora do wybranej subskrypcji platformy Azure.
1. Upewnij się, że każdy członek zespołu ma [odpowiednie uprawnienia dostępu do kontrolera Azure dev Spaces](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis). Można na przykład skonfigurować **grupę zasobów** obszaru dev platformy Azure, aby [przyznać dostęp współautora](/azure/active-directory/role-based-access-control-configure) do każdego członka zespołu. Aby sprawdzić grupę zasobów obszaru deweloperskiego, należy uruchomić następujące polecenie:`azds show-context`
1. Poproszenie członków zespołu o **wybranie przestrzeni deweloperskiej** w celu jej opracowania.
   * **Wiersz polecenia lub vs Code**: aby zobaczyć istniejące Azure dev Spaces masz dostęp do: `azds space list` . Aby wybrać miejsce deweloperskie: `azds space select` .
   * **Środowisko IDE programu Visual Studio**: Otwórz projekt w programie Visual Studio, wybierz **Azure dev Spaces** z listy rozwijanej uruchamianie ustawień. W otwartym oknie dialogowym wybierz istniejący klaster.

     ![Lista rozwijana ustawień uruchamiania programu Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz informacje [o projektowaniu zespołu](../team-development-nodejs.md) .
