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
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: 822d0c742bbd54b5bab0c69e82652743584a0696
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89659612"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Połącz dane z usługi Azure Defender (dawniej Azure Security Center) dla IoT z platformą Azure — wskaźnikiem 


> [!IMPORTANT]
> Łącznik danych usługi Azure Defender for IoT jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Za pomocą łącznika usługi Azure Defender for IoT można przesyłać strumieniowo wszystkie zdarzenia usługi Azure Defender for IoT do usługi Azure wskaźnikowej. 

## <a name="prerequisites"></a>Wymagania wstępne

- Uprawnienia do **odczytu** i **zapisu** w obszarze roboczym, na którym jest wdrażany wskaźnik na platformie Azure
- **Usługa Azure Defender for IoT** musi być **włączona** na odpowiednich IoT Hub
- Uprawnienia do **odczytu** i **zapisu** na **IoT Hub platformy Azure** , które chcesz połączyć
- Uprawnienia do **odczytu** i **zapisu** w **grupie zasobów usługi Azure IoT Hub**

## <a name="connect-to-azure-defender-for-iot"></a>Nawiązywanie połączenia z usługą Azure Defender dla IoT

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie wybierz pozycję **Azure Defender for IoT** (nadal może być wywoływana Azure Security Center dla IoT) w galerii.
1. W prawym dolnym okienku kliknij pozycję **Otwórz stronę łącznika**. 
1. Kliknij przycisk **Połącz**obok każdej IoT Hub subskrypcji, której alerty i alerty urządzeń chcesz przesłać do usługi Azure wskaźnikowej. 
    - Jeśli usługa Azure Defender for IoT nie jest włączona w tym centrum, zobaczysz komunikat ostrzegawczy **Włącz** . Kliknij link **Włącz** , aby uruchomić usługę. 
1. Możesz zdecydować, czy alerty z usługi Azure Defender for IoT mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń**wybierz pozycję **Włącz** , aby włączyć domyślną regułę analityczną w celu automatycznego tworzenia zdarzeń z alertów generowanych w połączonej usłudze zabezpieczeń. Tę regułę można zmienić lub edytować w obszarze **Analytics**  >  **aktywne** reguły analizy.

> [!NOTE]
> Odświeżanie listy centrów może potrwać trochę czasu po wprowadzeniu zmian w połączeniu. 

## <a name="log-analytics-alert-display"></a>Wyświetlanie alertów Log Analytics

Aby użyć odpowiedniego schematu w Log Analytics, aby wyświetlić alerty usługi Azure Defender for IoT:

1. Otwórz pozycję **Logs**  >  **SecurityInsights**  >  **SecurityAlert**lub wyszukaj ciąg **SecurityAlert**. 
2. Filtruj, aby wyświetlić tylko usługi Azure Defender dla alertów wygenerowanych przez usługę IoT przy użyciu następującego filtru KQL:

```kusto
SecurityAlert | where ProductName == "Azure Defender for IoT"
``` 

### <a name="service-notes"></a>Uwagi dotyczące usługi

Po nawiązaniu połączenia IoT Hub dane centrum są dostępne na platformie Azure — około 15 minut później.


## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Azure Defender dla danych IoT z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
