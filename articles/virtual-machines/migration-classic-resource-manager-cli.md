---
title: Migrowanie maszyn wirtualnych do Menedżer zasobów przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule omówiono migrację zasobów z klasycznej do Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurecli
ms.openlocfilehash: 671b27f927c91397d2aacd98cb7b500d8197d1c5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669334"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrowanie zasobów rozwiązania IaaS z modelu klasycznego do modelu opartego na usłudze Azure Resource Manager przy użyciu interfejsu wiersza polecenia

> [!IMPORTANT]
> Dzisiaj około 90% maszyn wirtualnych IaaS korzysta z [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28 lutego 2020, klasyczne maszyny wirtualne są przestarzałe i zostaną w pełni wycofane z 1 marca 2023. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym zaniechaniu i [sposobach jego działania](classic-vm-deprecation.md#how-does-this-affect-me).

W tych krokach pokazano, jak za pomocą poleceń interfejsu wiersza polecenia (CLI) platformy Azure przeprowadzić migrację zasobów infrastruktury jako usługi (IaaS) z klasycznego modelu wdrażania do modelu wdrażania Azure Resource Manager. Artykuł wymaga [klasycznego interfejsu wiersza polecenia platformy Azure](/cli/azure/install-classic-cli). Ponieważ interfejs wiersza polecenia platformy Azure ma zastosowanie tylko do zasobów Azure Resource Manager, nie można go użyć do tej migracji.

> [!NOTE]
> Wszystkie operacje opisane w tym miejscu to idempotentne. Jeśli wystąpi problem inny niż nieobsługiwana funkcja lub błąd konfiguracji, zalecamy ponowną próbę wykonania operacji przygotowywania, przerwania lub zatwierdzania. Platforma spróbuje wykonać akcję ponownie.
> 
> 

<br>
Oto schemat blokowy służący do identyfikowania kolejności, w której należy wykonać kroki w procesie migracji

![Zrzut ekranu przedstawiający kroki migracji](./media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Krok 1. Przygotowanie do migracji
Poniżej przedstawiono kilka najlepszych rozwiązań, które zalecamy podczas szacowania migracji zasobów IaaS z klasycznej do Menedżer zasobów:

* Zapoznaj się [z listą nieobsługiwanych konfiguracji lub funkcji](migration-classic-resource-manager-overview.md). Jeśli masz maszyny wirtualne korzystające z nieobsługiwanych konfiguracji lub funkcji, zalecamy zaczekanie na anonsowanie obsługi funkcji/konfiguracji. Alternatywnie możesz usunąć tę funkcję lub przenieść ją z tej konfiguracji, aby umożliwić migrację stosownie do potrzeb.
* W przypadku zautomatyzowanych skryptów, które obecnie wdrażają infrastrukturę i aplikacje, spróbuj utworzyć podobną konfigurację testu przy użyciu tych skryptów do migracji. Alternatywnie można skonfigurować przykładowe środowiska przy użyciu Azure Portal.

> [!IMPORTANT]
> Bramy aplikacji nie są obecnie obsługiwane w przypadku migracji z wersji klasycznej do Menedżer zasobów. Aby przeprowadzić migrację klasycznej sieci wirtualnej z bramą aplikacji, Usuń bramę przed uruchomieniem operacji przygotowywania, aby przenieść sieć. Po zakończeniu migracji Połącz się ponownie z bramą w Azure Resource Manager. 
>
>Nie można automatycznie migrować bram ExpressRoute łączących się ze obwodami usługi ExpressRoute w innej subskrypcji. W takich przypadkach Usuń bramę ExpressRoute, Przeprowadź migrację sieci wirtualnej i Utwórz ponownie bramę. Aby uzyskać więcej informacji, zobacz [Migrowanie obwodów usługi ExpressRoute i skojarzonych sieci wirtualnych z klasycznej do modelu wdrażania Menedżer zasobów](../expressroute/expressroute-migration-classic-resource-manager.md) .
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Krok 2. Ustawianie subskrypcji i rejestrowanie dostawcy
W przypadku scenariuszy migracji należy skonfigurować środowisko dla klasycznego i Menedżer zasobów. [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-classic-cli) i [Wybierz swoją subskrypcję](/cli/azure/authenticate-azure-cli).

Zaloguj się do swojego konta.

```azurecli
azure login
```

Wybierz subskrypcję platformy Azure przy użyciu następującego polecenia.

```azurecli
azure account set "<azure-subscription-name>"
```

> [!NOTE]
> Rejestracja jest jednym z etapów, ale należy wykonać ją raz przed podjęciem próby migracji. Bez rejestracji zostanie wyświetlony następujący komunikat o błędzie 
> 
> *Nieprawidłowego żądania: subskrypcja nie jest zarejestrowana na potrzeby migracji.* 
> 
> 

Zarejestruj się w dostawcy zasobów migracji przy użyciu następującego polecenia. Należy zauważyć, że w niektórych przypadkach to polecenie przestawia limit czasu. Rejestracja zostanie jednak zakończona pomyślnie.

```azurecli
azure provider register Microsoft.ClassicInfrastructureMigrate
```

Zaczekaj pięć minut na zakończenie rejestracji. Stan zatwierdzenia można sprawdzić za pomocą następującego polecenia. Przed kontynuowaniem upewnij się, że RegistrationState `Registered` .

```azurecli
azure provider show Microsoft.ClassicInfrastructureMigrate
```

Teraz Przełącz interfejs wiersza polecenia do `asm` trybu.

```azurecli
azure config mode asm
```

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 3. Upewnij się, że masz wystarczającą Azure Resource Manager procesorów wirtualnych vCPU maszyny wirtualnej w regionie świadczenia usługi Azure bieżącego wdrożenia lub sieci wirtualnej
W tym kroku należy przełączyć się do `arm` trybu. Zrób to za pomocą następującego polecenia.

```azurecli
azure config mode arm
```

Aby sprawdzić bieżącą liczbę procesorów wirtualnych vCPU w Azure Resource Manager, można użyć następującego polecenia CLI. Aby dowiedzieć się więcej o przydziałach vCPU, zobacz [limity i Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

```azurecli
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Po zakończeniu weryfikowania tego kroku możesz przełączyć się z powrotem do `asm` trybu.

```azurecli
azure config mode asm
```

## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Krok 4. Opcja 1 — Migrowanie maszyn wirtualnych w usłudze w chmurze
Pobierz listę usług w chmurze przy użyciu następującego polecenia, a następnie wybierz usługę w chmurze, którą chcesz zmigrować. Należy pamiętać, że jeśli maszyny wirtualne w usłudze w chmurze znajdują się w sieci wirtualnej lub jeśli mają role sieci Web/proces roboczy, zostanie wyświetlony komunikat o błędzie.

```azurecli
azure service list
```

Uruchom następujące polecenie, aby pobrać nazwę wdrożenia usługi w chmurze z pełnych danych wyjściowych. W większości przypadków nazwa wdrożenia jest taka sama jak nazwa usługi w chmurze.

```azurecli
azure service show <serviceName> -vv
```

Najpierw sprawdź, czy można przeprowadzić migrację usługi w chmurze przy użyciu następujących poleceń:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Przygotuj maszyny wirtualne w usłudze w chmurze na potrzeby migracji. Dostępne są dwie opcje do wyboru.

Jeśli chcesz migrować maszyny wirtualne do sieci wirtualnej utworzonej na platformie, użyj następującego polecenia.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""
```

Jeśli chcesz przeprowadzić migrację do istniejącej sieci wirtualnej w modelu wdrażania Menedżer zasobów, użyj następującego polecenia.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>
```

Po pomyślnym ukończeniu operacji przygotowywania można przeszukać pełne dane wyjściowe, aby uzyskać stan migracji maszyn wirtualnych i upewnić się, że znajdują się one w `Prepared` stanie.

```azurecli
azure vm show <vmName> -vv
```

Sprawdź konfigurację przygotowanych zasobów przy użyciu interfejsu wiersza polecenia lub Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia.

```azurecli
azure service deployment abort-migration <serviceName> <deploymentName>
```

Jeśli gotowa konfiguracja wygląda dobrze, można przenieść do przodu i zatwierdzić zasoby przy użyciu następującego polecenia.

```azurecli
azure service deployment commit-migration <serviceName> <deploymentName>
```

## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Krok 4. Opcja 2 — Migrowanie maszyn wirtualnych w sieci wirtualnej
Wybierz sieć wirtualną, która ma zostać poddana migracji. Należy pamiętać, że jeśli sieć wirtualna zawiera role sieci Web/proces roboczy lub maszyny wirtualne z nieobsługiwanymi konfiguracjami, zostanie wyświetlony komunikat o błędzie walidacji.

Pobierz wszystkie sieci wirtualne w ramach subskrypcji za pomocą następującego polecenia.

```azurecli
azure network vnet list
```

Dane wyjściowe będą przypominać następujące:

![Zrzut ekranu przedstawiający wiersz polecenia z wyróżnioną całą nazwą sieci wirtualnej.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

W powyższym przykładzie **virtualNetworkName** jest cała nazwa **"Grupa classicubuntu16 classicubuntu16"**.

Najpierw sprawdź, czy można przeprowadzić migrację sieci wirtualnej za pomocą następującego polecenia:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Przygotuj wybraną sieć wirtualną do migracji przy użyciu następującego polecenia.

```azurecli
azure network vnet prepare-migration <virtualNetworkName>
```

Sprawdź konfigurację przygotowanych maszyn wirtualnych przy użyciu interfejsu wiersza polecenia lub Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia.

```azurecli
azure network vnet abort-migration <virtualNetworkName>
```

Jeśli gotowa konfiguracja wygląda dobrze, można przenieść do przodu i zatwierdzić zasoby przy użyciu następującego polecenia.

```azurecli
azure network vnet commit-migration <virtualNetworkName>
```

## <a name="step-5-migrate-a-storage-account"></a>Krok 5. Migrowanie konta magazynu
Po zakończeniu migracji maszyn wirtualnych zalecamy przeprowadzenie migracji konta magazynu.

Przygotuj konto magazynu do migracji przy użyciu następującego polecenia

```azurecli
azure storage account prepare-migration <storageAccountName>
```

Sprawdź konfigurację przygotowanego konta magazynu przy użyciu interfejsu wiersza polecenia lub Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia.

```azurecli
azure storage account abort-migration <storageAccountName>
```

Jeśli gotowa konfiguracja wygląda dobrze, można przenieść do przodu i zatwierdzić zasoby przy użyciu następującego polecenia.

```azurecli
azure storage account commit-migration <storageAccountName>
```

## <a name="next-steps"></a>Następne kroki

* [Przegląd obsługiwanej przez platformę migracji zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Migrowanie zasobów IaaS z klasycznego do Azure Resource Manager za pomocą programu PowerShell](migration-classic-resource-manager-ps.md)
* [Narzędzia społecznościowe ułatwiające migrację zasobów IaaS z klasycznej do Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md)
* [Zapoznaj się z najczęściej zadawanymi pytaniami dotyczącymi migrowania zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-faq.md)
