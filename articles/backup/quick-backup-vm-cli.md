---
title: Szybki Start — tworzenie kopii zapasowej maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku szybki start dowiesz się, jak utworzyć magazyn Recovery Services, włączyć ochronę na maszynie wirtualnej i utworzyć początkowy punkt odzyskiwania przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 92990df3049f7fa1074d55fc34734e13d6673cd7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91328823"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>Tworzenie kopii zapasowej maszyny wirtualnej na platformie Azure za pomocą interfejsu wiersza polecenia

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Możesz chronić swoje dane, tworząc kopie zapasowe w regularnych odstępach czasu. Usługa Azure Backup tworzy punkty odzyskiwania, które można przechowywać w geograficznie nadmiarowych magazynach odzyskiwania. Ten artykuł szczegółowo opisuje sposób tworzenia kopii zapasowej maszyny wirtualnej na platformie Azure za pomocą interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu programu [Azure PowerShell](quick-backup-vm-powershell.md) lub w witrynie [Azure Portal](quick-backup-vm-portal.md).

W tym przewodniku Szybki start opisano wykonywanie kopii zapasowej istniejącej maszyny wirtualnej platformy Azure. Jeśli musisz utworzyć maszynę wirtualną, możesz [utworzyć maszynę wirtualną za pomocą interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby zainstalować interfejs wiersza polecenia lokalnie i korzystać z niego, należy korzystać z interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Aby znaleźć wersję interfejsu wiersza polecenia, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn usługi Recovery Services jest kontenerem logicznym, który przechowuje dane kopii zapasowej dla każdego chronionego zasobu, takiego jak maszyny wirtualne platformy Azure. Gdy zadanie tworzenia kopii zapasowej chronionego zasobu zostaje uruchomione, tworzony jest punkt odzyskiwania w magazynie usługi Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Utwórz magazyn usługi Recovery Services za pomocą polecenia [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create). Określ taką samą grupę zasobów i lokalizację, jak w przypadku maszyny wirtualnej, która ma być chroniona. Jeśli został przez Ciebie użyty [Szybki start dla maszyny wirtualnej](../virtual-machines/linux/quick-create-cli.md), to masz utworzone:

- grupę zasobów o nazwie *myResourceGroup*,
- maszynę wirtualną o nazwie *myVM*,
- zasoby w lokalizacji *eastus*.

```azurecli-interactive
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

Domyślnie magazyn usługi Recovery Services jest ustawiony na magazyn geograficznie nadmiarowy. Magazyn Geograficznie nadmiarowy gwarantuje, że dane kopii zapasowej są replikowane do dodatkowego regionu świadczenia usługi Azure, który znajduje się setki kilometrów od regionu podstawowego. Jeśli należy zmodyfikować ustawienie nadmiarowości magazynu, użyj polecenia [AZ Backup Storage Backup-Properties Set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set) .

```azurecli
az backup vault backup-properties set \
    --name myRecoveryServicesVault  \
    --resource-group myResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

## <a name="enable-backup-for-an-azure-vm"></a>Włączanie tworzenia kopii zapasowej maszyny wirtualnej platformy Azure

Zasady ochrony należy utworzyć, aby określić, kiedy zadanie tworzenia kopii zapasowej ma być uruchamiane oraz jak długo mają być przechowywane punkty odzyskiwania. Domyślne zasady ochrony uruchamiają zadanie tworzenia kopii zapasowej codziennie i przechowują punkty odzyskiwania przez 30 dni. Możesz użyć domyślnych wartości zasad, aby zapewnić szybką ochronę maszyny wirtualnej. Aby włączyć ochronę kopii zapasowej dla maszyny wirtualnej, użyj polecenie [AZ Backup Protection Enable-for-VM](/cli/azure/backup/protection#az-backup-protection-enable-for-vm). Określ grupę zasobów i maszynę wirtualną, które chcesz chronić, a następnie zasady, które mają zostać użyte:

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
> Jeśli maszyna wirtualna nie znajduje się w tej samej grupie zasobów, co magazyn, wówczas Grupa zasobów odwołuje się do grupy zasobu, w której został utworzony magazyn. Zamiast nazwy maszyny wirtualnej podaj jej identyfikator, jak opisano poniżej.

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') \
    --policy-name DefaultPolicy
```

> [!IMPORTANT]
> Podczas korzystania z interfejsu wiersza polecenia w celu jednoczesnego włączenia tworzenia kopii zapasowych wielu maszyn wirtualnych upewnij się, że z jednymi zasadami nie są skojarzone więcej niż 100 maszyn wirtualnych. Jest to [zalecane najlepsze rozwiązanie](./backup-azure-vm-backup-faq.md#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy). Obecnie klient programu PowerShell nie jest jawnie blokowany, jeśli istnieje więcej niż 100 maszyn wirtualnych, ale w przyszłości sprawdzanie jest planowane.

## <a name="start-a-backup-job"></a>Uruchamianie zadania tworzenia kopii zapasowej

Aby uruchomić tworzenie kopii zapasowej od razu, zamiast czekać, aż zasady domyślne uruchomią zadanie w zaplanowanym czasie, użyj polecenia [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now). Pierwsze zadanie tworzenia kopii zapasowej tworzy punkt pełnego odzyskiwania. Każde zadanie tworzenia kopii zapasowej uruchomione po początkowej kopii zapasowej tworzy przyrostowe punkty odzyskiwania. Przyrostowe punkty odzyskiwania są oszczędne pod względem czasu i miejsca w magazynie, ponieważ przesyłają wyłącznie zmiany wprowadzone od czasu ostatniej kopii zapasowej.

Do utworzenia kopii zapasowej maszyny wirtualnej używa się następujących parametrów:

- `--container-name` to nazwa maszyny wirtualnej
- `--item-name` to nazwa maszyny wirtualnej
- `--retain-until` — ta wartość powinna być ustawiona na ostatni dostępny termin, w formacie czasu UTC (**dd-mm-rrrr**), do którego punkt odzyskiwania ma być dostępny

W poniższym przykładzie tworzona jest kopia zapasowa maszyny wirtualnej o nazwie *myVM*, a termin wygaśnięcia punktu odzyskiwania jest ustawiony na 18 października 2017 r.:

```azurecli-interactive
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```

## <a name="monitor-the-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

Aby monitorować stan zadania tworzenia kopii zapasowej, użyj polecenia [az backup job list](/cli/azure/backup/job#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Dane wyjściowe są podobne do następującego przykładu informującego, że zadanie tworzenia kopii zapasowej jest w toku (*InProgress*):

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Gdy *stan* wskaże, że zadanie tworzenia kopii zapasowej jest ukończone (*Completed*), maszyna wirtualna jest chroniona za pomocą usługi Recovery Services i przechowywany jest punkt pełnego odzyskiwania.

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Gdy ochrona maszyny wirtualnej nie jest już potrzebna, można ją wyłączyć i usunąć punkty przywracania oraz magazyn usługi Recovery Services, a następnie usunąć grupę zasobów i powiązane zasoby maszyny wirtualnej. Jeśli używasz istniejącej maszyny wirtualnej, możesz pominąć końcowe polecenie [az group delete](/cli/azure/group#az-group-delete), aby pozostawić grupę zasobów i maszynę wirtualną na miejscu.

Jeśli chcesz wypróbować samouczek tworzenia kopii zapasowych, który objaśnia, jak przywrócić dane dla maszyny wirtualnej, przejdź do sekcji [Następne kroki](#next-steps).

```azurecli-interactive
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn usługi Recovery Services, włączono ochronę maszyny wirtualnej i utworzono początkowy punkt odzyskiwania. Aby dowiedzieć się więcej na temat usług Azure Backup i Recovery Services, przejdź do samouczków.

> [!div class="nextstepaction"]
> [Tworzenie kopii zapasowych wielu maszyn wirtualnych platformy Azure](./tutorial-backup-vm-at-scale.md)
