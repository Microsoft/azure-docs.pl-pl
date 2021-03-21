---
title: Zarządzanie środowiskiem generacji 2 — Seria czasowa platformy Azure | Microsoft Docs
description: Dowiedz się, jak zarządzać środowiskiem Azure Time Series Insights generacji 2.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: cbd28bdf5318bdaf932447f5d1f936d2c614a7f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461899"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Zarządzaj Azure Time Series Insights Gen2

Po utworzeniu środowiska Azure Time Series Insights Gen2 przy użyciu [interfejsu wiersza polecenia platformy Azure](./how-to-create-environment-using-cli.md) lub [Azure Portal](./how-to-create-environment-using-portal.md)można zmodyfikować zasady dostępu i inne atrybuty środowiska, aby odpowiadały potrzebom biznesowym.

## <a name="manage-the-environment"></a>Zarządzanie środowiskiem

Azure Time Series Insights środowisku Gen2 można zarządzać przy użyciu [Azure Portal](https://portal.azure.com/). Istnieje kilka najważniejszych różnic między środowiskiem Gen2 i Gen1 S1 lub Gen1 S2, które należy wziąć pod uwagę podczas zarządzania środowiskiem za pomocą Azure Portal:

* W okienku **przegląd** Azure Portal Gen2 wprowadzono następujące zmiany:

  * Pojemność jest usuwana, ponieważ nie dotyczy środowisk Gen2.
  * Zostanie dodana Właściwość **identyfikatora szeregów czasowych** . Określa sposób partycjonowania danych.
  * Zestawy danych referencyjnych są usuwane.
  * Wyświetlany adres URL kieruje użytkownika do [eksploratora Azure Time Series Insights](./concepts-ux-panels.md).
  * Nazwa konta usługi Azure Storage znajduje się na liście.

* Okienko **konfigurowania** Azure Portal zostanie usunięte, ponieważ jednostki skalowania nie mają zastosowania do Azure Time Series Insights środowisk Gen2. Można jednak użyć **konfiguracji magazynu** w celu skonfigurowania nowo wprowadzonego magazynu w postaci ciepłej.

* Okienko **dane referencyjne** Azure Portal zostało usunięte w Azure Time Series Insights Gen2, ponieważ koncepcja danych referencyjnych została zastąpiona przez [model szeregów czasowych (TSM)](./concepts-model-overview.md).

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Azure Time Series Insights środowisko Gen2 w Azure Portal":::

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z listą najlepszych rozwiązań pozyskiwania [strumieniowego](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)
* Informacje na temat [diagnozowania i rozwiązywania problemów](./how-to-diagnose-troubleshoot.md)
