---
title: 'Szybki start: tworzenie aplikacji Java w usłudze Azure Service Fabric'
description: W ramach tego przewodnika Szybki start utworzysz aplikację Java dla platformy Azure za pomocą aplikacji przykładowej niezawodnych usług usługi Service Fabric.
ms.date: 01/29/2019
ms.topic: quickstart
ms.custom:
- mvc
- devcenter
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 199fe459ec8fd58d05b4946dda4291c46a4881c3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530905"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Szybki start: wdrażanie aplikacji Java na platformie Azure Service Fabric systemie Linux

W tym przewodniku Szybki start wdrożysz aplikację Java na platformie Azure Service Fabric środowisku ECLIPSE IDE na maszynie dewelopera z systemem Linux. Po zakończeniu pracy będziesz mieć aplikację do głosowania z frontonem internetowym w języku Java, która zapisuje wyniki głosowania w stanowej usłudze zaplecza w klastrze.

Usługa Azure Service Fabric to platforma systemów rozproszonych umożliwiająca wdrażanie mikrousług i kontenerów, a także zarządzanie nimi.

## <a name="prerequisites"></a>Wymagania wstępne

- [Środowisko Java i](./service-fabric-get-started-linux.md#set-up-java-development) [yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Eclipse Neon (4.6)+](https://www.eclipse.org/downloads/packages/) i [wtyczka środowiska Eclipse dla Service Fabric](./service-fabric-get-started-linux.md#install-the-eclipse-plug-in-optional)
- [Service Fabric SDK i interfejs wiersza polecenia (CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Usługa Git](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>Pobieranie przykładu

W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Lokalne uruchamianie aplikacji

1. Uruchom klaster lokalny, uruchamiając następujące polecenie:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Uruchamianie klastra lokalnego zajmuje nieco czasu. Aby potwierdzić, że klaster jest w pełni uruchomiony, otwórz narzędzie Service Fabric Explorer dostępne pod adresem `http://localhost:19080`. Pięć węzłów w dobrej kondycji oznacza, że klaster lokalny jest uruchomiony.

    ![Usługa Azure Service Fabric Explorer pokazuje węzły w dobrej kondycji](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Otwórz program Eclipse.
3. Wybierz **pozycję Importuj** plik w istniejącym projekcie  >    >    >  **gradle i** postępuj zgodnie z kroku kreatora.
4. Wybierz **pozycję Katalog** i wybierz katalog **Voting** z folderu **service-fabric-java-quickstart** sklonowanego z usługi GitHub. Wybierz pozycję **Zakończ**.

    ![Importowanie projektu Gradle do środowiska Eclipse](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Teraz projekt `Voting` będzie dostępny w widoku Eksplorator pakietów programu Eclipse.
6. Kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Publish Application (Publikuj aplikację)** Service Fabric **rozwijanej.** Wybierz **pozycję PublishProfiles/Local.jsna jako** Profil docelowy, a następnie wybierz pozycję **Opublikuj.**

    ![Publikowanie Service Fabric JSON na platformie Azure](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Uruchom przeglądarkę internetową i uzyskaj dostęp do aplikacji, przechodząc do adresu `http://localhost:8080`.

    ![Usługa Azure Service Fabric hosta lokalnego](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Teraz możesz dodać zestaw opcji głosowania i rozpocząć obsługę głosów. Aplikacja zostanie uruchomiona i będzie przechować wszystkie dane w klastrze usługi Service Fabric, bez konieczności używania oddzielnej bazy danych.

![Przykład głosowania Service Fabric azure](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalowanie aplikacji i usług w klastrze

Usługi można skalować na klaster w celu dostosowania ich do zmiany obciążenia. Skalowanie usługi odbywa się przez zmienianie liczby wystąpień uruchomionych w klastrze. Istnieje wiele sposobów skalowania usług. Na przykład można użyć skryptów lub poleceń z interfejsu wiersza Service Fabric wiersza polecenia ( `sfctl` ). W poniższych krokach będzie używane narzędzie Service Fabric Explorer.

Service Fabric Explorer działa we wszystkich Service Fabric klastrach i można uzyskać do niego dostęp z przeglądarki, przeglądając port zarządzania HTTP klastra (19080). Na przykład `http://localhost:19080`.

Aby skalować usługę internetową frontonu, wykonaj następujące czynności:

1. Otwórz Service Fabric Explorer w klastrze. Na przykład `https://localhost:19080`.
2. Wybierz wielokropek (**...**) obok węzła **fabric:/Voting/VotingWeb** w widoku drzewa i wybierz pozycję **Skaluj usługę**.

    ![Skalowanie usługi w usłudze Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Teraz możesz skalować liczbę wystąpień usługi internetowej frontonu.

3. Zmień liczbę na **2 i** wybierz pozycję **Skaluj usługę**.
4. Wybierz węzeł **fabric:/Voting/VotingWeb** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID).

    ![Skalowana usługa w usłudze Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Teraz widać, że usługa ma dwa wystąpienia, a w widoku drzewa można dostrzec węzły, w których są one uruchomione.

Za pomocą tego prostego zadania zarządzania zostały podwojone zasoby dostępne dla usługi frontonu na potrzeby przetwarzania obciążenia użytkownika. Pamiętaj, że nie musisz mieć wielu wystąpień usługi, aby działała ona niezawodnie. W przypadku awarii usługa Service Fabric gwarantuje uruchomienie nowego wystąpienie usługi w klastrze.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Używanie programu Eclipse jako narzędzia dla aplikacji Java usługi Service Fabric
* Wdrażanie aplikacji Java w klastrze lokalnym
* Skalowanie aplikacji w poziomie na wiele węzłów

Aby dowiedzieć się więcej o pracy z aplikacjami Java w usłudze Service Fabric, przejdź do samouczka dotyczącego aplikacji Java.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji Java](./service-fabric-tutorial-create-java-app.md)
