---
title: 'Szybki Start: Tworzenie aplikacji Java na platformie Azure Service Fabric'
description: W ramach tego przewodnika Szybki start utworzysz aplikację Java dla platformy Azure za pomocą aplikacji przykładowej niezawodnych usług usługi Service Fabric.
ms.topic: quickstart
ms.date: 01/29/2019
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 2f1e5f8f73f74d4b427e574b9e6a75aaf84a4211
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91529866"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Szybki Start: wdrażanie aplikacji Java na platformie Azure Service Fabric w systemie Linux

W tym przewodniku szybki start wdrożono aplikację Java na platformie Azure Service Fabric przy użyciu środowiska IDE w języku zaćmienie na komputerze dewelopera systemu Linux. Po zakończeniu pracy będziesz mieć aplikację do głosowania z frontonem internetowym w języku Java, która zapisuje wyniki głosowania w stanowej usłudze zaplecza w klastrze.

Usługa Azure Service Fabric to platforma systemów rozproszonych umożliwiająca wdrażanie mikrousług i kontenerów, a także zarządzanie nimi.

## <a name="prerequisites"></a>Wymagania wstępne

- [Środowisko Java](./service-fabric-get-started-linux.md#set-up-java-development) i [Narzędzia Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Zaćmienie (4.6) +](https://www.eclipse.org/downloads/packages/) i [zaćmienie plug-in dla Service Fabric](./service-fabric-get-started-linux.md#install-the-eclipse-plug-in-optional)
- [Service Fabric zestawu SDK i interfejsu wiersza polecenia (CLI)](./service-fabric-get-started-linux.md#installation-methods)
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

    ![Usługa Azure Service Fabric Explorer wyświetla w dobrej kondycji węzły](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Otwórz program Eclipse.
3. Wybierz pozycję **plik**  >  **Import**  >  **Gradle**  >  **istniejący projekt Gradle** i postępuj zgodnie z instrukcjami kreatora.
4. Wybierz pozycję **katalog** i wybierz katalog **głosowania** z folderu **Service-Fabric-Java-Start** , który został sklonowany z witryny GitHub. Wybierz pozycję **Zakończ**.

    ![Importuj projekt Gradle do przezaćmienia](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Teraz projekt `Voting` będzie dostępny w widoku Eksplorator pakietów programu Eclipse.
6. Kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Opublikuj aplikację** na liście rozwijanej **Service Fabric** . Wybierz pozycję **PublishProfiles/Local.js** jako profil docelowy, a następnie wybierz pozycję **Publikuj**.

    ![Usługa Azure Service Fabric publikowanie lokalnego pliku JSON](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Uruchom przeglądarkę internetową i uzyskaj dostęp do aplikacji, przechodząc do adresu `http://localhost:8080`.

    ![Host lokalny Service Fabric platformy Azure](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Teraz możesz dodać zestaw opcji głosowania i rozpocząć obsługę głosów. Aplikacja zostanie uruchomiona i będzie przechować wszystkie dane w klastrze usługi Service Fabric, bez konieczności używania oddzielnej bazy danych.

![Przykład głosowania Service Fabric platformy Azure](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalowanie aplikacji i usług w klastrze

Usługi można skalować na klaster w celu dostosowania ich do zmiany obciążenia. Skalowanie usługi odbywa się przez zmienianie liczby wystąpień uruchomionych w klastrze. Istnieje wiele sposobów skalowania usług. Można na przykład użyć skryptów lub poleceń z Service Fabric interfejsu wiersza polecenia ( `sfctl` ). W poniższych krokach będzie używane narzędzie Service Fabric Explorer.

Service Fabric Explorer działa we wszystkich klastrach Service Fabric i można uzyskać do nich dostęp z przeglądarki, przechodząc do portu HTTP zarządzania klastrami (19080). Na przykład `http://localhost:19080`.

Aby skalować usługę internetową frontonu, wykonaj następujące czynności:

1. Otwórz Service Fabric Explorer w klastrze. Na przykład `https://localhost:19080`.
2. Wybierz wielokropek (**...**) obok węzła **Sieć szkieletowa:/głosu/VotingWeb** w elemencie TreeView i wybierz pozycję **Skaluj usługę**.

    ![Skalowanie usługi w usłudze Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Teraz możesz skalować liczbę wystąpień usługi internetowej frontonu.

3. Zmień liczbę na **2** i wybierz pozycję **Skaluj usługę**.
4. Wybierz węzeł **Sieć szkieletowa:/głosowania/VotingWeb** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID).

    ![Usługa skalowana w Service Fabric na platformie Azure](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

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
