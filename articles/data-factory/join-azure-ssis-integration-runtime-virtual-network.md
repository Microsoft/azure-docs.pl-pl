---
title: Dołączanie środowiska Azure SSIS Integration Runtime do sieci wirtualnej
description: Dowiedz się, jak dołączać środowisko Azure-SSIS Integration Runtime do sieci wirtualnej platformy Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/02/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: a8928f9d52fd8e721ac770dda8f0cbf0162a0f61
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797910"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Dołączanie środowiska Azure SSIS Integration Runtime do sieci wirtualnej

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Korzystając z SQL Server Integration Services (SSIS) w Azure Data Factory, należy przyłączyć się do środowiska Azure-SSIS Integration Runtime (IR) do sieci wirtualnej platformy Azure w następujących scenariuszach:

- Chcesz połączyć się z lokalnymi magazynami danych z pakietów SSIS, które są uruchamiane na Azure-SSIS IR bez konfigurowania lub zarządzania własnym hostowanym programem IR jako serwer proxy. 

- Chcesz hostować bazę danych wykazu usług SSIS (SSISDB) w Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub w wystąpieniu zarządzanym SQL z prywatnym punktem końcowym. 

- Chcesz połączyć się z zasobami platformy Azure skonfigurowanymi za pomocą punktów końcowych usługi sieci wirtualnej z pakietów SSIS, które są uruchomione na Azure-SSIS IR.

- Chcesz nawiązać połączenie z magazynami danych/zasobami skonfigurowanymi przy użyciu reguł zapory adresów IP z pakietów SSIS uruchomionych na Azure-SSIS IR.

Data Factory umożliwia dołączenie Azure-SSIS IR do sieci wirtualnej utworzonej za pomocą klasycznego modelu wdrażania lub Azure Resource Managergo modelu wdrażania.

> [!IMPORTANT]
> Klasyczna Sieć wirtualna jest przestarzała, dlatego użyj w zamian Azure Resource Manager sieci wirtualnej.  Jeśli używasz już klasycznej sieci wirtualnej, przełącz się do Azure Resource Manager sieci wirtualnej najszybciej, jak to możliwe.

W przypadku [konfigurowania środowiska Integration Runtime (IR) platformy Azure — SQL Server Integration Services (SSIS) w celu dołączenia do samouczka dotyczącego sieci wirtualnej](tutorial-deploy-ssis-virtual-network.md) przedstawiono minimalne kroki w Azure Portal. Ten artykuł rozszerza się w samouczku i opisuje wszystkie opcjonalne zadania:

- Jeśli używasz sieci wirtualnej (klasycznej).
- Jeśli utworzysz własne publiczne adresy IP dla Azure-SSIS IR.
- Jeśli używasz własnego serwera DNS (Domain Name System).
- W przypadku używania sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w podsieci.
- Jeśli używasz usługi Azure ExpressRoute lub trasy zdefiniowanej przez użytkownika (UDR).
- Jeśli używasz niestandardowych Azure-SSIS IR.
- W przypadku korzystania z aprowizacji programu Azure PowerShell.

## <a name="access-to-on-premises-data-stores"></a>Dostęp do lokalnych magazynów danych

Jeśli pakiety SSIS mają dostęp do lokalnych magazynów danych, możesz przyłączyć Azure-SSIS IR do sieci wirtualnej, która jest połączona z siecią lokalną. Możesz też skonfigurować własne środowisko IR jako serwer proxy dla Azure-SSIS IR i zarządzać nim. Aby uzyskać więcej informacji, zobacz [Konfigurowanie własnego środowiska IR jako serwera proxy dla Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). 

Podczas dołączania Azure-SSIS IR do sieci wirtualnej należy pamiętać o następujących kwestiach: 

- Jeśli żadna Sieć wirtualna nie jest połączona z siecią lokalną, najpierw Utwórz [Azure Resource Manager sieci wirtualnej](../virtual-network/quick-create-portal.md#create-a-virtual-network) dla Azure-SSIS IR do przyłączenia. Następnie skonfiguruj [połączenie bramy sieci VPN typu](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) lokacja-lokacja lub połączenie [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) z tej sieci wirtualnej z siecią lokalną. 

- Jeśli Azure Resource Manager Sieć wirtualna jest już połączona z siecią lokalną w tej samej lokalizacji co Azure-SSIS IR, można przyłączyć je do tej sieci wirtualnej. 

- Jeśli klasyczna Sieć wirtualna jest już połączona z siecią lokalną w innej lokalizacji z Azure-SSIS IR, można utworzyć [Azure Resource Manager sieci wirtualnej](../virtual-network/quick-create-portal.md#create-a-virtual-network) dla Azure-SSIS IR do przyłączenia. Następnie skonfiguruj połączenie z [siecią wirtualną w trybie klasycznym Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) . 
 
- Jeśli Azure Resource Manager Sieć wirtualna jest już połączona z siecią lokalną w innej lokalizacji z Azure-SSIS IR, można najpierw utworzyć [Azure Resource Manager sieci wirtualnej](../virtual-network/quick-create-portal.md#create-a-virtual-network) dla Azure-SSIS IR do przyłączenia. Następnie skonfiguruj połączenie sieci wirtualnej Azure Resource Manager Azure Resource Manager. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hostowanie wykazu usług SSIS w SQL Database

Jeśli katalog SSIS jest hostowany w Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej, upewnij się, że dołączysz Azure-SSIS IR do tej samej sieci wirtualnej i podsieci.

Jeśli katalog usług SSIS jest hostowany w wystąpieniu zarządzanym SQL z prywatnym punktem końcowym, należy się upewnić, że dołączysz Azure-SSIS IR do tej samej sieci wirtualnej, ale w innej podsieci niż wystąpienie zarządzane. Aby przyłączyć Azure-SSIS IR do innej sieci wirtualnej niż wystąpienie zarządzane SQL, zalecamy wirtualne sieci równorzędne (ograniczone do tego samego regionu) lub połączenie z sieci wirtualnej z siecią wirtualną. Aby uzyskać więcej informacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym usługi Azure SQL](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-services"></a>Dostęp do usług platformy Azure

Jeśli pakiety SSIS mają dostęp do zasobów platformy Azure, które obsługują [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) i chcesz zabezpieczyć dostęp do tych zasobów z Azure-SSIS IR, możesz dołączyć Azure-SSIS IR do podsieci sieci wirtualnej skonfigurowanej dla punktów końcowych usługi sieci wirtualnej, a następnie dodać regułę sieci wirtualnej do odpowiednich zasobów platformy Azure, aby zezwolić na dostęp z tej samej podsieci.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Dostęp do źródeł danych chronionych przez regułę zapory adresów IP

Jeśli pakiety SSIS mają dostęp do magazynów danych/zasobów, które zezwalają na tylko określone statyczne publiczne adresy IP i chcesz zabezpieczyć dostęp do tych zasobów z Azure-SSIS IR, możesz skojarzyć [publiczne adresy IP](../virtual-network/virtual-network-public-ip-address.md) z Azure-SSIS IR podczas dołączania do sieci wirtualnej, a następnie dodać regułę zapory IP do odpowiednich zasobów, aby umożliwić dostęp z tych adresów IP. Istnieją dwa alternatywne sposoby wykonania tej czynności: 

- Podczas tworzenia Azure-SSIS IR można przenieść własne publiczne adresy IP i określić je za pomocą [interfejsu użytkownika Data Factory lub zestawu SDK](#join-the-azure-ssis-ir-to-a-virtual-network). Tylko wychodzące połączenie internetowe Azure-SSIS IR będzie używać podanych publicznych adresów IP i innych urządzeń w podsieci nie będzie ich używać.
- Istnieje również możliwość skonfigurowania [Virtual Network translatora adresów sieciowych](../virtual-network/nat-overview.md) dla podsieci, która Azure-SSIS IR zostanie przyłączona, a wszystkie połączenia wychodzące w tej podsieci będą używały określonych publicznych adresów IP.

We wszystkich przypadkach sieć wirtualna można wdrożyć tylko za pomocą modelu wdrażania Azure Resource Manager.

Poniższe sekcje zawierają więcej szczegółów. 

## <a name="virtual-network-configuration"></a>Konfiguracja sieci wirtualnej

Skonfiguruj sieć wirtualną tak, aby spełniała te wymagania: 

- Upewnij się, że `Microsoft.Batch` jest zarejestrowanym dostawcą w ramach subskrypcji podsieci sieci wirtualnej, która hostuje Azure-SSIS IR. Jeśli używasz klasycznej sieci wirtualnej, Dołącz także `MicrosoftAzureBatch` do roli współautor klasycznej maszyny wirtualnej dla tej sieci wirtualnej. 

- Upewnij się, że masz wymagane uprawnienia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uprawnień](#perms).

- Wybierz odpowiednią podsieć, aby hostować Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [wybierz podsieć](#subnet). 

- Jeśli chcesz uzyskać własne publiczne adresy IP dla Azure-SSIS IR, zobacz [Wybieranie statycznych publicznych adresów IP](#publicIP)

- Jeśli używasz własnego serwera DNS (Domain Name System) w sieci wirtualnej, zobacz [Konfigurowanie serwera DNS](#dns_server). 

- Jeśli używasz sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w podsieci, zobacz [Konfigurowanie sieciowej grupy zabezpieczeń](#nsg). 

- Jeśli używasz usługi Azure ExpressRoute lub trasy zdefiniowanej przez użytkownika (UDR), zobacz [Korzystanie z usługi Azure ExpressRoute lub UDR](#route). 

- Upewnij się, że grupa zasobów sieci wirtualnej (lub Grupa zasobów publiczne adresy IP), jeśli będziesz udostępniać własne publiczne adresy IP), może tworzyć i usuwać niektóre zasoby sieci platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie grupy zasobów](#resource-group). 

- W przypadku dostosowania Azure-SSIS IR zgodnie z opisem w temacie [Konfiguracja niestandardowa dla Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md), nasz wewnętrzny proces zarządzania jego węzłami będzie używać prywatnych adresów IP ze wstępnie zdefiniowanego zakresu od 172.16.0.0 do 172.31.255.255. W związku z tym upewnij się, że zakresy prywatnych adresów IP sieci wirtualnych lub lokalnych nie kolidują z tym zakresem.

Ten diagram przedstawia wymagane połączenia dla Azure-SSIS IR:

![Diagram przedstawiający wymagane połączenia dla Azure-SSIS IR.](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a> Konfigurowanie uprawnień

Użytkownik, który tworzy Azure-SSIS IR, musi mieć następujące uprawnienia:

- W przypadku dołączania środowiska IR do programu SSIS do sieci wirtualnej Azure Resource Manager dostępne są dwie opcje:

  - Użyj wbudowanej roli współautor sieci. Ta rola jest dostarczana z _firmą Microsoft. \* Network/_ uprawnieniem o znacznie większym zakresie niż jest to konieczne.

  - Utwórz rolę niestandardową, która zawiera tylko wymagane uprawnienie _Microsoft. Network/virtualNetworks/ \* /Join/Action_ . Jeśli chcesz również przenieść własne publiczne adresy IP do Azure-SSIS IR podczas dołączania do Azure Resource Manager sieci wirtualnej, w roli należy również uwzględnić uprawnienie _Microsoft. Network/adresów publicipaddress/*/Join/Action_ .

- Jeśli dołączysz środowisko IR usług SSIS do klasycznej sieci wirtualnej, zalecamy użycie wbudowanej roli współautor klasycznej maszyny wirtualnej. W przeciwnym razie musisz zdefiniować rolę niestandardową, która zawiera uprawnienie do przyłączenia do sieci wirtualnej.

### <a name="select-the-subnet"></a><a name="subnet"></a> Wybierz podsieć

Podczas wybierania podsieci: 

- Nie wybieraj GatewaySubnet do wdrożenia Azure-SSIS IR. Jest on przeznaczony dla bram sieci wirtualnej. 

- Upewnij się, że wybrana podsieć ma wystarczającą ilość dostępnej przestrzeni adresowej dla Azure-SSIS IR do użycia. Pozostaw dostępne adresy IP dla co najmniej dwóch razy numeru węzła IR. Platforma Azure rezerwuje niektóre adresy IP w poszczególnych podsieciach. Nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane na potrzeby zgodności protokołów, a dla usług platformy Azure są używane trzy więcej adresów. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Nie należy używać podsieci, która jest przeznaczona wyłącznie dla innych usług platformy Azure (na przykład SQL Database wystąpienia zarządzane SQL, App Service itd.). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Wybierz statyczne publiczne adresy IP

Jeśli chcesz udostępnić własne statyczne publiczne adresy IP dla Azure-SSIS IR podczas dołączania do sieci wirtualnej, upewnij się, że spełniają one następujące wymagania:

- Należy podać dokładnie dwa nieużywane zasoby, które nie są jeszcze skojarzone z innymi zasobami platformy Azure. Ta dodatkowa zostanie użyta, gdy okresowo uaktualniamy Azure-SSIS IR. Należy pamiętać, że jeden publiczny adres IP nie może być współużytkowany przez aktywnego urzędu skarbowego Azure-SSIS.

- Obydwie te elementy powinny być statyczne dla typu standardowego. Aby uzyskać więcej informacji, zobacz [jednostki SKU publicznego adresu IP](../virtual-network/public-ip-addresses.md#sku) .

- Powinny one mieć nazwę DNS. Jeśli nie podano nazwy DNS podczas jej tworzenia, można to zrobić na Azure Portal.

![Środowisko IR Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Te i Sieć wirtualna powinny znajdować się w tej samej subskrypcji i w tym samym regionie.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a> Konfigurowanie serwera DNS 
Jeśli potrzebujesz użyć własnego serwera DNS w sieci wirtualnej przyłączonej przez Azure-SSIS IR w celu rozpoznania prywatnej nazwy hosta, upewnij się, że można również rozpoznać globalne nazwy hostów platformy Azure (na przykład obiekt BLOB usługi Azure Storage o nazwie `<your storage account>.blob.core.windows.net` ). 

Poniżej przedstawiono zalecane podejście: 

-   Skonfiguruj niestandardowe DNS do przesyłania żądań do Azure DNS. Nierozpoznane rekordy DNS można przesłać dalej na adres IP tłumaczeń cyklicznych platformy Azure (168.63.129.16) na własnym serwerze DNS. 

Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw używające własnego serwera DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Użyj w pełni kwalifikowanej nazwy domeny (FQDN) dla nazwy hosta prywatnego (na przykład użyj `<your_private_server>.contoso.com` zamiast `<your_private_server>` ). Alternatywnie możesz użyć standardowej instalacji niestandardowej na Azure-SSIS IR, aby automatycznie dołączyć swój własny sufiks DNS (na przykład `contoso.com` ) do dowolnej niekwalifikowanej nazwy domeny o pojedynczej etykiecie i przekształcić ją w nazwę FQDN przed użyciem jej w zapytaniach DNS, zobacz [standardowe przykłady instalacji niestandardowej](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples). 

### <a name="set-up-an-nsg"></a><a name="nsg"></a> Konfigurowanie sieciowej grupy zabezpieczeń
Jeśli musisz zaimplementować sieciowej grupy zabezpieczeń dla podsieci używanej przez Azure-SSIS IR, Zezwól na ruch przychodzący i wychodzący przez następujące porty: 

-   **Wymagania przychodzące Azure-SSIS IR**

| Kierunek | Protokół transportowy | Element źródłowy | Zakres portów źródłowych | Element docelowy | Zakres portów docelowych | Komentarze |
|---|---|---|---|---|---|---|
| Przychodzący | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (w przypadku dołączenia środowiska IR do sieci wirtualnej Menedżer zasobów) <br/><br/>10100, 20100, 30100 (Jeśli dołączysz środowisko IR do klasycznej sieci wirtualnej)| Usługa Data Factory używa tych portów do komunikacji z węzłami Azure-SSIS IR w sieci wirtualnej. <br/><br/> Niezależnie od tego, czy tworzysz sieciowej grupy zabezpieczeń poziomu podsieci, Data Factory zawsze konfiguruje sieciowej grupy zabezpieczeń na poziomie kart sieciowych podłączonych do maszyn wirtualnych, które obsługują Azure-SSIS IR. Tylko ruch przychodzący z Data Factory adresów IP na określonych portach jest dozwolony przez ten sieciowej grupy zabezpieczeń na poziomie karty sieciowej. Nawet w przypadku otwarcia tych portów do ruchu internetowego na poziomie podsieci ruch z adresów IP, które nie są Data Factory adresy IP, jest blokowany na poziomie karty sieciowej. |
| Przychodzący | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | Obowiązkowe Ta reguła jest wymagana tylko w przypadku, gdy pomoc techniczna firmy Microsoft poprosiła klienta o otwarcie na potrzeby zaawansowanego rozwiązywania problemów i może być ZAMKNIĘTA bezpośrednio po rozwiązaniu problemu. Tag usługi **CorpNetSaw** zezwala na korzystanie z pulpitu zdalnego tylko zabezpieczonym stacjom roboczym dostępu w sieci firmowej firmy Microsoft. Nie można wybrać tego tagu usługi z portalu i jest on dostępny tylko za pośrednictwem Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. <br/><br/> Na poziomie karty sieciowej sieciowej grupy zabezpieczeń Port 3389 jest otwarty domyślnie i zezwalamy na kontrolowanie portu 3389 na poziomie podsieci sieciowej grupy zabezpieczeń, Tymczasem Azure-SSIS IR nie 3389 zezwolił na ruch wychodzący domyślnie w regule zapory systemu Windows w każdym węźle podczerwieni do ochrony. |
||||||||

-   **Wymagania wychodzące Azure-SSIS IR**

| Kierunek | Protokół transportowy | Element źródłowy | Zakres portów źródłowych | Element docelowy | Zakres portów docelowych | Komentarze |
|---|---|---|---|---|---|---|
| Wychodzący | TCP | VirtualNetwork | * | AzureCloud | 443 | Węzły Azure-SSIS IR w sieci wirtualnej używają tego portu do uzyskiwania dostępu do usług platformy Azure, takich jak Azure Storage i Azure Event Hubs. |
| Wychodzący | TCP | VirtualNetwork | * | Internet | 80 | Obowiązkowe Węzły Azure-SSIS IR w sieci wirtualnej używają tego portu do pobierania listy odwołania certyfikatów z Internetu. Jeśli zablokujesz ten ruch, może wystąpić obniżenie wydajności podczas uruchamiania środowiska IR i utrata możliwości sprawdzenia listy odwołania certyfikatów w celu użycia certyfikatu. Jeśli chcesz jeszcze bardziej zawęzić miejsce docelowe do określonych nazw FQDN, zapoznaj się z sekcją **Korzystanie z usługi Azure ExpressRoute lub UDR** .|
| Wychodzący | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | Obowiązkowe Ta reguła jest wymagana tylko wtedy, gdy węzły Azure-SSIS IR w sieci wirtualnej uzyskują dostęp do SSISDB hostowanego przez serwer. Jeśli zasada połączenia serwera jest ustawiona na **serwer proxy** zamiast **przekierowania**, wymagany jest tylko port 1433. <br/><br/> Ta reguła zabezpieczeń dla ruchu wychodzącego nie ma zastosowania do SSISDB hostowanego przez wystąpienie zarządzane SQL w sieci wirtualnej lub SQL Database skonfigurowany za pomocą prywatnego punktu końcowego. |
| Wychodzący | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | Obowiązkowe Ta reguła jest wymagana tylko wtedy, gdy węzły Azure-SSIS IR w sieci wirtualnej uzyskują dostęp do SSISDB hostowanego przez wystąpienie zarządzane SQL w sieci wirtualnej lub SQL Database skonfigurowany przy użyciu prywatnego punktu końcowego. Jeśli zasada połączenia serwera jest ustawiona na **serwer proxy** zamiast **przekierowania**, wymagany jest tylko port 1433. |
| Wychodzący | TCP | VirtualNetwork | * | Magazyn | 445 | Obowiązkowe Ta reguła jest wymagana tylko wtedy, gdy chcesz uruchomić pakiet SSIS przechowywany w Azure Files. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Korzystanie z usługi Azure ExpressRoute lub UDR
Jeśli chcesz przeprowadzić inspekcję ruchu wychodzącego z Azure-SSIS IR, możesz kierować ruchem zainicjowanym z Azure-SSIS IR do lokalnego urządzenia zapory za pośrednictwem tunelowania [ExpressRoute platformy Azure](https://azure.microsoft.com/services/expressroute/) (anonsowanie trasy BGP, 0.0.0.0/0, do sieci wirtualnej) lub do sieciowego urządzenia wirtualnego (urządzenie WUS) jako zapory lub [zapory platformy Azure](../firewall/index.yml) za pośrednictwem [UDR](../virtual-network/virtual-networks-udr-overview.md). 

![Scenariusz urządzenie WUS dla Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Należy wykonać następujące czynności, aby wykonać cały scenariusz pracy
   -   Ruch przychodzący między Azure Batch usług zarządzania i Azure-SSIS IR nie może być kierowany przez urządzenie zapory.
   -   Urządzenie zapory zezwala na ruch wychodzący wymagany przez Azure-SSIS IR.

Ruch przychodzący między Azure Batch usług zarządzania i Azure-SSIS IR nie może być kierowany do urządzenia zapory, w przeciwnym razie ruch zostanie przerwany z powodu problemu z routingiem asymetrycznym. Trasy muszą być zdefiniowane dla ruchu przychodzącego, aby ruch może oddzwonić w taki sam sposób jak w przypadku. Można zdefiniować określony UDR, aby kierować ruchem między Azure Batch usług zarządzania i Azure-SSIS IR z typem następnego przeskoku jako **Internet**.

Na przykład jeśli Azure-SSIS IR znajduje się w lokalizacji `UK South` i chcesz przeprowadzić inspekcję ruchu wychodzącego za pośrednictwem zapory platformy Azure, należy najpierw pobrać listę zakres adresów IP `BatchNodeManagement.UKSouth` z [zakresu adresów IP znaczników usługi](https://www.microsoft.com/download/details.aspx?id=56519) lub za pośrednictwem [interfejsu API odnajdywania tagów usług](../virtual-network/service-tags-overview.md#service-tags-on-premises). Następnie Zastosuj następującą UDR powiązanych tras zakresów adresów IP z typem następnego przeskoku jako **Internet** wraz z trasą 0.0.0.0/0 z typem następnego przeskoku jako **urządzeniem wirtualnym**.

![Azure Batch ustawienia UDR](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Takie podejście wiąże się z dodatkowym kosztem konserwacji. Regularnie sprawdzaj zakres adresów IP i Dodaj nowe zakresy adresów IP do UDR, aby uniknąć przerwania Azure-SSIS IR. Zalecamy sprawdzanie zakresu adresów IP miesięcznie, ponieważ gdy nowy adres IP pojawia się w tagu usługi, zostanie wprowadzony kolejny miesiąc. 

Aby ułatwić konfigurowanie reguł UDR, można uruchomić następujący skrypt programu PowerShell w celu dodania reguł UDR dla usług zarządzania Azure Batch:
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

Aby urządzenie zapory zezwalało na ruch wychodzący, należy zezwolić na wychodzący port do poniżej portów, takich jak wymagania w regułach wychodzących sieciowej grupy zabezpieczeń.
-   Port 443 z miejscem docelowym jako usług Azure Cloud Services.

    W przypadku korzystania z zapory platformy Azure można określić regułę sieci za pomocą tagu usługi AzureCloud. W przypadku zapory innych typów można po prostu zezwolić na miejsce docelowe jako wszystkie dla portu 443 lub pozostawić poniższe nazwy FQDN w oparciu o typ środowiska platformy Azure:

    | Środowisko platformy Azure | Punkty końcowe                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure — publiczna      | <ul><li><b>Azure Data Factory (Zarządzanie)</b><ul><li>\*. frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage (Zarządzanie)</b><ul><li>\*.blob.core.windows.net</li><li>\*. table.core.windows.net</li></ul></li><li><b>Azure Container Registry (Konfiguracja niestandardowa)</b><ul><li>\*. azurecr.io</li></ul></li><li><b>Centrum zdarzeń (rejestrowanie)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Usługa rejestrowania firmy Microsoft (użycie wewnętrzne)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (Zarządzanie)</b><ul><li>\*. frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage (Zarządzanie)</b><ul><li>\*. blob.core.usgovcloudapi.net</li><li>\*. table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container Registry (Konfiguracja niestandardowa)</b><ul><li>\*. azurecr.us</li></ul></li><li><b>Centrum zdarzeń (rejestrowanie)</b><ul><li>\*. servicebus.usgovcloudapi.net</li></ul></li><li><b>Usługa rejestrowania firmy Microsoft (użycie wewnętrzne)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure w Chinach — 21Vianet     | <ul><li><b>Azure Data Factory (Zarządzanie)</b><ul><li>\*. frontend.datamovement.azure.cn</li></ul></li><li><b>Azure Storage (Zarządzanie)</b><ul><li>\*. blob.core.chinacloudapi.cn</li><li>\*. table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container Registry (Konfiguracja niestandardowa)</b><ul><li>\*. azurecr.cn</li></ul></li><li><b>Centrum zdarzeń (rejestrowanie)</b><ul><li>\*. servicebus.chinacloudapi.cn</li></ul></li><li><b>Usługa rejestrowania firmy Microsoft (użycie wewnętrzne)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Podobnie jak w przypadku nazw FQDN usługi Azure Storage, Azure Container Registry i centrum zdarzeń, można również włączyć następujące punkty końcowe dla sieci wirtualnej, dzięki czemu ruch sieciowy do tych punktów końcowych przechodzi przez sieć szkieletową platformy Azure, a nie jest kierowany do urządzenia zapory:
    -  Microsoft.Storage
    -  Microsoft. ContainerRegistry
    -  Microsoft. EventHub


-   Port 80 z lokalizacją docelową pobierania listy CRL.

    Zezwalasz na używanie poniższych nazw FQDN, które są używane jako listy CRL (lista odwołania certyfikatów), Pobierz Lokacje certyfikatów do celów zarządzania Azure-SSIS IR:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Jeśli używasz certyfikatów z inną listą CRL, możesz je również uwzględnić. Możesz przeczytać ten temat, aby dowiedzieć się więcej na temat [listy odwołania certyfikatów](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

    Jeśli ten ruch nie zostanie zablokowany, może wystąpić obniżenie wydajności podczas uruchamiania Azure-SSIS IR i utrata możliwości sprawdzenia listy odwołania certyfikatów na potrzeby użycia certyfikatów, co nie jest zalecane z punktu widzenia zabezpieczeń.

-   Port 1433, 11000-11999 z miejscem docelowym jako Azure SQL Database (wymagane tylko wtedy, gdy węzły Azure-SSIS IR w sieci wirtualnej uzyskują dostęp do SSISDB hostowanego przez serwer).

    Jeśli używasz zapory platformy Azure, możesz określić regułę sieci przy użyciu tagu usługi Azure SQL. w przeciwnym razie możesz zezwolić na miejsce docelowe jako określony adres URL usługi Azure SQL w urządzeniu zapory.

-   Port 445 z miejscem docelowym usługi Azure Storage (wymagane tylko w przypadku uruchamiania pakietu SSIS przechowywanego w Azure Files).

    Jeśli używasz zapory platformy Azure, możesz określić regułę sieciową za pomocą tagu usługi magazynu, w przeciwnym razie możesz zezwolić na miejsce docelowe jako określony adres URL usługi Azure File Storage w urządzeniu zapory.

> [!NOTE]
> W przypadku usług Azure SQL i Storage w przypadku konfigurowania punktów końcowych usługi Virtual Network w podsieci, ruch między Azure-SSIS IR i Azure SQL w tym samym regionie \ Magazyn Azure w tym samym regionie lub w tym samym regionie będzie kierowany do Microsoft Azure sieci szkieletowej bezpośrednio, a nie na urządzeniu zapory.

Jeśli nie potrzebujesz możliwości sprawdzenia ruchu wychodzącego Azure-SSIS IR, możesz po prostu zastosować trasę, aby wymusić cały ruch do typu następnego przeskoku do **Internetu**:

-   W scenariuszu usługi Azure ExpressRoute można zastosować trasę 0.0.0.0/0 z typem następnego przeskoku jako **Internet** w podsieci, która hostuje Azure-SSIS IR. 
-   W scenariuszu urządzenie WUS można zmodyfikować istniejącą trasę 0.0.0.0/0 zastosowana w podsieci, która hostuje Azure-SSIS IR z typu następnego przeskoku jako **urządzenia wirtualnego** do **Internetu**.

![Dodawanie trasy](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Określ trasę z typem następnego przeskoku **Internet** nie oznacza, że cały ruch przejdzie przez Internet. Jeśli adres docelowy jest przeznaczony dla jednej z usług platformy Azure, platforma Azure kieruje ruch bezpośrednio do usługi za pośrednictwem sieci szkieletowej platformy Azure, a nie kierowanie ruchu do Internetu.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a> Konfigurowanie grupy zasobów

Azure-SSIS IR musi utworzyć określone zasoby sieciowe w ramach tej samej grupy zasobów co sieć wirtualna. Te zasoby obejmują:
- Moduł równoważenia obciążenia platformy Azure o nazwie *\<Guid> -azurebatch-cloudserviceloadbalancer*.
- Publiczny adres IP platformy Azure o nazwie *\<Guid> -azurebatch-cloudservicepublicip*.
- Grupa zabezpieczeń sieć służbowa o nazwie *\<Guid> -azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Możesz teraz przenieść własne statyczne publiczne adresy IP do Azure-SSIS IR. W tym scenariuszu utworzysz tylko usługę Azure load module i sieciową grupę zabezpieczeń w ramach tej samej grupy zasobów co statyczne publiczne adresy IP zamiast sieci wirtualnej.

Te zasoby zostaną utworzone po rozpoczęciu Azure-SSIS IR. Zostaną one usunięte po zatrzymaniu Azure-SSIS IR. Jeśli użytkownik wprowadzi własne statyczne publiczne adresy IP dla Azure-SSIS IR, własne statyczne publiczne adresy IP nie zostaną usunięte, gdy Azure-SSIS IR zostanie zatrzymana. Aby zapobiec zablokowaniu Azure-SSIS IR z zatrzymywania, nie używaj ponownie tych zasobów sieciowych w innych zasobach.

Upewnij się, że nie masz blokady zasobów w grupie zasobów/subskrypcji, do której należą Sieć wirtualna/Twoje statyczne publiczne adresy IP. W przypadku skonfigurowania blokady tylko do odczytu/usuwania, uruchamianie i zatrzymywanie Azure-SSIS IR zakończy się niepowodzeniem lub przestanie odpowiadać.

Upewnij się, że nie masz przypisania Azure Policy uniemożliwiające utworzenie następujących zasobów w grupie zasobów/subskrypcji, do której należą Sieć wirtualna/statyczne publiczne adresy IP: 
- Microsoft. Network/LoadBalancers 
- Microsoft. Network/NetworkSecurityGroups 
- Microsoft. Network/adresów publicipaddress 

Upewnij się, że przydział zasobów dla subskrypcji jest wystarczający dla powyższych trzech zasobów sieciowych. W odniesieniu do każdego Azure-SSIS IR utworzonego w sieci wirtualnej należy zarezerwować dwa bezpłatne limity dla każdego z powyższych trzech zasobów sieciowych. Podczas okresowego uaktualniania Azure-SSIS IR zostanie użyty dodatkowy przydział.

### <a name="faq"></a><a name="faq"></a> FAQ

- Jak mogę chronić publiczny adres IP uwidoczniony w Azure-SSIS IR na potrzeby połączenia przychodzącego? Czy można usunąć publiczny adres IP?
 
  Teraz publiczny adres IP zostanie utworzony automatycznie, gdy Azure-SSIS IR zostanie przyłączony do sieci wirtualnej. Mamy sieciowej grupy zabezpieczeń na poziomie kart sieciowych, aby zezwalać na połączenia przychodzące tylko Azure Batch usług zarządzania z Azure-SSIS IR. Można także określić sieciowej grupy zabezpieczeń na poziomie podsieci na potrzeby ochrony ruchu przychodzącego.

  Jeśli nie chcesz ujawniać żadnych publicznych adresów IP, rozważ [skonfigurowanie własnego środowiska IR jako serwera proxy dla Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md) zamiast przyłączania Azure-SSIS IR do sieci wirtualnej, jeśli ma to zastosowanie do danego scenariusza.
 
- Czy mogę dodać publiczny adres IP mojego Azure-SSIS IR do listy dozwolonych przez zaporę dla moich źródeł danych?

  Możesz teraz przenieść własne statyczne publiczne adresy IP do Azure-SSIS IR. W takim przypadku można dodać adresy IP do listy dozwolonych w zaporze dla źródeł danych. Możesz również uwzględnić inne poniższe opcje, aby zabezpieczyć dostęp do danych z Azure-SSIS IR w zależności od scenariusza:

  - Jeśli źródło danych jest lokalne, po nawiązaniu połączenia z siecią wirtualną z siecią lokalną i dołączeniu Azure-SSIS IR do podsieci sieci wirtualnej można dodać zakres prywatnych adresów IP tej podsieci do listy dozwolonych dla danego źródła danych.
  - Jeśli źródło danych jest usługą platformy Azure, która obsługuje punkty końcowe usługi sieci wirtualnej, można skonfigurować punkt końcowy usługi sieci wirtualnej w podsieci sieci wirtualnej i dołączyć do Azure-SSIS IR do tej podsieci. Następnie można dodać regułę sieci wirtualnej z tą podsiecią do zapory dla źródła danych.
  - Jeśli źródło danych jest usługą w chmurze spoza platformy Azure, możesz użyć UDR do kierowania ruchu wychodzącego z Azure-SSIS IR do zapory urządzenie WUS/Azure za pośrednictwem statycznego publicznego adresu IP. Następnie można dodać statyczny publiczny adres IP zapory urządzenie WUS/Azure do listy dozwolonych przez zaporę dla źródła danych.
  - Jeśli żadna z powyższych opcji nie spełnia Twoich potrzeb, należy rozważyć skonfigurowanie samodzielnego [środowiska IR jako serwera proxy dla Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). Następnie można dodać statyczny publiczny adres IP maszyny, która hostuje własne środowisko IR na liście dozwolonych w zaporze dla źródła danych.

- Dlaczego muszę podać dwa statyczne adresy publiczne, jeśli chcę utworzyć własne dla Azure-SSIS IR?

  Azure-SSIS IR jest regularnie aktualizowana automatycznie. Podczas uaktualniania tworzone są nowe węzły, które zostaną usunięte. Jednak aby uniknąć przestoju, stare węzły nie zostaną usunięte, dopóki nowe nie będą gotowe. W ten sposób pierwszy statyczny publiczny adres IP używany przez stare węzły nie może zostać natychmiast wydzierżawiony, a do utworzenia nowych węzłów potrzebny jest drugi statyczny publiczny adres IP.

- Mam własne statyczne publiczne adresy IP dla Azure-SSIS IR, ale dlaczego nadal nie można uzyskać dostępu do moich źródeł danych?

  - Upewnij się, że dwa statyczne publiczne adresy IP są dodawane do listy dozwolonych w zaporze dla źródeł danych. Za każdym razem, gdy Azure-SSIS IR jest uaktualniana, jego statyczny publiczny adres IP jest przełączany między tymi, które zostały przez Ciebie połączone. Jeśli dodasz tylko jeden z nich do listy dozwolonych, dostęp do danych dla Azure-SSIS IR zostanie przerwany po uaktualnieniu.
  - Jeśli źródło danych jest usługą platformy Azure, sprawdź, czy została ona skonfigurowana za pomocą punktów końcowych usługi sieci wirtualnej. W takim przypadku ruch z Azure-SSIS IR do źródła danych zostanie przełączony w celu użycia prywatnych adresów IP zarządzanych przez usługi platformy Azure, a dodanie własnych statycznych publicznych adresów IP do listy dozwolonych przez zaporę dla źródła danych nie zacznie obowiązywać.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (Data Factory interfejsie użytkownika)

W tej sekcji pokazano, jak przyłączyć istniejące Azure-SSIS IR do sieci wirtualnej (klasycznej lub Azure Resource Manager) za pomocą interfejsu użytkownika Azure Portal i Data Factory. 

Przed przyłączeniem Azure-SSIS IR do sieci wirtualnej należy prawidłowo skonfigurować sieć wirtualną. Postępuj zgodnie z instrukcjami w sekcji, które dotyczą typu sieci wirtualnej (klasyczne lub Azure Resource Manager). Następnie postępuj zgodnie z instrukcjami w trzeciej sekcji, aby dołączać Azure-SSIS IR do sieci wirtualnej. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Konfigurowanie sieci wirtualnej Azure Resource Manager

Użyj portalu, aby skonfigurować sieć wirtualną Azure Resource Manager przed podjęciem próby dołączenia do niej Azure-SSIS IR.

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki sieci Web obsługują interfejs użytkownika Data Factory. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. Wybierz pozycję **więcej usług**. Odfiltruj i wybierz **sieci wirtualne**. 

1. Odfiltruj i wybierz sieć wirtualną na liście. 

1. Na stronie **Sieć wirtualna** wybierz pozycję **Właściwości**. 

1. Wybierz przycisk Kopiuj dla **identyfikatora zasobu** , aby skopiować identyfikator zasobu dla sieci wirtualnej do Schowka. Zapisz identyfikator ze schowka w programie OneNote lub pliku. 

1. Z menu po lewej stronie wybierz pozycję **podsieci**. Upewnij się, że liczba dostępnych adresów jest większa niż węzły w Azure-SSIS IR. 

1. Sprawdź, czy dostawca Azure Batch jest zarejestrowany w subskrypcji platformy Azure z siecią wirtualną. Lub Zarejestruj dostawcę Azure Batch. Jeśli masz już konto Azure Batch w subskrypcji, Twoja subskrypcja jest zarejestrowana na potrzeby Azure Batch. (Jeśli utworzysz Azure-SSIS IR w portalu Data Factory, dostawca Azure Batch zostanie automatycznie zarejestrowany.) 

   1. W Azure Portal w menu po lewej stronie wybierz pozycję **subskrypcje**. 

   1. Wybierz subskrypcję. 

   1. Po lewej stronie wybierz pozycję **dostawcy zasobów** i upewnij się, że **Microsoft.Batch** jest zarejestrowanym dostawcą. 

   ![Potwierdzenie stanu zarejestrowanego](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli na liście nie widzisz **Microsoft.Batch** , aby zarejestrować ją, [utwórz puste konto Azure Batch](../batch/batch-account-create-portal.md) w subskrypcji. Można go później usunąć. 

### <a name="configure-a-classic-virtual-network"></a>Konfigurowanie klasycznej sieci wirtualnej

Użyj portalu, aby skonfigurować klasyczną sieć wirtualną przed podjęciem próby dołączenia do niej Azure-SSIS IR. 

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki sieci Web obsługują interfejs użytkownika Data Factory. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. Wybierz pozycję **więcej usług**. Filtruj i wybieraj **sieci wirtualne (klasyczne)**. 

1. Odfiltruj i wybierz sieć wirtualną na liście. 

1. Na stronie **Sieć wirtualna (klasyczna)** wybierz pozycję **Właściwości**. 

   ![Identyfikator zasobu klasycznej sieci wirtualnej](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Wybierz przycisk Kopiuj dla **identyfikatora zasobu** , aby skopiować identyfikator zasobu dla sieci klasycznej do Schowka. Zapisz identyfikator ze schowka w programie OneNote lub pliku. 

1. Z menu po lewej stronie wybierz pozycję **podsieci**. Upewnij się, że liczba dostępnych adresów jest większa niż węzły w Azure-SSIS IR. 

   ![Liczba dostępnych adresów w sieci wirtualnej](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Dołącz **MicrosoftAzureBatch** do roli **współautora klasycznej maszyny wirtualnej** dla sieci wirtualnej. 

   1. W menu po lewej stronie wybierz pozycję **Kontrola dostępu (IAM)**, a następnie wybierz kartę **przypisania ról** . 

       ![Przyciski "kontrola dostępu" i "Dodaj"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Wybierz pozycję **Dodaj przypisanie roli**.

   1. Na stronie **Dodaj przypisanie roli** w polu **rola** wybierz opcję **współautor klasycznej maszyny wirtualnej**. W polu **wyboru** wklej **ddbf3205-c6bd-46ae-8127-60eb93363864**, a następnie wybierz pozycję **Microsoft Azure Batch** z listy wyników wyszukiwania. 

       ![Wyniki wyszukiwania na stronie "Dodawanie przypisania roli"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Wybierz pozycję **Zapisz** , aby zapisać ustawienia, a następnie zamknij stronę. 

       ![Zapisz ustawienia dostępu](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Upewnij się, że na liście współautorów jest widoczny **Microsoft Azure Batch** . 

       ![Potwierdź dostęp Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Sprawdź, czy dostawca Azure Batch jest zarejestrowany w subskrypcji platformy Azure z siecią wirtualną. Lub Zarejestruj dostawcę Azure Batch. Jeśli masz już konto Azure Batch w subskrypcji, Twoja subskrypcja jest zarejestrowana na potrzeby Azure Batch. (Jeśli utworzysz Azure-SSIS IR w portalu Data Factory, dostawca Azure Batch zostanie automatycznie zarejestrowany.) 

   1. W Azure Portal w menu po lewej stronie wybierz pozycję **subskrypcje**. 

   1. Wybierz subskrypcję. 

   1. Po lewej stronie wybierz pozycję **dostawcy zasobów** i upewnij się, że **Microsoft.Batch** jest zarejestrowanym dostawcą. 

   ![Potwierdzenie stanu zarejestrowanego](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli na liście nie widzisz **Microsoft.Batch** , aby zarejestrować ją, [utwórz puste konto Azure Batch](../batch/batch-account-create-portal.md) w subskrypcji. Można go później usunąć. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Dołącz Azure-SSIS IR do sieci wirtualnej

Po skonfigurowaniu sieci wirtualnej Azure Resource Manager lub klasycznej sieci wirtualnej można przyłączyć Azure-SSIS IR do sieci wirtualnej:

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki sieci Web obsługują interfejs użytkownika Data Factory. 

1. W [Azure Portal](https://portal.azure.com)w menu po lewej stronie wybierz pozycję **fabryki danych**. Jeśli nie widzisz **fabryk danych** w menu, wybierz pozycję **więcej usług**, a następnie w sekcji **Analiza i analiza** wybierz pozycję **fabryki danych**. 

   ![Lista fabryk danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Wybierz fabrykę danych z Azure-SSIS IR na liście. Zostanie wyświetlona strona główna fabryki danych. Wybierz kafelek **tworzenie & monitor** . Na oddzielnej karcie zobaczysz interfejs użytkownika Data Factory. 

   ![Strona główna fabryki danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. W interfejsie użytkownika Data Factory przejdź do karty **Edycja** , wybierz pozycję **połączenia** i przejdź do karty **środowisko Integration Runtime** . 

   ![Karta "Integration Runtimes"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Jeśli Azure-SSIS IR jest uruchomiona, na liście **środowiska Integration Runtime** w kolumnie **Akcje** wybierz przycisk **Zatrzymaj** dla Azure-SSIS IR. Nie można edytować Azure-SSIS IR, dopóki nie zostanie zatrzymane. 

   ![Zatrzymaj środowisko IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na liście **środowiska Integration Runtime** w kolumnie **Akcje** wybierz przycisk **Edytuj** dla Azure-SSIS IR. 

   ![Edytuj środowisko Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. W panelu ustawień środowiska Integration Runtime przejdź do sekcji **Ustawienia ogólne** i **Ustawienia SQL** , wybierając przycisk **dalej** . 

1. W sekcji **Ustawienia zaawansowane** : 

   1. Wybierz **sieć wirtualną, do której chcesz dołączyć Azure-SSIS Integration Runtime, Zezwól na automatyczny dostęp do zasobów sieciowych, a opcjonalnie zaznacz pole wyboru statyczne publiczne adresy IP** . 

   1. W obszarze **subskrypcja** wybierz subskrypcję platformy Azure, która ma swoją sieć wirtualną.

   1. W polu **Lokalizacja** jest wybierana ta sama lokalizacja środowiska Integration Runtime.

   1. W polu **Typ** wybierz typ sieci wirtualnej: klasyczny lub Azure Resource Manager. Zalecamy wybranie Azure Resource Manager sieci wirtualnej, ponieważ klasyczne sieci wirtualne będą wkrótce przestarzałe.

   1. W polu **Nazwa** sieci wirtualnej wybierz nazwę swojej usługi. Powinien być to ten sam, który służy do SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym SQL z prywatnym punktem końcowym do hostowania SSISDB. Lub powinien być taki sam, jak jeden połączony z siecią lokalną. W przeciwnym razie może to być dowolna Sieć wirtualna do przenoszenia własnych statycznych publicznych adresów IP dla Azure-SSIS IR.

   1. W polu **Nazwa podsieci** wybierz nazwę podsieci dla sieci wirtualnej. Powinien być to ten sam, który służy do SQL Database z punktami końcowymi usługi sieci wirtualnej do hostowania SSISDB. Lub powinien być inną podsiecią niż używana do wystąpienia zarządzanego SQL z prywatnym punktem końcowym do hostowania SSISDB. W przeciwnym razie może być dowolna podsieć do przenoszenia własnych statycznych publicznych adresów IP dla Azure-SSIS IR.

   1. Zaznacz pole wyboru **Przenieś statyczne publiczne adresy IP dla Azure-SSIS Integration Runtime** , aby wybrać, czy chcesz przenieść własne statyczne publiczne adresy ip dla Azure-SSIS IR, dzięki czemu możesz zezwolić na dostęp do nich w zaporze dla źródeł danych.

      Jeśli zaznaczysz pole wyboru, wykonaj następujące czynności.

      1. W polu **pierwszy statyczny publiczny adres IP** wybierz pierwszy statyczny publiczny adres IP, który [spełnia wymagania](#publicIP) Azure-SSIS IR. Jeśli nie masz żadnego z tych opcji, kliknij przycisk **Utwórz nowy** link, aby utworzyć statyczne publiczne adresy IP na Azure Portal a następnie kliknij przycisk Odśwież tutaj, aby je wybrać.
      
      1. W polu **drugi statyczny publiczny adres IP** wybierz drugi statyczny publiczny adres IP, który [spełnia wymagania](#publicIP) Azure-SSIS IR. Jeśli nie masz żadnego z tych opcji, kliknij przycisk **Utwórz nowy** link, aby utworzyć statyczne publiczne adresy IP na Azure Portal a następnie kliknij przycisk Odśwież tutaj, aby je wybrać.

   1. Wybierz pozycję **weryfikacja sieci wirtualnej**. Jeśli sprawdzanie poprawności zakończy się pomyślnie, wybierz pozycję **Kontynuuj**. 

   ![Ustawienia zaawansowane sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Na stronie **Podsumowanie** Przejrzyj wszystkie ustawienia Azure-SSIS IR. Następnie wybierz pozycję **Aktualizuj**.

1. Rozpocznij Azure-SSIS IR, wybierając przycisk **Start** w kolumnie **Akcje** dla Azure-SSIS IR. Uruchomienie Azure-SSIS IR dołączania do sieci wirtualnej trwa od 20 do 30 minut. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Definiuj zmienne

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Konfigurowanie sieci wirtualnej

Aby można było przyłączyć Azure-SSIS IR do sieci wirtualnej, należy skonfigurować sieć wirtualną. Aby automatycznie skonfigurować uprawnienia i ustawienia sieci wirtualnej dla Azure-SSIS IR w celu dołączenia do sieci wirtualnej, Dodaj następujący skrypt:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Tworzenie Azure-SSIS IR i dołączanie do sieci wirtualnej

Można utworzyć Azure-SSIS IR i przyłączyć ją do sieci wirtualnej w tym samym czasie. Aby uzyskać kompletny skrypt i instrukcje, zobacz [tworzenie Azure-SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Dołącz do istniejącej Azure-SSIS IR do sieci wirtualnej

W artykule [tworzenie Azure-SSIS IR](create-azure-ssis-integration-runtime.md) pokazano, jak utworzyć Azure-SSIS IR i dołączyć go do sieci wirtualnej w tym samym skrypcie. Jeśli masz już Azure-SSIS IR, wykonaj następujące kroki, aby dołączyć je do sieci wirtualnej: 
1. Zatrzymaj Azure-SSIS IR. 
1. Skonfiguruj Azure-SSIS IR, aby dołączyć do sieci wirtualnej. 
1. Uruchom Azure-SSIS IR. 

### <a name="stop-the-azure-ssis-ir"></a>Zatrzymaj Azure-SSIS IR

Aby można było przyłączyć się do sieci wirtualnej, należy zatrzymać Azure-SSIS IR. To polecenie zwalnia wszystkie jego węzły i przerywa rozliczanie:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Skonfiguruj ustawienia sieci wirtualnej dla Azure-SSIS IR do przyłączenia

Aby skonfigurować ustawienia dla sieci wirtualnej, do których zostanie dołączona platforma Azure-SSIS, użyj tego skryptu: 

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

### <a name="configure-the-azure-ssis-ir"></a>Skonfiguruj Azure-SSIS IR

Aby dołączyć Azure-SSIS IR do sieci wirtualnej, uruchom `Set-AzDataFactoryV2IntegrationRuntime` polecenie: 

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

### <a name="start-the-azure-ssis-ir"></a>Uruchom Azure-SSIS IR

Aby uruchomić Azure-SSIS IR, uruchom następujące polecenie: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

To polecenie zajmie od 20 do 30 minut.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure-SSIS IR, zobacz następujące artykuły: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera ogólne informacje koncepcyjne dotyczące urzędu skarbowego, w tym Azure-SSIS IR. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](./tutorial-deploy-ssis-packages-azure.md). Ten samouczek zawiera instrukcje krok po kroku dotyczące tworzenia Azure-SSIS IR. Używa Azure SQL Database do hostowania wykazu usług SSIS. 
- [Utwórz Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Ten artykuł rozszerza się w samouczku. Zawiera instrukcje dotyczące używania Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym SQL w sieci wirtualnej do hostowania wykazu usług SSIS. Przedstawiono sposób przyłączania Azure-SSIS IR do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule pokazano, jak uzyskać informacje o Azure-SSIS IR. Zawiera opisy stanu dla zwracanych informacji. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania Azure-SSIS IR. Przedstawiono w nim również sposób skalowania Azure-SSIS IR przez dodanie węzłów.
