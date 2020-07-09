---
title: Potok ciągłej integracji/ciągłego wdrażania z Azure DevOps Projects-Azure IoT Edge | Microsoft Docs
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Ułatwia ona uruchamianie wybranej aplikacji Azure IoT Edge w kilku prostych krokach.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c7598aafea85e91f28233645d3d71bf85bf45425
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82130993"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Utwórz potok ciągłej integracji/ciągłego wdrażania dla IoT Edge z Azure DevOps Projects

Skonfiguruj ciągłą integrację (CI) i ciągłe dostarczanie (CD) dla aplikacji IoT Edge z DevOps Projects. Usługa DevOps Projects ułatwia początkową konfigurację potoku kompilacji i wydania w usłudze Azure Pipelines.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure DevOps. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa DevOps Projects tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się do [Portalu Microsoft Azure](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**, a następnie wyszukaj pozycję **DevOps Projects**.  

1. Wybierz pozycję **Utwórz**.

## <a name="create-a-new-application-pipeline"></a>Tworzenie nowego potoku aplikacji

1. Moduły Azure IoT Edge można napisać w [językach C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) i [Java](tutorial-java-module.md). Wybierz preferowany język, aby rozpocząć nową aplikację: **.NET**, **Node.js**, **Python**, **C**lub **Java**. Wybierz przycisk **Dalej**, aby kontynuować.

   ![Wybierz język, aby utworzyć nową aplikację](./media/how-to-devops-project/select-language.png)

2. Wybierz pozycję **prosta IoT** jako strukturę aplikacji, a następnie wybierz pozycję **dalej**.

   ![Wybierz prostą strukturę IoT Framework](media/how-to-devops-project/select-iot.png)

3. Wybierz **IoT Edge** jako usługę platformy Azure, która wdraża aplikację, a następnie wybierz przycisk **dalej**.

   ![Wybierz usługę IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Utwórz bezpłatnie nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

   1. Podaj nazwę dla projektu.

   2. Wybierz organizację usługi Azure DevOps. Jeśli nie masz istniejącej organizacji, wybierz opcję **dodatkowe ustawienia** , aby utworzyć nowe.

   3. Wybierz swoją subskrypcję platformy Azure.

   4. Użyj nazwy IoT Hub wygenerowanej przez nazwę projektu lub podaj swój własny.

   5. Zaakceptuj lokalizację domyślną lub wybierz ją blisko siebie.

   6. Wybierz pozycję **dodatkowe ustawienia** , aby skonfigurować zasoby platformy Azure, które DevOps projects tworzyć w Twoim imieniu.

   7. Wybierz pozycję **gotowe** , aby zakończyć tworzenie projektu.

   ![Nazwa i tworzenie aplikacji](media/how-to-devops-project/select-devops.png)

Po kilku minutach w witrynie Azure Portal zostanie wyświetlony pulpit nawigacyjny usługi DevOps Projects. Wybierz nazwę projektu, aby zobaczyć postęp. Może być konieczne odświeżenie strony. Przykładowa aplikacja IoT Edge jest skonfigurowana w repozytorium w organizacji usługi Azure DevOps, jest wykonywana kompilacja, a aplikacja jest wdrażana na urządzeniu IoT Edge. Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania i aplikację na platformie Azure.

   ![Wyświetlanie aplikacji w Azure Portal](./media/how-to-devops-project/devops-portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

DevOps Projects utworzyć repozytorium git dla projektu w Azure Repos. W tej sekcji przeglądasz repozytorium i wprowadzasz zmiany w kodzie aplikacji.

1. Aby przejść do repozytorium utworzonego dla projektu, wybierz pozycję **repozytoria** w menu na pulpicie nawigacyjnym projektu.  

   ![Wyświetl repozytorium wygenerowane w Azure Repos](./media/how-to-devops-project/view-repositories.png)

2. Poniższe kroki umożliwiają wprowadzenie zmian w kodzie przy użyciu przeglądarki sieci Web. Jeśli chcesz sklonować repozytorium lokalnie, wybierz pozycję **Klonuj** w prawym górnym rogu okna. Użyj podanego adresu URL, aby sklonować repozytorium Git w Visual Studio Code lub preferowanym narzędziu programistycznym.

3. Repozytorium zawiera już kod dla modułu o nazwie **FilterModule** na podstawie języka aplikacji wybranego w procesie tworzenia. Otwórz plik **modules/FilterModule/module.jsw** pliku.

   ![Otwórz module.jsw pliku w Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Zwróć uwagę, że ten plik używa [zmiennych kompilacji usługi Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) w parametrze **Version** . Ta konfiguracja zapewnia, że nowa wersja modułu zostanie utworzona za każdym razem, gdy zostanie uruchomiona nowa kompilacja.

## <a name="examine-the-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania

W poprzednich sekcjach Azure DevOps Projects automatycznie konfigurować pełny potok ciągłej integracji/ciągłego dostarczania dla aplikacji IoT Edge. Teraz Przejrzyj i Dostosuj potok zgodnie z wymaganiami. Wykonaj następujące kroki, aby zaznajomić się z usługą Azure DevOps Build i potokiem wydań.

1. Aby wyświetlić potoki kompilacji w projekcie DevOps, wybierz pozycję **potoki kompilacji** w menu pulpitu nawigacyjnego projektu. Ten link otwiera kartę przeglądarki i potok kompilacji usługi Azure DevOps dla nowego projektu.

   ![Wyświetlanie potoków kompilacji w Azure Pipelines](./media/how-to-devops-project/view-build-pipelines.png)

2. Wybierz pozycję **Edytuj**.

    ![Edytuj potok kompilacji](media/how-to-devops-project/click-edit-button.png)

3. W panelu, który zostanie otwarty, można przejrzeć zadania, które są wykonywane w momencie uruchomienia potoku kompilacji. Potok kompilacji wykonuje różne zadania, takie jak pobieranie źródeł z repozytorium git, kompilowanie obrazów modułów IoT Edge, wypychanie modułów IoT Edge do rejestru kontenerów i publikowanie danych wyjściowych używanych do wdrożeń. Aby dowiedzieć się więcej o Azure IoT Edge zadaniach w usłudze Azure DevOps, zobacz [konfigurowanie Azure Pipelines do ciągłej integracji](how-to-ci-cd.md#configure-continuous-integration).

4. Wybierz nagłówek **potoku** w górnej części potoku kompilacji, aby otworzyć Szczegóły potoku. Zmień nazwę potoku kompilacji na bardziej opisową.

   ![Edytuj szczegóły potoku](./media/how-to-devops-project/edit-build-pipeline.png)

5. Wybierz pozycję **zapisz & kolejkę**, a następnie wybierz pozycję **Zapisz**.

6. Wybierz pozycję **wyzwalacze** z menu potok kompilacji. Usługa DevOps Projects automatycznie utworzyła wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację.  Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

7. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

8. Wybierz pozycję **historia**. Panel Historia zawiera dziennik inspekcji ostatnich zmian w kompilacji. Usługa Azure Pipelines śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

9. Po zakończeniu eksplorowania potoku kompilacji przejdź do odpowiedniego potoku wydania. Wybierz pozycję **wersje** w obszarze **potoki**, a następnie wybierz pozycję **Edytuj** , aby wyświetlić szczegóły potoku.

    ![Wyświetl potok wydania](media/how-to-devops-project/release-pipeline.png)

10. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Źródłem tego artefaktu jest dane wyjściowe potoku kompilacji, które zostały sprawdzone w poprzednich krokach.

11. Obok ikony **upuszczania** wybierz **wyzwalacz ciągłego wdrażania** , który wygląda jak piorun. Ten potok wersji włączył wyzwalacz, który uruchamia wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania.  

12. W menu potoku wydania wybierz pozycję **zadania** , a następnie wybierz etap **dev** na liście rozwijanej. DevOps Projects utworzyć etap wydania, który tworzy Centrum IoT Hub, tworzy urządzenie IoT Edge w tym centrum wdraża przykładowy moduł z potoku kompilacji i inicjuje maszynę wirtualną do uruchomienia jako urządzenie IoT Edge. Aby dowiedzieć się więcej o Azure IoT Edge zadaniach dla dysku CD, zobacz [konfigurowanie Azure Pipelines do ciągłego wdrażania](how-to-ci-cd.md#configure-continuous-deployment).

    ![Wyświetlanie zadań ciągłego wdrażania](media/how-to-devops-project/dev-release.png)

13. Po prawej stronie wybierz pozycję **Wyświetl wydania**. Ten widok przedstawia historię wersji.

14. Wybierz nazwę wydania, aby wyświetlić więcej informacji na jego temat.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy usługa Azure App Service i inne utworzone powiązane zasoby nie będą już potrzebne, możesz je usunąć. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z zadaniami Azure IoT Edge w usłudze Azure DevOps w ramach [ciągłej integracji i ciągłego wdrażania w Azure IoT Edge](how-to-ci-cd.md)
* Informacje na temat wdrażania IoT Edge w temacie [omówienie IoT Edge wdrożeń dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md)
* Zapoznaj się z instrukcjami tworzenia, aktualizowania lub usuwania wdrożenia w sekcji [wdrażanie i monitorowanie modułów IoT Edge](how-to-deploy-at-scale.md)w odpowiedniej skali.
