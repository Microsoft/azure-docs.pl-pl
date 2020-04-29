---
title: Automatyzuj wdrażanie i wydawanie aplikacji mobilnych za pomocą usług Visual Studio App Center i Azure
description: Dowiedz się więcej na temat usług, takich jak App Center, które pomagają skonfigurować potok ciągłego dostarczania dla aplikacji mobilnych.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235353"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatyzuj wdrażanie i wydawanie aplikacji mobilnych za pomocą usług ciągłego dostarczania

Deweloperzy mogą napisać kod i sprawdzać go w repozytorium kodu, ale zatwierdzenia, które zaewidencjonowano, nie zawsze są spójne. Gdy wielu deweloperów pracuje nad tym samym projektem, problemy mogą być zintegrowane z integracją. Zespoły mogą działać w sytuacjach, w których nie działają, które są błędne, a rozwój projektu jest opóźniony. Deweloperzy muszą czekać, aż cały kod oprogramowania zostanie skompilowany i przetestowany w celu sprawdzenia pod kątem błędów, co sprawia, że proces jest powolny i mniej iteracyjny.

Dzięki ciągłemu dostarczaniu można zautomatyzować wdrażanie i wydawanie aplikacji mobilnych. Nie ma znaczenia, czy aplikacja jest dystrybuowana do grupy testerów, czy pracowników firmy (do testowania wersji beta) czy do sklepu z aplikacjami (w przypadku produkcji). Ciągłe dostarczanie sprawia, że wdrożenia są mniej ryzykowne i zachęca do szybkich iteracji. Nowe zmiany klientów można także zwolnić w sposób ciągły.

## <a name="distribute-application-binaries-to-beta-testers"></a>Dystrybuowanie plików binarnych aplikacji do testerów wersji beta
Testowanie wersji beta aplikacji mobilnej jest jednym z kluczowych kroków w procesie tworzenia aplikacji. Pomaga w wczesnym znajdowaniu usterek i problemów w aplikacji. Opinia poprawia jakość aplikacji, gdy wszystko jest gotowe do użycia w środowisku produkcyjnym.

Użyj następujących usług, aby włączyć potok ciągłego dostarczania w aplikacjach mobilnych.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[Dystrybucja App Center](/appcenter/distribution/) to narzędzie dla deweloperów umożliwiające szybkie wydanie kompilacji na urządzeniach. W przypadku pełnego środowiska instalacji portalu App Center dystrybucja jest zaawansowanym rozwiązaniem do dystrybucji testera aplikacji beta. Jest to również wygodna alternatywa do dystrybucji za pomocą publicznych sklepów z aplikacjami. Deweloperzy mogą jeszcze bardziej zautomatyzować przepływ pracy dystrybucji, App Center kompilacje i publiczne integracje magazynu aplikacji.

**Najważniejsze funkcje**
- Rozpowszechnij aplikację do testerów wersji beta i użytkowników i upewnij się, że wszystkie Testery znajdują się w najnowszej wersji aplikacji.
- Powiadamiaj testerów o nowych wersjach bez testerów, którzy przechodzą ponownie przez przepływ pobierania.
- Zarządzaj grupami dystrybucji dla różnych wersji aplikacji.
- Dystrybuuj do sklepów: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Uzyskaj wsparcie dla platformy dla systemów iOS, Android, macOS, systemu tvOS, Xamarin, w systemach natywnych, Unity i Cordova.
- Automatyczne rejestrowanie urządzeń z systemem iOS w Twoim profilu aprowizacji.

**Dokumentacja**
- [Zarejestruj się w usłudze Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do dystrybucji App Center](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) to w pełni oferowana usługa ciągłej integracji (ci) i ciągłe dostarczanie (CD), które współdziałają z preferowanym dostawcą usługi git. Azure Pipelines można wdrożyć w większości głównych usług w chmurze, takich jak usługi platformy Azure. Możesz zacząć od kodu w usłudze GitHub, w witrynie GitHub Enterprise Server, GitLab, BitBucket Cloud lub Azure Repos. Następnie można zautomatyzować kompilowanie, testowanie i wdrażanie kodu w celu Microsoft Azure, Google Cloud Platform lub Amazon Web Services (AWS).

**Najważniejsze funkcje**
- **Uproszczone środowisko oparte na zadaniach służące do konfigurowania serwera Ci:** Skonfiguruj serwer CI dla natywnych (Android, iOS i Windows) i międzyplatformowych aplikacji mobilnych (Xamarin, Cordova i reaguj natywnie).
- **Dowolny język, platforma i chmura:** Kompiluj, Testuj i wdrażaj aplikacje Node. js, Python, Java, PHP, Ruby, go, C/C++, C#, Android i iOS. Uruchamiaj równolegle w systemach Linux, macOS i Windows. Wdrażaj do dostawców chmury, takich jak Azure, AWS i Google Cloud Platform. Dystrybuuj aplikacje mobilne za poorednictwem kanałów beta i sklepów z aplikacjami.
- **Obsługa kontenera macierzystego:** Łatwo twórz nowe kontenery i wypchnij je do dowolnego rejestru. Wdróż kontenery na niezależnych hostach lub Kubernetes.
- **Zaawansowane przepływy pracy i funkcje:** Łatwo twórz łańcuchy kompilacji i kompilacje wieloetapowe. Uzyskaj pomoc techniczną dotyczącą YAML, integracji testów, bram wydań, raportowania i nie tylko.
- **Rozszerzalne:** Korzystaj z wielu zadań kompilowania, testowania i wdrażania utworzonych przez społeczność, które obejmują setki rozszerzeń od zapasu do SonarCloud. Można nawet wdrożyć z innych systemów CI, takich jak Jenkins. Elementy webhook i interfejsy API REST mogą ułatwić integrację.
- **Bezpłatne kompilacje hostowane w chmurze:** Te kompilacje są dostępne dla repozytoriów publicznych i prywatnych.
- **Obsługa wdrożenia innym dostawcom chmury:** Dostawcy obejmują AWS i Google Cloud Platform.

**Dokumentacja**
- [Wprowadzenie do przewodnika Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Rozpoczynanie pracy z usługą Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Dystrybuuj swoją aplikację bezpośrednio do sklepów z aplikacjami
Gdy aplikacja będzie gotowa do użycia w środowisku produkcyjnym i chcesz ją używać publicznie, należy ją przesłać do sklepów z aplikacjami, które mogą zostać pobrane przez klientów. Istnieje wiele sposobów dystrybuowania aplikacji bezpośrednio do sklepów z aplikacjami. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Dzięki [Rozproszeniu App Center](/appcenter/distribution/stores/)możesz publikować swoje aplikacje mobilne bezpośrednio w sklepach z aplikacjami. Gdy aplikacja będzie gotowa do pobrania przez użytkowników, można publikować pliki binarne aplikacji bezpośrednio z poziomu portalu Visual Studio App Center. 

Możesz bezpośrednio dystrybuować do:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Sklep Google Play](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
W sklepie z aplikacjami opracowaną i obsługiwaną przez firmę Apple użytkownicy mogą przeglądać i pobierać aplikacje opracowane dla urządzeń z systemem iOS, MacOS, systemu watchOS i systemu tvOS. Deweloperzy muszą przesłać swoje aplikacje dla systemu iOS do sklepu Apple App Store do użytku publicznego.

### <a name="google-play"></a>Google Play

Google Play to oficjalny sklep z aplikacjami dla systemu Android, w którym użytkownicy mogą przeglądać i pobierać aplikacje opracowane dla urządzeń z systemem Android publikowanych za pomocą usługi Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) to usługa oparta na chmurze w obszarze zarządzania mobilnością w przedsiębiorstwie, która pomaga zapewnić produktywność pracowników przy zachowaniu ochrony danych firmowych. Usługa Intune umożliwia:
- zarządzanie urządzeniami przenośnymi i komputerami używanymi przez pracowników do uzyskiwania dostępu do danych firmowych,
- Zarządzanie aplikacjami mobilnymi używanymi przez pracowników.
- Ochrona informacji o firmie przez kontrolowanie sposobu, w jaki pracownicy uzyskują do nich dostęp i udostępniają ją.
- Upewnij się, że urządzenia i aplikacje są zgodne z wymaganiami firmy dotyczącymi zabezpieczeń.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Wdrażaj aktualizacje bezpośrednio na urządzeniach użytkowników

### <a name="codepush"></a>Usługi codepush
Dzięki [usługi codepush](/appcenter/distribution/codepush/) w App Center Apache Cordova i reagowanie na natywnych deweloperów mogą wdrażać aktualizacje aplikacji mobilnych bezpośrednio na urządzeniach użytkowników. Działa jako centralne repozytorium, w którym deweloperzy mogą publikować pewne aktualizacje, takie jak JavaScript, HTML, CSS i zmiany obrazów. Następnie aplikacje mogą wysyłać zapytania o aktualizacje z repozytorium za pomocą dostarczonych zestawów SDK klienta. W ten sposób można mieć bardziej deterministyczny i bezpośredni model zaangażowania z użytkownikami podczas rozwiązywania usterek lub dodawania małych funkcji. Nie jest wymagane ponowne kompilowanie danych binarnych ani ich rozpowszechnianie za pomocą jakichkolwiek publicznych sklepów z aplikacjami.

**Najważniejsze funkcje**
- Oprogramowanie Cordova i reagowanie na natywnych deweloperów może wdrażać aktualizacje aplikacji mobilnych bezpośrednio na urządzeniach użytkowników bez ich zwolnienia w sklepie.
- Przydatne w przypadku naprawiania usterek lub dodawania i usuwania małych funkcji, które nie wymagają ponownego kompilowania danych binarnych i rozpowszechniania ich w odpowiednich magazynach.

**Dokumentacja**
- [Zarejestruj się w usłudze Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Rozpoczynanie pracy z usługą usługi codepush w App Center](/appcenter/distribution/codepush/)
- [Interfejs wiersza polecenia usługi codepush](/appcenter/distribution/codepush/cli)