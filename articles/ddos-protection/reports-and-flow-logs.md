---
title: Azure DDoS Protection Raporty standardowe i dzienniki przepływów
description: Informacje o konfigurowaniu raportów i dzienników przepływów.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 5c3bfbdf133777f0bc219d1306f80bd4d38b56ea
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746271"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>Konfigurowanie raportów ograniczenia skutków ataków DDoS i dzienników przepływów 

Azure DDoS Protection Standard zapewnia szczegółowe informacje o atakach i wizualizacje z DDoSą analizą ataków. Klienci chroniący sieci wirtualne przed atakami DDoS mają szczegółowy wgląd w ruch związany z atakami i działania podejmowane w celu ograniczenia ataku za pośrednictwem raportów ograniczenia ataków, które &ją dzienniki przepływów ograniczenia. Bogate dane telemetryczne są udostępniane za pośrednictwem Azure Monitor, w tym szczegółowych metryk w czasie trwania ataku DDoS. Alerty można skonfigurować dla dowolnych metryk Azure Monitor uwidocznionych przez DDoS Protection. Rejestrowanie może być dodatkowo zintegrowane z [platformą Azure](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), pakietem OMS log Analytics i usługą Azure Storage, aby uzyskać zaawansowaną analizę za pośrednictwem interfejsu diagnostyki Azure monitor.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Wyświetl i skonfiguruj raporty ograniczenia ataków DDoS
> * Wyświetlanie i Konfigurowanie dzienników przepływów łagodzenia ataków DDoS

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Przed wykonaniem kroków opisanych w tym samouczku należy najpierw utworzyć [Plan ochrony programu Azure DDoS Standard](manage-ddos-protection.md).

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>Wyświetl i skonfiguruj raporty ograniczenia ataków DDoS

Raporty ograniczenia ataków używają danych z protokołu Flow, które są agregowane w celu zapewnienia szczegółowych informacji o ataku na zasób. Gdy zasób publicznego adresu IP jest w trakcie ataku, generowanie raportu rozpocznie się zaraz po rozpoczęciu środków zaradczych. Zostanie wygenerowany raport przyrostowy generowany co 5 minut, a raport po pominięciu dotyczy całego okresu zaradczego. Ma to na celu zapewnienie, że w przypadku gdy atak DDoS będzie kontynuowany przez dłuższy czas, będzie można wyświetlić najbardziej aktualną migawkę raportu o ograniczeniach co 5 minut i pełne podsumowanie po przekroczeniu tego ograniczenia. 

1. Wybierz pozycję **wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź wartość *monitor* w polu **Filtr** . Gdy **monitor** pojawi się w wynikach, wybierz go.
3. W obszarze **Ustawienia** wybierz pozycję **Ustawienia diagnostyczne**.
4. Wybierz **subskrypcję** i **grupę zasobów** ZAWIERAJĄCą publiczny adres IP, który chcesz zarejestrować.
5. Wybierz pozycję **publiczny adres IP** dla opcji **Typ zasobu**, a następnie wybierz konkretny publiczny adres IP, dla którego chcesz rejestrować metryki.
6. Wybierz pozycję **Włącz diagnostykę, aby zebrać dziennik DDoSMitigationReports** , a następnie wybierz dowolną z następujących opcji:

    - **Archiwizowanie na koncie magazynu**: dane są zapisywane na koncie usługi Azure Storage. Aby dowiedzieć się więcej na temat tej opcji, zobacz [Archiwizowanie dzienników zasobów](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Przesyłaj strumieniowo do centrum zdarzeń**: umożliwia odbiornikowi dziennika pobieranie dzienników przy użyciu centrum zdarzeń platformy Azure. Centra zdarzeń umożliwiają integrację z systemem Splunk lub innymi systemami SIEM. Aby dowiedzieć się więcej na temat tej opcji, zobacz [przesyłanie strumieniowe dzienników zasobów do centrum zdarzeń](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Wyślij do log Analytics**: zapisuje dzienniki w usłudze Azure monitor. Aby dowiedzieć się więcej na temat tej opcji, zobacz [zbieranie dzienników do użycia w dziennikach Azure monitor](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

Zarówno przyrostowe, & raporty dotyczące ograniczania ataków po ataku są następujące pola
- Wektory ataków
- Statystyka ruchu
- Przyczyna porzuconych pakietów
- Uwzględnione protokoły
- 10 najważniejszych krajów lub regionów źródłowych
- 10 najważniejszych źródeł WPW

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>Wyświetlanie i Konfigurowanie dzienników przepływów łagodzenia ataków DDoS
Za pomocą dzienników przepływów zaradczych ataków można zapoznać się z porzuconym ruchem, przekazywanym ruchem i innymi interesującymi się punktami danych podczas aktywnego ataku DDoS w czasie niemal rzeczywistym. Możesz pozyskać stałe strumienie tych danych do usługi Azure wskaźnikowej lub do systemów SIEM innych firm za pośrednictwem centrum zdarzeń w celu monitorowania niemal w czasie rzeczywistym, podejmować potencjalne działania i rozwiązywać potrzeby operacji obrony.

1. Wybierz pozycję **wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź wartość *monitor* w polu **Filtr** . Gdy **monitor** pojawi się w wynikach, wybierz go.
3. W obszarze **Ustawienia** wybierz pozycję **Ustawienia diagnostyczne**.
4. Wybierz **subskrypcję** i **grupę zasobów** ZAWIERAJĄCą publiczny adres IP, który chcesz zarejestrować.
5. Wybierz pozycję **publiczny adres IP** dla opcji **Typ zasobu**, a następnie wybierz konkretny publiczny adres IP, dla którego chcesz rejestrować metryki.
6. Wybierz pozycję **Włącz diagnostykę, aby zebrać dziennik DDoSMitigationFlowLogs** , a następnie wybierz dowolną z następujących opcji:

    - **Archiwizowanie na koncie magazynu**: dane są zapisywane na koncie usługi Azure Storage. Aby dowiedzieć się więcej na temat tej opcji, zobacz [Archiwizowanie dzienników zasobów](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Przesyłaj strumieniowo do centrum zdarzeń**: umożliwia odbiornikowi dziennika pobieranie dzienników przy użyciu centrum zdarzeń platformy Azure. Centra zdarzeń umożliwiają integrację z systemem Splunk lub innymi systemami SIEM. Aby dowiedzieć się więcej na temat tej opcji, zobacz [przesyłanie strumieniowe dzienników zasobów do centrum zdarzeń](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Wyślij do log Analytics**: zapisuje dzienniki w usłudze Azure monitor. Aby dowiedzieć się więcej na temat tej opcji, zobacz [zbieranie dzienników do użycia w dziennikach Azure monitor](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

Ten [szablon](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Enable%20Diagnostic%20Logging/Azure%20Policy) służy do tworzenia definicji Azure Policy w celu włączenia rejestrowania diagnostycznego.

### <a name="azure-sentinel-data-connector"></a>Łącznik danych wskaźnikowych platformy Azure

Można połączyć dzienniki przepływów zaradczych ataków na platformę Azure, wyświetlać i analizować dane w skoroszytach, tworzyć niestandardowe alerty i dołączać je do procesów dochodzeniowych. Aby nawiązać połączenie z wskaźnikiem kontrolnym platformy Azure, zobacz [nawiązywanie połączenia z platformą Azure](../sentinel/connect-azure-ddos-protection.md). 

![Łącznik usługi Azure — wskaźnik DDoS](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Skoroszyt Azure DDoS Protection

Aby wyświetlić dane dzienników przepływu na pulpicie nawigacyjnym usługi Azure Analytics, można zaimportować przykładowego pulpitu nawigacyjnego z https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

Dzienniki przepływów będą zawierać następujące pola: 
- Źródłowy adres IP
- Docelowy adres IP
- Port źródłowy 
- Port docelowy 
- Typ protokołu 
- Akcja podjęta podczas łagodzenia

![Skoroszyt DDoS Protection](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

Analiza ataków będzie działała tylko wtedy, gdy w sieci wirtualnej publicznego adresu IP jest włączona DDoS Protection Standard. 

## <a name="sample-log-outputs"></a>Przykładowe dane wyjściowe dziennika

Następujące zrzuty ekranu przedstawiają przykładowe dane wyjściowe dziennika:

### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

![DDoS Protection DDoSMitigationFlowLogs](./media/ddos-attack-telemetry/ddos-mitigation-flow-logs.png)

### <a name="ddosprotectionnotifications"></a>DDoSProtectionNotifications

![DDoS Protection DDoSProtectionNotifications](./media/ddos-attack-telemetry/ddos-protection-notifications.png)

### <a name="ddosmitigationreports"></a>DDoSMitigationReports

![DDoS Protection DDoSMitigationReports](./media/ddos-attack-telemetry/ddos-mitigation-reports.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

- Wyświetl i skonfiguruj raporty ograniczenia ataków DDoS
- Wyświetlanie i Konfigurowanie dzienników przepływów łagodzenia ataków DDoS

Aby dowiedzieć się, jak testować i symulować atak DDoS, zobacz Przewodnik dotyczący testowania symulacji:

> [!div class="nextstepaction"]
> [Testowanie przez symulacje](test-through-simulations.md)