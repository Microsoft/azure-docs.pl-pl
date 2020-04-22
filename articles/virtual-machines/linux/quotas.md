---
title: Limity przydziału procesorów wirtualnych
description: Dowiedz się więcej o przydziałach procesora wirtualnego dla platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 4f8d181194328725c2e7c02f6e73752cf568ef4e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759184"
---
# <a name="virtual-machine-vcpu-quotas"></a>Limity przydziału procesorów wirtualnych maszyny wirtualnej

Przydziały procesora wirtualnego dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych są rozmieszczone w dwóch warstwach dla każdej subskrypcji w każdym regionie. Pierwsza warstwa to całkowita regionalna maszyna wirtualna, a druga warstwa to różne rdzenie rodziny maszyn wirtualnych, takie jak procesory wirtualne z serii D. Za każdym razem, gdy nowa maszyna wirtualna jest wdrażana, procesory wirtualne dla maszyny wirtualnej nie mogą przekraczać przydziału vCPU dla rodziny rozmiarów maszyn wirtualnych ani całkowitego regionalnego przydziału vCPU. Jeśli którykolwiek z tych przydziałów zostaną przekroczone, wdrożenie maszyny Wirtualnej nie będzie dozwolone. Istnieje również przydział dla ogólnej liczby maszyn wirtualnych w regionie. Szczegóły dotyczące każdego z tych przydziałów można zobaczyć w **użycia + przydziały** sekcji **subskrypcji** strony w [witrynie Azure portal](https://portal.azure.com)lub można zbadać wartości przy użyciu interfejsu wiersza polecenia platformy Azure.


## <a name="check-usage"></a>Sprawdzanie użycia

Użycie przydziału można sprawdzić za pomocą [az vm list-usage](/cli/azure/vm).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

Dane wyjściowe powinny wyglądać następująco:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Wystąpienia zarezerwowane maszyn wirtualnych
Zarezerwowane wystąpienia maszyn wirtualnych, które są ograniczone do pojedynczej subskrypcji bez elastyczności rozmiaru maszyny Wirtualnej, doda nowy aspekt do przydziałów vCPU. Te wartości opisują liczbę wystąpień określonego rozmiaru, które muszą być wdrażalne w subskrypcji. Działają one jako symbol zastępczy w systemie przydziałów, aby upewnić się, że przydział jest zarezerwowany, aby upewnić się, że rezerwacje platformy Azure są możliwe do wdrożenia w ramach subskrypcji. Na przykład jeśli określona subskrypcja ma 10 Standard_D1 rezerwacji limit użycia dla Standard_D1 rezerwacji będzie 10. Spowoduje to, że platforma Azure zapewni, że w przydziałzie totalialnych procesorów wirtualnych będzie używanych co najmniej 10 procesorów wirtualnych w przydziału regionalnych procesorów wirtualnych, które mają być używane dla Standard_D1 wystąpień, a w przydziałzie wirtualnego procesora typu D dostępne jest co najmniej 10 procesorów wirtualnych, które mają być używane dla Standard_D1 wystąpień.

Jeśli zwiększenie przydziału jest wymagane do zakupu identyfikatora RI pojedynczej subskrypcji, można [zażądać zwiększenia przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) w ramach subskrypcji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozliczeń i przydziałów, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
