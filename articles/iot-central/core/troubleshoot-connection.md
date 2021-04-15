---
title: Rozwiązywanie problemów z połączeniami urządzeń z Azure IoT Central | Microsoft Docs
description: Rozwiązywanie problemów z tym, dlaczego nie widzisz danych z urządzeń w IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: 494608f9dd8fbf986dcda6eeb782a64f6a2ca008
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378571"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Rozwiązywanie problemów dotyczących niewyświetlania danych z urządzeń w usłudze Azure IoT Central

Ten dokument pomaga deweloperom urządzeń dowiedzieć się, dlaczego dane, które ich urządzenia wysyłają do IoT Central nie są wyświetlane w aplikacji.

Istnieją dwa główne obszary do zbadania:

- Problemy z łącznością urządzeń
  - Problemy z uwierzytelnianiem, takie jak nieprawidłowe poświadczenia urządzenia
  - Problemy z łącznością sieciową
  - Urządzenie nie zostało zatwierdzone lub zablokowane
- Problemy z kształtem ładunku urządzenia

Ten przewodnik rozwiązywania problemów koncentruje się na problemach z łącznością urządzeń i problemach z kształtem ładunku urządzenia.

## <a name="device-connectivity-issues"></a>Problemy z łącznością urządzeń

Ta sekcja pomaga określić, czy dane docierają do IoT Central.

Jeśli jeszcze tego nie zrobiono, zainstaluj `az cli` narzędzie i `azure-iot` rozszerzenie.

Aby dowiedzieć się, jak zainstalować `az cli` program , zobacz Instalowanie [interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

Aby [zainstalować](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation) `azure-iot` rozszerzenie, uruchom następujące polecenie:

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> Przy pierwszym uruchomieniu polecenia rozszerzenia może zostać wyświetlony monit o `uamqp` zainstalowanie biblioteki.

Po zainstalowaniu rozszerzenia uruchom urządzenie, aby sprawdzić, czy wysyłane przez niego komunikaty nie są w stanie `azure-iot` IoT Central.

Użyj następujących poleceń, aby zalogować się do subskrypcji, w której masz IoT Central aplikację:

```azurecli
az login
az set account --subscription <your-subscription-id>
```

Aby monitorować dane telemetryczne, które wysyła urządzenie, użyj następującego polecenia:

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Jeśli urządzenie zostało pomyślnie połączone z IoT Central, zobaczysz dane wyjściowe podobne do następujących:

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

Aby monitorować aktualizacje właściwości, które urządzenie wymienia z IoT Central, użyj następującego polecenia w wersji zapoznawczej:

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

Jeśli w terminalu pojawią się dane, dane będą się one pojawiać nawet do twojej IoT Central aplikacji.

Jeśli po kilku minutach nie widać żadnych danych, spróbuj nacisnąć klawisz lub na klawiaturze, jeśli dane wyjściowe `Enter` `return` są zablokowane.

Jeśli nadal nie widzisz żadnych danych w terminalu, prawdopodobnie urządzenie ma problemy z łącznością sieciową lub nie wysyła danych poprawnie do IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>Sprawdzanie stanu aprowizowania urządzenia

Jeśli dane nie są wyświetlane na monitorze, sprawdź stan aprowowania urządzenia, uruchamiając następujące polecenie:

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

Następujące dane wyjściowe pokazują przykład urządzenia, które ma zablokowaną dostęp do połączenia:

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

| Stan aprowowania urządzenia | Opis | Możliwe środki zaradcze |
| - | - | - |
| Zaaprowizowane | Nie ma natychmiast rozpoznawalnego problemu. | Nie dotyczy |
| Zarejestrowany | Urządzenie nie zostało jeszcze połączone z IoT Central. | Sprawdź dzienniki urządzeń, aby uzyskać informacje o problemach z łącznością. |
| Zablokowane | Urządzenie nie może nawiązywać połączenia z IoT Central. | Urządzenie nie może nawiązywać połączenia z IoT Central aplikacji. Odblokuj urządzenie na stronie IoT Central ponów próbę. Aby dowiedzieć się więcej, zobacz [Block devices (Blokowanie urządzeń).](concepts-get-connected.md#device-status-values) |
| Niezatwierdzone | Urządzenie nie zostało zatwierdzone. | Urządzenie nie jest zatwierdzone do łączenia się z IoT Central aplikacji. Zatwierdź urządzenie w IoT Central i ponów próbę. Aby dowiedzieć się więcej, zobacz [Zatwierdzanie urządzeń](concepts-get-connected.md#device-registration) |
| Nieskojarzona | Urządzenie nie jest skojarzone z szablonem urządzenia. | Skojarz urządzenie z szablonem urządzenia, aby IoT Central sposób analizowania danych. |

Dowiedz się więcej o [kodach stanu urządzeń.](concepts-get-connected.md#device-status-values)

### <a name="error-codes"></a>Kody błędów

Jeśli nadal nie możesz zdiagnozować, dlaczego dane nie są wyświetlane w u ciebie , następnym krokiem jest poszukanie kodów błędów zgłaszanych `monitor-events` przez urządzenie.

Rozpocznij sesję debugowania na urządzeniu lub zbierz dzienniki z urządzenia. Sprawdź kody błędów zgłaszane przez urządzenie.

W poniższych tabelach przedstawiono typowe kody błędów i możliwe akcje do ograniczenia.

Jeśli widzisz problemy związane z przepływem uwierzytelniania:

| Kod błędu | Opis | Możliwe środki zaradcze |
| - | - | - |
| 400 | Treść żądania jest nieprawidłowy. Na przykład nie można go waliduje lub nie można zweryfikować obiektu. | Upewnij się, że wysyłasz prawidłową treść żądania w ramach przepływu zaświadczenia lub użyj zestawu SDK urządzenia. |
| 401 | Nie można zweryfikować tokenu autoryzacji. Na przykład wygasł lub nie ma zastosowania do URI żądania. Ten kod błędu jest również zwracany do urządzeń w ramach przepływu zaświadczenia modułu TPM. | Upewnij się, że urządzenie ma poprawne poświadczenia. |
| 404 | Wystąpienie usługi Device Provisioning Service lub zasób, taki jak rejestracja, nie istnieje. | [Zamieścisz bilet pomocy technicznej dla klientów.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) |
| 412 | W `ETag` żądaniu nie jest zgodne z istniejącym zasobem, `ETag` jak w dokumencie RFC7232. | [Zamieścisz bilet pomocy technicznej dla klientów.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) |
| 429 | Operacje są ograniczane przez usługę. Aby uzyskać informacje o limitach określonych usług, [zobacz IoT Hub Device Provisioning Service limity usługi](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | Zmniejsz częstotliwość komunikatów, dzieląc obowiązki między więcej urządzeń. |
| 500 | Wystąpił błąd wewnętrzny. | [W celu uzyskania pomocy technicznej możesz](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) utworzyć bilet pomocy technicznej. |

### <a name="file-upload-error-codes"></a>Kody błędów przekazywania plików

Poniżej znajduje się lista typowych kodów błędów, które można zobaczyć, gdy urządzenie próbuje przekazać plik do chmury. Pamiętaj, że aby urządzenie może przekazać plik, musisz skonfigurować przekazywanie [plików](howto-configure-file-uploads.md) urządzenia w aplikacji.

| Kod błędu | Opis | Możliwe środki zaradcze |
| - | - | - |
| 403006  | Przekroczono liczbę współbieżnych operacji przekazywania plików. Każdy klient urządzenia jest ograniczony do 10 współbieżnych przekazywania plików. | Upewnij się, że urządzenie niezwłocznie IoT Central, że operacja przekazywania pliku została zakończona. Jeśli to nie zadziała, spróbuj zmniejszyć limit czasu żądania. |

## <a name="payload-shape-issues"></a>Problemy z kształtem ładunku

Po ujednaniu, że urządzenie wysyła dane do IoT Central, następnym krokiem jest upewninie się, że urządzenie wysyła dane w prawidłowym formacie.

Istnieją dwie główne kategorie typowych problemów, które powodują, że dane urządzenia nie są wyświetlane w IoT Central:

- Niezgodność danych szablonu urządzenia z urządzeniem:
  - Niezgodność nazw, takich jak literówki lub problemy z dopasowywaniem literówek.
  - Właściwości niemodelowane, w których schemat nie jest zdefiniowany w szablonie urządzenia.
  - Niezgodność schematu, taka jak typ zdefiniowany w szablonie jako `boolean` , ale dane są ciągiem.
  - Ta sama nazwa telemetrii jest zdefiniowana w wielu interfejsach, ale urządzenie nie jest IoT Plug and Play zgodne.
- Kształt danych jest nieprawidłowy w postaci JSON. Aby dowiedzieć się więcej, zobacz [Telemetria, właściwości i ładunki poleceń](concepts-telemetry-properties-commands.md).

Aby wykryć kategorie, w których występuje problem, uruchom najbardziej odpowiednie polecenie dla swojego scenariusza:

- Aby zweryfikować telemetrię, użyj polecenia w wersji zapoznawczej:

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Aby zweryfikować aktualizacje właściwości, użyj polecenia w wersji zapoznawczej

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

Przy pierwszym uruchomieniu polecenia może zostać wyświetlony monit o `uamqp` zainstalowanie `validate` biblioteki.

Następujące dane wyjściowe pokazują przykładowe komunikaty o błędach i ostrzeżeniach z polecenia validate:

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Jeśli wolisz używać graficznego interfejsu użytkownika, użyj IoT Central **danych** pierwotnych, aby sprawdzić, czy coś nie jest modelowane. Widok **Nieprzetworzone** dane nie wykrywa, czy urządzenie wysyła źle sformułowany dane JSON.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Zrzut ekranu przedstawiający widok danych pierwotnych":::

Po wykryciu problemu może być konieczne zaktualizowanie oprogramowania układowego urządzenia lub utworzenie nowego szablonu urządzenia, który będzie modelował wcześniej niemodelowane dane.

Jeśli wybrano opcję utworzenia nowego szablonu, który poprawnie modeluje dane, przemigruj urządzenia ze starego szablonu do nowego szablonu. Aby dowiedzieć się więcej, zobacz [Manage devices in your Azure IoT Central application (Zarządzanie](howto-manage-devices.md)urządzeniami w Azure IoT Central aplikacji).

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy, możesz skontaktować się z ekspertami w dziedzinie platformy Azure na platformie Azure w witrynie [MSDN Stack Overflow forach.](https://azure.microsoft.com/support/community/) Alternatywnie możesz utworzyć bilet pomocy technicznej [platformy Azure.](https://portal.azure.com/#create/Microsoft.Support)

Aby uzyskać więcej informacji, zobacz Opcje pomocy [i obsługi usługi Azure IoT.](../../iot-fundamentals/iot-support-help.md)
