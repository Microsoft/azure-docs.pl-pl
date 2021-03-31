---
title: Wersje Multi-Factor Authentication i plany użycia usługi Azure AD
description: Dowiedz się więcej na temat klienta usługi Azure AD Multi-Factor Authentication i dostępnych różnych metod i wersji.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d880146a0b068a5d097c452c14b28db4907098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743959"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Funkcje i licencje dla usługi Azure AD Multi-Factor Authentication

Aby chronić konta użytkowników w organizacji, należy użyć uwierzytelniania wieloskładnikowego. Ta funkcja jest szczególnie ważna w przypadku kont, które mają uprzywilejowany dostęp do zasobów. Podstawowe funkcje usługi uwierzytelniania wieloskładnikowego są dostępne dla administratorów Microsoft 365 i Azure Active Directory (Azure AD) bez dodatkowych kosztów. Jeśli chcesz uaktualnić funkcje dla administratorów lub rozszerzyć uwierzytelnianie wieloskładnikowe na pozostałych użytkowników, możesz kupić Multi-Factor Authentication usługi Azure AD na kilka sposobów.

> [!IMPORTANT]
> W tym artykule szczegółowo opisano różne sposoby licencjonowania i używania Multi-Factor Authentication usługi Azure AD. Aby uzyskać szczegółowe informacje na temat cen i rozliczeń, zobacz [stronę z cennikiem usługi Azure AD Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>Dostępne wersje usługi Azure AD Multi-Factor Authentication

Usługi Azure AD Multi-Factor Authentication mogą być używane i licencjonowane na kilka różnych sposobów, w zależności od potrzeb organizacji. Licencjobiorca może już być uprawniony do korzystania z usługi Azure AD Multi-Factor Authentication w zależności od obecnie posiadanej licencji usługi Azure AD, pakietu EMS lub Microsoft 365. W poniższej tabeli przedstawiono różne sposoby uzyskiwania Multi-Factor Authentication usługi Azure AD, niektóre funkcje i przypadki użycia dla każdej z nich.

| Jeśli jesteś użytkownikiem | Możliwości i przypadki użycia |
| --- | --- |
| Microsoft 365 Business Premium, EMS lub Microsoft 365 E3 i E5 | Pakiet EMS E3, Microsoft 365 E3 i Microsoft 365 Business Premium obejmują Azure AD — wersja Premium P1. Pakiet EMS E5 lub Microsoft 365 E5 zawiera Azure AD — wersja Premium P2. Możesz użyć tych samych funkcji dostępu warunkowego zanotowanych w poniższych sekcjach, aby zapewnić użytkownikom uwierzytelnianie wieloskładnikowe. |
| Usługa Azure AD — wersja Premium P1 | Za pomocą [dostępu warunkowego usługi Azure AD](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) możesz monitować użytkowników o uwierzytelnianie wieloskładnikowe w określonych scenariuszach lub zdarzeniach zgodnie z wymaganiami biznesowymi. |
| Usługa Azure AD — wersja Premium P2 | Zapewnia najmocniejszą pozycję zabezpieczeń i ulepszone środowisko użytkownika. Dodaje [dostęp warunkowy oparty na ryzyku](../conditional-access/howto-conditional-access-policy-risk.md) do funkcji Azure AD — wersja Premium P1, które dostosowują się do wzorców użytkownika i minimalizują komunikaty uwierzytelniania wieloskładnikowego. |
| Wszystkie plany Microsoft 365 | Usługę Azure AD Multi-Factor Authentication można [włączyć dla poszczególnych użytkowników](howto-mfa-userstates.md)lub włączyć lub wyłączyć dla wszystkich użytkowników korzystających z [domyślnych ustawień zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md). Zarządzaniem Multi-Factor Authentication usługi Azure AD odbywa się za pomocą portalu Microsoft 365. Aby ulepszyć środowisko użytkownika, należy przeprowadzić uaktualnienie do Azure AD — wersja Premium P1 lub P2 i korzystać z dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie zasobów Microsoft 365 przy użyciu usługi uwierzytelniania wieloskładnikowego](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication). |
| Usługa Azure AD — warstwa Bezpłatna | Możesz użyć [domyślnych ustawień zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md) , aby włączyć uwierzytelnianie wieloskładnikowe dla wszystkich użytkowników. Nie masz szczegółowej kontroli nad włączonymi użytkownikami lub scenariuszami, ale udostępniamy ten dodatkowy krok zabezpieczeń.<br /> Nawet jeśli wartości domyślne zabezpieczeń nie są używane do włączania uwierzytelniania wieloskładnikowego dla wszystkich użytkowników, można skonfigurować rolę *administratora globalnego usługi Azure AD* do korzystania z uwierzytelniania wieloskładnikowego. Ta funkcja warstwy Bezpłatna gwarantuje, że konta administratorów krytyczne są chronione za pomocą usługi uwierzytelniania wieloskładnikowego. |

## <a name="feature-comparison-of-versions"></a>Porównanie funkcji w wersjach

Poniższa tabela zawiera listę funkcji, które są dostępne w różnych wersjach usługi Azure AD Multi-Factor Authentication. Zaplanuj potrzeby zabezpieczania uwierzytelniania użytkowników, a następnie ustal, które podejście spełnia te wymagania. Na przykład, chociaż Azure AD — wersja Bezpłatna zapewnia domyślne ustawienia zabezpieczeń, które udostępniają usługę Azure AD Multi-Factor Authentication, do monitu uwierzytelniania można używać tylko aplikacji do zarządzania urządzeniami przenośnymi, a nie połączeń telefonicznych ani wiadomości SMS. Takie podejście może być ograniczeniem, jeśli nie można zagwarantować, że aplikacja do uwierzytelniania mobilnego jest zainstalowana na urządzeniu osobistym użytkownika.

| Cecha | Azure AD — wersja Bezpłatna — domyślne ustawienia zabezpieczeń | Azure AD — wersja Bezpłatna — Administratorzy globalni usługi Azure AD | Aplikacje Microsoft 365 | Usługa Azure AD — wersja Premium P1 lub P2 |
| --- |:---:|:---:|:---:|:---:|
| Ochrona kont administratorów dzierżawy usługi Azure AD za pomocą uwierzytelniania wieloskładnikowego | ● | ● (Tylko konta *administratora globalnego usługi Azure AD* ) | ● | ● |
| Aplikacja mobilna jako drugi składnik | ● | ● | ● | ● |
| Połączenie telefoniczne jako drugi czynnik | | ● | ● | ● |
| Wiadomość SMS jako drugi składnik | | ● | ● | ● |
| Kontrola administracyjna nad metodami weryfikacji | | ● | ● | ● |
| Alert dotyczący wykrycia oszustwa | | | | ● |
| Raporty usługi MFA | | | | ● |
| Niestandardowe powitania dla połączeń telefonicznych | | | | ● |
| Niestandardowy identyfikator obiektu wywołującego dla połączeń telefonicznych | | | | ● |
| Zaufane adresy IP | | | | ● |
| Pamiętanie uwierzytelniania MFA w przypadku zaufanych urządzeń | | ● | ● | ● |
| Uwierzytelnianie wieloskładnikowe dla aplikacji lokalnych | | | | ● |

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>Kupowanie i Włączanie usługi Azure AD Multi-Factor Authentication

Aby korzystać z usługi Azure AD Multi-Factor Authentication, zarejestruj się w usłudze Azure AD lub Kup ją jako kwalifikującą się. Usługa Azure AD jest dostępna w czterech wersjach — bezpłatnych, Microsoft 365 aplikacjach, Premium P1 i Premium P2.

Wersja bezpłatna jest oferowana w ramach subskrypcji platformy Azure. Zapoznaj się z [sekcją poniżej](#azure-ad-free-tier) , aby uzyskać informacje na temat korzystania z domyślnych ustawień zabezpieczeń lub ochrony kont przy użyciu roli *administratora globalnego usługi Azure AD* .

Wersje Azure AD — wersja Premium są dostępne za pomocą przedstawiciela firmy Microsoft, [programu licencjonowania zbiorowego Open](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)i [programu dostawców rozwiązań w chmurze](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Subskrybenci platformy Azure i Microsoft 365 mogą również kupować Azure Active Directory — wersja Premium P1 i P2 w trybie online. [Zaloguj](https://portal.office.com/Commerce/Catalog.aspx) się, aby dokonać zakupu.

Po zakupie wymaganej warstwy usługi Azure AD [Zaplanuj i wdróż Multi-Factor Authentication usługi Azure AD](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Warstwa Azure AD — wersja Bezpłatna

Wszyscy użytkownicy w dzierżawie Azure AD — wersja Bezpłatna mogą używać usługi Azure AD Multi-Factor Authentication przy użyciu domyślnych ustawień zabezpieczeń. Aplikacja do uwierzytelniania mobilnego jest jedyną metodą, która może być używana dla Multi-Factor Authentication usługi Azure AD w przypadku używania Azure AD — wersja Bezpłatna domyślnych ustawień zabezpieczeń.

* [Dowiedz się więcej o domyślnych ustawieniach zabezpieczeń usługi Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Włącz domyślne ustawienia zabezpieczeń dla użytkowników w Azure AD — wersja Bezpłatna](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Jeśli nie chcesz włączać usługi Azure AD Multi-Factor Authentication dla wszystkich użytkowników, możesz zamiast tego zdecydować się na ochronę kont użytkowników przy użyciu roli *administratora globalnego usługi Azure AD* . Takie podejście zapewnia dodatkowe instrukcje uwierzytelniania dla najważniejszych kont administratorów. Należy włączyć usługę Azure AD Multi-Factor Authentication w jeden z następujących sposobów, w zależności od używanego typu konta:

* Jeśli używasz konta Microsoft, zarejestruj się [w usłudze uwierzytelniania wieloskładnikowego](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Jeśli nie korzystasz z konta Microsoft, [Włącz uwierzytelnianie wieloskładnikowe dla użytkownika lub grupy w usłudze Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o kosztach, zobacz [Cennik usługi Azure AD Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
* [Co to jest dostęp warunkowy?](../conditional-access/overview.md)

