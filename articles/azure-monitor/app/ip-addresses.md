---
title: Adresy IP używane przez Azure Monitor
description: Wyjątki zapory serwera wymagane przez Application Insights
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 56ff33cc0a34cb254ca88f96d69a07bc131bebf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714038"
---
# <a name="ip-addresses-used-by-azure-monitor"></a>Adresy IP używane przez Azure Monitor

[Azure monitor](../overview.md) używa wielu adresów IP. Azure Monitor składa się z podstawowych metryk platformy i dzienników oprócz Log Analytics i Application Insights. Może być konieczne poznanie tych adresów, jeśli monitorowana aplikacja lub infrastruktura jest hostowana za zaporą.

> [!NOTE]
> Chociaż te adresy są statyczne, istnieje możliwość, że będziemy musieli zmienić je od czasu do czasu. Cały ruch Application Insights reprezentuje ruch wychodzący z wyjątkiem monitorowania dostępności i elementów webhook, które wymagają reguł zapory dla ruchu przychodzącego.

> [!TIP]
> Za pomocą [tagów usługi sieciowej](../../virtual-network/service-tags-overview.md) platformy Azure można zarządzać dostępem, jeśli są używane grupy zabezpieczeń sieci platformy Azure. Jeśli zarządzasz dostępem do zasobów hybrydowych/lokalnych, możesz pobrać równoważne listy adresów IP jako [pliki JSON](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) , które są aktualizowane w każdym tygodniu:. Aby uwzględnić wszystkie wyjątki w tym artykule, należy użyć tagów usługi: `ActionGroup` , `ApplicationInsightsAvailability` , i `AzureMonitor` .

Alternatywnie można subskrybować Tę stronę jako źródło danych RSS, dodając https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom do ulubionego czytnika RSS/Atom, aby otrzymywać powiadomienia o najnowszych zmianach.


## <a name="outgoing-ports"></a>Porty wychodzące

Należy otworzyć niektóre porty wychodzące w zaporze serwera, aby zezwolić Application Insights SDK i/lub monitor stanu na wysyłanie danych do portalu:

| Przeznaczenie | Adres URL | Adres IP | Porty |
| --- | --- | --- | --- |
| Telemetria |dc.applicationinsights.azure.com<br/>dc.applicationinsights.microsoft.com<br/>dc.services.visualstudio.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248<br/>13.69.66.140<br/>13.77.52.29<br/>51.107.59.180<br/>40.71.12.235<br/>20.44.8.10<br/>40.71.13.169<br/>13.66.141.156<br/>40.71.13.170<br/>13.69.65.23<br/>20.44.17.0<br/>20.36.114.207 <br/>51.116.155.246 <br/>51.107.155.178 <br/>51.140.212.64 <br/>13.86.218.255 <br/>20.37.74.240 <br/>65.52.250.236 <br/>13.69.229.240 <br/>52.236.186.210<br/>52.167.107.65<br/>40.71.12.237<br/>40.78.229.32<br/>40.78.229.33<br/>51.105.67.161<br/>40.124.64.192<br/>20.44.12.194<br/>20.189.172.0<br/>13.69.106.208<br/>40.78.253.199<br/>40.78.253.198<br/>40.78.243.19 | 443 |
| Transmisja strumieniowa metryk na żywo | live.applicationinsights.azure.com<br/>rt.applicationinsights.microsoft.com<br/>rt.services.visualstudio.com|23.96.28.38<br/>13.92.40.198<br/>40.112.49.101<br/>40.117.80.207<br/>157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113| 443 |

## <a name="status-monitor"></a>Monitor stanu

Konfiguracja monitor stanu — wymagana tylko w przypadku wprowadzania zmian.

| Przeznaczenie | Adres URL | Adres IP | Porty |
| --- | --- | --- | --- |
| Konfigurowanie |`management.core.windows.net` | |`443` |
| Konfigurowanie |`management.azure.com` | |`443` |
| Konfigurowanie |`login.windows.net` | |`443` |
| Konfigurowanie |`login.microsoftonline.com` | |`443` |
| Konfigurowanie |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Konfigurowanie |`auth.gfx.ms` | |`443` |
| Konfigurowanie |`login.live.com` | |`443` |
| Instalacja | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`, `dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>Testy dostępności

Jest to lista adresów, z których są uruchamiane [testy sieci Web dostępności](./monitor-web-app-availability.md) . Jeśli chcesz uruchomić testy sieci Web w aplikacji, ale serwer sieci Web jest ograniczony do obsługi określonych klientów, musisz zezwolić na ruch przychodzący z naszych serwerów testowych dostępności.


> [!NOTE]
> W przypadku zasobów znajdujących się w prywatnych sieciach wirtualnych, które nie mogą zezwalać na bezpośrednią komunikację przychodzącą z agentami testów dostępności na publicznej platformie Azure, jedyną opcją jest [utworzenie i hostowanie własnych niestandardowych testów dostępności](availability-azure-functions.md).

### <a name="service-tag"></a>Tag usługi

Jeśli używasz grup zabezpieczeń sieci platformy Azure, po prostu Dodaj **regułę portów przychodzących** , aby zezwolić na ruch z Application Insights testów dostępności, **wybierając tag usługi** jako **Źródło** i **ApplicationInsightsAvailability** jako **tag usługi źródłowej**.

>[!div class="mx-imgBorder"]
>![W obszarze Ustawienia wybierz pozycję Reguły zabezpieczeń dla ruchu przychodzącego, a następnie wybierz pozycję Dodaj w górnej części karty. ](./media/ip-addresses/add-inbound-security-rule.png)

>[!div class="mx-imgBorder"]
>![Karta Dodaj regułę zabezpieczeń dla ruchu przychodzącego](./media/ip-addresses/add-inbound-security-rule2.png)

Otwórz porty 80 (http) i 443 (https) dla ruchu przychodzącego z tych adresów (adresy IP są pogrupowane według lokalizacji):

### <a name="addresses-grouped-by-location"></a>Adresy pogrupowane według lokalizacji

> [!NOTE]
> Te adresy są wymienione w notacji CIDR (Classless Inter-Domain Routing). Oznacza to, że wpis podobny `51.144.56.112/28` do 16 adresów IP rozpoczyna się od `51.144.56.112` i kończy się na `51.144.56.127` .

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```  

#### <a name="azure-government"></a>Azure Government

Niepotrzebne, jeśli jesteś klientem chmury publicznej platformy Azure.

```
USGov Virginia
52.227.229.80/31


USGov Arizona
52.244.35.112/31


USGov Texas
52.243.157.80/31


USDoD Central
52.182.23.96/31


USDoD East
52.181.33.96/31

```

## <a name="application-insights--log-analytics-apis"></a>Application Insights & Log Analytics interfejsów API

| Przeznaczenie | URI |  Adres IP | Porty |
| --- | --- | --- | --- |
| Interfejs API |`api.applicationinsights.io`<br/>`api1.applicationinsights.io`<br/>`api2.applicationinsights.io`<br/>`api3.applicationinsights.io`<br/>`api4.applicationinsights.io`<br/>`api5.applicationinsights.io`<br/>`dev.applicationinsights.io`<br/>`dev.applicationinsights.microsoft.com`<br/>`dev.aisvc.visualstudio.com`<br/>`www.applicationinsights.io`<br/>`www.applicationinsights.microsoft.com`<br/>`www.aisvc.visualstudio.com`<br/>`api.loganalytics.io`<br/>`*.api.loganalytics.io`<br/>`dev.loganalytics.io`<br>`docs.loganalytics.io`<br/>`www.loganalytics.io` |20.37.52.188 <br/> 20.37.53.231 <br/> 20.36.47.130 <br/> 20.40.124.0 <br/> 20.43.99.158 <br/> 20.43.98.234 <br/> 13.70.127.61 <br/> 40.81.58.225 <br/> 20.40.160.120 <br/> 23.101.225.155 <br/> 52.139.8.32 <br/> 13.88.230.43 <br/> 52.230.224.237 <br/> 52.242.230.209 <br/> 52.173.249.138 <br/> 52.229.218.221 <br/> 52.229.225.6 <br/> 23.100.94.221 <br/> 52.188.179.229 <br/> 52.226.151.250 <br/> 52.150.36.187 <br/> 40.121.135.131 <br/> 20.44.73.196 <br/> 20.41.49.208 <br/> 40.70.23.205 <br/> 20.40.137.91 <br/> 20.40.140.212 <br/> 40.89.189.61 <br/> 52.155.118.97 <br/> 52.156.40.142 <br/> 23.102.66.132 <br/> 52.231.111.52 <br/> 52.231.108.46 <br/> 52.231.64.72 <br/> 52.162.87.50 <br/> 23.100.228.32 <br/> 40.127.144.141 <br/> 52.155.162.238 <br/> 137.116.226.81 <br/> 52.185.215.171 <br/> 40.119.4.128 <br/> 52.171.56.178 <br/> 20.43.152.45 <br/> 20.44.192.217 <br/> 13.67.77.233 <br/> 51.104.255.249 <br/> 51.104.252.13 <br/> 51.143.165.22 <br/> 13.78.151.158 <br/> 51.105.248.23 <br/> 40.74.36.208 <br/> 40.74.59.40 <br/> 13.93.233.49 <br/> 52.247.202.90 |80 443 |
| Rozszerzenie adnotacji potoku platformy Azure | aigs1.aisvc.visualstudio.com |dynamiczna|443 | 

## <a name="application-insights-analytics"></a>Analiza Application Insights

| Przeznaczenie | URI | Adres IP | Porty |
| --- | --- | --- | --- |
| Portal analizy | analytics.applicationinsights.io | dynamiczna | 80 443 |
| CDN | applicationanalytics.azureedge.net | dynamiczna | 80 443 |
| Usługa Media CDN | applicationanalyticsmedia.azureedge.net | dynamiczna | 80 443 |

Uwaga: *. applicationinsights.io domena jest własnością zespołu Application Insights.

## <a name="log-analytics-portal"></a>Portal Log Analytics

| Przeznaczenie | URI | Adres IP | Porty |
| --- | --- | --- | --- |
| Portal | portal.loganalytics.io | dynamiczna | 80 443 |
| CDN | applicationanalytics.azureedge.net | dynamiczna | 80 443 |

Uwaga: domena loganalytics.io jest własnością zespołu Log Analytics.

## <a name="application-insights-azure-portal-extension"></a>Rozszerzenie Azure Portal Application Insights

| Przeznaczenie | URI | Adres IP | Porty |
| --- | --- | --- | --- |
| Application Insights rozszerzenie | stamp2.app.insightsportal.visualstudio.com | dynamiczna | 80 443 |
| Application Insights rozszerzenia CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dynamiczna | 80 443 |

## <a name="application-insights-sdks"></a>Zestawy SDK Application Insights

| Przeznaczenie | URI | Adres IP | Porty |
| --- | --- | --- | --- |
| Application Insights JS SDK sieci CDN | az416426.vo.msecnd.net<br/>js.monitor.azure.com | dynamiczna | 80 443 |

## <a name="action-group-webhooks"></a>Elementy webhook grupy akcji

Można wysyłać zapytania do listy adresów IP używanych przez grupy akcji za pomocą [polecenia programu PowerShell Get-AzNetworkServiceTag](/powershell/module/az.network/Get-AzNetworkServiceTag).

### <a name="action-groups-service-tag"></a>Tag usługi grup akcji
Zarządzanie zmianami źródłowych adresów IP może być czasochłonne. Korzystanie z **tagów usługi** eliminuje konieczność aktualizowania konfiguracji. Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza adresami IP i automatycznie aktualizuje tag usługi jako adresy, eliminując konieczność aktualizowania reguł zabezpieczeń sieciowych dla grupy akcji.

1. W Azure Portal w obszarze usługi platformy Azure Wyszukaj *grupę zabezpieczeń sieci*.
2. Kliknij pozycję **Dodaj** i Utwórz sieciową grupę zabezpieczeń.

   1. Dodaj nazwę grupy zasobów, a następnie wprowadź *szczegóły wystąpienia*.
   1. Kliknij przycisk **Przegląd + Utwórz** , a następnie kliknij przycisk *Utwórz*.
   
   :::image type="content" source="../alerts/media/action-groups/action-group-create-security-group.png" alt-text="Przykład tworzenia sieciowej grupy zabezpieczeń."border="true":::

3. Przejdź do grupy zasobów, a następnie kliknij utworzoną *grupę zabezpieczeń sieci* .

    1. Wybierz pozycję *reguły zabezpieczeń dla ruchu przychodzącego*.
    1. Kliknij przycisk **Dodaj**.
    
    :::image type="content" source="../alerts/media/action-groups/action-group-add-service-tag.png" alt-text="Przykład dodawania znacznika usługi." border="true":::

4. Nowe okno zostanie otwarte w okienku po prawej stronie.
    1.  Wybierz źródło: **tag usługi**
    1.  Tag usługi źródłowej: **ActionName**
    1.  Kliknij pozycję **Dodaj**.
    
    :::image type="content" source="../alerts/media/action-groups/action-group-service-tag.png" alt-text="Przykład dotyczący dodawania tagu usługi." border="true":::


## <a name="profiler"></a>Profiler

| Przeznaczenie | URI | Adres IP | Porty |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*. agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portal | gateway.azureserviceprofiler.net | dynamiczna | 443
| Storage | *.core.windows.net | dynamiczna | 443

## <a name="snapshot-debugger"></a>Debuger migawek

> [!NOTE]
> Profiler i Snapshot Debugger współużytkują ten sam zestaw adresów IP.

| Przeznaczenie | URI | Adres IP | Porty |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*. agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portal | gateway.azureserviceprofiler.net | dynamiczna | 443
| Storage | *.core.windows.net | dynamiczna | 443