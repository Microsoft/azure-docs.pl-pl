---
title: Konfigurowanie kontrolki wskaźnikowej (wersja zapoznawcza)
description: W tym artykule wyjaśniono, jak skonfigurować usługę Azure Wskaźnikowanie do odbierania danych z Azure Security Center na potrzeby rozwiązania IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: fe8e4c1b08f96e5f6b2fc7649f7a4361616b7c87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311316"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Łączenie danych z Azure Security Center dla IoT z platformą Azure — wskaźnikiem (wersja zapoznawcza)

> [!IMPORTANT]
> W publicznej wersji zapoznawczej Azure Security Center łącznika danych IoT na platformie Azure.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku dowiesz się, jak połączyć Azure Security Center danych IoT z platformą Azure.

> [!div class="checklist"]
> * Wymagania wstępne
> * Ustawienia połączenia
> * Widok alertów Log Analytics

Łączenie alertów z Azure Security Center dla usługi IoT i przesyłanie strumieniowe ich bezpośrednio do platformy Azure — wskaźnik produkcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć uprawnienia do **odczytu** i **zapisu** obszaru roboczego.
- **Azure Security Center usługi IoT** muszą być **włączone** na odpowiednich IoT Hubach.
- Musisz mieć uprawnienia do **odczytu** i **zapisu** na **platformie Azure IoT Hub** , które chcesz połączyć.
- Musisz mieć również uprawnienia do **odczytu** i **zapisu** w **grupie zasobów usługi Azure IoT Hub**.

> [!NOTE]
> Aby można było wysyłać ogólne alerty zasobów platformy Azure, w ramach subskrypcji musi być uruchomiona Azure Security Center licencjonowania warstwy standardowej. Dzięki licencjonowaniu w warstwie Bezpłatna wymaganej do Azure Security Center usługi IoT tylko Azure Security Center alertów związanych z IoT są przekazywane do platformy Azure.

## <a name="connect-to-azure-security-center-for-iot"></a>Nawiązywanie połączenia z usługą Azure Security Center dla IoT

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Azure Security Center for IoT** .
1. W dolnej części okienka po prawej stronie kliknij pozycję **Otwórz stronę łącznika**.
1. Kliknij przycisk **Połącz**obok każdej IoT Hub subskrypcji, której alerty i alerty urządzeń chcesz przesłać do usługi Azure wskaźnikowej.
    - Jeśli Azure Security Center dla IoT nie jest włączona w tym centrum, zobaczysz komunikat ostrzegawczy Włącz. Kliknij link **Włącz** , aby uruchomić i włączyć usługę.
1. Możesz zdecydować, czy alerty od Azure Security Center dla IoT mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń**wybierz pozycję **Włącz** , aby włączyć regułę do automatycznego tworzenia zdarzeń z wygenerowanych alertów.  Tę regułę można zmienić lub edytować w obszarze **Analytics** > **aktywne** reguły analizy.

> [!NOTE]
>Odświeżenie listy centrów po wprowadzeniu zmian połączenia może zająć 10 sekund lub dłużej.

## <a name="log-analytics-alert-display"></a>Wyświetlanie alertów Log Analytics

Aby użyć odpowiedniego schematu w Log Analytics, aby wyświetlić Azure Security Center alertów IoT:

1. Otwórz pozycję **Logs** > **SecurityInsights** > **SecurityAlert**lub wyszukaj ciąg **SecurityAlert**.
1. Filtruj, aby wyświetlić tylko Azure Security Center dla alertów generowanych przez IoT przy użyciu następującego filtru KQL:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Uwagi dotyczące usługi

Po nawiązaniu połączenia IoT Hub dane centrum są dostępne na platformie Azure — około 15 minut później.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z Azure Security Centerami IoT z platformą Azure. Aby dowiedzieć się więcej na temat wykrywania zagrożeń i dostępu do danych zabezpieczeń, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)za pomocą usługi Azure wskaźnikowej.

- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń IoT](how-to-security-data-access.md)
