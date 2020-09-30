---
title: Korzystanie z rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure w celu współpracy z urządzeniami Plug and Play IoT | Microsoft Docs
description: Zainstaluj rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure i użyj go do współpracy z urządzeniami Plug and Play IoT podłączonymi do centrum IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 680cd4ef4f73c63850a2137b344fd0af6b27c673
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577462"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instalowanie i używanie rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) to narzędzie wielodostępnej do obsługi wielu platform i zarządzania zasobami platformy Azure, takimi jak IoT Hub. Interfejs wiersza polecenia platformy Azure jest dostępny w systemach Windows, Linux i macOS. Interfejs wiersza polecenia platformy Azure umożliwia zarządzanie zasobami IoT Hub platformy Azure, wystąpieniami usługi Device Provisioning Service i połączonymi centrami bez instalowania żadnych rozszerzeń.

Rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure to narzędzie wiersza poleceń umożliwiające współdziałanie z urządzeniami Plug and Play IoT i testowanie ich. Możesz użyć rozszerzenia, aby:

- Nawiąż połączenie z urządzeniem.
- Wyświetl dane telemetryczne wysyłane przez urządzenie.
- Pracuj z właściwościami urządzeń.
- Wywoływanie poleceń dotyczących urządzeń.

W tym artykule wyjaśniono, jak:

- Zainstaluj i skonfiguruj rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure.
- Użyj rozszerzenia, aby korzystać z i testować urządzenia.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Zainstaluj rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure

### <a name="step-1---install-the-azure-cli"></a>Krok 1. Instalowanie interfejsu wiersza polecenia platformy Azure

Postępuj zgodnie z [instrukcjami instalacji](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) , aby skonfigurować interfejs wiersza polecenia platformy Azure w Twoim środowisku. Aby uzyskać najlepsze doświadczenia, wersja interfejsu wiersza polecenia platformy Azure powinna być w wersji 2.9.1 lub nowszej. Użyj polecenia `az -–version` w celu przeprowadzenia weryfikacji.

### <a name="step-2---install-iot-extension"></a>Krok 2. Instalowanie rozszerzenia IoT

[Plik readme rozszerzenia IoT](https://github.com/Azure/azure-iot-cli-extension) opisuje kilka sposobów instalowania rozszerzenia. Najprostszym sposobem jest uruchomienie polecenia `az extension add --name azure-iot`. Po zakończeniu instalacji można użyć polecenia `az extension list` w celu zweryfikowania aktualnie zainstalowanych rozszerzeń lub polecenia `az extension show --name azure-iot` w celu wyświetlenia szczegółów rozszerzenia IoT. W momencie pisania numer wersji rozszerzenia to `0.10.0` .

Aby usunąć rozszerzenie, można użyć polecenia `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Korzystanie z rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne

Aby zalogować się do subskrypcji platformy Azure, uruchom następujące polecenie:

```azurecli
az login
```

Aby korzystać z rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure, potrzebne są:

- Usługa Azure IoT Hub. Istnieje wiele sposobów dodawania Centrum IoT Hub do subskrypcji platformy Azure, np. [Tworzenie Centrum IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure](../iot-hub/iot-hub-create-using-cli.md). Do uruchamiania poleceń rozszerzenia usługi Azure IoT wymagane są parametry połączenia Centrum IoT Hub. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Urządzenie zarejestrowane w usłudze IoT Hub. Aby zarejestrować urządzenie, można użyć poniższego polecenia interfejsu CLI platformy Azure. Pamiętaj, aby zastąpić `{YourIoTHubName}` `{YourDeviceID}` symbole zastępcze i wartościami:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>Korzystanie z urządzenia

Za pomocą rozszerzenia można wyświetlać i korzystać z urządzeń Plug and Play IoT podłączonych do centrum IoT Hub. Rozszerzenie współpracuje z dwucyfrowym sznurem, który reprezentuje urządzenie Plug and Play IoT.

#### <a name="list-devices"></a>Wyświetl listę urządzeń

Wyświetl listę wszystkich urządzeń na IoT Hub:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>Wyświetl dwuosiową cyfrę

Wyświetl cyfrowe sznurki urządzenia:

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>Właściwości

Ustaw wartość właściwości do odczytu i zapisu:

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>Polecenia

Wywołaj polecenie:

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>Cyfrowe wydarzenia bliźniaczye

Monitoruj wszystkie usługi IoT Plug and Play zdarzenia cyfrowego przędzy z określonego urządzenia i interfejsu przechodzącego do **$default** grupy odbiorców centrum zdarzeń:

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

### <a name="manage-models-in-the-model-repository"></a>Zarządzanie modelami w repozytorium modeli

Aby zarządzać modelami w repozytorium, można użyć poleceń repozytorium modelu interfejsu wiersza polecenia platformy Azure.

#### <a name="create-model-repository"></a>Utwórz repozytorium modelu

Utwórz nowe repozytorium firmowe usługi IoT Plug and Play dla Twojej dzierżawy, jeśli jesteś pierwszym użytkownikiem w dzierżawie:

```azurecli
az iot pnp repo create
```

#### <a name="manage-model-repository-tenant-roles"></a>Zarządzaj rolami dzierżawy repozytorium modelu

Utwórz przypisanie roli dla użytkownika lub nazwy głównej usługi do określonego zasobu.

Na przykład nadaj user@consoso.com roli **ModelsCreator** dzierżawcy:

```azurecli
az iot pnp role-assignment create --resource-id {tenant_id} --resource-type Tenant --subject-id {user@contoso.com} --subject-type User --role ModelsCreator
```

Lub nadaj user@consoso.com roli **ModelAdministrator** dla określonego modelu:

```azurecli
az iot pnp role-assignment create --resource-id {model_id} --resource-type Model --subject-id {user@contoso.com} --subject-type User --role ModelAdministrator
```

#### <a name="create-a-model"></a>Tworzenie modelu

Utwórz nowy model w repozytorium firmy:

```azurecli
az iot pnp model create --model {model_json or path_to_file}
```

#### <a name="search-a-model"></a>Przeszukiwanie modelu

Utwórz listę modeli pasujących do określonego słowa kluczowego:

```azurecli
az iot pnp model list -q {search_keyword}
```

#### <a name="publish-a-model"></a>Publikowanie modelu

Opublikuj model urządzenia znajdujący się w repozytorium firmy w repozytorium publicznym.

Na przykład utwórz publiczny model z IDENTYFIKATORem `dtmi:com:example:ClimateSensor;1` :

```azurecli
az iot pnp model publish --dtmi "dtmi:com:example:ClimateSensor;1"
```

Aby opublikować model, muszą zostać spełnione następujące wymagania:

- Dzierżawa firmy lub organizacji musi być partnerem firmy Microsoft. 
- Nazwa główna użytkownika lub usługi musi być członkiem roli **wydawcy** dzierżawy repozytorium.

## <a name="next-steps"></a>Następne kroki

W tym artykule poznasz sposób instalowania i używania rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure w celu współdziałania z urządzeniami Plug and Play IoT. Sugerowany następny krok to Dowiedz się, jak używać programu [Azure IoT Explorer z urządzeniami](./howto-use-iot-explorer.md).
