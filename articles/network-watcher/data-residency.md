---
title: Zamieszkania danych na platformie Azure Network Watcher | Microsoft Docs
description: Ten artykuł pomoże Ci zrozumieć dane zamieszkania usługi Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: a6be3a7fd19b43bd1b18af05d0dbfaf5053fb181
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682965"
---
# <a name="data-residency-for-azure-network-watcher"></a>Zamieszkania danych dla Network Watcher platformy Azure
Z wyjątkiem usługi monitor połączeń (wersja zapoznawcza) usługa Azure Network Watcher nie przechowuje danych klienta.


## <a name="connection-monitor-preview-data-residency"></a>Dane z miejsca zamieszkania (wersja zapoznawcza) monitora połączeń
Usługa monitor połączeń (wersja zapoznawcza) przechowuje dane klientów. Te dane są automatycznie przechowywane przez Network Watcher w jednym regionie. Monitor połączeń (wersja zapoznawcza) automatycznie spełnia wymagania dotyczące miejsca zamieszkania danych w regionie, w tym wymagania określone w [Centrum zaufania](https://azuredatacentermap.azurewebsites.net/).

## <a name="data-residency"></a>Rezydencja danych
Na platformie Azure funkcja, która umożliwia przechowywanie danych klienta w jednym regionie, jest obecnie dostępna tylko w regionie Południowo-Wschodnia region (Singapur) Azja i Pacyfik regionu geograficznego i Brazylia Południowa (stan Świętego Paulo) geograficznej. W przypadku wszystkich innych regionów dane klienta są przechowywane w lokalizacji geograficznej. Aby uzyskać więcej informacji, zobacz [Centrum zaufania](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z omówieniem [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
