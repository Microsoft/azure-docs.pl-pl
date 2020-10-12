---
title: Analizowanie wzorców użycia usługi Azure CDN
description: W tym artykule opisano różne typy raportów analizy dostępnych dla Azure CDN produktów.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/22/2020
ms.author: allensu
ms.openlocfilehash: 169889dbb87d00fdde44ff72c0d2004c331604ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073046"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analizowanie wzorców użycia usługi Azure CDN

Po włączeniu usługi CDN dla aplikacji możesz monitorować użycie usługi CDN, sprawdzić kondycję dostawy i rozwiązywać potencjalne problemy. Azure CDN udostępnia te funkcje w następujący sposób: 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>Nieprzetworzone dzienniki dla Azure CDN firmy Microsoft
Przy użyciu standardowego profilu Microsoft można włączyć dzienniki pierwotne i wybrać opcję przesyłania strumieniowego dzienników do:

* Azure Storage
* Usługa Event Hubs
* Azure Log Analytics

Za pomocą platformy Azure Log Analytics można wyświetlić metryki monitorowania i skonfigurować alerty. 

Aby uzyskać więcej informacji, zobacz [Azure CDN dzienników nieprzetworzonych http](enable-raw-logs.md).


## <a name="core-analytics-via-azure-diagnostic-logs"></a>Analiza podstawowa za pośrednictwem dzienników diagnostycznych platformy Azure

Analiza podstawowa jest dostępna dla punktów końcowych usługi CDN dla wszystkich warstw cenowych. Dzienniki diagnostyczne platformy Azure umożliwiają eksportowanie podstawowych analiz do usługi Azure Storage, centrów zdarzeń lub dzienników Azure Monitor. Dzienniki Azure Monitor oferują rozwiązanie z wykresami, które są konfigurowalne i dostosowywane przez użytkownika. Aby uzyskać więcej informacji na temat dzienników diagnostycznych platformy Azure, zobacz [dzienniki diagnostyczne platformy Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon podstawowe raporty

**Azure CDN Standard from Verizon** lub **Azure CDN Premium z profilów Verizon** zapewniają podstawowe raporty. Raporty podstawowe można wyświetlać w dodatkowym portalu Verizon. Raporty podstawowe Verizon są dostępne za pośrednictwem opcji **zarządzania** w Azure Portal i oferują różne rodzaje wykresów i widoków. Aby uzyskać więcej informacji, zobacz [podstawowe raporty z Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Raporty niestandardowe Verizon

**Azure CDN Standard from Verizon** lub **Azure CDN Premium z profilów Verizon** udostępnia raporty niestandardowe. Raporty niestandardowe można wyświetlać w dodatkowym portalu Verizon. Raporty niestandardowe Verizon są dostępne za pośrednictwem opcji **zarządzania** w Azure Portal. 

Raporty niestandardowe przedstawiają liczbę trafień lub danych transferowanych dla każdej krawędzi CNAME. Dane są pogrupowane według kodu odpowiedzi HTTP lub stanu pamięci podręcznej w danym okresie czasu. Aby uzyskać więcej informacji, zobacz [raporty niestandardowe z usługi Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium z raportów Verizon

Za pomocą **Azure CDN Premium Verizon**można także uzyskać dostęp do następujących raportów:
   * [Zaawansowane raporty HTTP](cdn-advanced-http-reports.md)
   * [Statystyki w czasie rzeczywistym](cdn-real-time-stats.md)
   * [Wydajność Azure CDN węzła brzegowego](cdn-edge-performance.md)

## <a name="next-steps"></a>Następne kroki
Ten artykuł zawiera informacje o różnych opcjach raportów analizy dla Azure CDN.

Aby uzyskać więcej informacji na temat Azure CDN i innych usług platformy Azure wymienionych w tym artykule, zobacz:

* [Co to jest Azure CDN?](cdn-overview.md)
* [Azure CDN dziennikami nieprzetworzonymi HTTP](enable-raw-logs.md)


