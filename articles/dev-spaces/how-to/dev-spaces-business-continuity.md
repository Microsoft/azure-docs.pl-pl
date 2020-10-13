---
title: Ciągłość działania i odzyskiwanie po awarii
services: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Dowiedz się, jak korzystać z usług Azure Dev Spaces i Azure Kubernetes Services w celu zapewnienia ciągłości działania i przygotowania do odzyskiwania po awarii
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 824eb1e65e7da5736080e47509aa163a868d8ec9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973142"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Ciągłość działania i odzyskiwanie po awarii w Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Przejrzyj wskazówki dotyczące odzyskiwania po awarii dla usługi Azure Kubernetes Service (AKS)

Azure Dev Spaces jest funkcją usługi Azure Kubernetes Service (AKS). Należy zapoznać się z wytycznymi dotyczącymi odzyskiwania po awarii w programie AKS i zastanowić się, czy mają one zastosowanie do klastrów AKS używanych na potrzeby funkcji Spaces dev. Aby uzyskać więcej informacji, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi ciągłości działania i odzyskiwania po awarii w usłudze Azure Kubernetes Service (AKS)](../../aks/operator-best-practices-multi-region.md)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Włącz miejsca deweloperskie w klastrach AKS w różnych regionach

Włączenie funkcji miejsca deweloperskie w klastrach AKS w różnych regionach pozwala na wznowienie pracy przy użyciu spacji deweloperskich bezpośrednio po awarii regionu platformy Azure.

Aby uzyskać ogólne informacje na temat wdrożeń wieloregionowych AKS, zobacz [Planowanie wdrożenia wieloregionowego](../../aks/operator-best-practices-multi-region.md#plan-for-multiregion-deployment)

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Włączanie funkcji Spaces dev za pośrednictwem interfejsu wiersza polecenia platformy Azure

Możesz również włączyć funkcję miejsca deweloperskie w wierszu polecenia:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Wdróż linię bazową zespołu w każdym klastrze

Podczas pracy z miejscami programistycznymi zwykle wdrażana jest cała aplikacja w nadrzędnym obszarze deweloperskim w klastrze Kubernetes. Domyślnie `default` jest używane miejsce. Początkowe wdrożenie obejmuje wszystkie usługi, a także zasoby zewnętrzne, od których zależą te usługi, takie jak bazy danych lub kolejki. Ta wartość jest określana jako *linia bazowa*. Po skonfigurowaniu linii bazowej w nadrzędnym obszarze deweloperskim można przechodzić i debugować poszczególne usługi w podrzędnych miejscach deweloperskich.

Najnowsze wersje zestawu bazowych usług należy wdrożyć do klastrów w wielu regionach. Aktualizowanie usług bazowych w ten sposób zapewnia, że w przypadku awarii regionu platformy Azure można nadal korzystać z funkcji miejsca do tworzenia. Na przykład, jeśli plan bazowy zostanie wdrożony za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania, należy zmodyfikować potok, tak aby został wdrożony w wielu klastrach w różnych regionach.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Wybierz poprawny klaster AKS, który ma być używany przez funkcję Spaces dev

Po poprawnym skonfigurowaniu klastra kopii zapasowej z uruchomioną linią bazową zespołu można szybko przełączać się do klastra kopii zapasowej w dowolnym momencie. Następnie można ponownie uruchomić poszczególne usługi, nad którymi pracujesz w podrzędnych miejscach deweloperskich.

Wybierz inny klaster z następującym poleceniem interfejsu wiersza polecenia:

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Można wyświetlić listę dostępnych miejsc dev w nowym klastrze przy użyciu następującego polecenia:

```cmd
azds space list
```

Można utworzyć nowe miejsce deweloperskie do pracy lub wybrać istniejące miejsce deweloperskie za pomocą następującego polecenia:

```cmd
azds space select -n <space name>
```

Po uruchomieniu tych poleceń wybrany klaster i przestrzeń dev będą używane do kolejnych operacji interfejsu wiersza polecenia oraz do debugowania projektów przy użyciu rozszerzenia Visual Studio Code Azure Dev Spaces.

Jeśli używasz programu Visual Studio, możesz przełączyć klaster używany przez istniejący projekt, wykonując następujące czynności:

1. Otwórz projekt w programie Visual Studio.
1. Kliknij prawym przyciskiem myszy nazwę projektu w Eksplorator rozwiązań a następnie kliknij pozycję **Właściwości** .
1. W okienku po lewej stronie kliknij pozycję **Debuguj** .
1. Na stronie właściwości debugowania kliknij listę rozwijaną **profil** i wybierz pozycję **Azure dev Spaces**.
1. Kliknij przycisk **Zmień** .
1. W wyświetlonym oknie dialogowym Wybierz klaster AKS, którego chcesz użyć. W razie potrzeby wybierz inną przestrzeń programistyczną, w której będziesz korzystać, lub Utwórz nowe miejsce dev, wybierając odpowiednią opcję z listy rozwijanej **miejsce** .

Po wybraniu odpowiedniego klastra i miejsca możesz nacisnąć klawisz F5, aby uruchomić usługę w obszarze dev Spaces.

Powtórz te kroki dla wszystkich innych projektów skonfigurowanych do używania oryginalnego klastra.

## <a name="access-a-service-on-a-backup-cluster"></a>Dostęp do usługi w klastrze kopii zapasowej

Jeśli usługa została skonfigurowana pod kątem używania publicznej nazwy DNS, usługa będzie mieć inny adres URL, jeśli zostanie uruchomiony w klastrze kopii zapasowej. Publiczne nazwy DNS są zawsze w formacie `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io` . Jeśli przełączysz się do innego klastra, identyfikator GUID klastra i prawdopodobnie region zmienią się.

Miejsca dev Spaces zawsze wyświetlają prawidłowy adres URL usługi podczas uruchamiania `azds up` lub w oknie danych wyjściowych w programie Visual Studio w obszarze **Azure dev Spaces**.

Możesz również znaleźć adres URL, uruchamiając `azds list-uris` polecenie:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Użyj tego adresu URL podczas uzyskiwania dostępu do usługi.
