---
title: Jak przygotowywanie projektu dla Azure Dev Spaces działa
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Opisuje, jak przygotowywanie projektu z Azure Dev Spaces działa
keywords: azds. YAML, Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: 61351072494b51d02a1d6c31399208b9e9b54fce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213417"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Jak przygotowywanie projektu dla Azure Dev Spaces działa

Azure Dev Spaces zapewnia wiele sposobów na szybkie Iterowanie i debugowanie aplikacji Kubernetes oraz współpracę z zespołem w klastrze usługi Azure Kubernetes Service (AKS). Spacje dev mogą generować wykresy wieloetapowe dockerfile i Helm dla projektu. Miejsca dev tworzy również plik konfiguracyjny służący do wdrażania, uruchamiania i debugowania aplikacji Kubernetes w AKS. Wszystkie te pliki znajdują się w kodzie aplikacji i można je dodać do systemu kontroli wersji.

W tym artykule opisano, co się dzieje w przygotowaniu projektu do uruchamiania w AKS z miejscami deweloperskimi.

## <a name="prepare-your-code"></a>Przygotowywanie kodu

Aby można było uruchomić aplikację w przestrzeni deweloperskiej, musi ona być zamieszczona w kontenerze i należy określić sposób jej wdrożenia w Kubernetes. Aby konteneryzowanie aplikację, potrzebujesz pliku dockerfile. Aby zdefiniować sposób wdrażania aplikacji w usłudze Kubernetes, potrzebny jest [Wykres Helm](https://docs.helm.sh/). Aby pomóc w tworzeniu wykresu pliku dockerfile i Helm dla aplikacji, narzędzia po stronie klienta udostępniają `prep` następujące polecenie:

```cmd
azds prep --enable-ingress
```

`prep`Polecenie będzie przeglądać pliki w projekcie i próbować utworzyć wykres pliku dockerfile i Helm do uruchamiania aplikacji w Kubernetes. Obecnie `prep` polecenie generuje wykres pliku dockerfile i Helm z następującymi językami:

* Java
* Node.js
* .NET Core

*Należy* uruchomić `prep` polecenie z katalogu, który zawiera kod źródłowy. Uruchomienie `prep` polecenia z poprawnego katalogu pozwala narzędziom po stronie klienta zidentyfikować język i utworzyć odpowiednie pliku dockerfile do konteneryzowanie aplikacji. Możesz również uruchomić `prep` polecenie z katalogu, który zawiera plik *pom.xml* dla projektów języka Java.

Jeśli uruchomisz `prep` polecenie z katalogu, który nie zawiera kodu źródłowego, narzędzia po stronie klienta nie będą generować pliku dockerfile. Zostanie również wyświetlony komunikat o błędzie mówiący: nie można *wygenerować pliku dockerfile z powodu nieobsługiwanego języka*. Ten błąd występuje również, gdy narzędzia po stronie klienta nie rozpoznają typu projektu.

Po uruchomieniu `prep` polecenia można określić `--enable-ingress` flagę. Ta flaga nakazuje kontrolerowi utworzenie punktu końcowego dostępnego z Internetu dla tej usługi. Jeśli ta flaga nie zostanie określona, usługa będzie dostępna tylko z poziomu klastra lub przy użyciu tunelu localhost utworzonego przez narzędzia po stronie klienta. To zachowanie można włączyć lub wyłączyć po uruchomieniu `prep` polecenia przez zaktualizowanie wygenerowanego wykresu Helm.

`prep`Polecenie nie zastąpi żadnych istniejących wykresów wieloetapowe dockerfile lub Helm w projekcie. Jeśli istniejący wykres pliku dockerfile lub Helm używa tej samej konwencji nazewnictwa jak pliki wygenerowane przez `prep` polecenie, `prep` polecenie spowoduje pominięcie generowania tych plików. W przeciwnym razie `prep` polecenie spowoduje wygenerowanie własnego wykresu pliku dockerfile lub Helm wzdłuż istniejących plików.

> [!IMPORTANT]
> Azure Dev Spaces używa wykresu pliku dockerfile i Helm dla projektu do kompilowania i uruchamiania kodu, ale można modyfikować te pliki, jeśli chcesz zmienić sposób kompilowania i uruchamiania projektu.

`prep`Polecenie spowoduje również wygenerowanie `azds.yaml` pliku w katalogu głównym projektu. Azure Dev Spaces używa tego pliku do kompilowania, instalowania, konfigurowania i uruchamiania aplikacji. Ten plik konfiguracyjny zawiera lokalizację wykresu pliku dockerfile i Helm, a także dodatkową konfigurację na podstawie tych artefaktów.

Oto przykład pliku azds. YAML utworzonego za pomocą [przykładowej aplikacji platformy .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

`azds.yaml`Plik wygenerowany przez `prep` polecenie jest przeznaczony do pracy w prostym scenariuszu projektowania pojedynczego projektu. Jeśli konkretny projekt ma zwiększoną złożoność, może być konieczne zaktualizowanie tego pliku po uruchomieniu `prep` polecenia. Na przykład projekt może wymagać pewnych zmian w procesie kompilacji lub uruchamiania w zależności od potrzeb związanych z programowaniem lub debugowaniem. W projekcie może być również wiele aplikacji, które wymagają wielu procesów kompilacji lub innej zawartości kompilacji.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o uruchamianiu kodu w obszarze deweloperskim, zobacz [jak uruchomić kod za pomocą Azure dev Spaces działa][how-it-works-up].

[how-it-works-up]: how-dev-spaces-works-up.md