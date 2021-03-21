---
title: Uruchamianie poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów BLOB
titleSuffix: Azure Storage
description: Program PowerShell obsługuje logowanie przy użyciu poświadczeń usługi Azure AD w celu uruchamiania poleceń dotyczących danych obiektów BLOB w usłudze Azure Storage. Token dostępu jest dostarczany dla sesji i używany do autoryzacji operacji wywoływania. Uprawnienia są zależne od roli platformy Azure przypisanej do podmiotu zabezpieczeń usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.openlocfilehash: 45fa5cf4c76577cb5e8ba9bf482f4aab7301e3c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100391494"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-data"></a>Uruchamianie poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów BLOB

Usługa Azure Storage udostępnia rozszerzenia dla programu PowerShell, które umożliwiają logowanie i uruchamianie poleceń skryptów przy użyciu poświadczeń Azure Active Directory (Azure AD). Gdy zalogujesz się do programu PowerShell przy użyciu poświadczeń usługi Azure AD, zwracany jest token dostępu OAuth 2,0. Ten token jest automatycznie używany przez program PowerShell do autoryzacji kolejnych operacji na danych w usłudze BLOB Storage. W przypadku obsługiwanych operacji nie jest już konieczne przekazywanie klucza konta ani tokenu SAS przy użyciu polecenia.

Uprawnienia do danych obiektów BLOB można przypisywać do podmiotu zabezpieczeń usługi Azure AD za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC). Aby uzyskać więcej informacji na temat ról platformy Azure w usłudze Azure Storage, zobacz [Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą funkcji RBAC platformy Azure](../common/storage-auth-aad-rbac-portal.md).

## <a name="supported-operations"></a>Obsługiwane operacje

Rozszerzenia usługi Azure Storage są obsługiwane w przypadku operacji na danych obiektów BLOB. Operacje, które można wywołać, zależą od uprawnień udzielonych podmiotowi zabezpieczeń usługi Azure AD, za pomocą którego logujesz się do programu PowerShell. Uprawnienia do kontenerów usługi Azure Storage są przypisywane za pośrednictwem usługi Azure RBAC. Na przykład, jeśli przypisano rolę **czytnika danych obiektów BLOB** , można uruchamiać polecenia skryptów, które odczytują dane z kontenera. Jeśli przypisano rolę **współautor danych obiektów BLOB** , można uruchamiać polecenia skryptów, które odczytują, zapisują lub usuwają kontener lub zawarte w nich dane.

Aby uzyskać szczegółowe informacje o uprawnieniach wymaganych dla każdej operacji usługi Azure Storage w kontenerze, zobacz [wywoływanie operacji magazynu za pomocą tokenów OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

> [!IMPORTANT]
> [W przypadku](/rest/api/storagerp/storageaccounts/listkeys) zablokowania konta magazynu z Azure Resource Manager blokadą **tylko do odczytu** nie jest dozwolone dla tego konta magazynu. **Klucze list** są operacją post, a wszystkie operacje post są blokowane, gdy dla konta skonfigurowano blokadę **tylko do odczytu** . Z tego powodu, gdy konto jest zablokowane w przypadku blokady **tylko do odczytu** , użytkownicy, którzy nie posiadają jeszcze kluczy konta, muszą używać poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów BLOB. W programie PowerShell Dołącz `-UseConnectedAccount` parametr, aby utworzyć obiekt **AzureStorageContext** przy użyciu poświadczeń usługi Azure AD.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Wywoływanie poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby użyć Azure PowerShell do logowania się i uruchamiania kolejnych operacji w usłudze Azure Storage przy użyciu poświadczeń usługi Azure AD, Utwórz kontekst magazynu, aby odwołać się do konta magazynu, i Uwzględnij `-UseConnectedAccount` parametr.

Poniższy przykład pokazuje, jak utworzyć kontener na nowym koncie magazynu z Azure PowerShell przy użyciu poświadczeń usługi Azure AD. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

1. Zaloguj się do konta platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Aby uzyskać więcej informacji na temat logowania się do platformy Azure przy użyciu programu PowerShell, zobacz [Logowanie za pomocą Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Utwórz grupę zasobów platformy Azure, wywołując metodę [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Utwórz konto magazynu, wywołując metodę [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Pobierz kontekst konta magazynu, który określa nowe konto magazynu, wywołując metodę [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Gdy działa na koncie magazynu, można odwołać się do kontekstu, zamiast wielokrotnie przekazywać poświadczenia. Dołącz `-UseConnectedAccount` parametr do wywołania wszelkich kolejnych operacji na danych przy użyciu poświadczeń usługi Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Przed utworzeniem kontenera Przypisz do siebie rolę [współautor danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) . Mimo że jesteś właścicielem konta, potrzebujesz jawnych uprawnień do wykonywania operacji na danych na koncie magazynu. Aby uzyskać więcej informacji na temat przypisywania ról platformy Azure, zobacz [używanie Azure Portal do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Propagowanie przypisań ról platformy Azure może potrwać kilka minut.

1. Utwórz kontener, wywołując metodę [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Ponieważ to wywołanie używa kontekstu utworzonego w poprzednich krokach, kontener jest tworzony przy użyciu poświadczeń usługi Azure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek przy użyciu programu PowerShell](../common/storage-auth-aad-rbac-powershell.md)
- [Autoryzuj dostęp do danych obiektów blob i kolejek z tożsamościami zarządzanymi dla zasobów platformy Azure](../common/storage-auth-aad-msi.md)