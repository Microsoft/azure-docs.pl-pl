---
title: Zarządzanie alertami z narzędzi System Center Operations Manager, Zabbix i Nagios w usłudze Azure Monitor
description: Zarządzanie alertami z narzędzi System Center Operations Manager, Zabbix i Nagios w usłudze Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 367a7020e56db7105da7624c7911ae5b59f1365a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105893"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Zarządzanie alertami z narzędzi System Center Operations Manager, Zabbix i Nagios w usłudze Azure Monitor

Możesz teraz wyświetlać alerty z Nagios, Zabbix i System Center Operations Manager w [Azure monitor](./alerts-overview.md). Te alerty pochodzą z integracji z serwerami Nagios/Zabbix lub System Center Operations Manager w Log Analytics. 

## <a name="prerequisites"></a>Wymagania wstępne
Wszystkie rekordy w repozytorium Log Analytics z typem alertu zostaną zaimportowane do Azure Monitor, więc należy wykonać konfigurację wymaganą do zebrania tych rekordów.
1. W przypadku alertów **Nagios** i **Zabbix** [Skonfiguruj te serwery](../learn/quick-collect-linux-computer.md) do [wysyłania alertów](./data-sources-custom-logs.md?toc=/azure/azure-monitor/toc.json) do log Analytics.
1. W przypadku alertów **System Center Operations Manager** [połącz grupę zarządzania Operations Manager z obszarem roboczym log Analytics](./om-agents.md). Poniżej przedstawiono wdrożenie rozwiązania [alert Management](./alert-management-solution.md) w portalu Azure Solutions Marketplace. Po wykonaniu tych czynności wszystkie alerty utworzone w System Center Operations Manager są importowane do Log Analytics.

## <a name="view-your-alert-instances"></a>Wyświetlanie wystąpień alertów
Po skonfigurowaniu importu do Log Analytics można rozpocząć wyświetlanie wystąpień alertów z tych usług monitorowania w programie [Azure monitor](./alerts-overview.md). Gdy są one obecne w Azure Monitor, można [zarządzać wystąpieniami alertów](./alerts-managing-alert-instances.md?toc=%252fazure%252fazure-monitor%252ftoc.json), [zarządzać grupami inteligentnymi utworzonymi w tych alertach](./alerts-managing-smart-groups.md?toc=%252fazure%252fazure-monitor%252ftoc.json) oraz [zmieniać stan alertów i grup inteligentnych](./alerts-managing-alert-states.md?toc=%252fazure%252fazure-monitor%252ftoc.json).

> [!NOTE]
>  1. To rozwiązanie pozwala tylko wyświetlać wystąpienia alertów System Center Operations Manager/Zabbix/Nagios w programie Azure Monitor. Konfigurację reguły alertu można wyświetlać/edytować tylko w odpowiednich narzędziach do monitorowania. 
>  1. Wszystkie uruchomione wystąpienia alertów będą dostępne zarówno w Azure Monitor, jak i na platformie Azure Log Analytics. Obecnie nie ma możliwości wyboru między dwoma lub pozyskiwania konkretnie wybranych alertów.
>  1. Wszystkie alerty z System Center Operations Manager, Zabbix i Nagios mają typ sygnału "nieznany", ponieważ podstawowy typ telemetrii nie jest dostępny.
>  1. Alerty Nagios nie są stanowe — na przykład [warunek monitora](./alerts-overview.md) alertu nie zostanie przeszedł z "wyzwolone" do "rozwiązany". Zamiast tego zarówno "wyzwolone", jak i "rozwiązane" są wyświetlane jako osobne wystąpienia alertów.