---
title: Korzyść użycia hybrydowego platformy Azure dla zestawów skalowania maszyn wirtualnych z systemem Linux
description: Dowiedz się, jak Korzyść użycia hybrydowego platformy Azure można zastosować do zestawu skalowania maszyn wirtualnych, aby ułatwić oszczędność pieniędzy na maszynach wirtualnych z systemem Linux działających na platformie Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.subservice: linux
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: 09e41b4071ad02120f7d303b32bc87bc0ba39e2c
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105549301"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set-public-preview"></a>Korzyść użycia hybrydowego platformy Azure dla zestawu skalowania maszyn wirtualnych z systemem Linux (publiczna wersja zapoznawcza)

**Korzyść użycia hybrydowego platformy Azure dla zestawu skalowania maszyn wirtualnych z systemem Linux jest teraz w publicznej wersji zapoznawczej**. Korzyść AHB może pomóc w zmniejszeniu kosztów uruchamiania RHEL i SLES [zestawów skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

Dzięki tym korzyściom płacisz tylko za koszt infrastruktury zestawu skalowania. Korzyść jest dostępna dla wszystkich obrazów RHEL i SLES Marketplace z opcją płatność zgodnie z rzeczywistym użyciem.


>[!NOTE]
> W tym artykule opisano Korzyść użycia hybrydowego platformy Azure dla systemu Linux VMSS. Istnieje oddzielny [Artykuł dostępny [tutaj AHB dla maszyn wirtualnych z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/azure-hybrid-benefit-linux), który jest już dostępny dla klientów platformy Azure od listopada 2020.

## <a name="benefit-description"></a>Opis korzyści
Hybrydowe platformy Azure umożliwiają korzystanie z istniejących licencji dostępowych w chmurze w systemie Red Hat lub SUSE oraz elastyczne konwertowanie wystąpień zestawu skalowania maszyn wirtualnych na rozliczanie własnych subskrypcji (BYOS). 

Zestaw skalowania maszyn wirtualnych wdrożony z obrazów w witrynie PAYG Marketplace spowoduje naliczenie opłaty za infrastrukturę i oprogramowanie. W przypadku AHB wystąpienia PAYG można przekonwertować na model rozliczeń BYOS bez ponownego wdrożenia.

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Korzyść użycia hybrydowego platformy Azure wizualizacji kosztów na maszynach wirtualnych z systemem Linux.":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>Zakres Korzyść użycia hybrydowego platformy Azure uprawnień dla systemu Linux
Korzyść użycia hybrydowego platformy Azure jest dostępny dla wszystkich obrazów RHEL i SLES PAYG z witryny Azure Marketplace. Korzyść nie jest jeszcze dostępna dla obrazów RHEL lub SLES BYOS ani obrazów niestandardowych z witryny Azure Marketplace.

Wystąpienia dedykowanego hosta platformy Azure i korzyści z używania hybrydowych danych SQL nie są uprawnione do Korzyść użycia hybrydowego platformy Azure, jeśli już używasz korzyści z maszynami wirtualnymi z systemem Linux.

## <a name="get-started"></a>Rozpoczęcie pracy

### <a name="red-hat-customers"></a>Klienci firmy Red Hat

Korzyść użycia hybrydowego platformy Azure dla RHEL jest dostępny dla klientów Red Hat, którzy spełniają oba te kryteria:

- Masz aktywne lub nieużywane subskrypcje RHEL, które kwalifikują się do użycia na platformie Azure
- Włączono co najmniej jedną z tych subskrypcji do użycia na platformie Azure przy użyciu programu [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

> [!IMPORTANT]
> Upewnij się, że w programie [dostępu do chmury](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) włączono poprawną subskrypcję.

Aby zacząć korzystać z zalet firmy Red Hat:

1. Włącz odpowiednie subskrypcje RHEL na platformie Azure, korzystając z [interfejsu klienta Red Hat Cloud Access](https://access.redhat.com/management/cloud).

   W przypadku subskrypcji platformy Azure, które podano podczas procesu włączania dostępu w chmurze Red Hat, będzie można używać funkcji Korzyść użycia hybrydowego platformy Azure.
1. Zastosuj Korzyść użycia hybrydowego platformy Azure do dowolnego istniejącego i nowego zestawu skalowania maszyn wirtualnych RHEL PAYG. Możesz użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby umożliwić korzystanie z tej korzyści.
1. Postępuj zgodnie z zalecanymi [następnymi krokami](https://access.redhat.com/articles/5419341) dotyczącymi konfigurowania źródeł aktualizacji dla maszyn wirtualnych RHEL oraz wytycznych dotyczących zgodności subskrypcji RHEL.


### <a name="suse-customers"></a>Klienci SUSE

Aby zacząć korzystać z korzyści dla SUSE:

1. Zarejestruj się w programie firmy SUSE w chmurze publicznej.
1. Zastosuj korzyść do nowo utworzonego lub istniejącego zestawu skalowania maszyn wirtualnych za pośrednictwem Azure Portal lub interfejsu wiersza polecenia platformy Azure.
1. Zarejestruj swoje maszyny wirtualne otrzymujące korzyść przy użyciu oddzielnego źródła aktualizacji.


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Włączanie i wyłączanie korzyści w witrynie Azure Portal 
Środowisko portalu do włączania i wyłączania AHB w zestawie skalowania maszyn wirtualnych **nie jest obecnie dostępne**.

## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>Włączanie i wyłączanie korzyści przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz użyć polecenia, `az vmss update` Aby zaktualizować istniejące maszyny wirtualne. W przypadku maszyn wirtualnych RHEL Uruchom polecenie z `--license-type` parametrem `RHEL_BYOS` . W przypadku maszyn wirtualnych SLES Uruchom polecenie z `--license-type` parametrem `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Przykład interfejsu wiersza polecenia, aby włączyć korzyść
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Przykład interfejsu wiersza polecenia do wyłączania korzyści
Aby wyłączyć korzyść, użyj `--license-type` wartości `None` :

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> Zestawy skalowania mają ["zasady uaktualniania"](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , które określają sposób, w jaki maszyny wirtualne są uaktualniane przy użyciu najnowszego modelu zestawu skalowania. W związku z tym jeśli VMSS mają zasady uaktualniania "Automatyczne", korzyść AHB zostanie automatycznie zastosowana w miarę aktualizowania wystąpień maszyn wirtualnych. Jeśli VMSS mają zasady uaktualniania "toczenia" oparte na zaplanowanych aktualizacjach, zostanie zastosowane AHB.
W przypadku zasad uaktualniania "ręczne" należy wykonać "ręczne uaktualnienie" każdej istniejącej maszyny wirtualnej.  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>Przykład interfejsu wiersza polecenia do uaktualniania wystąpień zestawu skalowania maszyn wirtualnych w przypadku zasad "ręczne uaktualnienie" 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>Zastosuj Korzyść użycia hybrydowego platformy Azure w czasie tworzenia zestawu skalowania maszyn wirtualnych 
Oprócz zastosowania Korzyść użycia hybrydowego platformy Azure do istniejącego zestawu skalowania maszyn wirtualnych z opcją płatność zgodnie z rzeczywistym użyciem można wywołać go w momencie tworzenia zestawu skalowania maszyn wirtualnych. Korzyści wynikające z tego są Threefold:
- Można udostępnić zarówno funkcję PAYG, jak i BYOS wystąpienia zestawu skalowania maszyn wirtualnych, korzystając z tego samego obrazu i procesu.
- Umożliwia ona zmiany trybu licencjonowania w przyszłości, coś nie jest dostępne z obrazem BYOS.
- Wystąpienia zestawu skalowania maszyn wirtualnych będą domyślnie połączone z usługą Red Hat Update Infrastructure (RHUI), aby upewnić się, że są one aktualne i bezpieczne. Zaktualizowany mechanizm można zmienić po wdrożeniu w dowolnym momencie.

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>Przykład interfejsu wiersza polecenia do tworzenia zestawu skalowania maszyn wirtualnych z korzyścią AHB
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak tworzyć i aktualizować maszyny wirtualne oraz dodawać typy licencji (RHEL_BYOS, SLES_BYOS) dla Korzyść użycia hybrydowego platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/vmss)
