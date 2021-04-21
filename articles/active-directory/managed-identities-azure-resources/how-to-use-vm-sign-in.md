---
title: Logowanie przy użyciu tożsamości zarządzanych na maszynie wirtualnej platformy Azure — Azure ADV
description: Instrukcje krok po kroku i przykłady dotyczące używania tożsamości zarządzanych przez maszynę wirtualną platformy Azure dla jednostki usługi zasobów platformy Azure w celu logowania się klienta skryptu i uzyskiwania dostępu do zasobów.
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
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 59366f1a5b4bd0572af1b36f7be2f5bf91392660
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784789"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Jak używać tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure do logowania 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ten artykuł zawiera przykłady skryptów programu PowerShell i interfejsu wiersza polecenia do logowania się przy użyciu tożsamości zarządzanych dla jednostki usługi zasobów platformy Azure oraz wskazówki dotyczące ważnych tematów, takich jak obsługa błędów.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Jeśli planujesz użyć przykładów interfejsu wiersza polecenia platformy Azure Azure PowerShell z tego artykułu, pamiętaj o zainstalowaniu najnowszej wersji programu [Azure PowerShell](/powershell/azure/install-az-ps) interfejsu wiersza polecenia [platformy Azure.](/cli/azure/install-azure-cli) 

> [!IMPORTANT]
> - We wszystkich przykładowych skryptach w tym artykule przyjęto założenie, że klient wiersza polecenia jest uruchomiony na maszynie wirtualnej z włączonymi tożsamościami zarządzanymi dla zasobów platformy Azure. Użyj funkcji "Połącz" maszyny wirtualnej w Azure Portal, aby zdalnie nawiązać połączenie z maszyną wirtualną. Aby uzyskać szczegółowe informacje na temat włączania tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej, zobacz Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej przy użyciu programu [Azure Portal](qs-configure-portal-windows-vm.md)lub jednego z wariantów artykułów (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub zestawu Azure SDK). 
> - Aby zapobiec błędom podczas dostępu do zasobów, tożsamość zarządzana maszyny wirtualnej musi mieć co najmniej dostęp "Czytelnik" w odpowiednim zakresie (maszyny wirtualnej lub wyższej), aby umożliwić Azure Resource Manager operacji na maszynie wirtualnej. Aby uzyskać szczegółowe informacje, zobacz Assign [managed identities for Azure resources access to a resource using the Azure Portal](howto-assign-access-portal.md) (Przypisywanie tożsamości zarządzanych do zasobów platformy Azure przy użyciu Azure Portal zasobów).

## <a name="overview"></a>Omówienie

Tożsamości zarządzane dla zasobów platformy Azure zapewniają obiekt [jednostki](../develop/developer-glossary.md#service-principal-object) usługi , który jest tworzony po włączeniu tożsamości zarządzanych dla zasobów platformy [Azure na](overview.md) maszynie wirtualnej. Jednostkę usługi można uzyskać dostęp do zasobów platformy Azure i używać jej jako tożsamości przez klientów skryptów/wiersza polecenia na potrzeby logowania i dostępu do zasobów. Tradycyjnie, aby uzyskać dostęp do zabezpieczonych zasobów w ramach własnej tożsamości, klient skryptu musi:  

   - być zarejestrowane i wyrażać zgodę w usłudze Azure AD jako aplikacja klienska poufne/internetowe
   - zaloguj się pod jednostką usługi przy użyciu poświadczeń aplikacji (które prawdopodobnie są osadzone w skrypcie)

Dzięki tożsamościom zarządzanym dla zasobów platformy Azure klient skryptu nie musi już nic robić, ponieważ może zalogować się w obszarze tożsamości zarządzanych dla jednostki usługi zasobów platformy Azure. 

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniższy skrypt przedstawia sposób wykonywania następujących czynności:

1. Zaloguj się do usługi Azure AD w ramach tożsamości zarządzanej maszyny wirtualnej dla jednostki usługi zasobów platformy Azure  
2. Wywołaj Azure Resource Manager i uzyskaj identyfikator jednostki usługi maszyny wirtualnej. Interfejs wiersza polecenia automatycznie zarządza pozyskiwaniem/użyciem tokenów. Pamiętaj, aby zastąpić nazwę maszyny wirtualnej nazwą `<VM-NAME>` .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Poniższy skrypt przedstawia sposób wykonywania następujących czynności:

1. Zaloguj się do usługi Azure AD w ramach tożsamości zarządzanej maszyny wirtualnej dla jednostki usługi zasobów platformy Azure  
2. Wywołaj Azure Resource Manager cmdlet, aby uzyskać informacje o maszynie wirtualnej. Program PowerShell automatycznie zarządza użyciem tokenów.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Identyfikatory zasobów dla usług platformy Azure

Zobacz [Usługi platformy Azure,](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) które obsługują uwierzytelnianie usługi Azure AD, aby uzyskać listę zasobów, które obsługują usługę Azure AD i zostały przetestowane przy użyciu tożsamości zarządzanych dla zasobów platformy Azure i ich odpowiednich identyfikatorów zasobów.

## <a name="error-handling-guidance"></a>Wskazówki dotyczące obsługi błędów 

Odpowiedzi, takie jak poniższe, mogą wskazywać, że tożsamość zarządzana maszyny wirtualnej dla zasobów platformy Azure nie została poprawnie skonfigurowana:

- PowerShell: *Invoke-WebRequest: Nie można nawiązać połączenia z serwerem zdalnym*
- Interfejs wiersza polecenia: MSI: Nie można pobrać tokenu z z błędem *`http://localhost:50342/oauth2/token` "HTTPConnectionPool(host='localhost', port=50342)* 

Jeśli wystąpi jeden z tych błędów, wróć do maszyny wirtualnej  platformy Azure  w witrynie [Azure Portal](https://portal.azure.com) i przejdź do strony Tożsamość i upewnij się, że dla ustawienia Przypisano system ustawiono wartość "Tak".

## <a name="next-steps"></a>Następne kroki

- Aby włączyć tożsamości zarządzane dla zasobów platformy Azure na maszynie wirtualnej platformy Azure, zobacz Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu [PowerShell](qs-configure-powershell-windows-vm.md)lub Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy [Azure](qs-configure-cli-windows-vm.md)
