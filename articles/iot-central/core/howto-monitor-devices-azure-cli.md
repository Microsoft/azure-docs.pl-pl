---
title: Monitorowanie łączności urządzeń za pomocą Eksploratora IoT Central platformy Azure
description: Monitoruj komunikaty urządzeń i obserwuj zmiany dotyczące sznurka urządzenia za pomocą interfejsu wiersza polecenia IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 1a6106a45f5062850ceb12205528a05ed1d494be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81756671"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorowanie łączności urządzeń przy użyciu interfejsu wiersza polecenia platformy Azure

*Ten temat dotyczy deweloperów urządzeń i konstruktorów rozwiązań.*

Użyj rozszerzenia usługi IoT dla interfejsu wiersza polecenia platformy Azure, aby wyświetlić komunikaty wysyłane przez urządzenia do IoT Central i obserwować zmiany w bliźniaczych urządzeniach. Za pomocą tego narzędzia można debugować i obserwować łączność urządzeń oraz diagnozować problemy dotyczące komunikatów urządzeń, które nie docierają do chmury lub urządzeń, które nie odpowiadają na zmiany bliźniaczy.

[Aby uzyskać więcej informacji, przejdź do dokumentacji rozszerzeń interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Wymagania wstępne

+ Zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.7 lub nowszej. Sprawdź wersję interfejsu wiersza polecenia platformy Azure, uruchamiając `az --version`. Dowiedz się, jak zainstalować i zaktualizować z dokumentacji [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Konto służbowe na platformie Azure, które zostało dodane jako użytkownik w aplikacji IoT Central.

## <a name="install-the-iot-central-extension"></a>Zainstaluj rozszerzenie IoT Central

Uruchom następujące polecenie w wierszu polecenia, aby zainstalować program:

```azurecli
az extension add --name azure-iot
```

Sprawdź wersję rozszerzenia, uruchamiając:

```azurecli
az --version
```

Powinno zostać wyświetlone rozszerzenie Azure-IoT to 0.8.1 lub nowsze. Jeśli tak nie jest, uruchom polecenie:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Przy użyciu rozszerzenia

W poniższych sekcjach opisano typowe polecenia i opcje, których można użyć podczas uruchamiania `az iot central`programu. Aby wyświetlić pełen zestaw poleceń i opcji, Przekaż `--help` do `az iot central` lub dowolne z jego podpoleceń.

### <a name="login"></a>Logowanie

Zacznij od zalogowania się do interfejsu wiersza polecenia platformy Azure. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Pobieranie identyfikatora aplikacji aplikacji IoT Central
W obszarze **ustawienia administrowania/aplikacji**Skopiuj **Identyfikator aplikacji**. Ta wartość jest używana w dalszych krokach.

### <a name="monitor-messages"></a>Monitorowanie komunikatów
Monitoruj komunikaty wysyłane do aplikacji IoT Central z urządzeń. Dane wyjściowe obejmują wszystkie nagłówki i adnotacje.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Wyświetl właściwości urządzenia
Wyświetl bieżące właściwości urządzenia odczyt i odczyt/zapis dla danego urządzenia.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, sugerowanym następnym krokiem jest zapoznanie się z informacjami [na temat łączności urządzenia w usłudze Azure IoT Central](./concepts-get-connected.md).
