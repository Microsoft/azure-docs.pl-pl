---
title: Grupy IP w zaporze platformy Azure
description: Grupy IP umożliwiają grupowanie adresów IP dla reguł zapory platformy Azure i zarządzanie nimi.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: 7bfa1ae5bd0f2ffe92fb37494f9fe589e1b2040e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565554"
---
# <a name="ip-groups-in-azure-firewall"></a>Grupy IP w zaporze platformy Azure

Grupy IP umożliwiają grupowanie adresów IP dla reguł zapory platformy Azure i zarządzanie nimi w następujący sposób:

- Jako adres źródłowy w regułach DNAT
- Jako adres źródłowy lub docelowy w regułach sieci
- Jako adres źródłowy w regułach aplikacji


Grupa adresów IP może mieć jeden adres IP, wiele adresów IP lub jeden lub więcej zakresów adresów IP.

Grupy adresów IP mogą być ponownie używane w regułach DNAT, sieci i aplikacji zapory platformy Azure dla wielu zapór w różnych regionach i subskrypcjach na platformie Azure. Nazwy grup muszą być unikatowe. Grupę adresów IP można skonfigurować w Azure Portal, w interfejsie wiersza polecenia platformy Azure lub interfejsu API REST. Przykładowy szablon jest dostarczany, aby pomóc Ci rozpocząć pracę.

## <a name="sample-format"></a>Przykładowy format

Następujące przykłady formatu adresu IPv4 są prawidłowe do użycia w grupach adresów IP:

- Pojedynczy adres: 10.0.0.0
- Notacja CIDR: 10.1.0.0/32
- Zakres adresów: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Tworzenie grupy adresów IP

Grupę adresów IP można utworzyć przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub API REST. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy adresów IP](create-ip-group.md).

## <a name="browse-ip-groups"></a>Przeglądaj grupy adresów IP
1. Na pasku wyszukiwania Azure Portal wpisz **grupy adresów IP** i wybierz je. Zobaczysz listę grup adresów IP lub możesz wybrać pozycję **Dodaj** , aby utworzyć nową grupę adresów IP.
2. Wybierz grupę adresów IP, aby otworzyć stronę przegląd. Można edytować, dodawać lub usuwać adresy IP lub grupy IP.

   ![Przegląd grup adresów IP](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Zarządzanie grupą adresów IP

Widoczne są wszystkie adresy IP należące do grupy IP oraz skojarzone z nimi reguły lub zasoby. Aby usunąć grupę adresów IP, należy najpierw odłączyć grupę adresów IP od zasobu, który go używa.

1. Aby wyświetlić lub edytować adresy IP, wybierz opcję **adresy IP** w obszarze **Ustawienia** w okienku po lewej stronie.
2. Aby dodać jeden lub wiele adresów IP, wybierz pozycję **Dodaj adresy IP**. Spowoduje to otwarcie strony **przeciągnij lub Przeglądaj** w celu przekazania lub można wprowadzić adres ręcznie.
3.    Wybranie wielokropka (**...**) po prawej stronie do edytowania lub usuwania adresów IP. Aby edytować lub usunąć wiele adresów IP, zaznacz pola i wybierz pozycję **Edytuj** lub **Usuń** u góry.
4. Na koniec można wyeksportować plik w formacie CSV.

> [!NOTE]
> W przypadku usunięcia wszystkich adresów IP w grupie IP, gdy jest ona nadal używana w regule, ta reguła zostanie pominięta.


## <a name="use-an-ip-group"></a>Użyj grupy adresów IP

Teraz można wybrać **grupę adresów** IP jako **typ źródłowy** lub **docelowy** dla adresów IP podczas tworzenia reguł DNAT, aplikacji lub sieci platformy Azure.

![Grupy IP w zaporze](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Dostępność w danym regionie

Grupy adresów IP są dostępne we wszystkich regionach chmury publicznej.

## <a name="ip-address-limits"></a>Limity adresów IP

W przypadku grup IP 50 lub mniej można mieć maksymalnie 5000 pojedynczych adresów IP dla każdego wystąpienia zapory. W przypadku 51 do 100 grup adresów IP można dla każdego wystąpienia zapory 500 poszczególne adresy IP.

### <a name="examples"></a>Przykłady

#### <a name="example-1-supported"></a>Przykład 1: obsługiwane

|Grupy adresów IP  |Liczba adresów IP  |Notacja  |Reguła  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Rule1|
|IPGroup2     |3|196.0.0.0 - 196.0.0.2|Rule1|
|IPGroup3     |1|1.2.3.4|Rule1|
|     |**Łącznie 4100**|         |         |
|     |         |         |         |

#### <a name="example-2-supported"></a>Przykład 2: obsługiwane

|Grupy adresów IP  |Liczba adresów IP  |Notacja  |Reguła  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Rule1|
|IPGroup2     |4096|11.0.0.0/20|Rule1|
|     |**Łącznie 8192**|         |         |

#### <a name="example-3-not-supported"></a>Przykład 3: nieobsługiwane

|Grupy adresów IP  |Liczba adresów IP  |Notacja  |Reguła  |
|---------|---------|---------|---------|
|IPGroup1 |8192     |10.0.0.0/20, 11.0.0.0/20  |Rule1|
|     |**Łącznie 8192**|||

#### <a name="example-4-supported"></a>Przykład 4: obsługiwane

|Grupy adresów IP  |Liczba adresów IP  |Notacja  |Reguła  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Rule1|
|IPGroup2     |4096|11.0.0.0/20|Ograniczeniem zakresu wystąpień|
|     |**Łącznie 8192**|         |         |


## <a name="related-azure-powershell-cmdlets"></a>Pokrewne polecenia cmdlet Azure PowerShell

Następujące polecenia cmdlet Azure PowerShell mogą służyć do tworzenia grup adresów IP i zarządzania nimi:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [New-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md).