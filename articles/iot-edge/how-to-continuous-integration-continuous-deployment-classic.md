---
title: Ciągła integracja i ciągłe wdrażanie na Azure IoT Edge (edytor klasyczny) — Azure IoT Edge
description: Konfigurowanie ciągłej integracji i ciągłego wdrażania przy użyciu edytora klasycznego — Azure IoT Edge z Azure DevOps, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f7c28ecbaa58731c528a9ecb5f869eba2bc0c99f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484425"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Ciągła integracja i ciągłe wdrażanie na Azure IoT Edge (edytor klasyczny)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Metodyki DevOps można łatwo Azure IoT Edge aplikacjami za pomocą wbudowanych Azure IoT Edge zadań Azure Pipelines. W tym artykule pokazano, jak używać funkcji ciągłej integracji i ciągłego wdrażania usługi Azure Pipelines do szybkiego i wydajnego kompilowania, testowania i wdrażania aplikacji Azure IoT Edge użyciu edytora klasycznego. Alternatywnie możesz użyć [yaml](how-to-continuous-integration-continuous-deployment.md).

![Diagram — gałęzie ci i CD dla procesów development i production](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

Z tego artykułu dowiesz się, jak [](/azure/devops/pipelines/tasks/build/azure-iot-edge) używać wbudowanych zadań Azure IoT Edge dla Azure Pipelines do tworzenia potoków kompilacji i wydania dla IoT Edge rozwiązania. Każde Azure IoT Edge dodane do potoku implementuje jedną z następujących czterech akcji:

 | Akcja | Opis |
 | --- | --- |
 | Tworzenie obrazów modułów | Pobiera kod IoT Edge i tworzy obrazy kontenerów.|
 | Wypychanie obrazów modułów | Wypycha obrazy modułów do określonego rejestru kontenerów. |
 | Generowanie manifestu wdrożenia | Pobiera deployment.template.jspliku i zmiennych, a następnie generuje ostateczny IoT Edge manifestu wdrożenia. |
 | Wdrażanie na urządzeniach usługi IoT Edge | Tworzy IoT Edge wdrożenia na co najmniej jednym IoT Edge urządzeń. |

O ile nie określono inaczej, procedury w tym artykule nie eksplorować wszystkich funkcji dostępnych za pośrednictwem parametrów zadania. Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Wersja zadania](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Zaawansowane** — jeśli ma to zastosowanie, określ moduły, które nie mają być budowaną.
* [Opcje kontrolki](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Zmienne środowiskowe](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Zmienne wyjściowe](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Wymagania wstępne

* Repozytorium Azure Repos repozytorium. Jeśli go nie masz, możesz utworzyć nowe repozytorium [Git w projekcie](/azure/devops/repos/git/create-new-repo). W tym artykule utworzono repozytorium o nazwie **IoTEdgeRepo.**
* Rozwiązanie IoT Edge zatwierdzone i wypchniętą do repozytorium. Jeśli chcesz utworzyć nowe przykładowe rozwiązanie do testowania tego artykułu, wykonaj kroki opisane w artykule Tworzenie i debugowanie modułów w [programie Visual Studio Code](how-to-vs-code-develop-module.md) lub Tworzenie i debugowanie modułów [języka C#](./how-to-visual-studio-develop-module.md)w programie Visual Studio . W tym artykule w naszym repozytorium zostało utworzone rozwiązanie o nazwie **IoTEdgeSolution,** które zawiera kod modułu o nazwie **filtermodule.**

   Na potrzeby tego artykułu wystarczy folder rozwiązania utworzony przez szablony IoT Edge w Visual Studio Code lub Visual Studio. Nie musisz kompilować, wypychać, wdrażać ani debugować tego kodu przed przystąpieniem. Te procesy skonfigurujesz w Azure Pipelines.

   Jeśli tworzysz nowe rozwiązanie, najpierw sklonuj repozytorium lokalnie. Następnie podczas tworzenia rozwiązania możesz wybrać jego utworzenie bezpośrednio w folderze repozytorium. W tym miejscu można łatwo zatwierdzać i wypychać nowe pliki.

* Rejestr kontenerów, w którym można wypychać obrazy modułów. Możesz użyć [Azure Container Registry](../container-registry/index.yml) lub rejestru innej firmy.
* Aktywne centrum Azure [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) z co najmniej dwoma IoT Edge do testowania oddzielnych etapów testowania i wdrażania produkcyjnego. Możesz postępować zgodnie z artykułami Szybkiego startu, aby utworzyć IoT Edge w systemie [Linux lub](quickstart-linux.md) [Windows](quickstart.md)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Tworzenie potoku kompilacji w celu ciągłej integracji

W tej sekcji utworzysz nowy potok kompilacji. Potok konfiguruje się tak, aby był uruchamiany automatycznie po zaewidencjoncjomniania zmian w przykładowym IoT Edge i publikowaniu dzienników kompilacji.

1. Zaloguj się do swojej Azure DevOps () i otwórz projekt, który zawiera `https://dev.azure.com/{your organization}` IoT Edge repozytorium rozwiązań.

    ![Otwieranie projektu DevOps](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. W menu okienka po lewej stronie w projekcie wybierz **pozycję Potoki**. Wybierz **pozycję Utwórz potok** w środku strony. Jeśli masz już potoki kompilacji, wybierz przycisk **Nowy** potok w prawym górnym rogu.

    ![Tworzenie nowego potoku kompilacji](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. W dolnej części strony Where is your code? (Gdzie jest **Twój kod?)** wybierz **pozycję Use the classic editor (Użyj edytora klasycznego).** Jeśli chcesz użyć pliku YAML do utworzenia potoków kompilacji projektu, zobacz [przewodnik YAML](how-to-continuous-integration-continuous-deployment.md).

    ![Wybierz pozycję Użyj edytora klasycznego](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Postępuj zgodnie z monitami, aby utworzyć potok.

   1. Podaj informacje źródłowe dla nowego potoku kompilacji. Wybierz **Azure Repos Git** jako źródło, a następnie wybierz projekt, repozytorium i gałąź, w IoT Edge znajduje się kod rozwiązania. Następnie wybierz pozycję **Kontynuuj.**

      ![Wybieranie źródła potoku](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Wybierz **pozycję Puste** zadanie zamiast szablonu.

      ![Rozpoczynanie od pustego zadania dla potoku kompilacji](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. Po utworzeniu potoku zostaniesz przekierowyny do edytora potoku. W tym miejscu możesz zmienić nazwę potoku, pulę agentów i specyfikację agenta.

   Możesz wybrać pulę hostowaną przez firmę Microsoft lub zarządzaną przez Siebie pulę.

   W opisie potoku wybierz poprawną specyfikację agenta na podstawie platformy docelowej:

   * Jeśli chcesz utworzyć moduły na platformie amd64 dla kontenerów systemu Linux, **wybierz ubuntu-16.04**

   * Jeśli chcesz utworzyć moduły na platformie amd64 dla kontenerów systemu Windows 1809, musisz skonfigurować własnego agenta w [systemie Windows.](/azure/devops/pipelines/agents/v2-windows)

   * Jeśli chcesz utworzyć moduły na platformie arm32v7 lub arm64 dla kontenerów systemu Linux, musisz skonfigurować własnego agenta w [systemie Linux.](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent)

    ![Konfigurowanie specyfikacji agenta kompilacji](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. Potok jest wstępnie skonfigurowany z zadaniem o nazwie **Zadanie agenta 1.** Wybierz znak plus ( ), aby dodać cztery zadania do zadania: Azure IoT Edge dwa razy, raz skopiuj pliki i opublikuj **+** **artefakty kompilacji** jeden raz.   Wyszukaj każde zadanie i umieść kursor nad jego nazwą, aby wyświetlić **przycisk** Dodaj.

   ![Dodawanie Azure IoT Edge zadań](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Po dodaniu wszystkich czterech zadań zadanie agenta wygląda jak w poniższym przykładzie:

   ![Cztery zadania w potoku kompilacji](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Wybierz pierwsze **zadanie Azure IoT Edge,** aby je edytować. To zadanie tworzy wszystkie moduły w rozwiązaniu z platformą docelową, która jest przez Ciebie określana. Edytuj zadanie przy użyciu następujących wartości:

    | Parametr | Opis |
    | --- | --- |
    | Nazwa wyświetlana | Nazwa wyświetlana jest automatycznie aktualizowana po zmianie pola Akcja. |
    | Akcja | Wybierz **pozycję Build module images (Kompilowanie obrazów modułów).** |
    | .template.jsw pliku | Wybierz wielokropek (**...**) i przejdź dodeployment.template.js **pliku** w repozytorium zawierającym IoT Edge rozwiązania. |
    | Platforma domyślna | Wybierz odpowiedni system operacyjny dla modułów na podstawie docelowego IoT Edge operacyjnego. |
    | Zmienne wyjściowe | Podaj nazwę odwołania do skojarzenia ze ścieżką pliku, w której deployment.jsw pliku, taką jak **edge**. |

   Te konfiguracje używają repozytorium obrazów i tagów zdefiniowanych w `module.json` pliku, aby nazwać i oznaczyć obraz modułu. **Obrazy modułów kompilacji** pomagają również zastąpić zmienne dokładną wartością zdefiniowaną w `module.json` pliku. W Visual Studio lub Visual Studio Code określasz rzeczywistą wartość w `.env` pliku. W Azure Pipelines wartość jest ustawiana na karcie **Zmienne potoku.** Wybierz **kartę Zmienne** w menu edytora potoku i skonfiguruj nazwę i wartość w następujący sposób:

    * **ACR_ADDRESS:** wartość Azure Container Registry serwera **logowania.** Serwer logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal.

    Jeśli masz inne zmienne w projekcie, możesz określić nazwę i  wartość na tej karcie. Obrazy modułu kompilacji rozpoznają tylko te zmienne, które są w `${VARIABLE}` formacie. Upewnij się, że używasz tego formatu w `**/module.json` plikach.

8. Wybierz drugie zadanie **Azure IoT Edge,** aby je edytować. To zadanie wypycha wszystkie obrazy modułów do wybranego rejestru kontenerów.

    | Parametr | Opis |
    | --- | --- |
    | Nazwa wyświetlana | Nazwa wyświetlana jest automatycznie aktualizowana po zmianie pola Akcja. |
    | Akcja | Wybierz pozycję **Push module images (Wypychanie obrazów modułów).** |
    | Typ rejestru kontenerów | Użyj typu domyślnego: `Azure Container Registry` . |
    | Subskrypcja platformy Azure | Wybierz subskrypcję. |
    | Azure Container Registry | Wybierz typ rejestru kontenerów, który będzie używać do przechowywania obrazów modułów. W zależności od wybranego typu rejestru formularz się zmienia. Jeśli wybierzesz **Azure Container Registry**, użyj list rozwijanych, aby wybrać subskrypcję platformy Azure i nazwę rejestru kontenerów. W przypadku wybrania **opcji Container Registry** wybierz pozycję **Nowy,** aby utworzyć połączenie usługi rejestru. |
    | .template.jsw pliku | Wybierz wielokropek (**...**) i przejdź dodeployment.template.js **w** pliku w repozytorium, które zawiera IoT Edge rozwiązania. |
    | Platforma domyślna | Wybierz odpowiedni system operacyjny dla modułów na podstawie docelowego IoT Edge operacyjnego. |
    | Dodawanie poświadczeń rejestru do manifestu wdrożenia | Określ wartość true, aby dodać poświadczenia rejestru do wypychania obrazów platformy Docker do manifestu wdrożenia. |

   Jeśli masz wiele rejestrów kontenerów do hostowania obrazów modułów, musisz zduplikować  to zadanie, wybrać  inny rejestr kontenerów i użyć modułów obejścia w ustawieniach zaawansowanych, aby pominąć obrazy, które nie są dla tego konkretnego rejestru.

9. Wybierz zadanie **Kopiuj pliki,** aby je edytować. To zadanie pozwala skopiować pliki do katalogu przemieszczania artefaktów.

    | Parametr | Opis |
    | --- | --- |
    | Nazwa wyświetlana | Użyj nazwy domyślnej lub dostosuj |
    | Folder źródłowy | Folder z plikami do skopiowania. |
    | Zawartość | Dodaj dwa wiersze: `deployment.template.json` i `**/module.json` . Te dwa pliki służą jako dane wejściowe do generowania IoT Edge wdrożenia. |
    | Folder docelowy | Określ zmienną `$(Build.ArtifactStagingDirectory)` . Aby dowiedzieć się więcej o opisie, zobacz Build [variables](/azure/devops/pipelines/build/variables#build-variables) (Tworzenie zmiennych). |

10. Wybierz zadanie **Publish Build Artifacts (Publikuj artefakty kompilacji),** aby je edytować. Podaj ścieżkę katalogu tymczasowego artefaktu do zadania, aby można było opublikować ścieżkę w potoku wydania.

    | Parametr | Opis |
    | --- | --- |
    | Nazwa wyświetlana | Użyj nazwy domyślnej lub dostosuj. |
    | Ścieżka do opublikowania | Określ zmienną `$(Build.ArtifactStagingDirectory)` . Aby dowiedzieć [się więcej,](/azure/devops/pipelines/build/variables#build-variables) zobacz Build variables (Tworzenie zmiennych). |
    | Nazwa artefaktu | Użyj nazwy domyślnej: **drop** |
    | Lokalizacja publikowania artefaktu | Użyj lokalizacji domyślnej: **Azure Pipelines** |

11. Otwórz **kartę Wyzwalacze** i zaznacz pole wyboru Włącz **ciągłą integrację.** Upewnij się, że gałąź zawierająca kod jest uwzględniona.

    ![Włączanie wyzwalacza ciągłej integracji](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Wybierz **pozycję Zapisz** z listy **rozwijanej & kolejki.**

Ten potok jest teraz skonfigurowany do automatycznego uruchamiania podczas wypychania nowego kodu do swojego repo. Ostatnie zadanie, publikowanie artefaktów potoku, wyzwala potok wydania. Przejdź do następnej sekcji, aby skompilować potok wydania.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Jeśli chcesz używać wdrożeń **warstwowych** w potoku, wdrożenia warstwowe nie są jeszcze obsługiwane w Azure IoT Edge w Azure DevOps.
>
>Można jednak użyć zadania interfejsu wiersza polecenia [platformy Azure w Azure DevOps,](/azure/devops/pipelines/tasks/deploy/azure-cli) aby utworzyć wdrożenie jako wdrożenie warstwowe. Dla wartości **Skryptu wbudowanego** możesz użyć polecenia [az iot edge deployment create:](/cli/azure/iot/edge/deployment)
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Następne kroki

* IoT Edge przykład najlepszych rozwiązań metodyki DevOps w [Azure DevOps Starter dla IoT Edge](how-to-devops-starter.md)
* Opis wdrażania IoT Edge w [te IoT Edge wdrożenia dla pojedynczych urządzeń lub na dużą skalę](module-deployment-monitoring.md)
* Aby utworzyć, zaktualizować lub usunąć wdrożenie, należy wykonać kroki opisane w te tematze Wdrażanie i monitorowanie [IoT Edge modułów na dużą skalę.](how-to-deploy-at-scale.md)