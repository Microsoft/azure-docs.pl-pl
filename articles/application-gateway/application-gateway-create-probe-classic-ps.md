---
title: Tworzenie niestandardowej sondy przy użyciu klasycznego modelu wdrażania — Application Gateway platformy Azure
description: Dowiedz się, jak utworzyć niestandardową sondę dla Application Gateway przy użyciu programu PowerShell w klasycznym modelu wdrażania
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 1445d1418bde6d5d15e365c59ceb56e7661faccb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088074"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Tworzenie niestandardowej sondy dla platformy Azure Application Gateway (klasyczny) przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-create-probe-ps.md)
> * [Klasyczny portal Azure — program PowerShell](application-gateway-create-probe-classic-ps.md)

W tym artykule opisano Dodawanie niestandardowej sondy do istniejącej bramy aplikacji przy użyciu programu PowerShell. Niestandardowe sondy są przydatne w przypadku aplikacji, które mają konkretną kontrolę kondycji lub dla aplikacji, które nie zapewniają prawidłowej odpowiedzi w domyślnej aplikacji sieci Web.

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../azure-resource-manager/management/deployment-models.md). W tym artykule opisano korzystanie z klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.  Dowiedz się, jak [wykonać te kroki w modelu przy użyciu usługi Resource Manager](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Aby utworzyć bramę aplikacji:

1. Utwórz zasób bramy aplikacji.
2. Utwórz konfiguracyjny plik XML lub obiekt konfiguracji.
3. Przekaż konfigurację aplikacji do nowo utworzonego zasobu bramy aplikacji.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Tworzenie zasobu bramy aplikacji za pomocą sondy niestandardowej

Aby utworzyć bramę, użyj polecenia cmdlet `New-AzureApplicationGateway`, zastępując wartości własnymi. Opłaty za bramę nie są jeszcze naliczane. Rozliczanie zaczyna się na późniejszym etapie, po pomyślnym uruchomieniu bramy.

Poniższy przykład tworzy bramę aplikacji przy użyciu sieci wirtualnej „testvnet1” i podsieci „subnet-1”.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Aby sprawdzić, czy brama została utworzona, możesz użyć polecenia cmdlet `Get-AzureApplicationGateway`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> Wartość domyślna parametru *InstanceCount* to 2, a wartość maksymalna — 10. Wartość domyślna parametru *GatewaySize* to Medium (Średnia). Można wybrać opcję mały, średni i duży.
> 
> 

Parametry *VirtualIPs* (Wirtualne adresy IP) i *DnsName* (Nazwa serwera DNS) są wyświetlane jako puste, ponieważ brama nie została jeszcze uruchomiona. Te wartości są tworzone, gdy Brama jest w stanie uruchomienia.

### <a name="configure-an-application-gateway-by-using-xml"></a>Konfigurowanie bramy aplikacji przy użyciu języka XML

W poniższym przykładzie używany jest plik XML, aby skonfigurować wszystkie ustawienia bramy aplikacji i zatwierdzić je w zasobie bramy aplikacji.  

Skopiuj poniższy tekst do Notatnika.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Edytuj zawarte w nawiasach wartości elementów konfiguracji. Zapisz plik z rozszerzeniem .xml.

Poniższy przykład pokazuje, jak użyć pliku konfiguracji w celu skonfigurowania bramy aplikacji do równoważenia obciążenia ruchu HTTP na porcie publicznym 80 i wysyłania ruchu sieciowego do portu z zapleczem 80 między dwoma adresami IP przy użyciu sondy niestandardowej.

> [!IMPORTANT]
> W elemencie Http lub Https jest rozróżniana wielkość liter.

Dodano nowy element konfiguracji \<Probe\> w celu skonfigurowania sond niestandardowych.

Parametry konfiguracji są następujące:

|Parametr|Opis|
|---|---|
|**Nazwa** |Nazwa odwołania dla sondy niestandardowej. |
| **Protokół** | Używany protokół (możliwe wartości to HTTP lub HTTPS).|
| **Host** i **ścieżka** | Pełna ścieżka URL, która jest wywoływana przez bramę aplikacji w celu określenia kondycji wystąpienia. Na przykład jeśli masz witrynę sieci Web http: \/ /contoso.com/, można skonfigurować niestandardową sondę dla "http: \/ /contoso.com/Path/custompath.htm", aby testy sondy miały POMYŚLNE odpowiedzi HTTP.|
| **Interwał** | Konfiguruje testy interwału sondowania w sekundach.|
| **Limit czasu** | Określa limit czasu sondy dla sprawdzania odpowiedzi HTTP.|
| **UnhealthyThreshold** | Liczba nieprawidłowych odpowiedzi HTTP wymaganych do oflagowania wystąpienia zaplecza jako *złej kondycji*.|

Nazwa sondy jest przywoływana w \<BackendHttpSettings\> konfiguracji, aby przypisać pulę zaplecza, która używa niestandardowych ustawień sondowania.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Dodaj niestandardową sondę do istniejącej bramy aplikacji

Zmiana bieżącej konfiguracji bramy aplikacji wymaga wykonania trzech kroków: pobrać bieżący plik konfiguracyjny XML, zmodyfikować w taki sposób, aby miał niestandardową sondę i skonfigurować bramę aplikacji przy użyciu nowych ustawień XML.

1. Pobierz plik XML za pomocą polecenia `Get-AzureApplicationGatewayConfig` . To polecenie cmdlet eksportuje plik XML konfiguracji, aby mógł zostać zmodyfikowany w celu dodania ustawienia sondowania.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Otwórz plik XML w edytorze tekstu. Dodaj `<probe>` sekcję po `<frontendport>` .

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   W sekcji backendHttpSettings w pliku XML Dodaj nazwę sondy, jak pokazano w następującym przykładzie:

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   Zapisz plik XML.

1. Zaktualizuj konfigurację bramy aplikacji za pomocą nowego pliku XML przy użyciu polecenia `Set-AzureApplicationGatewayConfig` . To polecenie cmdlet aktualizuje bramę aplikacji przy użyciu nowej konfiguracji.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować usługę Transport Layer Security (TLS), która wcześniej znana jako odciążanie SSL (SSL), zobacz [Konfigurowanie bramy aplikacji na potrzeby odciążania protokołu TLS](application-gateway-ssl.md).

Jeśli chcesz skonfigurować bramę aplikacji do użycia z wewnętrznym modułem równoważenia obciążenia, zobacz artykuł [Create an application gateway with an internal load balancer (ILB)](application-gateway-ilb.md) (Tworzenie bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia).

