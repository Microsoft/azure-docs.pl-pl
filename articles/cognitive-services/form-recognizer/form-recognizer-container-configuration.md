---
title: Jak skonfigurować kontener dla aparatu rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować kontener aparatu rozpoznawania formularzy, aby analizować dane formularzy i tabel.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: 324b70fc810acc4faba4f488f821049f7eb0875e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "86538007"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurowanie kontenerów aparatów rozpoznawania formularzy

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Korzystając z kontenerów usługi Azure form, można utworzyć architekturę aplikacji, która jest zoptymalizowana pod kątem korzystania z zarówno niezawodnej możliwości chmury, jak i lokalizacji brzegowej.

Można skonfigurować środowisko uruchomieniowe kontenera aparatu rozpoznawania formularzy przy użyciu `docker run` argumentów polecenia. Ten kontener ma kilka wymaganych ustawień i kilka opcjonalnych ustawień. Aby uzyskać kilka przykładów, zobacz sekcję ["Przykładowe polecenia uruchamiania platformy Docker"](#example-docker-run-commands) . Ustawienia dotyczące rozliczeń dotyczą tylko kontenera.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Ustawienia, [`Billing`](#billing-configuration-setting) i [`Eula`](#eula-setting) są używane razem. Należy podać prawidłowe wartości dla wszystkich trzech ustawień; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera przy użyciu tych ustawień konfiguracji, zobacz [rozliczenia](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

To `ApiKey` ustawienie określa klucz zasobów platformy Azure, który służy do śledzenia informacji rozliczeniowych dla kontenera. Wartość ApiKey musi być prawidłowym kluczem dla zasobu _aparatu rozpoznawania formularza_ , który jest określony `Billing` w sekcji "ustawienie konfiguracji rozliczeń".

To ustawienie można znaleźć w Azure Portal w obszarze **klucze** w obszarze **Zarządzanie zasobami aparatu rozpoznawania formularzy**.

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

`Billing`Ustawienie określa identyfikator URI punktu końcowego dla zasobu _aparatu rozpoznawania_ w systemie Azure, który służy do mierzenia informacji rozliczeniowych dla kontenera. Wartość tego ustawienia konfiguracji musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _aparatu rozpoznawania formularza_ na platformie Azure. Kontener zgłasza użycie co 10 do 15 minut.

To ustawienie można znaleźć w Azure Portal w sekcji **Omówienie aparatu rozpoznawania formularzy** w obszarze **punkt końcowy**.

|Wymagane| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Tak| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczenia. Aby uzyskać więcej informacji na temat uzyskiwania identyfikatora URI rozliczeń, zobacz [zbieranie wymaganych parametrów](form-recognizer-container-howto.md#gathering-required-parameters). Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Ustawienie umowy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia pozostały

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia logowania

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Ustawienia instalacji

Użyj instalacji powiązań, aby odczytywać i zapisywać dane w kontenerze i z niego. Można określić instalację wejściową lub instalację wyjściową, określając `--mount` opcję w [ `docker run` poleceniu](https://docs.docker.com/engine/reference/commandline/run/).

Kontener rozpoznawania formularzy wymaga instalacji wejściowej i instalacji wyjściowej. Instalacja wejściowa może być tylko do odczytu i jest wymagana w celu uzyskania dostępu do danych używanych do szkolenia i oceniania. Instalacja wyjściowa musi być zapisywalna i służy do przechowywania modeli i danych tymczasowych.

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera hosta](form-recognizer-container-howto.md#the-host-computer) może być niedostępna z powodu konfliktu między uprawnieniami konta usługi platformy Docker a uprawnieniami do lokalizacji instalacji hosta.

|Opcjonalne| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Wymagane| `Input` | Ciąg | Obiekt docelowy instalacji wejściowej. Wartość domyślna to `/input`.    <br><br>Przykład:<br>`--mount type=bind,src=c:\input,target=/input`|
|Wymagane| `Output` | Ciąg | Obiekt docelowy instalacji wyjściowej. Wartość domyślna to `/output`.  <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy Docker

W poniższych przykładach użyto ustawień konfiguracji, aby zilustrować sposób pisania i używania `docker run` poleceń. Gdy jest uruchomiona, kontener kontynuuje działanie, dopóki nie zostanie [zatrzymany](form-recognizer-container-howto.md#stop-the-container).

* **Znak kontynuacji wiersza**: polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego ( \\ ) jako znaku kontynuacji wiersza. Zastąp lub Usuń ten znak, w zależności od wymagań systemu operacyjnego hosta.
* **Kolejność** argumentów: nie zmieniaj kolejności argumentów, chyba że znasz kontenery Docker.

Zastąp {_argument_name_} w poniższej tabeli własnymi wartościami:

| Symbol zastępczy | Wartość |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | Klucz, który służy do uruchamiania kontenera. Jest ona dostępna na stronie klucze aparatu rozpoznawania Azure Portal formularza. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | Wartość identyfikatora URI punktu końcowego rozliczenia jest dostępna na stronie Omówienie aparatu rozpoznawania Azure Portal formularza.|
| **{COMPUTER_VISION_API_KEY}** | Klucz jest dostępny na stronie klucze interfejs API przetwarzania obrazów Azure Portal.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | Punkt końcowy rozliczeń. Jeśli używasz zasobu przetwarzanie obrazów opartego na chmurze, wartość identyfikatora URI jest dostępna na stronie Przegląd Azure Portal interfejs API przetwarzania obrazów. Jeśli używasz kontenera " *poznawcze-Services-rozpoznaje-Text* ", użyj adresu URL punktu końcowego rozliczenia, który jest przesyłany do kontenera w `docker run` poleceniu. |

Aby uzyskać szczegółowe informacje na temat sposobu uzyskiwania tych wartości, zobacz [zbieranie wymaganych parametrów](form-recognizer-container-howto.md#gathering-required-parameters) .

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Aby uruchomić kontener, określ `Eula` `Billing` Opcje, i `ApiKey` ; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Przykłady platformy Docker kontenera aparatu rozpoznawania formularzy

Poniższe przykłady platformy Docker dotyczą kontenera aparatów rozpoznawania formularzy.

### <a name="basic-example-for-form-recognizer"></a>Podstawowy przykład dla aparatu rozpoznawania formularza

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Przykład rejestrowania dla aparatu rozpoznawania formularza

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Następne kroki

* Przejrzyj temat [Instalowanie i uruchamianie kontenerów](form-recognizer-container-howto.md).
