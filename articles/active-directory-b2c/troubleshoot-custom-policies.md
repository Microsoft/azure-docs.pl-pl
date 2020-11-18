---
title: Rozwiązywanie problemów z zasadami niestandardowymi w Azure Active Directory B2C
description: Dowiedz się więcej na temat metod rozwiązywania problemów podczas pracy z zasadami niestandardowymi w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 87ea77540e5fcaac0c4231403473d25ebae46aac
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840257"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Rozwiązywanie problemów Azure AD B2C zasad niestandardowych i struktury środowiska obsługi tożsamości

W przypadku korzystania z zasad niestandardowych Azure Active Directory B2C (Azure AD B2C) mogą wystąpić problemy z skonfigurowaniem struktury środowiska tożsamości w formacie XML języka zasad. Uczenie się do pisania zasad niestandardowych może być podobne do uczenia się nowego języka. W tym artykule opisano niektóre narzędzia i wskazówki, które mogą pomóc w wykrywaniu i rozwiązywaniu problemów.

Ten artykuł koncentruje się na rozwiązywaniu problemów z konfiguracją zasad niestandardowych Azure AD B2C. Nie dotyczy to aplikacji jednostki uzależnionej ani jej biblioteki tożsamości.

## <a name="xml-editing"></a>Edycja XML

Najbardziej typowym błędem konfigurowania zasad niestandardowych jest nieprawidłowo sformatowana zawartość XML. Dobry Edytor XML jest niemal istotny. Wyświetla natywnie XML, zawartość kodów kolorów, wstępnie wypełnień wspólnych warunków, zachowuje indeksowane elementy XML i może być zweryfikowane względem schematu XML.

Dwa z naszych ulubionych edytorów to [Visual Studio Code](https://code.visualstudio.com/) i [Notatnik + +](https://notepad-plus-plus.org/).

Walidacja schematu XML identyfikuje błędy przed przekazaniem pliku XML. W folderze głównym [pakietu Starter](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)Pobierz plik definicji schematu XML *TrustFrameworkPolicy_0.3.0.0. xsd*. Aby dowiedzieć się, jak używać pliku schematu XSD do walidacji w edytorze, poszukaj *narzędzi XML* i *walidacji kodu XML* lub podobnych w dokumentacji edytora.

Przegląd reguł XML może być przydatny. Azure AD B2C odrzuca wszystkie wykryte błędy w formacie XML. Czasami nieprawidłowo sformatowana zawartość XML może spowodować błędy komunikatów o błędach.

## <a name="upload-policies-and-policy-validation"></a>Przekazywanie zasad i sprawdzanie poprawności zasad

Sprawdzanie poprawności pliku zasad XML jest wykonywane automatycznie przy przekazywaniu. Większość błędów powoduje niepowodzenie przekazywania. Walidacja obejmuje plik zasad, który jest przekazywany. Obejmuje również łańcuch plików, do których odnosi się plik przekazywania (plik zasad jednostki uzależnionej, plik rozszerzeń i plik podstawowy).

Typowe błędy walidacji obejmują następujące elementy:

> Fragment kodu błędu: `...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* Wartość elementu ClaimType może być błędna lub nie istnieje w schemacie.
* Wartości typu ClaimType muszą być zdefiniowane w co najmniej jednym z plików w ramach zasad.
    Na przykład: `<ClaimType Id="issuerUserId">`
* Jeśli element ClaimType jest zdefiniowany w pliku rozszerzeń, ale jest również używany w wartości profilu technicznym w pliku podstawowym, przekazanie pliku bazowego spowoduje wystąpienie błędu.

> Fragment kodu błędu: `...makes a reference to a ClaimsTransformation with id...`

* Przyczyny tego błędu mogą być takie same jak w przypadku błędu elementu ClaimType.

> Fragment kodu błędu: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Sprawdź, czy wartość TenantId w `<TrustFrameworkPolicy\>` `<BasePolicy\>` elementach i jest zgodna z docelową dzierżawą Azure AD B2C.

## <a name="troubleshoot-the-runtime"></a>Rozwiązywanie problemów dotyczących środowiska uruchomieniowego

* Używaj **teraz** i `https://jwt.ms` Przetestuj swoje zasady niezależnie od aplikacji sieci Web lub mobilnej. Ta witryna sieci Web działa jak aplikacja jednostki uzależnionej. Wyświetla zawartość tokenu sieci Web JSON (JWT) wygenerowanego przez zasady Azure AD B2C.

    Aby utworzyć aplikację testową, która może przekierować do `https://jwt.ms` inspekcji tokenu:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Aby śledzić wymianę komunikatów między przeglądarką klienta i Azure AD B2C, użyj [programu Fiddler](https://www.telerik.com/fiddler). Może to pomóc w uzyskaniu informacji o tym, gdzie podróż użytkownika kończy się niepowodzeniem w ramach kroków aranżacji.

* W **trybie programistycznym** Użyj [Application Insights](troubleshoot-with-application-insights.md) , aby śledzić aktywność użytkownika platformy do obsługi tożsamości. W **trybie programistycznym** można obserwować wymianę oświadczeń między strukturą środowiska tożsamości a różnymi dostawcami oświadczeń, które są zdefiniowane przez profile techniczne, takie jak dostawcy tożsamości, usługi oparte na interfejsie API, katalog Azure AD B2C użytkowników i inne usługi, takie jak usługa Azure AD Multi-Factor Authentication.

## <a name="recommended-practices"></a>Zalecane praktyki

**Zachowaj wiele wersji scenariuszy. Grupuj je w projekcie z Twoją aplikacją.** Podstawowe, rozszerzenia i pliki jednostek uzależnionych są bezpośrednio od siebie zależne. Zapisz je jako grupę. W miarę dodawania nowych funkcji do zasad należy zachować różne wersje robocze. Przemieszczanie wersji roboczych we własnym systemie plików przy użyciu kodu aplikacji, z którym się pracują. Aplikacje mogą wywoływać wiele różnych zasad jednostki uzależnionej w dzierżawie. Mogą one być zależne od oświadczeń, których oczekują od zasad Azure AD B2C.

**Opracowuj i Testuj profile techniczne ze znanymi przejazdami użytkowników.** Użyj zasad przetestowanych pakietów początkowych, aby skonfigurować profile techniczne. Przetestuj je oddzielnie przed wprowadzeniem ich do własnej podróży użytkownika.

**Opracowywanie i testowanie podróży użytkowników przy użyciu przetestowanych profilów technicznych.** Zmień kroki aranżacji przejazdu użytkownika przyrostowo. Stopniowo Kompiluj zamierzone scenariusze.

## <a name="next-steps"></a>Następne kroki

Dostępne w usłudze GitHub Pobierz plik archiwum [Active Directory-B2C-Custom-Policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) . zip. Można również sklonować repozytorium:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
