---
title: Rozwiązywanie problemów z połączeniami urządzeń z platformą Azure IoT Central | Microsoft Docs
description: Rozwiązywanie problemów z tym, dlaczego nie widzisz danych z urządzeń w IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: d1a7c94152b611ea0dbea249156add617178d7ca
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673238"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Rozwiązywanie problemów dotyczących niewyświetlania danych z urządzeń w usłudze Azure IoT Central

Ten dokument ułatwia deweloperom urządzeń Dowiedz się, dlaczego dane wysyłane przez urządzenia do IoT Central mogą nie być wyświetlane w aplikacji.

Istnieją dwa główne obszary do zbadania:

- Problemy z łącznością urządzeń
  - Problemy z uwierzytelnianiem, takie jak urządzenie, ma nieprawidłowe poświadczenia
  - Problemy z łącznością sieciową
  - Urządzenie nie jest zatwierdzone lub zablokowane
- Problemy z kształtem ładunku urządzenia

Ten przewodnik rozwiązywania problemów koncentruje się na problemach z łącznością urządzeń i rozwiązaniu problemów z kształtem ładunku

## <a name="device-connectivity-issues"></a>Problemy z łącznością urządzeń

W tej sekcji można określić, czy dane docierają do IoT Central.

Jeśli jeszcze tego nie zrobiono, zainstaluj `az cli` Narzędzie i `azure-iot` rozszerzenie.

Aby dowiedzieć się, jak zainstalować `az cli` program, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Aby [zainstalować](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation) `azure-iot` rozszerzenie, uruchom następujące polecenie:

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> Może zostać wyświetlony monit o zainstalowanie `uamqp` biblioteki przy pierwszym uruchomieniu polecenia rozszerzenia.

Po zainstalowaniu `azure-iot` rozszerzenia Uruchom urządzenie, aby sprawdzić, czy wysyłane przez niego wiadomości są w sposób IoT Central.

Użyj następujących poleceń, aby zalogować się do subskrypcji, w której masz aplikację IoT Central:

```azurecli
az login
az set account --subscription <your-subscription-id>
```

Aby monitorować dane telemetryczne wysyłane przez urządzenie, użyj następującego polecenia:

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Jeśli urządzenie zostało pomyślnie podłączone do IoT Central, zobaczysz dane wyjściowe podobne do następujących:

```output
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

Aby monitorować aktualizacje właściwości, urządzenie jest wymieniane z IoT Central, użyj następującego polecenia w wersji zapoznawczej:

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Jeśli urządzenie pomyślnie wyśle aktualizacje właściwości, zobaczysz dane wyjściowe podobne do następujących:

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Jeśli zobaczysz, że dane są wyświetlane w terminalu, dane są tak samo, jak w przypadku aplikacji IoT Central.

Jeśli nie widzisz żadnych danych po kilku minutach, spróbuj nacisnąć klawisz `Enter` lub `return` na klawiaturze, w przypadku gdy dane wyjściowe są zablokowane.

Jeśli nadal nie widzisz żadnych danych w terminalu, prawdopodobnie urządzenie ma problemy z łącznością sieciową lub nie wysyła prawidłowo danych do IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>Sprawdź stan aprowizacji urządzenia

Jeśli dane nie są wyświetlane na monitorze, sprawdź stan aprowizacji urządzenia, uruchamiając następujące polecenie:

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

Poniższe dane wyjściowe przedstawiają przykład urządzenia, które jest blokowane przed połączeniem:

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| Stan aprowizacji urządzeń | Opis | Możliwe środki zaradcze |
| - | - | - |
| Zaaprowizowane | Brak natychmiastowego rozpoznawania problemu. | Nie dotyczy |
| Zarejestrowany | Urządzenie nie zostało jeszcze podłączone do IoT Central. | Sprawdź dzienniki urządzeń pod kątem problemów z łącznością. |
| Zablokowane | Urządzenie ma zablokowany dostęp do IoT Central. | Urządzenie ma zablokowany dostęp do aplikacji IoT Central. Odblokuj urządzenie w IoT Central i ponów próbę. Aby dowiedzieć się więcej, zobacz [blokowanie urządzeń](concepts-get-connected.md#device-status-values). |
| Niezatwierdzonych | Urządzenie nie jest zatwierdzone. | Urządzenie nie jest zatwierdzone do nawiązania połączenia z aplikacją IoT Central. Zatwierdź urządzenie w IoT Central i ponów próbę. Aby dowiedzieć się więcej, zobacz [zatwierdzanie urządzeń](concepts-get-connected.md#device-registration) |
| Nieskojarzone | Urządzenie nie jest skojarzone z szablonem urządzenia. | Skojarz urządzenie z szablonem urządzenia, aby IoT Central wie, jak analizować dane. |

Dowiedz się więcej na temat [kodów stanu urządzeń](concepts-get-connected.md#device-status-values).

### <a name="error-codes"></a>Kody błędów

Jeśli nadal nie można zdiagnozować, dlaczego dane nie są wyświetlane w programie `monitor-events` , następnym krokiem jest wyszukiwanie kodów błędów raportowanych przez urządzenie.

Rozpocznij sesję debugowania na urządzeniu lub Zbierz dzienniki z urządzenia. Sprawdź kody błędów, które są zgłaszane przez urządzenie.

W poniższych tabelach przedstawiono typowe kody błędów i możliwe akcje, które należy wyeliminować.

Jeśli widzisz problemy związane z przepływem uwierzytelniania:

| Kod błędu | Opis | Możliwe środki zaradcze |
| - | - | - |
| 400 | Treść żądania jest nieprawidłowa. Na przykład nie można jej przeanalizować lub nie można sprawdzić poprawności obiektu. | Upewnij się, że wysyłasz poprawną treść żądania w ramach przepływu zaświadczania lub użyj zestawu SDK urządzeń. |
| 401 | Nie można zweryfikować tokenu autoryzacji. Na przykład wygasła lub nie dotyczy identyfikatora URI żądania. Ten kod błędu jest również zwracany do urządzeń w ramach przepływu zaświadczania modułu TPM. | Upewnij się, że urządzenie ma poprawne poświadczenia. |
| 404 | Wystąpienie usługi Device Provisioning lub zasób, taki jak rejestracja, nie istnieje. | Wykorzystaj [bilet z obsługą klienta](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 412 | `ETag`W żądaniu nie jest zgodny z `ETag` istniejącym zasobem, zgodnie z RFC7232. | Wykorzystaj [bilet z obsługą klienta](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 429 | Operacje są ograniczane przez usługę. Aby uzyskać szczegółowe limity usługi, zobacz [limity IoT Hub Device Provisioning Service](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | Zmniejsz częstotliwość komunikatów, Podziel odpowiedzialności między więcej urządzeń. |
| 500 | Wystąpił błąd wewnętrzny. | Zapoznaj się [z biletem z obsługą klienta](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , aby sprawdzić, czy może Ci pomóc. |

## <a name="payload-shape-issues"></a>Problemy z kształtem ładunku

Po ustaleniu, że urządzenie wysyła dane do IoT Central, następnym krokiem jest upewnienie się, że urządzenie wysyła dane w prawidłowym formacie.

Istnieją dwie główne kategorie typowych problemów, które powodują, że dane urządzenia nie są wyświetlane w IoT Central:

- Niezgodność szablonu urządzenia z danymi urządzenia:
  - Niezgodność nazw, takich jak literówki lub problemy z dopasowywaniem wielkości liter.
  - Właściwości niemodelowane, w których schemat nie jest zdefiniowany w szablonie urządzenia.
  - Niezgodność schematu, taka jak typ zdefiniowany w szablonie `boolean` , ale dane są ciągami.
  - Ta sama nazwa telemetrii jest zdefiniowana w wielu interfejsach, ale urządzenie nie jest zgodne z Plug and Play IoT.
- Kształt danych jest nieprawidłowy w formacie JSON. Aby dowiedzieć się więcej, zobacz dane [telemetryczne, właściwości i poleceń](concepts-telemetry-properties-commands.md).

Aby wykryć, których kategorii dotyczy problem, uruchom najbardziej odpowiednie polecenie dla danego scenariusza:

- Aby sprawdzić poprawność telemetrii, użyj polecenia podglądu:

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Aby sprawdzić poprawność aktualizacji właściwości, użyj polecenia podglądu

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

Może zostać wyświetlony monit o zainstalowanie `uamqp` biblioteki podczas pierwszego uruchomienia `validate` polecenia.

Następujące dane wyjściowe pokazują przykład błędu i komunikaty ostrzegawcze z polecenia Validate:

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Jeśli wolisz używać graficznego interfejsu użytkownika, użyj widoku **nieprzetworzonych danych** IoT Central, aby sprawdzić, czy coś nie jest modelowane. Widok **nieprzetworzonych danych** nie wykrywa, czy urządzenie wysyła źle sformułowany kod JSON.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Zrzut ekranu przedstawiający widok danych nieprzetworzonych":::

Po wykryciu problemu może być konieczne zaktualizowanie oprogramowania układowego urządzenia lub utworzenie nowego szablonu urządzenia, który modeluje poprzednio niemodelowane dane.

W przypadku wybrania opcji utworzenia nowego szablonu, który poprawnie modeluje dane, Migruj urządzenia ze starego szablonu do nowego szablonu. Aby dowiedzieć się więcej, zobacz [Zarządzanie urządzeniami w aplikacji IoT Central platformy Azure](howto-manage-devices.md).

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie możesz zaplikować [bilet pomocy technicznej systemu Azure](https://portal.azure.com/#create/Microsoft.Support).

Aby uzyskać więcej informacji, zobacz temat pomoc [techniczna i opcje pomocy usługi Azure IoT](../../iot-fundamentals/iot-support-help.md).
