---
title: Kontrola zabezpieczeń platformy Azure — tożsamość i Access Control
description: Tożsamość i Access Control kontroli zabezpieczeń platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8d2901e71a3c638e25899803ff9b24e20fdf1969
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412667"
---
# <a name="security-control-identity-and-access-control"></a>Kontrola zabezpieczeń: tożsamość i Access Control

Zalecenia dotyczące zarządzania tożsamościami i dostępem koncentrują się na rozwiązywaniu problemów związanych z kontrolą dostępu opartą na tożsamościach, blokowaniu dostępu administracyjnego, alertach dotyczących zdarzeń związanych z tożsamościami, nieprawidłowym zachowaniu konta i kontroli dostępu opartej na rolach.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3,1 | 4.1 | Klient |

Usługa Azure AD ma wbudowane role, które muszą być jawnie przypisane i są queryable. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true)

## <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3.2 | 4.2 | Klient |

Usługa Azure AD nie ma koncepcji domyślnych haseł. Inne zasoby platformy Azure wymagające hasła wymuszają utworzenie hasła przy użyciu wymagań dotyczących złożoności i minimalnej długości hasła, które różnią się w zależności od usługi. Użytkownik jest odpowiedzialny za aplikacje innych firm i usługi Marketplace, które mogą korzystać z domyślnych haseł.

## <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3.3 | 4.3 | Klient |

Tworzenie standardowych procedur operacyjnych wokół korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Możesz również włączyć dostęp do usługi Microsoft just-in-Time/tylko do odczytu, korzystając z ról uprzywilejowanych Azure AD Privileged Identity Management dla usług firmowych i Azure Resource Manager. 

- [Dowiedz się więcej o Privileged Identity Management](../../active-directory/privileged-identity-management/index.yml)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3.4 | 4.4 | Klient |

Wszędzie tam, gdzie to możliwe, należy używać Azure Active Directory rejestracji jednokrotnej zamiast konfigurować indywidualne poświadczenia autonomiczne dla poszczególnych usług. Użyj Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem.

- [Opis logowania jednokrotnego w usłudze Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3,5 | 4,5, 11,5, 12,11, 16,3 | Klient |

Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem.

- [Jak włączyć usługę MFA na platformie Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../../security-center/security-center-identity-access.md)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3,6 | 4,6, 11,6, 12,12 | Klient |

Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) za pomocą usługi MFA skonfigurowanej do logowania się i konfigurowania zasobów platformy Azure.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Jak włączyć usługę MFA na platformie Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3.7 | 4,8, 4,9 | Klient |

Za pomocą raportów zabezpieczeń Azure Active Directory generowanie dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych w celu działania ryzykownego](../../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center](../../security-center/security-center-identity-access.md)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3,8 | 11,7 | Klient |

Użyj dostępu warunkowego o nazwie lokalizacje, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

## <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3.9 | 16,1, 16,2, 16,4, 16,5, 16,6 | Klient |

Użyj Azure Active Directory jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3,10 | 16,9, 16,10 | Klient |

Usługa Azure AD udostępnia dzienniki, które ułatwiają odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp. 

- [Informacje o raportowaniu usługi Azure AD](../../active-directory/reports-monitoring/index.yml)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../../active-directory/governance/access-reviews-overview.md)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3,11 | 16,12 | Klient |

Masz dostęp do źródeł działań związanych z logowaniem do usługi Azure AD, inspekcji i zagrożeń dzienników zdarzeń, które umożliwiają integrację z dowolnym narzędziem SIEM/monitorowania.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla Azure Active Directory kont użytkowników i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3,12 | 16,13 | Klient |

Użyj funkcji ryzyka i ochrony tożsamości usługi Azure AD, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

- [Jak wyświetlić ryzykowne logowania usługi Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../../sentinel/quickstart-onboard.md)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 3,13 | 16 | Klient |

W przypadku scenariuszy, w których firma Microsoft potrzebuje dostępu do danych klienta, Skrytka klienta udostępnia interfejs umożliwiający przeglądanie i zatwierdzanie lub odrzucanie żądań dostępu do danych klienta.

- [Opis Skrytka klienta](../fundamentals/customer-lockbox-overview.md)


## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń: [Ochrona danych](security-control-data-protection.md)