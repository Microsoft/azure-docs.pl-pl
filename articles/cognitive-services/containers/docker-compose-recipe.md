---
title: Wdrażanie wielu kontenerów za pomocą narzędzia Docker Compose
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrożyć wiele kontenerów Cognitive Services. W tym artykule przedstawiono sposób organizowania wielu obrazów kontenerów platformy Docker za pomocą Docker Compose.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: aahi
ms.openlocfilehash: cedcf8a3fcd656c4af0ca7493c598791d35d20d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95996133"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Wdrażanie wielu kontenerów za pomocą narzędzia Docker Compose

W tym artykule opisano sposób wdrażania wielu kontenerów Cognitive Services platformy Azure. W tym celu dowiesz się, jak za pomocą Docker Compose organizować wiele obrazów kontenerów platformy Docker.

> [Docker Compose](https://docs.docker.com/compose/) to narzędzie służące do definiowania i uruchamiania aplikacji platformy Docker z obsługą kilku kontenerów. W obszarze redagowanie możesz skonfigurować usługi aplikacji przy użyciu pliku YAML. Następnie należy utworzyć i uruchomić wszystkie usługi z konfiguracji, uruchamiając pojedyncze polecenie.

Przydatne może być organizowanie wielu obrazów kontenerów na pojedynczym komputerze-hoście. W tym artykule połączymy kontenery aparatu rozpoznawania odczytu i formularza.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura wymaga kilku narzędzi, które muszą być zainstalowane i uruchomione lokalnie:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services).
* [Aparat platformy Docker](https://www.docker.com/products/docker-engine). Upewnij się, że interfejs wiersza polecenia platformy Docker działa w oknie konsoli.
* Zasób platformy Azure z poprawną warstwą cenową. Tylko następujące warstwy cenowe współpracują z tym kontenerem:
  * Zasób **Przetwarzanie obrazów** tylko za pomocą warstwy cenowej F0 lub standardowa.
  * Zasób **aparatu rozpoznawania formularza** z F0 lub warstwą cenową standardowa.
  * **Cognitive Services** zasób z warstwą cenową S0.
* Jeśli używasz kontenera warunkowego w wersji zapoznawczej, musisz ukończyć [formularz żądania online](https://aka.ms/csgate/) , aby go użyć.

## <a name="docker-compose-file"></a>Plik Docker Compose

Plik YAML definiuje wszystkie usługi, które mają zostać wdrożone. Te usługi korzystają z `DockerFile` lub istniejący obraz kontenera. W takim przypadku będziemy używać dwóch obrazów w wersji zapoznawczej. Skopiuj i wklej następujący plik YAML i Zapisz go jako *Docker-redagowanie. YAML*. Podaj odpowiednie wartości **apikey**, **rozliczeń** i **wartości endpointuri** w pliku.

```yaml
version: '3.7'
services:
  forms:
    image: "mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Utwórz katalogi na komputerze-hoście, które są określone w węźle **woluminy** . Takie podejście jest wymagane, ponieważ katalogi muszą istnieć przed podjęciem próby zainstalowania obrazu przy użyciu powiązań woluminu.

## <a name="start-the-configured-docker-compose-services"></a>Uruchom skonfigurowane usługi Docker Compose

Plik Docker Compose umożliwia zarządzanie wszystkimi etapami w cyklu życia zdefiniowanej usługi: uruchamianie, zatrzymywanie i ponowne kompilowanie usług; Wyświetlanie stanu usługi; i przesyłania strumieniowego dzienników. Otwórz interfejs wiersza polecenia z katalogu projektu (w którym znajduje się plik Docker-redagowanie. YAML).

> [!NOTE]
> Aby uniknąć błędów, należy się upewnić, że maszyna hosta prawidłowo współużytkuje dyski z aparatem platformy Docker. Na przykład jeśli *E:\publicpreview* jest używany jako katalog w pliku *Docker-redagowanie. YAML* , Udostępnij dysk **E** przy użyciu platformy Docker.

W interfejsie wiersza polecenia wykonaj następujące polecenie, aby uruchomić (lub ponownie uruchomić) wszystkie usługi zdefiniowane w pliku *Docker-Zredaguj. YAML* :

```console
docker-compose up
```

Pierwszy czas Docker wykonuje polecenie **Docker-Zredaguj** , korzystając z tej konfiguracji, pobierając obrazy skonfigurowane w węźle **usługi** , a następnie pobiera i instaluje je:

```console
Pulling forms (mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout:)...
latest: Pulling from azure-cognitive-services/form-recognizer/layout
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview:)...
latest: Pulling from /azure-cognitive-services/vision/read:3.1-preview
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Po pobraniu obrazów usługi Image Services są uruchamiane:

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Sprawdzanie dostępności usługi

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Oto przykładowe dane wyjściowe:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout          latest
4be104c126c5        mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview         latest
```

### <a name="test-containers"></a>Kontenery testowe

Otwórz przeglądarkę na komputerze hosta i przejdź do **hosta localhost** przy użyciu określonego portu z pliku *Docker-Zredaguj. YAML* , takiego jak http://localhost:5021/swagger/index.html . Można na przykład użyć funkcji **try it** w interfejsie API, aby przetestować punkt końcowy aparatu rozpoznawania formularza. Obie strony struktury Swagger kontenerów powinny być dostępne i weryfikowalne.

![Kontener aparatu rozpoznawania formularzy](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Kontenery Cognitive Services](../cognitive-services-container-support.md)
