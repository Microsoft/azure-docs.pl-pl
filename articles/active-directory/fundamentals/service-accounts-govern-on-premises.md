---
title: Zarządzanie kontami lokalnych usług | Azure Active Directory
description: Przewodnik tworzenia i uruchamiania procesu cyklu życia konta dla kont usług
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ad7cf7fe2ca1ddcb592e895014b1d956e55e1b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557373"
---
# <a name="governing-on-premises-service-accounts"></a>Zarządzanie kontami lokalnych usług

Istnieją cztery typy lokalnych kont usług w systemie Windows Active Directory:

* [Konta usług zarządzane przez grupę](service-accounts-group-managed.md) (kont gMSA)

* [Autonomiczne zarządzane konta usług](service-accounts-standalone-managed.md) (sMSAs)

* [Konta komputerów](service-accounts-computer.md)

* [Konta użytkowników działające jako konta usług](service-accounts-user-on-premises.md)


Bardzo ważne jest, aby zarządzać kontami usług w sposób ścisły: 

* Ochrona kont usług na podstawie ich wymagań dotyczących przypadków użycia i przeznaczenia.

* Zarządzaj cyklem życia kont usług i ich poświadczeń.

* Oceniaj konta usług na podstawie ryzyka, do którego zostaną ujawnione i jakie posiadane uprawnienia, 

* Upewnij się, że Active Directory i Azure Active Directory nie mają starych kont usług z potencjalnie idącymi uprawnieniami.

## <a name="principles-for-creating-a-new-service-account"></a>Zasady dotyczące tworzenia nowego konta usługi

Podczas tworzenia nowego konta usługi należy użyć następujących kryteriów.

| Zasady| Zagadnienia do rozważenia | 
| - |- | 
| Mapowanie konta usługi| Należy powiązać konto usługi z jedną usługą, aplikacją lub skryptem. |
| Własność| Upewnij się, że istnieje właściciel, który żąda i przyjmuje odpowiedzialność za konto. |
| Zakres| Zdefiniuj zakres jasno i przewidywany czas użytkowania dla konta usługi. |
| Przeznaczenie| Utwórz konta usług dla jednego określonego celu. |
| Privilege| Stosuj zasadę najniższych uprawnień, wykonując następujące działania: <br>Nigdy nie należy przypisywać ich do wbudowanych grup, takich jak Administratorzy.<br> Usuwanie uprawnień komputera lokalnego, jeśli jest to konieczne.<br>Dostosowanie dostępu i używanie delegowania Active Directory na potrzeby dostępu do katalogu.<br>Korzystanie z szczegółowych uprawnień dostępu.<br>Ustawianie wygaśnięć kont i ograniczeń opartych na lokalizacji na kontach usług opartych na użytkownikach |
| Monitorowanie i używanie inspekcji| Monitoruj dane logowania i upewnij się, że są one zgodne z zamierzonym użyciem. Ustawianie alertów dotyczących nietypowego użycia. |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>Wymuszaj najmniejsze uprawnienia dla kont użytkowników i Ogranicz nadmierne użycie konta

Użyj następujących ustawień z kontami użytkowników używanymi jako konta usług:

* [**Wygaśnięcie konta**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps): ustawienie konta usługi na automatycznie wygasa po upływie określonego czasu od momentu jego sprawdzenia, chyba że zostanie ustalone, że powinien on być kontynuowany

*  **LogonWorkstations**: Ogranicz uprawnienia do miejsca, w którym można się zalogować za pomocą konta usługi. Jeśli działa lokalnie na komputerze i uzyskuje dostęp tylko do zasobów na tym komputerze, ogranicz je do logowania w dowolnym miejscu.

* [**Nie można zmienić hasła**](/powershell/module/addsadministration/set-aduser): zapobiegaj zmianie własnego hasła przez konto usługi przez ustawienie parametru na false.

 
## <a name="build-a-lifecycle-management-process"></a>Kompiluj proces zarządzania cyklem życia

Aby zachować bezpieczeństwo kont usług, należy nimi zarządzać od momentu, w którym należy je określić, dopóki nie zostaną zlikwidowane. 

Aby zarządzać cyklem życia kont usług, należy użyć następującego procesu:

1. Zbierz informacje o użyciu konta
1. Dołączanie konta usługi i aplikacji do bazy danych zarządzania konfiguracją (CMDB)
1. Przeprowadzenie oceny ryzyka lub formalnego przeglądu
1. Utwórz konto usługi i Zastosuj ograniczenia.
1. Zaplanuj i wykonaj przeglądy cykliczne. Dostosuj uprawnienia i zakresy w razie potrzeby.
1. W razie potrzeby należy anulować obsługę konta.

### <a name="collect-usage-information-for-the-service-account"></a>Zbieranie informacji o użyciu konta usługi

Zbierz odpowiednie informacje biznesowe dotyczące każdego konta usługi. W poniższej tabeli przedstawiono minimalne informacje, które mają być zbierane, ale należy zebrać wszystko, co jest konieczne, aby utworzyć przypadek biznesowy dla faktów istnienia konta.

| Dane| Szczegóły |
| - | - |
| Właściciel| Użytkownik lub Grupa, która jest odpowiedzialna za konto usługi |
| Przeznaczenie| Cel konta usługi |
| Uprawnienia (zakresy)| Oczekiwany zestaw uprawnień |
| Linki bazy danych zarządzania konfiguracją (CMDB)| Konto usługi między łączami z docelowym skryptem/aplikacją i właścicielami |
| Ryzyko| Ocena ryzyka i wpływu na działalność biznesową na podstawie oceny zagrożeń bezpieczeństwa |
| Okres istnienia| Przewidywany maksymalny okres istnienia umożliwiający Planowanie wygaśnięcia lub ponownej certyfikacji konta |


 

W idealnym przypadku należy wprowadzić żądanie dotyczące samoobsługowego konta i wymagać odpowiednich informacji. Właściciel, który może być właścicielem aplikacji lub firmy, członkiem IT lub właścicielem infrastruktury. Za pomocą narzędzia takiego jak Microsoft Forms dla tego żądania, a powiązane informacje ułatwiają przenoszenie go do narzędzia do spisywania programu CMDB, jeśli konto zostanie zatwierdzone.

### <a name="onboard-service-account-to-cmdb"></a>Dołączanie konta usługi do CMDB

Przechowywanie zebranych informacji w aplikacji typu CMDB. Oprócz informacji służbowych należy uwzględnić wszystkie zależności z innymi infrastrukturami, aplikacjami i procesami.  To centralne repozytorium ułatwi:

* Ocena ryzyka.

* Skonfiguruj konto usługi z wymaganymi ograniczeniami.

* Zrozumienie odpowiednich zależności funkcjonalnych i zabezpieczeń.

* Przeprowadzaj regularne przeglądy pod kątem bezpieczeństwa i kontynuacji.

* Skontaktuj się z właścicielami, aby przeglądać, wycofywanie i zmieniać konto usługi.

Rozważ konto usługi używane do uruchamiania witryny sieci Web i ma uprawnienia do nawiązywania połączenia z co najmniej jedną bazą danych SQL. Informacje przechowywane w Twoim obszarze CMDB dla tego konta usługi mogą być następujące:

|Dane | Szczegóły|
| - | - |
| Właściciel, zastępca| Jan nalotem, Anna Mayers |
| Przeznaczenie| Uruchom stronę sieci Web w usłudze HR i Połącz się z usługą HR — bazy danych. Może personifikować użytkownika końcowego podczas uzyskiwania dostępu do baz danych. |
| Uprawnienia, zakresy| HR-WebServer: Logowanie lokalne, uruchamianie strony sieci Web<br>HR-SQL1: Zaloguj się lokalnie, Przeczytaj wszystkie informacje o usłudze HR * Database<br>HR-SQL2: Zaloguj się lokalnie, Przeczytaj o WYNAGRODZENIu * baza danych |
| Cost Center| 883944 |
| Ocena ryzyka| Średniookresow Wpływ na działalność: Średni; informacje prywatne; Średniookresow |
| Ograniczenia konta| Zaloguj się do: tylko wyżej wymienione serwery; Nie można zmienić hasła; Zasady MBI-Password; |
| Okres istnienia| nieograniczone |
| Cykl przeglądu| Dwuletnie (według właściciela, przez zespół ds. zabezpieczeń, według prywatności) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>Przeprowadzenie oceny ryzyka lub formalnego przeglądu użycia konta usługi

Mając na uwadze swoje uprawnienia i cel, należy ocenić ryzyko, jakie to konto może stanowić dla skojarzonej aplikacji lub usługi, i infrastruktury w przypadku naruszenia zabezpieczeń. Rozważ bezpośrednie i pośrednie ryzyko. 

* Do czego atakującej uzyskuje bezpośredni dostęp?

* Jakie inne informacje lub systemy mogą uzyskać dostęp do konta usługi?

* Czy można użyć konta do udzielenia dodatkowych uprawnień?

* Jak będzie wiadomo, kiedy zmienią się uprawnienia?

Ocena ryzyka, po przeprowadzeniu i udokumentowaniu, może mieć wpływ na:

* Ograniczenia konta

* Okres istnienia konta

* Wymagania dotyczące przeglądu konta (erze i recenzenci)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>Tworzenie konta usługi i stosowanie ograniczeń konta

Utwórz konto usługi tylko wtedy, gdy odpowiednie informacje są udokumentowane w Twoim CMDB, i przeprowadź ocenę ryzyka. Ograniczenia konta powinny być wyrównane do oceny ryzyka. Należy wziąć pod uwagę poniższe ograniczenia dotyczące oceny.:

* [Wygaśnięcie konta](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)

   * W przypadku wszystkich kont użytkowników używanych jako konta usług Zdefiniuj realistyczną i niezależną datę końcową do użycia. Ustaw tę wartość przy użyciu flagi "Konto wygasa". Aby uzyskać więcej informacji, zobacz[ Set-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration). 

* Zaloguj się do ([LogonWorkstation](/powershell/module/addsadministration/set-aduser))

* Wymagania dotyczące [zasad haseł](../../active-directory-domain-services/password-policy.md)

* Tworzenie w [lokalizacji jednostki organizacyjnej](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) , która zapewnia zarządzanie tylko dla użytkowników uprzywilejowanych

* Skonfiguruj i zbierz inspekcje [, które wykryją zmiany](/windows/security/threat-protection/auditing/audit-directory-service-changes) konta usługi i [użycia konta usługi](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html).

Gdy wszystko jest gotowe do wprowadzenia do środowiska produkcyjnego, udziel bezpiecznego dostępu do konta usługi. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>Zaplanuj regularne przeglądy kont usług

Skonfiguruj regularne przeglądy kont usług sklasyfikowanych jako średnie i wysokie ryzyko. Przeglądy powinny obejmować: 

* Zaświadczanie właściciela do ciągłej potrzeby dla konta oraz uzasadnienie uprawnień i zakresów.

* Przegląd przez zespoły ds. prywatności i zabezpieczeń, w tym Ocena połączeń nadrzędnych i podrzędnych.

* Dane inspekcji zapewniające, że są używane tylko do celów zamierzonych

### <a name="deprovision-service-accounts"></a>Cofanie aprowizacji kont usługi

W procesie anulowania obsługi administracyjnej najpierw usuń uprawnienia i monitoruj, a następnie usuń konto, jeśli jest to konieczne.

Cofaj obsługę administracyjną kont usługi, gdy:

* Skrypt lub aplikacja, dla której zostało utworzone konto usługi, zostało wycofane.

* Funkcja w skrypcie lub aplikacji, do której jest używane konto usługi (na przykład dostęp do określonego zasobu) jest wycofywana.

* Konto usługi zostało zastąpione innym kontem usługi.

Po usunięciu wszystkich uprawnień Użyj tego procesu, aby usunąć konto.

1. Po cofnięciu aprowizacji skojarzonej aplikacji lub skryptu Monitoruj logowania i dostęp do zasobów dla skojarzonych kont usługi, aby upewnić się, że nie jest on używany w innym procesie. Jeśli masz pewność, że nie jest już potrzebne, przejdź do następnego kroku.

2. Wyłącz logowanie przy użyciu konta usługi i upewnij się, że nie jest już potrzebne. Utwórz zasady biznesowe dla kont czasu, które powinny pozostać wyłączone.

3. Usuń konto usługi po spełnieniu zasad pozostania wyłączenia. 

   * W przypadku kont MSA można [odinstalować go](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps) przy użyciu programu PowerShell lub usunąć ręcznie z kontenera zarządzanego konta usługi.

   * W przypadku kont komputerów lub użytkowników można ręcznie usunąć konto z usługi w Active Directory.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania kont usługi

* [Wprowadzenie do kont usług lokalnych](service-accounts-on-premises.md)

* [Bezpieczne konta usług zarządzane przez grupę](service-accounts-group-managed.md)

* [Zabezpieczone autonomicznie zarządzane konta usług](service-accounts-standalone-managed.md)

* [Bezpieczne konta komputerów](service-accounts-computer.md)

* [Zabezpieczanie kont użytkowników](service-accounts-user-on-premises.md)

* [Zarządzanie kontami lokalnych usług](service-accounts-govern-on-premises.md)