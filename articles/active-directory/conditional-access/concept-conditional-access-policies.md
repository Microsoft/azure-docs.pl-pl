---
title: Tworzenie zasad dostępu warunkowego — Azure Active Directory
description: Jakie są wszystkie opcje dostępne w przypadku tworzenia zasad dostępu warunkowego i ich znaczenia?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a79b046170a5a3f3574895490aa649fd02da082
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016131"
---
# <a name="building-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Zgodnie z opisem w artykule [co to jest dostęp warunkowy](overview.md), zasady dostępu warunkowego to instrukcja if-then, **przypisań** i **kontroli dostępu**. Zasady dostępu warunkowego oferują sygnały ze sobą, aby podejmować decyzje i wymuszać zasady organizacyjne.

Jak organizacja tworzy te zasady? Co jest wymagane?

![Dostęp warunkowy (sygnały + decyzje + wymuszanie = zasady)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Przypisania

Część przypisania kontroluje osoby, co i gdzie zasady dostępu warunkowego.

### <a name="users-and-groups"></a>Użytkownicy i grupy

[Użytkownicy i grupy](concept-conditional-access-users-groups.md) mogą przypisywać osoby, które te zasady uwzględnią lub wykluczają. To przypisanie może obejmować wszystkich użytkowników, określonych grup użytkowników, ról katalogów lub użytkowników zewnętrznych Gości. 

### <a name="cloud-apps-or-actions"></a>Aplikacje w chmurze lub akcje

[Aplikacje w chmurze lub akcje](concept-conditional-access-cloud-apps.md) mogą zawierać lub wykluczać aplikacje w chmurze lub akcje użytkownika, które będą podlegać zasadom.

### <a name="conditions"></a>Warunki

Zasady mogą zawierać wiele [warunków](concept-conditional-access-conditions.md).

#### <a name="sign-in-risk"></a>Ryzyko związane z logowaniem

W przypadku organizacji z [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)wykryte wykrycia ryzyka mogą mieć wpływ na zasady dostępu warunkowego.

#### <a name="device-platforms"></a>Platformy urządzeń

Organizacje z wieloma platformami systemu operacyjnego urządzeń mogą wymuszać określone zasady na różnych platformach. 

Informacje używane do obliczania platformy urządzenia pochodzą z niezweryfikowanych źródeł, takich jak ciągi agenta użytkownika, które można zmienić.

#### <a name="locations"></a>Lokalizacje

Dane lokalizacji są udostępniane przez dane geolokalizacji IP. Administratorzy mogą określić lokalizacje i określić, że będą one uznawane za zaufane, takie jak te w lokalizacjach sieciowych organizacji.

#### <a name="client-apps"></a>Aplikacje klienckie

Domyślnie zasady dostępu warunkowego są stosowane do aplikacji przeglądarki, aplikacji mobilnych i klientów klasycznych obsługujących nowoczesne uwierzytelnianie. 

Ten warunek przypisania umożliwia zasadom dostępu warunkowego kierowanie określonych aplikacji klienckich, które nie korzystają z nowoczesnego uwierzytelniania. Te aplikacje obejmują klientów programu Exchange ActiveSync, starsze aplikacje pakietu Office, które nie korzystają z nowoczesnego uwierzytelniania, oraz protokoły poczty, takie jak IMAP, MAPI, POP i SMTP.

#### <a name="device-state"></a>Stan urządzenia

Ta kontrolka służy do wykluczania urządzeń, które są dołączone do hybrydowej usługi Azure AD lub oznaczone jako zgodne w usłudze Intune. To wykluczenie można wykonać w celu zablokowania urządzeń niezarządzanych. 

## <a name="access-controls"></a>Kontrole dostępu

Część kontroli dostępu w zasadach dostępu warunkowego steruje sposobem wymuszania zasad.

### <a name="grant"></a>Uprawnienie

[Przyznaj](concept-conditional-access-grant.md) administratorom środki wymuszania zasad, które mogą blokować lub przyznawać dostęp.

#### <a name="block-access"></a>Blokowanie dostępu

Blokuj dostęp to po prostu, że będzie blokować dostęp w określonych przypisaniach. Formant bloku jest zaawansowany i powinien być wielded z odpowiednią wiedzą.

#### <a name="grant-access"></a>Udzielanie dostępu

Formant Grant może wyzwalać wymuszanie jednej lub więcej kontrolek. 

- Wymagaj uwierzytelniania wieloskładnikowego (Azure Multi-Factor Authentication)
- Wymagaj, aby urządzenie było oznaczone jako zgodne (Intune)
- Wymagaj hybrydowego urządzenia dołączonego do usługi Azure AD
- Wymaganie zatwierdzonej aplikacji klienckiej
- Wymaganie zasad ochrony aplikacji

Administratorzy mogą wybrać, aby wymagać jednej z powyższych kontrolek lub wszystkich wybranych kontrolek przy użyciu poniższych opcji. Wartością domyślną dla wielu kontrolek jest wymaganie wszystkie.

- Wymagaj wszystkich zaznaczonych kontrolek (kontrolka i kontrola)
- Wymagaj jednej z wybranych kontrolek (kontrolki lub kontrolka)

### <a name="session"></a>Sesja

[Kontrolki sesji](concept-conditional-access-session.md) mogą ograniczyć środowisko 

- Użyj ograniczeń wymuszonych przez aplikację
   - Obecnie działa tylko z usługami Exchange Online i SharePoint Online.
      - Przekazuje informacje o urządzeniach, aby umożliwić kontrolę nad zapewnianiem pełnego lub ograniczonego dostępu.
- Użyj Kontrola dostępu warunkowego aplikacji
   - Używa sygnałów z Microsoft Cloud App Security, aby wykonać następujące czynności: 
      - Blokuj pobieranie, wycinanie, kopiowanie i drukowanie poufnych dokumentów.
      - Monitoruj ryzykowne zachowanie sesji.
      - Wymagaj etykietowania poufnych plików.
- Częstotliwość logowania
   - Możliwość zmiany domyślnej częstotliwości logowania dla nowoczesnego uwierzytelniania.
- Sesja trwałej przeglądarki
   - Umożliwia użytkownikom zalogowanie się po zamknięciu i ponownym otwarciu okna przeglądarki.

## <a name="simple-policies"></a>Zasady proste

Zasady dostępu warunkowego muszą zawierać co najmniej następujące elementy, które mają zostać wymuszone:

- **Nazwa** zasad.
- **Przypisania**
   - **Użytkownicy i/lub grupy** , do których mają być stosowane zasady.
   - **Aplikacje w chmurze lub akcje** , do których mają zostać zastosowane zasady.
- **Kontrole dostępu**
   - **Udziel** lub **Blokuj** kontrolki

![Puste zasady dostępu warunkowego](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

Artykuł [typowe zasady dostępu warunkowego](concept-conditional-access-policy-common.md) obejmują pewne zasady, które będą przydatne dla większości organizacji.

## <a name="next-steps"></a>Następne kroki

[Tworzenie zasad dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json#create-a-conditional-access-policy)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)

[Planowanie wdrożenia usługi Azure Multi-Factor Authentication opartej na chmurze](../authentication/howto-mfa-getstarted.md)

[Zarządzanie zgodnością urządzeń z usługą Intune](/intune/device-compliance-get-started)

[Microsoft Cloud App Security i dostęp warunkowy](/cloud-app-security/proxy-intro-aad)
