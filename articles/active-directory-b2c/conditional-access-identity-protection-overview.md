---
title: Ochrona tożsamości i dostęp warunkowy w Azure AD B2C
description: Dowiedz się, w jaki sposób Ochrona tożsamości zapewnia wgląd w ryzykowne logowania i wykrywanie zagrożeń. Dowiedz się, jak i czy dostęp warunkowy umożliwia wymuszanie zasad organizacyjnych na podstawie zdarzeń ryzyka w dzierżawach Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4a71740c487896208a3da7bf35bb39899c56937
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952067"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Ochrona tożsamości i dostęp warunkowy dla Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Zwiększ bezpieczeństwo Azure Active Directory B2C (Azure AD B2C), korzystając z Azure AD Identity Protection i dostępu warunkowego. Funkcje wykrywania ryzyka ochrony tożsamości, w tym ryzykowne Użytkownicy i ryzykowne logowania, są automatycznie wykrywane i wyświetlane w dzierżawie Azure AD B2C. Można utworzyć zasady dostępu warunkowego, które używają tych wykrywania ryzyka do określania akcji i wymuszania zasad organizacyjnych. Dzięki tym funkcjom Azure AD B2C właściciele aplikacji mogą mieć większą kontrolę nad ryzykownymi uwierzytelnieniami i zasadami dostępu.
  
Jeśli znasz już usługę [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) i [dostęp warunkowy](../active-directory/conditional-access/overview.md) w usłudze Azure AD, korzystanie z tych funkcji w Azure AD B2C będzie znanego doświadczenia z niewielkimi różnicami omówionymi w tym artykule.

![Dostęp warunkowy w dzierżawie B2C](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> Aby można było korzystać z dostępu warunkowego, wymagany jest Azure AD B2C Premium P2.

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Zalety usługi Identity Protection i dostępu warunkowego dla Azure AD B2C  

Kojarząc zasady dostępu warunkowego z wykrywaniem ryzyka ochrony tożsamości, można odpowiedzieć na ryzykowne uwierzytelnienia przy użyciu odpowiedniej akcji zasad.

- **Zyskaj nowy poziom widoczności dla zagrożeń związanych z uwierzytelnianiem dla aplikacji i bazy klientów**. W przypadku sygnałów z miliardów miesięcznych uwierzytelnień w usłudze Azure AD i koncie Microsoft algorytmy wykrywania ryzyka będą teraz flagować uwierzytelnianie jako niskie, średnie lub wysokie ryzyko dla lokalnego uwierzytelniania konsumentów lub obywateli.
- **Automatyczne rozwiązywanie zagrożeń przez skonfigurowanie własnego uwierzytelniania adaptacyjnego**. Dla określonych aplikacji można wymagać określonego zestawu użytkowników w celu zapewnienia drugiego czynnika uwierzytelniania, jak w przypadku uwierzytelniania wieloskładnikowego (MFA). Można też zablokować dostęp na podstawie wykrytych poziomów ryzyka. Podobnie jak w przypadku innych Azure AD B2C środowiska, można dostosować wyniki środowiska użytkownika końcowego z użyciem głosu, stylu i marki w organizacji. Możesz również wyświetlić alternatywy zaradcze, jeśli użytkownik nie może uzyskać dostępu.
- **Kontroluj dostęp na podstawie lokalizacji, grup i aplikacji**.Dostęp warunkowy może być również używany do kontrolowania sytuacji niezwiązanych z ryzykiem. Można na przykład wymagać uwierzytelniania wieloskładnikowego dla klientów uzyskujących dostęp do określonej aplikacji lub zablokować dostęp z określonych lokalizacje geograficzne.
- **Integracja z Azure AD B2C przepływami użytkowników i zasadami niestandardowymi platformy obsługi tożsamości**. Użyj istniejących dostosowanych środowisk i Dodaj kontrolki potrzebne do interfejsu z dostępem warunkowym. Możesz również zaimplementować zaawansowane scenariusze udzielania dostępu, takie jak dostęp oparty na wiedzy lub własny preferowany Dostawca usługi MFA.

## <a name="feature-differences-and-limitations"></a>Różnice i ograniczenia dotyczące funkcji

Usługa Identity Protection i dostęp warunkowy w Azure AD B2C zwykle działają tak samo jak w usłudze Azure AD, z następującymi wyjątkami:

- Security Center nie jest dostępna w Azure AD B2C.

- Usługi Identity Protection i dostęp warunkowy nie są obsługiwane w przypadku przepływów serwera ROPC na serwer w dzierżawach Azure AD B2C.

- W Azure AD B2C dzierżawy, wykrywanie ryzyka ochrony tożsamości są dostępne tylko dla lokalnych kont usługi B2C, a nie dla tożsamości społecznościowych, takich jak Google lub Facebook.

- W Azure AD B2C dzierżawy jest dostępny podzestaw wykrywania ryzyka związanego z ochroną tożsamości. Zobacz [Konfigurowanie ochrony tożsamości](conditional-access-identity-protection-setup.md#set-up-identity-protection).

- Funkcja zgodności urządzeń z dostępem warunkowym nie jest dostępna w dzierżawach Azure AD B2C.


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>Integracja dostępu warunkowego za pomocą przepływów użytkowników i zasad niestandardowych

W Azure AD B2C można wyzwolić warunki dostępu warunkowego z wbudowanych przepływów użytkowników. Możesz również włączyć dostęp warunkowy do zasad niestandardowych. Podobnie jak w przypadku innych aspektów przepływu użytkownika programu B2C, obsługa komunikatów użytkowników końcowych może być dostosowywana w zależności od głosu, marki i środków zaradczych w organizacji. Zobacz [Definiowanie profilu technicznego dostępu warunkowego](conditional-access-technical-profile.md).

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Można także zarządzać zasadami dostępu warunkowego w Azure AD B2C z interfejsem API Microsoft Graph. Aby uzyskać szczegółowe informacje, zobacz [dokumentację dostępu warunkowego](../active-directory/conditional-access/overview.md) i [informacje dotyczące Microsoft Graph](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta.md).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ochrony tożsamości i dostępu warunkowego dla Azure AD B2C](conditional-access-identity-protection-setup.md)
- [Informacje o ochronie tożsamości w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)
- [Informacje o dostępie warunkowym](../active-directory/conditional-access/overview.md)