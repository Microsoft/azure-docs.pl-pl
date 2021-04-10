---
title: Azure DDoS Protection Raporty standardowe i dzienniki przepływów
description: Informacje o konfigurowaniu raportów i dzienników przepływów.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 7f8e3df927b74cff7e4dc8bf1456600740c07088
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567682"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>Wyświetlanie i konfigurowanie rejestrowania diagnostycznego dotyczącego ataków DDoS

Azure DDoS Protection Standard zapewnia szczegółowe informacje o atakach i wizualizacje z DDoSą analizą ataków. Klienci chroniący sieci wirtualne przed atakami DDoS mają szczegółowy wgląd w ruch związany z atakami i działania podejmowane w celu ograniczenia ataku za pośrednictwem raportów ograniczenia ataków, które &ją dzienniki przepływów ograniczenia. Bogate dane telemetryczne są udostępniane za pośrednictwem Azure Monitor, w tym szczegółowych metryk w czasie trwania ataku DDoS. Można skonfigurować alerty dla dowolnych metryk usługi Azure Monitor ujawnionych przez usługę DDoS Protection. Rejestrowanie może być dodatkowo zintegrowane z [platformą Azure](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), pakietem OMS log Analytics i usługą Azure Storage, aby uzyskać zaawansowaną analizę za pośrednictwem interfejsu diagnostyki Azure monitor.

Następujące dzienniki diagnostyczne są dostępne dla Azure DDoS Protection standard: 

- **DDoSProtectionNotifications**: powiadomienia będą powiadamiane za każdym razem, gdy publiczny zasób IP jest w trakcie ataku, a w przypadku przekroczenia ograniczenia ataków.
- **DDoSMitigationFlowLogs**: osoby atakujące ograniczają dzienniki przepływów, dzięki czemu można zapoznać się z porzuconym ruchem, przesyłanym ruchem i innymi interesującymi punktami danych podczas aktywnego ataku DDoS w czasie niemal rzeczywistym. Możesz pozyskać stałe strumienie tych danych do usługi Azure wskaźnikowej lub do systemów SIEM innych firm za pośrednictwem centrum zdarzeń w celu monitorowania niemal w czasie rzeczywistym, podejmować potencjalne działania i rozwiązywać potrzeby operacji obrony.
- **DDoSMitigationReports**: raporty ograniczenia ataków używają danych protokołu sieci, które są agregowane w celu zapewnienia szczegółowych informacji na temat ataku na zasób. Gdy zasób publicznego adresu IP jest w trakcie ataku, generowanie raportu rozpocznie się zaraz po rozpoczęciu środków zaradczych. Zostanie wygenerowany raport przyrostowy generowany co 5 minut, a raport po pominięciu dotyczy całego okresu zaradczego. Ma to na celu zapewnienie, że w przypadku gdy atak DDoS będzie kontynuowany przez dłuższy czas, będzie można wyświetlić najbardziej aktualną migawkę raportu o ograniczeniach co 5 minut i pełne podsumowanie po przekroczeniu tego ograniczenia. 
- **AllMetrics**: udostępnia wszystkie możliwe metryki dostępne w czasie trwania ataku DDoS. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie dzienników diagnostycznych DDoS, w tym powiadomień, raportów o ograniczeniach i dzienników przepływów zaradczych. 
> * Włącz rejestrowanie diagnostyczne dla wszystkich publicznych adresów IP w zdefiniowanym zakresie.
> * Wyświetlanie danych dziennika w skoroszytach.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Przed wykonaniem kroków opisanych w tym samouczku należy najpierw utworzyć [Plan ochrony usługi Azure DDoS Standard](manage-ddos-protection.md) , a w sieci wirtualnej musi być włączona funkcja DDoS Protection Standard.
- DDoS monitoruje publiczne adresy IP przypisane do zasobów w ramach sieci wirtualnej. Jeśli nie masz żadnych zasobów z publicznymi adresami IP w sieci wirtualnej, musisz najpierw utworzyć zasób z publicznym adresem IP. Możesz monitorować publiczny adres IP wszystkich zasobów wdrożonych za pomocą Menedżer zasobów (nieklasyczny) wymieniony w [sieci wirtualnej dla usług platformy Azure (w](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) tym modułów równoważenia obciążenia platformy Azure, w których znajdują się maszyny wirtualne zaplecza w sieci wirtualnej), z wyjątkiem środowisk Azure App Service. Aby kontynuować pracę z tym samouczkiem, możesz szybko utworzyć maszynę wirtualną z [systemem Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .    

## <a name="configure-ddos-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych DDoS

Jeśli chcesz automatycznie włączyć rejestrowanie diagnostyczne dla wszystkich publicznych adresów IP w środowisku, pomiń to polecenie, aby [włączyć rejestrowanie diagnostyczne dla wszystkich publicznych adresów IP](#enable-diagnostic-logging-on-all-public-ips).

1. W lewym górnym rogu portalu wybierz pozycję **Wszystkie usługi**.
2. Wpisz *Monitor* w polu **Filtr**. Gdy w wynikach pojawi się pozycja **Monitor**, wybierz ją.
3. W obszarze **Ustawienia** wybierz pozycję **Ustawienia diagnostyczne**.
4. Wybierz **Subskrypcję** oraz **Grupę zasobów** zawierającą publiczny adres IP, który chcesz zarejestrować.
5. Wybierz pozycję **publiczny adres IP** dla opcji **Typ zasobu**, a następnie wybierz konkretny publiczny adres IP, dla którego chcesz włączyć dzienniki.
6. Wybierz pozycję **Dodaj ustawienia diagnostyczne**. W obszarze **Szczegóły kategorii** wybierz tyle poniższych opcji, ile musisz, a następnie wybierz pozycję **Zapisz**.

    ![Ustawienia diagnostyki DDoS](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. W obszarze **szczegóły miejsca docelowego** wybierz dowolną z następujących opcji, ile potrzebujesz:

    - **Archiwizowanie na koncie magazynu**: dane są zapisywane na koncie usługi Azure Storage. Aby dowiedzieć się więcej na temat tej opcji, zobacz [Archiwizowanie dzienników zasobów](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Przesyłaj strumieniowo do centrum zdarzeń**: umożliwia odbiornikowi dziennika pobieranie dzienników przy użyciu centrum zdarzeń platformy Azure. Centra zdarzeń umożliwiają integrację z systemem Splunk lub innymi systemami SIEM. Aby dowiedzieć się więcej na temat tej opcji, zobacz [przesyłanie strumieniowe dzienników zasobów do centrum zdarzeń](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Wyślij do log Analytics**: zapisuje dzienniki w usłudze Azure monitor. Aby dowiedzieć się więcej na temat tej opcji, zobacz [zbieranie dzienników do użycia w dziennikach Azure monitor](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

### <a name="log-schemas"></a>Schematy dzienników

W poniższej tabeli wymieniono nazwy pól i opisy:

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| Nazwa pola | Opis |
| --- | --- |
| **TimeGenerated** | Data i godzina w formacie UTC utworzenia powiadomienia. |
| **ResourceId** | Identyfikator zasobu publicznego adresu IP. |
| **Kategoria** | W przypadku powiadomień będzie to możliwe `DDoSProtectionNotifications` .|
| **ResourceGroup** | Grupa zasobów zawierająca publiczny adres IP i sieć wirtualną. |
| **Identyfikator** | Identyfikator subskrypcji planu ochrony DDoS. |
| **Zasób** | Nazwa publicznego adresu IP. |
| **ResourceType** | Zawsze będzie to możliwe `PUBLICIPADDRESS` . |
| **OperationName** | W przypadku powiadomień będzie to możliwe `DDoSProtectionNotifications` .  |
| **Wiadomość** | Szczegóły ataku. |
| **Typ** | Typ powiadomienia. Możliwe wartości to: `MitigationStarted` . `MitigationStopped`. |
| **PublicIpAddress** | Publiczny adres IP. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| Nazwa pola | Opis |
| --- | --- |
| **TimeGenerated** | Data i godzina w formacie UTC utworzenia dziennika przepływu. |
| **ResourceId** | Identyfikator zasobu publicznego adresu IP. |
| **Kategoria** | W przypadku dzienników przepływów będzie to możliwe `DDoSMitigationFlowLogs` .|
| **ResourceGroup** | Grupa zasobów zawierająca publiczny adres IP i sieć wirtualną. |
| **Identyfikator** | Identyfikator subskrypcji planu ochrony DDoS. |
| **Zasób** | Nazwa publicznego adresu IP. |
| **ResourceType** | Zawsze będzie to możliwe `PUBLICIPADDRESS` . |
| **OperationName** | W przypadku dzienników przepływów będzie to możliwe `DDoSMitigationFlowLogs` . |
| **Wiadomość** | Szczegóły ataku. |
| **SourcePublicIpAddress** | Publiczny adres IP klienta generującego ruch do publicznego adresu IP. |
| **SourcePort** | Numer portu z zakresu od 0 do 65535. |
| **DestPublicIpAddress** | Publiczny adres IP. |
| **DestPort** | Numer portu z zakresu od 0 do 65535. |
| **Protokół** | Typ protokołu. Możliwe wartości to `tcp` , `udp` , `other` .|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| Nazwa pola | Opis |
| --- | --- |
| **TimeGenerated** | Data i godzina w formacie UTC utworzenia raportu. |
| **ResourceId** | Identyfikator zasobu publicznego adresu IP. |
| **Kategoria** | W przypadku powiadomień będzie to możliwe `DDoSProtectionNotifications` .|
| **ResourceGroup** | Grupa zasobów zawierająca publiczny adres IP i sieć wirtualną. |
| **Identyfikator** | Identyfikator subskrypcji planu ochrony DDoS. |
| **Zasób** | Nazwa publicznego adresu IP. |
| **ResourceType** | Zawsze będzie to możliwe `PUBLICIPADDRESS` . |
| **OperationName** | W przypadku raportów zaradczych będzie to możliwe `DDoSMitigationReports` . |
| **ReportType** | Możliwe wartości to: `Incremental` , `PostMitigation` .|
| **MitigationPeriodStart** | Data i godzina w formacie UTC rozpoczęcia rozwiązywania problemów.  |
| **MitigationPeriodEnd** | Data i godzina w formacie UTC zakończenia środków zaradczych. |
| **IPAddress** | Publiczny adres IP. |
| **AttackVectors** |  Podział typów ataków. Klucze obejmują `TCP SYN flood` , `TCP flood` , `UDP flood` , `UDP reflection` , `Other packet flood` .|
| **TrafficOverview** |  Podział ruchu ataków. Klucze obejmują `Total packets` , `Total packets dropped` ,,,,, `Total TCP packets` `Total TCP packets dropped` `Total UDP packets` `Total UDP packets dropped` `Total Other packets` , `Total Other packets dropped` . |
| **Protokoły** | Podział protokołów, których to dotyczy. Klucze obejmują `TCP` , `UDP` , `Other` . |
| **DropReasons** | Podział przyczyn porzuconych pakietów. Klucze obejmują `Protocol violation invalid TCP syn` , `Protocol violation invalid TCP` , `Protocol violation invalid UDP` , `UDP reflection` , `TCP rate limit exceeded` `UDP rate limit exceeded` `Destination limit exceeded` `Other packet flood` `Rate limit exceeded` ,,, `Packet was forwarded to service` ,,. |
| **TopSourceCountries** | Podział 10 głównych państw źródłowych ruchu przychodzącego. |
| **TopSourceCountriesForDroppedPackets** | Podział 10 głównych krajów w przypadku ruchu ataków, który został skorygowany. |
| **TopSourceASNs** | Podział 10 pierwszych źródłowych numerów systemu (ASN) dla ruchu przychodzącego.  |
| **SourceContinents** | Podział kontynentów źródła ruchu przychodzącego. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>Włącz rejestrowanie diagnostyczne dla wszystkich publicznych adresów IP

Ten [szablon](https://aka.ms/ddosdiaglogs) służy do tworzenia definicji Azure Policy umożliwiającej automatyczne włączenie rejestrowania diagnostycznego dla wszystkich publicznych dzienników adresów IP w zdefiniowanym zakresie.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FPolicy%20-%20DDOS%20Enable%20Diagnostic%20Logging%2FAzure%20Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>Wyświetlanie danych dziennika w skoroszytach

### <a name="azure-sentinel-data-connector"></a>Łącznik danych wskaźnikowych platformy Azure

Możesz łączyć dzienniki z platformą Azure, wyświetlać i analizować dane w skoroszytach, tworzyć niestandardowe alerty i uwzględniać je w procesach dochodzeniowych. Aby nawiązać połączenie z wskaźnikiem kontrolnym platformy Azure, zobacz [nawiązywanie połączenia z platformą Azure](../sentinel/connect-azure-ddos-protection.md). 

![Łącznik usługi Azure — wskaźnik DDoS](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Skoroszyt Azure DDoS Protection

Za pomocą [tego szablonu Azure Resource Manager (ARM)](https://aka.ms/ddosworkbook) można wdrożyć skoroszyt analizy ataków. Ten skoroszyt umożliwia wizualizację danych ataku w kilku panelach z możliwością filtrowania, aby łatwo zrozumieć, co to jest. 

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FWorkbook%20-%20Azure%20DDOS%20monitor%20workbook%2FAzureDDoSWorkbook_ARM.json)

![Skoroszyt DDoS Protection](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>Weryfikuj i Testuj

Aby zasymulować atak DDoS na potrzeby weryfikowania dzienników, zobacz [Sprawdzanie poprawności wykrywania DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

- Konfigurowanie dzienników diagnostycznych DDoS, w tym powiadomień, raportów o ograniczeniach i dzienników przepływów zaradczych. 
- Włącz rejestrowanie diagnostyczne dla wszystkich publicznych adresów IP w zdefiniowanym zakresie.
- Wyświetlanie danych dziennika w skoroszytach.

Aby dowiedzieć się, jak skonfigurować alerty ataków, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Wyświetlanie i konfigurowanie alertów ochrony przed atakami DDoS](alerts.md)
