---
title: Tworzenie aplikacji React w celu dodawania użytkowników do usługi rozpoznawania twarzy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować środowisko projektowe i wdrożyć aplikację rozpoznawania twarzy, aby uzyskać zgodę od klientów.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 39a74c7f3d5fb8f8b60a66947fcce9837ed6ee13
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505109"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>Tworzenie aplikacji React w celu dodawania użytkowników do usługi rozpoznawania twarzy

W tym przewodniku popisano, jak rozpocząć pracę z przykładową aplikacją do rejestracji twarzy. W aplikacji pokazano najlepsze rozwiązania dotyczące uzyskiwania znaczącej zgody na dodawanie użytkowników do usługi rozpoznawania twarzy i uzyskiwanie danych twarzy o wysokiej dokładności. Zintegrowany system może używać aplikacji takiej jak ta, aby zapewnić bezduszną kontrolę dostępu, weryfikację tożsamości, śledzenie śledzenia śledzenia, kiosk personalizacji lub weryfikację tożsamości na podstawie ich danych twarzy.

Po jej zainicjowaniu aplikacja wyświetla użytkownikom szczegółowy ekran wyrażania zgody. Jeśli użytkownik wyrazić zgodę, aplikacja wyświetli monit o nazwę użytkownika i hasło, a następnie przechwyci obraz twarzy o wysokiej jakości przy użyciu aparatu urządzenia.

Przykładowa aplikacja została napisana przy użyciu języka JavaScript i React Native framework. Obecnie można go wdrożyć na urządzeniach z systemem Android. Więcej opcji wdrażania będzie dostępnych w przyszłości.

## <a name="prerequisites"></a>Wymagania wstępne 

* Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie.](https://azure.microsoft.com/free/cognitive-services/)  
* Po utworzeniu subskrypcji platformy Azure utwórz [zasób](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) rozpoznawania twarzy w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu wybierz pozycję **Przejdź do zasobu**.  
  * Będziesz potrzebować klucza i punktu końcowego z utworzonego zasobu, aby połączyć aplikację z interfejsem API rozpoznawania twarzy.  
  * W przypadku lokalnego testowania i testowania możesz wkleić klucz interfejsu API i punkt końcowy do pliku konfiguracji. W celu ostatecznego wdrożenia przechowuj klucz interfejsu API w bezpiecznej lokalizacji i nigdy w kodzie.  

> [!IMPORTANT]
> Te klucze subskrypcji są używane do uzyskiwania dostępu do interfejsu API usługi Cognitive Service. Nie udostępniaj kluczy. Przechowuj je bezpiecznie, na przykład przy użyciu Azure Key Vault. Zalecamy również regularne ponowne generowanie tych kluczy. Tylko jeden klucz jest niezbędny do wywołania interfejsu API. Podczas ponownego generowania pierwszego klucza można użyć drugiego klucza w celu uzyskania dalszego dostępu do usługi.

## <a name="set-up-the-development-environment"></a>Konfigurowanie środowiska projektowego

1. Sklonuj repozytorium git [przykładowej aplikacji](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Aby skonfigurować środowisko projektowe, postępuj zgodnie z React Native <a href="https://reactnative.dev/docs/environment-setup"  title=" "  target="_blank"> dokumentacji React Native </a> dokumentacji. Wybierz **React Native wiersza polecenia Szybki start** jako system operacyjny tworzenia oprogramowania i wybierz pozycję **Android** jako docelowy system operacyjny. Ukończ sekcje **Instalowanie zależności i** środowiska **projektowego systemu Android.**
1. Otwórz plik env.jsw preferowanym edytorze tekstów, takim jak [Visual Studio Code](https://code.visualstudio.com/), i dodaj punkt końcowy i klucz. Punkt końcowy i klucz możesz pobrać w Azure Portal na **karcie** Przegląd zasobu. Ten krok jest tylko do celów testowania lokalnego i nie umożliwia zaewidencjoncjonia klucza interfejsu API rozpoznawania &mdash; twarzy w repozytorium zdalnym.
1. Uruchom aplikację przy użyciu emulatora urządzenia wirtualnego z systemem Android Android Studio lub własnego urządzenia z systemem Android. Aby przetestować aplikację na urządzeniu fizycznym, postępuj zgodnie z React Native <a href="https://reactnative.dev/docs/running-on-device"  title=" dokumentacją React Native "  target="_blank"> </a> dokumentacji.  


## <a name="create-a-user-add-experience"></a>Tworzenie nowego interfejsu użytkownika  

Teraz, po skonfigurowaniu przykładowej aplikacji, możesz dostosować ją do własnych potrzeb.

Na przykład możesz dodać informacje specyficzne dla sytuacji na stronie zgody:

> [!div class="mx-imgBorder"]
> ![strona zgody aplikacji](./media/enrollment-app/1-consent-1.jpg)

Usługa zapewnia kontrolę jakości obrazu, aby ułatwić wybór, czy obraz jest wystarczająco wysokiej jakości, aby dodać klienta, czy podjąć próbę rozpoznawania twarzy. Ta aplikacja pokazuje, jak uzyskać dostęp do ramek z aparatu urządzenia, wybrać ramki najwyższej jakości i dodać wykrytą twarz do usługi interfejsu API rozpoznawania twarzy. 

Wiele problemów z rozpoznawaniem twarzy jest spowodowanych przez obrazy referencyjne niskiej jakości. Niektóre czynniki, które mogą obniżyć wydajność modelu, to:
* Rozmiar twarzy (twarze, które są odległe od aparatu)
* Orientacja twarzy (twarze obracane lub pochylony od kamery)
* Słabe warunki oświetlenia (małe oświetlenie lub oświetlenie), w których obraz może być źle narażony lub ma za dużo szumu
* Okluz (częściowo ukryte lub schowane twarze), w tym akcesoria, takie jak czapeki lub okulary o grubym rozmiarze
* Rozmycie (na przykład przez szybkie poruszanie się po twarzy podczas zdjęć). 

> [!div class="mx-imgBorder"]
> ![strona instrukcji przechwytywania obrazu aplikacji](./media/enrollment-app/4-instruction.jpg)

Zwróć uwagę, że aplikacja oferuje również funkcję usuwania informacji o użytkowniku oraz opcję ponownego dodawania.

> [!div class="mx-imgBorder"]
> ![strona zarządzania profilami](./media/enrollment-app/10-manage-2.jpg)

Aby rozszerzyć funkcjonalność aplikacji w celu pełnego [](enrollment-overview.md) obsługi, przeczytaj omówienie dodatkowych funkcji do zaimplementowania i najlepszych rozwiązań.

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

### <a name="android"></a>Android

Najpierw upewnij się, że aplikacja jest gotowa do wdrożenia produkcyjnego: usuń wszystkie klucze lub wpisy tajne z kodu aplikacji i upewnij się, że zostały stosowane [najlepsze rozwiązania w zakresie zabezpieczeń.](../cognitive-services-security.md?tabs=command-line%2ccsharp)

Gdy wszystko będzie gotowe do wydania aplikacji do produkcji, wygeneruje się plik APK gotowy do wydania, który jest formatem pliku pakietu dla aplikacji systemu Android. Ten plik APK musi być podpisany przy użyciu klucza prywatnego. W tej kompilacji wydania możesz rozpocząć dystrybucję aplikacji bezpośrednio na urządzeniach. 

Postępuj zgodnie z dokumentacją Przygotowywanie do wydania Przygotuj do wydania, aby dowiedzieć się, jak wygenerować klucz prywatny, podpisać aplikację i <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" "  target="_blank"> wygenerować pakiet APK </a> wydania.  

Po utworzeniu podpisanego pliku APK zobacz dokumentację Publikowanie aplikacji Opublikuj aplikację, aby dowiedzieć się więcej na temat <a href="https://developer.android.com/studio/publish"  title=" "  target="_blank"> sposobu wydania </a> aplikacji.

## <a name="next-steps"></a>Następne kroki  

W tym przewodniku opisano sposób skonfigurowania środowiska projektowego i rozpoczęcia pracy z przykładową aplikacją. Jeśli jesteś nowym użytkownikom React Native, możesz przeczytać [](https://reactnative.dev/docs/getting-started) ich dokumenty z wprowadzeniem, aby uzyskać więcej informacji. Pomocne może być również zapoznanie się z interfejsem [API rozpoznawania twarzy.](Overview.md) Przeczytaj inne sekcje dotyczące dodawania użytkowników przed rozpoczęciem tworzenia aplikacji.