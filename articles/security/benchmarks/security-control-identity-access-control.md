---
title: Kontrola zabezpieczeń platformy Azure — kontrola tożsamości i dostępu
description: Kontrola tożsamości i dostępu do kontroli zabezpieczeń platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 473fe838f53df0a874a2c793792533e01b7c069a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408530"
---
# <a name="security-control-identity-and-access-control"></a>Kontrola zabezpieczeń: kontrola tożsamości i dostępu

Zalecenia dotyczące zarządzania tożsamościami i dostępem koncentrują się na rozwiązywaniu problemów związanych z kontrolą dostępu opartą na tożsamości, blokowaniu dostępu administracyjnego, alertach o zdarzeniach związanych z tożsamością, nieprawidłowym zachowaniu konta i kontroli dostępu opartej na rolach.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Prowadzenie spisu rachunków administracyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3.1 | 4.1 | Klient |

Usługa Azure AD ma wbudowane role, które muszą być jawnie przypisane i są możliwe do queryable. Użyj modułu programu Azure AD PowerShell do wykonywania zapytań ad hoc w celu odnajdowania kont, które są członkami grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD za pomocą programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD za pomocą programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

## <a name="32-change-default-passwords-where-applicable"></a>3.2: W stosownych przypadkach zmieniaj domyślne hasła

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3,2 | 4.2 | Klient |

Usługa Azure AD nie ma pojęcia haseł domyślnych. Inne zasoby platformy Azure wymagające hasła wymusza hasło, które mają być tworzone z wymaganiami złożoności i minimalnej długości hasła, która różni się w zależności od usługi. Użytkownik jest odpowiedzialny za aplikacje innych firm i usługi marketplace, które mogą używać haseł domyślnych.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: Korzystanie z dedykowanych kont administracyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3.3 | 4.3 | Klient |

Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Usługa Azure Security Center Identity and Access Management służy do monitorowania liczby kont administracyjnych.

Można również włączyć just-in-time / Just-Enough-Access przy użyciu ról uprzywilejowanych zarządzania tożsamością usługi Azure AD dla usług firmy Microsoft i usługi Azure Resource Manager. 

- [Dowiedz się więcej o zarządzaniu tożsamościami uprzywilejowanymi](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Używanie logowania jednokrotnego (Logowanie jednokrotne) w usłudze Azure Active Directory

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3.4 | 4.4 | Klient |

Tam, gdzie to możliwe, użyj logowania sytego usługi Azure Active Directory zamiast konfigurowania poszczególnych poświadczeń autonomicznych na usługę. Użyj zaleceń dotyczących tożsamości i zarządzania dostępem w usłudze Azure Security Center.

- [Opis usługi SSO za pomocą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla całego dostępu opartego na usłudze Azure Active Directory

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3,5 | 4.5, 11.5, 12.11, 16.3 | Klient |

Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami dotyczącymi tożsamości i usług Azure Security Center dotyczącymi zarządzania tożsamościami i dostępem.

- [Jak włączyć usługę MFA na platformie Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Jak monitorować tożsamość i dostęp w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Używanie dedykowanych maszyn (stacje robocze z uprzywilejowanym dostępem) do wszystkich zadań administracyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Klient |

Użyj paws (uprzywilejowanych stacji roboczych dostępu) z usługi MFA skonfigurowane do logowania się i konfigurowania zasobów platformy Azure.

- [Dowiedz się więcej o stacjach roboczych dostępu uprzywilejowanego](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Jak włączyć usługę MFA na platformie Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i ostrzeganie o podejrzanych działaniach z kont administracyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3.7 | 4.8, 4.9 | Klient |

Raporty zabezpieczeń usługi Azure Active Directory są używane do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Usługa Azure Security Center służy do monitorowania tożsamości i dostępu do aktywności.

- [Jak zidentyfikować użytkowników usługi Azure AD oflagowanych pod kątem ryzykownych działań](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Jak monitorować tożsamość i dostęp użytkowników w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3.8 | 11.7 | Klient |

Użyj funkcji Nazwane lokalizacje dostępu warunkowego, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

## <a name="39-use-azure-active-directory"></a>3.9: Korzystanie z usługi Azure Active Directory

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Klient |

Użyj usługi Azure Active Directory (AAD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa AAD chroni dane przy użyciu silnego szyfrowania danych w spoczynku i podczas przesyłania. AAD również sole, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

- [Jak utworzyć i skonfigurować wystąpienie AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3.10 | 16.9, 16.10 | Klient |

Usługa Azure AD udostępnia dzienniki ułatwiające odnajdowanie starych kont. Ponadto użyj przeglądów dostępu do tożsamości platformy Azure, aby skutecznie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika można regularnie przeglądać, aby upewnić się, że tylko prawo Użytkownicy mają stały dostęp. 

- [Opis raportowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Jak korzystać z przeglądów dostępu tożsamości platformy Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3.11 | 16.12 | Klient |

Masz dostęp do działania logowania usługi Azure AD, inspekcji i źródeł dzienników zdarzeń ryzyka, które umożliwiają integrację z dowolnym narzędziem SIEM/Monitorowanie.

Możesz usprawnić ten proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure Active Directory i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Można skonfigurować żądane alerty w obszarze roboczym analizy dzienników.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert o odchyleniu zachowania logowania do konta

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3.12 | 16.13 | Klient |

Za pomocą funkcji Ochrony przed ryzykiem i tożsamościami usługi Azure AD można skonfigurować automatyczne odpowiedzi do wykrytych podejrzanych akcji związanych z tożsamościami użytkowników. Można również pozyskiwania danych do usługi Azure Sentinel do dalszego badania.

- [Jak wyświetlić ryzykowne logowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak skonfigurować i włączyć zasady ryzyka ochrony tożsamości](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Jak ować na pokładzie usługi Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 3.13 | 16 | Klient |

W scenariuszach pomocy technicznej, w których firma Microsoft musi uzyskać dostęp do danych klienta, skrytka klienta udostępnia interfejs do przeglądania i zatwierdzania lub odrzucania żądań dostępu do danych klienta.

- [Zrozumieć skrytę klienta](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)


## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń: [Ochrona danych](security-control-data-protection.md)