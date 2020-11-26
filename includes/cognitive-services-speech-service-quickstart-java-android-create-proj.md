---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: travisw
ms.openlocfilehash: 2efe17bdf597b7e3ba7d0277f06d4b24e9b51db3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187768"
---
1. Uruchom Android Studio i wybierz pozycję **Rozpocznij nowy projekt Android Studio** w oknie **powitalnym** .

    ![Zrzut ekranu okna powitalnego programu Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Zostanie wyświetlony Kreator **Wybierz projekt** . W polu wyboru działania wybierz pozycję **telefon i tablet** i **puste działanie** . Wybierz pozycję **Dalej**.

   ![Zrzut ekranu przedstawiający kreatora wyboru projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Na ekranie **Konfigurowanie projektu** wprowadź *Szybki Start* jako **nazwę** i wprowadź *Samples.Speech.cognitiveservices.Microsoft.com* jako **nazwę pakietu**. Następnie wybierz katalog projektu. W obszarze **minimalny poziom interfejsu API** wybierz pozycję **interfejs API 23: Android 6,0 (Marshmallow)**. Pozostaw wszystkie inne pola wyboru Wyczyść, a następnie wybierz pozycję **Zakończ**.

   ![Zrzut ekranu przedstawiający Kreatora konfigurowania projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Przygotowanie nowego projektu Android w programie Android Studio zajmie trochę czasu. Następnie skonfiguruj projekt, aby dowiedzieć się więcej na temat zestawu Speech SDK dla platformy Azure Cognitive Services i używania języka Java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu SDK mowy Cognitive Services to 1.13.0.

Zestaw Speech SDK dla systemu Android znajduje się w pakiecie o nazwie [AAR (biblioteka Android)](https://developer.android.com/studio/projects/android-library) i zawiera niezbędne biblioteki oraz wymagane uprawnienia systemu Android.
Jest ona hostowana w repozytorium Maven przy użyciu protokołu https: \/ /csspeechstorage.blob.Core.Windows.NET/Maven/.

Skonfiguruj projekt do korzystania z zestawu Speech SDK. Otwórz okno **struktury projektu** , wybierając pozycję **plik**  >  **Struktura projektu** na pasku menu Android Studio. W oknie **Struktura projektu** wprowadź następujące zmiany:

1. Na liście po lewej stronie okna wybierz pozycję **Project** (Project). Edytuj **domyślne ustawienia repozytorium bibliotek** , dołączając przecinek i nasz adres URL repozytorium Maven ujęty w znaki pojedynczego cudzysłowu: "https: \/ /csspeechstorage.blob.Core.Windows.NET/Maven/"

   ![Zrzut ekranu okna Project Structure (Struktura projektu)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Na tym samym ekranie po lewej stronie wybierz pozycję **aplikacja**. Następnie wybierz kartę **Dependencies** (Zależności) w górnej części okna. Wybierz zielony znak plus ( **+** ), a następnie wybierz pozycję **zależność biblioteki** z menu rozwijanego.

   ![Zrzut ekranu przedstawiający zależność biblioteki](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. W wyświetlonym oknie wprowadź nazwę i wersję zestawu Speech SDK dla systemu Android, *com. Microsoft. cognitiveservices. Speech: Client-SDK: 1.13.0*. Następnie wybierz przycisk **OK**.
   Zestaw mowy SDK należy dodać do listy zależności teraz, jak pokazano poniżej:

   ![Zrzut ekranu zestawu Speech SDK na liście zależności](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Wybierz kartę **Właściwości** . W przypadku **zgodności źródłowej** i **zgodności docelowej** wybierz pozycję **1,9**.

   ![Zrzut ekranu przedstawiający zgodność źródłową i docelową zgodność](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Wybierz **przycisk OK** , aby zamknąć okno **struktury projektu** i zastosować zmiany do projektu.
