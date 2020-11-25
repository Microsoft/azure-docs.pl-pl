---
title: Dostęp warunkowy — Wymagaj uwierzytelniania wieloskładnikowego dla wszystkich użytkowników — Azure Active Directory
description: Utwórz niestandardowe zasady dostępu warunkowego, aby wymagać od wszystkich użytkowników przeprowadzenia uwierzytelniania wieloskładnikowego
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e6185c4bde71285fc163cae2af46f64ba052195
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994761"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Dostęp warunkowy: Wymagaj uwierzytelniania wieloskładnikowego dla wszystkich użytkowników

Jako Alex Weinert, katalog zabezpieczeń tożsamości w firmie Microsoft, zawiera wpis w blogu " [PA $ $Word nie ma znaczenia](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> Twoje hasło nie ma znaczenia, ale usługa MFA! W oparciu o nasze studia Twoje konto ma więcej niż 99,9% mniej trudniejsze do naruszenia w przypadku korzystania z usługi MFA.

Wskazówki zawarte w tym artykule ułatwią organizacji Tworzenie zasad usługi MFA o zrównoważonym obciążeniu dla danego środowiska.

## <a name="user-exclusions"></a>Wykluczenia użytkowników

Zasady dostępu warunkowego to zaawansowane narzędzia, dlatego zalecamy wykluczenie następujących kont z zasad:

* **Dostęp awaryjny** lub konta z **przerwaniem** do blokowania kont w całej dzierżawie. W mało prawdopodobnym scenariuszu wszyscy administratorzy są Zablokowani z Twojej dzierżawy, w celu zalogowania się do dzierżawy można użyć konta administratora z dostępem awaryjnym.
   * Więcej informacji można znaleźć w artykule [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../roles/security-emergency-access.md).
* **Konta usług** i jednostki **usługi**, takie jak konto synchronizacji Azure AD Connect. Konta usług są kontami nieinteraktywnymi, które nie są powiązane z żadnym konkretnym użytkownikiem. Są one zwykle używane przez usługi zaplecza umożliwiające programistyczny dostęp do aplikacji, ale są również używane do logowania się do systemów w celach administracyjnych. Konta usług, takie jak te, powinny być wykluczone, ponieważ nie można programowo zakończyć usługi MFA. Wywołania wykonywane przez jednostki usługi nie są blokowane przez dostęp warunkowy.
   * Jeśli w organizacji są używane te konta w skryptach lub kodzie, należy rozważyć zastępowanie ich [tożsamościami zarządzanymi](../managed-identities-azure-resources/overview.md). W ramach tymczasowego obejścia można wykluczyć te określone konta z zasad linii bazowej.

## <a name="application-exclusions"></a>Wykluczenia aplikacji

Organizacje mogą korzystać z wielu aplikacji w chmurze. Nie wszystkie te aplikacje mogą wymagać jednakowego zabezpieczenia. Na przykład aplikacje płacowe i frekwencje mogą wymagać uwierzytelniania wieloskładnikowego, ale Cafeteria prawdopodobnie nie. Administratorzy mogą zdecydować się na wykluczenie określonych aplikacji z ich zasad.

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniższe kroki pomogą utworzyć zasady dostępu warunkowego, aby wymagać od wszystkich użytkowników przeprowadzenia uwierzytelniania wieloskładnikowego.

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory**  >  **Security**  >  **dostępu warunkowego** zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie** wybierz pozycję **Wszyscy użytkownicy** .
   1. W obszarze **Wyklucz** wybierz pozycję **Użytkownicy i grupy** , a następnie wybierz opcję dostęp awaryjny lub konta w ramach swojej organizacji. 
   1. Kliknij **Gotowe**.
1. W obszarze **aplikacje w chmurze lub akcje**  >  **Dołącz** wybierz pozycję **wszystkie aplikacje w chmurze**.
   1. W obszarze **Wyklucz** wybierz wszystkie aplikacje, które nie wymagają uwierzytelniania wieloskładnikowego.
1. W obszarze **warunki**  >  **aplikacje klienckie (wersja zapoznawcza)** w obszarze **Wybierz aplikacje klienckie te zasady zostaną zastosowane, aby** pozostawić wszystkie ustawienia domyślne, a następnie wybierz pozycję **gotowe**.
1. W obszarze **Kontrola dostępu**  >  **przyznawanie** wybierz pozycję **Udziel dostępu**, **Wymagaj uwierzytelniania wieloskładnikowego**, a następnie wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć zasady.

### <a name="named-locations"></a>Nazwane lokalizacje

Organizacje mogą dołączać znane lokalizacje sieciowe znane jako **nazwane lokalizacje** do zasad dostępu warunkowego. Te nazwane lokalizacje mogą obejmować zaufane sieci IPv4, takie jak te dla głównej lokalizacji biurowej. Aby uzyskać więcej informacji o konfigurowaniu nazwanych lokalizacji, zobacz artykuł [jaki jest warunek lokalizacji w Azure Active Directory dostęp warunkowy?](location-condition.md)

W przykładowych zasadach powyżej organizacja może zrezygnować z używania uwierzytelniania wieloskładnikowego w przypadku uzyskiwania dostępu do aplikacji w chmurze z sieci firmowej. W takim przypadku można dodać do zasad następującą konfigurację:

1. W obszarze **przypisania** wybierz pozycję **warunki**  >  **Locations**.
   1. Skonfiguruj **tak**.
   1. Uwzględnij **dowolną lokalizację**.
   1. Wyklucz **wszystkie Zaufane lokalizacje**.
   1. Kliknij **Gotowe**.
1. Kliknij **Gotowe**.
1. **Zapisz** zmiany zasad.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko Raport z dostępem warunkowym](howto-conditional-access-insights-reporting.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)
