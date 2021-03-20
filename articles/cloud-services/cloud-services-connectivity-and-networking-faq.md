---
title: Problemy dotyczące łączności i sieci
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących łączności i sieci dla Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c7b83c615e4ac19e10b5c4f6cc1a102206b1a39a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742424"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Problemy z łącznością i siecią dla platformy Azure Cloud Services (wersja klasyczna): często zadawane pytania

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

W tym artykule opisano często zadawane pytania dotyczące problemów z łącznością i siecią dla [usługi Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Informacje o rozmiarze można znaleźć na [stronie Cloud Services rozmiaru maszyny wirtualnej](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Nie można zarezerwować adresu IP w usłudze w chmurze z obsługą wielodostępności.
Najpierw upewnij się, że wystąpienie maszyny wirtualnej, dla którego próbujesz zarezerwować adres IP, jest włączone. Następnie upewnij się, że są używane zastrzeżone adresy IP dla wdrożeń przejściowych i produkcyjnych. *Nie* zmieniaj ustawień podczas uaktualniania wdrożenia.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Jak mogę używać Pulpit zdalny, gdy mam sieciowej grupy zabezpieczeń?
Dodaj reguły do sieciowej grupy zabezpieczeń, które zezwalają na ruch na portach **3389** i **20000**. Pulpit zdalny używa portu **3389**. Wystąpienia usługi w chmurze są zrównoważone obciążenia, więc nie można bezpośrednio kontrolować tego, z którym wystąpieniem ma zostać nawiązane połączenie. Agenci *RemoteForwarder* i *RemoteAccess* zarządzają ruchem Remote Desktop Protocol (RDP) i umożliwiają klientowi wysyłanie plików cookie protokołu RDP i określanie poszczególnych wystąpień do nawiązania połączenia. Agenci *RemoteForwarder* i *RemoteAccess* wymagają otwartego portu **20000** , który może być zablokowany, jeśli masz sieciowej grupy zabezpieczeń.

## <a name="can-i-ping-a-cloud-service"></a>Czy mogę wysłać polecenie ping do usługi w chmurze?

Nie, nie przy użyciu normalnego protokołu/ICMP "ping". Protokół ICMP nie jest dozwolony za pomocą modułu równoważenia obciążenia platformy Azure.

W celu przetestowania łączności zalecamy wykonanie polecenia ping do portu. Gdy Ping.exe korzysta z protokołu ICMP, można użyć innych narzędzi, takich jak PSPing, Nmap i Telnet, aby przetestować łączność z określonym portem TCP.

Aby uzyskać więcej informacji, zobacz [Używanie ping portów zamiast protokołu ICMP do testowania łączności maszyn wirtualnych platformy Azure](/archive/blogs/mast/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Jak mogę zapobiec otrzymywaniu tysięcy trafień z nieznanych adresów IP, które mogą wskazywać na złośliwe ataki do usługi w chmurze?
Platforma Azure implementuje zabezpieczenia sieci Multilayer, aby chronić usługi platformy przed atakami typu "odmowa usługi" (DDoS). System obrony systemu Azure DDoS jest częścią procesu ciągłego monitorowania platformy Azure, który jest ciągle ulepszany przez testowanie penetracji. Ten system obrony DDoS został zaprojektowany tak, aby nie tylko ataki z zewnątrz, ale również od innych dzierżawców platformy Azure. Aby uzyskać więcej informacji, zobacz [zabezpieczenia sieci platformy Azure](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Można również utworzyć zadanie uruchamiania, aby selektywnie blokować niektóre określone adresy IP. Aby uzyskać więcej informacji, zobacz [blokowanie określonego adresu IP](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Podczas próby połączenia RDP z wystąpieniem usługi w chmurze otrzymuję komunikat "konto użytkownika wygasło".
Może zostać wyświetlony komunikat o błędzie "to konto użytkownika wygasło" po pominięciu daty wygaśnięcia skonfigurowanej w ustawieniach protokołu RDP. Datę wygaśnięcia można zmienić w portalu, wykonując następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com), przejdź do usługi w chmurze i wybierz kartę **pulpit zdalny** .

2. Wybierz miejsce wdrożenia **produkcyjnego** lub **przejściowego** .

3. Zmień datę **wygaśnięcia** , a następnie Zapisz konfigurację.

Teraz powinno być możliwe nawiązanie połączenia RDP z maszyną.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Dlaczego usługa Azure Load Balancer nie równoważy równo ruchu?
Aby uzyskać informacje o działaniu wewnętrznego modułu równoważenia obciążenia, zobacz [Azure Load Balancer nowy tryb dystrybucji](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Używany algorytm dystrybucji to skrót 5 (źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i typ protokołu) służący do mapowania ruchu do dostępnych serwerów. Zawiera lepkość tylko w ramach sesji transportu. Pakiety w tej samej sesji TCP lub UDP są kierowane do tego samego wystąpienia adresu IP centrum danych (DIP) za punktem końcowym ze zrównoważonym obciążeniem. Gdy klient zamknie i ponownie otworzy połączenie lub uruchomi nową sesję z tego samego źródłowego adresu IP, port źródłowy zmieni się i spowoduje, że ruch przejdzie do innego punktu końcowego DIP.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Jak można przekierować ruch przychodzący do domyślnego adresu URL mojej usługi w chmurze na niestandardowy adres URL?

Moduł ponownego zapisywania adresów URL usług IIS może służyć do przekierowywania ruchu, który jest dostarczany do domyślnego adresu URL dla usługi w chmurze (na przykład \* . cloudapp.NET) do niestandardowej nazwy/adresu URL. Ponieważ moduł ponownego zapisywania adresu URL jest domyślnie włączony w rolach sieci Web, a jego reguły są konfigurowane w web.config aplikacji, jest on zawsze dostępny na maszynie wirtualnej niezależnie od ponownych uruchomień/odniesień. Aby uzyskać więcej informacji, zobacz:

- [Utwórz reguły ponownego zapisywania dla modułu ponownego zapisywania adresu URL](/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Usuwanie linku domyślnego](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Jak zablokować lub wyłączyć ruch przychodzący do domyślnego adresu URL mojej usługi w chmurze?

Można zapobiec przychodzącemu ruchowi do domyślnego adresu URL/nazwy usługi w chmurze (na przykład \* . cloudapp.NET). Ustaw nagłówek hosta na niestandardową nazwę DNS (na przykład www \. MyCloudService.com) w obszarze Konfiguracja powiązania witryny w pliku definicji usługi w chmurze (*. csdef), jak wskazano poniżej:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Ponieważ to powiązanie nagłówka hosta jest wymuszane za pośrednictwem pliku csdef, usługa jest dostępna tylko za pośrednictwem niestandardowej nazwy "www.MyCloudService.com". Wszystkie żądania przychodzące do domeny "*. cloudapp.net" zawsze kończą się niepowodzeniem. W przypadku użycia niestandardowej sondy modułu równoważenia obciążenia lub wewnętrznego modułu ładowania w usłudze blokowanie domyślnego adresu URL/nazwy usługi może kolidować z zachowaniem sondowania.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Jak upewnić się, że publiczny adres IP usługi w chmurze nigdy nie ulega zmianie?

Aby upewnić się, że publiczny adres IP usługi w chmurze (znany również jako adres VIP) nigdy nie zmienia się, tak aby można go było zwyczajowo zatwierdzić przez kilku konkretnych klientów, zalecamy zastrzeżony adres IP. W przeciwnym razie wirtualny adres IP dostarczony przez platformę Azure zostanie cofnięty po usunięciu wdrożenia. W przypadku pomyślnej operacji zamiany adresu VIP wymagane są indywidualne zastrzeżone adresy IP dla miejsc produkcyjnych i przejściowych. Bez nich operacja zamiany kończy się niepowodzeniem. Aby zarezerwować adres IP i skojarzyć go z usługą w chmurze, zobacz następujące artykuły:

- [Zarezerwuj adres IP istniejącej usługi w chmurze](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [Kojarzenie zastrzeżonego adresu IP z usługą w chmurze przy użyciu pliku konfiguracji usługi](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Jeśli masz więcej niż jedno wystąpienie dla ról, skojarzenie protokołu RIP z usługą w chmurze nie powinno powodować przestojów. Alternatywnie możesz dodać zakres adresów IP centrum danych platformy Azure do listy dozwolonych. Wszystkie zakresy adresów IP platformy Azure można znaleźć w [Centrum pobierania Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Ten plik zawiera zakresy adresów IP (w tym obliczenia, SQL i zakresy magazynu) używane w centrach danych platformy Azure. Zaktualizowany plik jest publikowany co tydzień, co odzwierciedla aktualnie wdrożone zakresy i wszystkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych przez co najmniej jeden tydzień. Pobierz nowy plik XML co tydzień i wykonaj niezbędne zmiany w witrynie, aby prawidłowo identyfikować usługi działające na platformie Azure. Użytkownicy usługi Azure ExpressRoute mogą zauważyć, że ten plik służy do aktualizowania anonsu usługi Azure Space w pierwszym tygodniu każdego miesiąca.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Jak mogę używać Azure Resource Manager sieci wirtualnych z usługami w chmurze?

Usług Cloud Services nie można umieszczać w Azure Resource Manager sieci wirtualnych. Menedżer zasobów sieci wirtualnych i klasycznych sieci wirtualnych wdrożenia mogą być połączone za pomocą komunikacji równorzędnej. Aby uzyskać więcej informacji, zobacz [wirtualne sieci równorzędne](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Jak uzyskać listę publicznych adresów IP używanych przez moją Cloud Services?

Możesz użyć następującego skryptu PS, aby uzyskać listę publicznych adresów IP dla Cloud Services w ramach subskrypcji

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```