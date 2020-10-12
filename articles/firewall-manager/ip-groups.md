---
title: Grupy adresów IP w zasadach zapory platformy Azure
description: Grupy IP umożliwiają grupowanie adresów IP dla reguł zasad zapory platformy Azure i zarządzanie nimi.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 5b3b3fb5e5440fea888654027f4fbf1a68b34141
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91252429"
---
# <a name="ip-groups-in-azure-firewall-policy"></a>Grupy adresów IP w zasadach zapory platformy Azure

Grupy IP umożliwiają grupowanie adresów IP dla zasad zapory platformy Azure i zarządzanie nimi w następujący sposób:

- Jako typ źródła w regułach DNAT
- Jako typ źródłowy lub docelowy w regułach sieci
- Jako typ źródła w regułach aplikacji


Grupa adresów IP może mieć jeden adres IP, wiele adresów IP lub jeden lub więcej zakresów adresów IP.

Grupy adresów IP mogą być ponownie używane w regułach DNAT, sieci i aplikacji zapory platformy Azure dla wielu zapór w różnych regionach i subskrypcjach na platformie Azure. Nazwy grup muszą być unikatowe. Grupę adresów IP można skonfigurować w Azure Portal, w interfejsie wiersza polecenia platformy Azure lub interfejsu API REST. Przykładowy szablon jest dostarczany, aby pomóc Ci rozpocząć pracę.

## <a name="sample-format"></a>Format próbki

Następujące przykłady formatu adresu IPv4 są prawidłowe do użycia w grupach adresów IP:

- Pojedynczy adres: 10.0.0.0
- Notacja CIDR: 10.1.0.0/32
- Zakres adresów: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Tworzenie grupy adresów IP

Grupę adresów IP można utworzyć przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub API REST. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy adresów IP](../firewall/create-ip-group.md).

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

Teraz można wybrać **grupę adresów** IP jako **typ źródłowy** lub **docelowy** dla adresów IP podczas tworzenia zasad przy użyciu reguł DNAT, Application lub Network.

:::image type="content" source="media/ip-groups/firewall-ip-group.png" alt-text="Grupy IP w zaporze":::

## <a name="ip-address-limits"></a>Limity adresów IP

Dla każdej zapory można mieć maksymalnie 100 grup adresów IP z maksymalnie 5000 indywidualnymi adresami IP lub prefiksami IP dla każdej grupy adresów IP.

## <a name="related-azure-powershell-cmdlets"></a>Pokrewne polecenia cmdlet Azure PowerShell

Następujące polecenia cmdlet Azure PowerShell mogą służyć do tworzenia grup adresów IP i zarządzania nimi:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallPolicyNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallpolicynetworkrule?view=azps-3.4.0)
- [New-AzFirewallPolicyApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallpolicyapplicationrule?view=azps-3.4.0)
- [New-AzFirewallPolicyNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallpolicynatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Zabezpieczanie wirtualnej sieci WAN przy użyciu Menedżera zapory platformy Azure](secure-cloud-network.md)