---
title: Połącz dane standardowe punktu końcowego w chmurze VMware węgla czarnego z platformą Azure Microsoft Docs
description: Dowiedz się, jak połączyć dane standardowe punktu końcowego chmury VMware węgla
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 2c26931db617109c36c19b73bd3ec9263f4b40cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100096450"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>Połącz Standard punktu końcowego chmury VMware węgla czarnego z platformą Azure

> [!IMPORTANT]
> Standardowy Łącznik danych punktów końcowych usługi VMware węgla w chmurze na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Standardowy łącznik programu VMware węgla czarnego punktu końcowego w chmurze pozwala łatwo połączyć wszystkie dzienniki standardowego rozwiązania zabezpieczeń programu [VMware węgla w tle](https://www.carbonblack.com/products/endpoint-standard/) z platformą Azure, aby wyświetlić pulpity nawigacyjne, utworzyć niestandardowe alerty i poprawić badanie. Integracja między programem VMware węgla Black i platformą Azure wskaźnikowym polega na użyciu Azure Functions do ściągania danych dzienników przy użyciu interfejsu API REST.


> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>Konfigurowanie i łączenie programu VMware węgiel Black Cloud Endpoint Standard

Azure Functions mogą integrować i ściągać zdarzenia i dzienniki bezpośrednio z programu VMware węgla czarnego punktu końcowego w chmurze, a następnie przesyłać je do usługi Azure wskaźnikowej.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję **VMware węgiel Black** Connector.
2. Wybierz pozycję **Otwórz stronę łącznika**.
3. Postępuj zgodnie z instrukcjami na **szarej stronie VMware węgla** .


## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w Log Analytics pod **CarbonBlackAuditLogs_CL**, **CarbonBlackNotifications_CL** i * * * * CarbonBlackEvents_CL * * * * *.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności
Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut. 


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia ze standardem punktu końcowego chmury VMware węgla czarnego do platformy Azure wskaźnikiem przy użyciu aplikacji funkcji platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.

