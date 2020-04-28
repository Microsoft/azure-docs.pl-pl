---
title: Uruchamianie poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektu BLOB lub kolejki
titleSuffix: Azure Storage
description: Program PowerShell obsługuje logowanie przy użyciu poświadczeń usługi Azure AD, aby uruchamiać polecenia w usłudze Azure Storage BLOB i kolejkach danych. Token dostępu jest dostarczany dla sesji i używany do autoryzacji operacji wywoływania. Uprawnienia są zależne od roli RBAC przypisanej do podmiotu zabezpieczeń usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75553451"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Uruchamianie poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektu BLOB lub kolejki

Usługa Azure Storage udostępnia rozszerzenia dla programu PowerShell, które umożliwiają logowanie i uruchamianie poleceń skryptów przy użyciu poświadczeń Azure Active Directory (Azure AD). Gdy zalogujesz się do programu PowerShell przy użyciu poświadczeń usługi Azure AD, zwracany jest token dostępu OAuth 2,0. Ten token jest automatycznie używany przez program PowerShell do autoryzacji kolejnych operacji na danych względem magazynu obiektów blob lub Queue. W przypadku obsługiwanych operacji nie jest już konieczne przekazywanie klucza konta ani tokenu SAS przy użyciu polecenia.

Można przypisać uprawnienia do obiektów blob i kolejek danych do podmiotu zabezpieczeń usługi Azure AD za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji na temat ról RBAC w usłudze Azure Storage, zobacz [Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Obsługiwane operacje

Rozszerzenia usługi Azure Storage są obsługiwane w przypadku operacji na danych obiektów blob i kolejek. Operacje, które można wywołać, zależą od uprawnień udzielonych podmiotowi zabezpieczeń usługi Azure AD, za pomocą którego logujesz się do programu PowerShell. Uprawnienia do kontenerów lub kolejek usługi Azure Storage są przypisywane za pośrednictwem RBAC. Na przykład, jeśli przypisano rolę **czytnika danych obiektów BLOB** , można uruchamiać polecenia skryptów, które odczytują dane z kontenera lub kolejki. Jeśli przypisano rolę **współautor danych obiektów BLOB** , można uruchamiać polecenia skryptów, które odczytują, zapisują lub usuwają kontener lub kolejkę lub zawarte w nich dane.

Aby uzyskać szczegółowe informacje o uprawnieniach wymaganych dla każdej operacji usługi Azure Storage w kontenerze lub kolejce, zobacz [wywoływanie operacji magazynu przy użyciu tokenów OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Wywoływanie poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby użyć Azure PowerShell do logowania się i uruchamiania kolejnych operacji w usłudze Azure Storage przy użyciu poświadczeń usługi Azure AD, Utwórz kontekst magazynu, aby odwołać się do konta `-UseConnectedAccount` magazynu, i Uwzględnij parametr.

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

1. Przed utworzeniem kontenera Przypisz do siebie rolę [współautor danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) . Mimo że jesteś właścicielem konta, potrzebujesz jawnych uprawnień do wykonywania operacji na danych na koncie magazynu. Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [przyznawanie dostępu do obiektów blob platformy Azure i danych z kolejki RBAC w Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Propagowanie przypisań ról RBAC może potrwać kilka minut.

1. Utwórz kontener, wywołując metodę [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Ponieważ to wywołanie używa kontekstu utworzonego w poprzednich krokach, kontener jest tworzony przy użyciu poświadczeń usługi Azure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Następne kroki

- [Używanie programu PowerShell do przypisywania roli RBAC na potrzeby dostępu do danych obiektów blob i kolejek](storage-auth-aad-rbac-powershell.md)
- [Autoryzuj dostęp do danych obiektów blob i kolejek z tożsamościami zarządzanymi dla zasobów platformy Azure](storage-auth-aad-msi.md)
