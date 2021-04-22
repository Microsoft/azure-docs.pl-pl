---
title: Monitorowanie łączności urządzeń przy użyciu eksploratora Azure IoT Central Windows
description: Monitoruj komunikaty urządzeń i obserwuj zmiany bliźniaczych reprezentacji urządzeń za pośrednictwem interfejsu wiersza IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: 30a823b7e78145224929b427e9e37522a7e29f09
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871295"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorowanie łączności urządzeń przy użyciu interfejsu wiersza polecenia platformy Azure

*Ten temat dotyczy deweloperów urządzeń i konstruktorów rozwiązań.*

Użyj rozszerzenia IoT interfejsu wiersza polecenia platformy Azure, aby zobaczyć komunikaty wysyłane przez urządzenia IoT Central i obserwować zmiany w bliźniaczej reprezentacji urządzenia. Za pomocą tego narzędzia można debugować i obserwować łączność urządzeń oraz diagnozować problemy z komunikatami urządzeń, które nie docierają do chmury lub urządzenia nie reagują na zmiany bliźniaczych reprezentacji.

[Aby uzyskać więcej informacji, odwiedź stronę z informacjami o rozszerzeniach interfejsu wiersza polecenia platformy Azure](/cli/azure/iot/central)

## <a name="prerequisites"></a>Wymagania wstępne

+ Zainstalowany interfejs wiersza polecenia platformy Azure w wersji 2.7.0 lub wyższej. Sprawdź wersję interfejsu wiersza polecenia platformy Azure, uruchamiając narzędzie `az --version` . Dowiedz się, jak instalować i aktualizować z dokumentów interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
+ Konto służbowe na platformie Azure dodane jako użytkownik w IoT Central aplikacji.

## <a name="install-the-iot-central-extension"></a>Instalowanie IoT Central rozszerzenia

Uruchom następujące polecenie w wierszu polecenia, aby je zainstalować:

```azurecli
az extension add --name azure-iot
```

Sprawdź wersję rozszerzenia, uruchamiając:

```azurecli
az --version
```

Powinno zostać wyświetlony rozszerzenie azure-iot w wersji 0.9.9 lub wyższej. Jeśli tak nie jest, uruchom:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Korzystanie z rozszerzenia

W poniższych sekcjach opisano typowe polecenia i opcje, których można użyć podczas uruchamiania polecenia `az iot central` . Aby wyświetlić pełny zestaw poleceń i opcji, przekaż polecenie do polecenia lub `--help` `az iot central` dowolny z jego poleceń.

### <a name="login"></a>Zaloguj się

Rozpocznij od zalogowania się do interfejsu wiersza polecenia platformy Azure. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Uzyskiwanie identyfikatora aplikacji aplikacji IoT Central aplikacji
W **ustawieniach administracji/aplikacji** skopiuj **identyfikator aplikacji**. Tej wartości użyjemy w kolejnych krokach.

### <a name="monitor-messages"></a>Monitorowanie komunikatów
Monitoruj komunikaty wysyłane do aplikacji IoT Central z urządzeń. Dane wyjściowe zawierają wszystkie nagłówki i adnotacje.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Wyświetlanie właściwości urządzenia
Wyświetl bieżące właściwości urządzenia do odczytu i odczytu/zapisu dla danego urządzenia.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzeń, sugerowanym następnym krokiem jest zapoznanie się z tematem Łączność urządzenia w u [Azure IoT Central.](./concepts-get-connected.md)