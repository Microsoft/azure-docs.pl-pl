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
ms.openlocfilehash: 71147352c5b75195ed0dff2b05acc5315f3183cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940058"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Łączenie danych z usługi Defender for IoT z platformą Azure (wersja zapoznawcza)

W publicznej wersji zapoznawczej Azure Security Center łącznika danych IoT na platformie Azure. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

W tym przewodniku dowiesz się, jak połączyć usługę Defender for IoT z danymi na platformie Azure.

> [!div class="checklist"]
> * Wymagania wstępne
> * Ustawienia połączenia
> * Widok alertów Log Analytics

Łączenie alertów z usługi Defender for IoT i przesyłanie strumieniowe ich bezpośrednio do platformy Azure — wskaźnik produkcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć uprawnienia do **odczytu** i **zapisu** obszaru roboczego.
- Usługa **Defender for IoT** musi być **włączona** na odpowiednich IoT Hubach.
- Musisz mieć uprawnienia do **odczytu** i **zapisu** na **platformie Azure IoT Hub** , które chcesz połączyć.
- Musisz mieć również uprawnienia do **odczytu** i **zapisu** w **grupie zasobów usługi Azure IoT Hub**.

> [!NOTE]
> Aby można było wysyłać ogólne alerty zasobów platformy Azure, w ramach subskrypcji musi być uruchomiona Azure Security Center licencjonowania warstwy standardowej. Dzięki licencjonowaniu w warstwie Bezpłatna wymaganej w usłudze Defender for IoT tylko usługi Defender dla alertów związanych z usługą IoT będą przekazywane do Komisji wskaźnikowej platformy Azure.

## <a name="connect-to-defender-for-iot"></a>Nawiązywanie połączenia z usługą Defender for IoT

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Defender for IoT** .
1. W dolnej części okienka po prawej stronie kliknij pozycję **Otwórz stronę łącznika**.
1. Kliknij przycisk **Połącz**obok każdej IoT Hub subskrypcji, której alerty i alerty urządzeń chcesz przesłać do usługi Azure wskaźnikowej.
    - Jeśli usługa Defender for IoT nie jest włączona w tym centrum, zobaczysz komunikat z ostrzeżeniem o włączeniu. Kliknij link **Włącz** , aby uruchomić i włączyć usługę.
1. Możesz zdecydować, czy alerty z usługi Defender for IoT mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń**wybierz pozycję **Włącz** , aby włączyć regułę do automatycznego tworzenia zdarzeń z wygenerowanych alertów.  Tę regułę można zmienić lub edytować w obszarze **Analytics**  >  **aktywne** reguły analizy.

> [!NOTE]
>Odświeżenie listy centrów po wprowadzeniu zmian połączenia może zająć 10 sekund lub dłużej.

## <a name="log-analytics-alert-display"></a>Wyświetlanie alertów Log Analytics

Aby użyć odpowiedniego schematu w Log Analytics, aby wyświetlić alerty usługi Defender for IoT:

1. Otwórz pozycję **Logs**  >  **SecurityInsights**  >  **SecurityAlert**lub wyszukaj ciąg **SecurityAlert**.
1. Filtruj, aby wyświetlić tylko usługi Defender dla alertów generowanych przez usługę IoT przy użyciu następującego filtru KQL:

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Uwagi dotyczące usługi

Po nawiązaniu połączenia IoT Hub dane centrum są dostępne na platformie Azure — około 15 minut później.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Defender for IoT z platformą Azure. Aby dowiedzieć się więcej na temat wykrywania zagrożeń i dostępu do danych zabezpieczeń, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)za pomocą usługi Azure wskaźnikowej.

- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń IoT](how-to-security-data-access.md)
