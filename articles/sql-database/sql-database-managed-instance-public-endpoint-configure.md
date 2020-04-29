---
title: Konfigurowanie publicznego wystąpienia zarządzanego przez punkt końcowy
description: Dowiedz się, jak skonfigurować publiczny punkt końcowy dla wystąpienia zarządzanego
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79256161"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Konfigurowanie publicznego punktu końcowego w wystąpieniu zarządzanym usługi Azure SQL Database

Publiczny punkt końcowy dla [wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) umożliwia dostęp do danych w wystąpieniu zarządzanym spoza [sieci wirtualnej](../virtual-network/virtual-networks-overview.md). Możesz uzyskać dostęp do wystąpienia zarządzanego z wielodostępnych usług platformy Azure, takich jak Power BI, Azure App Service lub sieci lokalnej. Za pomocą publicznego punktu końcowego w wystąpieniu zarządzanym nie trzeba używać sieci VPN, co może pomóc w uniknięciu problemów z przepływem pracy w sieci VPN.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> - Włącz publiczny punkt końcowy dla wystąpienia zarządzanego w Azure Portal
> - Włącz publiczny punkt końcowy dla wystąpienia zarządzanego przy użyciu programu PowerShell
> - Skonfiguruj grupę zabezpieczeń sieci wystąpienia zarządzanego w taki sposób, aby zezwalała na ruch do publicznego punktu końcowego wystąpienia zarządzanego
> - Uzyskaj parametry połączenia publicznego punktu końcowego wystąpienia zarządzanego

## <a name="permissions"></a>Uprawnienia

Ze względu na czułość danych w wystąpieniu zarządzanym, konfiguracja do włączenia publicznego punktu końcowego wystąpienia zarządzanego wymaga procesu dwuetapowego. Ta miara zabezpieczeń jest zgodna z rozdzieleniem obowiązków (SoD):

- Włączenie publicznego punktu końcowego w wystąpieniu zarządzanym wymaga wykonania przez administratora wystąpienia zarządzanego. Administratora wystąpienia zarządzanego można znaleźć na stronie **Przegląd** zasobu wystąpienia zarządzanego SQL.
- Zezwalanie na ruch przy użyciu sieciowej grupy zabezpieczeń, która musi zostać wykonana przez administratora sieci. Aby uzyskać więcej informacji, zobacz [uprawnienia sieciowe grupy zabezpieczeń](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Włączanie publicznego punktu końcowego dla wystąpienia zarządzanego w Azure Portal

1. Uruchom Azure Portal w<https://portal.azure.com/.>
1. Otwórz grupę zasobów z wystąpieniem zarządzanym i wybierz **wystąpienie zarządzane SQL** , dla którego chcesz skonfigurować publiczny punkt końcowy.
1. Na stronie ustawienia **zabezpieczeń** wybierz kartę **Sieć wirtualna** .
1. Na stronie Konfiguracja sieci wirtualnej wybierz pozycję **Włącz** , a następnie ikonę **Zapisz** , aby zaktualizować konfigurację.

![mi-VNET-config. png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Włączanie publicznego punktu końcowego dla wystąpienia zarządzanego przy użyciu programu PowerShell

### <a name="enable-public-endpoint"></a>Włącz publiczny punkt końcowy

Uruchom następujące polecenia programu PowerShell. Zamień **Identyfikator subskrypcji** na identyfikator subskrypcji. Zastąp także **RG-Name nazwą** grupy zasobów dla wystąpienia zarządzanego i Zastąp wartość **mi nazwą** wystąpienia zarządzanego.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Wyłącz publiczny punkt końcowy

Aby wyłączyć publiczny punkt końcowy przy użyciu programu PowerShell, należy wykonać następujące polecenie (a także pamiętać o zamknięciu sieciowej grupy zabezpieczeń dla portu przychodzącego 3342, jeśli został on skonfigurowany):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Zezwalaj na ruch publicznego punktu końcowego w sieciowej grupie zabezpieczeń

1. Jeśli strona Konfiguracja wystąpienia zarządzanego jest nadal otwarta, przejdź do karty **Przegląd** . w przeciwnym razie Wróć do zasobu **wystąpienia zarządzanego SQL** . Wybierz łącze **Sieć wirtualna/podsieć** , co spowoduje przejście do strony Konfiguracja sieci wirtualnej.

    ![mi-Overview. png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Wybierz kartę **podsieci** w okienku Konfiguracja po lewej stronie sieci wirtualnej, a następnie zanotuj **grupę zabezpieczeń** dla wystąpienia zarządzanego.

    ![mi-VNET-Subnet. png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Wróć do grupy zasobów zawierającej wystąpienie zarządzane. Powinna zostać wyświetlona poprzednia nazwa **grupy zabezpieczeń sieci** . Wybierz nazwę, aby przejść do strony konfiguracji sieciowej grupy zabezpieczeń.

1. Wybierz kartę **reguły zabezpieczeń dla ruchu przychodzącego** i **Dodaj** regułę o wyższym priorytecie niż reguła **deny_all_inbound** z następującymi ustawieniami: </br> </br>

    |Ustawienie  |Sugerowana wartość  |Opis  |
    |---------|---------|---------|
    |**Element źródłowy**     |Dowolny adres IP lub tag usługi         |<ul><li>W przypadku usług platformy Azure, takich jak Power BI, wybierz tag usługi w chmurze platformy Azure</li> <li>Użyj adresu IP translatora adresów sieciowych dla komputera lub maszyny wirtualnej platformy Azure</li></ul> |
    |**Zakresy portów źródłowych**     |*         |Pozostaw to * (any), ponieważ porty źródłowe są zwykle przydzielane dynamicznie i nieprzewidywalne |
    |**Punktu**     |Dowolne         |Pozostawienie miejsca docelowego jako dowolnego do zezwalania na ruch w podsieci wystąpienia zarządzanego |
    |**Docelowe zakresy portów**     |3342         |Port docelowy zakresu do 3342, czyli publiczny punkt końcowy TDS wystąpienia zarządzanego |
    |**Protokol**     |TCP         |Wystąpienie zarządzane używa protokołu TCP dla TDS |
    |**Akcja**     |Zezwalaj         |Zezwalaj na ruch przychodzący do wystąpienia zarządzanego za pomocą publicznego punktu końcowego |
    |**Priorytet**     |1300         |Upewnij się, że ta reguła ma wyższy priorytet niż reguła **deny_all_inbound** |

    ![mi-NSG-rules. png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Port 3342 jest używany dla połączeń publicznego punktu końcowego z wystąpieniem zarządzanym i nie można go zmienić w tym momencie.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Uzyskiwanie parametrów połączenia publicznego punktu końcowego wystąpienia zarządzanego

1. Przejdź do strony konfiguracji wystąpienia zarządzanego SQL, która została włączona dla publicznego punktu końcowego. Wybierz kartę **Parametry połączenia** w obszarze Konfiguracja **ustawień** .
1. Należy pamiętać, że nazwa hosta publicznego punktu końcowego jest w formacie <mi_name>. **Public**. <dns_zone>. Database.Windows.NET i że port używany do połączenia to 3342.

    ![mi-Public-Endpoint-Conn-String. png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z bezpiecznym użyciem Azure SQL Database wystąpienia zarządzanego z publicznym punktem końcowym](sql-database-managed-instance-public-endpoint-securely.md).