---
title: Zastosuj blokadę Azure Resource Manager do konta magazynu
titleSuffix: Azure Storage
description: Dowiedz się, jak zastosować blokadę Azure Resource Manager do konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 79b88ad58a2eb95a48a140b3b98d606af495cb94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799789"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>Zastosuj blokadę Azure Resource Manager do konta magazynu

Firma Microsoft zaleca zablokowanie wszystkich kont magazynu z blokadą Azure Resource Manager, aby zapobiec przypadkowemu lub złośliwemu usunięciu konta magazynu. Istnieją dwa typy blokad zasobów Azure Resource Manager:

- Blokada **CannotDelete** uniemożliwia użytkownikom usuwanie konta magazynu, ale umożliwia odczytywanie i modyfikowanie jego konfiguracji.
- Blokada **tylko do odczytu** uniemożliwia użytkownikom usuwanie konta magazynu lub modyfikowanie jego konfiguracji, ale umożliwia odczytywanie konfiguracji.

Aby uzyskać więcej informacji na temat blokad Azure Resource Manager, zobacz [blokowanie zasobów, aby zapobiec zmianom](../../azure-resource-manager/management/lock-resources.md).

> [!CAUTION]
> Zablokowanie konta magazynu nie chroni kontenerów ani obiektów BLOB w ramach tego konta przed usunięciem lub zastąpieniem. Aby uzyskać więcej informacji na temat ochrony danych obiektów blob, zobacz temat [Ochrona danych — omówienie](../blobs/data-protection-overview.md).

## <a name="configure-an-azure-resource-manager-lock"></a>Konfigurowanie blokady Azure Resource Manager

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby skonfigurować blokadę na koncie magazynu przy użyciu Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W sekcji **Ustawienia** wybierz pozycję **blokady**.
1. Wybierz pozycję **Dodaj**.
1. Podaj nazwę blokady zasobu i określ typ blokady. W razie potrzeby Dodaj uwagę na blokadę.

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="Zrzut ekranu przedstawiający sposób blokowania konta magazynu z blokadą CannotDelete":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby skonfigurować blokadę na koncie magazynu przy użyciu programu PowerShell, najpierw upewnij się, że zainstalowano [moduł AZ PowerShell module](https://www.powershellgallery.com/packages/Az). Następnie Wywołaj polecenie [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) i określ typ blokady, którą chcesz utworzyć, jak pokazano w następującym przykładzie:

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby skonfigurować blokadę na koncie magazynu przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Lock Create](/cli/azure/lock#az_lock_create) i określ typ blokady, którą chcesz utworzyć, jak pokazano w następującym przykładzie:

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>Autoryzowanie operacji na danych, gdy jest włączona blokada ReadOnly

Po zastosowaniu blokady **tylko do odczytu** do konta magazynu operacja [listy kluczy](/rest/api/storagerp/storageaccounts/listkeys) jest blokowana dla tego konta magazynu. Operacja **list Keys** jest OPERACJĄ post protokołu HTTPS, a wszystkie operacje post są blokowane, gdy dla konta skonfigurowano blokadę **tylko do odczytu** . Operacja **list Keys** zwraca klucze dostępu do konta, które mogą być następnie używane do odczytywania i zapisywania danych na koncie magazynu.

Jeśli klient jest w posiadaniu kluczy dostępu do konta w momencie zastosowania blokady do konta magazynu, klient może nadal korzystać z kluczy w celu uzyskania dostępu do danych. Jednak klienci, którzy nie mają dostępu do kluczy, będą musieli użyć poświadczeń usługi Azure Active Directory (Azure AD) w celu uzyskania dostępu do danych obiektów blob lub kolejek na koncie magazynu.

Użytkownicy Azure Portal mogą mieć takie oddziaływania, gdy jest stosowana blokada **tylko do odczytu** , jeśli wcześniej uzyskują dostęp do danych obiektu BLOB lub kolejki w portalu przy użyciu kluczy dostępu do konta. Po zastosowaniu blokady użytkownicy portalu będą musieli użyć poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów blob lub kolejki w portalu. W tym celu użytkownik musi mieć przypisaną co najmniej dwie role RBAC: rolę czytnika Azure Resource Manager i jedną z ról dostępu do danych usługi Azure Storage. Aby uzyskać więcej informacji, zobacz jeden z następujących artykułów:

- [Wybierz sposób autoryzacji dostępu do danych obiektów BLOB w Azure Portal](../blobs/authorize-data-operations-portal.md)
- [Wybierz sposób autoryzacji dostępu do danych kolejki w Azure Portal](../queues/authorize-data-operations-portal.md)

Dane w Azure Files lub Table service mogą stać się niedostępne dla klientów, którzy wcześniej mieli do nich dostęp przy użyciu kluczy konta. Najlepszym rozwiązaniem w przypadku konieczności zastosowania blokady **ReadOnly** do konta magazynu jest przeniesienie Azure Files i Table Service obciążeń do konta magazynu, które nie jest zablokowane z blokadą **tylko do odczytu** .

## <a name="next-steps"></a>Następne kroki

- [Omówienie ochrony danych](../blobs/data-protection-overview.md)
- [Zablokuj zasoby, aby uniemożliwić zmiany](../../azure-resource-manager/management/lock-resources.md)
