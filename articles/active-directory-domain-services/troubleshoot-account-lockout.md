---
title: Rozwiązywanie problemów z blokadą konta w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak rozwiązywać typowe problemy, które powodują, że konta użytkowników mają być blokowane w Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 3341f290a5a5bb169b6e70ea22459a2afafedbbc
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103198958"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Rozwiązywanie problemów z blokadą konta przy użyciu domeny zarządzanej Azure Active Directory Domain Services

Aby zapobiec ponownemu złośliwemu podejmowaniu prób logowania, konta usług Azure Active Directory Domain Services (Azure AD DS) są blokowane po upływie zdefiniowanej wartości progowej. Ta blokada konta może również być spowodowana awarią bez zdarzenia ataku logowania. Na przykład jeśli użytkownik wielokrotnie wprowadzi nieprawidłowe hasło lub usługa próbuje użyć starego hasła, konto zostanie zablokowane.

Ten artykuł rozwiązywania problemów zawiera informacje o tym, dlaczego blokady kont są wykonywane i jak można skonfigurować zachowanie oraz jak przeglądać inspekcje zabezpieczeń w celu rozwiązywania problemów z blokadami zdarzeń.

## <a name="what-is-an-account-lockout"></a>Co to jest blokada konta?

Konto użytkownika w domenie zarządzanej AD DS platformy Azure jest blokowane, gdy zostanie spełniony określony próg nieudanych prób logowania. To zachowanie blokady konta zostało zaprojektowane z myślą o ochronie przed powtarzanymi próbami logowania z wykorzystaniem pełnego wymuszania, które mogą wskazywać na zautomatyzowany atak cyfrowy.

**Domyślnie jeśli w ciągu 2 minut występuje 5 nieudanych prób wprowadzenia hasła, konto jest zablokowane przez 30 minut.**

Domyślne progi blokady konta są konfigurowane przy użyciu szczegółowych zasad haseł. Jeśli masz określony zestaw wymagań, możesz zastąpić te domyślne progi blokady konta. Nie zaleca się jednak zwiększania limitów progu w celu zmniejszenia liczby blokad konta. Najpierw Rozwiąż problem z zachowaniem blokady konta.

### <a name="fine-grained-password-policy"></a>Szczegółowe zasady haseł

Szczegółowe zasady haseł (FGPPs) pozwalają stosować określone ograniczenia dotyczące zasad blokowania haseł i kont dla różnych użytkowników w domenie. SZCZEGÓŁOWYCH zasad haseł ma wpływ tylko na użytkowników w domenie zarządzanej. Użytkownicy chmury i użytkownicy domeny synchronizowane w domenie zarządzanej z usługi Azure AD mają wpływ tylko na zasady haseł w domenie zarządzanej. Nie ma to wpływu na konta w usłudze Azure AD lub katalog lokalny.

Zasady są dystrybuowane za pomocą skojarzenia grupy w domenie zarządzanej, a wszelkie wprowadzone zmiany są stosowane podczas następnego logowania użytkownika. Zmiana zasad nie powoduje odblokowania konta użytkownika, które jest już zablokowane.

Aby uzyskać więcej informacji na temat szczegółowych zasad haseł i różnic między użytkownikami utworzonymi bezpośrednio na platformie Azure AD DS a z usługą Azure AD, zobacz [Konfigurowanie zasad blokowania haseł i kont][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Najczęstsze przyczyny blokady konta

Najczęstsze przyczyny zablokowania konta, bez żadnego złośliwego zamiaru lub czynników, obejmują następujące scenariusze:

* **Użytkownik zablokował się.**
    * Czy po zmianie hasła użytkownik będzie kontynuował korzystanie z poprzedniego hasła? Domyślne zasady blokady konta z pięciu nieudanych prób w ciągu 2 minut mogą być spowodowane przez użytkownika przypadkowo próbującego ponawiać stare hasło.
* **Istnieje aplikacja lub usługa, która ma stare hasło.**
    * Jeśli konto jest używane przez aplikacje lub usługi, te zasoby mogą wielokrotnie próbować zalogować się przy użyciu starego hasła. To zachowanie powoduje, że konto jest zablokowane.
    * Spróbuj zminimalizować użycie konta w wielu różnych aplikacjach lub usługach, a następnie zarejestruj, gdzie są używane poświadczenia. Jeśli hasło konta zostanie zmienione, należy odpowiednio zaktualizować powiązane aplikacje lub usługi.
* **Hasło zostało zmienione w innym środowisku, a nowe hasło nie zostało jeszcze zsynchronizowane.**
    * Jeśli hasło konta zostanie zmienione poza domeną zarządzaną, na przykład w środowisku Premium AD DS, może upłynąć kilka minut, zanim zmieni się hasło za pomocą usługi Azure AD i w domenie zarządzanej.
    * Użytkownik próbujący zalogować się do zasobu w domenie zarządzanej przed ukończeniem tego procesu synchronizacji haseł spowoduje zablokowanie konta.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Rozwiązywanie problemów z blokadami kont przy użyciu inspekcji zabezpieczeń

Aby rozwiązać problemy w przypadku wystąpienia zdarzeń blokady konta i lokalizacji, z których pochodzą, [Włącz inspekcje zabezpieczeń dla AD DS platformy Azure][security-audit-events]. Zdarzenia inspekcji są przechwytywane tylko po włączeniu tej funkcji. W idealnym przypadku należy włączyć inspekcje zabezpieczeń *przed* wystawieniem problemu z blokadą konta. Jeśli konto użytkownika wielokrotnie ma problemy z blokadą, można włączyć inspekcje zabezpieczeń gotowe do następnego momentu.

Po włączeniu inspekcji zabezpieczeń następujące przykładowe zapytania pokazują, jak przejrzeć *zdarzenia blokady konta*, kod *4740*.

Wyświetl wszystkie zdarzenia blokady konta w ciągu ostatnich siedmiu dni:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Wyświetl wszystkie zdarzenia blokady konta dla ostatnich siedmiu dni dla konta o nazwie *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Wyświetl wszystkie zdarzenia blokady konta w zakresie od 26 czerwca do 2020 o godzinie 9 i 1 lipca 2020 północy, posortowane rosnąco według daty i godziny:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-26 09:00) and TimeGenerated <= datetime(2020-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

**Uwaga**

Możesz znaleźć na 4776 i 4740 szczegóły zdarzenia "źródłowa stacja robocza:" puste. Wynika to z faktu, że nieprawidłowe hasło podczas logowania do sieci za pośrednictwem innych urządzeń.
Na przykład: Jeśli masz serwer RADIUS, który może przekazywać uwierzytelnianie do usługi AAD DS. Aby upewnić się, że włączono protokół RDP do zaplecza kontrolera domeny, skonfiguruj dzienniki usługi Netlogon.

03/04 19:07:29 [logowanie] [10752] Contoso: SamLogon: nadano przechodnie logowanie do sieci contoso\Nagappan.Veerappan z (za pośrednictwem LOB11-RADIUS) 

03/04 19:07:29 [logowanie] [10752] Contoso: SamLogon: przechodnie logowanie do sieci z contoso\Nagappan.Veerappan z (za pośrednictwem LOB11-RADIUS) zwraca 0xC000006A

03/04 19:07:35 [logowanie] [10753] Contoso: SamLogon: nadano przechodnie logowanie do sieci contoso\Nagappan.Veerappan z (za pośrednictwem LOB11-RADIUS) 

03/04 19:07:35 [logowanie] [10753] Contoso: SamLogon: przechodnie logowanie do sieci z contoso\Nagappan.Veerappan z (za pośrednictwem LOB11-RADIUS) zwraca 0xC000006A

Włącz protokół RDP dla kontrolerów domeny w sieciowej grupy zabezpieczeń do zaplecza, aby skonfigurować przechwytywanie diagnostyki (tj. Netlogon) https://docs.microsoft.com/azure/active-directory-domain-services/alert-nsg#inbound-security-rules Jeśli już zmodyfikowano sieciowej grupy zabezpieczeń domyślne, postępuj zgodnie z PSlet sposób, aby włączyć https://docs.microsoft.com/azure/active-directory-domain-services/network-considerations#port-3389---management-using-remote-desktop

Aby włączyć dziennik Netlogon na dowolnym serwerze https://docs.microsoft.com/troubleshoot/windows-client/windows-security/enable-debug-logging-netlogon-service

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat szczegółowych zasad haseł w celu dostosowania progów blokady konta, zobacz [Konfigurowanie zasad blokowania haseł i kont][configure-fgpp].

Jeśli nadal masz problemy z przyłączaniem maszyny wirtualnej do domeny zarządzanej, [Znajdź pomoc i Otwórz bilet pomocy technicznej dla Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
