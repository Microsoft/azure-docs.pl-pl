---
title: Konfigurowanie platformy Azure dla usługi Defender for IoT (wersja zapoznawcza)
description: W tym artykule wyjaśniono, jak skonfigurować platformę Azure do odbierania danych z rozwiązania Defender for IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/16/2020
ms.author: shhazam
ms.openlocfilehash: c033f6fc6f3fc40005d98bd48832c15792d6f484
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629089"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Łączenie danych z usługi Defender for IoT z platformą Azure (wersja zapoznawcza)

> [!IMPORTANT]
> Łącznik danych usługi Defender for IoT jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Użyj łącznika Defender for IoT, aby przesłać strumieniowo wszystkie zdarzenia usługi Defender dotyczące usług IoT do platformy Azure. 

Ta integracja pozwala organizacjom szybko wykrywać ataki potokach wieloetapowych, które często przekraczają te i niegranicę. Ponadto usługa Defender dla integracji IoT z funkcjami aranżacji, automatyzacji i reagowania (o) na platformie Azure — umożliwia automatyczne reagowanie i zapobieganie przy użyciu wbudowanych niezoptymalizowanych elementy PlayBook. 

## <a name="prerequisites"></a>Wymagania wstępne

- Uprawnienia do **odczytu** i **zapisu** w obszarze roboczym, na którym jest wdrażany wskaźnik na platformie Azure
- Usługa **Defender for IoT** musi być **włączona** na odpowiednich IoT Hub
- Musisz mieć uprawnienia **współautora** w **ramach subskrypcji** , którą chcesz połączyć.

## <a name="connect-to-defender-for-iot"></a>Nawiązywanie połączenia z usługą Defender for IoT

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie wybierz pozycję **Defender for IoT** (nadal może być wywoływana Azure Security Center dla IoT) w galerii.

1. W dolnej części okienka po prawej stronie kliknij pozycję **Otwórz stronę łącznika**.

1. Kliknij przycisk **Połącz** obok każdej IoT Hub subskrypcji, której alerty i alerty urządzeń chcesz przesłać do usługi Azure wskaźnikowej.
    - Jeśli usługa Defender for IoT nie zostanie włączona na co najmniej jednym IoT Hub w ramach subskrypcji, zostanie wyświetlony komunikat o błędzie. Włącz usługę Defender dla IoT w IoT Hub, aby usunąć błąd.

1. Możesz zdecydować, czy alerty z usługi Defender for IoT mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń** wybierz pozycję **Włącz** , aby włączyć domyślną regułę analizy, aby automatycznie tworzyć zdarzenia z wygenerowanych alertów. Tę regułę można zmienić lub edytować w obszarze   >  **aktywne reguły** analizy.

> [!NOTE]
> Odświeżanie listy **subskrypcji** po wprowadzeniu zmian połączenia może potrwać 10 sekund lub dłużej. 

## <a name="log-analytics-alert-view"></a>Widok alertów Log Analytics

Aby użyć odpowiedniego schematu w Log Analytics, aby wyświetlić alerty usługi Defender for IoT:

1. Otwórz pozycję **Logs**  >  **SecurityInsights**  >  **SecurityAlert** lub wyszukaj ciąg **SecurityAlert**.

1. Filtruj, aby wyświetlić tylko usługi Defender dla alertów generowanych przez usługę IoT przy użyciu następującego filtru KQL:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Uwagi dotyczące usługi

Po nawiązaniu połączenia z **subskrypcją** dane centrum są dostępne na platformie Azure — około 15 minut później.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Defender for IoT z platformą Azure. Aby dowiedzieć się więcej na temat wykrywania zagrożeń i dostępu do danych zabezpieczeń, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)za pomocą usługi Azure wskaźnikowej.
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń IoT](how-to-security-data-access.md)
