---
title: Ciągła integracja i ciągłe wdrażanie do Azure IoT Edge urządzeń — Azure IoT Edge
description: Skonfiguruj ciągłą integrację i ciągłe wdrażanie za pomocą YAML Azure IoT Edge z usługą Azure DevOps, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 444ab8ccfe5a8441a4fd7d280e33d8e929d9387d
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435896"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Ciągła integracja i ciągłe wdrażanie do Azure IoT Edge urządzeń

Możesz łatwo przyjmować DevOps z aplikacjami Azure IoT Edge przy użyciu wbudowanych zadań Azure IoT Edge w Azure Pipelines. W tym artykule pokazano, jak korzystać z funkcji ciągłej integracji i ciągłego wdrażania Azure Pipelines, aby szybko i wydajnie kompilować, testować i wdrażać aplikacje w Azure IoT Edge za pomocą YAML. Alternatywnie można [użyć edytora klasycznego](how-to-continuous-integration-continuous-deployment-classic.md).

![Diagramy — gałęzie CI i CD na potrzeby programowania i produkcji](./media/how-to-continuous-integration-continuous-deployment/model.png)

W tym artykule dowiesz się, jak za pomocą wbudowanych [Azure IoT Edge zadań](/azure/devops/pipelines/tasks/build/azure-iot-edge) dla Azure Pipelines utworzyć potoki kompilacji i wydania dla rozwiązania IoT Edge. Każde zadanie Azure IoT Edge dodane do potoku implementuje jedną z następujących czterech akcji:

 | Akcja | Opis |
 | --- | --- |
 | Kompilowanie obrazów modułów | Pobiera IoT Edge kod rozwiązania i kompiluje obrazy kontenerów.|
 | Wypychanie obrazów modułów | Wypychanie obrazów modułów do określonego rejestru kontenerów. |
 | Generuj manifest wdrożenia | Przyjmuje deployment.template.jsw pliku i zmiennych, a następnie generuje plik manifestu wdrożenia Final IoT Edge. |
 | Wdrażanie na urządzeniach usługi IoT Edge | Tworzy IoT Edge wdrożenia na co najmniej jednym urządzeniu IoT Edge. |

O ile nie określono inaczej, procedury przedstawione w tym artykule nie eksplorują wszystkich funkcji dostępnych za pomocą parametrów zadań. Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Wersja zadania](/azure/devops/pipelines/process/tasks?tabs=yaml#task-versions)
* **Zaawansowane** — Jeśli ma to zastosowanie, Określ moduły, które nie mają być tworzone.
* [Opcje kontrolki](/azure/devops/pipelines/process/tasks?tabs=yaml#task-control-options)
* [Zmienne środowiskowe](/azure/devops/pipelines/process/variables?tabs=yaml#environment-variables)
* [Zmienne wyjściowe](/azure/devops/pipelines/process/variables?tabs=yaml#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Wymagania wstępne

* Repozytorium Azure Repos. Jeśli go nie masz, możesz [utworzyć nowe repozytorium Git w projekcie](/azure/devops/repos/git/create-new-repo). W tym artykule utworzyliśmy repozytorium o nazwie **IoTEdgeRepo**.
* Rozwiązanie IoT Edge zostało zatwierdzone i wypchnięte do repozytorium. Jeśli chcesz utworzyć nowe przykładowe rozwiązanie do testowania tego artykułu, postępuj zgodnie z instrukcjami w temacie [programowanie i debugowanie modułów w Visual Studio Code](how-to-vs-code-develop-module.md) lub [opracowywanie i debugowanie modułów w języku C# w programie Visual Studio](./how-to-visual-studio-develop-module.md). W tym artykule utworzyliśmy rozwiązanie w naszym repozytorium o nazwie **IoTEdgeSolution**, które ma kod dla modułu o nazwie **filtermodule**.

   W tym artykule wystarczy, że jest to folder rozwiązania utworzony przez szablony IoT Edge w Visual Studio Code lub Visual Studio. Nie musisz kompilować, wypchnięciować, wdrażać ani debugować tego kodu przed kontynuowaniem. Te procesy zostaną skonfigurowane w Azure Pipelines.

   Jeśli tworzysz nowe rozwiązanie, najpierw Sklonuj repozytorium lokalnie. Następnie podczas tworzenia rozwiązania można utworzyć je bezpośrednio w folderze repozytorium. W tym miejscu możesz łatwo zatwierdzić i wypchnąć nowe pliki.

* Rejestr kontenerów, w którym można wypchnąć obrazy modułów. Możesz użyć [Azure Container Registry](../container-registry/index.yml) lub rejestru innej firmy.
* Aktywne [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) Azure z co najmniej dwoma IoT Edge urządzeniami do testowania oddzielnych etapów wdrożenia testowego i produkcyjnego. Możesz skorzystać z artykułów szybkiego startu, aby utworzyć urządzenie IoT Edge w systemie [Linux](quickstart-linux.md) lub [Windows](quickstart.md)

Aby uzyskać więcej informacji o korzystaniu z Azure Repos, zobacz [udostępnianie kodu za pomocą programu Visual Studio i Azure Repos](/azure/devops/repos/git/share-your-code-in-git-vs)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Tworzenie potoku kompilacji na potrzeby ciągłej integracji

W tej sekcji utworzysz nowy potok kompilacji. Potok można skonfigurować tak, aby uruchamiał się automatycznie po zaewidencjonowaniu wszelkich zmian w przykładowym rozwiązaniu IoT Edge i publikowaniu dzienników kompilacji.

1. Zaloguj się do organizacji usługi Azure DevOps ( `https://dev.azure.com/{your organization}` ) i Otwórz projekt, który zawiera repozytorium rozwiązań IoT Edge.

   ![Otwórz projekt DevOps](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. Z menu po lewej stronie w projekcie wybierz pozycję **potoki**. Wybierz pozycję **Utwórz potok** na środku strony. Lub, jeśli masz już potoki kompilacji, wybierz przycisk **Nowy potok** w prawym górnym rogu.

    ![Tworzenie nowego potoku kompilacji przy użyciu przycisku Nowy potok](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. Na stronie **gdzie masz swój kod?** wybierz pozycję **Azure Repos git `YAML`**. Jeśli chcesz użyć klasycznego edytora, aby utworzyć potoki kompilacji projektu, zobacz [klasyczny Przewodnik edytora](how-to-continuous-integration-continuous-deployment-classic.md).

4. Wybierz repozytorium, dla którego tworzysz potok.

    ![Wybierz repozytorium dla potoku kompilacji](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. Na stronie **Konfiguruj potok** wybierz pozycję **potok początkowy**. Jeśli masz istniejący Azure Pipelines plik YAML, którego chcesz użyć do utworzenia tego potoku, możesz wybrać **istniejący plik Azure PIPELINES YAML** i podać gałąź i ścieżkę do pliku.

    ![Wybierz pozycję potok początkowy lub istniejący plik Azure Pipelines YAML, aby rozpocząć potok kompilacji](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. Na stronie **Przejrzyj swoją YAMLę potoku** możesz kliknąć nazwę domyślną, `azure-pipelines.yml` Aby zmienić nazwę pliku konfiguracji potoku.

   Wybierz pozycję **Pokaż asystenta** , aby otworzyć paletę **zadania** .

    ![Wybierz pozycję Pokaż asystenta, aby otworzyć paletę zadań](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. Aby dodać zadanie, umieść kursor na końcu YAML lub wszędzie tam, gdzie chcesz dodać instrukcje dla zadania. Wyszukaj i wybierz **Azure IoT Edge**. Wypełnij parametry zadania w następujący sposób. Następnie wybierz pozycję **Dodaj**.

   | Parametr | Opis |
   | --- | --- |
   | Akcja | Wybierz pozycję **obrazy modułu kompilacji**. |
   | .template.jspliku | Podaj ścieżkę do **deployment.template.jsw** pliku w repozytorium zawierającym rozwiązanie IoT Edge. |
   | Platforma domyślna | Wybierz odpowiedni system operacyjny dla modułów w oparciu o Twoje urządzenie IoT Edge. |

    ![Użyj palety zadań, aby dodać zadania do potoku](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > Po dodaniu każdego zadania edytor automatycznie podświetla dodane wiersze. Aby zapobiec przypadkowemu zastąpieniu, usuń zaznaczenie wierszy i podaj nowe miejsce dla następnego zadania przed dodaniem kolejnych zadań.

8. Powtórz ten proces, aby dodać trzy więcej zadań z następującymi parametrami:

   * Zadanie: **Azure IoT Edge**

       | Parametr | Opis |
       | --- | --- |
       | Akcja | Wybierz pozycję **wypychanie obrazów modułu**. |
       | Typ rejestru kontenera | Użyj typu domyślnego: **Azure Container Registry**. |
       | Subskrypcja platformy Azure | Wybierz subskrypcję. |
       | Azure Container Registry | Wybierz rejestr, który ma być używany dla potoku. |
       | .template.jspliku | Podaj ścieżkę do **deployment.template.jsw** pliku w repozytorium zawierającym rozwiązanie IoT Edge. |
       | Platforma domyślna | Wybierz odpowiedni system operacyjny dla modułów w oparciu o Twoje urządzenie IoT Edge. |

   * Zadanie: **Kopiowanie plików**

       | Parametr | Opis |
       | --- | --- |
       | Folder źródłowy | Folder źródłowy do skopiowania. Wartość pusta jest katalogiem głównym repozytorium. Użyj zmiennych, jeśli pliki nie znajdują się w repozytorium. Przykład: `$(agent.builddirectory)`.
       | Zawartość | Dodaj dwie linie: `deployment.template.json` i `**/module.json` . |
       | Folder docelowy | Określ zmienną `$(Build.ArtifactStagingDirectory)` . Zobacz temat [Tworzenie zmiennych](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) , aby dowiedzieć się więcej o opisie. |

   * Zadanie: **Publikowanie artefaktów kompilacji**

       | Parametr | Opis |
       | --- | --- |
       | Ścieżka do publikowania | Określ zmienną `$(Build.ArtifactStagingDirectory)` . Zobacz temat [Tworzenie zmiennych](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) , aby dowiedzieć się więcej o opisie. |
       | Nazwa artefaktu | Określ nazwę domyślną: `drop` |
       | Lokalizacja publikowania artefaktu | Użyj domyślnej lokalizacji: `Azure Pipelines` |

9. Wybierz pozycję **Zapisz** z listy rozwijanej **Zapisz i uruchom** w prawym górnym rogu.

10. Wyzwalacz integracji ciągłej jest domyślnie włączony dla potoku YAML. Jeśli chcesz edytować te ustawienia, wybierz potok, a następnie kliknij przycisk **Edytuj** w prawym górnym rogu. Wybierz pozycję **więcej akcji** obok przycisku **Uruchom** w prawym górnym rogu i przejdź do pozycji **wyzwalacze**. **Ciągła integracja** jest wyświetlana jako włączona pod nazwą potoku. Jeśli chcesz zobaczyć szczegóły wyzwalacza, zaznacz pole wyboru **Zastąp wyzwalacz ciągła integracja z usługą YAML w tym miejscu** .

    ![Aby przejrzeć ustawienia wyzwalacza potoku, zobacz Wyzwalacze w obszarze więcej akcji](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

Przejdź do następnej sekcji, aby skompilować potok wersji.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Następne kroki

* Przykład IoT Edge DevOps Best Practices w [usłudze Azure DevOps Starter dla IoT Edge](how-to-devops-starter.md)
* Informacje na temat wdrażania IoT Edge w temacie [omówienie IoT Edge wdrożeń dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md)
* Zapoznaj się z instrukcjami tworzenia, aktualizowania lub usuwania wdrożenia w sekcji [wdrażanie i monitorowanie modułów IoT Edge](how-to-deploy-at-scale.md)w odpowiedniej skali.