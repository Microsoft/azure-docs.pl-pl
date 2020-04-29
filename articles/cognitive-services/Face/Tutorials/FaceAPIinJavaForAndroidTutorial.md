---
title: 'Samouczek: wykrywanie i oznaczanie ramką twarzy na obrazie za pomocą zestawu Android SDK'
titleSuffix: Azure Cognitive Services
description: W ramach tego samouczka utworzysz prostą aplikację dla systemu Android, która korzysta z usługi twarzy do wykrywania i tworzenia klatek w obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: dd986a7557f468b939aefe1da825c9834618047f
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81402920"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Samouczek: tworzenie aplikacji dla systemu Android wykrywającej i oznaczającej ramką twarze na obrazie

W tym samouczku utworzysz aplikację dla systemu Android, która korzysta z usługi Azure twarzy za pośrednictwem zestawu Java SDK w celu wykrywania twarzy na obrazie. Aplikacja wyświetla wybrany obraz i rysuje ramkę wokół każdej wykrytej twarzy.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> - Tworzenie aplikacji systemu Android
> - Instalowanie biblioteki klienta programu Front
> - Korzystanie z biblioteki klienta do wykrywania twarzy na obrazie
> - Rysowanie ramki wokół każdej wykrytej twarzy

![Zrzut ekranu z systemu Android ze zdjęciem, na którym widać twarze otoczone czerwoną ramką](../Images/android_getstarted2.1.PNG)

Kompletny przykładowy kod jest dostępny w repozytorium [Cognitive Services Face Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) w witrynie GitHub.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) . 

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji programu Marketo. Klucz subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Lub postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , aby subskrybować usługę i uzyskać klucz. Następnie [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla ciągu punktu końcowego klucza i usługi, odpowiednio `FACE_SUBSCRIPTION_KEY` nazwane `FACE_ENDPOINT`i.
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).
- [Android Studio](https://developer.android.com/studio/) z poziomu interfejsu API 22 lub nowszego.

## <a name="create-the-android-studio-project"></a>Tworzenie projektu programu Android Studio

Wykonaj następujące kroki, aby utworzyć nowy projekt aplikacji dla systemu Android.

1. W programie Android Studio wybierz pozycję **Start a new Android Studio project** (Utwórz nowy projekt programu Android Studio).
1. Na ekranie **Create Android Project** (Tworzenie projektu systemu Android) zmodyfikuj domyślną zawartość pól, jeśli to konieczne, a następnie kliknij przycisk **Next** (Dalej).
1. Na ekranie **Target Android Devices** (Docelowe urządzenia z systemem Android) użyj rozwijanego selektora, aby wybrać interfejs **API 22** lub nowszy, a następnie kliknij przycisk **Next** (Dalej).
1. Wybierz pozycję **Empty Activity** (Puste działanie), a następnie kliknij przycisk **Next** (Dalej).
1. Usuń zaznaczenie pola wyboru **Backwards Compatibility** (Zgodność z wcześniejszymi wersjami), a następnie kliknij przycisk **Finish** (Zakończ).

## <a name="add-the-initial-code"></a>Dodawanie początkowego kodu

### <a name="create-the-ui"></a>Tworzenie interfejsu użytkownika

Otwórz plik *activity_main.xml*. W edytorze układu Layout Editor wybierz kartę **Text** (Tekst), a następnie zastąp zawartość następującym kodem.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>Tworzenie głównej klasy

Otwórz plik *MainActivity.java* i zastąp istniejące instrukcje `import` następującym kodem.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

Następnie zastąp zawartość klasy **MainActivity** następującym kodem. Utworzy on procedurę obsługi zdarzeń dla elementu **Button**, która uruchamia nowe działanie pozwalające użytkownikowi wybrać obraz. Wyświetla obraz w widoku **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>Testowanie aplikacji

Oznacz jako komentarz wywołanie funkcji **detectAndFrame** w metodzie **onActivityResult**. Następnie naciśnij pozycję **Uruchom** w menu, aby przetestować aplikację. Gdy aplikacja zostanie otwarta, w emulatorze lub na podłączonym urządzeniu, kliknij pozycję **Przeglądaj** na dole. Powinno zostać wyświetlone okno dialogowe wyboru pliku urządzenia. Wybierz obraz i sprawdź, czy jest on wyświetlany w oknie. Następnie zamknij aplikację i przejdź do kolejnego kroku.

![Zrzut ekranu z systemu Android przedstawiający zdjęcie z twarzami](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Dodawanie zestawu SDK interfejsu API rozpoznawania twarzy

### <a name="add-the-gradle-dependency"></a>Dodawanie zależności narzędzia Gradle

W okienku **Project** (Projekt) wybierz za pomocą rozwijanego selektora pozycję **Android**. Rozwiń pozycję **Gradle Scripts** (Skrypty Gradle), a następnie otwórz plik *build.gradle (Module: app)*. Dodaj zależność dla biblioteki klienta rozpoznawania twarzy, `com.microsoft.projectoxford:face:1.4.3`, jak pokazano na poniższym zrzucie ekranu, a następnie kliknij pozycję **Sync now** (Synchronizuj teraz).

![Zrzut ekranu z programu Android Studio przedstawiający plik build.gradle aplikacji](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Dodawanie kodu projektu związanego z rozpoznawaniem twarzy

Wróć do pliku **MainActivity.java** i dodaj następujące instrukcje `import`:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

Następnie wstaw następujący kod w klasie **MainActivity** powyżej metody **onCreate**:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

W okienku **Project** (Projekt) rozwiń pozycje **app** i **manifests**, a następnie otwórz plik *AndroidManifest.xml*. Wstaw następujący element jako bezpośredni element podrzędny elementu `manifest`:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>Przekazywanie obrazu i wykrywanie twarzy

Aplikacja będzie wykrywać twarze, wywołując metodę **faceClient. twarzy. DetectWithStreamAsync** , która otacza interfejs API [rest i](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) zwraca listę wystąpień **twarzy** .

Każdy zwrócony element **Face** zawiera prostokąt wskazujący lokalizację twarzy wraz z serią opcjonalnych atrybutów twarzy. W tym przykładzie żądane są tylko prostokąty wskazujące lokalizację twarzy.

Wstaw następujące dwie metody do klasy **MainActivity**. Po zakończeniu wykrywania czołowego aplikacja wywołuje metodę **drawFaceRectanglesOnBitmap** , aby zmodyfikować **ImageView**. Zdefiniujesz tę metodę w następnej kolejności.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>Rysowanie prostokątów twarzy

Wstaw następującą metodę pomocniczą do klasy **MainActivity**. Ta metoda rysuje prostokąt wokół każdej wykrytej twarzy przy użyciu współrzędnych prostokąta każdego wystąpienia elementu **Face**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

Na koniec usuń znaczniki komentarza z wywołania metody **detectAndFrame** w metodzie **onActivityResult**.

## <a name="run-the-app"></a>Uruchomienie aplikacji

Uruchom aplikację i znajdź obraz z twarzą. Zaczekaj kilka sekund, aby umożliwić usłudze rozpoznawania twarzy udzielenie odpowiedzi. Wokół każdej twarzy na obrazie powinien pojawić się czerwony prostokąt.

![Zrzut ekranu systemu Android przedstawiający twarze z czerwonymi prostokątami narysowanymi wokół nich](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak używać zestawu SDK języka Java do tworzenia aplikacji do wykrywania i szkieletowej twarzy na obrazie. Teraz dowiedz się więcej o szczegółach wykrywania twarzy.

> [!div class="nextstepaction"]
> [Jak wykrywać twarze na obrazie](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
