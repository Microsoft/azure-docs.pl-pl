---
title: Uruchamianie wielu akcji z poziomu reguły IoT Central platformy Azure | Microsoft Docs
description: Uruchom wiele akcji z jednej reguły IoT Central i Utwórz grupy akcji wielokrotnego użytku, które można uruchamiać z wielu reguł.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 84a723023bf8614b837ba9783bae987403921c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585493"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Grupuj wiele akcji do uruchomienia z jednej lub kilku reguł

*Ten artykuł dotyczy konstruktorów i administratorów.*

W usłudze Azure IoT Central tworzysz reguły uruchamiania akcji po spełnieniu warunku. Reguły są oparte na danych telemetrycznych lub zdarzeniach urządzeń. Na przykład można powiadomić operatora, gdy temperatura urządzenia przekroczy wartość progową. W tym artykule opisano, jak używać *grup akcji* [Azure monitor](../../azure-monitor/overview.md) do dołączania wielu akcji do reguły IoT Central. Grupę akcji można dołączyć do wielu reguł. [Grupa akcji](../../azure-monitor/alerts/action-groups.md) to zbiór preferencji powiadomień definiowanych przez właściciela subskrypcji platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacja utworzona przy użyciu standardowego planu cenowego
- Konto platformy Azure i subskrypcja do tworzenia grup akcji Azure Monitor i zarządzania nimi

## <a name="create-action-groups"></a>Tworzenie grup akcji

Można [tworzyć grupy akcji i zarządzać nimi w Azure Portal](../../azure-monitor/alerts/action-groups.md) lub z [szablonem Azure Resource Manager](../../azure-monitor/alerts/action-groups-create-resource-manager-template.md).

Grupa akcji może:

- Wysyłaj powiadomienia, takie jak wiadomość e-mail, wiadomość SMS lub nawiązać połączenie głosowe.
- Uruchom akcję, taką jak wywołanie elementu webhook.

Poniższy zrzut ekranu przedstawia grupę akcji, która wysyła wiadomości e-mail i powiadomienia SMS oraz wywołuje element webhook:

![Grupa akcji](media/howto-use-action-groups/actiongroup.png)

Aby użyć grupy akcji w regule IoT Central, Grupa akcji musi znajdować się w tej samej subskrypcji platformy Azure co aplikacja IoT Central.

## <a name="use-an-action-group"></a>Korzystanie z grupy akcji

Aby użyć grupy akcji w aplikacji IoT Central, należy najpierw utworzyć regułę. Po dodaniu akcji do reguły wybierz pozycję **Azure monitor grupy akcji**:

![Wybieranie akcji](media/howto-use-action-groups/chooseaction.png)

Wybierz grupę akcji z subskrypcji platformy Azure:

![Wybierz grupę akcji](media/howto-use-action-groups/chooseactiongroup.png)

Wybierz pozycję **Zapisz**. Grupa akcji zostanie teraz wyświetlona na liście akcji do uruchomienia, gdy reguła zostanie wyzwolona:

![Zapisano grupę akcji](media/howto-use-action-groups/savedactiongroup.png)

Poniższa tabela zawiera podsumowanie informacji wysyłanych do obsługiwanych typów akcji:

| Typ akcji | Format danych wyjściowych |
| ----------- | -------------- |
| E-mail       | Szablon wiadomości e-mail w warstwie Standardowa IoT Central |
| SMS         | Alert IoT Central platformy Azure: $ {applicationName}-"$ {RuleName}" wyzwolony dla "$ {DeviceName}" w $ {triggerDate} $ {triggerTime} |
| Połączenia głosowe       | Alert dotyczący platformy Azure I. O. T Central: reguła "$ {RuleName}" została wyzwolona na urządzeniu "$ {DeviceName}" pod adresem $ {triggerDate} $ {triggerTime} w aplikacji $ {applicationName} |
| Webhook     | {"schemaId": "AzureIoTCentralRuleWebhook", "Data": {[regularny ładunek elementu webhook](howto-create-webhooks.md#payload)}} |

Następujący tekst to przykładowy komunikat SMS z grupy akcji:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać grup akcji z regułami, sugerowanym następnym krokiem jest zapoznanie się z tematem [Zarządzanie urządzeniami](howto-manage-devices.md).
