---
title: Zarządzanie strefami DNS w Azure DNS — PowerShell | Microsoft Docs
description: Strefami DNS można zarządzać przy użyciu programu Azure PowerShell. W tym artykule opisano, jak aktualizować, usuwać i tworzyć strefy DNS na Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 347fde86b2a37aa0f82c09c94d6aa2f9e405da9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89075600"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Jak zarządzać Strefy DNS przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Program PowerShell](dns-operations-dnszones.md)
> * [Klasyczny interfejs wiersza polecenia platformy Azure](dns-operations-dnszones-cli-nodejs.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md)

W tym artykule pokazano, jak zarządzać strefami DNS przy użyciu Azure PowerShell. Strefami DNS można także zarządzać za pomocą międzyplatformowego [interfejsu wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md) lub Azure Portal.

W tym przewodniku zawarto specjalne strefy DNS. Aby uzyskać informacje na temat używania Azure PowerShell do zarządzania strefami prywatnymi w Azure DNS, zobacz Wprowadzenie do [Azure DNS Private Zones przy użyciu Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

Strefa DNS jest tworzona za pomocą polecenia cmdlet `New-AzureRmDnsZone`.

Poniższy przykład tworzy strefę DNS o nazwie *contoso.com* w grupie zasobów o nazwie Moja *resourceName*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

Poniższy przykład pokazuje, jak utworzyć strefę DNS z dwoma [Azure Resource Manager tagami](dns-zones-records.md#tags), *Project = Demonstracja* i *ENV = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Azure DNS obsługuje również prywatne strefy DNS.  Aby uzyskać więcej informacji na temat prywatnych stref DNS, zobacz [Using Azure DNS for private domains (Używanie usługi Azure DNS dla domen prywatnych)](private-dns-overview.md). Aby uzyskać przykład sposobu tworzenia prywatnej strefy DNS, zobacz [Rozpoczynanie pracy ze strefami prywatnymi usługi Azure DNS przy użyciu programu PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Pobieranie strefy DNS

Aby pobrać strefę DNS, użyj `Get-AzureRmDnsZone` polecenia cmdlet. Ta operacja zwraca obiekt strefy DNS odpowiadający istniejącej strefie w Azure DNS. Obiekt zawiera dane dotyczące strefy (na przykład liczby zestawów rekordów), ale nie zawiera same zestawy rekordów (zobacz `Get-AzureRmDnsRecordSet` ).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>Wyświetlanie listy stref DNS

Pomijając nazwę strefy w elemencie `Get-AzureRmDnsZone`, można wyliczyć wszystkie strefy w grupie zasobów. Ta operacja zwraca tablicę obiektów stref.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Pomijając zarówno nazwę strefy, jak i nazwę grupy zasobów w elemencie `Get-AzureRmDnsZone`, można wyliczyć wszystkie strefy w subskrypcji platformy Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>Aktualizowanie strefy DNS

Zmiany w zasobie strefy DNS można wprowadzić przy użyciu polecenia `Set-AzureRmDnsZone`. To polecenie cmdlet nie powoduje aktualizacji żadnego z zestawów rekordów DNS w strefie (zobacz [How to Manage DNS records (Jak zarządzać rekordami DNS)](dns-operations-recordsets.md)). Służy ono wyłącznie do aktualizacji właściwości samego zasobu strefy. Właściwości strefy zapisywalnej są obecnie ograniczone do [Azure Resource Manager "tagów" dla zasobu strefy](dns-zones-records.md#tags).

Aby zaktualizować strefę DNS, użyj jednego z następujących dwóch sposobów:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Określanie strefy przy użyciu nazwy strefy i grupy zasobów

Takie podejście zastępuje istniejące Tagi strefy określonymi wartościami.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Określenie strefy przy użyciu obiektu $zone

Takie podejście pobiera istniejący obiekt strefy, modyfikuje znaczniki, a następnie zatwierdza zmiany. W ten sposób można zachować istniejące Tagi.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

W przypadku używania `Set-AzureRmDnsZone` z obiektem $zone [testy ETag](dns-zones-records.md#etags) są używane do zapewnienia, że współbieżne zmiany nie są zastępowane. Aby pominąć te testy, można użyć opcjonalnego `-Overwrite` przełącznika.

## <a name="delete-a-dns-zone"></a>Usuwanie strefy DNS

Strefy DNS można usunąć przy użyciu `Remove-AzureRmDnsZone` polecenia cmdlet.

> [!NOTE]
> Usunięcie strefy DNS powoduje również usunięcie wszystkich rekordów DNS w strefie. Tej operacji nie można cofnąć. Jeśli strefa DNS jest używana, po jej usunięciu usługi korzystające z tej strefy będą kończyć się niepowodzeniem.
>
>Aby zapobiec przypadkowemu usunięciu strefy, zobacz [jak chronić strefy i rekordy DNS](dns-protect-zones-recordsets.md).


Strefę DNS można usunąć przy użyciu jednego z dwóch poniższych sposobów:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Określenie strefy przy użyciu nazwy strefy i nazwy grupy zasobów

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Określenie strefy przy użyciu obiektu $zone

Strefę do usunięcia można wskazać przy użyciu obiektu `$zone` zwróconego przez element `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Obiekt strefy można również przekazać w potoku zamiast przekazywania jako parametr:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

Podobnie jak w przypadku `Set-AzureRmDnsZone` , określenie strefy przy użyciu `$zone` obiektu umożliwia sprawdzenie elementów ETag, aby upewnić się, że zmiany współbieżne nie są usuwane. Użyj `-Overwrite` przełącznika, aby pominąć te testy.

## <a name="confirmation-prompts"></a>Monity o potwierdzenie

Polecenia cmdlet `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` i `Remove-AzureRmDnsZone` obsługują monity o potwierdzenie.

Polecenia `New-AzureRmDnsZone` i `Set-AzureRmDnsZone` monitują o potwierdzenie, jeśli zmienna preferencji `$ConfirmPreference` programu PowerShell ma wartość `Medium` lub mniejszą. Z powodu potencjalnie dużego wpływu usunięcia strefy DNS polecenie cmdlet `Remove-AzureRmDnsZone` monituje o potwierdzenie, jeśli zmienna `$ConfirmPreference` programu PowerShell ma wartość inną niż `None`.

Ponieważ domyślną wartością elementu `$ConfirmPreference` jest `High`, tylko polecenie `Remove-AzureRmDnsZone` domyślnie monituje o potwierdzenie.

Bieżące ustawienie `$ConfirmPreference` można zastąpić przy użyciu parametru `-Confirm`. W przypadku wybrania elementów `-Confirm` lub `-Confirm:$True` polecenie cmdlet monituje o potwierdzenie przed uruchomieniem. W przypadku wybrania elementu `-Confirm:$False` polecenie cmdlet nie monituje o potwierdzenie.

Aby uzyskać więcej informacji na temat elementów `-Confirm` i `$ConfirmPreference`, zobacz [About Preference Variables (Informacje o zmiennych preferencji)](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak zarządzać zestawami rekordów i rekordami](dns-operations-recordsets.md) w strefie DNS.
<br>
Dowiedz się, jak [delegować domenę do Azure DNS](dns-domain-delegation.md).
<br>
Zapoznaj się z [dokumentacją programu Azure DNS PowerShell](/powershell/module/azurerm.dns).

