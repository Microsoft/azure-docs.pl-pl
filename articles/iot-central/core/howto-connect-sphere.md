---
title: Podłączanie urządzenia Azure Sphere na platformie Azure IoT Central | Microsoft Docs
description: Dowiedz się, jak połączyć urządzenie Azure Sphere (DevKit) z aplikacją IoT Central platformy Azure.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.custom: device-developer
ms.openlocfilehash: 770f6e56a669ab2d9b425a7a2879eeef5d37377b
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123427"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>Łączenie urządzenia Azure Sphere z aplikacją Azure IoT Central

*Ten artykuł ma zastosowanie do deweloperów urządzeń.*

W tym artykule opisano sposób nawiązywania połączenia z urządzeniem Azure Sphere (DevKit) z aplikacją IoT Central platformy Azure.

Azure Sphere to zabezpieczona platforma aplikacji wysokiego poziomu z wbudowanymi funkcjami komunikacji i zabezpieczeń na potrzeby urządzeń połączonych z Internetem. Obejmuje ona bezpieczną, połączoną, skrzyżowaną jednostkę mikrokontrolerów (MIKROKONTROLERY), niestandardową, opartą na systemie Linux system operacyjny (OS) i usługę zabezpieczeń opartą na chmurze, która zapewnia ciągły, odnawialny poziom zabezpieczeń. Aby uzyskać więcej informacji, zobacz [co to jest Azure Sphere?](/azure-sphere/product-overview/what-is-azure-sphere).

[Azure Sphere Development Kit](https://azure.microsoft.com/services/azure-sphere/get-started/) zapewniają wszystko, czego potrzebujesz, aby rozpocząć tworzenie prototypów i opracowywanie aplikacji Azure Sphere. Usługa Azure IoT Central z Azure Sphere zapewnia kompleksowy stos rozwiązania IoT. Azure Sphere zapewnia obsługę urządzeń i IoT Central jako kod zerowy, zarządzaną platformą aplikacji IoT.

W tym artykule poznasz następujące informacje:

- Utwórz urządzenie Azure Sphere w IoT Central przy użyciu szablonu urządzenia Azure Sphere DevKit z biblioteki.
- Przygotuj Azure Sphere urządzenie DevKit dla usługi Azure IoT.
- Połącz Azure Sphere DevKit z usługą Azure IoT Central.
- Wyświetl dane telemetryczne z urządzenia w IoT Central.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące zasoby:

- Aplikacja IoT Central platformy Azure.
- Program Visual Studio 2019, wersja 16,4 lub nowsza.
- [Azure Sphere MT3620 Development Kit z Seeed Studios](/azure-sphere/hardware/mt3620-reference-board-design).

> [!NOTE]
> Jeśli nie masz urządzenia fizycznego, po pierwszym kroku Przeskocz do ostatniej sekcji, aby wypróbować urządzenie symulowane.

## <a name="create-the-device-in-iot-central"></a>Tworzenie urządzenia w IoT Central

Aby utworzyć urządzenie Azure Sphere w IoT Central:

1. W aplikacji IoT Central platformy Azure wybierz kartę **Szablony urządzeń** i wybierz pozycję **+ Nowy**. W sekcji **Użyj proponowanego szablonu urządzenia**wybierz pozycję **Azure Sphere przykładowe urządzenie**.

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Szablon urządzenia dla Azure Sphere DevKit":::

1. W szablonie urządzenia edytuj widok o nazwie **Przegląd** , aby wyświetlić **temperaturę** i **naciśnięcie przycisku**.

1. Wybierz pozycję **Edytowanie urządzenia i danych w chmurze** , aby dodać kolejny widok, który pokazuje **diodę stanu**właściwości odczyt/zapis. Przeciągnij Właściwość **status LED** do pustego, kropkowanego prostokąta po prawej stronie formularza. Wybierz pozycję **Zapisz**.

## <a name="prepare-the-device"></a>Przygotowywanie urządzenia

Aby można było nawiązać połączenie z urządzeniem Azure Sphere DevKit w IoT Central, należy [skonfigurować urządzenie i środowisko programistyczne](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT).

## <a name="connect-the-device"></a>Podłącz urządzenie

Aby umożliwić przykładowi łączenie się z IoT Central, musisz [skonfigurować aplikację IoT Central platformy Azure, a następnie zmodyfikować manifest aplikacji przykładowej](https://aka.ms/iotcentral-sphere-git-readme).

## <a name="view-the-telemetry-from-the-device"></a>Wyświetlanie danych telemetrycznych z urządzenia

Gdy urządzenie jest połączone z IoT Central, dane telemetryczne są widoczne na pulpicie nawigacyjnym.

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Szablon urządzenia dla Azure Sphere DevKit":::

## <a name="create-a-simulated-device"></a>Tworzenie symulowanego urządzenia

Jeśli nie masz urządzenia fizycznego Azure Sphere DevKit, możesz utworzyć symulowane urządzenie, aby wypróbować aplikację Azure IoT Central.

Aby utworzyć symulowane urządzenie:

- Wybierz **urządzenia > usłudze Azure IoT Sphere**
- Wybierz pozycję **+ Nowe**.
- Wprowadź unikatowy **Identyfikator urządzenia** i przyjazną **nazwę urządzenia**.
- Włącz ustawienie **symulowane** .
- Wybierz przycisk **Utwórz**.

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, Oto kilka sugerowanych następnych kroków:

- Przeczytaj o [łączności urządzeń w usłudze Azure IoT Central](./concepts-get-connected.md)
- Dowiedz się, jak [monitorować łączność z urządzeniem przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-monitor-devices-azure-cli.md)