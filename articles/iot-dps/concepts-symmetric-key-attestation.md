---
title: Azure IoT Hub Device Provisioning Service — zaświadczenie klucza symetrycznego
description: Ten artykuł zawiera omówienie pojęć związanych z zaświadczeniem klucza symetrycznego przy użyciu usługi IoT Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: 994c2c3124d6822f047af942268ad7a401d5a976
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90531563"
---
# <a name="symmetric-key-attestation"></a>Zaświadczanie klucza symetrycznego

W tym artykule opisano proces zaświadczania tożsamości w przypadku używania kluczy symetrycznych z usługą Device Provisioning. 

Zaświadczenie klucza symetrycznego to proste podejście do uwierzytelniania urządzenia za pomocą wystąpienia usługi Device Provisioning. Ta metoda zaświadczania reprezentuje środowisko "Hello World" dla deweloperów, którzy są nowym sposobem aprowizacji urządzeń lub nie mają rygorystycznych wymagań dotyczących zabezpieczeń. Zaświadczenie urządzenia przy użyciu [modułu TPM](concepts-tpm-attestation.md) lub [certyfikatu X. 509](concepts-x509-attestation.md) jest bezpieczniejsze i powinno być używane do bardziej rygorystycznych wymagań w zakresie bezpieczeństwa.

Rejestracje klucza symetrycznego zapewniają również doskonały sposób na starszych urządzeniach z ograniczoną funkcjonalnością zabezpieczeń do ładowania początkowego do chmury za pośrednictwem usługi Azure IoT. Aby uzyskać więcej informacji na temat zaświadczania klucza symetrycznego ze starszymi urządzeniami, zobacz [jak używać kluczy symetrycznych ze starszymi urządzeniami](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Tworzenie klucza symetrycznego

Domyślnie usługa Device Provisioning tworzy nowe klucze symetryczne z domyślną długością 64 bajtów w przypadku zapisania nowych rejestracji z włączoną opcją **automatycznego generowania kluczy** .

![Automatycznie Generuj klucze symetryczne](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Można również udostępnić klucze symetryczne do rejestracji, wyłączając tę opcję. Podczas określania własnych kluczy symetrycznych klucze muszą mieć długość klucza wynoszącą od 16 bajtów do 64 bajtów. Ponadto klucze symetryczne muszą być podane w prawidłowym formacie base64.



## <a name="detailed-attestation-process"></a>Szczegółowy proces zaświadczania

Zaświadczenie klucza symetrycznego za pomocą usługi Device Provisioning jest realizowane przy użyciu tych samych [tokenów zabezpieczających](../iot-hub/iot-hub-devguide-security.md#security-token-structure) , które są obsługiwane przez centra IoT do identyfikowania urządzeń. Te tokeny zabezpieczające są [tokenami sygnatury dostępu współdzielonego (SAS)](../service-bus-messaging/service-bus-sas.md). 

Tokeny sygnatury dostępu współdzielonego mają *sygnaturę* skrótu, która jest tworzona przy użyciu klucza symetrycznego. Sygnatura jest tworzona ponownie przez usługę Device Provisioning w celu sprawdzenia, czy token zabezpieczający przedstawiony podczas zaświadczania jest autentyczny.

Tokeny sygnatury dostępu współdzielonego mają następującą formę:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Poniżej przedstawiono składniki każdego tokenu:

| Wartość | Opis |
| --- | --- |
| podpisane |Ciąg sygnatury HMAC-SHA256. W przypadku indywidualnych rejestracji Ta sygnatura jest generowana przy użyciu klucza symetrycznego (podstawowego lub pomocniczego) do wykonania skrótu. W przypadku grup rejestracji do wykonania skrótu jest używany klucz pochodzący z klucza grupy rejestracji. Skrót jest wykonywany w postaci komunikatu: `URL-encoded-resourceURI + "\n" + expiry` . **Ważne**: klucz należy zdekodować z formatu Base64 przed użyciem, aby wykonać obliczenia HMAC-SHA256. Ponadto wynik podpisu musi być zakodowany w adresie URL. |
| ResourceURI |Identyfikator URI punktu końcowego rejestracji, do którego można uzyskać dostęp za pomocą tego tokenu, rozpoczynając od identyfikatora zakresu dla wystąpienia usługi Device Provisioning. Na przykład `{Scope ID}/registrations/{Registration ID}` |
| wygaśnięcia |Ciągi UTF8 przez liczbę sekund od czasu epoki 00:00:00 UTC 1 stycznia 1970. |
| {Zakodowany w adresie URL — resourceURI} |Małe adresy URL — kodowanie identyfikatora URI zasobu niższej wielkości liter |
| PolicyName |Nazwa zasad dostępu współdzielonego, do których odwołuje się ten token. Nazwa zasad używana podczas aprowizacji z zaświadczeniem klucza symetrycznego jest **zarejestrowani**. |

Gdy urządzenie jest zaświadczania rejestracji indywidualnej, urządzenie używa klucza symetrycznego zdefiniowanego w wpisie rejestracji indywidualnej w celu utworzenia skrótu sygnatury dla tokenu sygnatury dostępu współdzielonego.

Przykłady kodu, które tworzą token SAS, znajdują się w temacie [tokeny zabezpieczające](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Tworzenie tokenów zabezpieczających dla zaświadczania klucza symetrycznego jest obsługiwane przez zestaw SDK języka C dla usługi Azure IoT. Aby zapoznać się z przykładem za pomocą zestawu SDK usługi Azure IoT C do zaświadczania rejestracji indywidualnej, zobacz temat [udostępnianie urządzenia symulowanego za pomocą kluczy symetrycznych](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Rejestracje grup

Klucze symetryczne dla rejestracji grup nie są używane bezpośrednio przez urządzenia podczas aprowizacji. Zamiast tego urządzenia należące do grupy rejestracji są inicjowane przy użyciu klucza urządzenia pochodnego. 

Najpierw unikatowy identyfikator rejestracji jest definiowany dla każdego urządzenia zaświadczania do grupy rejestracji. Prawidłowe znaki identyfikatora rejestracji to małe litery alfanumeryczne i myślnik ("-"). Ten identyfikator rejestracji powinien być unikatowy, który identyfikuje urządzenie. Na przykład starsze urządzenie może nie obsługiwać wielu funkcji zabezpieczeń. Na starszej urządzeniu może być dostępny tylko adres MAC lub numer seryjny, aby można było jednoznacznie zidentyfikować to urządzenie. W takim przypadku Identyfikator rejestracji może składać się z adresu MAC i numeru seryjnego podobnego do poniższego:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Ten dokładny przykład jest używany w artykule [jak udostępnić starsze urządzenia przy użyciu kluczy symetrycznych](how-to-legacy-device-symm-key.md) .

Po zdefiniowaniu identyfikatora rejestracji dla urządzenia klucz symetryczny dla grupy rejestracji jest używany do obliczania skrótu [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) identyfikatora rejestracji w celu utworzenia pochodnego klucza urządzenia. Skrót identyfikatora rejestracji można wykonać przy użyciu następującego kodu w języku C#:

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Otrzymany klucz urządzenia jest następnie używany do generowania tokenu sygnatury dostępu współdzielonego, który będzie używany na potrzeby zaświadczania. Każde urządzenie w grupie rejestracji jest wymagane do zaświadczania przy użyciu tokenu zabezpieczającego wygenerowanego na podstawie unikatowego klucza pochodnego. Klucza symetrycznego grupy rejestracji nie można używać bezpośrednio na potrzeby zaświadczania.

#### <a name="installation-of-the-derived-device-key"></a>Instalacja pochodnego klucza urządzenia

Najlepiej, gdy klucze urządzeń są wyprowadzane i instalowane w fabryce. Ta metoda gwarantuje, że klucz grupy nigdy nie jest uwzględniony w oprogramowaniu wdrożonym na urządzeniu. Gdy urządzenie ma przypisany adres MAC lub numer seryjny, klucz może być wyprowadzany i wprowadzany do urządzenia, ale producent zdecyduje się na jego przechowanie.

Rozważmy poniższy diagram, który pokazuje tabelę kluczy urządzeń generowanych w fabryce przez Podmieszanie poszczególnych identyfikatorów rejestracji urządzeń z kluczem rejestracji grupy (**K**). 

![Klucze urządzeń przypisane z fabryki](./media/concepts-symmetric-key-attestation/key-diversification.png)

Tożsamość każdego urządzenia jest reprezentowana przez identyfikator rejestracji i pochodny klucz urządzenia, który jest instalowany w fabryce. Klucz urządzenia nigdy nie jest kopiowany do innej lokalizacji, a klucz grupy nigdy nie jest przechowywany na urządzeniu.

Jeśli klucze urządzeń nie są zainstalowane w fabryce, w celu bezpiecznego przechowywania tożsamości urządzenia należy używać [sprzętowego modułu HSM](concepts-service.md#hardware-security-module) .

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już zaświadczenie klucza symetrycznego, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej:

* [Szybki start: aprowizowanie urządzenia symulowanego przy użyciu kluczy symetrycznych](quick-create-simulated-device-symm-key.md)
* [Informacje o pojęciach dotyczących aprowizacji](about-iot-dps.md#provisioning-process)
* [Wprowadzenie do korzystania z funkcji autoaprowizacji](./quick-setup-auto-provision.md) 
