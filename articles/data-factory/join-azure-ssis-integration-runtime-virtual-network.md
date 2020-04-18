---
title: Dołączanie środowiska Azure SSIS Integration Runtime do sieci wirtualnej
description: Dowiedz się, jak dołączyć do środowiska wykonawczego integracji platformy Azure-SSIS z siecią wirtualną platformy Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: c899d3b4d132ee51679f8c7b4fd1c578417500db
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605271"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Dołączanie środowiska Azure SSIS Integration Runtime do sieci wirtualnej

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Korzystając z usług SQL Server Integration Services (SSIS) w usłudze Azure Data Factory, należy dołączyć środowisko uruchomieniowe integracji platformy Azure-SSIS (IR) do sieci wirtualnej platformy Azure w następujących scenariuszach:

- Chcesz połączyć się z lokalnymi magazynami danych z pakietów SSIS, które są uruchamiane na platformie Azure-SSIS IR bez konfigurowania ir hostowanego samodzielnie jako serwera proxy. 

- Chcesz hostować bazę danych wykazu SSIS (SSISDB) w bazie danych SQL Azure z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym. 

- Chcesz połączyć się z zasobami platformy Azure skonfigurowane za pomocą punktów końcowych usługi sieci wirtualnej z pakietów SSIS, które są uruchamiane na platformie Azure-SSIS IR.

- Chcesz połączyć się z magazynami danych/zasobami skonfigurowanym przy regułach zapory IP z pakietów SSIS, które są uruchamiane na platformie Azure-SSIS IR.

Usługa Data Factory umożliwia dołączenie usługi Azure-SSIS IR do sieci wirtualnej utworzonej za pośrednictwem klasycznego modelu wdrażania lub modelu wdrażania usługi Azure Resource Manager.

> [!IMPORTANT]
> Klasyczna sieć wirtualna jest przestarzała, więc zamiast tego użyj sieci wirtualnej usługi Azure Resource Manager.  Jeśli używasz już klasycznej sieci wirtualnej, przełącz się do sieci wirtualnej usługi Azure Resource Manager tak szybko, jak to możliwe.

[Konfigurowanie środowiska uruchomieniowego integracji usługi Azure-SQL Server Integration Services (SSIS) (IR) w celu dołączenia do samouczka sieci wirtualnej](tutorial-deploy-ssis-virtual-network.md) zawiera minimalne kroki za pośrednictwem witryny Azure portal. W tym artykule rozwija się samouczek i opisano wszystkie zadania opcjonalne:

- Jeśli korzystasz z sieci wirtualnej (klasycznej).
- Jeśli przywieziesz własne publiczne adresy IP dla usługi Azure-SSIS IR.
- Jeśli używasz własnego serwera DNS (Domain Name System).
- Jeśli używasz sieciowej grupy zabezpieczeń (NSG) w podsieci.
- Jeśli używasz usługi Azure ExpressRoute lub trasy zdefiniowanej przez użytkownika (UDR).
- Jeśli używasz dostosowane Azure-SSIS IR.
- Jeśli używasz inicjowania obsługi administracyjnej usługi Azure Powershell.

## <a name="access-to-on-premises-data-stores"></a>Dostęp do lokalnych magazynów danych

Jeśli pakiety SSIS uzyskują dostęp do lokalnych magazynów danych, można dołączyć do usługi Azure-SSIS IR do sieci wirtualnej, która jest połączona z siecią lokalną. Możesz też skonfigurować i zarządzać samodzielnie hostowanym podczerwem jako serwerem proxy dla usługi Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Konfigurowanie samodzielnie hostowanego podczerwona jako serwera proxy dla usługi Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Dołączając do usługi Azure-SSIS IR do sieci wirtualnej, należy pamiętać o następujących ważnych kwestiach: 

- Jeśli żadna sieć wirtualna nie jest połączona z siecią lokalną, najpierw utwórz [sieć wirtualną usługi Azure Resource Manager,](../virtual-network/quick-create-portal.md#create-a-virtual-network) aby dołączyć do usługi Azure-SSIS IR. Następnie skonfiguruj [połączenie bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) lokacja lokacja lub połączenie Usługi [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) z tej sieci wirtualnej do sieci lokalnej. 

- Jeśli sieć wirtualna usługi Azure Resource Manager jest już połączona z siecią lokalną w tej samej lokalizacji co usługa Azure-SSIS IR, można dołączyć do tej sieci wirtualnej podczerwony. 

- Jeśli klasyczna sieć wirtualna jest już połączona z siecią lokalną w innej lokalizacji niż usługa Azure-SSIS IR, można utworzyć [sieć wirtualną usługi Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) dla usługi Azure-SSIS IR do dołączenia. Następnie skonfiguruj połączenie sieci wirtualnej usługi Azure Resource Manager.Then configure a [classic-to-Azure Resource Manager virtual network](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) connection. 
 
- Jeśli sieć wirtualna usługi Azure Resource Manager jest już połączona z siecią lokalną w innej lokalizacji niż usługa Azure-SSIS IR, można najpierw utworzyć [sieć wirtualną usługi Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) dla usługi Azure-SSIS IR do dołączenia. Następnie skonfiguruj połączenie sieci wirtualnej usługi Azure Resource Manager-to-Azure Resource Manager. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hostowanie katalogu SSIS w bazie danych SQL

Jeśli hostujesz katalog SSIS w bazie danych SQL Azure z punktami końcowymi usługi sieci wirtualnej, upewnij się, że łączysz urządzenie Azure-SSIS IR z tą samą siecią wirtualną i podsiecią.

Jeśli hostujesz katalog SSIS w wystąpieniu zarządzanym z prywatnym punktem końcowym, upewnij się, że łączysz usługę Azure-SSIS IR w tej samej sieci wirtualnej, ale w innej podsieci niż wystąpienie zarządzane. Aby dołączyć do usługi Azure-SSIS IR do innej sieci wirtualnej niż wystąpienie zarządzane, zaleca się równość sieci wirtualnej (która jest ograniczona do tego samego regionu) lub połączenie z sieci wirtualnej do sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Łączenie aplikacji z wystąpieniem zarządzanym usługi Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="access-to-azure-services"></a>Dostęp do usług platformy Azure

Jeśli pakiety SSIS uzyskują dostęp do zasobów platformy Azure, które obsługują [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) i chcesz zabezpieczyć dostęp do tych zasobów z usługi Azure-SSIS IR, możesz dołączyć do usługi Azure-SSIS IR do podsieci sieci wirtualnej skonfigurowanego dla punktów końcowych usługi sieci wirtualnej, a następnie dodać regułę sieci wirtualnej do odpowiednich zasobów platformy Azure, aby umożliwić dostęp z tej samej podsieci.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Dostęp do źródeł danych chronionych przez regułę zapory IP

Jeśli pakiety SSIS uzyskują dostęp do magazynów danych/zasobów, które zezwalają tylko na określone statyczne publiczne adresy IP i chcesz zabezpieczyć dostęp do tych zasobów z usługi Azure-SSIS IR, możesz przynieść własne [publiczne adresy IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) dla usługi Azure-SSIS IR podczas łączenia go z siecią wirtualną, a następnie dodać regułę zapory IP do odpowiednich zasobów, aby umożliwić dostęp z tych adresów IP.

We wszystkich przypadkach sieć wirtualną można wdrożyć tylko za pośrednictwem modelu wdrażania usługi Azure Resource Manager.

W poniższych sekcjach podano więcej szczegółów. 

## <a name="virtual-network-configuration"></a>Konfiguracja sieci wirtualnej

Skonfiguruj sieć wirtualną tak, aby spełniała następujące wymagania: 

- Upewnij się, że `Microsoft.Batch` jest zarejestrowanym dostawcą w ramach subskrypcji podsieci sieci wirtualnej, która obsługuje usługę Azure-SSIS IR. Jeśli używasz klasycznej sieci `MicrosoftAzureBatch` wirtualnej, dołącz również do roli współautora klasycznej maszyny wirtualnej dla tej sieci wirtualnej. 

- Upewnij się, że masz wymagane uprawnienia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uprawnień](#perms).

- Wybierz odpowiednią podsieć do obsługi usługi Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Wybieranie podsieci](#subnet). 

- Jeśli przywieziesz własne publiczne adresy IP dla usługi Azure-SSIS IR, zobacz [Wybieranie statycznych publicznych adresów IP](#publicIP)

- Jeśli używasz własnego serwera DNS (Domain Name System) w sieci wirtualnej, zobacz [Konfigurowanie serwera DNS](#dns_server). 

- Jeśli używasz sieciowej grupy zabezpieczeń w podsieci, zobacz [Konfigurowanie sieciowej grupy zabezpieczeń](#nsg). 

- Jeśli używasz usługi Azure ExpressRoute lub trasy zdefiniowanej przez użytkownika (UDR), zobacz [Korzystanie z usługi Azure ExpressRoute lub UDR.](#route) 

- Upewnij się, że grupa zasobów sieci wirtualnej (lub grupa zasobów publicznych adresów IP, jeśli wprowadzisz własne publiczne adresy IP) może tworzyć i usuwać niektóre zasoby sieci platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie grupy zasobów](#resource-group). 

- Jeśli dostosujesz podczerwony usługi Azure-SSIS zgodnie z opisem w [ustawieniach niestandardowych dla usługi Azure-SSIS IR,](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)twoje węzły IR usługi Azure-SSIS otrzymają prywatne adresy IP ze wstępnie zdefiniowanego zakresu od 172.16.0.0 do 172.31.255.255. Upewnij się więc, że zakresy prywatnych adresów IP sieci wirtualnych lub lokalnych nie zderzają się z tym zakresem.

Ten diagram przedstawia wymagane połączenia dla usługi Azure-SSIS IR:

![Środowisko IR Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a>Konfigurowanie uprawnień

Użytkownik, który tworzy azure-SSIS IR musi mieć następujące uprawnienia:

- Jeśli łączysz urządzenie SSIS IR z siecią wirtualną usługi Azure Resource Manager, masz dwie opcje:

  - Użyj wbudowanej roli Współautor sieci. Ta rola jest dostępna z uprawnieniem _Microsoft.Network/,\* _ które ma znacznie większy zakres niż jest to konieczne.

  - Utwórz rolę niestandardową, która zawiera tylko niezbędne _uprawnienie Microsoft.Network/virtualNetworks/\*/join/action._ Jeśli chcesz również przynieść własne publiczne adresy IP dla usługi Azure-SSIS IR podczas łączenia go do sieci wirtualnej usługi Azure Resource Manager, dołącz również _Microsoft.Network/publicIPAddresses/*/join/action_ permission w roli.

- Jeśli łączysz urządzenie SSIS IR z klasyczną siecią wirtualną, zalecamy użycie wbudowanej roli współautora klasycznej maszyny wirtualnej. W przeciwnym razie należy zdefiniować rolę niestandardową, która zawiera uprawnienie do dołączenia do sieci wirtualnej.

### <a name="select-the-subnet"></a><a name="subnet"></a>Wybierz podsieć

Po wybraniu podsieci: 

- Nie należy wybierać GatewaySubnet do wdrożenia usługi Azure-SSIS IR. Jest przeznaczony dla bram sieci wirtualnej. 

- Upewnij się, że wybrana podsieć ma wystarczającą ilość dostępnej przestrzeni adresowej dla usługi Azure-SSIS IR do użycia. Pozostaw dostępne adresy IP dla co najmniej dwa razy numeru węzła podczerwego. Platforma Azure rezerwuje niektóre adresy IP w każdej podsieci. Tych adresów nie można używać. Pierwszy i ostatni adres IP podsieci są zarezerwowane dla zgodności protokołu, a trzy kolejne adresy są używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Nie należy używać podsieci, która jest zajęta wyłącznie przez inne usługi platformy Azure (na przykład wystąpienie zarządzanej bazy danych SQL, usługa App Service i tak dalej). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Wybieranie statycznych publicznych adresów IP

Jeśli chcesz wprowadzić własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR podczas łączenia go do sieci wirtualnej, upewnij się, że spełniają one następujące wymagania:

- Dokładnie dwa nieużywane te, które nie są już skojarzone z innymi zasobami platformy Azure powinny być dostarczone. Dodatkowy będzie używany podczas okresowego uaktualniania usługi Azure-SSIS IR. Należy zauważyć, że jeden publiczny adres IP nie może być współużytkowane przez aktywne usługi Azure-SSIS IRs.

- Oba powinny być statyczne typu standardowego. Aby uzyskać więcej informacji, zapoznaj się [z jednostkami SKU publicznego adresu IP.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku)

- Oba powinny mieć nazwę DNS. Jeśli podczas ich tworzenia nie podano nazwy DNS, można to zrobić w witrynie Azure portal.

![Środowisko IR Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Oni i sieć wirtualna powinny być w ramach tej samej subskrypcji i w tym samym regionie.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a>Konfigurowanie serwera DNS 
Jeśli chcesz użyć własnego serwera DNS w sieci wirtualnej połączonej z usługą Azure-SSIS IR w celu rozpoznania nazwy hosta prywatnego, `<your storage account>.blob.core.windows.net`upewnij się, że może on również rozpoznawać globalne nazwy hostów platformy Azure (na przykład obiekt blob usługi Azure Storage o nazwie ). 

Poniżej znajduje się jedno z zalecanych podejść: 

-   Skonfiguruj niestandardowy system DNS do przesyłania dalej żądań do usługi Azure DNS. Nierozpoznane rekordy DNS można przesyłać dalej na adres IP cyklicznych programów rozpoznawania nazw platformy Azure (168.63.129.16) na własnym serwerze DNS. 

Aby uzyskać więcej informacji, zobacz [Rozpoznawanie nazw, które używa własnego serwera DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Użyj w pełni kwalifikowanej nazwy domeny (FQDN) dla nazwy hosta prywatnego, na przykład użyj `<your_private_server>.contoso.com` zamiast `<your_private_server>`, ponieważ usługa Azure-SSIS IR nie będzie automatycznie dołączać własnego sufiksu DNS.

### <a name="set-up-an-nsg"></a><a name="nsg"></a>Konfigurowanie nsg
Jeśli chcesz zaimplementować sieciowej sieciowej dla podsieci używanej przez usługę Azure-SSIS IR, zezwalaj na ruch przychodzący i wychodzący za pośrednictwem następujących portów: 

-   **Wymagania dotyczące ruchu przychodzącego usługi Azure-SSIS IR**

| Kierunek | Protokół transportu | Element źródłowy | Zakres portów źródłowych | Element docelowy | Zakres portów docelowych | Komentarze |
|---|---|---|---|---|---|---|
| Przychodzący | TCP | PartiaNodeZarządzanie | * | VirtualNetwork | 29876, 29877 (jeśli dołączysz do podczerwenia do sieci wirtualnej Menedżera zasobów) <br/><br/>10100, 20100, 30100 (jeśli dołączysz do podczerwenia do klasycznej sieci wirtualnej)| Usługa Data Factory używa tych portów do komunikowania się z węzłami usługi Azure-SSIS IR w sieci wirtualnej. <br/><br/> Niezależnie od tego, czy tworzysz sieć sieciową na poziomie podsieci, usługa Data Factory zawsze konfiguruje sieć sieciową na poziomie kart interfejsu sieciowego (NIC) dołączonych do maszyn wirtualnych hostujących usługę Azure-SSIS IR. Tylko ruch przychodzący z adresów IP fabryki danych na określonych portach jest dozwolony przez tę sieć sieciową na poziomie karty sieciowej. Nawet jeśli te porty zostaną otwarte dla ruchu internetowego na poziomie podsieci, ruch z adresów IP, które nie są adresami IP fabryki danych, jest blokowany na poziomie karty sieciowej. |
| Przychodzący | TCP | Piła corpNet | * | VirtualNetwork | 3389 | (Opcjonalnie) Ta reguła jest wymagana tylko wtedy, gdy obsługa firmy Microsoft prosi klienta o otwarcie w celu zaawansowanego rozwiązywania problemów i może zostać zamknięta zaraz po rozwiązaniu problemu. Tag usługi **CorpNetSaw** zezwala na korzystanie z pulpitu zdalnego tylko stacje robocze bezpiecznego dostępu w sieci firmowej firmy Microsoft. I ten tag usługi nie można wybrać z portalu i jest dostępny tylko za pośrednictwem programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. <br/><br/> Na poziomie sieciowej sieciowej sieciowej port 3389 jest domyślnie otwarty i umożliwia sterowanie portem 3389 na poziomie sieciowej sieciowej sieciowej, tymczasem usługa Azure-SSIS IR domyślnie wyłączyła port 3389 wychodzący w regule zapory systemu Windows w każdym węźle podczerwieni w celu ochrony. |
||||||||

-   **Wymagania dotyczące ruchu wychodzącego usługi Azure-SSIS IR**

| Kierunek | Protokół transportu | Element źródłowy | Zakres portów źródłowych | Element docelowy | Zakres portów docelowych | Komentarze |
|---|---|---|---|---|---|---|
| Wychodzący | TCP | VirtualNetwork | * | AzureCloud | 443 | Węzły usługi Azure-SSIS IR w sieci wirtualnej używają tego portu do uzyskiwania dostępu do usług platformy Azure, takich jak Usługa Azure Storage i usługi Azure Event Hubs. |
| Wychodzący | TCP | VirtualNetwork | * | Internet | 80 | (Opcjonalnie) Węzły usługi Azure-SSIS IR w sieci wirtualnej używają tego portu do pobierania listy odwołania certyfikatów z Internetu. Jeśli zablokujesz ten ruch, może wystąpić obniżenie wydajności podczas uruchamiania podczerwone i utratę możliwości sprawdzania listy odwołania certyfikatów dla użycia certyfikatu. Jeśli chcesz jeszcze bardziej zawęzić miejsce docelowe do określonych sieci FQDN, zapoznaj się **z sekcją Użyj usługi Azure ExpressRoute lub UDR**|
| Wychodzący | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | (Opcjonalnie) Ta reguła jest wymagana tylko wtedy, gdy węzły usługi Azure-SSIS IR w sieci wirtualnej uzyskują dostęp do usługi SSISDB hostowanego przez serwer bazy danych SQL. Jeśli zasady połączenia serwera bazy danych SQL są ustawione na **serwer proxy** zamiast **przekierowania,** potrzebny jest tylko port 1433. <br/><br/> Ta reguła zabezpieczeń wychodzących nie ma zastosowania do usługi SSISDB hostowanego przez wystąpienie zarządzane w sieci wirtualnej lub na serwerze usługi Azure Database skonfigurowanym z prywatnym punktem końcowym. |
| Wychodzący | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (Opcjonalnie) Ta reguła jest wymagana tylko wtedy, gdy węzły usługi Azure-SSIS IR w sieci wirtualnej uzyskują dostęp do usługi SSISDB hostowanego przez wystąpienie zarządzane w sieci wirtualnej lub serwerze bazy danych platformy Azure skonfigurowanym z prywatnym punktem końcowym. Jeśli zasady połączenia serwera bazy danych SQL są ustawione na **serwer proxy** zamiast **przekierowania,** potrzebny jest tylko port 1433. |
| Wychodzący | TCP | VirtualNetwork | * | Magazyn | 445 | (Opcjonalnie) Ta reguła jest wymagana tylko wtedy, gdy chcesz wykonać pakiet SSIS przechowywany w usłudze Azure Files. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a>Korzystanie z usługi Azure ExpressRoute lub UDR
Jeśli chcesz sprawdzić ruch wychodzący z usługi Azure-SSIS IR, możesz kierować ruch inicjowany z usługi Azure-SSIS IR do lokalnego urządzenia zapory za pośrednictwem [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) force tunneling (reklamowanie trasy BGP, 0.0.0.0/0, do sieci wirtualnej) lub do sieciowego urządzenia wirtualnego (NVA) jako zapora lub [platforma Azure zapory](https://docs.microsoft.com/azure/firewall/) za pośrednictwem [UDR.](../virtual-network/virtual-networks-udr-overview.md) 

![Scenariusz NVA dla usługi Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Musisz zrobić poniżej rzeczy, aby cały scenariusz działał
   -   Ruch przychodzący między usługami zarządzania usługami Azure Batch a usługą Azure-SSIS IR nie może być kierowany za pośrednictwem urządzenia zapory.
   -   Urządzenie zapory umożliwia ruch wychodzący wymagany przez usługę Azure-SSIS IR.

Ruch przychodzący między usługami zarządzania usługami Azure Batch i azure-SSIS IR nie może być kierowany do urządzenia zapory, w przeciwnym razie ruch zostanie przerwany z powodu problemu z routingiem asymetrycznym. Trasy muszą być zdefiniowane dla ruchu przychodzącego, aby ruch mógł odpowiedzieć w taki sam sposób, w jaki wszedł. Można zdefiniować określone UDR do kierowania ruchu między usługami zarządzania wsadową platformy Azure i Azure-SSIS IR z następnym typem przeskoku jako **Internet.**

Na przykład, jeśli usługa Azure-SSIS `UK South` IR znajduje się na stronie i chcesz sprawdzić ruch wychodzący za `BatchNodeManagement.UKSouth` pośrednictwem zapory platformy Azure, najpierw otrzymasz listę tagów zakresu IP tagu usługi z [łącza pobierania zakresu adresów IP tagów usług](https://www.microsoft.com/download/details.aspx?id=56519) lub za pośrednictwem interfejsu API [odnajdywania tagów usługi.](https://aka.ms/discoveryapi) Następnie zastosuj następujące UDR powiązanych tras zakresu IP z następnym typem przeskoku jako **Internet** wraz z trasą 0.0.0.0/0 z następnym typem przeskoku jako **urządzenie wirtualne**.

![Ustawienia UDR usługi Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Takie podejście wiąże się z dodatkowymi kosztami konserwacji. Regularnie sprawdzaj zakres adresów IP i dodawaj nowe zakresy adresów IP do konta UDR, aby uniknąć złamania identyfikatora IR usługi Azure-SSIS. Zalecamy sprawdzanie zakresu IP co miesiąc, ponieważ gdy nowy adres IP pojawi się w tagu usługi, adres IP zajmie kolejny miesiąc. 

Aby ułatwić konfigurację reguł UDR, można uruchomić następujący skrypt programu Powershell, aby dodać reguły UDR dla usług azure batch management services:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

Aby urządzenie zapory zezwalać na ruch wychodzący, musisz zezwolić na ruch wychodzący poniżej portów taki sam jak wymagania w regułach wychodzących sieciowej sieciowej.
-   Port 443 z miejscem docelowym jako usługami w chmurze azure.

    Jeśli używasz Zapory platformy Azure, możesz określić regułę sieci za pomocą tagu usługi AzureCloud. W przypadku zapory innych typów można po prostu zezwolić na miejsce docelowe jako wszystkie dla portu 443 lub zezwolić na poniżej sieci FQDN na podstawie typu środowiska platformy Azure:

    | Środowisko platformy Azure | Punkty końcowe                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure — publiczna      | <ul><li><b>Usługa Azure Data Factory (zarządzanie)</b><ul><li>\*frontend.clouddatahub.net .frontend.clouddatahub.net</li></ul></li><li><b>Usługa Azure Storage (zarządzanie)</b><ul><li>\*.blob.core.windows.net</li><li>\*table.core.windows.net .table.core.windows.net</li></ul></li><li><b>Usługa Azure Container Registry (konfiguracja niestandardowa)</b><ul><li>\*azurecr.io .azurecr.io</li></ul></li><li><b>Centrum zdarzeń (rejestrowanie)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Usługa rejestrowania firmy Microsoft (użycie wewnętrzne)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Usługa Azure Data Factory (zarządzanie)</b><ul><li>\*frontend.datamovement.azure.us .</li></ul></li><li><b>Usługa Azure Storage (zarządzanie)</b><ul><li>\*blob.core.usgovcloudapi.net .blob.core.usgovcloudapi.net</li><li>\*table.core.usgovcloudapi.net .table.core.usgovcloudapi.net</li></ul></li><li><b>Usługa Azure Container Registry (konfiguracja niestandardowa)</b><ul><li>\*azurecr.us .</li></ul></li><li><b>Centrum zdarzeń (rejestrowanie)</b><ul><li>\*servicebus.usgovcloudapi.net .</li></ul></li><li><b>Usługa rejestrowania firmy Microsoft (użycie wewnętrzne)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure w Chinach — 21Vianet     | <ul><li><b>Usługa Azure Data Factory (zarządzanie)</b><ul><li>\*frontend.datamovement.azure.cn .frontend.datamovement.azure.cn</li></ul></li><li><b>Usługa Azure Storage (zarządzanie)</b><ul><li>\*blob.core.chinacloudapi.cn .</li><li>\*table.core.chinacloudapi.cn .table.core.chinacloudapi.cn</li></ul></li><li><b>Usługa Azure Container Registry (konfiguracja niestandardowa)</b><ul><li>\*azurecr.cn .</li></ul></li><li><b>Centrum zdarzeń (rejestrowanie)</b><ul><li>\*servicebus.chinacloudapi.cn .</li></ul></li><li><b>Usługa rejestrowania firmy Microsoft (użycie wewnętrzne)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Jeśli chodzi o nazwy FQDN usługi Azure Storage, Azure Container Registry i Event Hub, można również włączyć następujące punkty końcowe usługi dla sieci wirtualnej, tak aby ruch sieciowy do tych punktów końcowych przechodzi przez sieć szkieletową platformy Azure zamiast być kierowane do urządzenia zapory:
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   Port 80 z miejscem docelowym jako witrynami do pobierania CRL.

    Użytkownik zezwala na pobieranie witryn certyfikatów dla platformy Azure-SSIS IR dla poniższych sieci FQDN, które są używane jako lista crl (lista odwołań certyfikatów):
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Jeśli używasz certyfikatów o różnych crl, zaleca się, aby uwzględnić je również. Możesz to przeczytać, aby dowiedzieć się więcej na [liście odwołania certyfikatów](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

    Jeśli nie zezwalasz na ten ruch, może wystąpić obniżenie wydajności podczas uruchamiania usługi Azure-SSIS IR i utratę możliwości sprawdzania listy odwołania certyfikatów dla użycia certyfikatu, co nie jest zalecane z punktu widzenia zabezpieczeń.

-   Port 1433, 11000-11999 z miejscem docelowym jako sql azure (wymagane tylko wtedy, gdy węzły usługi Azure-SSIS IR w sieci wirtualnej dostępu SSISDB hostowane przez serwer bazy danych SQL).

    Jeśli używasz Zapory platformy Azure, możesz określić regułę sieciową za pomocą tagu usługi Azure SQL, w przeciwnym razie możesz zezwolić na miejsce docelowe jako określony adres URL azure sql w urządzeniu zapory.

-   Port 445 z miejscem docelowym jako usługa Azure Storage (wymagany tylko podczas wykonywania pakietu SSIS przechowywanego w plikach platformy Azure).

    Jeśli używasz Zapory platformy Azure, można określić regułę sieci za pomocą tagu usługi magazynu, w przeciwnym razie można zezwolić na miejsce docelowe jako określony adres URL magazynu plików platformy Azure w urządzeniu zapory.

> [!NOTE]
> W przypadku usługi Azure SQL i usługi Storage, jeśli skonfigurujesz punkty końcowe usługi sieci wirtualnej w podsieci, ruch między usługą Azure-SSIS IR i azure SQL w tym samym regionie \ Usługa Azure Storage w tym samym regionie lub sparowanym regionie zostanie przekierowany bezpośrednio do sieci szkieletowej platformy Microsoft Azure zamiast urządzenia zapory.

Jeśli nie potrzebujesz możliwości sprawdzania ruchu wychodzącego usługi Azure-SSIS IR, możesz po prostu zastosować trasę, aby wymusić cały ruch do następnego typu przeskoku **Internet:**

-   W scenariuszu azure expressroute można zastosować 0.0.0.0/0 trasy z następnym typem przeskoku jako **Internet** w podsieci, która obsługuje azure-SSIS IR. 
-   W scenariuszu urządzenia WUS można zmodyfikować istniejącą trasę 0.0.0.0/0 zastosowaną w podsieci, która obsługuje podsieć Azure-SSIS IR z następnego typu przeskoku jako **urządzenie wirtualne** do **Internetu**.

![Dodawanie trasy](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Określ trasę z następnym typem przeskoku **Internet** nie oznacza, że cały ruch będzie przechodził przez Internet. Tak długo, jak adres docelowy jest dla jednej z usług platformy Azure, platforma Azure kieruje ruch bezpośrednio do usługi za pośrednictwem sieci szkieletowej platformy Azure, zamiast kierować ruch do Internetu.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a>Konfigurowanie grupy zasobów

Usługa Azure-SSIS IR musi utworzyć pewne zasoby sieciowe w ramach tej samej grupy zasobów co sieć wirtualna. Zasoby te obejmują:
- Moduł równoważenia obciążenia platformy Azure o nazwie * \<Guid>-azurebatch-cloudserviceloadbalancer*.
- Publiczny adres IP platformy Azure o nazwie * \<Guid>-azurebatch-cloudservicepublicip*.
- Grupa zabezpieczeń pracy sieciowej o nazwie * \<Guid>-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Teraz możesz przynieść własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR. W tym scenariuszu utworzymy tylko moduł równoważenia obciążenia platformy Azure i grupę zabezpieczeń sieci w ramach tej samej grupy zasobów, co statyczne publiczne adresy IP zamiast sieci wirtualnej.

Te zasoby zostaną utworzone po uruchomieniu usługi Azure-SSIS IR. Zostaną one usunięte po zatrzymaniu usługi Azure-SSIS IR. Jeśli wprowadzisz własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR, twoje własne statyczne publiczne adresy IP nie zostaną usunięte po zatrzymaniu usługi Azure-SSIS IR. Aby uniknąć blokowania zatrzymania usługi Azure-SSIS IR, nie należy ponownie używać tych zasobów sieciowych w innych zasobach.

Upewnij się, że nie masz blokady zasobów w grupie zasobów/subskrypcji, do której należą sieci wirtualne/statyczne publiczne adresy IP. Jeśli skonfigurujesz blokadę tylko do odczytu/usunięcia, uruchamianie i zatrzymywanie usługi Azure-SSIS IR zakończy się niepowodzeniem lub przestanie odpowiadać.

Upewnij się, że nie masz zasad platformy Azure, które uniemożliwiają tworzenie następujących zasobów w ramach grupy zasobów/subskrypcji, do której należą sieci wirtualnej/statycznych publicznych adresów IP: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

Upewnij się, że przydział zasobów subskrypcji jest wystarczający dla powyższych trzech zasobów sieciowych. W szczególności dla każdego środowiska Azure-SSIS IR utworzonego w sieci wirtualnej należy zarezerwować dwa przydziały wolne dla każdego z powyższych trzech zasobów sieciowych. Dodatkowy jeden przydział będzie używany podczas okresowego uaktualniania usługi Azure-SSIS IR.

### <a name="faq"></a><a name="faq"></a>FAQ

- Jak chronić publiczny adres IP ujawniony w moim usłudze Azure-SSIS IR dla połączenia przychodzącego? Czy możliwe jest usunięcie publicznego adresu IP?
 
  W tej chwili publiczny adres IP zostanie automatycznie utworzony, gdy usługa Azure-SSIS IR dołączy do sieci wirtualnej. Mamy sieci ową siecią sieciową na poziomie sieciowej sieciowej sieciowej, która umożliwia tylko usługi zarządzania partią Azure na łączenie przychodzące z urządzeniem Azure-SSIS IR. Można również określić sieci zabezpieczeń na poziomie podsieci dla ochrony ruchu przychodzącego.

  Jeśli nie chcesz, aby nie można ujawnić żadnego publicznego adresu IP, należy rozważyć [skonfigurowanie samodzielnie hostowanego podczerwony jako serwera proxy dla usługi Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) zamiast dołączania do usługi Azure-SSIS IR do sieci wirtualnej, jeśli dotyczy to w danym scenariuszu.
 
- Czy mogę dodać publiczny adres IP mojego usługi Azure-SSIS IR do listy dozwolonych zapory dla moich źródeł danych?

  Teraz możesz przynieść własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR. W takim przypadku można dodać adresy IP do listy dozwolonych zapory dla źródeł danych. Można również rozważyć inne opcje poniżej, aby zabezpieczyć dostęp do danych z usługi Azure-SSIS IR w zależności od scenariusza:

  - Jeśli źródło danych jest lokalne, po podłączeniu sieci wirtualnej do sieci lokalnej i połączeniu urządzenia Azure-SSIS IR z podsiecią sieci wirtualnej, można następnie dodać zakres prywatnych adresów IP tej podsieci do listy dozwolonych zapory dla źródła danych.
  - Jeśli źródłem danych jest usługa platformy Azure, która obsługuje punkty końcowe usługi sieci wirtualnej, można skonfigurować punkt końcowy usługi sieci wirtualnej w podsieci sieci wirtualnej i dołączyć do usługi Azure-SSIS IR do tej podsieci. Następnie można dodać regułę sieci wirtualnej z tą podsiecią do zapory dla źródła danych.
  - Jeśli źródło danych jest usługą w chmurze nienądrową, można użyć udr do kierowania ruchu wychodzącego z usługi Azure-SSIS IR do zapory NVA/Azure za pośrednictwem statycznego publicznego adresu IP. Następnie można dodać statyczny publiczny adres IP zapory NVA/Azure do listy dozwolonych zapory dla źródła danych.
  - Jeśli żadna z powyższych opcji nie spełnia Twoich potrzeb, należy rozważyć [skonfigurowanie samodzielnego podczerwania jako serwera proxy dla usługi Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Następnie można dodać statyczny publiczny adres IP komputera, na którym znajduje się samodzielna podczerwona wy, do listy dozwolonych zapory dla źródła danych.

- Dlaczego muszę podać dwa statyczne adresy publiczne, jeśli chcę przynieść własne dla usługi Azure-SSIS IR?

  Usługa Azure-SSIS IR jest regularnie aktualizowana automatycznie. Nowe węzły są tworzone podczas uaktualniania i stare zostaną usunięte. Jednak aby uniknąć przestojów, stare węzły nie zostaną usunięte, dopóki nowe nie będą gotowe. W związku z tym twój pierwszy statyczny publiczny adres IP używany przez stare węzły nie może zostać natychmiast wydany i potrzebujemy drugiego statycznego publicznego adresu IP, aby utworzyć nowe węzły.

- Przyniosłem własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR, ale dlaczego nadal nie może uzyskać dostępu do moich źródeł danych?

  - Upewnij się, że dwa statyczne publiczne adresy IP są dodawane do listy dozwolonych zapory dla źródeł danych. Za każdym razem, gdy usługa Azure-SSIS IR jest uaktualniany, jego statyczny publiczny adres IP jest przełączany między dwoma wniesionymi przez Ciebie. Jeśli dodasz tylko jeden z nich do listy dozwolonych, dostęp do danych dla usługi Azure-SSIS IR zostanie przerwany po jego uaktualnieniu.
  - Jeśli źródłem danych jest usługa platformy Azure, sprawdź, czy skonfigurowano ją z punktami końcowymi usługi sieci wirtualnej. W takim przypadku ruch z usługi Azure-SSIS IR do źródła danych przełączy się do korzystania z prywatnych adresów IP zarządzanych przez usługi platformy Azure i dodanie własnych statycznych publicznych adresów IP do listy dozwolonych zapory dla źródła danych nie zostanie zaniechanych.

## <a name="azure-portal-data-factory-ui"></a>Usługa Azure portal (interfejs użytkownika fabryki danych)

W tej sekcji pokazano, jak dołączyć do istniejącej usługi Azure-SSIS IR do sieci wirtualnej (klasycznej lub usługi Azure Resource Manager) przy użyciu witryny Azure portal i interfejsu użytkownika fabryki danych. 

Przed dołączeniem do usługi Azure-SSIS IR do sieci wirtualnej należy poprawnie skonfigurować sieć wirtualną. Wykonaj kroki opisane w sekcji, która ma zastosowanie do typu sieci wirtualnej (klasycznej lub Usługi Azure Resource Manager). Następnie wykonaj kroki opisane w trzeciej sekcji, aby dołączyć do usługi Azure-SSIS IR w sieci wirtualnej. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Konfigurowanie sieci wirtualnej usługi Azure Resource Manager

Użyj portalu, aby skonfigurować sieć wirtualną usługi Azure Resource Manager przed podjęciem próby dołączenia do usługi Azure-SSIS IR do niej.

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki internetowe obsługują interfejs użytkownika fabryki danych. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. Wybierz **pozycję Więcej usług**. Filtruj i wybierz **pozycję Sieci wirtualne**. 

1. Filtruj i wybierz sieć wirtualną na liście. 

1. Na stronie **Sieć wirtualna** wybierz pozycję **Właściwości**. 

1. Wybierz przycisk kopiowania **identyfikatora zasobu,** aby skopiować identyfikator zasobu dla sieci wirtualnej do schowka. Zapisz identyfikator ze schowka w programie OneNote lub w pliku. 

1. W menu po lewej stronie wybierz pozycję **Podsieci**. Upewnij się, że liczba dostępnych adresów jest większa niż węzły w usłudze Azure-SSIS IR. 

1. Sprawdź, czy dostawca usługi Azure Batch jest zarejestrowany w subskrypcji platformy Azure, która ma sieć wirtualną. Możesz też zarejestrować dostawcę usługi Azure Batch. Jeśli masz już konto usługi Azure Batch w ramach subskrypcji, twoja subskrypcja jest zarejestrowana dla usługi Azure Batch. (Jeśli utworzysz identyfikator Azure-SSIS IR w portalu usługi Data Factory, dostawca usługi Azure Batch zostanie automatycznie zarejestrowany). 

   1. W witrynie Azure portal po lewej stronie menu wybierz pozycję **Subskrypcje**. 

   1. Wybierz subskrypcję. 

   1. Po lewej stronie wybierz pozycję **Dostawcy zasobów**i upewnij się, że **firma Microsoft.Batch** jest zarejestrowanym dostawcą. 

   ![Potwierdzenie statusu "Zarejestrowanego"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli nie widzisz **microsoft.batch** na liście, aby go zarejestrować, [należy utworzyć puste konto usługi Azure Batch](../batch/batch-account-create-portal.md) w ramach subskrypcji. Można go usunąć później. 

### <a name="configure-a-classic-virtual-network"></a>Konfigurowanie klasycznej sieci wirtualnej

Użyj portalu, aby skonfigurować klasyczną sieć wirtualną przed podjęciem próby dołączenia do usługi Azure-SSIS IR do niej. 

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki internetowe obsługują interfejs użytkownika fabryki danych. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. Wybierz **pozycję Więcej usług**. Filtruj i wybierz **sieci wirtualne (klasyczne).** 

1. Filtruj i wybierz sieć wirtualną na liście. 

1. Na stronie **Sieć wirtualna (klasyczna)** wybierz pozycję **Właściwości**. 

   ![Identyfikator klasycznego zasobu sieci wirtualnej](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Wybierz przycisk kopiowania **identyfikatora zasobu,** aby skopiować identyfikator zasobu dla sieci klasycznej do schowka. Zapisz identyfikator ze schowka w programie OneNote lub w pliku. 

1. W menu po lewej stronie wybierz pozycję **Podsieci**. Upewnij się, że liczba dostępnych adresów jest większa niż węzły w usłudze Azure-SSIS IR. 

   ![Liczba dostępnych adresów w sieci wirtualnej](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Dołącz **microsoftAzureBatch** do klasycznej roli **współautora maszyny wirtualnej** dla sieci wirtualnej. 

   1. W menu po lewej stronie wybierz **pozycję Kontrola dostępu (IAM)** i wybierz kartę **Przypisania ról.** 

       ![Przyciski "Kontrola dostępu" i "Dodaj"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Wybierz pozycję **Dodaj przypisanie roli**.

   1. Na stronie **Dodawanie przypisania roli** w polu **Rola**wybierz pozycję Klasyczny współautor **maszyny wirtualnej**. W polu **Wybierz** wklej **ddbf3205-c6bd-46ae-8127-60eb93363864**, a następnie wybierz pozycję **Microsoft Azure Batch** z listy wyników wyszukiwania. 

       ![Wyniki wyszukiwania na stronie "Dodawanie przypisania roli"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Wybierz **pozycję Zapisz,** aby zapisać ustawienia i zamknąć stronę. 

       ![Zapisz ustawienia dostępu](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Upewnij się, że na liście współautorów jest widoczna **usługa Microsoft Azure Batch.** 

       ![Potwierdź dostęp wsadowy platformy Azure](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Sprawdź, czy dostawca usługi Azure Batch jest zarejestrowany w subskrypcji platformy Azure, która ma sieć wirtualną. Możesz też zarejestrować dostawcę usługi Azure Batch. Jeśli masz już konto usługi Azure Batch w ramach subskrypcji, twoja subskrypcja jest zarejestrowana dla usługi Azure Batch. (Jeśli utworzysz identyfikator Azure-SSIS IR w portalu usługi Data Factory, dostawca usługi Azure Batch zostanie automatycznie zarejestrowany). 

   1. W witrynie Azure portal po lewej stronie menu wybierz pozycję **Subskrypcje**. 

   1. Wybierz subskrypcję. 

   1. Po lewej stronie wybierz pozycję **Dostawcy zasobów**i upewnij się, że **firma Microsoft.Batch** jest zarejestrowanym dostawcą. 

   ![Potwierdzenie statusu "Zarejestrowanego"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli nie widzisz **microsoft.batch** na liście, aby go zarejestrować, [należy utworzyć puste konto usługi Azure Batch](../batch/batch-account-create-portal.md) w ramach subskrypcji. Można go usunąć później. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Dołącz do usługi Azure-SSIS IR w sieci wirtualnej

Po skonfigurowaniu sieci wirtualnej usługi Azure Resource Manager lub klasycznej sieci wirtualnej można dołączyć do usługi Azure-SSIS IR do sieci wirtualnej:

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki internetowe obsługują interfejs użytkownika fabryki danych. 

1. W [witrynie Azure portal](https://portal.azure.com)po lewej stronie menu wybierz pozycję **Fabryki danych**. Jeśli w menu nie widać **fabryk danych,** wybierz pozycję **Więcej usług**, a następnie w sekcji ANALIZY **+ ANALIZY** wybierz pozycję **Fabryki danych**. 

   ![Lista fabryk danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Wybierz fabrykę danych z azure-SSIS IR na liście. Zostanie wyświetlona strona główna fabryki danych. Wybierz kafelek **Monitor & Autor.** Interfejs użytkownika fabryki danych jest widoczny na osobnej karcie. 

   ![Strona główna fabryki danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. W interfejsie użytkownika fabryki danych przełącz się na kartę **Edycja,** wybierz pozycję **Połączenia**i przełącz się na kartę **Środowiska wykonawcze integracji.** 

   ![Karta "Środowiska wykonawcze integracji"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Jeśli twój identyfikator IR azure-SSIS jest uruchomiony, na liście **Środowiska wykonawcze integracji,** w **akcji** kolumny, wybierz przycisk **Zatrzymaj** dla azure-SSIS IR. Nie można edytować usługi Azure-SSIS IR, dopóki go nie zatrzymasz. 

   ![Zatrzymaj podczerwę](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na liście **Środowiska wykonawcze integracji** w kolumnie **Akcje** wybierz przycisk **Edytuj** dla urządzenia Azure-SSIS IR. 

   ![Edytowanie środowiska wykonawczego integracji](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. W panelu konfiguracji środowiska wykonawczego integracji przejdź przez sekcje **Ustawienia ogólne** i **Ustawienia SQL,** wybierając przycisk **Dalej.** 

1. W sekcji **Ustawienia zaawansowane:** 

   1. Wybierz **sieć wirtualną dla środowiska wykonawczego integracji platformy Azure-SSIS do dołączenia, zezwolić podajniku ADF na tworzenie pewnych zasobów sieciowych i opcjonalnie przywieźć własne statyczne publiczne adresy IP.** 

   1. W przypadku **subskrypcji**wybierz subskrypcję platformy Azure, która ma sieć wirtualną.

   1. W przypadku **lokalizacji**wybrano tę samą lokalizację środowiska wykonawczego integracji.

   1. W przypadku **opcji Typ**wybierz typ sieci wirtualnej: klasyczny lub Usługę Azure Resource Manager. Zaleca się wybranie sieci wirtualnej usługi Azure Resource Manager, ponieważ klasyczne sieci wirtualne zostaną wkrótce przestarzałe.

   1. W przypadku **nazwy sieci wirtualnej**wybierz nazwę sieci wirtualnej. Powinien być taki sam, który jest używany dla serwera usługi Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania usługi SSISDB. Lub powinien być ten sam, który jest połączony z siecią lokalną. W przeciwnym razie może to być dowolna sieć wirtualna, aby przynieść własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR.

   1. W obszarze **Nazwa podsieci**wybierz nazwę podsieci dla sieci wirtualnej. Powinien być taki sam, który jest używany dla serwera usługi Azure SQL Database z punktami końcowymi usługi sieci wirtualnej do obsługi usługi SSISDB. Lub powinna być inna podsieć niż używana dla wystąpienia zarządzanego z prywatnym punktem końcowym do hostowania SSISDB. W przeciwnym razie może być dowolna podsieć, aby przynieść własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR.

   1. Zaznacz pole wyboru **Przynieś statyczne publiczne adresy IP dla środowiska uruchomieniowego integracji platformy Azure-SSIS,** aby wybrać, czy chcesz przywieźć własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR, aby umożliwić im korzystanie z zapory dla źródeł danych.

      Jeśli zaznaczysz to pole wyboru, wykonaj następujące kroki.

      1. W przypadku **pierwszego statycznego publicznego adresu IP**wybierz pierwszy statyczny publiczny adres IP, który spełnia [wymagania](#publicIP) dotyczące usługi Azure-SSIS IR. Jeśli nie masz żadnych, kliknij przycisk **Utwórz nowe** łącze, aby utworzyć statyczne publiczne adresy IP w witrynie Azure portal, a następnie kliknij przycisk odświeżania tutaj, aby można je było wybrać.
      
      1. W przypadku **drugiego statycznego publicznego adresu IP**wybierz drugi statyczny publiczny adres IP, który spełnia [wymagania](#publicIP) dotyczące usługi Azure-SSIS IR. Jeśli nie masz żadnych, kliknij przycisk **Utwórz nowe** łącze, aby utworzyć statyczne publiczne adresy IP w witrynie Azure portal, a następnie kliknij przycisk odświeżania tutaj, aby można je było wybrać.

   1. Wybierz **pozycję Sprawdzanie poprawności sieci wirtualnej**. Jeśli sprawdzanie poprawności zakończy się pomyślnie, wybierz przycisk **Kontynuuj**. 

   ![Ustawienia zaawansowane sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. W sekcji **Podsumowanie** przejrzyj wszystkie ustawienia dla usługi Azure-SSIS IR. Następnie wybierz pozycję **Aktualizuj**.

1. Uruchom usługę Azure-SSIS IR, wybierając przycisk **Start** w kolumnie **Akcje** dla usługi Azure-SSIS IR. Trwa około 20 do 30 minut, aby uruchomić azure-SSIS IR, który łączy się z siecią wirtualną. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Definiowanie zmiennych

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Konfigurowanie sieci wirtualnej

Aby można było dołączyć do usługi Azure-SSIS IR w sieci wirtualnej, należy skonfigurować sieć wirtualną. Aby automatycznie skonfigurować uprawnienia i ustawienia sieci wirtualnej dla usługi Azure-SSIS IR do przyłączenia się do sieci wirtualnej, dodaj następujący skrypt:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Tworzenie usługi Azure-SSIS IR i łączenie jej w sieci wirtualnej

Można utworzyć usługę Azure-SSIS IR i połączyć go z siecią wirtualną w tym samym czasie. Aby uzyskać pełny skrypt i instrukcje, zobacz [Tworzenie usługi Azure-SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Dołącz do istniejącej usługi Azure-SSIS IR w sieci wirtualnej

Artykuł [Utwórz usługę Azure-SSIS IR](create-azure-ssis-integration-runtime.md) pokazuje, jak utworzyć usługę Azure-SSIS IR i dołączyć ją do sieci wirtualnej w tym samym skrypcie. Jeśli masz już usługę Azure-SSIS IR, wykonaj następujące kroki, aby dołączyć do sieci wirtualnej: 
1. Zatrzymaj usługę Azure-SSIS IR. 
1. Skonfiguruj usługę Azure-SSIS IR tak, aby dołączyć do sieci wirtualnej. 
1. Uruchom usługę Azure-SSIS IR. 

### <a name="stop-the-azure-ssis-ir"></a>Zatrzymywać usługę Azure-SSIS IR

Musisz zatrzymać azure-SSIS IR, zanim będzie można dołączyć do sieci wirtualnej. To polecenie zwalnia wszystkie jego węzły i zatrzymuje rozliczenia:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurowanie ustawień sieci wirtualnej dla usługi Azure-SSIS IR do dołączenia

Aby skonfigurować ustawienia sieci wirtualnej, do której dołączy azure-SSIS, użyj tego skryptu: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Konfigurowanie identyfikatora IR azure-SSIS

Aby dołączyć do usługi Azure-SSIS IR `Set-AzDataFactoryV2IntegrationRuntime` w sieci wirtualnej, uruchom polecenie: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>Uruchamianie usługi Azure-SSIS IR

Aby uruchomić usługę Azure-SSIS IR, uruchom następujące polecenie: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

To polecenie trwa od 20 do 30 minut.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure-SSIS IR, zobacz następujące artykuły: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera ogólne informacje koncepcyjne dotyczące irów, w tym usługi Azure-SSIS IR. 
- [Samouczek: Wdrażanie pakietów SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten samouczek zawiera instrukcje krok po kroku, aby utworzyć usługę Azure-SSIS IR. Używa usługi Azure SQL Database do obsługi katalogu SSIS. 
- [Utwórz usługę Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Ten artykuł rozwija się w samouczku. Zawiera instrukcje dotyczące korzystania z usługi Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym w sieci wirtualnej do obsługi katalogu SSIS. Pokazano w nim, jak dołączyć do usługi Azure-SSIS IR w sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule pokazano, jak uzyskać informacje o usłudze Azure-SSIS IR. Zawiera opisy stanu zwracanych informacji. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule pokazano, jak zatrzymać, uruchomić lub usunąć usługę Azure-SSIS IR. Pokazuje również, jak skalować w poziomie usługi Azure-SSIS IR przez dodanie węzłów.