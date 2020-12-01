---
title: Tworzenie aplikacji do rejestracji dla systemu Android przy użyciu reakcji
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować środowisko deweloperskie i wdrożyć aplikację do rejestracji w celu uzyskania zgody od klientów.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 085dd18214f795566669fb862bba63b67eb9115d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350368"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>Tworzenie aplikacji do rejestracji dla systemu Android przy użyciu reakcji

W tym przewodniku pokazano, jak rozpocząć pracę z przykładową aplikacją rejestracji aplikacji. W aplikacji przedstawiono najlepsze rozwiązania umożliwiające uzyskanie znaczącej zgody na rejestrację użytkowników w usłudze rozpoznawania aplikacji oraz uzyskanie danych o wysokiej dokładności. Zintegrowany system może korzystać z aplikacji do rejestracji, takiej jak ta, aby zapewnić bezdotykową kontrolę dostępu, weryfikację tożsamości, śledzenie frekwencji, kiosk personalizacji lub weryfikację tożsamości na podstawie ich danych.

Po uruchomieniu aplikacja pokazuje użytkowników na szczegółowym ekranie wyrażania zgody. Jeśli użytkownik wyrazi zgodę, aplikacja wyświetli komunikat z prośbą o nazwę użytkownika i hasło, a następnie przechwytuje obraz wysokiej jakości z aparatu urządzenia.

Przykładowa aplikacja do rejestracji jest zapisywana przy użyciu języka JavaScript i natywnej platformy do reagowania. Można ją teraz wdrożyć na urządzeniach z systemem Android. Więcej opcji wdrażania jest dostępnych w przyszłości.

## <a name="prerequisites"></a>Wymagania wstępne 

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/).  
* Gdy masz subskrypcję platformy Azure, [Utwórz zasób czołowy](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu wybierz pozycję **Przejdź do zasobu**.  
  * Będziesz potrzebować klucza i punktu końcowego z zasobu, który został utworzony w celu połączenia aplikacji z interfejs API rozpoznawania twarzy.  
  * W przypadku lokalnego tworzenia i testowania można wkleić klucz interfejsu API i punkt końcowy do pliku konfiguracji. W celu wdrożenia końcowego przechowuj klucz interfejsu API w bezpiecznej lokalizacji i nigdy nie w kodzie.  

> [!IMPORTANT]
> Te klucze subskrypcji są używane do uzyskiwania dostępu do interfejsu API usługi poznawczej. Nie udostępniaj kluczy. Przechowuj je bezpiecznie, na przykład przy użyciu Azure Key Vault. Zalecamy także regularne ponowne generowanie tych kluczy. Tylko jeden klucz jest wymagany do wywołania interfejsu API. Po ponownym wygenerowaniu pierwszego klucza można użyć drugiego klawisza w celu uzyskania ciągłego dostępu do usługi.

## <a name="set-up-the-development-environment"></a>Konfigurowanie środowiska projektowego

1. Sklonuj repozytorium git dla [przykładowej aplikacji do rejestracji](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Aby skonfigurować środowisko programistyczne, należy postępować zgodnie z dokumentacją natywną <a href="https://reactnative.dev/docs/environment-setup"  title=" "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . Wybierz pozycję **Reaguj na natywny Przewodnik Szybki start interfejsu wiersza polecenia** , a następnie wybierz pozycję **Android** jako docelowy system operacyjny. Wykonaj sekcje **instalujące zależności** i **środowisko deweloperskie dla systemu Android**.
1. Otwórz env.jsw pliku w preferowanym edytorze tekstu, takim jak [Visual Studio Code](https://code.visualstudio.com/), i Dodaj punkt końcowy i klucz. Punkt końcowy i klucz można uzyskać w Azure Portal na karcie **Przegląd** zasobu. Ten krok jest przeznaczony tylko do celów testowania lokalnego &mdash; nie należy ewidencjonować klucza interfejs API rozpoznawania twarzy w repozytorium zdalnym.
1. Uruchom aplikację przy użyciu emulatora urządzenia wirtualnego systemu Android z poziomu Android Studio lub własnego urządzenia z systemem Android. Aby przetestować aplikację na urządzeniu fizycznym, należy postępować zgodnie z dokumentacją natywną dotyczącą reakcji <a href="https://reactnative.dev/docs/running-on-device"  title=" "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .  


## <a name="create-an-enrollment-experience"></a>Tworzenie środowiska rejestracji  

Teraz po skonfigurowaniu przykładowej aplikacji do rejestracji można dostosować ją do własnych potrzeb dotyczących obsługi rejestracji.

Na przykład możesz chcieć dodać informacje specyficzne dla sytuacji na stronie zgody:

> [!div class="mx-imgBorder"]
> ![Strona zgody aplikacji](./media/enrollment-app/1-consent-1.jpg)

Usługa zapewnia kontrolę jakości obrazu, która pomaga w wyborze, czy obraz ma wystarczającą jakość, aby zarejestrować klienta lub próbować rozpoznać rozpoznawanie. Ta aplikacja pokazuje, jak uzyskiwać dostęp do ramek z aparatu urządzenia, wybierać klatki najwyższej jakości i rejestrować wykryte urządzenia w usłudze interfejs API rozpoznawania twarzy. 

Wiele problemów z rozpoznawaniem kroju jest spowodowanych przez obrazy referencyjne o niskiej jakości. Niektóre czynniki, które mogą obniżyć wydajność modelu, to:
* Rozmiar twarzy (twarze, które są odległe od aparatu)
* Orientacja twarzy (powierzchnie wyłączone lub pochylone z aparatu)
* Słabe warunki oświetlenia (niska światło lub światło punktowe), w których obraz może być źle narażony lub ma zbyt dużo szumów
* Zamknięcia (częściowo ukryte lub zasłonięte powierzchnie), w tym akcesoria, takie jak systemy lub grube okulary obwódkami)
* Rozmycie (na przykład przez szybkie przemieszczenie do przodu, gdy zdjęcie zostało wykonane). 

> [!div class="mx-imgBorder"]
> ![Strona instrukcji przechwytywania obrazu aplikacji](./media/enrollment-app/4-instruction.jpg)

Zwróć uwagę, że aplikacja oferuje również funkcje umożliwiające usunięcie rejestracji użytkownika i opcję ponownego zarejestrowania.

> [!div class="mx-imgBorder"]
> ![Strona Zarządzanie profilem](./media/enrollment-app/10-manage-2.jpg)

Aby dowiedzieć się, jak zapewnić pełną rejestrację funkcji aplikacji, zapoznaj się z [omówieniem](enrollment-overview.md) , aby zapoznać się z dodatkowymi funkcjami implementacji i najlepszych rozwiązań.

## <a name="deploy-the-enrollment-app"></a>Wdrażanie aplikacji rejestracji

### <a name="android"></a>Android

Najpierw upewnij się, że aplikacja jest gotowa do wdrożenia produkcyjnego: Usuń wszelkie klucze lub wpisy tajne z kodu aplikacji i upewnij się, że przestrzegasz najlepszych rozwiązań w zakresie [zabezpieczeń](../cognitive-services-security.md?tabs=command-line%2ccsharp).

Gdy wszystko będzie gotowe do opublikowania aplikacji w środowisku produkcyjnym, zostanie wygenerowany plik APK gotowy do wydania, który jest formatem pliku pakietu dla aplikacji systemu Android. Ten plik APK musi być podpisany przy użyciu klucza prywatnego. Ta kompilacja wydania umożliwia bezpośrednie Dystrybuowanie aplikacji do urządzeń. 

Postępuj zgodnie z <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" dokumentacją przygotowania do wydania "  target="_blank"> przygotowania do wydania, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Aby dowiedzieć się, jak wygenerować klucz prywatny, podpisać aplikację i wygenerować wersję APK.  

Po utworzeniu podpisanego APK zapoznaj się z dokumentacją publikowanie aplikacji Opublikuj swoją aplikację, <a href="https://developer.android.com/studio/publish"  title=" "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Aby dowiedzieć się więcej o sposobie zwolnienia aplikacji.

## <a name="next-steps"></a>Następne kroki  

W tym przewodniku wyjaśniono, jak skonfigurować środowisko deweloperskie i zacząć pracę z przykładową aplikacją do rejestracji. Jeśli dopiero zaczynasz pracę w trybie macierzystym, możesz przeczytać ich [dokumenty z wprowadzeniem](https://reactnative.dev/docs/getting-started) , aby dowiedzieć się więcej na temat informacji w tle. Warto również zapoznać się z [interfejs API rozpoznawania twarzy](Overview.md). Zapoznaj się z innymi sekcjami dotyczącymi rejestracji aplikacji przed rozpoczęciem opracowywania.