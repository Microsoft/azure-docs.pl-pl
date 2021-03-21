---
title: Logowanie do usługi Azure ADV przy użyciu tożsamości zarządzanych na maszynie wirtualnej platformy Azure
description: Instrukcje krok po kroku i przykłady dotyczące korzystania z tożsamości zarządzanych przez maszynę wirtualną platformy Azure dla jednostki usługi Azure Resources w celu logowania klienta skryptu i dostępu do zasobów.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 61e83bd27c9434c4222e0161e3b643b183d1aa84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99090964"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Jak używać tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure w celu logowania 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
W tym artykule przedstawiono przykłady skryptów programu PowerShell i interfejsu wiersza polecenia do logowania przy użyciu tożsamości zarządzanych dla jednostki usługi Azure sources oraz wskazówki dotyczące ważnych tematów, takich jak obsługa błędów.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Jeśli planujesz Azure PowerShell używanie przykładów interfejsu wiersza polecenia platformy Azure w tym artykule, Zadbaj o zainstalowanie najnowszej wersji [Azure PowerShell](/powershell/azure/install-az-ps) lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - We wszystkich przykładowych skryptach w tym artykule przyjęto założenie, że klient wiersza polecenia działa na maszynie wirtualnej z włączonymi tożsamościami zarządzanymi dla zasobów platformy Azure. Użyj funkcji "Połącz" maszyny wirtualnej w Azure Portal, aby zdalnie nawiązać połączenie z maszyną wirtualną. Aby uzyskać szczegółowe informacje na temat włączania tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](qs-configure-portal-windows-vm.md)lub jednego z artykułów wariantów (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub zestawu Azure SDK). 
> - Aby zapobiec wystąpieniu błędów podczas uzyskiwania dostępu do zasobów, zarządzana tożsamość maszyny wirtualnej musi mieć co najmniej dostęp do czytnika w odpowiednim zakresie (maszynie wirtualnej lub wyższej), aby umożliwić Azure Resource Manager operacji na maszynie wirtualnej. Aby uzyskać szczegółowe informacje [, zobacz Przypisywanie zarządzanych tożsamości dla zasobów platformy Azure do zasobu przy użyciu Azure Portal](howto-assign-access-portal.md) .

## <a name="overview"></a>Omówienie

Zarządzane tożsamości dla zasobów platformy Azure zapewniają [obiekt jednostki usługi](../develop/developer-glossary.md#service-principal-object) , który jest [tworzony po włączeniu zarządzanych tożsamości dla zasobów platformy Azure](overview.md) na maszynie wirtualnej. Jednostka usługi może uzyskać dostęp do zasobów platformy Azure i służyć jako tożsamość za pomocą skryptów/klientów wiersza polecenia w celu zalogowania się i dostępu do zasobów. Tradycyjnie, aby uzyskać dostęp do zabezpieczonych zasobów w ramach własnej tożsamości, klient skryptów powinien:  

   - być zarejestrowane i wyrażane za pomocą usługi Azure AD jako aplikacji klienta poufnego/sieci Web
   - Zaloguj się pod swoją jednostką usługi przy użyciu poświadczeń aplikacji (które mogą być osadzone w skrypcie).

W przypadku zarządzanych tożsamości dla zasobów platformy Azure klient skryptów nie musi już wykonywać żadnych czynności, ponieważ może się zalogować w ramach zarządzanych tożsamości dla jednostki usługi Azure Resources. 

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniższy skrypt pokazuje, jak:

1. Zaloguj się do usługi Azure AD w obszarze zarządzana tożsamość maszyny wirtualnej dla jednostki usługi Azure Resources  
2. Wywołaj Azure Resource Manager i Pobierz identyfikator jednostki usługi maszyny wirtualnej. Interfejs wiersza polecenia obsługuje zarządzanie uzyskaniem/użyciem tokenów automatycznie. Pamiętaj, aby zastąpić nazwę maszyny wirtualnej `<VM-NAME>` .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Poniższy skrypt pokazuje, jak:

1. Zaloguj się do usługi Azure AD w obszarze zarządzana tożsamość maszyny wirtualnej dla jednostki usługi Azure Resources  
2. Wywołaj polecenie cmdlet Azure Resource Manager, aby uzyskać informacje o maszynie wirtualnej. Program PowerShell obsługuje automatyczne zarządzanie użyciem tokenów.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Identyfikatory zasobów dla usług platformy Azure

Zobacz [usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) , aby uzyskać listę zasobów, które obsługują usługę Azure AD i zostały przetestowane przy użyciu tożsamości zarządzanych dla zasobów platformy Azure oraz ich identyfikatorów zasobów.

## <a name="error-handling-guidance"></a>Wskazówki dotyczące obsługi błędów 

Odpowiedzi na przykład następujące mogą wskazywać, że zarządzana tożsamość maszyny wirtualnej dla zasobów platformy Azure nie została prawidłowo skonfigurowana:

- PowerShell: *Invoke-WebRequest: nie można nawiązać połączenia z serwerem zdalnym*
- Interfejs wiersza polecenia: *MSI: nie można pobrać tokenu z `http://localhost:50342/oauth2/token` z powodu błędu "HTTPConnectionPool (host =" localhost ", port = 50342)* 

Jeśli zostanie wyświetlony jeden z tych błędów, Wróć do maszyny wirtualnej platformy Azure w [Azure Portal](https://portal.azure.com) i przejdź do strony **tożsamość** i upewnij się, że **przypisany system** ma wartość "yes".

## <a name="next-steps"></a>Następne kroki

- Aby włączyć zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell](qs-configure-powershell-windows-vm.md)lub [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy](qs-configure-cli-windows-vm.md) Azure