---
title: Analizowanie wzorców użycia Azure CDN | Microsoft Docs
description: W tym artykule opisano różne typy raportów analizy dostępnych dla Azure CDN produktów.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: allensu
ms.openlocfilehash: c78dff9f5258023d90100c70bd244e8e0d016d6f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253615"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analizowanie wzorców użycia usługi Azure CDN

Po włączeniu usługi CDN dla aplikacji możesz monitorować użycie usługi CDN, sprawdzić kondycję dostawy i rozwiązywać potencjalne problemy. Azure CDN udostępnia te funkcje w następujący sposób: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Analiza podstawowa za pośrednictwem dzienników diagnostycznych platformy Azure

Analiza podstawowa jest dostępna dla punktów końcowych usługi CDN dla wszystkich warstw cenowych. Dzienniki diagnostyczne platformy Azure umożliwiają eksportowanie podstawowych analiz do usługi Azure Storage, centrów zdarzeń lub dzienników Azure Monitor. Dzienniki Azure Monitor oferują rozwiązanie z wykresami, które są konfigurowalne i dostosowywane przez użytkownika. Aby uzyskać więcej informacji na temat dzienników diagnostycznych platformy Azure, zobacz [dzienniki diagnostyczne platformy Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon podstawowe raporty

Jako użytkownik Azure CDN ze **standardem Azure CDN z Verizon** lub **Azure CDN Premium z profilu Verizon** , można wyświetlać raporty Verizon Core w portalu uzupełniającym Verizon. Raporty podstawowe Verizon są dostępne za pośrednictwem opcji **zarządzania** w Azure Portal i oferują różnorodne wykresy i widoki. Aby uzyskać więcej informacji, zobacz [podstawowe raporty z Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Raporty niestandardowe Verizon

Jako użytkownik Azure CDN ze **standardem Azure CDN z Verizon** lub **Azure CDN Premium z profilu Verizon** , można wyświetlić raporty niestandardowe Verizon w portalu uzupełniającym Verizon. Raporty niestandardowe Verizon są dostępne za pośrednictwem opcji **zarządzania** w Azure Portal. Na stronie niestandardowe raporty Verizon wyświetlana jest liczba trafień lub danych transferowanych dla każdej krawędzi rekordu CName należącej do profilu Azure CDN. Dane mogą być pogrupowane według kodu odpowiedzi HTTP lub stanu pamięci podręcznej w dowolnym okresie czasu. Aby uzyskać więcej informacji, zobacz [raporty niestandardowe z usługi Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium z raportów Verizon

Za pomocą **Azure CDN Premium Verizon**można także uzyskać dostęp do następujących raportów:
   * [Zaawansowane raporty HTTP](cdn-advanced-http-reports.md)
   * [Statystyki w czasie rzeczywistym](cdn-real-time-stats.md)
   * [Wydajność węzła brzegowego](cdn-edge-performance.md)

