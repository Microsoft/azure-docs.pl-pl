---
title: Automatyczne stosowanie poprawek gościa maszyny wirtualnej dla maszyn wirtualnych platformy Azure
description: Dowiedz się, jak automatycznie poprawiać maszyny wirtualne na platformie Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 1a6a67fe43d4e0a6086154d71e61fe51680dbcd0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762591"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>Wersja zapoznawcza: automatyczne stosowanie poprawek gościa maszyny wirtualnej dla maszyn wirtualnych platformy Azure

Włączenie automatycznego poprawiania gościa maszyny wirtualnej dla maszyn wirtualnych platformy Azure ułatwia zarządzanie aktualizacjami dzięki bezpiecznemu i automatycznemu poprawianiu maszyn wirtualnych w celu zachowania zgodności z zabezpieczeniami.

Automatyczne stosowanie poprawek gościa maszyny wirtualnej ma następujące cechy:
- Poprawki sklasyfikowane jako *Krytyczne* lub *Zabezpieczenia* są automatycznie pobierane i stosowane na maszynie wirtualnej.
- Poprawki są stosowane poza godzinami szczytu w strefie czasowej maszyny wirtualnej.
- Orkiestracja poprawek jest zarządzana przez platformę Azure, a poprawki są stosowane zgodnie z [zasadami pierwszej dostępności.](#availability-first-patching)
- Kondycja maszyny wirtualnej określona za pośrednictwem sygnałów kondycji platformy jest monitorowana w celu wykrywania błędów poprawek.
- Działa dla wszystkich rozmiarów maszyn wirtualnych.

> [!IMPORTANT]
> Automatyczne stosowanie poprawek gościa maszyny wirtualnej jest obecnie dostępne w publicznej wersji zapoznawczej. Procedura zgody jest potrzebna do korzystania z funkcji publicznej wersji zapoznawczej opisanej poniżej.
> Ta wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Jak działa automatyczne stosowanie poprawek gościa maszyny wirtualnej?

Jeśli automatyczne stosowanie poprawek gościa maszyny wirtualnej  jest włączone na maszynie wirtualnej, dostępne poprawki Krytyczne i Zabezpieczenia są pobierane i stosowane automatycznie na maszynie wirtualnej.  Ten proces jest uruchamiany automatycznie co miesiąc, gdy są zwalniane nowe poprawki. Ocena poprawek i instalacja są automatyczne, a proces obejmuje ponowne uruchomienie maszyny wirtualnej zgodnie z potrzebami.

Maszyna wirtualna jest okresowo oceniana co kilka dni i wiele razy w dowolnym okresie 30 dni w celu określenia odpowiednich poprawek dla tej maszyny wirtualnej. Poprawki można zainstalować w dowolnym dniu na maszynie wirtualnej poza godzinami szczytu maszyny wirtualnej. Ta automatyczna ocena gwarantuje, że wszelkie brakujące poprawki zostaną odnalezione jak najszybciej.

Poprawki są instalowane w ciągu 30 dni od comiesięcznych wydań poprawek zgodnie z aranżacją availability-first opisaną poniżej. Poprawki są instalowane tylko poza godzinami szczytu maszyny wirtualnej, w zależności od strefy czasowej maszyny wirtualnej. Maszyna wirtualna musi działać poza godzinami szczytu, aby poprawki zostały automatycznie zainstalowane. Jeśli maszyna wirtualna zostanie wyłączona podczas okresowej oceny, maszyna wirtualna zostanie automatycznie oceniona, a odpowiednie poprawki zostaną zainstalowane automatycznie podczas następnej okresowej oceny (zazwyczaj w ciągu kilku dni), gdy maszyna wirtualna jest wł.

Aktualizacje definicji i inne poprawki, które nie zostały sklasyfikowane jako *krytyczne* *lub* nie zostaną zainstalowane za pomocą automatycznego poprawiania gościa maszyny wirtualnej. Aby zainstalować poprawki z innymi klasyfikacjami poprawek lub zaplanować instalację poprawek we własnym niestandardowym [oknie](./windows/tutorial-config-management.md#manage-windows-updates)obsługi, możesz użyć Update Management .

### <a name="availability-first-patching"></a>Stosowanie poprawek najpierw dostępności

Proces instalacji poprawek jest aranżowany globalnie przez platformę Azure dla wszystkich maszyn wirtualnych z włączonym automatycznym stosowaniem poprawek gościa maszyny wirtualnej. Ta aranżacja jest zgodna z zasadami dostępności pierwszej na różnych poziomach dostępności zapewnianych przez platformę Azure.

W przypadku grupy maszyn wirtualnych w trakcie aktualizacji platforma Azure będzie orkiestrować aktualizacje:

**W różnych regionach:**
- Comiesięczna aktualizacja jest aranżowana globalnie na platformie Azure w sposób etapowy, aby zapobiec niepowodzeniu wdrażania globalnego.
- Faza może mieć co najmniej jeden region, a aktualizacja przechodzi do następnych faz tylko wtedy, gdy kwalifikujące się maszyny wirtualne w aktualizacji fazowej są pomyślnie aktualizowane.
- Regiony sparowane geograficznie nie są aktualizowane współbieżnie i nie mogą być w tej samej fazie regionalnej.
- Powodzenie aktualizacji jest mierzone przez śledzenie kondycji maszyny wirtualnej po aktualizacji. Kondycja maszyny wirtualnej jest śledzona za pomocą wskaźników kondycji platformy dla maszyny wirtualnej.

**W obrębie regionu:**
- Maszyny wirtualne w różnych Strefy dostępności nie są aktualizowane jednocześnie.
- Maszyny wirtualne, które nie są częścią zestawu dostępności, są wsadowe, aby uniknąć równoczesnych aktualizacji dla wszystkich maszyn wirtualnych w subskrypcji.

**W zestawie dostępności:**
- Wszystkie maszyny wirtualne w wspólnym zestawie dostępności nie są aktualizowane współbieżnie.
-   Maszyny wirtualne we wspólnym zestawie dostępności są aktualizowane w granicach domeny aktualizacji, a maszyny wirtualne w wielu domenach aktualizacji nie są aktualizowane współbieżnie.

Data instalacji poprawki dla danej maszyny wirtualnej może różnić się z miesiąca na miesiąc, ponieważ określoną maszynę wirtualną można wybrać w innej partii między miesięcznymi cyklami poprawek.

### <a name="which-patches-are-installed"></a>Które poprawki są zainstalowane?
Zainstalowane poprawki zależą od etapu we/wy dla maszyny wirtualnej. Co miesiąc jest rozpoczynane nowe globalne wprowadzenie, w którym są instalowane wszystkie poprawki zabezpieczeń i krytyczne ocenione dla poszczególnych maszyn wirtualnych dla tej maszyny wirtualnej. Etap ten jest aranżowany we wszystkich regionach świadczenia usługi Azure w partiach (opisany w powyższej sekcji dotyczącej poprawiania po raz pierwszy dostępności).

Dokładny zestaw poprawek do zainstalowania różni się w zależności od konfiguracji maszyny wirtualnej, w tym typu systemu operacyjnego i czasu oceny. Dwie identyczne maszyny wirtualne w różnych regionach mogą instalować różne poprawki, jeśli jest dostępnych więcej lub mniej poprawek, gdy aranżacja poprawek dociera do różnych regionów w różnym czasie. Podobnie, ale rzadziej, maszyny wirtualne w tym samym regionie, ale oceniane w różnym czasie (z powodu różnych partii strefy dostępności lub zestawu dostępności), mogą uzyskać różne poprawki.

Ponieważ automatyczne stosowanie poprawek gościa maszyny wirtualnej nie konfiguruje źródła poprawek, dwie podobne maszyny wirtualne skonfigurowane do różnych źródeł poprawek, takie jak repozytorium publiczne i repozytorium prywatne, mogą również zobaczyć różnicę w dokładnym zestawie zainstalowanych poprawek.

W przypadku typów systemów operacyjnych, które zwalniają poprawki w stałym czasie, maszyny wirtualne skonfigurowane w repozytorium publicznym dla systemu operacyjnego mogą oczekiwać, że w ciągu miesiąca otrzymają ten sam zestaw poprawek w różnych fazach realizacji. Na przykład maszyny wirtualne z systemem Windows skonfigurowane do publicznego Windows Update repozytorium.

W związku z tym, że nowe uruchomienie jest wyzwalane co miesiąc, maszyna wirtualna będzie otrzymywać co najmniej jedną poprawkę w każdym miesiącu, jeśli maszyna wirtualna jest wyzwolona poza godzinami szczytu. Dzięki temu maszyna wirtualna będzie co miesiąc poprawiana przy użyciu najnowszych dostępnych poprawek zabezpieczeń i krytycznych. Aby zapewnić spójność w zestawie zainstalowanych poprawek, możesz skonfigurować maszyny wirtualne do oceniania i pobierania poprawek z własnych repozytoriów prywatnych.

## <a name="supported-os-images"></a>Obsługiwane obrazy systemu operacyjnego
W wersji zapoznawczej są obecnie obsługiwane tylko maszyny wirtualne utworzone na podstawie niektórych obrazów platformy systemu operacyjnego. Obrazy niestandardowe nie są obecnie obsługiwane w wersji zapoznawczej.

Obecnie obsługiwane są następujące jednostki SKU platformy (i kolejne są dodawane okresowo):

| Publisher               | Oferta systemu operacyjnego      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| Redhat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019 Datacenter-Core |

## <a name="patch-orchestration-modes"></a>Tryby orkiestracji poprawek
Maszyny wirtualne na platformie Azure obsługują teraz następujące tryby orkiestracji poprawek:

**AutomaticByPlatform:**
- Ten tryb jest obsługiwany zarówno dla maszyn wirtualnych z systemem Linux, jak i Windows.
- Ten tryb umożliwia automatyczne stosowanie poprawek gościa maszyny wirtualnej dla maszyny wirtualnej, a kolejna instalacja poprawek jest aranżowana przez platformę Azure.
- Ten tryb jest wymagany w przypadku poprawiania najpierw dostępności.
- Ten tryb jest obsługiwany tylko w przypadku maszyn wirtualnych utworzonych przy użyciu obsługiwanych obrazów platformy systemu operacyjnego wymienionych powyżej.
- W przypadku maszyn wirtualnych z systemem Windows ustawienie tego trybu powoduje również wyłączenie natywnych aktualizacji automatycznych na maszynie wirtualnej z systemem Windows w celu uniknięcia duplikacji.
- Aby użyć tego trybu na maszynach wirtualnych z systemem Linux, ustaw właściwość `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` w szablonie maszyny wirtualnej.
- Aby użyć tego trybu na maszynach wirtualnych z systemem Windows, ustaw właściwość `osProfile.windowsConfiguration.enableAutomaticUpdates=true` i ustaw właściwość w  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` szablonie maszyny wirtualnej.

**AutomaticByOS:**
- Ten tryb jest obsługiwany tylko w przypadku maszyn wirtualnych z systemem Windows.
- Ten tryb włącza aktualizacje automatyczne na maszynie wirtualnej z systemem Windows, a poprawki są instalowane na maszynie wirtualnej za pośrednictwem aktualizacji automatycznych.
- Ten tryb nie obsługuje poprawiania najpierw dostępności.
- Ten tryb jest ustawiany domyślnie, jeśli nie określono innego trybu poprawek dla maszyny wirtualnej z systemem Windows.
- Aby użyć tego trybu na maszynach wirtualnych z systemem Windows, ustaw właściwość `osProfile.windowsConfiguration.enableAutomaticUpdates=true` i ustaw właściwość w  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` szablonie maszyny wirtualnej.

**Ręcznie:**
- Ten tryb jest obsługiwany tylko w przypadku maszyn wirtualnych z systemem Windows.
- Ten tryb wyłącza aktualizacje automatyczne na maszynie wirtualnej z systemem Windows.
- Ten tryb nie obsługuje poprawek najpierw dostępności.
- Ten tryb należy ustawić w przypadku korzystania z niestandardowych rozwiązań do stosowania poprawek.
- Aby użyć tego trybu na maszynach wirtualnych z systemem Windows, ustaw właściwość `osProfile.windowsConfiguration.enableAutomaticUpdates=false` i ustaw właściwość w  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` szablonie maszyny wirtualnej.

**ImageDefault:**
- Ten tryb jest obsługiwany tylko w przypadku maszyn wirtualnych z systemem Linux.
- Ten tryb nie obsługuje poprawiania najpierw dostępności.
- W tym trybie jest stosowana domyślna konfiguracja poprawek na obrazie użytym do utworzenia maszyny wirtualnej.
- Ten tryb jest ustawiany domyślnie, jeśli nie określono innego trybu poprawek dla maszyny wirtualnej z systemem Linux.
- Aby użyć tego trybu na maszynach wirtualnych z systemem Linux, ustaw `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` właściwość w szablonie maszyny wirtualnej.

> [!NOTE]
>W przypadku maszyn wirtualnych z systemem Windows właściwość można obecnie ustawić `osProfile.windowsConfiguration.enableAutomaticUpdates` tylko podczas tworzenia maszyny wirtualnej po raz pierwszy. Przełączanie z trybu ręcznego na automatyczny lub z trybu automatycznego do trybu ręcznego nie jest obecnie obsługiwane. Obsługiwane jest przełączanie z trybu AutomaticByOS do trybu AutomaticByPlatfom.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Wymagania dotyczące włączania automatycznego poprawiania gościa maszyny wirtualnej

- Maszyna wirtualna musi mieć zainstalowanego agenta maszyny wirtualnej platformy Azure dla [systemu Windows](./extensions/agent-windows.md) [lub Linux.](./extensions/agent-linux.md)
- W przypadku maszyn wirtualnych z systemem Linux agent systemu Linux platformy Azure musi być w wersji 2.2.53.1 lub wyższej. [Zaktualizuj agenta systemu Linux,](./extensions/update-linux-agent.md) jeśli bieżąca wersja jest niższa niż wymagana wersja.
- W przypadku maszyn wirtualnych z systemem Windows Windows Update musi być uruchomiona na maszynie wirtualnej.
- Maszyna wirtualna musi mieć dostęp do skonfigurowanych punktów końcowych aktualizacji. Jeśli maszyna wirtualna jest skonfigurowana do używania repozytoriów prywatnych dla systemu Linux lub Windows Server Update Services (WSUS) dla maszyn wirtualnych z systemem Windows, odpowiednie punkty końcowe aktualizacji muszą być dostępne.
- Użyj interfejsu API obliczeń w wersji 2020-12-01 lub wyższej. Interfejs API obliczeń w wersji 2020-06-01 może być używany dla maszyn wirtualnych z systemem Windows o ograniczonej funkcjonalności.

Włączenie funkcji w wersji zapoznawczej wymaga zgody na funkcje **InGuestAutoPatchVMPreview** i **InGuestPatchVMPreview** na subskrypcję, zgodnie z informacjami w poniższej sekcji.

### <a name="rest-api"></a>Interfejs API REST
W poniższym przykładzie opisano sposób włączania wersji zapoznawczej subskrypcji:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Register-AzProviderFeature,](/powershell/module/az.resources/register-azproviderfeature) aby włączyć podgląd subskrypcji.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [az feature register,](/cli/azure/feature#az_feature_register) aby włączyć podgląd subskrypcji.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Włączanie automatycznego stosowania poprawek gościa maszyny wirtualnej
Aby włączyć automatyczne stosowanie poprawek gościa maszyny wirtualnej na maszynie wirtualnej z systemem Windows, upewnij się, że właściwość *osProfile.windowsConfiguration.enableAutomaticUpdates* została ustawiona na *wartość true* w definicji szablonu maszyny wirtualnej. Tę właściwość można ustawić tylko podczas tworzenia maszyny wirtualnej. Ta dodatkowa właściwość nie ma zastosowania w przypadku maszyn wirtualnych z systemem Linux.

### <a name="rest-api-for-linux-vms"></a>Interfejs API REST dla maszyn wirtualnych z systemem Linux
W poniższym przykładzie opisano sposób włączania automatycznego poprawiania gościa maszyny wirtualnej:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>Interfejs API REST dla maszyn wirtualnych z systemem Windows
W poniższym przykładzie opisano sposób włączania automatycznego poprawiania gościa maszyny wirtualnej:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>Azure PowerShell dla maszyn wirtualnych z systemem Windows
Użyj polecenia cmdlet [Set-AzVMOperatingSystem,](/powershell/module/az.compute/set-azvmoperatingsystem) aby włączyć automatyczne stosowanie poprawek gościa maszyny wirtualnej podczas tworzenia lub aktualizowania maszyny wirtualnej.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Interfejs wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Windows
Użyj [az vm create,](/cli/azure/vm#az_vm_create) aby włączyć automatyczne stosowanie poprawek gościa maszyny wirtualnej podczas tworzenia nowej maszyny wirtualnej. W poniższym przykładzie skonfigurowano automatyczne stosowanie poprawek gościa maszyny wirtualnej dla maszyny wirtualnej o nazwie *myVM* w grupie zasobów *o nazwie myResourceGroup:*

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Aby zmodyfikować istniejącą maszynę wirtualną, użyj [az vm update](/cli/azure/vm#az_vm_update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Włączanie i ocena

> [!NOTE]
>Włączenie automatycznych aktualizacji gościa maszyny wirtualnej na maszynie wirtualnej może potrwać więcej niż trzy godziny, ponieważ włączanie jest ukończone poza godzinami szczytu maszyny wirtualnej. Ponieważ ocena i instalacja poprawek występują tylko poza godzinami szczytu, maszyna wirtualna musi być również uruchomiona poza godzinami szczytu, aby zastosować poprawki.

Gdy automatyczne stosowanie poprawek gościa maszyny wirtualnej jest włączone dla maszyny wirtualnej, rozszerzenie typu maszyny wirtualnej jest instalowane na maszynie wirtualnej z systemem Linux lub na maszynie wirtualnej z `Microsoft.CPlat.Core.LinuxPatchExtension` `Microsoft.CPlat.Core.WindowsPatchExtension` systemem Windows. Tego rozszerzenia nie trzeba ręcznie instalować ani aktualizować, ponieważ jest zarządzane przez platformę Azure w ramach procesu automatycznego poprawiania gościa maszyny wirtualnej.

Włączenie automatycznych aktualizacji gościa maszyny wirtualnej na maszynie wirtualnej może potrwać więcej niż trzy godziny, ponieważ włączanie jest ukończone poza godzinami szczytu maszyny wirtualnej. Rozszerzenie jest również instalowane i aktualizowane poza godzinami szczytu maszyny wirtualnej. Jeśli godziny poza godzinami szczytu maszyny wirtualnej zakończą się przed ukończeniem włączania, proces włączania zostanie wznowiony w następnym dostępnym czasie poza szczytem.

Aktualizacje automatyczne są wyłączone w większości scenariuszy, a instalacja poprawek odbywa się w ramach rozszerzenia w przyszłości. Obowiązują następujące warunki.
- Jeśli maszyna wirtualna z systemem Windows wcześniej Windows Update włączona za pośrednictwem trybu poprawki AutomaticByOS, funkcja automatycznego Windows Update jest wyłączona dla maszyny wirtualnej podczas instalowania rozszerzenia.
- W przypadku maszyn wirtualnych z systemem Ubuntu domyślne aktualizacje automatyczne są automatycznie wyłączane po zakończeniu włączania automatycznego poprawiania gościa maszyny wirtualnej.
- W przypadku systemu RHEL należy ręcznie wyłączyć aktualizacje automatyczne (jest to ograniczenie wersji zapoznawczej). Wykonać:

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Aby sprawdzić, czy automatyczne stosowanie poprawek gościa maszyny wirtualnej zostało ukończone i czy na maszynie wirtualnej zainstalowano rozszerzenie poprawek, możesz przejrzeć widok wystąpienia maszyny wirtualnej. Jeśli proces włączania zostanie ukończony, rozszerzenie zostanie zainstalowane, a wyniki oceny dla maszyny wirtualnej będą dostępne w obszarze `patchStatus` . Dostęp do widoku wystąpienia maszyny wirtualnej można uzyskać na wiele sposobów, zgodnie z poniższym opisem.

### <a name="rest-api"></a>Interfejs API REST

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia [cmdlet Get-AzVM](/powershell/module/az.compute/get-azvm) z parametrem , aby uzyskać dostęp do `-Status` widoku wystąpienia maszyny wirtualnej.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

Obecnie program PowerShell udostępnia tylko informacje o rozszerzeniu poprawek. Informacje na `patchStatus` temat usługi będą również dostępne wkrótce za pośrednictwem programu PowerShell.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Użyj [az vm get-instance-view,](/cli/azure/vm#az_vm_get_instance_view) aby uzyskać dostęp do widoku wystąpienia maszyny wirtualnej.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Opis stanu poprawki dla maszyny wirtualnej

Sekcja odpowiedzi widoku wystąpienia zawiera szczegółowe informacje na temat najnowszej oceny i `patchStatus` instalacji ostatniej poprawki dla maszyny wirtualnej.

Wyniki oceny maszyny wirtualnej można przejrzeć w `availablePatchSummary` sekcji . Ocena jest okresowo przeprowadzana dla maszyny wirtualnej z włączonym automatycznym stosowaniem poprawek gościa maszyny wirtualnej. Liczba dostępnych poprawek po podano w obszarze `criticalAndSecurityPatchCount` i `otherPatchCount` wyników oceny. Automatyczne stosowanie poprawek gościa maszyny wirtualnej spowoduje zainstalowanie wszystkich poprawek ocenionych w ramach *klasyfikacji* poprawek krytycznych *i* zabezpieczeń. Wszelkie inne ocenione poprawki są pomijane.

Wyniki instalacji poprawek dla maszyny wirtualnej można przejrzeć w `lastPatchInstallationSummary` sekcji . Ta sekcja zawiera szczegółowe informacje o ostatniej próbie instalacji poprawki na maszynie wirtualnej, w tym liczbę zainstalowanych poprawek, oczekujących, nieudanych lub pominiętych. Poprawki są instalowane tylko poza godzinami szczytu okna obsługi maszyny wirtualnej. Podczas następnego okna obsługi poza godzinami szczytu są automatycznie ponownie tworzone oczekujące i nieudane poprawki.

## <a name="on-demand-patch-assessment"></a>Ocena poprawek na żądanie
Jeśli automatyczne stosowanie poprawek gościa maszyny wirtualnej jest już włączone dla maszyny wirtualnej, okresowa ocena poprawek jest wykonywana na maszynie wirtualnej poza godzinami szczytu. Ten proces jest automatyczny, a wyniki najnowszej oceny można przeglądać za pomocą widoku wystąpienia maszyny wirtualnej, jak opisano wcześniej w tym dokumencie. W dowolnym momencie możesz również wyzwolić ocenę poprawek na żądanie dla maszyny wirtualnej. Ocena poprawek może potrwać kilka minut, a stan najnowszej oceny zostanie zaktualizowany w widoku wystąpienia maszyny wirtualnej.

Włączenie funkcji w wersji zapoznawczej wymaga jednej zgody dla funkcji **InGuestPatchVMPreview** na subskrypcję. Ta wersja zapoznawcza funkcji różni się od automatycznej rejestracji funkcji poprawek gościa maszyny wirtualnej wykonanej wcześniej dla **funkcji InGuestAutoPatchVMPreview.** Włączenie dodatkowej wersji zapoznawczej funkcji jest oddzielnym i dodatkowym wymaganiem. Podgląd funkcji oceny poprawek na żądanie można [](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) włączyć zgodnie z opisanym wcześniej procesem włączania wersji zapoznawczej automatycznego poprawiania gościa maszyny wirtualnej.

> [!NOTE]
>Ocena poprawek na żądanie nie wyzwala automatycznie instalacji poprawek. Jeśli włączono automatyczne stosowanie poprawek gościa maszyny wirtualnej, ocenione i odpowiednie poprawki dla maszyny wirtualnej zostaną zainstalowane poza godzinami szczytu maszyny wirtualnej zgodnie z procesem poprawiania po raz pierwszy dostępności opisanym wcześniej w tym dokumencie.

### <a name="rest-api"></a>Interfejs API REST
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Invoke-AzVmPatchAssessment,](/powershell/module/az.compute/invoke-azvmpatchassessment) aby ocenić dostępne poprawki dla maszyny wirtualnej.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Użyj [az vm assess-patchs,](/cli/azure/vm#az_vm_assess_patches) aby ocenić dostępne poprawki dla maszyny wirtualnej.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat tworzenia maszyn wirtualnych z systemem Windows i zarządzania nimi](./windows/tutorial-manage-vm.md)
