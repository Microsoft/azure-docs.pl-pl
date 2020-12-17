---
title: Łączenie usługi Azure Defender for IoT z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć usługę Azure Defender (dawniej Azure Security Center) na potrzeby danych IoT z platformą Azure.
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
ms.date: 12/16/2020
ms.author: yelevin
ms.openlocfilehash: 95e78c7557092a4d1203a8df3a107fe7b63eac9b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631445"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Połącz dane z usługi Azure Defender (dawniej Azure Security Center) dla IoT z platformą Azure — wskaźnikiem 


> [!IMPORTANT]
> Łącznik danych usługi Defender for IoT jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Użyj łącznika Defender for IoT, aby przesłać strumieniowo wszystkie zdarzenia usługi Defender dotyczące usług IoT do platformy Azure. 

Ta integracja pozwala organizacjom szybko wykrywać ataki potokach wieloetapowych, które często przekraczają te i niegranicę. Ponadto usługa Defender dla integracji IoT z funkcjami aranżacji, automatyzacji i reagowania (o) na platformie Azure — umożliwia automatyczne reagowanie i zapobieganie przy użyciu wbudowanych niezoptymalizowanych elementy PlayBook. 
## <a name="prerequisites"></a>Wymagania wstępne

- Uprawnienia do **odczytu** i **zapisu** w obszarze roboczym, na którym jest wdrażany wskaźnik na platformie Azure
- Usługa **Defender for IoT** musi być **włączona** na odpowiednich IoT Hub
- Musisz mieć uprawnienia **współautora** w **ramach subskrypcji** , którą chcesz połączyć.

## <a name="connect-to-defender-for-iot"></a>Nawiązywanie połączenia z usługą Defender for IoT

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie wybierz pozycję **Defender for IoT** (nadal może być wywoływana Azure Security Center dla IoT) z galerii.

1. W dolnej części okienka po prawej stronie kliknij pozycję **Otwórz stronę łącznika**. 

1. Kliknij przycisk **Połącz** obok każdej IoT Hub subskrypcji, której alerty i alerty urządzeń chcesz przesłać do usługi Azure wskaźnikowej. 
    - Jeśli usługa Defender for IoT nie zostanie włączona na co najmniej jednym IoT Hub w ramach subskrypcji, zostanie wyświetlony komunikat o błędzie. Włącz usługę Defender dla IoT w IoT Hub, aby usunąć błąd.

1. Możesz zdecydować, czy alerty z usługi Defender for IoT mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń** wybierz pozycję **Włącz** , aby włączyć domyślną regułę analizy, aby automatycznie tworzyć zdarzenia z wygenerowanych alertów. Tę regułę można zmienić lub edytować w obszarze   >  **aktywne reguły** analizy.

> [!NOTE]
> Odświeżanie listy **subskrypcji** po wprowadzeniu zmian połączenia może potrwać 10 sekund lub dłużej. 

## <a name="log-analytics-alert-view"></a>Widok alertów Log Analytics

Aby użyć odpowiedniego schematu w Log Analytics, aby wyświetlić alerty usługi Defender for IoT:

1. Otwórz pozycję **Logs**  >  **SecurityInsights**  >  **SecurityAlert** lub wyszukaj ciąg **SecurityAlert**. 

2. Filtruj, aby wyświetlić tylko usługi Defender dla alertów generowanych przez usługę IoT przy użyciu następującego filtru KQL:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Uwagi dotyczące usługi

Po nawiązaniu połączenia z **subskrypcją** dane centrum są dostępne na platformie Azure — około 15 minut później.


## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Defender for IoT z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
