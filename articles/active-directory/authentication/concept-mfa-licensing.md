---
title: Wersje Multi-Factor Authentication i plany użycia platformy Azure
description: Dowiedz się więcej na temat klienta usługi Azure MFA i różnych dostępnych metod i wersji.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77648006"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Funkcje i licencje dla usługi Azure Multi-Factor Authentication

Aby chronić konta użytkowników w organizacji, należy użyć uwierzytelniania wieloskładnikowego. Ta funkcja jest szczególnie ważna w przypadku kont, które mają uprzywilejowany dostęp do zasobów. Podstawowe funkcje usługi uwierzytelniania wieloskładnikowego są dostępne dla administratorów pakietu Office 365 i usługi Azure Active Directory (Azure AD) bez dodatkowych kosztów. Jeśli chcesz uaktualnić funkcje dla administratorów lub rozszerzyć uwierzytelnianie wieloskładnikowe na pozostałych użytkowników, możesz kupić Multi-Factor Authentication platformy Azure na kilka sposobów.

> [!IMPORTANT]
> W tym artykule szczegółowo opisano różne sposoby licencjonowania i używania usługi Azure Multi-Factor Authentication. Aby uzyskać szczegółowe informacje na temat cen i rozliczeń, zobacz [stronę z cennikiem usługi Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Dostępne wersje usługi Azure Multi-Factor Authentication

Usługi Azure Multi-Factor Authentication mogą być używane i licencjonowane na kilka różnych sposobów, w zależności od potrzeb organizacji. Licencjobiorca może już być uprawniony do korzystania z usługi Azure Multi-Factor Authentication w zależności od obecnie posiadanej licencji usługi Azure AD, Office 365, EMS lub Microsoft 365. W poniższej tabeli przedstawiono różne sposoby uzyskiwania informacji o platformie Azure Multi-Factor Authentication i niektóre funkcje i przypadki użycia dla każdej z nich.

| Jeśli jesteś użytkownikiem | Możliwości i przypadki użycia |
| --- | --- |
| Pakiet EMS lub Microsoft 365 E3 i E5 | Pakiet EMS E3 lub Microsoft 365 E3 (obejmujący pakiety EMS i Office 365) zawiera Azure AD — wersja Premium P1. Pakiet EMS E5 lub Microsoft 365 E5 zawiera Azure AD — wersja Premium P2. Możesz użyć tych samych funkcji dostępu warunkowego zanotowanych w poniższych sekcjach, aby zapewnić użytkownikom uwierzytelnianie wieloskładnikowe. |
| Usługa Azure AD — warstwa Premium P1 | Za pomocą [dostępu warunkowego usługi Azure AD](../conditional-access/overview.md) możesz monitować użytkowników o uwierzytelnianie wieloskładnikowe w określonych scenariuszach lub zdarzeniach zgodnie z wymaganiami biznesowymi. |
| Usługa Azure AD — warstwa Premium P2 | Zapewnia najmocniejszą pozycję zabezpieczeń i ulepszone środowisko użytkownika. Dodaje [dostęp warunkowy oparty na ryzyku](../conditional-access/howto-conditional-access-policy-risk.md) do funkcji Azure AD — wersja Premium P1, które dostosowują się do wzorców użytkownika i minimalizują komunikaty uwierzytelniania wieloskładnikowego. |
| Pakiet Office 365 Business Premium, E3 lub E5 | Usługa Azure Multi-Factor Authentication jest włączona lub wyłączona dla wszystkich użytkowników w przypadku wszystkich zdarzeń związanych z logowaniem. Nie ma możliwości włączania uwierzytelniania wieloskładnikowego tylko dla podzbioru użytkowników lub tylko w określonych scenariuszach. Zarządzanie odbywa się za pomocą portalu pakietu Office 365. Aby ulepszyć środowisko użytkownika, należy przeprowadzić uaktualnienie do Azure AD — wersja Premium P1 lub P2 i korzystać z dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie zasobów pakietu Office 365 przy użyciu usługi uwierzytelniania wieloskładnikowego](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). |
| Usługa Azure AD — warstwa Bezpłatna | Możesz użyć [domyślnych ustawień zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md) , aby włączyć uwierzytelnianie wieloskładnikowe dla wszystkich użytkowników, za każdym razem, gdy jest wykonywane żądanie uwierzytelniania. Nie masz szczegółowej kontroli nad włączonymi użytkownikami lub scenariuszami, ale udostępniamy ten dodatkowy krok zabezpieczeń.<br /> Nawet jeśli wartości domyślne zabezpieczeń nie są używane do włączania uwierzytelniania wieloskładnikowego dla wszystkich użytkowników, można skonfigurować rolę *administratora globalnego usługi Azure AD* do korzystania z uwierzytelniania wieloskładnikowego. Ta funkcja warstwy Bezpłatna gwarantuje, że konta administratorów krytyczne są chronione za pomocą usługi uwierzytelniania wieloskładnikowego. |

## <a name="feature-comparison-of-versions"></a>Porównanie funkcji w wersjach

Poniższa tabela zawiera listę funkcji, które są dostępne w różnych wersjach systemu Azure Multi-Factor Authentication. Zaplanuj potrzeby zabezpieczania uwierzytelniania użytkowników, a następnie ustal, które podejście spełnia te wymagania. Na przykład, chociaż Azure AD — wersja Bezpłatna zapewnia domyślne ustawienia zabezpieczeń, które udostępniają usługę Azure Multi-Factor Authentication, do monitu uwierzytelniania można używać tylko aplikacji do zarządzania urządzeniami przenośnymi, a nie połączeń telefonicznych ani wiadomości SMS. Takie podejście może być ograniczeniem, jeśli nie można zagwarantować, że aplikacja do uwierzytelniania mobilnego jest zainstalowana na urządzeniu osobistym użytkownika.

| Funkcja | Azure AD — wersja Bezpłatna — domyślne ustawienia zabezpieczeń | Azure AD — wersja Bezpłatna — Administratorzy globalni usługi Azure AD | Pakiet Office 365 Business Premium, E3 lub E5 | Azure AD — wersja Premium P1 lub P2 |
| --- |:---:|:---:|:---:|:---:|
| Ochrona kont administratorów dzierżawy usługi Azure AD za pomocą uwierzytelniania wieloskładnikowego | ● | ● (Tylko konta*administratora globalnego usługi Azure AD* ) | ● | ● |
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

> [!IMPORTANT]
> Od marca 2019 opcje połączeń telefonicznych nie są już dostępne dla użytkowników usługi Azure Multi-Factor Authentication i samoobsługowego resetowania hasła na platformie Azure w dzierżawach Azure AD — wersja Bezpłatna/w wersji próbnej. Ta zmiana nie ma wpływu na wiadomości SMS. Połączenia telefoniczne są nadal dostępne dla użytkowników w Azure AD — wersja Premiumych dzierżawcach P1 lub P2 lub korzystających z pakietu Office 365 Business Premium, E3 lub E5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Kupowanie i Włączanie usługi Azure Multi-Factor Authentication

Aby korzystać z usługi Azure Multi-Factor Authentication, zarejestruj się lub Kup kwalifikującą się warstwę usługi Azure AD. Usługa Azure AD jest dostępna w czterech wersjach: bezpłatna, Office 365 Apps Edition (dla pakietu Office 365 Business Premium E3 lub E5 Customers), Premium P1 i Premium P2.

Wersja bezpłatna jest oferowana w ramach subskrypcji platformy Azure. Zapoznaj się z [sekcją poniżej](#azure-ad-free-tier) , aby uzyskać informacje na temat korzystania z domyślnych ustawień zabezpieczeń lub ochrony kont przy użyciu roli *administratora globalnego usługi Azure AD* .

Wersje Azure AD — wersja Premium są dostępne za pomocą przedstawiciela firmy Microsoft, [programu licencjonowania zbiorowego Open](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)i [programu dostawców rozwiązań w chmurze](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Subskrybenci platformy Azure i pakietu Office 365 mogą również kupować Azure Active Directory — wersja Premium P1 i P2 w trybie online. [Zaloguj](https://portal.office.com/Commerce/Catalog.aspx) się, aby dokonać zakupu.

> [!IMPORTANT]
> Licencjonowanie oparte na zużyciu nie jest już dostępne dla nowych klientów obowiązujące od 1 września 2018. Istniejący klienci korzystający z modelu opartego na zużyciu mogą nadal korzystać z każdego włączonego użytkownika lub za uwierzytelnienie.

Po zakupie wymaganej warstwy usługi Azure AD [Zaplanuj i wdróż Multi-Factor Authentication platformy Azure](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Warstwa Azure AD — wersja Bezpłatna

Wszyscy użytkownicy w dzierżawie Azure AD — wersja Bezpłatna mogą korzystać z uwierzytelniania wieloskładnikowego systemu Azure przy użyciu domyślnych ustawień zabezpieczeń. Te domyślne ustawienia zabezpieczeń umożliwiają uwierzytelnianie wieloskładnikowe systemu Azure dla wszystkich użytkowników przy każdym logowaniu. Aplikacja do uwierzytelniania mobilnego jest jedyną metodą, która może być używana w przypadku usługi Azure Multi-Factor Authentication w przypadku używania Azure AD — wersja Bezpłatna domyślnych ustawień zabezpieczeń.

* [Dowiedz się więcej o domyślnych ustawieniach zabezpieczeń usługi Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Włącz domyślne ustawienia zabezpieczeń dla użytkowników w Azure AD — wersja Bezpłatna](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Jeśli nie chcesz włączać usługi Azure Multi-Factor Authentication dla wszystkich użytkowników i każdego zdarzenia logowania, możesz zamiast tego zdecydować się na ochronę kont użytkowników przy użyciu roli *administratora globalnego usługi Azure AD* . Takie podejście zapewnia dodatkowe instrukcje uwierzytelniania dla najważniejszych kont administratorów. Multi-Factor Authentication platformy Azure można włączyć w jeden z następujących sposobów, w zależności od używanego typu konta:

* Jeśli używasz konta Microsoft, zarejestruj się [w usłudze uwierzytelniania wieloskładnikowego](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Jeśli nie korzystasz z konta Microsoft, [Włącz uwierzytelnianie wieloskładnikowe dla użytkownika lub grupy w usłudze Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o kosztach, zobacz [Cennik usługi Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
