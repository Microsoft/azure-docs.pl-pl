---
title: Podręcznik operatora IoT Central platformy Azure
description: Azure IoT Central to platforma aplikacji IoT, która upraszcza tworzenie rozwiązań IoT. Ten artykuł zawiera omówienie roli operatora w IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b8692973f187743e282de6f8e54a55363cc3105b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669947"
---
# <a name="iot-central-operator-guide"></a>Podręcznik operatora IoT Central

*Ten artykuł ma zastosowanie do operatorów.*

Aplikacja IoT Central umożliwia monitorowanie milionów urządzeń i zarządzanie nimi w całym cyklu życia. Ten przewodnik jest przeznaczony dla operatorów, którzy używają aplikacji IoT Central do zarządzania urządzeniami IoT.

Operator:

- Monitoruje i zarządza urządzeniami podłączonymi do aplikacji.
- Rozwiązywanie problemów z urządzeniami i korygowanie ich.
- Inicjuje obsługę nowych urządzeń.

## <a name="monitor-and-manage-devices"></a>Monitorowanie urządzeń i zarządzanie nimi

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="Zrzut ekranu pokazujący widok urządzenia":::

Aby monitorować urządzenia, operator może używać widoków urządzeń zdefiniowanych przez konstruktora rozwiązań w ramach szablonu urządzenia. W tych widokach można wyświetlić wartości telemetryczne i właściwości urządzenia. Przykładem **jest widok opisany na poprzednim** zrzucie ekranu.

Aby uzyskać bardziej szczegółowe informacje, operator może korzystać z grup urządzeń i wbudowanych funkcji analitycznych. Aby dowiedzieć się więcej, zobacz [jak używać analiz do analizowania danych urządzenia](howto-create-analytics.md).

Aby zarządzać poszczególnymi urządzeniami, operator może używać widoków urządzeń do ustawiania właściwości urządzenia i chmury oraz wywoływania poleceń dotyczących urządzeń. Przykładowo Dołącz widoki **Zarządzanie urządzeniami** i **poleceniami** na poprzednim zrzucie ekranu.

Aby zarządzać urządzeniami zbiorczo, operator może tworzyć i planować zadania. Zadania mogą aktualizować właściwości i uruchamiać polecenia na wielu urządzeniach. Aby dowiedzieć się więcej, zobacz [Tworzenie i uruchamianie zadania w aplikacji IoT Central platformy Azure](howto-run-a-job.md).

## <a name="troubleshoot-and-remediate-issues"></a>Rozwiązywanie problemów i korygowanie problemów

Operator jest odpowiedzialny za kondycję aplikacji i jej urządzeń. [Przewodnik rozwiązywania problemów](troubleshoot-connection.md) ułatwia operatorom diagnozowanie i rozwiązywanie typowych problemów. Operator może użyć strony **urządzenia** , aby zablokować urządzenia, które pozornie nie działa, dopóki problem nie zostanie rozwiązany.

## <a name="add-and-remove-devices"></a>Dodawanie i usuwanie urządzeń

Operator może dodawać i usuwać urządzenia do aplikacji IoT Central pojedynczo lub zbiorczo. Aby dowiedzieć się więcej, zobacz [Zarządzanie urządzeniami w aplikacji IoT Central platformy Azure](howto-manage-devices.md).

## <a name="personalize"></a>Personalizowanie

Operatory umożliwiają tworzenie spersonalizowanych pulpitów nawigacyjnych w aplikacji IoT Central, która zawiera linki do najczęściej używanych zasobów. Aby dowiedzieć się więcej, zobacz [Zarządzanie pulpitami nawigacyjnymi](howto-create-personal-dashboards.md#manage-dashboards).

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej o korzystaniu z IoT Central, sugerowane następne kroki to wypróbowanie przewodnika Szybki Start, rozpoczynając od [tworzenia aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md).
