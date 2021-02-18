---
title: Łączenie danych aktywności platformy Azure z usługą Azure wskaźnikowego | Microsoft Docs
description: Przesyłaj strumieniowo dzienniki aktywności platformy Azure do usługi Azure wskaźnikowej przy użyciu jednego kliknięcia. Dziennik aktywności rejestruje i wyświetla zdarzenia na poziomie subskrypcji na platformie Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 8f0bac363e020826e34df3cb0b4e3d3582990302
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595451"
---
# <a name="connect-data-from-azure-activity-log"></a>Połącz dane z dziennika aktywności platformy Azure

Dzienniki [aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md) można przesyłać strumieniowo do usługi Azure wskaźnikowej przy użyciu jednego kliknięcia. Dziennik aktywności jest dziennikiem subskrypcji, który rejestruje i wyświetla zdarzenia na poziomie subskrypcji na platformie Azure, od Azure Resource Manager danych operacyjnych do aktualizacji Service Health zdarzeń. Za pomocą dziennika aktywności można określić, kto i kiedy "dla operacji zapisu (PUT, POST, DELETE) wykonanych dla zasobów w subskrypcji. Możesz również poznać stan operacji i inne istotne właściwości. Dziennik aktywności nie obejmuje operacji odczytu (GET) ani operacji dla zasobów, które używają modelu klasycznego/"FRONTONu". 

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć uprawnienia współautora do obszaru roboczego Log Analytics.
- Użytkownik musi mieć uprawnienia czytelnika do każdej subskrypcji, której dzienniki mają być przesyłane strumieniowo do usługi Azure wskaźnikowej.

## <a name="set-up-the-azure-activity-connector"></a>Konfigurowanie łącznika aktywności platformy Azure

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Łączniki danych**. Z listy łączników kliknij pozycję **aktywność Azure**, a następnie na przycisk **Otwórz stronę łącznika** w prawym dolnym rogu.

2. Na karcie **instrukcje** kliknij link **Konfiguruj dzienniki aktywności platformy Azure >** .

3. W okienku **Dziennik aktywności platformy Azure** wybierz subskrypcje, których dzienniki chcesz przesłać do usługi Azure wskaźnikowej. 

4. W okienku subskrypcja, które jest otwierane z prawej strony, kliknij pozycję **Połącz**.

5. Aby użyć odpowiedniego schematu w Log Analytics dla alertów aktywności platformy Azure, wpisz `AzureActivity` w oknie zapytania.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia dziennika aktywności platformy Azure z wskaźnikiem kontrolnym platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij wykrywanie zagrożeń przy użyciu funkcji wskaźnikowej platformy Azure, korzystając z [wbudowanych](tutorial-detect-threats-built-in.md) lub [niestandardowych](tutorial-detect-threats-custom.md) reguł.
