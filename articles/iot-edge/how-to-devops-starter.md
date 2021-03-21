---
title: Potok ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps Starter — Azure IoT Edge | Microsoft Docs
description: Usługa Azure DevOps Starter ułatwia rozpoczęcie pracy z platformą Azure. Ułatwia ona uruchamianie wybranej aplikacji Azure IoT Edge w kilku prostych krokach.
author: kgremban
ms.author: kgremban
ms.date: 08/25/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8a23041a714089c447ab35ee05aae0de0c9c33d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201154"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-starter"></a>Tworzenie potoku ciągłej integracji/ciągłego wdrażania dla IoT Edge przy użyciu usługi Azure DevOps Starter

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Skonfiguruj ciągłą integrację (CI) i ciągłe dostarczanie (CD) dla aplikacji IoT Edge z DevOps Projects. DevOps Starter upraszcza konfigurację początkową potoku kompilacji i wydania w Azure Pipelines.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

DevOps Starter tworzy potok ciągłej integracji/ciągłego wdrażania na platformie Azure DevOps. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. DevOps Starter tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się do witryny [Microsoft Azure Portal](https://portal.azure.com).

1. W lewym okienku wybierz pozycję **Utwórz zasób**, a następnie wyszukaj ciąg **DevOps Starter**.  

1. Wybierz przycisk **Utwórz**.

1. Domyślnie DevOps Starter jest skonfigurowany przy użyciu usługi GitHub. Aby skorzystać z funkcji w tej metodzie, przełącz DevOps Starter na konfigurację przy użyciu usługi Azure DevOps. Skorzystaj z linku **Zmień ustawienia w tym miejscu** .

   ![Wybierz pozycję Zmień ustawienia tutaj, aby przełączyć się z witryny GitHub na platformę Azure DevOps](./media/how-to-devops-starter/create-with-github-change-settings.png)

1. W prawym okienku wybierz kafelek **Azure DevOps** , a następnie wybierz pozycję **gotowe**.

   ![Wybierz pozycję Azure DevOps, aby skonfigurować DevOps Starter](./media/how-to-devops-starter/select-azure-devops.png)

   Powinieneś teraz zobaczyć, że DevOps Starter jest konfigurowany za pomocą usługi Azure DevOps.

## <a name="create-a-new-application-pipeline"></a>Tworzenie nowego potoku aplikacji

1. Moduły Azure IoT Edge można napisać w [językach C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) i [Java](tutorial-java-module.md). Wybierz preferowany język, aby rozpocząć nową aplikację: **.NET**, **Node.js**, **Python**, **C** lub **Java**. Wybierz przycisk **Dalej**, aby kontynuować.

   ![Wybierz język, aby utworzyć nową aplikację](./media/how-to-devops-starter/select-language.png)

2. Wybierz pozycję **prosta IoT** jako strukturę aplikacji, a następnie wybierz pozycję **dalej**.

   ![Wybierz prostą strukturę IoT Framework](media/how-to-devops-starter/select-iot.png)

3. Wybierz **IoT Edge** jako usługę platformy Azure, która wdraża aplikację, a następnie wybierz przycisk **dalej**.

   ![Wybierz usługę IoT Edge](media/how-to-devops-starter/select-iot-edge.png)

4. Utwórz bezpłatnie nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

   1. Podaj nazwę dla projektu.

   2. Wybierz organizację usługi Azure DevOps. Jeśli nie masz istniejącej organizacji, wybierz opcję **dodatkowe ustawienia** , aby utworzyć nowe.

   3. Wybierz swoją subskrypcję platformy Azure.

   4. Użyj nazwy IoT Hub wygenerowanej przez nazwę projektu lub podaj swój własny.

   5. Zaakceptuj lokalizację domyślną lub wybierz ją blisko siebie.

   6. Wybierz pozycję **dodatkowe ustawienia** , aby skonfigurować zasoby platformy Azure, które DevOps Starter w Twoim imieniu.

   7. Wybierz pozycję **gotowe** , aby zakończyć tworzenie projektu.

   ![Nazwa i tworzenie projektu](media/how-to-devops-starter/create-project.png)

Po kilku minutach DevOps początkowy pulpit nawigacyjny zostanie wyświetlony w Azure Portal. Wybierz nazwę projektu, aby zobaczyć postęp. Może być konieczne odświeżenie strony. Przykładowa aplikacja IoT Edge jest skonfigurowana w repozytorium w organizacji usługi Azure DevOps, jest wykonywana kompilacja, a aplikacja jest wdrażana na urządzeniu IoT Edge. Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania i aplikację na platformie Azure.

   ![Wyświetl projekt w Azure Portal](./media/how-to-devops-starter/portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

DevOps Starter utworzył repozytorium git dla projektu w Azure Repos. W tej sekcji przeglądasz repozytorium i wprowadzasz zmiany w kodzie aplikacji.

1. Aby przejść do repozytorium utworzonego dla projektu, wybierz pozycję **repozytoria** w menu na pulpicie nawigacyjnym projektu. To łącze powoduje otwarcie karty przeglądarki i repozytorium Azure DevOps dla nowego projektu.

   ![Wyświetl repozytorium wygenerowane w Azure Repos](./media/how-to-devops-starter/view-repositories.png)

   > [!NOTE]
   > Poniższe kroki umożliwiają wprowadzenie zmian w kodzie przy użyciu przeglądarki sieci Web. Jeśli chcesz sklonować repozytorium lokalnie, wybierz pozycję **Klonuj** w prawym górnym rogu okna. Użyj podanego adresu URL, aby sklonować repozytorium Git w Visual Studio Code lub preferowanym narzędziu programistycznym.

2. Repozytorium zawiera już kod dla modułu o nazwie **FilterModule** na podstawie języka aplikacji wybranego w procesie tworzenia. Otwórz plik **modules/FilterModule/module.jsw** pliku.

   ![Otwórz module.jsw pliku w Azure Repos](./media/how-to-devops-starter/open-module-json.png)

3. Zwróć uwagę, że ten plik używa [zmiennych kompilacji usługi Azure DevOps](/azure/devops/pipelines/build/variables#build-variables) w parametrze **Version** . Ta konfiguracja zapewnia, że nowa wersja modułu zostanie utworzona za każdym razem, gdy zostanie uruchomiona nowa kompilacja.

## <a name="examine-the-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania

W poprzednich sekcjach usługa Azure DevOps Starter automatycznie skonfigurowała pełny potok ciągłej integracji/ciągłego dostarczania dla aplikacji IoT Edge. Teraz Przejrzyj i Dostosuj potok zgodnie z wymaganiami. Wykonaj następujące kroki, aby zaznajomić się z usługą Azure DevOps Build i potokiem wydań.

1. Aby wyświetlić potoki kompilacji w projekcie DevOps, wybierz pozycję **potoki kompilacji** w menu pulpitu nawigacyjnego projektu. Ten link otwiera kartę przeglądarki i potok kompilacji usługi Azure DevOps dla nowego projektu.

   ![Wyświetlanie potoków kompilacji w Azure Pipelines](./media/how-to-devops-starter/view-build-pipelines.png)

2. Otwórz automatycznie wygenerowany potok kompilacji i wybierz pozycję **Edytuj** w prawym górnym rogu.

    ![Edytuj potok kompilacji](media/how-to-devops-starter/click-edit-button.png)

3. W panelu, który zostanie otwarty, można przejrzeć zadania, które są wykonywane w momencie uruchomienia potoku kompilacji. Potok kompilacji wykonuje różne zadania, takie jak pobieranie źródeł z repozytorium git, kompilowanie obrazów modułów IoT Edge, wypychanie modułów IoT Edge do rejestru kontenerów i publikowanie danych wyjściowych używanych do wdrożeń. Aby dowiedzieć się więcej o Azure IoT Edge zadaniach w usłudze Azure DevOps, zobacz [konfigurowanie Azure Pipelines do ciągłej integracji](how-to-continuous-integration-continuous-deployment-classic.md#create-a-build-pipeline-for-continuous-integration).

4. Wybierz nagłówek **potoku** w górnej części potoku kompilacji, aby otworzyć Szczegóły potoku. Zmień nazwę potoku kompilacji na bardziej opisową.

   ![Edytuj szczegóły potoku](./media/how-to-devops-starter/edit-build-pipeline.png)

5. Wybierz pozycję **zapisz & kolejkę**, a następnie wybierz pozycję **Zapisz**. Dodawanie komentarzy do komentarza jest opcjonalne.

6. Wybierz pozycję **wyzwalacze** z menu potok kompilacji. DevOps Starter automatycznie utworzył wyzwalacz CI i każde zatwierdzenie do repozytorium uruchamia nową kompilację.  Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

7. Wybierz pozycję **Przechowywanie**. Postępuj zgodnie z linkiem, aby przekierować do ustawień projektu, w którym znajdują się zasady przechowywania. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

8. Wybierz pozycję **historia**. Panel Historia zawiera dziennik inspekcji ostatnich zmian w kompilacji. Usługa Azure Pipelines śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

9. Po zakończeniu eksplorowania potoku kompilacji przejdź do odpowiedniego potoku wydania. Wybierz pozycję **wersje** w obszarze **potoki**, a następnie wybierz pozycję **Edytuj** , aby wyświetlić szczegóły potoku.

    ![Wyświetl potok wydania](media/how-to-devops-starter/release-pipeline.png)

10. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Źródłem tego artefaktu jest dane wyjściowe potoku kompilacji, które zostały sprawdzone w poprzednich krokach.

11. Obok ikony **upuszczania** wybierz **wyzwalacz ciągłego wdrażania** , który wygląda jak piorun. Ten potok wersji włączył wyzwalacz, który uruchamia wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania.  

12. W menu potoku wydania wybierz pozycję **zadania** , a następnie wybierz etap **dev** na liście rozwijanej. DevOps Projects utworzyć etap wydania, który tworzy Centrum IoT Hub, tworzy urządzenie IoT Edge w tym centrum wdraża przykładowy moduł z potoku kompilacji i inicjuje maszynę wirtualną do uruchomienia jako urządzenie IoT Edge. Aby dowiedzieć się więcej o Azure IoT Edge zadaniach dla dysku CD, zobacz [konfigurowanie Azure Pipelines do ciągłego wdrażania](how-to-continuous-integration-continuous-deployment-classic.md#create-a-release-pipeline-for-continuous-deployment).

    ![Wyświetlanie zadań ciągłego wdrażania](media/how-to-devops-starter/choose-release.png)

13. Po prawej stronie wybierz pozycję **Wyświetl wydania**. Ten widok przedstawia historię wersji.

14. Wybierz nazwę wydania, aby wyświetlić więcej informacji na jego temat.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy usługa Azure App Service i inne utworzone powiązane zasoby nie będą już potrzebne, możesz je usunąć. Użyj funkcji **usuwania** na pulpicie nawigacyjnym DevOps Starter.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z zadaniami Azure IoT Edge w usłudze Azure DevOps w ramach [ciągłej integracji i ciągłego wdrażania w Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* Informacje na temat wdrażania IoT Edge w temacie [omówienie IoT Edge wdrożeń dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md)
* Zapoznaj się z instrukcjami tworzenia, aktualizowania lub usuwania wdrożenia w sekcji [wdrażanie i monitorowanie modułów IoT Edge](how-to-deploy-at-scale.md)w odpowiedniej skali.
