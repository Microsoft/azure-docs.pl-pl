---
title: Migrowanie do usługi Azure Cloud Services (obsługa rozszerzona) przy użyciu programu PowerShell
description: Jak przeprowadzić migrację z usługi Azure Cloud Services (klasyczny) do usługi Azure Cloud Services (obsługa rozszerzona) przy użyciu programu PowerShell
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: eea49a41e81e7e580becce815ff91aff6aa430d6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287009"
---
# <a name="migrate-to-azure-cloud-services-extended-support-using-powershell"></a>Migrowanie do usługi Azure Cloud Services (obsługa rozszerzona) przy użyciu programu PowerShell

W tych krokach pokazano, jak za pomocą poleceń Azure PowerShell przeprowadzić migrację z [Cloud Services (klasycznego)](../cloud-services/cloud-services-choose-me.md) do [Cloud Services (obsługa rozszerzona)](overview.md).

> [!IMPORTANT]
> Migrowanie z Cloud Services (klasycznego) do Cloud Services (obsługa rozszerzona) za pomocą narzędzia migracji jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="1-plan-for-migration"></a>1) Planowanie migracji
Planowanie jest najważniejszym krokiem dla pomyślnego środowiska migracji. Zapoznaj się z [omówieniem Cloud Services (obsługa rozszerzona)](overview.md) i [zaplanuj migrację zasobów IaaS z klasycznej do Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-plan.md) przed rozpoczęciem wszelkich kroków migracji. 

## <a name="2-install-the-latest-version-of-powershell"></a>2) Zainstaluj najnowszą wersję programu PowerShell
Dostępne są dwie główne opcje instalacji Azure PowerShell: [Galeria programu PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) lub [Instalatora platformy sieci Web (Instalatora WebPI)](https://aka.ms/webpi-azps). Instalatora WebPI otrzymuje comiesięczne aktualizacje. Galeria programu PowerShell otrzymuje aktualizacje w sposób ciągły. Ten artykuł jest oparty na Azure PowerShell wersji 2.1.0.

Instrukcje instalacji znajdują się w temacie [How to Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0&preserve-view=true).

## <a name="3-ensure-admin-permissions"></a>3) Upewnij się, że uprawnienia administratora
Aby przeprowadzić migrację, należy dodać jako administratora dla subskrypcji w [Azure Portal](https://portal.azure.com).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu **centrum** wybierz pozycję **subskrypcja**. Jeśli go nie widzisz, wybierz pozycję **wszystkie usługi**.
3. Znajdź odpowiedni wpis subskrypcji, a następnie poszukaj pola **My role** . W przypadku współadministratora wartość powinna być *administratorem konta*.

Jeśli nie możesz dodać współadministratora, skontaktuj się z administratorem usługi lub współadministratorem, aby uzyskać subskrypcję, która ma zostać dodana.

## <a name="4-register-the-classic-provider-and-cloudservice-feature"></a>4) Zarejestruj klasycznego dostawcę i funkcję CloudService
Najpierw Uruchom wiersz polecenia programu PowerShell. W przypadku migracji Skonfiguruj środowisko dla środowiska klasycznego i Menedżer zasobów.

Zaloguj się do swojego konta, aby uzyskać Menedżer zasobów model.

```powershell
Connect-AzAccount
```

Uzyskaj dostępne subskrypcje przy użyciu następującego polecenia:

```powershell
Get-AzSubscription | Sort Name | Select Name
```

Ustaw subskrypcję platformy Azure dla bieżącej sesji. Ten przykład ustawia domyślną nazwę subskrypcji na **moją subskrypcję platformy Azure**. Zastąp własną przykładową nazwę subskrypcji.

```powershell
Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

Zarejestruj się w dostawcy zasobów migracji przy użyciu następującego polecenia:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```
> [!NOTE]
> Rejestracja to jednorazowy krok, ale należy wykonać go raz przed podjęciem próby migracji. Bez rejestrowania zostanie wyświetlony następujący komunikat o błędzie:
>
> *Nieprawidłowego żądania: subskrypcja nie jest zarejestrowana na potrzeby migracji.*

Zarejestruj funkcję CloudServices dla subskrypcji. Rejestracja może potrwać kilka minut. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Zaczekaj pięć minut na zakończenie rejestracji. 

Sprawdź stan zatwierdzenia klasycznego dostawcy za pomocą następującego polecenia:

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Sprawdź stan rejestracji, korzystając z następujących elementów:  
```powershell
Get-AzProviderFeature -FeatureName CloudServices
```

Przed kontynuowaniem upewnij się, że RegistrationState jest `Registered` dla obu.

Przed przełączeniem do klasycznego modelu wdrażania upewnij się, że masz wystarczającą liczbę Azure Resource Manager przydziału vCPU w regionie świadczenia usługi Azure bieżącego wdrożenia lub sieci wirtualnej. Aby sprawdzić bieżącą liczbę procesorów wirtualnych vCPU w Azure Resource Manager, można użyć następującego polecenia programu PowerShell. Aby dowiedzieć się więcej o przydziałach vCPU, zobacz [limity i Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Ten przykład sprawdza dostępność w regionie **zachodnie stany USA** . Zamień nazwę przykładowego regionu na własny.

```powershell
Get-AzVMUsage -Location "West US"
```

Teraz Zaloguj się do swojego konta przy użyciu klasycznego modelu wdrażania.

```powershell
Add-AzureAccount
```

Uzyskaj dostępne subskrypcje przy użyciu następującego polecenia:

```powershell
Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Ustaw subskrypcję platformy Azure dla bieżącej sesji. W tym przykładzie ustawiono domyślną subskrypcję dla **mojej subskrypcji platformy Azure**. Zastąp własną przykładową nazwę subskrypcji.

```powershell
Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="5-migrate-your-cloud-services"></a>5) Migrowanie Cloud Services 
* [Migrowanie usługi w chmurze, która nie znajduje się w sieci wirtualnej](#51-option-1---migrate-a-cloud-service-not-in-a-virtual-network)
* [Migrowanie usługi w chmurze w sieci wirtualnej](#51-option-2---migrate-a-cloud-service-in-a-virtual-network)

> [!NOTE]
> Wszystkie operacje opisane w tym miejscu to idempotentne. Jeśli wystąpi problem inny niż nieobsługiwana funkcja lub błąd konfiguracji, zalecamy ponowną próbę wykonania operacji przygotowywania, przerwania lub zatwierdzania. Następnie platforma próbuje wykonać akcję ponownie.


### <a name="51-option-1---migrate-a-cloud-service-not-in-a-virtual-network"></a>5,1) opcja 1 — Migrowanie usługi w chmurze spoza sieci wirtualnej
Pobierz listę usług w chmurze za pomocą następującego polecenia. Następnie wybierz usługę w chmurze, która ma zostać poddana migracji.

```powershell
Get-AzureService | ft Servicename
```

Pobierz nazwę wdrożenia dla usługi w chmurze. W tym przykładzie nazwa usługi to **Moja usługa**. Zastąp przykładową nazwę usługi własną nazwą usługi.

```powershell
$serviceName = "My Service"
$deployment = Get-AzureDeployment -ServiceName $serviceName
$deploymentName = $deployment.DeploymentName
```

Najpierw sprawdź, czy można przeprowadzić migrację usługi w chmurze, używając następujących poleceń:

```powershell
$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
$validate.ValidationMessages
 ```

Następujące polecenie wyświetla ostrzeżenia i błędy, które blokują migrację. Jeśli sprawdzanie poprawności zakończy się pomyślnie, możesz przejść do kroku przygotowania.

```powershell
Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

### <a name="51-option-2---migrate-a-cloud-service-in-a-virtual-network"></a>5,1) Opcja 2 — Migrowanie usługi w chmurze w sieci wirtualnej

Aby przeprowadzić migrację usługi w chmurze w sieci wirtualnej, należy przeprowadzić migrację sieci wirtualnej. Usługa w chmurze jest automatycznie migrowana z siecią wirtualną.

> [!NOTE]
> Nazwa sieci wirtualnej może się różnić od tego, co jest widoczne w nowym portalu. Nowy Azure Portal wyświetla nazwę jako `[vnet-name]` , ale rzeczywista nazwa sieci wirtualnej jest typu `Group [resource-group-name] [vnet-name]` . Przed rozpoczęciem migracji Wyszukaj rzeczywistą nazwę sieci wirtualnej przy użyciu polecenia `Get-AzureVnetSite | Select -Property Name` lub Wyświetl je w starym Azure Portal. 

Ten przykład ustawia nazwę sieci wirtualnej na **myVnet**. Zamień nazwę przykładowej sieci wirtualnej na własną.

```powershell
$vnetName = "myVnet"
```

Najpierw sprawdź, czy można przeprowadzić migrację sieci wirtualnej za pomocą następującego polecenia:

```powershell
Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Następujące polecenie wyświetla ostrzeżenia i błędy, które blokują migrację. Jeśli sprawdzanie poprawności zakończy się pomyślnie, możesz przejść do następujących kroków:

```powershell
Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Sprawdź konfigurację przygotowanej usługi w chmurze przy użyciu Azure PowerShell lub Azure Portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia:

```powershell
Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Jeśli gotowa konfiguracja wygląda dobrze, można przenieść do przodu i zatwierdzić zasoby przy użyciu następującego polecenia:

```powershell
Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z sekcją [zmiany po migracji](in-place-migration-overview.md#post-migration-changes) , aby zobaczyć zmiany w plikach wdrożenia, automatyzacji i innych atrybutach nowego wdrożenia Cloud Services (obsługa rozszerzona). 
