---
title: Monitoruj urządzenie Azure Stack Edge | Microsoft Docs
description: Opisuje sposób używania Azure Portal i lokalnego interfejsu użytkownika sieci Web do monitorowania Azure Stack krawędzi.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 2fc7435988a07968e65aaf265c33878c6e72e85b
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569435"
---
# <a name="monitor-your-azure-stack-edge"></a>Monitoruj Azure Stack krawędź

W tym artykule opisano sposób monitorowania Azure Stack krawędzi. Aby monitorować urządzenie, możesz użyć Azure Portal lub lokalnego interfejsu użytkownika sieci Web. Użyj Azure Portal, aby wyświetlić zdarzenia dotyczące urządzeń, skonfigurować alerty i zarządzać nimi oraz wyświetlić metryki. Użyj lokalnego interfejsu użytkownika sieci Web na urządzeniu fizycznym, aby wyświetlić stan sprzętu różnych składników urządzeń.

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

    ![Wyświetl stan sprzętu](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Wyświetlanie metryk

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Zarządzanie alertami

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Następne kroki 

Dowiedz się, jak [zarządzać przepustowością](azure-stack-edge-manage-bandwidth-schedules.md).