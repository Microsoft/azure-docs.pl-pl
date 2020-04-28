---
title: PRZESTARZAŁE Pule agentów DC/OS dla Azure Container Service
description: Jak publiczne i prywatne pule agentów współpracują z Azure Container Service klastrem DC/OS
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bb9b33bf537ebd5a563f8e8a8afd45cd2e5b292d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278382"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>PRZESTARZAŁE Pule agentów DC/OS dla Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Klastry DC/OS w Azure Container Service zawierają węzły agentów w dwóch pulach, pulę publiczną i pulę prywatną. Aplikację można wdrożyć w jednej puli, co wpływa na dostępność między maszynami w usłudze kontenera. Komputery można uwidocznić w Internecie (Public) lub utrzymywać wewnętrznie (prywatne). Ten artykuł zawiera krótkie omówienie Dlaczego istnieją pule publiczne i prywatne.


* **Agenci prywatni**: prywatne węzły agenta działają za pomocą sieci bez obsługi routingu. Ta sieć jest dostępna tylko ze strefy administratora lub przez router brzegowy strefy publicznej. Domyślnie system DC/OS uruchamia aplikacje w prywatnych węzłach agenta. 

* **Agenci publiczni**: publiczne węzły agenta uruchamiają aplikacje i usługi platformy DC/OS za pomocą publicznie dostępnej sieci. 

Więcej informacji o zabezpieczeniach sieci DC/OS znajduje się w [dokumentacji platformy DC/OS](https://docs.mesosphere.com/).

## <a name="deploy-agent-pools"></a>Wdróż pule agentów

Pule agentów DC/OS w Azure Container Service są tworzone w następujący sposób:

* **Pula prywatna** zawiera liczbę węzłów agentów, które można określić podczas [wdrażania klastra DC/OS](container-service-deployment.md). 

* **Pula publiczna** początkowo zawiera wstępnie określoną liczbę węzłów agentów. Ta pula jest dodawana automatycznie po zainicjowaniu obsługi administracyjnej klastra DC/OS.

Pula prywatna i Pula publiczna są zestawami skalowania maszyn wirtualnych platformy Azure. Po wdrożeniu można zmienić rozmiar tych pul.

## <a name="use-agent-pools"></a>Korzystanie z pul agentów
Domyślnie **Marathon** wdraża każdą nową aplikację w węzłach agenta *prywatnego* . Musisz jawnie wdrożyć aplikację w węzłach *publicznych* podczas tworzenia aplikacji. Wybierz **opcjonalną** kartę i wprowadź **slave_public** dla wartości **zaakceptowanych ról zasobów** . Ten proces jest udokumentowany w [tym miejscu](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) oraz w dokumentacji [DC/OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) .

## <a name="next-steps"></a>Następne kroki
* Przeczytaj więcej [na temat zarządzania kontenerami DC/OS](container-service-mesos-marathon-ui.md).

* Dowiedz się [, jak otworzyć Zaporę](container-service-enable-public-access.md) dostarczoną przez platformę Azure, aby umożliwić publiczny dostęp do kontenerów DC/OS.

