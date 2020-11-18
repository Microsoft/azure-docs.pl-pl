---
title: Dokumentacja podręcznika obsługi ogólnej Azure Active Directory
description: W tym przewodniku odwołuje się opis operacji sprawdzania i działań, które należy wykonać w celu zabezpieczenia ogólnych operacji
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: d5a8fe4192c3778e259ed18239a4198398d8807b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836839"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Dokumentacja podręcznika obsługi ogólnej Azure Active Directory

W tej części [przewodnika dotyczącego odwołań usługi Azure AD](active-directory-ops-guide-intro.md) opisano operacje sprawdzania i działania, które należy wykonać w celu zoptymalizowania ogólnych operacji Azure Active Directory (Azure AD).

> [!NOTE]
> Te zalecenia są aktualne w dniu opublikowania, ale mogą ulec zmianie w czasie. Organizacje powinny stale szacować swoje praktyki operacyjne jako produkty i usługi firmy Microsoft, które są roznoszone wraz z upływem czasu.

## <a name="key-operational-processes"></a>Najważniejsze procesy operacyjne

### <a name="assign-owners-to-key-tasks"></a>Przypisywanie właścicieli do kluczowych zadań

Zarządzanie Azure Active Directory wymaga ciągłego wykonywania kluczowych zadań operacyjnych i procesów, które mogą nie być częścią projektu wdrożenia. Nadal ważne jest, aby skonfigurować te zadania w celu zoptymalizowania środowiska. Najważniejsze zadania i ich zalecani właściciele obejmują:

| Zadanie | Właściciel |
| :- | :- |
| Udoskonalenia dotyczące tożsamości dotyczącej bezpiecznego wyniku | Zespół ds. operacji InfoSec |
| Obsługa serwerów Azure AD Connect | Zespół operacji IAM |
| Regularne wykonywanie i Klasyfikacja raportów IdFix | Zespół operacji IAM |
| Klasyfikacja alerty programu Azure AD Connect Health na potrzeby synchronizacji i AD FS | Zespół operacji IAM |
| Jeśli nie korzystasz z Azure AD Connect Health, klient ma odpowiedni proces i narzędzia do monitorowania infrastruktury niestandardowej | Zespół operacji IAM |
| Jeśli nie korzystasz z AD FS, klient ma odpowiedni proces i narzędzia do monitorowania infrastruktury niestandardowej | Zespół operacji IAM |
| Monitoruj dzienniki hybrydowe: aplikacja usługi Azure AD łączników serwera proxy | Zespół operacji IAM |
| Monitoruj dzienniki hybrydowe: przekazywanie agentów uwierzytelniania | Zespół operacji IAM |
| Monitoruj dzienniki hybrydowe: usługa zapisywania zwrotnego haseł | Zespół operacji IAM |
| Monitorowanie dzienników hybrydowych: lokalna Brama ochrony haseł | Zespół operacji IAM |
| Monitoruj dzienniki hybrydowe: rozszerzenie NPS usługi Azure AD MFA (jeśli dotyczy) | Zespół operacji IAM |

Podczas przeglądania listy może być konieczne przypisanie właściciela do zadań, w których brakuje właściciela, lub dostosować własność do zadań z właścicielami, które nie są wyrównane do powyższych zaleceń.

#### <a name="owners-recommended-reading"></a>Zalecane odczytywanie przez właścicieli

- [Przypisywanie ról administratorów w usłudze Azure Active Directory](../roles/permissions-reference.md)
- [Nadzór na platformie Azure](../../governance/index.yml)

## <a name="hybrid-management"></a>Zarządzanie hybrydowe

### <a name="recent-versions-of-on-premises-components"></a>Najnowsze wersje składników lokalnych

Najbardziej aktualne wersje składników lokalnych zapewniają klientom wszystkie najnowsze aktualizacje zabezpieczeń, ulepszenia wydajności oraz funkcje, które mogą pomóc w dalszej uproszczeniu środowiska. Większość składników ma ustawienie automatycznego uaktualniania, które automatyzuje proces uaktualniania.

Te składniki obejmują:

- Azure AD Connect
- Łączniki serwer proxy aplikacji usługi Azure AD platformy Azure
- Agenci uwierzytelniania przekazującego usługi Azure AD
- Azure AD Connect Health agenci

O ile nie został on ustanowiony, należy zdefiniować proces uaktualniania tych składników i korzystać z funkcji automatycznego uaktualniania, jeśli jest to możliwe. Jeśli znajdziesz składniki, które są już sześć lub więcej miesięcy, należy przeprowadzić uaktualnienie tak szybko, jak to możliwe.

#### <a name="hybrid-management-recommended-reading"></a>Zalecany odczyt z funkcji zarządzania hybrydowego

- [Azure AD Connect: automatyczne uaktualnianie](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [Omówienie łączników usługi Azure serwer proxy aplikacji usługi Azure AD | Aktualizacje automatyczne](../manage-apps/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health linia bazowa alertu

Organizacje powinny wdrożyć [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) na potrzeby monitorowania i raportowania Azure AD Connect i AD FS. Azure AD Connect i AD FS są podstawowymi składnikami, które mogą przerwać zarządzanie cyklem życia i uwierzytelnianie, a tym samym prowadzić do przestoju. Azure AD Connect Health pomaga monitorować i uzyskiwać wgląd w lokalną infrastrukturę tożsamości w taki sposób, aby zapewnić niezawodność środowiska.

![Azure AD Connect architektura kondycji](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Podczas monitorowania kondycji środowiska należy natychmiast rozwiązać wszystkie alerty o wysokiej ważności, a po nich alerty o niższej ważności.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health zalecanym odczytywaniem

- [Instalowanie agenta programu Azure AD Connect Health](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>Dzienniki agentów lokalnych

Niektóre usługi zarządzania tożsamościami i dostępem wymagają agentów lokalnych do włączania scenariuszy hybrydowych. Przykładami mogą być: Resetowanie hasła, uwierzytelnianie przekazywane (PTA), platforma Azure serwer proxy aplikacji usługi Azure AD i rozszerzenie NPS usługi Azure AD MFA. Jest to kluczowy plan bazowy zespołu operacji i monitoruje kondycję tych składników przez archiwizowanie i analizowanie dzienników agentów składników przy użyciu rozwiązań, takich jak System Center Operations Manager lub SIEM. Jest to równie ważne w przypadku zespołu operacji INFOSEC lub pomocy technicznej zrozumienie, jak rozwiązywać problemy z wzorcem błędów.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Agenci w lokalnych dziennikach rejestrują zalecaną czytelność

- [Rozwiązywanie problemów z serwerem proxy aplikacji](../manage-apps/application-proxy-troubleshoot.md)
- [Rozwiązywanie problemów z samoobsługowym resetowaniem hasła — Azure Active Directory](../authentication/troubleshoot-sspr.md)
- [Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](../manage-apps/application-proxy-connectors.md)
- [Azure AD Connect: Rozwiązywanie problemów z uwierzytelnianiem przekazywanym](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [Rozwiązywanie problemów z kodami błędów dla rozszerzenia serwera NPS usługi Azure AD MFA](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>Zarządzanie agentami lokalnymi

Stosowanie najlepszych rozwiązań może pomóc w optymalnym działaniu agentów lokalnych. Należy wziąć pod uwagę następujące najlepsze rozwiązania:

- Zaleca się używanie wielu łączników serwera proxy aplikacji usługi Azure AD na grupę łączników w celu zapewnienia bezproblemowego równoważenia obciążenia i wysokiej dostępności poprzez uniknięcie pojedynczych punktów awarii podczas uzyskiwania dostępu do aplikacji serwera proxy. Jeśli w grupie łączników, która obsługuje aplikacje w środowisku produkcyjnym, istnieje tylko jeden łącznik, należy wdrożyć co najmniej dwa łączniki w celu zapewnienia nadmiarowości.
- Tworzenie i używanie grupy łączników serwera proxy aplikacji na potrzeby debugowania może być przydatne w scenariuszach rozwiązywania problemów oraz podczas dołączania nowych aplikacji lokalnych. Zalecamy również zainstalowanie narzędzi sieciowych, takich jak Message Analyzer i programu Fiddler na maszynach łączników.
- W celu zapewnienia bezproblemowego równoważenia obciążenia i wysokiej dostępności zaleca się użycie wielu agentów uwierzytelniania przekazywanego, unikając single point of failure podczas przepływu uwierzytelniania. Należy koniecznie wdrożyć co najmniej dwa agenci uwierzytelniania przekazywanego w celu zapewnienia nadmiarowości.

#### <a name="on-premises-agents-management-recommended-reading"></a>Zalecane odczyty lokalnego zarządzania agentami

- [Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](../manage-apps/application-proxy-connectors.md)
- [Uwierzytelnianie przekazywane przez usługę Azure AD — Szybki Start](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Zarządzanie na dużą skalę

### <a name="identity-secure-score"></a>Wskaźnik bezpieczeństwa tożsamości

[Wynikowa tożsamość tożsamości](./identity-secure-score.md) zapewnia wymierny pomiar stan zabezpieczeń organizacji. Jest to klucz, aby stale przeglądać i rozwiązywać zgłoszone wnioski i dążyć do uzyskania najwyższej możliwej oceny. Ten wskaźnik ułatwi Ci:

- Obiektywne mierzenie poziomu bezpieczeństwa tożsamości
- Planowanie poprawy bezpieczeństwa tożsamości
- Sprawdzanie powodzenia wprowadzonych udoskonaleń

![Wskaźnik bezpieczeństwa](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Jeśli w danej organizacji nie ma obecnie żadnego programu do monitorowania zmian w bezpiecznej oceny tożsamości, zaleca się zaimplementowanie planu i przypisanie właścicieli do monitorowania i zwiększenia liczby działań. Organizacje powinny skorygować działania związane z wynikiem oceny o wpływie wyższym niż 30 tak szybko, jak to możliwe.

### <a name="notifications"></a>Powiadomienia

Firma Microsoft wysyła wiadomości e-mail do administratorów w celu powiadomienia różnych zmian w usłudze, wymaganych aktualizacji konfiguracji i błędów wymagających interwencji administratora. Należy pamiętać, że klienci ustawiają adresy e-mail powiadomień, aby powiadomienia były wysyłane do odpowiednich członków zespołu, którzy będą mogli potwierdzić i korzystać ze wszystkich powiadomień. Zalecamy dodanie wielu odbiorców do [centrum wiadomości](/office365/admin/manage/message-center) i zażądanie powiadomienia (w tym powiadomienia o Azure AD Connect Healthach) do listy dystrybucyjnej lub udostępnionej skrzynki pocztowej. Jeśli masz tylko jedno konto administratora globalnego z adresem e-mail, pamiętaj, aby skonfigurować co najmniej dwa konta z obsługą poczty e-mail.

Istnieją dwa adresy "od" używane przez usługę Azure AD: <o365mc@email2.microsoft.com> , które wysyła powiadomienia centrum komunikatów, i <azure-noreply@microsoft.com> , które wysyła powiadomienia dotyczące:

- [Przeglądy dostępu w usłudze Azure AD](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Usługa Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-email-notifications.md)
- [Powiadomienie o wygaśnięciu certyfikatu aplikacji dla przedsiębiorstw](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- Powiadomienia usługi aprowizacji aplikacji dla przedsiębiorstw

Zapoznaj się z poniższą tabelą, aby uzyskać informacje o typie wysyłanych powiadomień i o tym, gdzie je sprawdzić:

| Źródło powiadomienia | Co jest wysyłane | Gdzie można sprawdzić |
|:-|:-|:-|
| Kontakt techniczny | Błędy synchronizacji | Azure Portal — blok właściwości |
| Centrum wiadomości | Powiadomienia o zdarzeniach i obniżeniu wydajności usług Identity Services oraz Microsoft 365 usług zaplecza | Portal Office |
| Cotygodniowe podsumowanie ochrony tożsamości | Podsumowanie programu Identity Protection | Azure AD Identity Protection blok |
| Azure AD Connect Health | Powiadomienia o alertach | Blok Azure Portal — Azure AD Connect Health |
| Powiadomienia o aplikacjach dla przedsiębiorstw | Powiadomienia, gdy certyfikaty wkrótce wygasną i błędy aprowizacji | Azure Portal — blok aplikacji dla przedsiębiorstw (każda aplikacja ma własne ustawienie adresu e-mail) |

#### <a name="notifications-recommended-reading"></a>Zalecane odczyty

- [Zmień adres organizacji, kontakt techniczny i inne](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Obszar operacyjnej powierzchni

### <a name="ad-fs-lockdown"></a>Blokada AD FS

Organizacje, które konfigurują aplikacje do uwierzytelniania bezpośrednio w usłudze Azure AD z poziomu [inteligentnego blokowania usługi Azure AD](../authentication/concept-sspr-howitworks.md). Jeśli używasz AD FS w systemie Windows Server 2012 R2, zaimplementuj AD FS [Ochrona blokowania ekstranetu](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Jeśli używasz AD FS w systemie Windows Server 2016 lub nowszym, zaimplementuj [inteligentne blokowanie ekstranetu](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Zaleca się włączenie blokowania ekstranetu w taki sposób, aby zawierało ryzyko wymuszania ataków na Active Directory lokalnych. Jeśli jednak masz AD FS w systemie Windows 2016 lub nowszym, należy również włączyć blokadę blokowania ekstranetu, która pomoże ograniczyć ataki z wykorzystaniem [hasła](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) .

Jeśli AD FS jest używany tylko w przypadku federacji usługi Azure AD, niektóre punkty końcowe można wyłączyć, aby zminimalizować obszar narażony na ataki. Na przykład jeśli AD FS jest używany tylko w usłudze Azure AD, należy wyłączyć punkty końcowe WS-Trust inne niż punkty końcowe włączone dla **usernamemixed** i **windowstransport**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Dostęp do maszyn z lokalnymi składnikami tożsamości

Organizacje powinny blokować dostęp do maszyn z lokalnymi składnikami hybrydowymi w taki sam sposób, jak w przypadku domeny lokalnej. Na przykład operator kopii zapasowej lub administrator funkcji Hyper-V nie powinien być w stanie zalogować się do serwera Azure AD Connect w celu zmiany reguł.

Active Directory model warstwy administracyjnej został zaprojektowany tak, aby chronić systemy tożsamości przy użyciu zestawu stref buforowych między pełną kontrolą środowiska (warstwa 0) i zasobów stacji roboczej wysokiego ryzyka, które atakują często. ![Diagram przedstawiający trzy warstwy modelu warstwy](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[Model warstwowy](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) składa się z trzech poziomów i zawiera tylko konta administracyjne, a nie konta użytkowników standardowych.

- **Warstwa 0** — bezpośrednia kontrola nad tożsamościami używanymi w środowisku przedsiębiorstwa. Warstwa 0 obejmuje konta, grupy i inne zasoby, które mają bezpośrednią lub pośrednią kontrolę administracyjną nad lasem usługi Active Directory, domenami, kontrolerami domeny i wszystkimi zasobami znajdującymi się w tym lesie. Czułość zabezpieczeń wszystkich zasobów warstwy 0 jest równoważna ich wzajemnej, efektywnej kontroli.
- **Warstwa 1** — kontrola nad korporacyjnymi serwerami i aplikacjami. Zasoby warstwy 1 obejmują systemy operacyjne serwerów, usługi w chmurze i aplikacje używane w przedsiębiorstwie. Konta administratorów w warstwie 1 umożliwiają prowadzenie nadzoru nad znaczną częścią elementów generujących wartość biznesową, które są hostowane w tych zasobach. Typowym przykładem takiej roli są administratorzy serwerów, którzy obsługują te systemy operacyjne i mogą zmieniać działanie wszystkich usług w przedsiębiorstwie.
- **Warstwa 2** — kontrola nad urządzeniami i stacjami roboczymi użytkowników. Konta administratorów w warstwie 2 umożliwiają prowadzenie nadzoru nad znaczną częścią elementów generujących wartość biznesową, które są hostowane na urządzeniach i stacjach roboczych użytkowników. Przykłady obejmują administratorów pomocy technicznej, którzy mają wpływ na spójność prawie wszystkich danych użytkowników.

Zablokuj dostęp do lokalnych składników tożsamości, takich jak Azure AD Connect, AD FS i usługi SQL w taki sam sposób, jak w przypadku kontrolerów domeny.

## <a name="summary"></a>Podsumowanie

Istnieje siedem aspektów bezpiecznej infrastruktury tożsamości. Ta lista ułatwi znalezienie czynności, które należy wykonać w celu zoptymalizowania operacji dla Azure Active Directory (Azure AD).

- Przypisywanie właścicieli do kluczowych zadań.
- Automatyzuj proces uaktualniania lokalnych składników hybrydowych.
- Wdróż Azure AD Connect Health monitorowania i raportowania Azure AD Connect i AD FS.
- Monitorowanie kondycji lokalnych składników hybrydowych przez archiwizowanie i analizowanie dzienników agentów składników przy użyciu System Center Operations Manager lub rozwiązania SIEM.
- Wdrażaj ulepszenia zabezpieczeń, mierząc stan zabezpieczeń dzięki zabezpieczeniom tożsamości.
- Zablokuj AD FS.
- Zablokuj dostęp do maszyn z lokalnymi składnikami tożsamości.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [planami wdrażania usługi Azure AD](active-directory-deployment-plans.md) , aby uzyskać szczegółowe informacje na temat wszystkich możliwości, które nie zostały wdrożone.