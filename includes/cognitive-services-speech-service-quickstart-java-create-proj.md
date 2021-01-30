---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 68e83ca0fc92247a31e840e76fc8019736b71dd9
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213551"
---
1. Uruchom środowisko Eclipse.

1. W programie Eclipse Launcher w polu **Workspace** (Obszar roboczy) wprowadź nazwę nowego katalogu roboczego. Następnie wybierz pozycję **Launch** (Uruchom).

   ![Zrzut ekranu przedstawiający program Eclipse Launcher](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Za chwilę zostanie wyświetlone główne okno środowiska IDE programu Eclipse. Zamknij ekran **powitalny** , jeśli jest obecny.

1. Na pasku menu zaćmienie Utwórz nowy projekt, wybierając pozycję **plik**  >  **Nowy**  >  **projekt**.

1. Zostanie wyświetlone okno dialogowe **Nowy projekt**. Wybierz pozycję **Java Project** (Projekt języka Java) i wybierz pozycję **Next** (Dalej).

   ![Zrzut ekranu dialogowego New Project (Nowy projekt) z wyróżnioną pozycją Java Project (Projekt języka Java)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. Zostanie uruchomiony Kreator **nowego projektu Java** . W polu **Project name** (Nazwa projektu) wprowadź ciąg **quickstart** i wybierz **JavaSE-1.8** jako środowisko wykonania. Wybierz pozycję **Zakończ**.

   ![Zrzut ekranu przedstawiający kreatora nowego projektu języka Java](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Jeśli zostanie wyświetlone okno **Open Associated Perspective?** (Otworzyć skojarzoną perspektywę?), wybierz pozycję **Open Perspective** (Otwórz perspektywę).

1. W narzędziu **Package Explorer** kliknij prawym przyciskiem myszy projekt **quickstart**. Wybierz pozycję **Konfiguruj**  >  **Konwertuj do projektu Maven** z menu kontekstowego.

   ![Zrzut ekranu narzędzia Package Explorer](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Zostanie wyświetlone okno **Create new POM** (Tworzenie nowego modelu POM). W polu **Identyfikator grupy** wprowadź wartość *com. Microsoft. cognitiveservices. Speech. Samples* i w polu **Identyfikator artefaktu** wprowadź *Szybki Start*. Następnie wybierz pozycję **Zakończ**.

   ![Zrzut ekranu okna Create new POM (Tworzenie nowego modelu POM)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Otwórz plik *pom.xml* i edytuj go.

   * Na końcu pliku przed tagiem zamykającym `</project>` utwórz element `repositories` z odwołaniem do repozytorium narzędzia Maven dla zestawu Speech SDK, jak pokazano poniżej:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#repositories)]

   * Należy również dodać `dependencies` element z opcją 1.15.0 zestawu mowy SDK jako zależność:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#dependencies)]

   * Zapisz zmiany.
