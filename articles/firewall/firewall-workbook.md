---
title: Monitorowanie dzienników przy użyciu skoroszytu zapory platformy Azure
description: Skoroszyty zapory platformy Azure zapewniają elastyczną kanwę do analizy danych w zaporze platformy Azure oraz tworzenie zaawansowanych raportów wizualnych w ramach Azure Portal.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: e63336b0e84d303b51eda56f90ca6fb453d8ae0d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831845"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Monitorowanie dzienników przy użyciu skoroszytu zapory platformy Azure

Skoroszyt zapory platformy Azure zapewnia elastyczną kanwę do analizy danych w zaporze platformy Azure. Można go użyć do tworzenia rozbudowanych raportów wizualnych w Azure Portal. Możesz wybrać wiele zapór wdrożonych na platformie Azure i połączyć je w ujednolicone interaktywne środowiska.

Możesz uzyskać wgląd w zdarzenia zapory platformy Azure, dowiedzieć się więcej o regułach aplikacji i sieci, a także wyświetlić statystyki dotyczące aktywności zapory w adresach URL, portach i adresach. Skoroszyt zapory platformy Azure umożliwia filtrowanie zapór i grup zasobów oraz dynamiczne filtrowanie według kategorii przy użyciu łatwo odczytywanych zestawów danych podczas badania problemu w dziennikach. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy [włączyć rejestrowanie diagnostyczne](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) za pomocą Azure Portal. Ponadto zapoznaj się z tematem [dzienniki i metryki zapory platformy Azure](logs-and-metrics.md) , aby zapoznać się z omówieniem dzienników diagnostycznych i metryk dostępnych dla zapory platformy Azure.

## <a name="get-started"></a>Rozpoczęcie pracy

Aby wdrożyć skoroszyt, przejdź do [Azure monitor skoroszycie dla zapory platformy Azure](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook) i postępując zgodnie z instrukcjami na stronie. Skoroszyt zapory platformy Azure jest przeznaczony do pracy w wielu dzierżawcach, wielu subskrypcjach i można go filtrować na wiele zapór.

## <a name="overview-page"></a>Strona omówienia

Strona przegląd umożliwia filtrowanie między obszarami roboczymi, czasem i zaporami. Przedstawia zdarzenia według czasu dla zapór i typów dzienników (aplikacji, sieci, zagrożeń Intel, serwera proxy DNS).

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Omówienie skoroszytu zapory platformy Azure":::

## <a name="application-rule-log-statistics"></a>Statystyka dzienników reguł aplikacji

Ta strona zawiera unikatowe źródła adresów IP w czasie, użycie liczby reguł aplikacji, odmowa/dozwoloną nazwę FQDN w czasie i filtrowane dane. Dane można filtrować na podstawie adresu IP.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Dziennik reguł aplikacji w skoroszycie zapory platformy Azure":::

## <a name="network-rule-log-statistics"></a>Statystyka dziennika reguły sieci

Ta strona zawiera akcję Wyświetl według reguły — Zezwalaj/Odmów, port docelowy według adresu IP i DNAT w czasie. Można również filtrować według akcji, portu i typu miejsca docelowego.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Dziennik reguł sieci w skoroszycie zapory platformy Azure":::

Możesz również filtrować dzienniki na podstawie przedziału czasu:

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Okno czasu dziennika reguły sieci w skoroszycie zapory platformy Azure":::

## <a name="investigations"></a>Przegląd

Możesz przeglądać dzienniki i dowiedzieć się więcej o zasobach na podstawie źródłowego adresu IP. Możesz uzyskać informacje takie jak nazwa maszyny wirtualnej i nazwa interfejsu sieciowego. Można go łatwo odfiltrować do zasobu z dzienników.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Badanie skoroszytu zapory platformy Azure":::

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [diagnostyki zapory platformy Azure](firewall-diagnostics.md)
