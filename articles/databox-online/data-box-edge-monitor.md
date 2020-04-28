---
title: Monitoruj urządzenie Azure Data Box Edge | Microsoft Docs
description: Opisuje sposób używania Azure Portal i lokalnego interfejsu użytkownika sieci Web do monitorowania Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "60756756"
---
# <a name="monitor-your-azure-data-box-edge"></a>Monitoruj Azure Data Box Edge

W tym artykule opisano sposób monitorowania Azure Data Box Edge. Aby monitorować urządzenie, możesz użyć Azure Portal lub lokalnego interfejsu użytkownika sieci Web. Użyj Azure Portal, aby wyświetlić zdarzenia dotyczące urządzeń, skonfigurować alerty i zarządzać nimi oraz wyświetlić metryki. Użyj lokalnego interfejsu użytkownika sieci Web na urządzeniu fizycznym, aby wyświetlić stan sprzętu różnych składników urządzeń.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Wyświetlanie zdarzeń urządzeń i odpowiadających im alertów
> * Wyświetlanie stanu sprzętu dla składników urządzenia
> * Wyświetlanie metryk pojemności i transakcji dla urządzenia
> * Konfigurowanie alertów i zarządzanie nimi

## <a name="view-device-events"></a>Wyświetl zdarzenia urządzenia

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Wyświetl stan sprzętu

Wykonaj następujące kroki w lokalnym interfejsie użytkownika sieci Web, aby wyświetlić stan sprzętu składników urządzenia.

1. Nawiąż połączenie z lokalnym interfejsem użytkownika sieci Web urządzenia.
2. Przejdź do pozycji **konserwacja > stan sprzętu**. Można wyświetlić kondycję różnych składników urządzeń.

    ![Wyświetl stan sprzętu](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Wyświetlanie metryk

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Zarządzanie alertami

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Następne kroki 

Dowiedz się, jak [zarządzać przepustowością](data-box-edge-manage-bandwidth-schedules.md).