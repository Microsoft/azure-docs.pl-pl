---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 706b2306fbe9f2a744d2874a8b55f78fa2fc8e4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89302955"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>Tworzenie potoku wydania na potrzeby ciągłego wdrażania

W tej sekcji utworzysz potok wydania, który jest skonfigurowany do automatycznego uruchamiania, gdy potok kompilacji pospadnie artefakty, a dzienniki wdrażania zostaną wyświetlone w Azure Pipelines.

Utwórz nowy potok i Dodaj nowy etap:

1. Na karcie **wersje** w obszarze **potoki**wybierz pozycję **+ Nowy potok**. Lub, jeśli masz już potoki wersji, wybierz przycisk **+ Nowy** i wybierz pozycję **+ Nowy potok wersji**.  

    ![Dodawanie potoku wydania przy użyciu przycisku + Nowy potok](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. Po wyświetleniu monitu o wybranie szablonu wybierz pozycję Rozpocznij od **pustego zadania**.

    ![Zacznij od pustego zadania dla potoku wydania](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. Nowy potok wydania jest inicjowany z jednym etapem, zwanym **etapem 1**. Zmień nazwę etapu 1 na **dev** i Traktuj ją jako potok ciągłego wdrażania dla środowiska deweloperskiego. Zwykle potoki ciągłego wdrażania mają wiele etapów, w tym **dev**, **Staging**i **prod**. Możesz użyć różnych nazw i utworzyć więcej na podstawie swojej DevOps. Zamknij okno Szczegóły etapu po jego zmianie nazwy.

   Możesz również zmienić nazwę potoku wydania, zaznaczając w górnej części tekst "Nowa wersja potoku".

4. Połącz wydanie z artefaktami kompilacji, które są publikowane przez potok kompilacji. Kliknij przycisk **Dodaj** w obszarze artefaktów.

   ![Kliknij przycisk Dodaj w obszarze artefaktów interfejsu](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. Na **stronie Dodawanie artefaktu**wybierz opcję **Kompiluj** jako **Typ źródła**. Wybierz projekt i utworzony potok kompilacji. Jeśli chcesz, możesz zmienić **alias źródłowy** na coś bardziej opisowego. Następnie wybierz pozycję **Dodaj**.

   ![Na stronie Dodawanie artefaktu wybierz pozycję Dodaj, aby utworzyć artefakt](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. Otwórz wyzwalacze artefaktów i wybierz przełącznik, aby włączyć wyzwalacz ciągłego wdrażania. Teraz Nowa wersja zostanie utworzona za każdym razem, gdy zostanie udostępniona nowa kompilacja.

   ![Otwórz wyzwalacze artefaktów i Przełącz, aby włączyć wyzwalacz ciągłego wdrażania](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. Etap **dev** jest wstępnie skonfigurowany przy użyciu jednego zadania i zero zadań. Z menu potok wybierz pozycję **zadania** , a następnie wybierz etap **dev** . Wybierz **zadanie agenta** i zmień jego **nazwę wyświetlaną** na wartość **pytania i odpowiedzi**. Można skonfigurować szczegółowe informacje o zadaniu agenta, ale zadanie wdrażania jest niezależne od platformy, aby można było użyć dowolnej **specyfikacji agenta** w wybranej **puli agentów**.

   ![Wyświetl zadania dla etapu dev na karcie zadania](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. W zadaniu pytań i odpowiedzi wybierz znak plus ( **+** ), aby dodać dwa zadania. Wyszukaj i Dodaj **Azure IoT Edge** dwa razy.

9. Wybierz pierwsze zadanie **Azure IoT Edge** i skonfiguruj je przy użyciu następujących wartości:

    | Parametr | Opis |
    | --- | --- |
    | Nazwa wyświetlana | Nazwa wyświetlana jest automatycznie aktualizowana, gdy pole akcji ulega zmianie. |
    | Akcja | Wybierz pozycję `Generate deployment manifest`. |
    | .template.jspliku | Określ ścieżkę: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` . Ścieżka jest publikowana z potoku kompilacji. |
    | Platforma domyślna | Wybierz odpowiedni system operacyjny dla modułów w oparciu o Twoje urządzenie IoT Edge. |
    | Ścieżka wyjściowa| Umieść ścieżkę `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Ta ścieżka to końcowy plik manifestu wdrażania IoT Edge. |

    Te konfiguracje ułatwiają zastępowanie adresów URL obrazów modułów w `deployment.template.json` pliku. Funkcja **Generuj manifest wdrożenia** pomaga również zastąpić zmienne dokładnie wartością określoną w `deployment.template.json` pliku. W programie VS/VS Code określasz wartość rzeczywistą w `.env` pliku. W Azure Pipelines należy ustawić wartość na karcie **zmienne potoku wydania** . Przejdź do karty **zmienne** i skonfiguruj nazwę i wartość w następujący sposób:

    * **ACR_ADDRESS**: Azure Container Registry wartość **serwera logowania** . Serwer logowania można pobrać ze strony Przegląd rejestru kontenerów w Azure Portal.
    * **ACR_PASSWORD**: hasło Azure Container Registry.
    * **ACR_USER**: Azure Container Registry nazwy użytkownika.

    Jeśli w projekcie znajdują się inne zmienne, możesz określić nazwę i wartość na tej karcie. W przypadku **wygenerowania manifestu wdrożenia** można rozpoznać tylko zmienne `${VARIABLE}` . Upewnij się, że używasz tego `*.template.json` pliku w plikach.

    ![Konfigurowanie zmiennych dla potoku wydania na karcie zmienne](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. Wybierz drugie zadanie **Azure IoT Edge** i skonfiguruj je przy użyciu następujących wartości:

    | Parametr | Opis |
    | --- | --- |
    | Nazwa wyświetlana | Nazwa wyświetlana jest automatycznie aktualizowana, gdy pole akcji ulega zmianie. |
    | Akcja | Wybierz pozycję `Deploy to IoT Edge devices`. |
    | Plik wdrożenia | Umieść ścieżkę `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Ta ścieżka jest plikiem manifestu wdrażania IoT Edge. |
    | Subskrypcja platformy Azure | Wybierz subskrypcję zawierającą IoT Hub.|
    | Nazwa IoT Hub | Wybierz Centrum IoT Hub.|
    | Wybieranie jednego/wielu urządzeń | Zdecyduj, czy potok wydania ma zostać wdrożony na jednym, czy na wielu urządzeniach. W przypadku wdrażania na jednym urządzeniu wprowadź **IoT Edge identyfikator urządzenia**. W przypadku wdrażania na wielu urządzeniach należy określić **warunek docelowy**urządzenia. Warunek docelowy to filtr zgodny z zestawem IoT Edge urządzeń w IoT Hub. Jeśli chcesz używać tagów urządzenia jako warunku, musisz zaktualizować odpowiednie znaczniki urządzeń za pomocą sznurka urządzenia IoT Hub. Zaktualizuj **IoT Edge identyfikator wdrożenia** i **IoT Edge priorytet wdrożenia** w obszarze Ustawienia zaawansowane. Aby uzyskać więcej informacji na temat tworzenia wdrożenia dla wielu urządzeń, zobacz [opis IoT Edge wdrożeń automatycznych](../articles/iot-edge/module-deployment-monitoring.md). |
    | Identyfikator urządzenia lub warunek docelowy | W zależności od wcześniejszego wyboru Określ identyfikator urządzenia lub [warunek docelowy](../articles/iot-edge/module-deployment-monitoring.md#target-condition) , który ma zostać wdrożony na wielu urządzeniach. |
    | Zaawansowane | Dla identyfikatora wdrożenia IoT Edge Określ `$(System.TeamProject)-$(Release.EnvironmentName)` . Ta zmienna mapuje projekt i nazwę wydania na identyfikator wdrożenia IoT Edge. |

    ![Dodawanie Azure IoT Edge zadań dla etapu deweloperskiego](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. Wybierz pozycję **Zapisz** , aby zapisać zmiany w nowym potoku wydania. Wróć do widoku potoku, wybierając z menu pozycję Karta **potoku** .
