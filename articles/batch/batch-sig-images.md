---
title: Używanie galerii obrazów udostępnionych do tworzenia niestandardowej puli obrazów
description: Pule obrazów niestandardowych są wydajnym sposobem konfigurowania węzłów obliczeniowych do uruchamiania obciążeń wsadowych.
ms.topic: conceptual
ms.date: 11/18/2020
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: eb21a9e0d355274142e34fbb5c90a4d293c88ef1
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327308"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-image-pool"></a>Używanie galerii obrazów udostępnionych do tworzenia niestandardowej puli obrazów

Podczas tworzenia puli Azure Batch przy użyciu konfiguracji maszyny wirtualnej należy określić obraz maszyny wirtualnej, który dostarcza system operacyjny dla każdego węzła obliczeniowego w puli. Pulę maszyn wirtualnych można utworzyć za pomocą obsługiwanego obrazu witryny Azure Marketplace lub utworzyć obraz niestandardowy z [obrazem udostępnionej galerii obrazów](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Zalety galerii obrazów udostępnionych

Gdy używasz galerii obrazów udostępnionych dla niestandardowego obrazu, masz kontrolę nad typem i konfiguracją systemu operacyjnego, a także typem dysków danych. Udostępniony obraz może zawierać aplikacje i dane referencyjne, które stają się dostępne we wszystkich węzłach puli partii, gdy tylko zostaną zainicjowane.

Istnieje również możliwość użycia wielu wersji obrazu w zależności od potrzeb danego środowiska. W przypadku tworzenia maszyny wirtualnej przy użyciu wersji obrazu, wersja obrazu służy do tworzenia nowych dysków dla maszyny wirtualnej.

Za pomocą udostępnionego obrazu można zaoszczędzić czas podczas przygotowywania węzłów obliczeniowych puli do uruchamiania obciążenia usługi Batch. Istnieje możliwość użycia obrazu portalu Azure Marketplace i zainstalowania oprogramowania w każdym węźle obliczeniowym po zainicjowaniu obsługi, ale użycie obrazu udostępnionego jest zwykle bardziej wydajne. Dodatkowo można określić wiele replik dla obrazu udostępnionego, tak aby podczas tworzenia pul z wieloma maszynami wirtualnymi (więcej niż 600 maszyn wirtualnych) zaoszczędzić czas podczas tworzenia puli.

Używanie udostępnionego obrazu skonfigurowanego dla danego scenariusza może zapewnić kilka korzyści:

- **Używaj tych samych obrazów w różnych regionach.** Można utworzyć udostępnione repliki obrazów w różnych regionach, aby wszystkie pule używały tego samego obrazu.
- **Skonfiguruj system operacyjny (OS).** Można dostosować konfigurację dysku systemu operacyjnego obrazu.
- **Aplikacje przed instalacją.** Wstępne Instalowanie aplikacji na dysku systemu operacyjnego jest wydajniejsze i mniej podatne na błędy niż instalowanie aplikacji po zainicjowaniu obsługi administracyjnej węzłów obliczeniowych za pomocą zadania podrzędnego.
- **Kopiuj duże ilości danych raz.** Utwórz statyczny element danych w zarządzanym obrazie udostępnionym, kopiując go do dysków danych w zarządzanym obrazie. Należy to zrobić tylko raz i udostępnić dane dla każdego węzła puli.
- **Zwiększaj pule do większych rozmiarów.** Za pomocą galerii obrazów udostępnionych można tworzyć większe pule przy użyciu dostosowanych obrazów wraz z bardziej udostępnionymi replikami obrazu.
- **Lepsza wydajność niż użycie tylko obrazu zarządzanego jako obrazu niestandardowego.** W przypadku niestandardowej puli obrazów dla udostępnionego obrazu czas osiągnięcia stanu stałego jest szybszy do 25%, a opóźnienie bezczynności maszyny wirtualnej jest krótsze niż 30%.
- **Przechowywanie wersji obrazów i grupowanie w celu łatwiejszego zarządzania.** Definicja grupowania obrazów zawiera informacje na temat przyczyny utworzenia obrazu, jego systemu operacyjnego oraz informacji o korzystaniu z obrazu. Grupowanie obrazów umożliwia łatwiejsze zarządzanie obrazami. Aby uzyskać więcej informacji, zobacz [definicje obrazu](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Musisz uwierzytelnić się przy użyciu usługi Azure AD. W przypadku korzystania z uwierzytelniania za pomocą klucza Shared-Key zostanie wyświetlony komunikat o błędzie uwierzytelniania.  

- **Konto usługi Azure Batch.** Aby utworzyć konto usługi Batch, zobacz Przewodnik Szybki Start w usłudze Batch przy użyciu [Azure Portal](quick-create-portal.md) lub [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md).

- **Obraz udostępnionej galerii obrazów**. Aby utworzyć obraz udostępniony, musisz mieć lub utworzyć zasób obrazu zarządzanego. Obraz należy utworzyć na podstawie migawek dysku systemu operacyjnego maszyny wirtualnej i opcjonalnie dołączonych dysków danych.

> [!NOTE]
> Jeśli udostępniony obraz nie znajduje się w tej samej subskrypcji co konto usługi Batch, należy [zarejestrować dostawcę zasobów Microsoft.Batch](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) dla tej subskrypcji. Dwie subskrypcje muszą znajdować się w tej samej dzierżawie usługi Azure AD.
>
> Obraz może znajdować się w innym regionie, o ile ma repliki w tym samym regionie, co konto w usłudze Batch.

Jeśli używasz aplikacji usługi Azure AD do tworzenia niestandardowej puli obrazów przy użyciu obrazu z galerii obrazów udostępnionych, ta aplikacja musi mieć przydzieloną [wbudowaną rolę platformy Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) , która zapewnia dostęp do udostępnionego obrazu. Ten dostęp można udzielić w Azure Portal, przechodząc do udostępnionego obrazu, wybierając pozycję **Kontrola dostępu (IAM)** i dodając przypisanie roli do aplikacji.

## <a name="prepare-a-shared-image"></a>Przygotowywanie udostępnionego obrazu

Na platformie Azure można przygotować udostępniony obraz z zarządzanego obrazu, który można utworzyć na podstawie:

- Migawki systemu operacyjnego i dysków danych maszyny wirtualnej platformy Azure
- Uogólniona maszyna wirtualna platformy Azure z dyskami zarządzanymi
- Uogólniony, lokalny wirtualny dysk twardy przekazany do chmury

> [!NOTE]
> Partia obsługuje tylko uogólnione obrazy udostępnione; nie można użyć specjalnego obrazu udostępnionego do utworzenia puli.

Poniższe kroki pokazują, jak przygotować maszynę wirtualną, wykonać migawkę i utworzyć obraz na podstawie migawki.

### <a name="prepare-a-vm"></a>Przygotowywanie maszyny wirtualnej

Jeśli tworzysz nową maszynę wirtualną dla obrazu, Użyj obrazu z witryny Azure Marketplace w pierwszej kolejności jako obrazu podstawowego dla zarządzanego obrazu. Jako obrazu podstawowego można używać tylko obrazów pierwszej strony. Aby uzyskać pełną listę odwołań do obrazów w portalu Azure Marketplace obsługiwanych przez Azure Batch, zobacz część operacji [jednostek SKU agenta węzła listy](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) .

> [!NOTE]
> Nie można użyć obrazu innej firmy, który ma dodatkową licencję i warunki zakupu jako obraz podstawowy. Aby uzyskać informacje na temat tych obrazów z portalu Marketplace, zobacz Wskazówki dotyczące maszyn wirtualnych z systemem [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) lub [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) .

- Upewnij się, że maszyna wirtualna została utworzona przy użyciu dysku zarządzanego. Jest to domyślne ustawienie magazynu podczas tworzenia maszyny wirtualnej.
- Na maszynie wirtualnej nie należy instalować rozszerzeń platformy Azure, takich jak rozszerzenie niestandardowego skryptu. Jeśli obraz zawiera wstępnie zainstalowane rozszerzenie, platforma Azure może napotkać problemy podczas wdrażania puli usługi Batch.
- W przypadku korzystania z dołączonych dysków danych należy zainstalować i sformatować dyski z poziomu maszyny wirtualnej w celu ich użycia.
- Upewnij się, że określony obraz podstawowego systemu operacyjnego używa domyślnego dysku tymczasowego. Agent węzła wsadowego aktualnie oczekuje domyślnego dysku tymczasowego.
- Upewnij się, że dysk systemu operacyjnego nie jest szyfrowany.
- Gdy maszyna wirtualna jest uruchomiona, nawiąż połączenie z nią za pośrednictwem protokołu RDP (dla systemu Windows) lub SSH (system Linux). Zainstaluj wymagane oprogramowanie lub skopiuj wymagane dane.  

### <a name="create-a-vm-snapshot"></a>Tworzenie migawki maszyny wirtualnej

Migawka to pełna kopia tylko do odczytu dysku VHD. Aby utworzyć migawkę systemu operacyjnego lub dysków danych maszyny wirtualnej, można użyć Azure Portal lub narzędzi wiersza polecenia. Aby zapoznać się z krokami i opcjami tworzenia migawki, zobacz Wskazówki dotyczące maszyn wirtualnych z systemem [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) lub [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) .

### <a name="create-an-image-from-one-or-more-snapshots"></a>Tworzenie obrazu z jednej lub kilku migawek

Aby utworzyć obraz zarządzany na podstawie migawki, użyj narzędzi wiersza polecenia platformy Azure, takich jak polecenie [AZ Image Create](/cli/azure/image) . Utwórz obraz, określając migawkę dysku systemu operacyjnego i opcjonalnie co najmniej jedną migawkę dysku danych.

### <a name="create-a-shared-image-gallery"></a>Tworzenie galerii obrazów udostępnionych

Po pomyślnym utworzeniu zarządzanego obrazu musisz utworzyć udostępnioną galerię obrazów, aby udostępnić niestandardowy obraz. Aby dowiedzieć się, jak utworzyć udostępnioną galerię obrazów dla obrazów, zobacz [Tworzenie udostępnionej galerii obrazów za pomocą interfejsu wiersza polecenia platformy Azure](../virtual-machines/shared-images-cli.md) lub [Tworzenie galerii obrazów udostępnionych przy użyciu Azure Portal](../virtual-machines/linux/shared-images-portal.md).

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Tworzenie puli na podstawie udostępnionego obrazu przy użyciu interfejsu wiersza polecenia platformy Azure

Aby utworzyć pulę na podstawie udostępnionego obrazu przy użyciu interfejsu wiersza polecenia platformy Azure, użyj `az batch pool create` polecenie. Określ identyfikator obrazu udostępnionego w `--image` polu. Upewnij się, że typ systemu operacyjnego i jednostka SKU są zgodne z wersjami określonymi przez `--node-agent-sku-id`

> [!NOTE]
> Musisz uwierzytelnić się przy użyciu usługi Azure AD. W przypadku korzystania z uwierzytelniania za pomocą klucza Shared-Key zostanie wyświetlony komunikat o błędzie uwierzytelniania.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Tworzenie puli na podstawie udostępnionego obrazu przy użyciu języka C #

Alternatywnie można utworzyć pulę z udostępnionego obrazu przy użyciu zestawu C# SDK.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-python"></a>Tworzenie puli na podstawie udostępnionego obrazu przy użyciu języka Python

Można również utworzyć pulę z udostępnionego obrazu przy użyciu zestawu SDK języka Python: 

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch as batch
import azure.batch.models as batchmodels
from azure.common.credentials import ServicePrincipalCredentials

# Specify Batch account and service principal account credentials
account = "{batch-account-name}"
batch_url = "{batch-account-url}"
ad_client_id = "{sp-client-id}"
ad_tenant = "{tenant-id}"
ad_secret = "{sp-secret}"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client with Azure AD authentication
creds = ServicePrincipalCredentials(
    client_id=ad_client_id,
    secret=ad_secret,
    tenant=ad_tenant,
    resource="https://batch.core.windows.net/"
)
client = batch.BatchServiceClient(creds, batch_url)

# Configure the start task for the pool
start_task = batchmodels.StartTask(
    command_line="printenv AZ_BATCH_NODE_STARTUP_DIR"
)
start_task.run_elevated = True

# Create an ImageReference which specifies the image from
# Shared Image Gallery to install on the nodes.
ir = batchmodels.ImageReference(
    virtual_machine_image_id="/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}"
)

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04"
)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(
    id=pool_id,
    vm_size=vm_size,
    target_dedicated_nodes=node_count,
    virtual_machine_configuration=vmc,
    start_task=start_task
)

# Create pool in the Batch service
client.pool.add(new_pool)
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Tworzenie puli na podstawie udostępnionego obrazu przy użyciu Azure Portal

Wykonaj następujące kroki, aby utworzyć pulę z udostępnionego obrazu w Azure Portal.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Przejdź do pozycji **konta wsadowe** i wybierz swoje konto.
1. Wybierz pozycję **Pule** , a następnie **Dodaj** , aby utworzyć nową pulę.
1. W sekcji **Typ obrazu** wybierz pozycję **Galeria obrazów udostępnionych**.
1. Wypełnij pozostałe sekcje informacjami o zarządzanym obrazie.
1. Wybierz przycisk **OK**.

![Utwórz pulę przy użyciu udostępnionego obrazu z portalem.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Zagadnienia dotyczące dużych pul

Jeśli planujesz utworzenie puli z setkami lub tysiącami maszyn wirtualnych lub więcej przy użyciu udostępnionego obrazu, Skorzystaj z poniższych wskazówek.

- **Numery replik galerii obrazów udostępnionych.**  W przypadku każdej puli z maksymalnie 300 wystąpieniami zalecamy zachowanie co najmniej jednej repliki. Na przykład, jeśli tworzysz pulę z 3000 maszyn wirtualnych, należy pozostawić co najmniej 10 replik obrazu. Zawsze sugerujemy przechowywanie większej liczby replik niż minimalne wymagania w celu uzyskania lepszej wydajności.

- **Limit czasu zmiany rozmiaru.** Jeśli pula zawiera stałą liczbę węzłów (jeśli nie ma automatycznego skalowania), zwiększ `resizeTimeout` Właściwość puli w zależności od rozmiaru puli. Dla każdego 1000 maszyn wirtualnych zalecany limit rozmiaru wynosi co najmniej 15 minut. Na przykład zalecany limit rozmiaru dla puli z maszynami wirtualnymi 2000 wynosi co najmniej 30 minut.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z szczegółowym omówieniem usługi Batch, zobacz temat [przepływ pracy i zasoby usług Batch](batch-service-workflow-features.md).
- Dowiedz się więcej o [galerii obrazów udostępnionych](../virtual-machines/windows/shared-image-galleries.md).
