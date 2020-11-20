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
ms.openlocfilehash: 2e6a92a4d08f1603f480a990ad437a90302a8189
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966092"
---
# <a name="data-residency-for-azure-network-watcher"></a>Zamieszkania danych dla Network Watcher platformy Azure
Z wyjątkiem usługi monitor połączeń (wersja zapoznawcza) usługa Azure Network Watcher nie przechowuje danych klienta.


## <a name="connection-monitor-preview-data-residency"></a>Dane z miejsca zamieszkania (wersja zapoznawcza) monitora połączeń
Usługa monitor połączeń (wersja zapoznawcza) przechowuje dane klientów. Te dane są automatycznie przechowywane przez Network Watcher w jednym regionie. Monitor połączeń (wersja zapoznawcza) automatycznie spełnia wymagania dotyczące miejsca zamieszkania danych w regionie, w tym wymagania określone w [Centrum zaufania](https://azuredatacentermap.azurewebsites.net/).

## <a name="data-residency"></a>Rezydencja danych
Na platformie Azure funkcja, która umożliwia przechowywanie danych klienta w jednym regionie, jest obecnie dostępna tylko w regionie Południowo-Wschodnia region (Singapur) Azja i Pacyfik regionu geograficznego i Brazylia Południowa (stan Świętego Paulo) geograficznej. W przypadku wszystkich innych regionów dane klienta są przechowywane w lokalizacji geograficznej. Aby uzyskać więcej informacji, zobacz [Centrum zaufania](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z omówieniem [Network Watcher](./network-watcher-monitoring-overview.md).