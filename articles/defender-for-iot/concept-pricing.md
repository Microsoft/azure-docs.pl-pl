---
title: Cennik i powiązane koszty
description: Dowiedz się więcej o kosztach związanych z usługą Defender for IoT i sposobach ich kontrolowania.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: 24ae6c4014948639aa737a0d2d88ec15f98a7cb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939526"
---
# <a name="pricing-and-associated-costs"></a>Cennik i powiązane koszty

W tym artykule opisano model cen usługi Defender for IoT, podsumowano wszystkie powiązane koszty i wyjaśniono, jak zarządzać nimi.

## <a name="pricing"></a>Cennik

Model cen usługi Defender for IoT składa się z dwóch części i jest rozliczany po [włączeniu](quickstart-onboard-iot-hub.md) IoT Hub w usłudze Defender dla IoT:

- Koszt według wbudowanych funkcji zabezpieczeń urządzeń opartych na analizie IoT Hub dzienników.

- Koszt dzięki ulepszonym funkcjom zabezpieczeń opartym na komunikatach zabezpieczeń z urządzeń IoT Edge lub liści.

Aby uzyskać więcej informacji, zobacz [Cennik usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Powiązane koszty

Usługa Defender for IoT ma powiązane koszty, które nie są częścią cen bezpośrednich:

- Log Analytics koszty magazynu

Możesz zmniejszyć koszty związane z niektórymi funkcjami rozwiązania. Zrezygnuj z zmiany ustawień.

Aby zmienić ustawienia:

1. Otwórz IoT Hub.

1. W obszarze **zabezpieczenia**kliknij pozycję **Ustawienia**.

1. Kliknij pozycję **zbieranie danych**.

Poniższa tabela zawiera podsumowanie powiązanych kosztów i implikacje poszczególnych opcji.

| Opcja | Użycie | Komentarz |
| --- | --- | --- |
| **Magazyn Log Analytics** |  |
| Zalecenia i alerty dotyczące urządzeń| Zalecenia dotyczące zabezpieczeń i alerty wygenerowane przez usługę | Nieopcjonalne |
| Surowe dane zabezpieczeń| Surowe dane zabezpieczeń z urządzeń IoT zebranych przez agentów zabezpieczeń | Wyłącz _zdarzenia związane z zabezpieczeniami magazynu RAW_ |
|

>[!Important]
> Rezygnacja z usługi Defender na potrzeby dostępności funkcji zabezpieczeń IoT.

| Zrezygnuj | Implikacje |
| --- | --- |
| _Zbieranie metadanych z przędzy_ | Wyłącz [alerty niestandardowe](quickstart-create-custom-alerts.md) |
| | Wyłącz zalecenia dotyczące IoT Edge manifest |
| | Wyłączanie zaleceń i alertów opartych na tożsamości urządzenia |
| _Przechowuj zdarzenia dotyczące zabezpieczeń nieprzetworzonych urządzeń_ | Szczegółowe informacje o zaleceniach bazowych systemu operacyjnego urządzeń nie są dostępne |
| | Szczegóły dotyczące postępowania z [alertami](concept-security-alerts.md) i [zaleceniami](concept-recommendations.md) nie są dostępne |
|

## <a name="see-also"></a>Zobacz też

- Uzyskiwanie dostępu do [danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
- [Badanie urządzenia](how-to-investigate-device.md)
- Omówienie i eksplorowanie [zaleceń dotyczących zabezpieczeń](concept-recommendations.md)
- Poznawanie i eksplorowanie [alertów zabezpieczeń](concept-security-alerts.md)
