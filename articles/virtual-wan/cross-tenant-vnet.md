---
title: 'Łączenie wielu dzierżawców sieci wirtualnych z centrum: PowerShell'
titleSuffix: Azure Virtual WAN
description: Ten artykuł pomaga połączyć międzydzierżawcę sieci wirtualnych z koncentratorem wirtualnym przy użyciu programu PowerShell.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: 875fd40fea315269f7fe72032942c40551a6b144
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078973"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>Łączenie wielu dzierżawców sieci wirtualnych z wirtualnym koncentratorem sieci WAN

Ten artykuł ułatwia korzystanie z wirtualnej sieci WAN do łączenia sieci wirtualnej z koncentratorem wirtualnym w innej dzierżawie. Ta architektura jest przydatna, jeśli masz obciążenia klientów, które muszą być połączone z tą samą siecią, ale znajdują się w różnych dzierżawach. Na przykład, jak pokazano na poniższym diagramie, można połączyć sieć wirtualną firm innych niż contoso (dzierżawę zdalną) z koncentratorem wirtualnym firmy Contoso (dzierżawę nadrzędną).

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="Skonfiguruj konfigurację routingu" :::

W tym artykule omówiono sposób wykonywania następujących zadań:

* Dodaj kolejną dzierżawę jako współautora w ramach subskrypcji platformy Azure.
* Podłącz sieć wirtualną między dzierżawcami do koncentratora wirtualnego.

Kroki związane z tą konfiguracją są wykonywane przy użyciu kombinacji Azure Portal i programu PowerShell. Jednak sama funkcja jest dostępna tylko w programie PowerShell i interfejsie wiersza polecenia.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

### <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć już skonfigurowaną następującą konfigurację w Twoim środowisku:

* Wirtualna sieć WAN i koncentrator wirtualny w ramach subskrypcji nadrzędnej.
* Sieć wirtualna skonfigurowana w ramach subskrypcji w innej dzierżawie (zdalnej).
* Upewnij się, że przestrzeń adresowa sieci wirtualnej w zdalnej dzierżawie nie pokrywa się z żadną inną przestrzenią adresową w żadnym innym sieci wirtualnych już połączonym z nadrzędnym koncentratorem wirtualnym.

### <a name="working-with-azure-powershell"></a>Praca z Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>Przypisywanie uprawnień

Aby subskrypcja nadrzędna z koncentratorem wirtualnym była modyfikowana i uzyskiwać dostęp do sieci wirtualnych w dzierżawie zdalnej, należy przypisać uprawnienia **współautora** do subskrypcji nadrzędnej ze zdalnej subskrypcji dzierżawy.

1. Dodaj przypisanie roli **współautor** do konta nadrzędnego (z jednym z koncentratora sieci wirtualnej). Aby przypisać tę rolę, można użyć programu PowerShell lub Azure Portal. Wykonaj kroki opisane w następujących artykułach **Dodawanie lub usuwanie przypisań ról** :

   * [Program PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [Portal](../role-based-access-control/role-assignments-portal.md)

1. Następnie Dodaj zdalną subskrypcję dzierżawy i nadrzędną subskrypcję dzierżawy do bieżącej sesji programu PowerShell. Uruchom następujące polecenie. Jeśli logujesz się do elementu nadrzędnego, wystarczy uruchomić polecenie dla dzierżawy zdalnej.

   ```azurepowershell-interactive
   Add-AzAccount "xxxxx-b34a-4df9-9451-4402dcaecc5b"
   ```

1. Sprawdź, czy przypisanie roli zakończyło się pomyślnie, logując się do Azure PowerShell przy użyciu poświadczeń nadrzędnych i uruchamiając następujące polecenie:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Jeśli uprawnienia zostały pomyślnie przekazane do elementu nadrzędnego i zostały dodane do sesji, subskrypcja, której właścicielem jest zdalna dzierżawa, będzie wyświetlana w danych wyjściowych polecenia.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>Łączenie sieci wirtualnej z centrum

W poniższych krokach nastąpi przełączenie między kontekstem dwóch subskrypcji podczas łączenia sieci wirtualnej z koncentratorem wirtualnym. Zastąp przykładowe wartości, aby odzwierciedlały własne środowisko.

1. Upewnij się, że jesteś w kontekście konta zdalnego, uruchamiając następujące polecenie:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. Utwórz zmienną lokalną do przechowywania metadanych sieci wirtualnej, które chcesz połączyć z centrum.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. Przełącz się ponownie na konto nadrzędne.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. Podłącz sieć wirtualną do centrum.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $[local variable name]
   ```

1. Nowe połączenie można wyświetlić w programie PowerShell lub w Azure Portal.

   * Program **PowerShell:** Metadane z nowo utworzonego połączenia będą wyświetlane w konsoli programu PowerShell, jeśli połączenie zostało pomyślnie utworzone.
   * **Azure Portal:** Przejdź do centrum wirtualnego, **> połączenia Virtual Network połączeń**. Możesz wyświetlić wskaźnik do połączenia. Aby wyświetlić rzeczywisty zasób, potrzebne są odpowiednie uprawnienia.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>Rozwiązywanie problemów

* Sprawdź, czy metadane w $remote (z poprzedniej [sekcji](#connect)) są zgodne z informacjami z Azure Portal.
* Możesz sprawdzić uprawnienia przy użyciu ustawień usługi IAM dla zdalnej grupy zasobów dzierżawy lub za pomocą poleceń Azure PowerShell (Get-AzSubscription).
* Upewnij się, że cudzysłowy są zawarte wokół nazw grup zasobów lub innych zmiennych specyficznych dla środowiska (np. "VirtualHub1" lub "VirtualNetwork1").

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz:

* Wirtualna sieć WAN [— często zadawane pytania](virtual-wan-faq.md)
