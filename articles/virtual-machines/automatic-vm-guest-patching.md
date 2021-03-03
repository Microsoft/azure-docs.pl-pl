---
title: Automatyczna poprawka gościa maszyny wirtualnej dla maszyn wirtualnych platformy Azure
description: Dowiedz się, jak automatycznie poprawiać maszyny wirtualne na platformie Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 276762bc2b8624f687cbb77e1af771478791a57b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680316"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>Wersja zapoznawcza: automatyczna poprawka gościa maszyny wirtualnej dla maszyn wirtualnych platformy Azure

Włączenie automatycznej poprawki gościa maszyny wirtualnej dla maszyn wirtualnych platformy Azure ułatwia zarządzanie aktualizacjami przez bezpieczne i automatyczne poprawianie maszyn wirtualnych w celu zachowania zgodności z zabezpieczeniami.

Automatyczna poprawka gościa maszyny wirtualnej ma następującą charakterystykę:
- Poprawki sklasyfikowane jako *krytyczne* lub *zabezpieczenia* są automatycznie pobierane i stosowane na maszynie wirtualnej.
- Poprawki są stosowane w godzinach poza godzinami szczytu w strefie czasowej maszyny wirtualnej.
- Aranżacja poprawek jest zarządzana przez platformę Azure, a poprawki są stosowane po [pierwszej zasadzie dostępności](#availability-first-patching).
- Kondycja maszyny wirtualnej określona za pośrednictwem sygnałów kondycji platformy jest monitorowana w celu wykrywania niepowodzeń poprawek.
- Działa w przypadku wszystkich rozmiarów maszyn wirtualnych.

> [!IMPORTANT]
> Automatyczne stosowanie poprawek gościa maszyny wirtualnej jest obecnie w publicznej wersji zapoznawczej. Aby korzystać z funkcji publicznej wersji zapoznawczej opisanej poniżej, wymagana jest procedura zgody.
> Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Jak działa automatyczna poprawka gościa maszyny wirtualnej?

Jeśli na maszynie wirtualnej jest włączona automatyczna poprawka gościa maszyny wirtualnej, dostępne poprawki *krytyczne* i *zabezpieczeń* są pobierane i stosowane automatycznie na maszynie wirtualnej. Ten proces jest automatycznie rozpoczynany w każdym miesiącu, gdy zostaną wydane nowe poprawki. Ocena poprawek i instalacja jest automatyczna, a proces obejmuje ponowne uruchomienie maszyny wirtualnej zgodnie z wymaganiami.

Maszyna wirtualna jest oceniana okresowo co kilka dni i wiele razy w ciągu 30-dniowego okresu, aby określić odpowiednie poprawki dla tej maszyny wirtualnej. Poprawki można instalować dowolnego dnia na maszynie wirtualnej poza godzinami szczytu maszyny wirtualnej. Ta automatyczna ocena gwarantuje, że wszystkie brakujące poprawki zostaną odnalezione w najwcześniejszym możliwym momencie.

Poprawki są instalowane w ciągu 30 dni od comiesięcznych wersji poprawek, które opisano poniżej. Poprawki są instalowane tylko poza godzinami szczytu maszyny wirtualnej, w zależności od strefy czasowej maszyny wirtualnej. Maszyna wirtualna musi być uruchomiona w godzinach poza godzinami szczytu, aby poprawki były instalowane automatycznie. Jeśli maszyna wirtualna jest wyłączona podczas okresowej oceny, maszyna wirtualna zostanie automatycznie oceniona, a odpowiednie poprawki zostaną automatycznie zainstalowane podczas kolejnej oceny okresowej (zwykle w ciągu kilku dni), gdy maszyna wirtualna jest włączona.

Aktualizacje definicji i inne poprawki niesklasyfikowane jako *krytyczne* lub *zabezpieczenia* nie zostaną zainstalowane za poorednictwem automatycznej poprawki gościa maszyny wirtualnej. Aby zainstalować poprawki z innymi klasyfikacjami poprawek lub zaplanować instalację poprawki w ramach własnego niestandardowego okna obsługi, można użyć [Update Management](./windows/tutorial-config-management.md#manage-windows-updates).

### <a name="availability-first-patching"></a>Dostępność — pierwsza poprawka

Proces instalacji poprawek jest zorganizowany globalnie przez platformę Azure dla wszystkich maszyn wirtualnych, na których włączono automatyczne stosowanie poprawek gościa maszyny wirtualnej. Ta aranżacja podlega zasadom pierwszej dostępności na różnych poziomach dostępności udostępnianych przez platformę Azure.

W przypadku grupy maszyn wirtualnych z aktualizacją platforma Azure będzie organizować aktualizacje:

**Między regionami:**
- Comiesięczna aktualizacja jest w fazie organizowania na platformie Azure globalnie, aby zapobiec występowaniu błędów wdrażania globalnego.
- Faza może mieć jeden lub więcej regionów, a aktualizacja przechodzi do następnej fazy tylko w przypadku pomyślnej aktualizacji odpowiednich maszyn wirtualnych w fazie.
- Regiony z parowaniem geograficznym nie są aktualizowane współbieżnie i nie mogą znajdować się w tej samej fazie regionalnej.
- Powodzenie aktualizacji jest mierzone przez śledzenie aktualizacji kondycji maszyny wirtualnej. Kondycja maszyny wirtualnej jest śledzona przez wskaźniki kondycji platformy dla maszyny wirtualnej.

**W obrębie regionu:**
- Maszyny wirtualne w różnych Strefy dostępności nie są aktualizowane współbieżnie.
- Maszyny wirtualne niebędące częścią zestawu dostępności są przetwarzane wsadowo w celu uniknięcia współbieżnych aktualizacji dla wszystkich maszyn wirtualnych w ramach subskrypcji.

**W zestawie dostępności:**
- Wszystkie maszyny wirtualne w wspólnym zestawie dostępności nie są aktualizowane współbieżnie.
-   Maszyny wirtualne w ramach wspólnego zestawu dostępności są aktualizowane w ramach granic domeny aktualizacji i maszyny wirtualne w wielu domenach aktualizacji nie są aktualizowane współbieżnie.

Data instalacji poprawki dla danej maszyny wirtualnej może się różnić od miesiąca do miesiąca, ponieważ konkretna maszyna wirtualna może zostać pobrana w innej partii między miesięcznymi cyklami poprawek.

### <a name="which-patches-are-installed"></a>Które poprawki są zainstalowane?
Zainstalowane poprawki zależą od etapu wdrożenia maszyny wirtualnej. Co miesiąc nowe globalne wdrożenie jest uruchamiane w przypadku, gdy wszystkie poprawki zabezpieczeń i krytyczne są oceniane dla pojedynczej maszyny wirtualnej są zainstalowane dla tej maszyny wirtualnej. Wdrożenie jest zorganizowane między wszystkimi regionami świadczenia usługi Azure w partiach (opisanymi w powyższej sekcji dostępność — pierwsza poprawka).

Dokładny zestaw poprawek do zainstalowania zależy od konfiguracji maszyny wirtualnej, w tym typu systemu operacyjnego, oraz czasu oceny. W przypadku dwóch identycznych maszyn wirtualnych w różnych regionach można zainstalować różne poprawki, jeśli podczas organizowania poprawek w różnych regionach jest dostępnych więcej lub mniej poprawek. Podobnie, ale rzadziej, maszyny wirtualne w tym samym regionie, ale oceniane w różnym czasie (z powodu różnych stref dostępności lub partii zestawu dostępności) mogą uzyskać różne poprawki.

Ponieważ automatyczna poprawka gościa maszyny wirtualnej nie konfiguruje źródła poprawek, dwie podobne maszyny wirtualne skonfigurowane do różnych źródeł poprawek, takie jak repozytorium publiczne i repozytorium prywatne, mogą również zobaczyć różnicę w dokładnie zainstalowanym zestawie poprawek.

W przypadku typów systemu operacyjnego, które uwalniają poprawki na erze, maszyny wirtualne skonfigurowane do repozytorium publicznego dla systemu operacyjnego mogą oczekiwać, że ten sam zestaw poprawek jest w różnych fazach wdrażania w danym miesiącu. Na przykład maszyny wirtualne z systemem Windows są skonfigurowane dla publicznego repozytorium Windows Update.

W miarę jak nowe wdrożenie jest wyzwalane co miesiąc, maszyna wirtualna będzie odbierać co najmniej jedno wdrożenie poprawek co miesiąc, jeśli maszyna wirtualna jest włączona w godzinach poza godzinami szczytu. Pozwala to zagwarantować, że na maszynie wirtualnej jest instalowanych co miesiąc najnowszych dostępnych poprawek zabezpieczeń i krytycznych. Aby zapewnić spójność zestawu poprawek, można skonfigurować maszyny wirtualne do oceny i pobierania poprawek z własnych repozytoriów prywatnych.

## <a name="supported-os-images"></a>Obsługiwane obrazy systemu operacyjnego
W wersji zapoznawczej obecnie są obsługiwane tylko maszyny wirtualne utworzone na podstawie niektórych obrazów platformy systemu operacyjnego. Obrazy niestandardowe nie są obecnie obsługiwane w wersji zapoznawczej.

Następujące jednostki SKU platformy są obecnie obsługiwane (i więcej jest dodawanych okresowo):

| Publisher               | Oferta systemu operacyjnego      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18,04 – LTS |
| Redhat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016 — centrum danych    |
| MicrosoftWindowsServer  | WindowsServer | 2016 — Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019 — centrum danych |
| MicrosoftWindowsServer  | WindowsServer | 2019 — centrum danych — rdzeń |

## <a name="patch-orchestration-modes"></a>Tryby aranżacji poprawek
Maszyny wirtualne na platformie Azure obsługują teraz następujące tryby aranżacji poprawek:

**AutomaticByPlatform:**
- Ten tryb jest obsługiwany zarówno dla maszyn wirtualnych z systemem Linux, jak i Windows.
- Ten tryb włącza automatyczną poprawkę gościa maszyny wirtualnej, a kolejna Instalacja poprawek jest aranżacja przez platformę Azure.
- Ten tryb jest wymagany w przypadku stosowania poprawek po pierwszej dostępności.
- Ten tryb jest obsługiwany tylko w przypadku maszyn wirtualnych, które są tworzone za pomocą powyższych obrazów platformy obsługiwanej przez system operacyjny.
- W przypadku maszyn wirtualnych z systemem Windows ustawienie tego trybu wyłącza również natywne aktualizacje automatyczne na maszynie wirtualnej z systemem Windows, aby uniknąć duplikacji.
- Aby użyć tego trybu na maszynach wirtualnych z systemem Linux, należy ustawić właściwość `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` w szablonie maszyny wirtualnej.
- Aby użyć tego trybu na maszynach wirtualnych z systemem Windows, należy ustawić właściwość `osProfile.windowsConfiguration.enableAutomaticUpdates=true` i ustawić właściwość  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` w szablonie maszyny wirtualnej.

**AutomaticByOS:**
- Ten tryb jest obsługiwany tylko w przypadku maszyn wirtualnych z systemem Windows.
- Ten tryb włącza aktualizacje automatyczne na maszynie wirtualnej z systemem Windows, a poprawki są instalowane na maszynie wirtualnej za pomocą funkcji Aktualizacje automatyczne.
- Ten tryb nie obsługuje stosowania poprawek po pierwszej dostępności.
- Ten tryb jest ustawiany domyślnie, jeśli nie określono innego trybu poprawek dla maszyny wirtualnej z systemem Windows.
- Aby użyć tego trybu na maszynach wirtualnych z systemem Windows, należy ustawić właściwość `osProfile.windowsConfiguration.enableAutomaticUpdates=true` i ustawić właściwość  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` w szablonie maszyny wirtualnej.

**Ręcznie:**
- Ten tryb jest obsługiwany tylko w przypadku maszyn wirtualnych z systemem Windows.
- Ten tryb wyłącza aktualizacje automatyczne na maszynie wirtualnej z systemem Windows.
- Ten tryb nie obsługuje stosowania poprawek po pierwszej dostępności.
- Ten tryb należy ustawić w przypadku używania niestandardowych rozwiązań poprawek.
- Aby użyć tego trybu na maszynach wirtualnych z systemem Windows, należy ustawić właściwość `osProfile.windowsConfiguration.enableAutomaticUpdates=false` i ustawić właściwość  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` w szablonie maszyny wirtualnej.

**ImageDefault:**
- Ten tryb jest obsługiwany tylko w przypadku maszyn wirtualnych z systemem Linux.
- Ten tryb nie obsługuje stosowania poprawek po pierwszej dostępności.
- Ten tryb uwzględnia domyślną konfigurację poprawek w obrazie użytym do utworzenia maszyny wirtualnej.
- Ten tryb jest ustawiany domyślnie, jeśli nie określono innego trybu poprawek dla maszyny wirtualnej z systemem Linux.
- Aby użyć tego trybu na maszynach wirtualnych z systemem Linux, należy ustawić właściwość `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` w szablonie maszyny wirtualnej.

> [!NOTE]
>W przypadku maszyn wirtualnych z systemem Windows Właściwość `osProfile.windowsConfiguration.enableAutomaticUpdates` można obecnie ustawić tylko wtedy, gdy maszyna wirtualna jest najpierw tworzona. Przełączenie z ręcznego do trybu automatycznego lub z dowolnego trybu automatycznego do trybu ręcznego nie jest obecnie obsługiwane. Przełączenie z trybu AutomaticByOS do trybu AutomaticByPlatfom jest obsługiwane.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Wymagania dotyczące włączania automatycznej poprawki gościa maszyny wirtualnej

- Na maszynie wirtualnej musi być zainstalowany agent maszyny wirtualnej platformy Azure dla [systemu Windows](./extensions/agent-windows.md) lub [Linux](./extensions/agent-linux.md) .
- W przypadku maszyn wirtualnych z systemem Linux Agent systemu Azure Linux musi mieć wersję 2.2.53.1 lub nowszą. [Zaktualizuj agenta systemu Linux](./extensions/update-linux-agent.md) , jeśli bieżąca wersja jest starsza niż wymagana wersja.
- W przypadku maszyn wirtualnych z systemem Windows na maszynie wirtualnej musi być uruchomiona usługa Windows Update.
- Maszyna wirtualna musi mieć możliwość dostępu do skonfigurowanych punktów końcowych aktualizacji. Jeśli maszyna wirtualna jest skonfigurowana do korzystania z prywatnych repozytoriów dla maszyn wirtualnych z systemem Linux lub Windows Server Update Services (WSUS), odpowiednie punkty końcowe aktualizacji muszą być dostępne.
- Użyj interfejsu API obliczeń w wersji 2020-12-01 lub nowszej. Interfejsu API obliczeń w wersji 2020-06-01 można używać w przypadku maszyn wirtualnych z systemem Windows z ograniczoną funkcjonalnością.

Włączenie funkcji wersji zapoznawczej wymaga jednorazowej zgody na funkcje **InGuestAutoPatchVMPreview** i **InGuestPatchVMPreview** na subskrypcję, zgodnie z opisem w poniższej sekcji.

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
Po zarejestrowaniu tej funkcji dla subskrypcji Ukończ proces akceptacji, propagowanie zmiany do dostawcy zasobów obliczeniowych.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Aby włączyć podgląd subskrypcji, użyj polecenia cmdlet [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Po zarejestrowaniu tej funkcji dla subskrypcji Ukończ proces akceptacji, propagowanie zmiany do dostawcy zasobów obliczeniowych.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [AZ Feature Register](/cli/azure/feature#az-feature-register) , aby włączyć podgląd dla Twojej subskrypcji.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Po zarejestrowaniu tej funkcji dla subskrypcji Ukończ proces akceptacji, propagowanie zmiany do dostawcy zasobów obliczeniowych.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Włączanie automatycznego stosowania poprawek gościa maszyny wirtualnej
Aby włączyć automatyczną poprawkę gościa maszyny wirtualnej na maszynie wirtualnej z systemem Windows, upewnij się, że właściwość *osProfile. windowsConfiguration. enableAutomaticUpdates* ma *wartość true* w definicji szablonu maszyny wirtualnej. Tę właściwość można ustawić tylko podczas tworzenia maszyny wirtualnej. Ta dodatkowa właściwość nie ma zastosowania w przypadku maszyn wirtualnych z systemem Linux.

### <a name="rest-api-for-linux-vms"></a>Interfejs API REST dla maszyn wirtualnych z systemem Linux
W poniższym przykładzie opisano sposób włączania automatycznej poprawki gościa maszyny wirtualnej:

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
W poniższym przykładzie opisano sposób włączania automatycznej poprawki gościa maszyny wirtualnej:

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
Użyj polecenia cmdlet [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) , aby włączyć automatyczną poprawkę gościa maszyny wirtualnej podczas tworzenia lub aktualizowania maszyny wirtualnej.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Interfejs wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Windows
Użyj [AZ VM Create](/cli/azure/vm#az-vm-create) , aby włączyć automatyczną poprawkę gościa maszyny wirtualnej podczas tworzenia nowej maszyny wirtualnej. Poniższy przykład służy do konfigurowania automatycznej poprawki gościa maszyny wirtualnej dla maszyny wirtualnej o nazwie *myVM* w grupie zasobów o nazwie Moja *Grupa:*

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Aby zmodyfikować istniejącą maszynę wirtualną, użyj polecenie [AZ VM Update](/cli/azure/vm#az-vm-update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Włączanie i Ocena

> [!NOTE]
>Włączenie automatycznych aktualizacji gościa maszyny wirtualnej na maszynie wirtualnej może potrwać więcej niż trzy godziny, ponieważ Włączanie jest wykonywane w godzinach poza godzinami pracy maszyny wirtualnej. Ponieważ instalacja oceny i poprawki odbywa się tylko w godzinach poza godzinami szczytu, maszyna wirtualna musi być również uruchomiona poza godzinami szczytu, aby zastosować poprawki.

Gdy dla maszyny wirtualnej jest włączone automatyczne stosowanie poprawek gościa maszyny wirtualnej, rozszerzenie maszyny wirtualnej typu `Microsoft.CPlat.Core.LinuxPatchExtension` jest zainstalowane na maszynie wirtualnej z systemem Linux lub na maszynie wirtualnej z `Microsoft.CPlat.Core.WindowsPatchExtension` systemem Windows jest zainstalowane rozszerzenie maszyny wirtualnej typu. To rozszerzenie nie musi być ręcznie instalowane ani aktualizowane, ponieważ to rozszerzenie jest zarządzane przez platformę Azure w ramach procesu automatycznej poprawki gościa maszyny wirtualnej.

Włączenie automatycznych aktualizacji gościa maszyny wirtualnej na maszynie wirtualnej może potrwać więcej niż trzy godziny, ponieważ Włączanie jest wykonywane w godzinach poza godzinami pracy maszyny wirtualnej. Rozszerzenie jest również instalowane i aktualizowane poza godzinami szczytu maszyny wirtualnej. Jeśli zakończenie pracy w godzinach szczytu maszyny wirtualnej przed włączeniem będzie możliwe, proces włączania zostanie wznowiony podczas następnego dostępnego czasu poza godzinami szczytu.

Aktualizacje automatyczne są wyłączone w większości scenariuszy, a Instalacja poprawek odbywa się za pośrednictwem rozszerzenia. Obowiązują następujące warunki.
- Jeśli maszyna wirtualna z systemem Windows była wcześniej Windows Update włączona w trybie poprawek AutomaticByOS, Windows Update automatyczne jest wyłączone dla maszyny wirtualnej, gdy rozszerzenie zostanie zainstalowane.
- W przypadku maszyn wirtualnych Ubuntu domyślne aktualizacje automatyczne są automatycznie wyłączane po włączeniu automatycznej poprawki gościa maszyny wirtualnej.
- W przypadku programu RHEL aktualizacje automatyczne należy wyłączyć ręcznie (jest to ograniczenie wersji zapoznawczej). Wykonana

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Aby sprawdzić, czy automatyczna poprawka gościa maszyny wirtualnej została zakończona, a rozszerzenie poprawki jest zainstalowane na maszynie wirtualnej, można sprawdzić widok wystąpienia maszyny wirtualnej. Jeśli proces włączania zostanie ukończony, rozszerzenie zostanie zainstalowane, a wyniki oceny dla maszyny wirtualnej będą dostępne w ramach programu `patchStatus` . Dostęp do widoku wystąpienia maszyny wirtualnej można uzyskać za pomocą wielu sposobów opisanych poniżej.

### <a name="rest-api"></a>Interfejs API REST

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) z `-Status` parametrem, aby uzyskać dostęp do widoku wystąpienia dla maszyny wirtualnej.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

Program PowerShell obecnie zawiera tylko informacje o rozszerzeniu poprawki. Informacje o programie są `patchStatus` również dostępne wkrótce za poorednictwem programu PowerShell.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Użyj [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view) , aby uzyskać dostęp do widoku wystąpienia dla maszyny wirtualnej.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Informacje o stanie poprawek dla maszyny wirtualnej

`patchStatus`Sekcja odpowiedzi w widoku wystąpienia zawiera szczegółowe informacje dotyczące najnowszej oceny i ostatniej instalacji poprawki dla maszyny wirtualnej.

Wyniki oceny dla maszyny wirtualnej można przejrzeć pod `availablePatchSummary` sekcją. Okresowe oceny są wykonywane w przypadku maszyny wirtualnej z włączoną funkcją automatycznej poprawki gościa maszyny wirtualnej. Liczba dostępnych poprawek po ocenie znajduje się w obszarze `criticalAndSecurityPatchCount` i `otherPatchCount` wyniki. Automatyczna poprawka gościa maszyny wirtualnej spowoduje zainstalowanie wszystkich poprawek ocenionych w klasyfikacji poprawek *krytycznych* i *zabezpieczeń* . Każda inna Szacowana poprawka jest pomijana.

Wyniki instalacji poprawek dla maszyny wirtualnej można przejrzeć pod `lastPatchInstallationSummary` sekcją. Ta sekcja zawiera szczegółowe informacje dotyczące ostatniej próby instalacji poprawki na maszynie wirtualnej, w tym liczbę poprawek, które zostały zainstalowane, oczekują na awarię lub pominięto. Poprawki są instalowane tylko w oknie obsługi w godzinach poza szczytem dla maszyny wirtualnej. Poprawki oczekujące i nieudane zostaną automatycznie ponowione w następnym przedziale czasu (w godzinach).

## <a name="on-demand-patch-assessment"></a>Ocena poprawek na żądanie
Jeśli automatyczne stosowanie poprawek gościa maszyny wirtualnej jest już włączone dla maszyny wirtualnej, okresową ocenę poprawek przeprowadza się na maszynie wirtualnej w godzinach poza godzinami pracy maszyny wirtualnej. Ten proces jest automatyczny, a wyniki najnowszej oceny można przejrzeć w widoku wystąpienia maszyny wirtualnej zgodnie z opisem we wcześniejszej części tego dokumentu. Możesz również wyzwolić ocenę poprawek na żądanie dla maszyny wirtualnej w dowolnym momencie. Ocena poprawek może potrwać kilka minut, a stan najnowszej oceny zostanie zaktualizowany w widoku wystąpienia maszyny wirtualnej.

Włączenie funkcji w wersji zapoznawczej wymaga jednorazowej zgody na funkcję **InGuestPatchVMPreview** na subskrypcję. Ta wersja zapoznawcza różni się od automatycznej rejestracji funkcji automatycznego stosowania poprawek gościa maszyny wirtualnej w programie **InGuestAutoPatchVMPreview**. Włączenie dodatkowej funkcji w wersji zapoznawczej jest osobnym i dodatkowym wymaganiem. Wersję zapoznawczą funkcji oceny poprawek na żądanie można włączyć zgodnie z wcześniejszym [procesem włączania wersji zapoznawczej](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) na potrzeby automatycznej poprawki gościa maszyny wirtualnej.

> [!NOTE]
>Ocena poprawek na żądanie nie powoduje automatycznego wyzwolenia instalacji poprawki. Jeśli włączono automatyczną poprawkę gościa maszyny wirtualnej, oceniona i odpowiednia poprawka dla maszyny wirtualnej zostanie zainstalowana w godzinach poza godzinami pracy maszyny wirtualnej, zgodnie z procesem stosowania poprawek w pierwszej kolejności opisanej wcześniej w tym dokumencie.

### <a name="rest-api"></a>Interfejs API REST
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) , aby ocenić dostępne poprawki dla maszyny wirtualnej.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Użyj [AZ VM ocenić-poprawek](/cli/azure/vm#az-vm-assess-patches) do oceny dostępnych poprawek dla maszyny wirtualnej.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat tworzenia maszyn wirtualnych z systemem Windows i zarządzania nimi](./windows/tutorial-manage-vm.md)
