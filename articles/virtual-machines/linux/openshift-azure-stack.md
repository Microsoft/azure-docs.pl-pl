---
title: Wdrażanie usługi OpenShift w usłudze Azure Stack
description: Wdrażanie funkcji OpenShift w usłudze Azure Stack.
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 51abfd1cbb438d0987554040867625f7fb71630b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758234"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Wdrażanie platformy kontenerowej OpenShift lub narzędzia OKD w usłudze Azure Stack

OpenShift można wdrożyć w usłudze Azure Stack. Istnieją pewne kluczowe różnice między platformą Azure i usługą Azure Stack, więc wdrożenie będzie się nieznacznie różnić, a możliwości będą się nieznacznie różnić.

Obecnie dostawca chmury azure nie działa w usłudze Azure Stack. Z tego powodu nie będzie można użyć dołączania dysku do magazynu trwałego w usłudze Azure Stack. Zamiast tego można skonfigurować inne opcje pamięci masowej, takie jak NFS, iSCSI, GlusterFS itp. Alternatywnie można włączyć układ OUN i używać GlusterFS do trwałego przechowywania. Jeśli cns jest włączony, trzy dodatkowe węzły zostaną wdrożone z dodatkowym magazynem dla użycia GlusterFS.

Można użyć jednej z kilku metod do wdrożenia platformy kontenerowej OpenShift lub OKD w usłudze Azure Stack:

- Można ręcznie wdrożyć niezbędne składniki infrastruktury platformy Azure, a następnie postępować zgodnie z [dokumentacją platformy kontenerów OpenShift](https://docs.openshift.com/container-platform) lub [dokumentacją OKD.](https://docs.okd.io)
- Można również użyć istniejącego [szablonu Menedżera zasobów,](https://github.com/Microsoft/openshift-container-platform/) który upraszcza wdrażanie klastra platformy kontenerów OpenShift.
- Można również użyć istniejącego [szablonu Menedżera zasobów,](https://github.com/Microsoft/openshift-origin) który upraszcza wdrażanie klastra OKD.

Jeśli używasz szablonu Menedżera zasobów, wybierz właściwą gałąź (azurestack-release-3.x). Szablony platformy Azure nie będą działać, ponieważ wersje interfejsu API różnią się między platformą Azure i usługą Azure Stack. Odwołanie do obrazu RHEL jest obecnie zakodowane na stałe jako zmienna w pliku azuredeploy.json i będzie musiał zostać zmieniony, aby dopasować obraz.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Dla wszystkich opcji wymagana jest subskrypcja Red Hat. Podczas wdrażania red hat enterprise linux wystąpienie jest zarejestrowany w subskrypcji Red Hat i dołączony do identyfikatora puli, który zawiera uprawnienia do platformy kontenera OpenShift.
Upewnij się, że masz prawidłową nazwę użytkownika, hasło i identyfikator puli Menedżera subskrypcji Red Hat (RHSM). Alternatywnie można użyć klucza aktywacyjnego, identyfikatora organizacji i identyfikatora puli.  Te informacje można zweryfikować, https://access.redhat.comlogując się do pliku .

## <a name="azure-stack-prerequisites"></a>Wymagania wstępne usługi Azure Stack

Obraz RHEL (OpenShift Container Platform) lub Obraz CentOS (OKD) musi zostać dodany do środowiska usługi Azure Stack, aby wdrożyć klaster OpenShift. Skontaktuj się z administratorem usługi Azure Stack, aby dodać te obrazy. Instrukcje można znaleźć tutaj:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Wdrażanie przy użyciu szablonu OpenShift Container Platform lub OKD Resource Manager

Aby wdrożyć za pomocą szablonu Menedżera zasobów, należy użyć pliku parametrów do dostarczenia parametrów wejściowych. Aby jeszcze bardziej dostosować wdrożenie, rozwiń repozytorium GitHub i zmień odpowiednie elementy.

Niektóre typowe opcje dostosowywania obejmują, ale nie są ograniczone do:

- Rozmiar maszyny Wirtualnej bastionu (zmienna w azuredeploy.json)
- Konwencje nazewnictwa (zmienne w pliku azuredeploy.json)
- Specyfika klastra OpenShift, zmodyfikowana za pomocą pliku hosts (deployOpenShift.sh)
- Odwołanie do obrazu RHEL (zmienna w azuredeploy.json)

Aby wykonać kroki do wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure, wykonaj odpowiednią sekcję w sekcji [Platformy kontenerów OpenShift](./openshift-container-platform-3x.md) lub w sekcji [OKD.](./openshift-okd.md)

## <a name="next-steps"></a>Następne kroki

- [Zadania po wdrożeniu](./openshift-container-platform-3x-post-deployment.md)
- [Rozwiązywanie problemów z wdrażaniem usługi OpenShift na platformie Azure](./openshift-container-platform-3x-troubleshooting.md)