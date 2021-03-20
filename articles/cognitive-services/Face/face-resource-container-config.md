---
title: Konfigurowanie kontenerów — Front
titleSuffix: Azure Cognitive Services
description: Środowisko uruchomieniowe kontenera czołowego jest konfigurowane przy użyciu `docker run` argumentów polecenia. Wymagane i opcjonalne ustawienia.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2f608843e27b79d02697df8e2a7f2aba6695e10a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80878429"
---
# <a name="configure-face-docker-containers"></a>Konfigurowanie kontenerów platformy Docker

Środowisko uruchomieniowe kontenera **czołowego** jest konfigurowane przy użyciu `docker run` argumentów polecenia. Ten kontener ma kilka wymaganych ustawień oraz kilka opcjonalnych ustawień. Kilka [przykładów](#example-docker-run-commands) polecenia jest dostępnych. Ustawienia dotyczące rozliczeń dotyczą tylko kontenera. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Ustawienia, [`Billing`](#billing-configuration-setting) , i [`Eula`](#eula-setting) są używane razem i należy podać prawidłowe wartości dla wszystkich trzech z nich; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera przy użyciu tych ustawień konfiguracji, zobacz [rozliczenia](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey`Ustawienie określa klucz zasobów platformy Azure służący do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość ApiKey, a wartość musi być prawidłowym kluczem dla zasobu _Cognitive Services_ określonego dla [`Billing`](#billing-configuration-setting) Ustawienia konfiguracji.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** zarządzania zasobami w obszarze **klucze**

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

`Billing`Ustawienie określa identyfikator URI punktu końcowego zasobu _Cognitive Services_ na platformie Azure używany do mierzenia informacji rozliczeniowych dla kontenera. Należy określić wartość tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _Cognitive Services_ na platformie Azure. Kontener zgłasza użycie co 10 do 15 minut.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** Omówienie, etykieta `Endpoint`

Pamiętaj _, aby dodać kierowanie_ do punktu końcowego, jak pokazano w przykładzie. 

|Wymagane| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Tak| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczenia. Aby uzyskać więcej informacji na temat uzyskiwania identyfikatora URI rozliczeń, zobacz [zbieranie wymaganych parametrów](face-how-to-install-containers.md#gathering-required-parameters). Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](../cognitive-services-custom-subdomains.md). |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Ustawienia konfiguracji CloudAI

Ustawienia konfiguracji w `CloudAI` sekcji zawierają opcje specyficzne dla kontenera, które są unikatowe dla Twojego kontenera. Następujące ustawienia i obiekty są obsługiwane dla kontenera Fronts w `CloudAI` sekcji

| Nazwa | Typ danych | Opis |
|------|-----------|-------------|
| `Storage` | Obiekt | Scenariusz magazynu używany przez kontener frontu. Aby uzyskać więcej informacji na temat scenariuszy magazynu i skojarzonych z nimi ustawień dla `Storage` obiektu, zobacz [Ustawienia scenariusza magazynu](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Ustawienia scenariusza magazynu

Kontener programu Front-Storage przechowuje dane obiektów blob, cache, Metadata i Queue, w zależności od przechowywanych elementów. Na przykład, indeksy szkoleniowe i wyniki dla dużej grupy osób są przechowywane jako dane obiektów BLOB. Kontener przedstawiający są dwa różne scenariusze magazynowania podczas korzystania z tych typów danych i ich przechowywania:

* Pamięć  
  Wszystkie cztery typy danych są przechowywane w pamięci. Nie są one dystrybuowane ani nie są trwałe. Po zatrzymaniu lub usunięciu kontenera rozpoznawania wartości wszystkie dane w magazynie dla tego kontenera zostaną zniszczone.  
  Jest to domyślny scenariusz magazynu dla kontenera czołowego.
* Azure  
  Kontener kroju używa usługi Azure Storage i Azure Cosmos DB do dystrybuowania tych czterech typów danych w magazynie trwałym. Dane obiektów blob i kolejek są obsługiwane przez usługę Azure Storage. Metadane i dane pamięci podręcznej są obsługiwane przez Azure Cosmos DB. Po zatrzymaniu lub usunięciu kontenera rozpoznawania wartości wszystkie dane w magazynie dla tego kontenera pozostają przechowywane w usłudze Azure Storage i Azure Cosmos DB.  
  W przypadku zasobów używanych w ramach scenariusza usługi Azure Storage dostępne są następujące dodatkowe wymagania
  * Zasób usługi Azure Storage musi używać rodzaju konta StorageV2
  * Zasób Azure Cosmos DB musi używać interfejsu API Azure Cosmos DB dla MongoDB

Scenariusze magazynu i powiązane ustawienia konfiguracji są zarządzane przez `Storage` obiekt, w `CloudAI` sekcji Konfiguracja. W obiekcie są dostępne następujące ustawienia konfiguracji `Storage` :

| Nazwa | Typ danych | Opis |
|------|-----------|-------------|
| `StorageScenario` | Ciąg | Scenariusz magazynu obsługiwany przez kontener. Dostępne są następujące wartości<br/>`Memory` — Wartość domyślna. Kontener używa nietrwałego, nierozproszonego i niemagazynowanego magazynu dla jednego węzła, tymczasowego użycia. Jeśli kontener zostanie zatrzymany lub usunięty, magazyn dla tego kontenera zostanie zniszczony.<br/>`Azure` -Kontener używa zasobów platformy Azure do przechowywania. Po zatrzymaniu lub usunięciu kontenera magazyn dla tego kontenera jest utrwalany.|
| `ConnectionStringOfAzureStorage` | Ciąg | Parametry połączenia dla zasobu usługi Azure Storage używanego przez kontener.<br/>To ustawienie ma zastosowanie tylko wtedy `Azure` , gdy jest określony dla `StorageScenario` Ustawienia konfiguracji. |
| `ConnectionStringOfCosmosMongo` | Ciąg | Parametry połączenia MongoDB dla zasobu Azure Cosmos DB używanego przez kontener.<br/>To ustawienie ma zastosowanie tylko wtedy `Azure` , gdy jest określony dla `StorageScenario` Ustawienia konfiguracji. |

Na przykład następujące polecenie określa scenariusz usługi Azure Storage i zawiera przykładowe parametry połączenia dla usługi Azure Storage i Cosmos DB zasoby używane do przechowywania danych dla kontenera czołowego.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Scenariusz magazynu jest obsługiwany niezależnie od instalacji i instalacji danych wyjściowych. Możesz określić kombinację tych funkcji dla jednego kontenera. Na przykład następujące polecenie definiuje instalację platformy Docker do `D:\Output` folderu na komputerze hosta jako instalację wyjściową, a następnie tworzy wystąpienie kontenera z obrazu kontenera rozpoznawania, zapisując pliki dziennika w formacie JSON w instalacji wyjściowej. To polecenie określa również scenariusz usługi Azure Storage i zawiera przykładowe parametry połączenia dla usługi Azure Storage i Cosmos DB zasoby używane do przechowywania danych dla kontenera.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Ustawienie umowy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia pozostały

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia logowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj instalacji powiązań, aby odczytywać i zapisywać dane w kontenerze i z niego. Można określić instalację wejściową lub instalację wyjściową, określając `--mount` opcję w poleceniu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Kontenery ze stroną nie używają instalacji danych wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usług. 

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera hosta](face-how-to-install-containers.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy Docker i uprawnieniami lokalizacji instalacji hosta. 

|Opcjonalne| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Kontenery kroju nie używają tego.|
|Opcjonalne| `Output` | Ciąg | Obiekt docelowy instalacji wyjściowej. Wartość domyślna to `/output`. Jest to lokalizacja dzienników. Dotyczy to również dzienników kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy Docker 

W poniższych przykładach użyto ustawień konfiguracji, aby zilustrować sposób pisania i używania `docker run` poleceń.  Po uruchomieniu kontenera kontynuuje działanie, dopóki nie zostanie [zatrzymane](face-how-to-install-containers.md#stop-the-container) .

* **Znak kontynuacji wiersza**: polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego, `\` jako znaku kontynuacji wiersza. Zastąp lub usuń to w zależności od wymagań systemu operacyjnego hosta. 
* **Kolejność** argumentów: nie zmieniaj kolejności argumentów, o ile nie znasz już kontenerów platformy Docker.

Zastąp ciąg {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz punktu końcowego `Face` zasobu na stronie kluczy platformy Azure `Face` . | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczenia jest dostępna na `Face` stronie Przegląd platformy Azure.| Zobacz [zbieranie wymaganych parametrów](face-how-to-install-containers.md#gathering-required-parameters) dla jawnych przykładów. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby można było uruchomić kontener, należy określić opcje, i. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](face-how-to-install-containers.md#billing).
> Wartość ApiKey jest **kluczem** ze `Cognitive Services` strony klucze zasobów platformy Azure. 

## <a name="face-container-docker-examples"></a>Przykłady platformy Docker kontenera

Poniższe przykłady platformy Docker dotyczą kontenera rozpoznawania. 

### <a name="basic-example"></a>Przykład podstawowy 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Przykład rejestrowania 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Następne kroki

* Zapoznaj [się z tematem Instalowanie i uruchamianie kontenerów](face-how-to-install-containers.md)
