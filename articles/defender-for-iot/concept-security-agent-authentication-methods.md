---
title: Metody uwierzytelniania agenta zabezpieczeń
description: Poznaj różne metody uwierzytelniania dostępne w przypadku korzystania z usługi Defender for IoT.
ms.topic: conceptual
ms.date: 01/24/2021
ms.openlocfilehash: bd4d89d8664dae2421ff7f268b3710b5a906427b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784615"
---
# <a name="security-agent-authentication-methods"></a>Metody uwierzytelniania agenta zabezpieczeń

W tym artykule opisano różne metody uwierzytelniania, których można użyć z agentem AzureIoTSecurity do uwierzytelniania za pomocą IoT Hub.

Dla każdego urządzenia dołączonego do usługi Defender for IoT w IoT Hub wymagana jest usługa Defender-IoT-Micro-Agent. Aby można było uwierzytelnić urządzenie, usługa Defender for IoT może korzystać z jednej z dwóch metod. Wybierz metodę, która najlepiej sprawdza się w przypadku istniejącego rozwiązania IoT.

- SecurityModule — opcja
- Opcja urządzenia

## <a name="authentication-methods"></a>Metody uwierzytelniania

Dwie metody dla agenta AzureIoTSecurity usługi Defender for IoT służące do przeprowadzania uwierzytelniania:

- Usługa **Defender-IoT-Micro-Agent** Authentication Mode<br>
Agent jest uwierzytelniany przy użyciu tożsamości usługi Defender-IoT-Micro-Agent niezależnie od tożsamości urządzenia.
Tego typu uwierzytelniania należy użyć, jeśli chcesz, aby Agent zabezpieczeń używał dedykowanej metody uwierzytelniania za pomocą protokołu Defender-IoT-Micro-Agent (tylko klucz symetryczny).

- Tryb uwierzytelniania **urządzenia**<br>
W tej metodzie Agent zabezpieczeń najpierw uwierzytelnia się przy użyciu tożsamości urządzenia. Po początkowym uwierzytelnianiu Agent usługi Defender for IoT wykonuje wywołanie **rest** do IoT Hub przy użyciu interfejsu API REST z danymi uwierzytelniania urządzenia. Agent Defender for IoT następnie żąda metody uwierzytelniania Defender-IoT-Micro-Agent i danych z IoT Hub. W ostatnim kroku Agent usługi Defender for IoT wykonuje uwierzytelnianie względem modułu Defender for IoT.

Użyj tego typu uwierzytelniania, jeśli chcesz, aby Agent zabezpieczeń ponownie używał istniejącej metody uwierzytelniania urządzenia (certyfikat z podpisem własnym lub klucz symetryczny).

Zobacz [parametry instalacji agenta zabezpieczeń](#security-agent-installation-parameters) , aby dowiedzieć się, jak skonfigurować.

## <a name="authentication-methods-known-limitations"></a>Znane ograniczenia metod uwierzytelniania

- Tryb uwierzytelniania **SecurityModule** obsługuje tylko uwierzytelnianie klucza symetrycznego.
- Certyfikat CA-Signed nie jest obsługiwany przez tryb uwierzytelniania **urządzenia** .

## <a name="security-agent-installation-parameters"></a>Parametry instalacji agenta zabezpieczeń

W przypadku [wdrażania agenta zabezpieczeń](how-to-deploy-agent.md)należy podać szczegóły uwierzytelniania jako argumenty.
Te argumenty są udokumentowane w poniższej tabeli.

|Nazwa parametru systemu Linux | Nazwa parametru systemu Windows | Parametr skrócony |Opis|Opcje|
|---------------------|---------------|---------|---------------|---------------|
|Uwierzytelnianie — tożsamość|AuthenticationIdentity|aui|Tożsamość uwierzytelniania| **SecurityModule** lub **urządzenie**|
|Uwierzytelnianie — Metoda|AuthenticationMethod|aum|Metoda uwierzytelniania|**SymmetricKey** lub **SelfSignedCertificate**|
|ścieżka pliku|Parametr|f|Bezwzględna pełna ścieżka pliku zawierającego certyfikat lub klucz symetryczny| |
|Nazwa hosta|HostName|HN|Nazwa FQDN IoT Hub|Przykład: ContosoIotHub.azure-devices.net|
|Identyfikator urządzenia|DeviceId|fosforan|Identyfikator urządzenia|Przykład: MyDevice1|
|Typ lokalizacji certyfikatu|CertificateLocationKind|CL|Lokalizacja przechowywania certyfikatów|**Plik_lokalny** lub **Store**|
|

W przypadku korzystania ze skryptu Zainstaluj agenta zabezpieczeń następująca konfiguracja jest wykonywana automatycznie. Aby ręcznie edytować uwierzytelnianie agenta zabezpieczeń, edytuj plik konfiguracji.

## <a name="change-authentication-method-after-deployment"></a>Zmień metodę uwierzytelniania po wdrożeniu

Podczas wdrażania agenta zabezpieczeń przy użyciu skryptu instalacji automatycznie tworzony jest plik konfiguracji.

Aby zmienić metody uwierzytelniania po wdrożeniu, wymagane jest ręczne edytowanie pliku konfiguracji.

### <a name="c-based-security-agent"></a>Agent zabezpieczeń oparty na języku C#

Edytuj _Authentication.config_ przy użyciu następujących parametrów:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>Agent zabezpieczeń oparty na języku C

Edytuj _LocalConfiguration.js_ przy użyciu następujących parametrów:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Zobacz też

- [Omówienie agentów zabezpieczeń](security-agent-architecture.md)
- [Wdróż agenta zabezpieczeń](how-to-deploy-agent.md)
- [Dostęp do nieprzetworzonych danych zabezpieczeń](how-to-security-data-access.md)
