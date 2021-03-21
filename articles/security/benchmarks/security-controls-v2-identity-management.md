---
title: Azure Security test — wersja 2 — Zarządzanie tożsamościami
description: Azure Security test — Zarządzanie tożsamościami w wersji 2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4a36bd69ff5ddbc79e358d6f8a2c5b4d640c6d5c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051450"
---
# <a name="security-control-v2-identity-management"></a>Kontrola zabezpieczeń v2: Zarządzanie tożsamościami

Zarządzanie tożsamościami obejmuje kontrolki służące do nawiązywania bezpiecznej kontroli tożsamości i dostępu przy użyciu Azure Active Directory. Obejmuje to korzystanie z logowania jednokrotnego, silnych uwierzytelnień, tożsamości zarządzanych (i zasad usługi) dla aplikacji, dostępu warunkowego i monitorowania anomalii kont.

Aby wyświetlić odpowiednie wbudowane Azure Policy, zobacz [szczegóły wbudowanej inicjatywy zgodność z przepisami usługi Azure Security Tests: Zarządzanie tożsamościami](../../governance/policy/samples/azure-security-benchmark.md#identity-management)

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standaryzacja usługi Azure Active Directory jako centralnego systemu tożsamości i uwierzytelniania

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| WIADOMOŚĆ BŁYSKAWICZNA — 1 | 16,1, 16,2, 16,4, 16,5 | IA-2, IA-8, AC-2, AC-3 |

Azure Active Directory (Azure AD) to domyślna usługa zarządzania tożsamościami i dostępem platformy Azure. Należy przeprowadzić standaryzację w usłudze Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji w programie:
- Zasoby w chmurze firmy Microsoft, takie jak Azure Portal, Azure Storage, Azure Virtual Machines (Linux i Windows), Azure Key Vault, PaaS i SaaS.

- Zasoby organizacji, takie jak aplikacje na platformie Azure lub formowe zasoby sieciowe.

Zabezpieczanie usługi Azure AD powinno mieć wysoki priorytet w zakresie zabezpieczeń w chmurze w organizacji. Usługa Azure AD zapewnia bezpieczną ocenę tożsamości, która pomaga ocenić stan zabezpieczeń tożsamości w porównaniu z zaleceniami dotyczącymi najlepszych rozwiązań firmy Microsoft. Użyj tego wskaźnika, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań oraz poprawić stan zabezpieczeń.

Uwaga: usługa Azure AD obsługuje zewnętrznych dostawców tożsamości, którzy umożliwiają użytkownikom nieposiadającym konta Microsoft logowanie się do aplikacji i zasobów za pomocą tożsamości zewnętrznej.

- [Dzierżawa w usłudze Azure AD](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Definiowanie dzierżaw usługi Azure AD](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)

- [Korzystanie z zewnętrznych dostawców tożsamości dla aplikacji](../../active-directory/external-identities/identity-providers.md)

- [Co to jest wskaźnik bezpieczeństwa tożsamości w usłudze Azure AD](../../active-directory/fundamentals/identity-secure-score.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Bezpieczne i automatyczne zarządzanie tożsamościami aplikacji

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| WIADOMOŚCI BŁYSKAWICZNE — 2 | Nie dotyczy | AC-2, AC-3, IA-2, IA-4, IA-9 |

W przypadku kont nieludzkich, takich jak usługi lub Automatyzacja, Użyj tożsamości zarządzanych przez platformę Azure, zamiast tworzyć bardziej zaawansowane konto w celu uzyskania dostępu do zasobów lub wykonywania kodu. Tożsamości zarządzane przez platformę Azure mogą uwierzytelniać usługi platformy Azure i zasoby, które obsługują uwierzytelnianie usługi Azure AD. Uwierzytelnianie jest włączane przy użyciu wstępnie zdefiniowanych reguł przyznawania dostępu, unikając zakodowanych poświadczeń w kodzie źródłowym lub plikach konfiguracyjnych. 

W przypadku usług, które nie obsługują tożsamości zarządzanych, Użyj usługi Azure AD, aby utworzyć jednostkę usługi z ograniczonymi uprawnieniami na poziomie zasobu. Zaleca się skonfigurowanie jednostek usługi z poświadczeniami certyfikatów i powrót do wpisów tajnych klienta. W obu przypadkach Azure Key Vault mogą być używane w połączeniu z tożsamościami zarządzanymi przez platformę Azure, dzięki czemu środowisko uruchomieniowe (takie jak funkcja platformy Azure) może pobrać poświadczenie z magazynu kluczy.

- [Tożsamości zarządzane przez platformę Azure](../../active-directory/managed-identities-azure-resources/overview.md)

- [Usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Jednostka usługi platformy Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Tworzenie jednostki usługi przy użyciu certyfikatów](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

Użyj Azure Key Vault do rejestracji podmiotu zabezpieczeń: Authentication # Autoryzuj-a-Security-Principal-to-Access-Key-magazyn

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Korzystanie z logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| WIADOMOŚCI BŁYSKAWICZNE — 3 | 4.4 | IA-2, IA-4 |

Usługa Azure AD zapewnia zarządzanie tożsamościami i dostępem do zasobów platformy Azure, aplikacji w chmurze i aplikacji lokalnych. Zarządzanie tożsamościami i dostępem dotyczy tożsamości przedsiębiorstwa, takich jak pracownicy, a także tożsamości zewnętrznych, takich jak partnerzy, dostawcy i dostawcy.

Funkcja logowania jednokrotnego w usłudze Azure AD umożliwia zarządzanie dostępem do danych i zasobów organizacji oraz ich lokalne i w chmurze. Połącz wszystkich użytkowników, aplikacje i urządzenia z usługą Azure AD w celu zapewnienia bezproblemowego, bezpiecznego dostępu i lepszego wglądu i kontroli. 

- [Informacje na temat logowania jednokrotnego aplikacji za pomocą usługi Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Używanie kontrolek silnego uwierzytelniania dla całego dostępu opartego na usłudze Azure Active Directory

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| WIADOMOŚCI BŁYSKAWICZNE — 4 | 4,2, 4,4 4,5, 11,5, 12,11, 16,3 | AC-2, AC-3, IA-2, IA-4 |

Usługa Azure AD obsługuje mechanizmy kontroli silnego uwierzytelniania za pomocą uwierzytelniania wieloskładnikowego (MFA) i silnych metod bezhaseł.

- Uwierzytelnianie wieloskładnikowe: Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem dla konfiguracji usługi MFA. Usługę MFA można wymusić dla wszystkich użytkowników, wybrać użytkowników lub na poziomie poszczególnych użytkowników na podstawie warunków logowania i czynników ryzyka.

- Uwierzytelnianie bezhasła: dostępne są trzy opcje uwierzytelniania bezhasła: funkcja Windows Hello dla firm, aplikacja Microsoft Authenticator i lokalne metody uwierzytelniania, takie jak karty inteligentne.

W przypadku użytkowników z uprawnieniami administratora i uprzywilejowanego upewnij się, że jest używana najwyższa Metoda silnego uwierzytelniania, a następnie zastąp odpowiednie zasady silnego uwierzytelniania innym użytkownikom.

Jeśli uwierzytelnianie na podstawie hasła jest nadal używane na potrzeby uwierzytelniania w usłudze Azure AD, należy pamiętać, że konta tylko w chmurze (konta użytkowników utworzone bezpośrednio na platformie Azure) mają domyślne zasady haseł linii bazowej. I konta hybrydowe (konta użytkowników, które pochodzą z lokalnego Active Directory), są zgodne z lokalnymi zasadami haseł. W przypadku korzystania z uwierzytelniania opartego na hasłach usługa Azure AD zapewnia funkcję ochrony hasłem, która uniemożliwia użytkownikom ustawianie haseł, które są łatwe do odgadnięcia. Firma Microsoft oferuje globalną listę zakazanych haseł, które są aktualizowane na podstawie danych telemetrycznych, a klienci mogą rozszerzyć listę na podstawie ich potrzeb (takich jak znakowanie, odwołania kulturowe itp.). Ta ochrona hasłem może być używana w przypadku kont tylko w chmurze i hybrydowych.

Uwaga: uwierzytelnianie oparte na poświadczeniach hasła jest podatne na popularne metody ataków. Aby zapewnić wyższy poziom zabezpieczeń, należy użyć silnego uwierzytelniania, takiego jak MFA i zasad silnych haseł. W przypadku aplikacji innych firm i usług Marketplace, które mogą mieć domyślne hasła, należy je zmienić podczas początkowej konfiguracji usługi.

- [Jak włączyć usługę MFA na platformie Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Wprowadzenie do opcji uwierzytelniania bez hasła w przypadku usługi Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Domyślne zasady haseł usługi Azure AD](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminowanie nieprawidłowych haseł przy użyciu ochrony haseł w usłudze Azure AD](../../active-directory/authentication/concept-password-ban-bad.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorowanie anomalii kont i wyświetlanie alertów

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| WIADOMOŚCI BŁYSKAWICZNE — 5 | 4,8, 4,9, 16,12, 16,13 | AC-2, AC-3, AC-7, AU-6 |

Usługa Azure AD udostępnia następujące źródła danych: 
-   Logowania — raporty aktywności logowania zawierają informacje na temat użycia zarządzanych aplikacji i działań użytkownika związane z logowaniem.

-   Dzienniki inspekcji — umożliwia śledzenie przy użyciu dzienników dla wszystkich zmian wprowadzonych za pomocą różnych funkcji w usłudze Azure AD. Przykłady rejestrowanych zmian dzienniki inspekcji obejmują dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

-   Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

-   Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Te źródła danych można zintegrować z Azure Monitor, wskaźnikiem produktów platformy Azure lub systemami SIEM innych firm.

Azure Security Center może również otrzymywać alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia i przestarzałe konta w subskrypcji. 

Azure Advanced Threat Protection (ATP) to rozwiązanie zabezpieczeń, które umożliwia używanie lokalnych sygnałów Active Directory do identyfikowania, wykrywania i badania zaawansowanych zagrożeń, złamanych tożsamości i złośliwych działań niejawnych.

- [Raporty dotyczące inspekcji w usłudze Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../../security-center/security-center-identity-access.md)

- [Alerty w module analizy zagrożeń usługi Azure Security Center](../../security-center/alerts-reference.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Łączenie danych z Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md)

- [Microsoft Defender for Identity](/azure-advanced-threat-protection/what-is-atp)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Ograniczanie dostępu do zasobów platformy Azure na podstawie warunków

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| WIADOMOŚCI BŁYSKAWICZNE — 6 | Nie dotyczy | AC-2, AC-3 |

Korzystanie z dostępu warunkowego usługi Azure AD w celu uzyskania bardziej szczegółowej kontroli dostępu na podstawie warunków zdefiniowanych przez użytkownika, takich jak wymaganie logowania użytkowników z określonych zakresów adresów IP do korzystania z usługi MFA. Szczegółowe zarządzanie sesjami uwierzytelniania może być również używane za pomocą zasad dostępu warunkowego usługi Azure AD dla różnych przypadków użycia. 

- [Omówienie dostępu warunkowego platformy Azure](../../active-directory/conditional-access/overview.md)

- [Typowe zasady dostępu warunkowego](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Skonfiguruj zarządzanie sesją uwierzytelniania z użyciem dostępu warunkowego](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminowanie niezamierzonego ujawnienia poświadczeń

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| WIADOMOŚCI BŁYSKAWICZNE — 7 | 18,1, 18,7 | IA-5 |

Zaimplementuj skaner poświadczeń usługi Azure DevOps, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przechodzenia odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

W usłudze GitHub można używać funkcji skanowania natywnego klucza tajnego do identyfikowania poświadczeń lub innej formy wpisów tajnych w kodzie.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Skanowanie wpisów tajnych usługi GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>KOMUNIKATOR-8: bezpieczny dostęp użytkowników do starszych aplikacji

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| WIADOMOŚĆ BŁYSKAWICZNA — 8 | 14,6 | AC-2, AC-3, SC-11 |

Upewnij się, że dysponujesz nowoczesnymi kontrolami dostępu i monitorowaniem sesji dla starszych aplikacji oraz przechowywanych i przetwarzanych danych. Gdy sieci VPN są często używane do uzyskiwania dostępu do starszych aplikacji, często mają tylko podstawową kontrolę dostępu i ograniczone monitorowanie sesji.

Usługa Azure serwer proxy aplikacji usługi Azure AD umożliwia publikowanie starszych aplikacji lokalnych dla użytkowników zdalnych z logowaniem jednokrotnym (SSO), a jednocześnie jawnie sprawdza wiarygodność zarówno użytkowników zdalnych, jak i urządzeń przy użyciu dostępu warunkowego usługi Azure AD.

Alternatywnie Microsoft Cloud App Security to usługa brokera zabezpieczeń dostępu w chmurze (CASB), która może zapewnić kontrolę monitorowania sesji użytkownika i akcji blokowania (dla starszych aplikacji lokalnych i aplikacji w chmurze jako usługi (SaaS)).

- [serwer proxy aplikacji usługi Azure AD platformy Azure](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Microsoft Cloud App Security najlepszych praktyk](/cloud-app-security/best-practices)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)