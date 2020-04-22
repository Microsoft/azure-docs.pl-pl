---
title: Wdrażanie platformy kontenerowej OpenShift 4.x na platformie Azure
description: Wdrażanie platformy kontenerowej OpenShift 4.x na platformie Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759496"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Wdrażanie platformy kontenerowej OpenShift 4.x na platformie Azure

Wdrażanie platformy kontenerowej OpenShift (OCP) 4.2 jest teraz obsługiwane na platformie Azure za pośrednictwem modelu infrastruktury aprowizacji instalatora (IPI).  Strona docelowa do wypróbowania OpenShift 4 jest [try.openshift.com](https://try.openshift.com/). Aby zainstalować OCP 4.2 na platformie Azure, odwiedź stronę [Menedżera klastrów Red Hat OpenShift.](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)  Poświadczenia Red Hat są wymagane, aby uzyskać dostęp do tej witryny.


## <a name="notes"></a>Uwagi 

 - Podmiot zabezpieczeń usługi Azure Active Directory (AAD) (SP) jest wymagany do zainstalowania i uruchomienia protokołu OCP 4.x na platformie Azure
     - Dodatek SP musi mieć uprawnienie interfejsu API **aplikacji.ReadWrite.OwnedBy** dla usługi Azure Active Directory Graphy
     - Administrator dzierżawy usługi AAD musi udzielić zgody administratora, aby to uprawnienie interfejsu API stało się skuteczne
     - Sp musi mieć przyznane **współtwórca** i **administratora dostępu** użytkownika role do subskrypcji
 - Model instalacji dla OCP 4.x różni się od 3.x i nie ma dostępnych szablonów usługi Azure Resource Manager do wdrażania OCP 4.x na platformie Azure
 - Jeśli podczas instalacji wystąpią problemy, skontaktuj się z odpowiednią firmą (Microsoft lub Red Hat)

| Opis problemu | Punkt kontaktowy |
|-------------------|---------------|
| Specyficzne problemy związane z platformą Azure (usługi AAD, SP, subskrypcja platformy Azure itp.)                              | Microsoft |
| Problemy specyficzne dla OpenShift (błędy instalacji / błędy, subskrypcja Red Hat itp.) |  Red Hat  |




## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do platformy kontenerowej OpenShift](https://docs.openshift.com)
