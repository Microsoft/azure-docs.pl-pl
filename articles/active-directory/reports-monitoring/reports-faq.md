---
title: Raporty Azure Active Directory często zadawane pytania | Microsoft Docs
description: Często zadawane pytania dotyczące Azure Active Directory raportów.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 05/12/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b6b182d030c267dcec7ed57c0c0dd1901b0cb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935091"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Często zadawane pytania dotyczące Azure Active Directory raportów

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące raportowania Azure Active Directory (Azure AD). Aby uzyskać więcej informacji, zobacz [Raporty w usłudze Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Wprowadzenie 

**P: obecnie korzystam z `https://graph.windows.net/<tenant-name>/reports/` interfejsów API punktu końcowego, aby przeprowadzić programistyczne raportowanie usługi Azure AD i raporty użycia zintegrowanej aplikacji w naszych systemach raportowania. Z czym mam się przełączyć?**

Odp **.:** Zapoznaj się z dokumentacją [interfejsu API](https://developer.microsoft.com/graph/) , aby zobaczyć, jak można [używać interfejsów API do uzyskiwania dostępu do raportów aktywności](concept-reporting-api.md). Ten punkt końcowy ma dwa raporty (**Inspekcja** i **logowania**), które zawierają wszystkie dane, które zostały uzyskane w starym punkcie końcowym interfejsu API. Ten nowy punkt końcowy zawiera również raport dotyczący logowania z licencją Azure AD — wersja Premium, za pomocą której można uzyskać informacje dotyczące użycia aplikacji, użycia urządzenia i logowania użytkownika.

---

**P: obecnie używam `https://graph.windows.net/<tenant-name>/reports/` interfejsów API punktu końcowego do ściągania raportów zabezpieczeń usługi Azure AD (określone typy wykrywania, takie jak nieujawnione poświadczenia lub logowania z anonimowych adresów IP) w naszych systemach raportowania programistycznie. Z czym mam się przełączyć?**

Odp **.:** Korzystając z [interfejsu API wykrywania ryzyka ochrony tożsamości](../identity-protection/howto-identity-protection-graph-api.md) , można uzyskać dostęp do wykrywania zabezpieczeń za pomocą Microsoft Graph. Ten nowy format zapewnia większą elastyczność w zakresie wykonywania zapytań dotyczących danych, z zaawansowanymi filtrowaniem, zaznaczaniem pól i wieloma innymi, a także umożliwia ujednolicenie wykrywania ryzyka w jednym typie w celu łatwiejszej integracji z rozwiązań Siem i innymi narzędziami zbierania danych. Ponieważ dane są w innym formacie, nie można zastąpić nowego zapytania dla starych zapytań. Jednak [Nowy interfejs API używa Microsoft Graph](/graph/api/resources/identityriskevent?view=graph-rest-beta), który jest standardem firmy Microsoft dla takich interfejsów api jak Microsoft 365 lub Azure AD. W związku z tym wymagana nakład pracy może stanowić zwiększenie bieżącej Microsoft Graph inwestycji lub pomóc Ci w rozpoczęciu przejścia na nową platformę standardową.

---

**P: Jak mogę uzyskać licencji Premium?**

Odp **.:** Aby uaktualnić wersję Azure Active Directory, zobacz [wprowadzenie do Azure Active Directory — wersja Premium](../fundamentals/active-directory-get-started-premium.md) .

---

**P: jak wkrótce należy zobaczyć dane o działaniach po uzyskaniu licencji Premium?**

Odp **.:** Jeśli masz już dane dotyczące działań jako bezpłatną licencję, możesz ją natychmiast zobaczyć. Jeśli nie masz żadnych danych, wyświetlenie danych w raportach zajmie do 3 dni.

---

**P: Czy mogę zobaczyć dane z ostatniego miesiąca po uzyskaniu licencji usługi Azure AD Premium?**

Odp **.:** Jeśli ostatnio przełączono się do wersji Premium (w tym wersji próbnej), na początku można zobaczyć dane do 7 dni. Po zebraniu danych można zobaczyć dane z ostatnich 30 dni.

---

**P: Czy muszę być administratorem globalnym, aby zobaczyć operacje logowania do Azure Portal lub pobrać dane za pomocą interfejsu API?**

Odp **.:** Nie. Możesz również uzyskać dostęp do danych raportowania za pomocą portalu lub za pomocą interfejsu API, jeśli jesteś **czytelnikiem zabezpieczeń** lub **administratorem zabezpieczeń** dla dzierżawy. Oczywiście **administratorzy globalni** będą również mieli dostęp do tych danych.

---


## <a name="activity-logs"></a>Dzienniki aktywności


**P: co to jest przechowywanie danych dla dzienników aktywności (inspekcja i logowania) w Azure Portal?** 

Odp **.:** Aby uzyskać więcej informacji, zobacz [zasady przechowywania danych dla raportów usługi Azure AD](reference-reports-data-retention.md).

---

**P: jak długo trwa czas, aż zobaczysz dane dotyczące aktywności po zakończeniu zadania?**

Odp **.:** Dzienniki inspekcji mają opóźnienie z przedziału od 15 minut do godziny. Dzienniki aktywności logowania mogą trwać od 15 minut do 2 godzin w przypadku niektórych rekordów.

---

**P: Czy można uzyskać Microsoft 365 informacje dziennika aktywności za pomocą Azure Portal?**

Odp **.:** Mimo że Microsoft 365 działania i dzienniki aktywności usługi Azure AD dzielą wiele zasobów katalogu, aby uzyskać pełny widok Microsoft 365 dzienników aktywności, należy przejść do [Centrum administracyjnego Microsoft 365](https://admin.microsoft.com) , aby uzyskać informacje o dzienniku aktywności pakietu Office 365.

---

**P: które interfejsy API są używane do uzyskiwania informacji o Microsoft 365 dziennikach aktywności?**

Odp **.:** Użyj [interfejsów API zarządzania Microsoft 365](/office/office-365-management-api/office-365-management-apis-overview) , aby uzyskać dostęp do dzienników aktywności Microsoft 365 za pomocą interfejsu API.

---

**P: ile rekordów można pobrać z Azure Portal?**

Odp **.:** Możesz pobrać do 5000 rekordów z Azure Portal. Rekordy są sortowane według *najnowszych ustawień i domyślnie* otrzymują najnowsze rekordy 5000.

---

## <a name="risky-sign-ins"></a>Ryzykowne logowania

**Pytanie: wystąpiło wykrywanie ryzyka w usłudze Identity Protection, ale nie widzę odpowiedniego logowania w raporcie logowania. Czy jest to oczekiwane?**

Odp **.:** Tak, program Identity Protection szacuje ryzyko dla wszystkich przepływów uwierzytelniania niezależnie od tego, czy interaktywny, czy nieinteraktywny. Jednak wszystkie raporty dotyczące tylko logowania są wyświetlane tylko w przypadku logowania interakcyjnego.

---

**P: Jak mogę wiedzieć, dlaczego zalogowanie lub użytkownik został oflagowany w Azure Portal?**

Odp **.:** Jeśli masz subskrypcję usługi **Azure AD — wersja Premium** , możesz dowiedzieć się więcej na temat podstawowych wykryć ryzyka, wybierając użytkownika w **przypadku użytkowników oflagowanych w ramach ryzyka** lub wybierając rekord w raporcie **ryzykowne logowania** . Jeśli masz subskrypcję **bezpłatną** lub **podstawową** , możesz przeglądać użytkowników na ryzyko i ryzykowne raporty dotyczące logowania, ale nie widzisz podstawowych informacji o wykrywaniu ryzyka.

---

**P: w jaki sposób adresy IP są obliczane w raporcie logowania i ryzykowne logowania?**

Odp **.:** Adresy IP są wystawiane w taki sposób, że nie istnieje ostateczne połączenie między adresem IP i lokalizacją, w której komputer z tym adresem jest fizycznie zlokalizowany. Mapowanie adresów IP jest bardziej skomplikowane, ponieważ takie czynniki jak dostawcy urządzeń przenośnych i sieci VPN wystawiające adresy IP z pul centralnych często są bardzo daleko od miejsca, w którym urządzenie klienckie jest rzeczywiście używane. Obecnie w raportach usługi Azure AD konwertowanie adresu IP na lokalizację fizyczną jest najlepszym nakładem pracy na podstawie śladów, danych rejestru, wyszukiwania wstecznego i innych informacji. 

---

**P: co oznacza wykrycie ryzyka "Logowanie z dodatkowym ryzykiem" oznacza?**

Odp **.:** Aby uzyskać wgląd we wszystkie ryzykowne logowania w środowisku, "Zaloguj się z dodatkowym ryzykiem wykrytym" funkcji jako symbol zastępczy dla logowań do wykrycia, które są wyłączne dla subskrybentów Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Dostęp warunkowy

**P: co nowego w tej funkcji?**

Odp **.:** Klienci mogą teraz rozwiązywać problemy z zasadami dostępu warunkowego za całym raportem logowania. Klienci mogą zapoznać się ze stanem dostępu warunkowego i szczegółowe w celu uzyskania szczegółowych informacji dotyczących zasad, które zastosowały się do logowania, oraz wyników dla każdej zasady.

**P: Jak mogę zacząć?**

Odp **.:** Aby rozpocząć:

* Przejdź do raportu logowania w [Azure Portal](https://portal.azure.com).
* Kliknij Logowanie, które chcesz rozwiązać.
* Przejdź do karty **dostęp warunkowy** . W tym miejscu można wyświetlić wszystkie zasady, które mają wpływ na logowanie i wyniki dla każdej zasady. 
    
**P: Jakie są wszystkie możliwe wartości dla stanu dostępu warunkowego?**

Odp **.:** Stan dostępu warunkowego może mieć następujące wartości:

* **Nie zastosowano**: oznacza to, że nie ma żadnych zasad dostępu warunkowego z użytkownikiem i aplikacją w zakresie. 
* **Sukces**: oznacza to, że wystąpiły zasady dostępu warunkowego z użytkownikiem i aplikacją w zakresie, a zasady dostępu warunkowego zostały pomyślnie spełnione. 
* **Niepowodzenie**: zalogowanie zostało spełnione warunek użytkownika i aplikacji co najmniej jednej zasady dostępu warunkowego, a kontrolki grantu nie są spełnione lub ustawione na zablokowanie dostępu.
    
**P: Jakie są wszystkie możliwe wartości wyniku zasady dostępu warunkowego?**

Odp **.:** Zasady dostępu warunkowego mogą mieć następujące wyniki:

* **Sukces**: zasady zostały pomyślnie spełnione.
* **Niepowodzenie**: zasady nie zostały spełnione.
* **Nie zastosowano**: może to być spowodowane tym, że warunki zasad nie zostały spełnione.
* **Niewłączone**: jest to spowodowane zasadami w stanie wyłączenia. 
    
**P: Nazwa zasad we wszystkich raportach logowania nie jest zgodna z nazwą zasad w urzędzie certyfikacji. Dlaczego?**

Odp **.:** Nazwa zasad we wszystkich raportach logowania jest oparta na nazwie zasad dostępu warunkowego w momencie logowania. Ta wartość może być niespójna z nazwą zasad w urzędzie certyfikacji w przypadku późniejszej aktualizacji nazwy zasad, czyli po zalogowaniu się.

**P: moje logowanie zostało zablokowane z powodu zasad dostępu warunkowego, ale raport działań związanych z logowaniem pokazuje, że logowanie powiodło się. Zalet?**

Odp **.:** Obecnie raport logowania może nie wyświetlać dokładnych wyników scenariuszy programu Exchange ActiveSync, gdy jest stosowany dostęp warunkowy. Mogą wystąpić sytuacje, w których wynik logowania w raporcie przedstawia Pomyślne logowanie, ale logowanie nie powiodło się z powodu zasad dostępu warunkowego.
