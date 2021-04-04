---
title: Odwrotny serwer DNS dla usług platformy Azure — Azure DNS
description: Za pomocą tej ścieżki szkoleniowej Rozpocznij Konfigurowanie wyszukiwania odwrotnej usługi DNS dla usług hostowanych na platformie Azure.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: c72e17e601ebf87b0f344a4723159ae22abc81d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919888"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Konfigurowanie odwrotnego serwera DNS dla usług hostowanych na platformie Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym artykule wyjaśniono, jak skonfigurować odwrotne wyszukiwania DNS dla usług hostowanych na platformie Azure.

Usługi na platformie Azure używają adresów IP przypisanych przez platformę Azure i należą do firmy Microsoft. Te rekordy zwrotne DNS (rekordy PTR) muszą zostać utworzone we właściwych strefach wyszukiwania odwrotnej usługi DNS firmy Microsoft. W tym artykule wyjaśniono, jak to zrobić.

Tego scenariusza nie należy mylić z możliwością [hostowania stref wyszukiwania wstecznego DNS dla przypisanych zakresów adresów IP w Azure DNS](dns-reverse-dns-hosting.md). W takim przypadku zakresy adresów IP reprezentowane przez strefę wyszukiwania wstecznego muszą być przypisane do organizacji, zazwyczaj przez usługodawcę internetowego.

Przed zapisaniem tego artykułu należy zapoznać się z tym [omówieniem zwrotnego systemu DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md).

W Azure DNS zasoby obliczeniowe (takie jak maszyny wirtualne, zestawy skalowania maszyn wirtualnych lub klastry Service Fabric) są udostępniane za pośrednictwem zasobu PublicIpAddress. Odwrotne wyszukiwania DNS są konfigurowane przy użyciu właściwości "ReverseFqdn" PublicIpAddress.


Odwrotny serwer DNS nie jest obecnie obsługiwany dla Azure App Service i Application Gateway.

## <a name="validation-of-reverse-dns-records"></a>Walidacja rekordów odwrotnego systemu DNS

Firma zewnętrzna nie powinna mieć możliwości tworzenia rekordów odwrotnego systemu DNS na potrzeby mapowania usługi platformy Azure do domen DNS. Aby tego uniknąć, platforma Azure zezwala na tworzenie rekordu odwrotnego DNS, w którym nazwa domeny określona w rekordzie odwrotnej usługi DNS jest taka sama jak nazwa DNS lub adres IP usługi PublicIpAddress lub w chmurze w tej samej subskrypcji platformy Azure.

Sprawdzanie poprawności jest wykonywane tylko wtedy, gdy rekord odwrotnego systemu DNS jest ustawiony lub modyfikowany. Okresowe ponowne sprawdzanie poprawności nie jest wykonywane.

Na przykład: Załóżmy, że zasób PublicIpAddress ma nazwę DNS contosoapp1.northus.cloudapp.azure.com i adres IP 23.96.52.53. ReverseFqdn dla PublicIpAddress można określić jako:
* Nazwa DNS dla PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* Nazwa DNS dla innego PublicIpAddress w tej samej subskrypcji, na przykład contosoapp2.westus.cloudapp.azure.com
* Nazwa DNS znaczącym, taka jak app1.contoso.com, tak długo, jak ta nazwa jest *najpierw* skonfigurowana jako CNAME do ContosoApp1.northus.cloudapp.Azure.com lub do innego PublicIpAddress w tej samej subskrypcji.
* Nazwa DNS znaczącym, taka jak app1.contoso.com, tak długo, jak ta nazwa jest *najpierw* skonfigurowana jako rekord A na adres IP 23.96.52.53 lub adres IP innego PublicIpAddress w tej samej subskrypcji.

Te same ograniczenia dotyczą odwrotnej usługi DNS dla Cloud Services.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Zwrotny serwer DNS dla zasobów PublicIpAddress

Ta sekcja zawiera szczegółowe instrukcje dotyczące konfigurowania zwrotnego systemu DNS dla zasobów PublicIpAddress w modelu wdrażania Menedżer zasobów przy użyciu Azure PowerShell, klasycznego interfejsu wiersza polecenia platformy Azure lub interfejsu wiersza polecenia platformy Azure. Konfigurowanie zwrotnego serwera DNS dla zasobów PublicIpAddress nie jest obecnie obsługiwane przez Azure Portal.

Obecnie platforma Azure obsługuje odwrotną usługę DNS tylko w przypadku zasobów PublicIpAddress IPv4. Nie jest obsługiwana w przypadku protokołu IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Dodawanie odwrotnego systemu DNS do istniejącej adresów publicipaddress

#### <a name="powershell"></a>PowerShell

Aby zaktualizować odwrotny serwer DNS do istniejącego PublicIpAddress:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Aby dodać odwrotny serwer DNS do istniejącego PublicIpAddress, który nie ma jeszcze nazwy DNS, należy również określić nazwę DNS:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

Aby dodać odwrotny serwer DNS do istniejącego PublicIpAddress:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Aby dodać odwrotny serwer DNS do istniejącego PublicIpAddress, który nie ma jeszcze nazwy DNS, należy również określić nazwę DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby dodać odwrotny serwer DNS do istniejącego PublicIpAddress:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Aby dodać odwrotny serwer DNS do istniejącego PublicIpAddress, który nie ma jeszcze nazwy DNS, należy również określić nazwę DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Utwórz publiczny adres IP z odwrotnym systemem DNS

Aby utworzyć nowy PublicIpAddress z właściwością odwrotnej usługi DNS:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Wyświetl odwrotny serwer DNS dla istniejącej PublicIpAddress

Aby wyświetlić skonfigurowaną wartość dla istniejącej PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Usuń odwrotny serwer DNS z istniejących Publiczne adresy IP

Aby usunąć odwrotną Właściwość DNS z istniejącej PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Skonfiguruj odwrotny serwer DNS dla Cloud Services

Ta sekcja zawiera szczegółowe instrukcje dotyczące konfigurowania zwrotnego systemu DNS dla Cloud Services w klasycznym modelu wdrażania przy użyciu Azure PowerShell. Konfigurowanie odwrotnego systemu DNS dla Cloud Services nie jest obsługiwane za pośrednictwem Azure Portal, klasycznego interfejsu wiersza polecenia platformy Azure lub interfejsu wiersza polecenia platformy Azure.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Dodaj odwrotny serwer DNS do istniejących Cloud Services

Aby dodać odwrotny rekord DNS do istniejącej usługi w chmurze:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Tworzenie usługi w chmurze z odwrotnym systemem DNS

Aby utworzyć nową usługę w chmurze z już określoną właściwością odwrotnej usługi DNS:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Wyświetl odwrotny serwer DNS dla istniejących Cloud Services

Aby wyświetlić Właściwość odwrotnej usługi DNS dla istniejącej usługi w chmurze:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Usuń odwrotny serwer DNS z istniejących Cloud Services

Aby usunąć odwrotną Właściwość DNS z istniejącej usługi w chmurze:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Często zadawane pytania

### <a name="how-much-do-reverse-dns-records-cost"></a>Jak wiele kosztów odwrotnych rekordów DNS?

Są one bezpłatne!  Nie ma dodatkowych kosztów dla rekordów lub zapytań wstecznych DNS.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Czy moje rekordy odwrotnego systemu DNS będą rozpoznawane z Internetu?

Tak. Po ustawieniu właściwości odwrotnej usługi DNS dla usługi platformy Azure system Azure zarządza wszystkimi delegowaniem DNS i strefami DNS, które są wymagane w celu zapewnienia, że rekord odwrotnego systemu DNS jest rozpoznawany dla wszystkich użytkowników Internetu.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Czy domyślne rekordy odwrotnego systemu DNS utworzone dla usług platformy Azure?

Nie. Odwrotny system DNS to funkcja opcjonalna. Nie są tworzone żadne domyślne rekordy odwrotnego DNS, jeśli nie zostaną one skonfigurowane.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Jaki jest format w pełni kwalifikowanej nazwy domeny (FQDN)?

Nazwy FQDN są określone w kolejności przesyłania dalej i muszą zostać zakończone przez kropkę (na przykład "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Co się stanie, jeśli sprawdzanie poprawności dla podanego wstecznego systemu DNS zakończy się niepowodzeniem?

W przypadku niepowodzenia sprawdzania poprawności odwrotnej usługi DNS nie można wykonać operacji konfigurowania rekordu odwrotnej usługi DNS. Skoryguj wartość odwrotnej usługi DNS zgodnie z potrzebami i spróbuj ponownie.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Czy można skonfigurować odwrotny serwer DNS dla Azure App Service?

Nie. Odwrotny serwer DNS nie jest obsługiwany dla Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Czy mogę skonfigurować wiele rekordów odwrotnej usługi DNS dla usługi platformy Azure?

Nie. Platforma Azure obsługuje pojedynczy rekord odwrotnego systemu DNS dla każdej usługi w chmurze Azure lub PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Czy mogę skonfigurować odwrotny serwer DNS dla zasobów PublicIpAddress IPv6?

Nie. Obecnie platforma Azure obsługuje odwrotną usługę DNS tylko w przypadku zasobów PublicIpAddress IPv4 i Cloud Services.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Czy mogę wysyłać wiadomości e-mail do domen zewnętrznych z usług obliczeniowych platformy Azure?

Możliwość wysyłania wiadomości e-mail bezpośrednio z wdrożenia platformy Azure zależy od typu subskrypcji. Niezależnie od typu subskrypcji firma Microsoft zaleca używanie zaufanych usług przekazywania poczty do wysyłania poczty wychodzącej. Aby uzyskać więcej informacji, zobacz [ulepszone zabezpieczenia platformy Azure na potrzeby wysyłania wiadomości e-mail — Aktualizacja z listopada 2017](../virtual-network/troubleshoot-outbound-smtp-connectivity.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat odwrotnej usługi DNS, zobacz [odwrotne wyszukiwanie DNS w witrynie Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Dowiedz się [, jak hostować strefę wyszukiwania wstecznego dla zakresu adresów IP przypisanych przez usługodawcę internetowego w Azure DNS](dns-reverse-dns-for-azure-services.md).
