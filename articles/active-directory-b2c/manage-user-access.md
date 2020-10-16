---
title: Zarządzanie dostępem użytkowników w Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak identyfikować małoletnie, zbierać daty urodzenia i danych kraju/regionu oraz otrzymywać warunki użytkowania w aplikacji za pomocą Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51a66d74750afa6c46dba7fa442477e85effb2d6
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102055"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Zarządzanie dostępem użytkowników w Azure Active Directory B2C

W tym artykule omówiono sposób zarządzania dostępem użytkowników do aplikacji przy użyciu Azure Active Directory B2C (Azure AD B2C). Zarządzanie dostępem w aplikacji obejmuje następujące metody:

- Identyfikowanie drobnych i kontrolowanie dostępu użytkowników do aplikacji.
- Wymaganie zgody rodziców na niewielkie użycie aplikacji.
- Gromadzenie danych o urodzeniu i kraju/regionie użytkowników.
- Przechwytywanie umowy o warunki użytkowania i dostęp kontroli.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Kontrola dostępu pomocniczego

Aplikacje i organizacje mogą zdecydować się na zablokowanie małoletnich z używania aplikacji i usług, które nie są przeznaczone dla tych odbiorców. Alternatywnie aplikacje i organizacje mogą zdecydować się na zaakceptowanie małoletnich, a następnie zarządzać zgodą rodziców oraz dostarczać dozwolone środowiska dla małoletnich, zgodnie z regułami biznesowymi i dozwolonych przez rozporządzenie.

Jeśli użytkownik jest zidentyfikowany jako pomocniczy, można ustawić przepływ użytkownika w Azure AD B2C na jedną z trzech opcji:

- **Wysyłanie podpisanej JWT id_token z powrotem do aplikacji**: użytkownik jest zarejestrowany w katalogu, a token jest zwracany do aplikacji. Następnie aplikacja przechodzi przez zastosowanie reguł firmy. Na przykład aplikacja może kontynuować proces wyrażania zgody rodzicielskiej. Aby użyć tej metody, wybierz otrzymywanie oświadczeń **grupy wiekowej** i **consentProvidedForMinor** z aplikacji.

- **Wyślij niepodpisany token JSON do aplikacji**: Azure AD B2C powiadamia aplikację, że użytkownik jest częścią podrzędną, i zapewnia stan zgody użytkownika na rodzica. Następnie aplikacja przechodzi przez zastosowanie reguł firmy. Token JSON nie kończy pomyślnego uwierzytelnienia w aplikacji. Aplikacja musi przetworzyć nieuwierzytelniony użytkownik zgodnie z oświadczeniami zawartymi w tokenie JSON, które mogą zawierać **nazwę**, **adres e-mail**, **grupy wiekowej**i **consentProvidedForMinor**.

- **Zablokuj użytkownika**: Jeśli użytkownik jest niewielki i nie podano zgody rodzicielskiej, Azure AD B2C może powiadomić użytkownika o tym, że są one zablokowane. Nie wydano tokenu, dostęp jest zablokowany, a konto użytkownika nie jest tworzone podczas podróży rejestracji. Aby zaimplementować to powiadomienie, należy podać odpowiednią stronę zawartości HTML/CSS, aby poinformować użytkownika i przedstawić odpowiednie opcje. Aplikacja nie potrzebuje dalszych działań w przypadku nowych rejestracji.

## <a name="get-parental-consent"></a>Uzyskaj zgodę rodzicielską

W zależności od regulacji aplikacji zgoda rodzicielska może być wymagana przez użytkownika, który jest zweryfikowany jako osoba dorosła. Azure AD B2C nie oferuje doświadczenia w celu zweryfikowania wieku osoby, a następnie umożliwienia zweryfikowania zgody użytkownika na przyznanie osobie dorosłej. To środowisko musi być dostarczone przez aplikację lub innego dostawcę usług.

Poniżej przedstawiono przykładowy przepływ użytkownika do zbierania zgody rodzicielskiej:

1. Operacja [interfejsu API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api) identyfikuje użytkownika jako element pomocniczy i zwraca dane użytkownika do aplikacji w postaci niepodpisanego tokenu JSON.

2. Aplikacja przetwarza token JSON i pokazuje ekran pomocniczy, powiadamiając o tym, że wymagana jest zgoda rodzicielska i żądając zgody na nadrzędną online.

3. Azure AD B2C pokazuje, że użytkownik może zalogować się w normalny sposób i wystawia token dla aplikacji, która jest ustawiona do dołączenia **legalAgeGroupClassification = "minorWithParentalConsent"**. Aplikacja zbiera adres e-mail elementu nadrzędnego i sprawdza, czy jest to osoba dorosła. W tym celu używa zaufanego źródła, takiego jak Biuro identyfikatora Narodowego, weryfikacja licencji lub potwierdzenie karty kredytowej. Jeśli weryfikacja zakończy się pomyślnie, aplikacja poprosi pomocnicze o zalogowanie się przy użyciu przepływu użytkownika Azure AD B2C. Jeśli zostanie wyrzucona zgoda (na przykład jeśli **legalAgeGroupClassification = "minorWithoutParentalConsent"**), Azure AD B2C zwraca token JSON (a nie nazwę logowania) do aplikacji, aby ponownie uruchomić proces wyrażania zgody. Opcjonalnie można dostosować przepływ użytkowników, tak aby drobna lub osoba dorosła mogła odzyskać dostęp do konta pomocniczego, wysyłając kod rejestracyjny na pomocniczy adres e-mail lub adres e-mail osoby dorosłej w rekordzie.

4. Aplikacja oferuje opcję elementu pomocniczego do odwołania.

5. Gdy osoba nieletnia lub Dorosła wycofa zgodę, interfejs API Microsoft Graph może zostać użyty do zmiany **consentProvidedForMinor** na **odmowa**. Alternatywnie, aplikacja może zdecydować się na usunięcie niewielkiego, którego zgody zostało odwołane. Opcjonalnie można dostosować przepływ użytkowników, tak aby uwierzytelniona pomocnicza (lub nadrzędna, która korzysta z konta pomocniczego) mogła odwołać zgodę. Azure AD B2C rekordy **consentProvidedForMinor** jako **odmowa**.

Aby uzyskać więcej informacji na temat **legalAgeGroupClassification**, **consentProvidedForMinor**i **grupy wiekowej**, zobacz [Typ zasobu użytkownika](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Aby uzyskać więcej informacji o atrybutach niestandardowych, zobacz [Używanie atrybutów niestandardowych do zbierania informacji o użytkownikach](user-flow-custom-attributes.md). Podczas rozwiązywania atrybutów rozszerzonych za pomocą interfejsu API Microsoft Graph należy użyć długiej wersji atrybutu, takiej jak *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Zbierz dane dotyczące daty urodzenia i kraju/regionu

Aplikacje mogą polegać na Azure AD B2C, aby zebrać datę urodzenia (DOB) i informacje o kraju/regionie ze wszystkich użytkowników podczas rejestracji. Jeśli te informacje jeszcze nie istnieją, aplikacja może zażądać jej od użytkownika podczas kolejnego uwierzytelniania (logowania). Użytkownicy nie mogą przechodzić bez podawania informacji o DOB i kraju/regionie. Azure AD B2C używa tych informacji do określenia, czy dana osoba jest traktowana jako drobna zgodnie ze standardami obowiązującymi tego kraju/regionu.

Dostosowany przepływ użytkowników może zbierać informacje o DOB i kraju/regionie oraz używać transformacji oświadczeń Azure AD B2C, aby określić **grupy wiekowej** i utrzymać wynik (lub utrzymać dob i informacje o kraju/regionie bezpośrednio) w katalogu.

Poniższe kroki pokazują logikę, która jest używana do obliczania **grupy wiekowej** od daty urodzenia użytkownika:

1. Spróbuj znaleźć kraj/region według kodu kraju/regionu na liście. Jeśli kraj/region nie zostanie znaleziony, Wróć do **domyślnego**.

2. Jeśli węzeł **MinorConsent** jest obecny w elemencie Country/Region:

    a. Oblicz datę, do której użytkownik musi zostać uznany za osobę dorosłą. Na przykład, jeśli bieżąca data to 14 marca 2015, a **MinorConsent** to 18, Data urodzenia nie może być późniejsza niż 14 marca 2000.

    b. Porównaj minimalną datę urodzenia z rzeczywistą datą urodzenia. Jeśli minimalna Data urodzenia jest wcześniejsza niż data urodzenia użytkownika, obliczenie zwróci wartość **pomocniczą** jako obliczenie grupy wiekowej.

3. Jeśli węzeł **MinorNoConsentRequired** jest obecny w elemencie Country/region, powtórz kroki od 2A do 2b przy użyciu wartości z **MinorNoConsentRequired**. Wyjście 2b zwraca **MinorNoConsentRequired** , jeśli minimalna Data urodzenia jest wcześniejsza niż data urodzenia użytkownika.

4. Jeśli żadna z obliczeń nie zwraca wartości true, obliczenia zwracają wartość **dorosłą**.

Jeśli aplikacja ma niezawodne dane DOB lub kraju/regionu według innych metod, aplikacja może używać interfejs API programu Graph do aktualizowania rekordu użytkownika przy użyciu tych informacji. Na przykład:

- Jeśli użytkownik jest znany jako osoba dorosła, należy zaktualizować atrybut katalogu **grupy wiekowej** wartością **dorosłą**.
- Jeśli użytkownik jest znany jako pomocniczy, należy zaktualizować atrybut katalogu **grupy wiekowej** o wartości **mniejszości** i ustawić **consentProvidedForMinor**, zgodnie z potrzebami.

Aby uzyskać więcej informacji na temat zbierania danych DOB, zobacz [Korzystanie z kontroli wieku w Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Przechwyć umowę dotyczącą warunków użytkowania

Podczas opracowywania aplikacji zwykle przechwytuje ona akceptację warunków użytkowania przez użytkowników w swoich aplikacjach bez względu na to, że mają one charakter nieistotny i mają udział w katalogu użytkownika. Istnieje jednak możliwość użycia przepływu użytkownika Azure AD B2C w celu zebrania akceptacji warunków użytkowania przez użytkownika, ograniczenia dostępu, jeśli akceptacja nie zostanie udzielona, i wymuszenie akceptacji przyszłych zmian warunków użytkowania w oparciu o datę ostatniej akceptacji i datę najnowszej wersji warunków użytkowania.

**Warunki użytkowania** mogą również obejmować "zgodę na udostępnianie danych podmiotom trzecim". W zależności od lokalnych zasad i reguł firmy można zebrać wszystkie warunki w połączeniu z użytkownikiem lub można zezwolić użytkownikowi na akceptowanie jednego warunku, a nie drugiego.

W poniższych krokach opisano sposób zarządzania warunkami użytkowania:

1. Zanotuj akceptację warunków użytkowania i datę akceptacji przy użyciu interfejs API programu Graph i atrybutów rozszerzonych. Można to zrobić za pomocą wbudowanych i niestandardowych przepływów użytkownika. Zalecamy tworzenie i Używanie atrybutów **extension_termsOfUseConsentDateTime** i **extension_termsOfUseConsentVersion** .

2. Utwórz wymagane pole wyboru z etykietą "Akceptuj warunki użytkowania" i Zapisz wynik podczas tworzenia konta. Można to zrobić za pomocą wbudowanych i niestandardowych przepływów użytkownika.

3. Azure AD B2C przechowuje umowę warunki użytkowania i akceptację użytkownika. Za pomocą interfejs API programu Graph można wykonywać zapytania dotyczące stanu dowolnego użytkownika, odczytując atrybut rozszerzenia używany do rejestrowania odpowiedzi (na przykład **termsOfUseTestUpdateDateTime**). Można to zrobić za pomocą wbudowanych i niestandardowych przepływów użytkownika.

4. Wymagaj akceptacji zaktualizowanych Warunków użytkowania, porównując datę akceptacji na datę najnowszej wersji warunków użytkowania. Daty można porównać tylko przy użyciu niestandardowego przepływu użytkownika. Użyj atrybutu rozszerzonego **extension_termsOfUseConsentDateTime**i porównaj wartość z zakresem **termsOfUseTextUpdateDateTime**. Jeśli akceptacja jest stara, Wymuś nową akceptację, wyświetlając ekran z własnymi potwierdzeń. W przeciwnym razie Zablokuj dostęp za pomocą logiki zasad.

5. Wymagaj akceptacji zaktualizowanych Warunków użytkowania, porównując numer wersji akceptacji z najnowszym zaakceptowanym numerem wersji. Numery wersji można porównać tylko przy użyciu niestandardowego przepływu użytkownika. Użyj atrybutu rozszerzonego **extension_termsOfUseConsentDateTime**i porównaj wartość z zakresem **extension_termsOfUseConsentVersion**. Jeśli akceptacja jest stara, Wymuś nową akceptację, wyświetlając ekran z własnymi potwierdzeń. W przeciwnym razie Zablokuj dostęp za pomocą logiki zasad.

Można przechwycić akceptację warunków użytkowania w następujących scenariuszach:

- Nowy użytkownik rejestruje się. Zostaną wyświetlone warunki użytkowania i zostanie zapisany wynik akceptacji.
- Użytkownik loguje się, kto wcześniej zaakceptował najnowsze lub aktywne warunki użytkowania. Warunki użytkowania nie są wyświetlane.
- Użytkownik loguje się, kto nie zaakceptował już najnowszych lub aktywnych warunków użytkowania. Zostaną wyświetlone warunki użytkowania i zostanie zapisany wynik akceptacji.
- Użytkownik loguje się, kto już zaakceptował starszą wersję warunków użytkowania, która jest teraz aktualizowana do najnowszej wersji. Zostaną wyświetlone warunki użytkowania i zostanie zapisany wynik akceptacji.

Na poniższej ilustracji przedstawiono zalecany przepływ użytkownika:

![Diagram wykresu przepływu przedstawiający zalecany przepływ użytkowników akceptacji](./media/manage-user-access/user-flow.png)

Poniżej znajduje się przykład wyrażenia warunków użytkowania w ramach daty. Jeśli to zgłoszenie `extension_termsOfUseConsentDateTime` jest starsze niż `2025-01-15T00:00:00` , Wymuś nowe zatwierdzenie, sprawdzając je `termsOfUseConsentRequired` i wyświetlając ekran z własnym potwierdzeń. 

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2025-01-15T00:00:00" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Poniżej znajduje się przykład zgody na użycie warunków użytkowania w ramach roszczeń. Jeśli wartość tego `extension_termsOfUseConsentVersion` żądania nie jest równa `V1` , Wymuś nowe zatwierdzenie, sprawdzając `termsOfUseConsentRequired` wartość logiczną i wyświetlając ekran z własnymi potwierdzeń.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak usunąć i wyeksportować dane użytkowników, zobacz [Zarządzanie danymi użytkownika](manage-user-data.md).
- Aby zapoznać się z przykładowymi zasadami niestandardowymi, które implementują monit dotyczący warunków użytkowania, zobacz [B2C IEF Custom Policy — Rejestracja i logowanie przy użyciu wiersza polecenia "warunki użytkowania"](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou).
