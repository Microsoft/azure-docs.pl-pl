---
title: Konfigurowanie platformy Azure dla usługi Defender for IoT (wersja zapoznawcza)
description: W tym artykule wyjaśniono, jak skonfigurować platformę Azure do odbierania danych z rozwiązania Defender for IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: c5c0f74ed8a5688b20eea4e74f747d3ff6dd0e63
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340003"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Łączenie danych z usługi Defender for IoT z platformą Azure (wersja zapoznawcza)

Łącznik danych usługi Azure Defender for IoT na platformie Azure jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

W tym przewodniku dowiesz się, jak połączyć usługę Defender for IoT z danymi na platformie Azure.

> [!div class="checklist"]
> * Wymagania wstępne
> * Ustawienia połączenia
> * Widok alertów Log Analytics

Łączenie alertów z usługi Defender for IoT i przesyłanie strumieniowe ich bezpośrednio do platformy Azure — wskaźnik produkcji.

Dzięki ściślejszej integracji usługi Azure Defender for IoT z platformą Azure, pierwszą SIEM natywną w chmurze i pierwszą SIEMą z natywnymi usługami IoT i z zabezpieczeniami, firma Microsoft zapewnia prostsze podejście do ujednoliconych zabezpieczeń w sieciach IT i branżowych. W połączeniu z uczeniem maszynowym platformy Azure ta Integracja pozwala organizacjom szybko wykrywać ataki potokach wieloetapowych, które często przekraczają te i niegranicę. Ponadto usługa Azure Defender dla integracji IoT z funkcjami aranżacji, automatyzacji i reagowania (o) na platformie Azure — umożliwia automatyczne reagowanie i zapobieganie przy użyciu wbudowanych niezoptymalizowanych elementy PlayBook. 

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć uprawnienia do **odczytu** i **zapisu** obszaru roboczego.
- Usługa **Defender for IoT** musi być **włączona** na odpowiednich IoT Hubach.
- Musisz mieć uprawnienia do **odczytu** i **zapisu** na **platformie Azure IoT Hub** , które chcesz połączyć.
- Musisz mieć również uprawnienia do **odczytu** i **zapisu** w **grupie zasobów usługi Azure IoT Hub**.


## <a name="connect-to-defender-for-iot"></a>Nawiązywanie połączenia z usługą Defender for IoT

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Defender for IoT** .
1. W dolnej części okienka po prawej stronie kliknij pozycję **Otwórz stronę łącznika**.
1. Kliknij przycisk **Połącz** obok każdej IoT Hub subskrypcji, której alerty i alerty urządzeń chcesz przesłać do usługi Azure wskaźnikowej.
    - Jeśli usługa Defender for IoT nie jest włączona w tym centrum, zobaczysz komunikat z ostrzeżeniem o włączeniu. Kliknij link **Włącz** , aby uruchomić i włączyć usługę.
1. Możesz zdecydować, czy alerty z usługi Defender for IoT mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń** wybierz pozycję **Włącz** , aby włączyć regułę do automatycznego tworzenia zdarzeń z wygenerowanych alertów.  Tę regułę można zmienić lub edytować w obszarze **Analytics**  >  **aktywne** reguły analizy.

> [!NOTE]
>Odświeżenie listy centrów po wprowadzeniu zmian połączenia może zająć 10 sekund lub dłużej.

## <a name="using-log-analytics-for-alert-display"></a>Używanie Log Analytics do wyświetlania alertów

Aby użyć odpowiedniego schematu w Log Analytics, aby wyświetlić alerty usługi Defender for IoT:

1. Otwórz pozycję **Logs**  >  **SecurityInsights**  >  **SecurityAlert** lub wyszukaj ciąg **SecurityAlert**.
1. Filtruj, aby wyświetlić tylko usługi Defender dla alertów generowanych przez usługę IoT przy użyciu następującego filtru KQL:

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Uwagi dotyczące usługi

Po nawiązaniu połączenia IoT Hub dane centrum są dostępne na platformie Azure — około 15 minut później.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Defender for IoT z platformą Azure. Aby dowiedzieć się więcej na temat wykrywania zagrożeń i dostępu do danych zabezpieczeń, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](../sentinel/quickstart-get-visibility.md)za pomocą usługi Azure wskaźnikowej.

- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń IoT](how-to-security-data-access.md)