---
title: Cennik i powiązane koszty
description: Dowiedz się więcej o kosztach związanych z Azure Security Centerami dla IoT i sposobach ich kontrolowania.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: 98afd1eb1f8a73c2d6dd3f293a07cdb70780d238
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004770"
---
# <a name="pricing-and-associated-costs"></a>Cennik i powiązane koszty

W tym artykule wyjaśniono Azure Security Center modelu cen usługi IoT, podsumowano wszystkie powiązane koszty i wyjaśniono, jak zarządzać nimi.

## <a name="pricing"></a>Cennik

Model cen usługi IoT Azure Security Center obejmuje dwie części, a opłaty są naliczane po [włączeniu](quickstart-onboard-iot-hub.md) IoT Hub w Azure Security Center dla IoT:

- Koszt według wbudowanych funkcji zabezpieczeń urządzeń opartych na analizie IoT Hub dzienników.

- Koszt dzięki ulepszonym funkcjom zabezpieczeń opartym na komunikatach zabezpieczeń z urządzeń IoT Edge lub liści.

Aby uzyskać więcej informacji, zobacz [Cennik usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Powiązane koszty

W Azure Security Center dla IoT są związane koszty, które nie są częścią cen bezpośrednich:

- Log Analytics koszty magazynu

Możesz zmniejszyć koszty związane z niektórymi funkcjami rozwiązania. Zrezygnuj z zmiany ustawień.

Aby zmienić ustawienia:

1. Otwórz IoT Hub.

1. W obszarze **zabezpieczenia**kliknij pozycję **Przegląd**.

1. Kliknij polecenie **Ustawienia**.

Poniższa tabela zawiera podsumowanie powiązanych kosztów i implikacje poszczególnych opcji.

| Opcja | Użycie | Komentarz |
| --- | --- | --- |
| **Magazyn Log Analytics** |  |
| Zalecenia i alerty dotyczące urządzeń| Zalecenia dotyczące zabezpieczeń i alerty wygenerowane przez usługę | Nieopcjonalne |
| Surowe dane zabezpieczeń| Surowe dane zabezpieczeń z urządzeń IoT zebranych przez agentów zabezpieczeń | Wyłącz _zdarzenia związane z zabezpieczeniami magazynu RAW_ |
|

>[!Important]
> Rezygnacja z nich ma surowe konsekwencje dla Azure Security Center na potrzeby dostępności funkcji zabezpieczeń IoT.

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
