---
title: Używanie Azure Portal do monitorowania zarządzanej aplikacji
description: Pokazuje, w jaki sposób używać Azure Portal do monitorowania dostępności i alertów dla aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: a02062edd1a940bcc6588ab53457e0af91fedd9a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578266"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Monitorowanie wdrożonego wystąpienia aplikacji zarządzanej

Po wdrożeniu aplikacji zarządzanej w ramach subskrypcji platformy Azure warto sprawdzić stan aplikacji. W tym artykule przedstawiono opcje w Azure Portal sprawdzania stanu. Dostępność zasobów w aplikacji zarządzanej można monitorować. Możesz również skonfigurować alerty i wyświetlać je.

## <a name="view-resource-health"></a>Wyświetlanie kondycji zasobu

1. Wybierz wystąpienie aplikacji zarządzanej.

   ![Wybierz aplikację zarządzaną](./media/monitor-managed-application-portal/select-managed-application.png)

1. Wybierz pozycję **Kondycja zasobów**.

   ![Wybierz kondycję zasobu](./media/monitor-managed-application-portal/select-resource-health.png)

1. Wyświetl dostępność zasobów w aplikacji zarządzanej.

   ![Wyświetlanie kondycji zasobu](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Wyświetlanie alertów

1. Wybierz pozycję **alerty**.

   ![Wybierz alerty](./media/monitor-managed-application-portal/select-alerts.png)

1. Jeśli masz skonfigurowane reguły alertów, zobaczysz informacje o alertach, które zostały zgłoszone.

   ![Wyświetlanie alertów](./media/monitor-managed-application-portal/view-alerts.png)

1. Aby dodać reguły alertów, wybierz pozycję **+ Nowa reguła alertu**.

   ![Tworzenie alertu](./media/monitor-managed-application-portal/create-new-alert.png)

Możesz tworzyć alerty dla wystąpienia aplikacji zarządzanej lub zasobów w zarządzanej aplikacji. Aby uzyskać informacje na temat tworzenia alertów, zobacz [Omówienie alertów w Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z przykładami aplikacji zarządzanych, zobacz [przykładowe projekty dla aplikacji zarządzanych przez platformę Azure](sample-projects.md).
* Aby wdrożyć aplikację zarządzaną, zobacz [wdrażanie aplikacji katalogu usług za pomocą Azure Portal](deploy-service-catalog-quickstart.md).