---
title: Jak zatwierdzić urządzenia Plug and Play IoT | Microsoft Docs
description: Jako Konstruktor urządzeń Dowiedz się, jak uruchomić testy i przesłać urządzenie do certyfikacji
author: konichi3
ms.author: koichih
ms.date: 08/21/2020
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6aa4273933190ccfe495bcaf243ee15a5ce823fb
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577649"
---
# <a name="how-to-certify-iot-plug-and-play-devices"></a>Jak zatwierdzić urządzenia Plug and Play IoT

Program certyfikacji urządzenia Plug and Play IoT zawiera narzędzia do sprawdzenia, czy urządzenie spełnia wymagania certyfikacji Plug and Play IoT. Narzędzia ułatwiają również organizacjom Rozwiązywanie problemów z dostępnością urządzeń Plug and Play IoT. Te certyfikowane urządzenia są dostosowane do rozwiązań IoT i pomagają w skróceniu czasu wprowadzenia na rynek.

W tym artykule wyjaśniono, jak:

- Instalowanie rozszerzenia narzędzia wiersza polecenia usługi Azure IoT dla interfejsu CLI platformy Azure
- Uruchom testy Plug and Play IoT, aby sprawdzić poprawność aplikacji urządzenia podczas fazy tworzenia  
- Weryfikowanie aplikacji urządzenia przy użyciu portalu Azure Certified Device

## <a name="prepare-your-device"></a>Przygotowywanie urządzenia

Kod aplikacji, który działa na Plug and Play IoT, musi:

- Połącz się z usługą Azure IoT Hub przy użyciu [usługi Device Provisioning (DPS)](../iot-dps/about-iot-dps.md).
- Postępuj zgodnie z [Konwencją Play](concepts-convention.md) , aby zaimplementować dane telemetryczne, właściwości i polecenia.

Aplikacja jest oprogramowaniem instalowanym niezależnie od systemu operacyjnego lub z systemem operacyjnym w obrazie oprogramowania układowego, który jest Flash na urządzeniu.

Proces certyfikacji sprawdza, czy urządzenie jest zgodne Plug and Play IoT, sprawdzając, czy implementacja urządzenia jest zgodna z danymi telemetrycznymi, właściwościami i poleceniami zdefiniowanymi w modelu urządzenia [Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) i czy model jest dostępny w [repozytorium modelu publicznego usługi Azure IoT](concepts-model-repository.md).

Aby spełnić wymagania dotyczące certyfikacji, urządzenie musi:

- Nawiązuje połączenie z usługą Azure IoT Hub przy użyciu usługi [DPS](../iot-dps/about-iot-dps.md).
- Zaimplementuj dane telemetryczne, właściwości lub polecenia po Konwencji Plug and Play IoT.
- Opisz interakcję urządzenia z modelem [DTDL v2](https://aka.ms/dtdl) .
- Publikuj model i wszystkie wymagane interfejsy w [repozytorium modelu publicznego usługi Azure IoT](https://devicemodels.azureiotsolutions.com/)
- Wyślij Identyfikator modelu podczas rejestracji w usłudze [DPS](concepts-developer-guide-device-csharp.md#dps-payload) w ładunku aprowizacji.
- Ogłoś Identyfikator modelu podczas połączenia usługi [MQTT](concepts-developer-guide-device-csharp.md#model-id-announcement).

## <a name="test-with-the-azure-iot-extension-cli"></a>Testowanie przy użyciu interfejsu wiersza polecenia usługi Azure IoT Extension

[Rozszerzenie interfejsu wiersza polecenia usługi Azure IoT](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/product?view=azure-cli-latest&preserve-view=true) pozwala sprawdzić, czy implementacja urządzenia jest zgodna z modelem przed przesłaniem urządzenia do certyfikacji za pomocą portalu certyfikowanego urządzenia platformy Azure.

Poniższe kroki pokazują, jak przygotować się do testów certyfikacji i uruchomić je za pomocą interfejsu wiersza polecenia:

### <a name="install-the-azure-iot-extension-for-the-azure-cli"></a>Instalowanie rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure

Zobacz instrukcje instalacji, aby skonfigurować [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) w Twoim środowisku.

Aby zainstalować rozszerzenie Azure IoT, uruchom następujące polecenie:

```azurecli
az extension add --name azure-iot
```

Aby dowiedzieć się więcej, zobacz [interfejs wiersza polecenia platformy Azure dla usługi Azure IoT](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-iot?view=azure-cli-latest&preserve-view=true).

### <a name="create-a-new-product-test"></a>Utwórz nowy test produktu

Następujące polecenie tworzy test przy użyciu usługi DPS z metodą zaświadczania klucza symetrycznego:

- Tworzy nowy produkt do testowania i generuje konfigurację testu. W danych wyjściowych zostaną wyświetlone informacje dotyczące usługi DPS, których urządzenie musi używać do aprowizacji: klucz podstawowy, identyfikator urządzenia i zakres identyfikatorów.
- Określa folder zawierający pliki DTDL opisujące model.

```azurecli
az iot product test create --badge-type Pnp --at SymmetricKey --device-type FinishedProduct --models {local folder name}
```

> [!NOTE]
> Jeśli używasz interfejsu wiersza polecenia, musisz [zalogować](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true) się do swojej subskrypcji.

Dane wyjściowe JSON z polecenia zawierają `primaryKey` , `registrationId` i `scopeID` do użycia podczas łączenia urządzenia.

Oczekiwane dane wyjściowe:

```json
"deviceType": "FinishedProduct",
"id": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
"provisioningConfiguration": {
  "symmetricKeyEnrollmentInformation": {
    "primaryKey":"Ci/Ghpqp0n4m8FD5PTicr6dEikIfM3AtVWzAhObU7yc=",
    "registrationId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
    "scopeId": "0ne000FFA42"
  }
}
```

### <a name="connect-your-device"></a>Nawiązywanie połączenia z urządzeniem

Aby połączyć urządzenie z wystąpieniem IoT Hub testowego, użyj danych wyjściowych informacji o wyjściu przez poprzednie polecenie.

### <a name="manage-and-configure-the-product-tests"></a>Zarządzanie i konfigurowanie testów produktu

Gdy urządzenie jest połączone i gotowe do współdziałania z usługą IoT Hub, wygeneruj plik konfiguracji testu produktu. Aby utworzyć plik:

- Użyj testu `id` z danych wyjściowych poprzedniego polecenia.
- Użyj `--wait` parametru, aby pobrać przypadek testowy.

```azurecli
az iot product test task create --type GenerateTestCases --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Oczekiwane dane wyjściowe:

```json
{
  "deviceTestId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
  "error": null,
  "id": "526da38e-91fc-4e20-a761-4f04b392c42b",
  "resultLink": "/deviceTests/d45d53d9-656d-4be7-9bbf-140bc87e98dc/TestCases",
  "status": "Completed",
  "type": "GenerateTestCases"
}
```

Możesz użyć polecenia, `az iot product test case update` Aby zmodyfikować plik konfiguracji testu.

### <a name="run-the-tests"></a>Uruchamianie testów

Po wygenerowaniu konfiguracji testu następnym krokiem jest uruchomienie testów. Użyj tego samego `devicetestId` z poprzednich poleceń jako parametru, aby uruchomić testy. Sprawdź wyniki testu, aby upewnić się, że wszystkie testy mają stan `Passed` .

```azurecli
az iot product test task create --type QueueTestRun --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Przykładowe dane wyjściowe przebiegu testu

```json
      "validationTasks": [
        {
          "componentName": "Default component",
          "endTime": "2020-08-25T05:18:49.5224772+00:00",
          "interfaceId": "dtmi:com:example:TemperatureController;1",
          "logs": [
            {
              "message": "Waiting for telemetry from the device",
              "time": "2020-08-25T05:18:37.3862586+00:00"
            },
            {
              "message": "Validating PnP properties",
              "time": "2020-08-25T05:18:37.3875168+00:00"
            },
            {
              "message": "Validating PnP commands",
              "time": "2020-08-25T05:18:37.3894343+00:00"
            },
            {
              "message": "{\"propertyName\":\"serialNumber\",\"expectedSchemaType\":null,\"actualSchemaType\":null,\"message\":\"Property is successfully validated\",\"passed\":true,\"time\":\"2020-08-25T05:18:37.4205985+00:00\"}",
              "time": "2020-08-25T05:18:37.4205985+00:00"
            },
            {
              "message": "PnP interface properties validation passed",
              "time": "2020-08-25T05:18:37.4206964+00:00"
            },
```

## <a name="test-using-the-azure-certified-device-portal"></a>Testowanie przy użyciu portalu certyfikowanego urządzenia platformy Azure

Poniższe kroki pokazują, jak korzystać z [portalu urządzenia z certyfikatem platformy Azure](https://aka.ms/acdp) do dołączenia, zarejestrować szczegóły produktu, przesłać Przewodnik wprowadzający, a następnie uruchomić testy certyfikacji.

### <a name="onboarding"></a>Dołączanie

Aby korzystać z [portalu certyfikacji](https://aka.ms/acdp), musisz użyć Azure Active Directory z dzierżawy służbowej.

Aby opublikować modele w repozytorium modelu publicznego usługi Azure IoT, Twoje konto musi być członkiem [Microsoft Partner Network](https://partner.microsoft.com). System sprawdza, czy identyfikator Microsoft Partner Network istnieje, a konto jest w pełni zbadane przed opublikowaniem w katalogu urządzeń.

### <a name="company-profile"></a>Profil firmy

Profilem firmy można zarządzać z menu nawigacji po lewej stronie. Profil firmy zawiera adres URL firmy, adres e-mail i logo firmy. Przed uruchomieniem jakichkolwiek operacji certyfikacyjnych należy zaakceptować umowę o programie na tej stronie.

Informacje o profilu firmy są używane w opisie urządzenia zaprezentowanym w katalogu urządzeń.

### <a name="create-new-project"></a>Tworzenie nowego projektu

Aby zatwierdzić urządzenie, należy najpierw utworzyć nowy projekt.

Przejdź do [portalu certyfikacji](https://aka.ms/acdp). Na stronie **projekty** wybierz pozycję *+ Utwórz nowy projekt*. Następnie wprowadź nazwę projektu, nazwę urządzenia i wybierz klasę urządzeń.

Informacje o produkcie, które podano podczas procesu certyfikacji, są podzielone na cztery kategorie:

- Informacje o urządzeniu. Zbiera informacje o urządzeniu, takie jak jego nazwa, opis, certyfikaty i system operacyjny.
- Przewodnik **wprowadzający** . Przed opublikowaniem urządzenia należy przesłać Przewodnik jako dokument PDF do zatwierdzenia przez administratora systemu.
- Szczegóły marketingu. Podaj informacje marketingowe przeznaczone dla klientów dla Twojego urządzenia. Informacje marketingowe obejmują opis, fotografię i dystrybutorów.
- Dodatkowe certyfikaty branżowe. Ta opcjonalna sekcja umożliwia podanie dodatkowych informacji o innych certyfikatach, które zostały odebrane przez urządzenie.

### <a name="connect-and-test"></a>Połącz i Testuj

Krok Połącz i Testuj sprawdza, czy urządzenie spełnia wymagania certyfikacji Plug and Play IoT.

Należy wykonać trzy czynności:

1. Łączenie i odnajdowanie interfejsów. Urządzenie musi nawiązać połączenie z usługą certyfikacji Azure IoT za pomocą usługi DPS. Wybierz metodę uwierzytelniania (certyfikat X. 509, klucze symetryczne lub moduł zaufanej platformy), aby użyć i zaktualizować aplikację urządzenia za pomocą informacji DPS.
1. Przejrzyj interfejsy. Sprawdź interfejs i upewnij się, że każdy z nich ma dane wejściowe ładunku, które mają sens dla testowania.
1. Badan. System sprawdza każdy model urządzenia, aby sprawdzić, czy dane telemetryczne, właściwości i polecenia opisane w modelu są zgodne z konwencjami Plug and Play IoT. Po zakończeniu testu wybierz łącze **Wyświetl dzienniki** , aby wyświetlić dane telemetryczne z urządzenia, a następnie nieprzetworzonych danych wysyłanych do IoT Hub właściwości bliźniaczych urządzeń.

### <a name="submit-and-publish"></a>Prześlij i Opublikuj

Ostatnim wymaganym etapem jest przesłanie projektu do przeglądu. Ten krok powiadamia członka zespołu urządzenia z certyfikatem platformy Azure o kompletności, w tym informacje dotyczące urządzenia i marketingu oraz Przewodnik wprowadzający. Członek zespołu może skontaktować się z Tobą w adresie e-mail firmy dostarczony wcześniej z pytaniami lub edytować żądania przed zatwierdzeniem.

Jeśli urządzenie wymaga dalszej weryfikacji ręcznej w ramach certyfikacji, w tej chwili otrzymasz powiadomienie.

Gdy urządzenie jest certyfikowane, można opublikować szczegóły produktu w wykazie certyfikowanych urządzeń platformy Azure przy użyciu funkcji **Publikuj w katalogu** na stronie Podsumowanie produktu.

## <a name="next-steps"></a>Następne kroki

Po zakończeniu tego procesu można skontaktować się z zespołem certyfikacji urządzenia w [iotcert@microsoft.com](mailto:iotcert@microsoft.com) usłudze, aby przejść do następnych kroków, które obejmują Microsoft Partner Network weryfikację członkostwa i przegląd przewodników wprowadzających. Jeśli spełnione są wszystkie wymagania, możesz zdecydować, aby urządzenie było dołączone do [wykazu urządzeń z certyfikatem usługi Azure IoT](https://aka.ms/devicecatalog).
