---
title: Usługa Azure IoT Central często zadawane pytania | Microsoft Docs
description: Platforma Azure IoT Central często zadawane pytania i odpowiedzi
author: dominicbetts
ms.author: dobett
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: f9c7412afcc191470902cc256586f9db21f8e78c
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91852147"
---
# <a name="frequently-asked-questions-for-iot-central"></a>Często zadawane pytania dotyczące IoT Central

**Jak mogę sprawdzić, czy występują problemy z poświadczeniami, jeśli urządzenie nie może nawiązać połączenia z moją aplikacją IoT Central?**

[Rozwiązywanie problemów z informacjami o tym, dlaczego dane z urządzeń nie są wyświetlane na platformie Azure IoT Central](troubleshoot-connection.md) obejmuje czynności do diagnozowania problemów z łącznością dla urządzeń.

**Jak mogę pliku biletu z obsługą klienta?**

Jeśli potrzebujesz pomocy, możesz zaplikować [bilet pomocy technicznej systemu Azure](https://portal.azure.com/#create/Microsoft.Support).

Aby uzyskać więcej informacji, w tym o innych opcjach pomocy technicznej, zobacz [Pomoc techniczna i opcje pomocy usługi Azure IoT](../../iot-fundamentals/iot-support-help.md).

**Jak mogę odblokować urządzenie?**

Gdy urządzenie jest zablokowane, nie może wysyłać danych do aplikacji IoT Central. Zablokowane urządzenia mają stan **zablokowany** na stronie **urządzenia** w aplikacji. Operator musi odblokować urządzenie, aby można było wznowić wysyłanie danych:

:::image type="content" source="media/howto-faq/blocked.png" alt-text="Zrzut ekranu przedstawiający zablokowane urządzenie":::

Gdy operator odblokowuje urządzenie, stan powraca do poprzedniej wartości, **zarejestrowane** lub **obsługiwane**.

**Jak mogę zatwierdzić urządzenie?**

Jeśli stan urządzenia oczekuje na **zatwierdzenie** na stronie **urządzenia** , oznacza to, że opcja **autozatwierdzania** jest wyłączona:

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="Zrzut ekranu przedstawiający zablokowane urządzenie":::

Operator musi jawnie zatwierdzić urządzenie przed rozpoczęciem wysyłania danych. Urządzenia, które nie zostały zarejestrowane ręcznie na stronie **urządzeń** , ale połączono z prawidłowymi poświadczeniami, będą mieć stan urządzenia **oczekujący na zatwierdzenie**. Operatory mogą zatwierdzać te urządzenia ze strony **urządzenia** za pomocą przycisku **Zatwierdź** :

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="Zrzut ekranu przedstawiający zablokowane urządzenie":::

**Jak mogę skojarzyć urządzenie z szablonem urządzenia?**

Jeśli stan urządzenia jest **nieskojarzony**, oznacza to, że urządzenie łączące się z IoT Central nie ma skojarzonego szablonu urządzenia. Ta sytuacja zwykle odbywa się w następujących scenariuszach:

- Zestaw urządzeń jest dodawany przy użyciu opcji **Importuj** na stronie **urządzenia** bez określania szablonu urządzenia.
- Urządzenie zostało zarejestrowane ręcznie na stronie **urządzenia** bez określania szablonu urządzenia. Następnie urządzenie jest połączone z prawidłowymi poświadczeniami.  

Operator może skojarzyć urządzenie z szablonem urządzenia ze strony **urządzenia** za pomocą przycisku **Migrowanie** . Aby dowiedzieć się więcej, zobacz [Zarządzanie urządzeniami w aplikacji Azure IoT Central > Migrowanie urządzeń do szablonu](howto-manage-devices.md).

**Gdzie można dowiedzieć się więcej o IoT Hub?**

Usługa Azure IoT Central korzysta z usługi Azure IoT Hub jako bramy w chmurze, która umożliwia łączność z urządzeniem. IoT Hub umożliwia:

- Pozyskiwanie danych na dużą skalę w chmurze.
- Zarządzanie urządzeniami.
- Zabezpieczanie łączności urządzeń.

Aby dowiedzieć się więcej na temat IoT Hub, zobacz [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

**Gdzie można dowiedzieć się więcej o usłudze Device Provisioning Service (DPS)?**

Usługa Azure IoT Central używa usługi DPS, aby umożliwić urządzeniom łączenie się z Twoją aplikacją. Aby dowiedzieć się więcej o roli DPS, która jest odtwarzana w obszarze Łączenie urządzeń z IoT Central, zobacz temat Nawiązywanie [połączenia z usługą Azure IoT Central](concepts-get-connected.md). Aby dowiedzieć się więcej o usłudze DPS, zobacz temat [Inicjowanie obsługi administracyjnej urządzeń za pomocą usługi Azure IoT Hub Device Provisioning Service](../../iot-dps/about-iot-dps.md).