---
title: Wdrażanie OpenShift kontenera platformy 4. x na platformie Azure
description: Wdróż OpenShift kontenerów platformy 4. x na platformie Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81759496"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Wdrażanie OpenShift kontenera platformy 4. x na platformie Azure

Wdrożenie platformy kontenerów OpenShift (OCP) 4,2 jest teraz obsługiwane na platformie Azure za pośrednictwem modelu infrastruktury obsługi administracyjnej (IPI) Instalatora.  Strona docelowa dla próby OpenShift 4 ma [try.OpenShift.com](https://try.openshift.com/). Aby zainstalować OCP 4,2 na platformie Azure, odwiedź stronę [Menedżera klastra Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Do uzyskania dostępu do tej witryny są wymagane poświadczenia Red Hat.


## <a name="notes"></a>Uwagi 

 - Do zainstalowania i uruchomienia programu OCP 4. x na platformie Azure jest wymagana nazwa główna usługi (SP). Azure Active Directory
     - SP musi mieć przyznane uprawnienie API **Application. ReadWrite. OwnedBy** for Azure Active Directory Graph
     - Aby to uprawnienie API zaczęło obowiązywać, Administrator dzierżawy usługi AAD musi udzielić zgody administratora.
     - SP musi mieć przyznane **współautor** i role **administratora dostępu użytkowników** do subskrypcji
 - Model instalacji dla oprogramowania OCP 4. x jest inny niż 3. x i nie ma Azure Resource Manager szablonów dostępnych do wdrożenia OCP 4. x na platformie Azure
 - Jeśli podczas instalacji wystąpią problemy, skontaktuj się z odpowiednią firmą (Microsoft lub Red Hat)

| Opis problemu | Punkt kontaktowy |
|-------------------|---------------|
| Problemy związane z platformą Azure (AAD, SP, subskrypcja platformy Azure itp.)                              | Microsoft |
| Problemy związane z OpenShift (błędy/błędy instalacji, subskrypcja Red Hat itp.) |  Red Hat  |




## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do platformy kontenerów OpenShift](https://docs.openshift.com)
