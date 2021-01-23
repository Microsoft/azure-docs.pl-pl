---
title: Grupy IP w zaporze platformy Azure
description: Grupy IP umożliwiają grupowanie adresów IP dla reguł zapory platformy Azure i zarządzanie nimi.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 6910fda855402d2f99994da1e835560be84ca2dd
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703381"
---
# <a name="ip-groups-in-azure-firewall"></a>Grupy IP w zaporze platformy Azure

Grupy IP umożliwiają grupowanie adresów IP dla reguł zapory platformy Azure i zarządzanie nimi w następujący sposób:

- Jako adres źródłowy w regułach DNAT
- Jako adres źródłowy lub docelowy w regułach sieci
- Jako adres źródłowy w regułach aplikacji


Grupa adresów IP może mieć jeden adres IP, wiele adresów IP lub jeden lub więcej zakresów adresów IP.

Grupy adresów IP mogą być ponownie używane w regułach DNAT, sieci i aplikacji zapory platformy Azure dla wielu zapór w różnych regionach i subskrypcjach na platformie Azure. Nazwy grup muszą być unikatowe. Grupę adresów IP można skonfigurować w Azure Portal, w interfejsie wiersza polecenia platformy Azure lub interfejsu API REST. Przykładowy szablon jest dostarczany, aby pomóc Ci rozpocząć pracę.

> [!NOTE]
> Grupy IP nie są obecnie dostępne w środowiskach chmury krajowej platformy Azure.

## <a name="sample-format"></a>Format próbki

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

Dla każdej zapory można mieć maksymalnie 100 grup adresów IP z maksymalnie 5000 indywidualnymi adresami IP lub prefiksami IP dla każdej grupy adresów IP.

## <a name="related-azure-powershell-cmdlets"></a>Pokrewne polecenia cmdlet Azure PowerShell

Następujące polecenia cmdlet Azure PowerShell mogą służyć do tworzenia grup adresów IP i zarządzania nimi:

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [New-AzFirewallNatRule](/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md).