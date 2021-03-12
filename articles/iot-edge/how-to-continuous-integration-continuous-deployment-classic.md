---
title: Ciągła integracja i ciągłe wdrażanie do Azure IoT Edge urządzeń (klasyczny edytor) — Azure IoT Edge
description: Skonfiguruj ciągłą integrację i ciągłe wdrażanie przy użyciu klasycznego edytora — Azure IoT Edge z usługą Azure DevOps, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 218c0f345e4ea453a2300b3de85ac8856a09c6ee
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199272"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Ciągła integracja i ciągłe wdrażanie na urządzeniach Azure IoT Edge (klasyczny edytor)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Możesz łatwo przyjmować DevOps z aplikacjami Azure IoT Edge przy użyciu wbudowanych zadań Azure IoT Edge w Azure Pipelines. W tym artykule pokazano, jak korzystać z funkcji ciągłej integracji i ciągłego wdrażania Azure Pipelines do kompilowania, testowania i wdrażania aplikacji szybko i wydajnie w Azure IoT Edge przy użyciu klasycznego edytora. Alternatywnie możesz [użyć YAML](how-to-continuous-integration-continuous-deployment.md).

![Diagramy — gałęzie CI i CD na potrzeby programowania i produkcji](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

W tym artykule dowiesz się, jak za pomocą wbudowanych [Azure IoT Edge zadań](/azure/devops/pipelines/tasks/build/azure-iot-edge) dla Azure Pipelines utworzyć potoki kompilacji i wydania dla rozwiązania IoT Edge. Każde zadanie Azure IoT Edge dodane do potoku implementuje jedną z następujących czterech akcji:

 | Akcja | Opis |
 | --- | --- |
 | Kompilowanie obrazów modułów | Pobiera IoT Edge kod rozwiązania i kompiluje obrazy kontenerów.|
 | Wypychanie obrazów modułów | Wypychanie obrazów modułów do określonego rejestru kontenerów. |
 | Generuj manifest wdrożenia | Przyjmuje deployment.template.jsw pliku i zmiennych, a następnie generuje plik manifestu wdrożenia Final IoT Edge. |
 | Wdrażanie na urządzeniach usługi IoT Edge | Tworzy IoT Edge wdrożenia na co najmniej jednym urządzeniu IoT Edge. |

O ile nie określono inaczej, procedury przedstawione w tym artykule nie eksplorują wszystkich funkcji dostępnych za pomocą parametrów zadań. Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Wersja zadania](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Zaawansowane** — Jeśli ma to zastosowanie, Określ moduły, które nie mają być tworzone.
* [Opcje kontrolki](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Zmienne środowiskowe](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Zmienne wyjściowe](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Wymagania wstępne

* Repozytorium Azure Repos. Jeśli go nie masz, możesz [utworzyć nowe repozytorium Git w projekcie](/azure/devops/repos/git/create-new-repo). W tym artykule utworzyliśmy repozytorium o nazwie **IoTEdgeRepo**.
* Rozwiązanie IoT Edge zostało zatwierdzone i wypchnięte do repozytorium. Jeśli chcesz utworzyć nowe przykładowe rozwiązanie do testowania tego artykułu, postępuj zgodnie z instrukcjami w temacie [programowanie i debugowanie modułów w Visual Studio Code](how-to-vs-code-develop-module.md) lub [opracowywanie i debugowanie modułów w języku C# w programie Visual Studio](./how-to-visual-studio-develop-module.md). W tym artykule utworzyliśmy rozwiązanie w naszym repozytorium o nazwie **IoTEdgeSolution**, które ma kod dla modułu o nazwie **filtermodule**.

   W tym artykule wystarczy, że jest to folder rozwiązania utworzony przez szablony IoT Edge w Visual Studio Code lub Visual Studio. Nie musisz kompilować, wypchnięciować, wdrażać ani debugować tego kodu przed kontynuowaniem. Te procesy zostaną skonfigurowane w Azure Pipelines.

   Jeśli tworzysz nowe rozwiązanie, najpierw Sklonuj repozytorium lokalnie. Następnie podczas tworzenia rozwiązania można utworzyć je bezpośrednio w folderze repozytorium. W tym miejscu możesz łatwo zatwierdzić i wypchnąć nowe pliki.

* Rejestr kontenerów, w którym można wypchnąć obrazy modułów. Możesz użyć [Azure Container Registry](../container-registry/index.yml) lub rejestru innej firmy.
* Aktywne [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) Azure z co najmniej dwoma IoT Edge urządzeniami do testowania oddzielnych etapów wdrożenia testowego i produkcyjnego. Możesz skorzystać z artykułów szybkiego startu, aby utworzyć urządzenie IoT Edge w systemie [Linux](quickstart-linux.md) lub [Windows](quickstart.md)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Tworzenie potoku kompilacji na potrzeby ciągłej integracji

W tej sekcji utworzysz nowy potok kompilacji. Potok można skonfigurować tak, aby uruchamiał się automatycznie po zaewidencjonowaniu wszelkich zmian w przykładowym rozwiązaniu IoT Edge i publikowaniu dzienników kompilacji.

1. Zaloguj się do organizacji usługi Azure DevOps ( `https://dev.azure.com/{your organization}` ) i Otwórz projekt, który zawiera repozytorium rozwiązań IoT Edge.

    ![Otwórz projekt DevOps](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. Z menu po lewej stronie w projekcie wybierz pozycję **potoki**. Wybierz pozycję **Utwórz potok** na środku strony. Lub, jeśli masz już potoki kompilacji, wybierz przycisk **Nowy potok** w prawym górnym rogu.

    ![Tworzenie nowego potoku kompilacji](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. W dolnej części strony **gdzie jest Twój kod?** wybierz pozycję **Użyj klasycznego edytora**. Jeśli chcesz użyć YAML do tworzenia potoków kompilacji projektu, zapoznaj się z [przewodnikiem YAML](how-to-continuous-integration-continuous-deployment.md).

    ![Wybierz pozycję Użyj klasycznego edytora](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Postępuj zgodnie z monitami, aby utworzyć potok.

   1. Podaj informacje źródłowe dla nowego potoku kompilacji. Wybierz pozycję **Azure Repos git** jako źródło, a następnie wybierz projekt, repozytorium i gałąź, w której znajduje się kod rozwiązania IoT Edge. Następnie wybierz pozycję **Kontynuuj**.

      ![Wybierz źródło potoku](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Wybierz **puste zadanie** zamiast szablonu.

      ![Zacznij od pustego zadania dla potoku kompilacji](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. Po utworzeniu potoku nastąpi przejście do edytora potoku. Tutaj można zmienić nazwę potoku, pulę agentów i specyfikację agenta.

   Można wybrać pulę hostowaną przez firmę Microsoft lub zarządzaną przez siebie pulę.

   W opisie potoku Wybierz poprawną specyfikację agenta opartą na platformie docelowej:

   * Jeśli chcesz skompilować moduły w programie platform amd64 dla kontenerów systemu Linux, wybierz pozycję **Ubuntu-16,04**

   * Jeśli chcesz skompilować moduły na platformie amd64 dla kontenerów systemu Windows 1809, musisz [skonfigurować samoobsługowy Agent w systemie Windows](/azure/devops/pipelines/agents/v2-windows).

   * Jeśli chcesz skompilować moduły na platformie arm32v7 lub arm64 dla kontenerów systemu Linux, musisz [skonfigurować agenta samoobsługowego w systemie Linux](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent).

    ![Konfigurowanie specyfikacji agenta kompilacji](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. Potok jest wstępnie skonfigurowany przy użyciu zadania o nazwie **zadanie agenta 1**. Wybierz znak plus ( **+** ), aby dodać cztery zadania do zadania: **Azure IoT Edge** dwa razy, **Skopiuj pliki** i **Opublikuj artefakty kompilacji** jeden raz. Wyszukaj każde zadanie i umieść kursor nad nazwą zadania, aby wyświetlić przycisk **Dodaj** .

   ![Dodawanie Azure IoT Edge zadania](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Po dodaniu wszystkich czterech zadań zadanie agenta będzie wyglądać podobnie do poniższego przykładu:

   ![Cztery zadania w potoku kompilacji](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Wybierz pierwsze zadanie **Azure IoT Edge** , aby je edytować. To zadanie kompiluje wszystkie moduły w rozwiązaniu z określoną platformą docelową. Edytuj zadanie przy użyciu następujących wartości:

    | Parametr | Opis |
    | --- | --- |
    | Nazwa wyświetlana | Nazwa wyświetlana jest automatycznie aktualizowana, gdy pole akcji ulega zmianie. |
    | Akcja | Wybierz pozycję **obrazy modułu kompilacji**. |
    | .template.jspliku | Wybierz wielokropek (**...**) i przejdź do **deployment.template.js** w pliku w repozytorium zawierającym rozwiązanie IoT Edge. |
    | Platforma domyślna | Wybierz odpowiedni system operacyjny dla modułów w oparciu o Twoje urządzenie IoT Edge. |
    | Zmienne wyjściowe | Podaj nazwę odwołania, która ma zostać skojarzona z ścieżką pliku, w której zostanie wygenerowany deployment.jspliku, na przykład **Edge**. |

   Te konfiguracje używają repozytorium obrazów i znacznika, które są zdefiniowane w `module.json` pliku jako nazwa i oznakowanie obrazu modułu. **Obrazy modułu kompilacji** ułatwiają także zastępowanie zmiennych wartościami dokładnie zdefiniowanymi w `module.json` pliku. W programie Visual Studio lub Visual Studio Code określasz rzeczywistą wartość w `.env` pliku. W Azure Pipelines ustawiasz wartość na karcie **zmienne potoku** . Wybierz kartę **zmienne** w menu Edytor potoku, a następnie skonfiguruj nazwę i wartość w następujący sposób:

    * **ACR_ADDRESS**: Azure Container Registry wartość **serwera logowania** . Serwer logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal.

    Jeśli w projekcie znajdują się inne zmienne, można na tej karcie określić nazwę i wartość. w **obrazach modułu kompilacji** są rozpoznawane tylko zmienne, które są w `${VARIABLE}` formacie. Upewnij się, że używasz tego formatu w `**/module.json` plikach.

8. Wybierz drugie zadanie **Azure IoT Edge** , aby je edytować. To zadanie powoduje wypchnięcie wszystkich obrazów modułu do rejestru kontenerów, które zostały wybrane.

    | Parametr | Opis |
    | --- | --- |
    | Nazwa wyświetlana | Nazwa wyświetlana jest automatycznie aktualizowana, gdy pole akcji ulega zmianie. |
    | Akcja | Wybierz pozycję **wypychanie obrazów modułu**. |
    | Typ rejestru kontenera | Użyj typu domyślnego: `Azure Container Registry` . |
    | Subskrypcja platformy Azure | Wybierz subskrypcję. |
    | Azure Container Registry | Wybierz typ rejestru kontenerów, który ma być używany do przechowywania obrazów modułu. Formularz zmienia się w zależności od wybranego typu rejestru. W przypadku wybrania **Azure Container Registry** Użyj listy rozwijanej, aby wybrać subskrypcję platformy Azure i nazwę rejestru kontenerów. W przypadku wybrania opcji **ogólne Container Registry** wybierz pozycję **Nowy** , aby utworzyć połączenie usługi rejestru. |
    | .template.jspliku | Wybierz wielokropek (**...**) i przejdź do **deployment.template.js** w pliku w repozytorium zawierającym rozwiązanie IoT Edge. |
    | Platforma domyślna | Wybierz odpowiedni system operacyjny dla modułów w oparciu o Twoje urządzenie IoT Edge. |
    | Dodawanie poświadczeń rejestru do manifestu wdrożenia | Określ wartość true, aby dodać poświadczenia rejestru umożliwiające wypychanie obrazów platformy Docker do manifestu wdrożenia. |

   Jeśli masz wiele rejestrów kontenerów do hostowania obrazów modułów, musisz zduplikować to zadanie, wybrać inny rejestr kontenerów i użyć opcji **Pomiń moduły** w ustawieniach **zaawansowanych** , aby pominąć obrazy, które nie są przeznaczone dla tego konkretnego rejestru.

9. Wybierz zadanie **Kopiuj pliki** , aby je edytować. To zadanie służy do kopiowania plików do katalogu przemieszczania artefaktu.

    | Parametr | Opis |
    | --- | --- |
    | Nazwa wyświetlana | Użyj nazwy domyślnej lub Dostosuj |
    | Folder źródłowy | Folder zawierający pliki, które mają zostać skopiowane. |
    | Zawartość | Dodaj dwie linie: `deployment.template.json` i `**/module.json` . Te dwa pliki służą jako dane wejściowe do generowania manifestu wdrażania IoT Edge. |
    | Folder docelowy | Określ zmienną `$(Build.ArtifactStagingDirectory)` . Zobacz temat [Tworzenie zmiennych](/azure/devops/pipelines/build/variables#build-variables) , aby dowiedzieć się więcej o opisie. |

10. Wybierz zadanie **Opublikuj artefakty kompilacji** , aby je edytować. Podaj ścieżkę katalogu przemieszczania artefaktu do zadania, aby można było opublikować ścieżkę w potoku wydania.

    | Parametr | Opis |
    | --- | --- |
    | Nazwa wyświetlana | Użyj nazwy domyślnej lub Dostosuj. |
    | Ścieżka do publikowania | Określ zmienną `$(Build.ArtifactStagingDirectory)` . Aby dowiedzieć się więcej, zobacz temat [Tworzenie zmiennych](/azure/devops/pipelines/build/variables#build-variables) . |
    | Nazwa artefaktu | Użyj nazwy domyślnej: **upuść** |
    | Lokalizacja publikowania artefaktu | Użyj domyślnej lokalizacji: **Azure Pipelines** |

11. Otwórz kartę **wyzwalacze** i zaznacz pole wyboru **Włącz integrację ciągłą**. Upewnij się, że gałąź zawierająca kod jest uwzględniona.

    ![Włącz wyzwalacz integracji ciągłej](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Wybierz pozycję **Zapisz** na liście rozwijanej **Zapisz & kolejki** .

Ten potok jest teraz skonfigurowany do automatycznego uruchamiania podczas wypychania nowego kodu do repozytorium. Ostatnim zadaniem, opublikowanie artefaktów potoku, jest wyzwalanie potoku wydania. Przejdź do następnej sekcji, aby skompilować potok wersji.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Jeśli chcesz używać **wdrożeń warstwowych** w potoku, wdrożenia warstwowe nie są jeszcze obsługiwane w Azure IoT Edge zadaniach w usłudze Azure DevOps.
>
>Można jednak użyć [zadania interfejsu wiersza polecenia platformy Azure w usłudze Azure DevOps](/azure/devops/pipelines/tasks/deploy/azure-cli) , aby utworzyć wdrożenie jako wdrożenie warstwowe. W przypadku wartości **skryptu wbudowanego** można użyć [polecenia AZ IoT Edge Deployment Create](/cli/azure/ext/azure-iot/iot/edge/deployment):
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Następne kroki

* Przykład IoT Edge DevOps Best Practices w [usłudze Azure DevOps Starter dla IoT Edge](how-to-devops-starter.md)
* Informacje na temat wdrażania IoT Edge w temacie [omówienie IoT Edge wdrożeń dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md)
* Zapoznaj się z instrukcjami tworzenia, aktualizowania lub usuwania wdrożenia w sekcji [wdrażanie i monitorowanie modułów IoT Edge](how-to-deploy-at-scale.md)w odpowiedniej skali.