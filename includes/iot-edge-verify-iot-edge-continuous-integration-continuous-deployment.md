---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: c7be10e14daf3ee769e86d51f648cc6b656a416a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89303055"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Weryfikuj IoT Edge ciągłej integracji/ciągłego wdrażania przy użyciu potoków kompilacja i wydanie

Aby wyzwolić zadanie kompilacji, można wypchnąć zatwierdzenie do repozytorium kodu źródłowego lub ręcznie je wyzwolić. W tej sekcji ręcznie wyzwolimy potok ciągłej integracji/ciągłego wdrażania, aby sprawdzić, czy działa. Następnie sprawdź, czy wdrożenie powiodło się.

1. W menu po lewej stronie wybierz pozycję **potoki** i Otwórz potok kompilacji utworzony na początku tego artykułu.

2. Możesz wyzwolić zadanie kompilacji w potoku kompilacji, wybierając przycisk **Uruchom potok** w prawym górnym rogu.

    ![Ręczne wyzwalanie potoku kompilacji przy użyciu przycisku Uruchom potok](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. Przejrzyj ustawienia **przebiegu potoku** . Następnie wybierz pozycję **Uruchom**.

    ![Określ opcje przebiegu potoku i wybierz pozycję Uruchom](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. Wybierz **zadanie agenta 1** , aby obserwować postęp przebiegu. Możesz przejrzeć dzienniki danych wyjściowych zadania, wybierając zadanie. 

    ![Przejrzyj dane wyjściowe dziennika zadania](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. W przypadku pomyślnego ukończenia potoku kompilacji jest wyzwalane wydanie do etapu **dev** . Pomyślne wydanie **deweloperskie** tworzy IoT Edge wdrożenie IoT Edge urządzeń docelowych.

    ![Wydanie do deweloperów](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. Kliknij pozycję etap **deweloperów** , aby wyświetlić dzienniki wydań.

    ![Dzienniki wydań](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)