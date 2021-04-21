---
title: Ochrona prywatnych stref i rekordów DNS — Azure DNS
description: W tej ścieżce szkoleniowej rozpoczynasz ochronę prywatnych stref DNS i zestawów rekordów w Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: a68b40852750e124749ac838c50acae2212c4732
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785887"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Jak chronić prywatne strefy i rekordy DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prywatna strefa DNS strefy i rekordy są krytycznymi zasobami. Usunięcie strefy DNS lub pojedynczego rekordu DNS może spowodować błąd usługi. Ważne jest, aby strefy i rekordy DNS zostały zabezpieczone przed nieautoryzowanymi lub przypadkowymi zmianami.

W tym artykule wyjaśniono, Azure DNS chronić prywatne strefy i rekordy DNS przed takimi zmianami.  Stosujemy dwie zaawansowane funkcje udostępniane przez usługę Azure Resource Manager: kontrola dostępu na podstawie ról [(RBAC)](../role-based-access-control/overview.md) platformy Azure i [blokady zasobów.](../azure-resource-manager/management/lock-resources.md)

## <a name="azure-role-based-access-control"></a>Kontrola dostępu na podstawie ról na platformie Azure

Kontrola dostępu oparta na rolach (RBAC) platformy Azure umożliwia precyzyjne zarządzanie dostępem dla użytkowników, grup i zasobów platformy Azure. Dzięki kontroli RBAC platformy Azure możesz przyznać użytkownikom poziom dostępu, który jest potrzebny. Aby uzyskać więcej informacji na temat sposobu, w jaki kontrola dostępu oparta na rolach platformy Azure ułatwia zarządzanie dostępem, zobacz Co to jest kontrola dostępu na podstawie ról [(RBAC)](../role-based-access-control/overview.md)platformy Azure).

### <a name="the-private-dns-zone-contributor-role"></a>Rola współautora Prywatna strefa DNS strefy czasowej

Rola współautora Prywatna strefa DNS strefie czasowej jest wbudowaną rolą do zarządzania prywatnymi zasobami DNS. Ta rola zastosowana do użytkownika lub grupy umożliwia zarządzanie prywatnymi zasobami DNS.

Grupa zasobów *myPrivateDNS* zawiera pięć stref dla firmy Contoso Corporation. Przyznanie administratorowi DNS uprawnień Prywatna strefa DNS współautora strefy do tej grupy zasobów umożliwia pełną kontrolę nad tymi strefami DNS. Pozwala to uniknąć udzielania niepotrzebnych uprawnień. Administrator DNS nie może tworzyć ani zatrzymywać maszyn wirtualnych.

Najprostszym sposobem przypisywania uprawnień RBAC platformy Azure jest [korzystanie z](../role-based-access-control/role-assignments-portal.md)Azure Portal .  

Otwórz **pozycję Kontrola dostępu (IAM)** dla grupy zasobów, wybierz pozycję **Dodaj,** a następnie wybierz **Prywatna strefa DNS współautora strefy.** Wybierz wymaganych użytkowników lub grupy, aby udzielić uprawnień.

![RBAC platformy Azure na poziomie grupy zasobów za pośrednictwem Azure Portal](./media/dns-protect-private-zones-recordsets/rbac1.png)

Uprawnienia można również [przyznać przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Równoważne polecenie jest również dostępne [za pośrednictwem interfejsu wiersza polecenia platformy Azure:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-azure-rbac"></a>RBAC platformy Azure na poziomie strefy prywatnej

Reguły RBAC platformy Azure można stosować do subskrypcji, grupy zasobów lub do pojedynczego zasobu. Ten zasób może być pojedynczą strefą DNS lub indywidualnym zestawem rekordów.

Na przykład grupa zasobów *myPrivateDNS*  zawiera strefę private.contoso.com strefę i strefę podrzędną *customers.private.contoso.com*. Rekordy CNAME są tworzone dla każdego konta klienta. Konto administratora używane do zarządzania rekordami CNAME ma przypisane uprawnienia do tworzenia rekordów w *strefie customers.private.contoso.com* CNAME. Konto może zarządzać *tylko customers.private.contoso.com* użytkownikami.

Uprawnienia RBAC platformy Azure na poziomie strefy można przyznać za pośrednictwem Azure Portal.  Otwórz **pozycję Kontrola dostępu (IAM)** dla strefy, wybierz pozycję **Dodaj,** a następnie wybierz **Prywatna strefa DNS współautora** strefy. Wybierz wymaganych użytkowników lub grupy, aby udzielić uprawnień.

![RBAC platformy Azure na poziomie strefy DNS za pośrednictwem Azure Portal](./media/dns-protect-private-zones-recordsets/rbac2.png)

Uprawnienia można również [przyznać przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

Równoważne polecenie jest również dostępne [za pośrednictwem interfejsu wiersza polecenia platformy Azure:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>RBAC platformy Azure na poziomie zestawu rekordów

Uprawnienia są stosowane na poziomie zestawu rekordów.  Użytkownik ma nadaną kontrolę nad potrzebnymi wpisami i nie może wprowadzać żadnych innych zmian.

Uprawnienia RBAC platformy Azure na poziomie zestawu rekordów można skonfigurować za pośrednictwem Azure Portal przy użyciu **przycisku Access Control (IAM)** na stronie zestawu rekordów:

![Zrzut ekranu Access Control przycisku "Kliknij przycisk".](./media/dns-protect-private-zones-recordsets/rbac3.png)

![Zrzut ekranu Access Control z wybraną pozycję Dodaj przypisanie roli.](./media/dns-protect-private-zones-recordsets/rbac4.png)

Uprawnienia RBAC platformy Azure na poziomie zestawu rekordów można również przyznać przy [użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Równoważne polecenie jest również dostępne [za pośrednictwem interfejsu wiersza polecenia platformy Azure:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Role niestandardowe

Wbudowana rola współautora Prywatna strefa DNS strefie umożliwia pełną kontrolę nad zasobem DNS. W celu zapewnienia precyzyjnej kontroli można utworzyć własne niestandardowe role platformy Azure.

Konto, które jest używane do zarządzania rekordami CNAME, ma przyznane uprawnienie do zarządzania tylko rekordami CNAME. Konto nie może modyfikować rekordów innych typów. Konto nie może wykonać operacji na poziomie strefy, takich jak usuwanie strefy.

W poniższym przykładzie przedstawiono definicję roli niestandardowej do zarządzania tylko rekordami CNAME:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Właściwość Actions definiuje następujące uprawnienia specyficzne dla systemu DNS:

* `Microsoft.Network/privateDnsZones/CNAME/*` przyznaje pełną kontrolę nad rekordami CNAME
* `Microsoft.Network/privateDNSZones/read` przyznaje uprawnienia do odczytu stref prywatnych DNS, ale nie do modyfikowania ich, dzięki czemu można zobaczyć strefę, w której jest tworzony rekord CNAME.

> [!NOTE]
> Używanie roli niestandardowej platformy Azure w celu zapobiegania usuwaniu zestawów rekordów przy jednoczesnym umożliwieniu ich aktualizacji nie jest efektywną kontrolą. Zapobiega to usuwaniu zestawów rekordów, ale nie uniemożliwia ich modyfikacji.  Dozwolone modyfikacje obejmują dodawanie i usuwanie rekordów z zestawu rekordów, w tym usuwanie wszystkich rekordów w celu pozostawienia pustego zestawu rekordów. Ma to taki sam efekt jak usunięcie zestawu rekordów z punktu widzenia rozpoznawania nazw DNS.

Definicji ról niestandardowych nie można obecnie zdefiniować za pośrednictwem Azure Portal. Rolę niestandardową na podstawie tej definicji roli można utworzyć przy użyciu Azure PowerShell:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Można go również utworzyć za pomocą interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

Rolę można następnie przypisać w taki sam sposób, jak role wbudowane, jak opisano wcześniej w tym artykule.

Aby uzyskać więcej informacji na temat tworzenia i przypisywania ról niestandardowych oraz zarządzania nimi, zobacz [Role niestandardowe platformy Azure.](../role-based-access-control/custom-roles.md)

## <a name="resource-locks"></a>Blokady zasobów

Azure Resource Manager obsługuje inny typ kontroli zabezpieczeń, czyli możliwość blokowania zasobów. Blokady zasobów są stosowane do zasobu i są skuteczne dla wszystkich użytkowników i ról. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](../azure-resource-manager/management/lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

Istnieją dwa typy blokady zasobów: **CanNotDelete** i **ReadOnly.** Te typy blokad można stosować do strefy Prywatna strefa DNS lub do pojedynczego zestawu rekordów.  W poniższych sekcjach opisano kilka typowych scenariuszy i sposób ich obsługi przy użyciu blokad zasobów.

### <a name="protecting-against-all-changes"></a>Ochrona przed wszystkimi zmianami

Aby zapobiec wprowadzaniu zmian, zastosuj blokadę ReadOnly do strefy. Ta blokada zapobiega tworzeniu nowych zestawów rekordów oraz modyfikacji lub usunięciu istniejących zestawów rekordów.

Blokady zasobów na poziomie strefy można tworzyć za pośrednictwem Azure Portal.  Na stronie Strefa DNS wybierz pozycję **Blokady,** a następnie wybierz **pozycję +Dodaj**:

![Blokady zasobów na poziomie strefy za pośrednictwem Azure Portal](./media/dns-protect-private-zones-recordsets/locks1.png)

Blokady zasobów na poziomie strefy można również tworzyć za pomocą [Azure PowerShell](/powershell/module/az.resources/new-azresourcelock):

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Równoważne polecenie jest również dostępne [za pośrednictwem interfejsu wiersza polecenia platformy Azure:](/cli/azure/lock#az_lock_create)

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Ochrona poszczególnych rekordów

Aby zapobiec modyfikacji istniejącego zestawu rekordów DNS, zastosuj blokadę ReadOnly do zestawu rekordów.

> [!NOTE]
> Stosowanie blokady CanNotDelete do zestawu rekordów nie jest efektywną kontrolką. Zapobiega to usunięciu zestawu rekordów, ale nie uniemożliwia jego modyfikacji.  Dozwolone modyfikacje obejmują dodawanie i usuwanie rekordów z zestawu rekordów, w tym usuwanie wszystkich rekordów w celu pozostawienia pustego zestawu rekordów. Ma to taki sam efekt jak usunięcie zestawu rekordów z punktu widzenia rozpoznawania nazw DNS.

Blokady zasobów na poziomie zestawu rekordów można obecnie konfigurować tylko przy użyciu Azure PowerShell.  Nie są one obsługiwane w interfejsie wiersza polecenia Azure Portal platformy Azure.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Ochrona przed usunięciem strefy

Gdy strefa zostanie usunięta w Azure DNS, wszystkie zestawy rekordów w strefie zostaną usunięte.  Tej operacji nie można cofnąć. Przypadkowe usunięcie strefy krytycznej może mieć znaczący wpływ na działalność biznesową.  Ważne jest, aby chronić przed przypadkowym usunięciem strefy.

Zastosowanie blokady CanNotDelete do strefy uniemożliwia usunięcie strefy. Blokady są dziedziczone przez zasoby podrzędne. Blokada uniemożliwia usunięcie wszystkich zestawów rekordów w strefie. Jak opisano w powyższej notce, jest to nieskuteczne, ponieważ rekordy można nadal usuwać z istniejących zestawów rekordów.

Alternatywnie zastosuj blokadę CanNotDelete do zestawu rekordów w strefie, takiego jak zestaw rekordów SOA. Strefa nie zostanie usunięta bez usunięcia zestawów rekordów. Ta blokada chroni przed usunięciem strefy, umożliwiając jednocześnie swobodne zmodyfikowanie zestawów rekordów w strefie. Jeśli podjęto próbę usunięcia strefy, Azure Resource Manager wykryje to usunięcie. Usunięcie spowoduje również usunięcie zestawu rekordów SOA, Azure Resource Manager blokuje wywołanie, ponieważ soa jest zablokowany.  Żadne zestawy rekordów nie są usuwane.

Następujące polecenie programu PowerShell tworzy blokadę CanNotDelete względem rekordu SOA danej strefy:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Inną opcją zapobiegającą przypadkowemu usunięciu strefy jest użycie roli niestandardowej. Ta rola gwarantuje, że konta używane do zarządzania strefami nie mają uprawnień do usuwania strefy. 

Gdy musisz usunąć strefę, możesz wymusić usunięcie dwuetapowe:

 - Najpierw przyznaj uprawnienia do usuwania strefy
 - Następnie przyznaj uprawnienia do usuwania strefy.

Rola niestandardowa działa dla wszystkich stref, do których dostęp mają te konta. Konta z uprawnieniami do usuwania strefy, takimi jak właściciel subskrypcji, nadal mogą przypadkowo usunąć strefę.

Można używać obu metod — blokad zasobów i ról niestandardowych — w tym samym czasie, jako podejścia do ochrony strefy DNS w głębi systemu.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat pracy z kontrolą dostępu opartą na rolach platformy Azure, zobacz Co to jest kontrola dostępu na podstawie ról [(RBAC)](../role-based-access-control/overview.md)platformy Azure).
* Aby uzyskać więcej informacji na temat pracy z blokadami zasobów, zobacz [Blokowanie zasobów za pomocą Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
