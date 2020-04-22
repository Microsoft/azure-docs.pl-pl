---
title: Użyj rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure do interakcji z urządzeniami IoT Plug and Play Preview | Dokumenty firmy Microsoft
description: Zainstaluj rozszerzenie Usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure i użyj go do interakcji z urządzeniami Typu Plug and Play IoT połączonymi z moim centrum IoT Hub.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770455"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instalowanie i używanie rozszerzenia IoT platformy Azure dla interfejsu wiersza polecenia platformy Azure

[Narzędzie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) to narzędzie wiersza polecenia platformy open source do zarządzania zasobami platformy Azure, takimi jak Centrum IoT Hub. Interfejs wiersza polecenia platformy Azure jest dostępny w systemach Windows, Linux i MacOS. Narzędzie cli platformy Azure jest również wstępnie zainstalowane w [usłudze Azure Cloud Shell.](https://shell.azure.com) Narzędzie interfejsu wiersza polecenia platformy Azure umożliwia zarządzanie zasobami usługi Azure IoT Hub, wystąpieniami usługi inicjowania obsługi administracyjnej urządzeń i połączonymi koncentratorami bez instalowania żadnych rozszerzeń.

Rozszerzenie Usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure to narzędzie wiersza polecenia do interakcji z urządzeniami IoT Plug and Play Preview i testowania. Można użyć rozszerzenia do:

- Połącz się z urządzeniem.
- Wyświetl dane telemetryczne, które wysyła urządzenie.
- Praca z właściwościami urządzenia.
- Wywołaj polecenia urządzenia.

W tym artykule wyjaśniono, jak:

- Zainstaluj i skonfiguruj rozszerzenie Usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure.
- Użyj rozszerzenia do interakcji z urządzeniami i testowania.
- Użyj rozszerzenia do zarządzania interfejsami w repozytorium modelu.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Instalowanie rozszerzenia IoT platformy Azure dla interfejsu wiersza polecenia platformy Azure

### <a name="step-1---install-the-azure-cli"></a>Krok 1 — instalowanie interfejsu wiersza polecenia platformy Azure

Postępuj zgodnie z [instrukcjami instalacji,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) aby skonfigurować interfejsu wiersza polecenia platformy Azure w twoim środowisku. Aby użyć wszystkich poniższych poleceń, wersja interfejsu wiersza polecenia platformy Azure musi mieć wersję 2.0.73 lub niższą. Użyj polecenia `az -–version` w celu przeprowadzenia weryfikacji.

### <a name="step-2---install-iot-extension"></a>Krok 2 - Zainstaluj rozszerzenie IoT

[Plik readme rozszerzenia IoT](https://github.com/Azure/azure-iot-cli-extension) opisuje kilka sposobów instalowania rozszerzenia. Najprostszym sposobem jest uruchomienie polecenia `az extension add --name azure-iot`. Po zakończeniu instalacji można użyć polecenia `az extension list` w celu zweryfikowania aktualnie zainstalowanych rozszerzeń lub polecenia `az extension show --name azure-iot` w celu wyświetlenia szczegółów rozszerzenia IoT. Aby usunąć rozszerzenie, można użyć polecenia `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Używanie rozszerzenia IoT platformy Azure dla interfejsu wiersza polecenia platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne

Aby zalogować się do subskrypcji platformy Azure, uruchom następujące polecenie:

```azurecli
az login
```

> [!NOTE]
> Jeśli używasz powłoki chmury platformy Azure, automatycznie się zalogujesz i nie musisz uruchamiać poprzedniego polecenia.

Aby użyć rozszerzenia IoT platformy Azure dla interfejsu wiersza polecenia platformy Azure, potrzebujesz:

- Centrum Usługi Azure IoT. Istnieje wiele sposobów dodawania centrum IoT hub do subskrypcji platformy Azure, takich jak [Tworzenie centrum IoT przy użyciu interfejsu wiersza polecenia platformy Azure.](../iot-hub/iot-hub-create-using-cli.md) Aby uruchomić polecenia rozszerzenia IoT platformy Azure, potrzebujesz ciągu połączenia centrum IoT. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

- Urządzenie zarejestrowane w centrum IoT Hub. Aby zarejestrować urządzenie, można użyć następującego polecenia interfejsu `{YourIoTHubName}` `{YourDeviceID}` wiersza polecenia platformy Azure, aby zastąpić symbole zastępcze wartościami:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Niektóre polecenia wymagają ciągu połączenia dla repozytorium modelu firmy. Repozytorium modelu dla twojej firmy jest tworzone podczas pierwszego [dołączania do portalu Certyfikatu platformy Azure dla IoT.](howto-onboard-portal.md) Strona trzecia może udostępnić ci swój ciąg połączenia repozytorium modelu, aby zapewnić dostęp do ich interfejsów i modeli.

### <a name="interact-with-a-device"></a>Interakcja z urządzeniem

Rozszerzenie służy do wyświetlania i interakcji z urządzeniami Typu Plug and Play IoT, które są podłączone do centrum IoT hub. Rozszerzenie współpracuje z cyfrową bliźniaczką, która reprezentuje urządzenie IoT Plug and Play.

#### <a name="list-devices-and-interfaces"></a>Wyświetlanie listy urządzeń i interfejsów

Wyświetl listę wszystkich urządzeń w centrum IoT Hub:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Wyświetl listę wszystkich interfejsów zarejestrowanych przez urządzenie Typu Plug and Play IoT:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Właściwości

Wyświetl listę wszystkich właściwości i wartości właściwości interfejsu na urządzeniu:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Ustaw wartość właściwości odczytu i zapisu:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Przykładowy plik ładunku, aby ustawić właściwość **name** w interfejsie **czujnika** urządzenia **contoso** wygląda następująco:

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Polecenia

Wyświetl listę wszystkich poleceń interfejsu na urządzeniu:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Bez `--repo-login` parametru to polecenie używa repozytorium modelu publicznego.

Wywoływanie polecenia:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Cyfrowe wydarzenia bliźniacze

Monitoruj wszystkie cyfrowe zdarzenia bliźniacze dwie bliźniacze IoT z określonego urządzenia i interfejsu przechodzącego do grupy odbiorców centrum zdarzeń **$Default:**

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Monitoruj wszystkie cyfrowe zdarzenia bliźniacze dwie usługi IoT Plug and Play z określonego urządzenia i interfejsu, przechodząc do określonej grupy odbiorców:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Zarządzanie interfejsami w repozytorium modelu

Poniższe polecenia używają publicznego repozytorium modelu IoT Plug and Play. Aby użyć repozytorium modelu firmy, dodaj `--login` argument z ciągiem połączenia repozytorium modelu.

Lista interfejsów w publicznym repozytorium modeli IoT Plug and Play:

```azurecli
az iot pnp interface list
```

Pokaż interfejs w publicznym repozytorium modeli IoT Plug and Play:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Utwórz interfejs w repozytorium modeli firmowych IoT Plug and Play:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Nie można bezpośrednio utworzyć interfejsu w repozytorium modelu publicznego.

Zaktualizuj interfejs w repozytorium modeli firmowych IoT Plug and Play:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Nie można bezpośrednio zaktualizować interfejsu w repozytorium modelu publicznego.

Opublikuj interfejs z repozytorium modelu firmy IoT Plug and Play w publicznym repozytorium modelu. Ta operacja sprawia, że interfejs jest niezmienny:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Tylko partnerzy firmy Microsoft mogą publikować interfejsy w publicznym repozytorium modelu.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Zarządzanie modelami możliwości urządzeń w repozytorium modeli

Poniższe polecenia używają publicznego repozytorium modelu IoT Plug and Play. Aby użyć repozytorium modelu firmy, dodaj `--login` argument z ciągiem połączenia repozytorium modelu.

Lista modeli możliwości urządzenia w publicznym repozytorium modeli IoT Plug and Play:

```azurecli
az iot pnp capability-model list
```

Pokaż model możliwości urządzenia w publicznym repozytorium modeli IoT Plug and Play:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Tworzenie modelu możliwości urządzenia w repozytorium modelu firmy IoT Plug and Play:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Nie można bezpośrednio utworzyć modelu w repozytorium modelu publicznego.

Zaktualizuj model możliwości urządzenia w repozytorium modelu firmy IoT Plug and Play:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Nie można bezpośrednio zaktualizować modelu w repozytorium modelu publicznego.

Opublikuj model możliwości urządzenia z repozytorium modelu firmy IoT Plug and Play w publicznym repozytorium modelu. Ta operacja sprawia, że model jest niezmienny:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Tylko partnerzy firmy Microsoft mogą publikować modele w publicznym repozytorium modelu.

## <a name="next-steps"></a>Następne kroki

W tym artykule in how-to dowiesz się, jak zainstalować i używać rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure do interakcji z urządzeniami Typu Plug and Play. Sugerowanym następnym krokiem jest nauczenie się [zarządzania modelami.](./howto-manage-models.md)
