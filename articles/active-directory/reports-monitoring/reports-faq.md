---
title: Raporty Azure Active Directory — często zadawane pytania | Microsoft Docs
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
ms.openlocfilehash: 4da0083a236900037b388798d825515e94613c20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533718"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Często zadawane pytania dotyczące raportów Azure Active Directory raportów

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Azure Active Directory (Azure AD). Aby uzyskać więcej informacji, zobacz [Raporty w usłudze Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Wprowadzenie 

**Pytanie: Obecnie używam interfejsów API punktu końcowego do programowego ściągania inspekcji usługi Azure AD i zintegrowanych raportów użycia `https://graph.windows.net/<tenant-name>/reports/` aplikacji do naszych systemów raportowania. Na co należy się przełączyć?**

**A:** Sprawdź odwołanie do [interfejsu API,](https://developer.microsoft.com/graph/) aby zobaczyć, jak można używać interfejsów [API do uzyskiwania dostępu do raportów aktywności.](concept-reporting-api.md) Ten punkt końcowy ma dwa raporty **(inspekcja** i **logowania),** które zawierają wszystkie dane, które zostały w starym punkcie końcowym interfejsu API. Ten nowy punkt końcowy zawiera również raport logowania z licencją usługi Azure AD — wersja Premium, który umożliwia uzyskiwanie informacji o użyciu aplikacji, użyciu urządzenia i logie użytkownika.

---

**Pytanie: Obecnie używam interfejsów API punktu końcowego do programowego ściągania raportów zabezpieczeń usługi Azure AD (określonych typów wykrywania, takich jak wyciek poświadczeń lub logowania z anonimowych adresów IP) do systemów `https://graph.windows.net/<tenant-name>/reports/` raportowania. Na co należy się przełączyć?**

**A:** Interfejs API wykrywania [ryzyka usługi Identity Protection](../identity-protection/howto-identity-protection-graph-api.md) umożliwia dostęp do wykrywania zabezpieczeń za pośrednictwem Microsoft Graph. Ten nowy format zapewnia większą elastyczność w zakresie wykonywania zapytań o dane dzięki zaawansowanemu filtrowaniu, wyborze pól i nie tylko oraz standaryzacji wykrywania ryzyka w jednym typie w celu łatwiejszej integracji z rozwiązaniami SIEM i innymi narzędziami do zbierania danych. Ponieważ dane są w innym formacie, nie można zastąpić nowego zapytania starymi zapytaniami. Jednak nowy [interfejs API używa Microsoft Graph](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true), która jest standardem firmy Microsoft dla takich interfejsów API jak Microsoft 365 lub Azure AD. Dlatego wymagane prace mogą rozszerzyć bieżące inwestycje Microsoft Graph lub rozpocząć przejście na tę nową standardową platformę.

---

**Pytanie: Jak mogę uzyskać licencję Premium?**

**A:** Zobacz [Wprowadzenie do Azure Active Directory — wersja Premium,](../fundamentals/active-directory-get-started-premium.md) aby uaktualnić wersję Azure Active Directory.

---

**Pytanie: Jak szybko powinny zostać wyświetlony dane działań po otrzymaniu licencji Premium?**

**A:** Jeśli masz już dane działań jako bezpłatną licencję, możesz je natychmiast zobaczyć. Jeśli nie masz żadnych danych, ich wyświetlanie w raportach może potrwać do 3 dni.

---

**Pytanie: Czy mogę wyświetlić dane z ostatniego miesiąca po otrzymaniu licencji usługi Azure AD w wersji Premium?**

**A:** Jeśli niedawno przeszliśmy do wersji Premium (w tym wersji próbnej), początkowo możesz zobaczyć dane z okresu do 7 dni. Gdy dane kumulują się, można zobaczyć dane z ostatnich 30 dni.

---

**Pytanie: Czy muszę być administratorem globalnym, aby zobaczyć logowania działania do aplikacji Azure Portal lub pobrać dane za pośrednictwem interfejsu API?**

**A:** Nie. Dostęp do danych raportowania można również uzyskać za pośrednictwem portalu lub interfejsu API, jeśli jesteś czytelnikiem **zabezpieczeń** lub **administratorem** zabezpieczeń dzierżawy. Oczywiście administratorzy **globalni** będą również mieć dostęp do tych danych.

---


## <a name="activity-logs"></a>Dzienniki aktywności


**Pytanie: jaki jest okres przechowywania danych dla dzienników aktywności (inspekcji i logowania) w Azure Portal?** 

**A:** Aby uzyskać więcej informacji, zobacz [zasady przechowywania danych dla raportów usługi Azure AD.](reference-reports-data-retention.md)

---

**Pytanie: Jak długo trwa wyświetlanie danych aktywności po ukończeniu zadania?**

**A:** Dzienniki inspekcji mają opóźnienie od 15 minut do godziny. Dzienniki aktywności logowania w przypadku niektórych rekordów mogą potrwać od 15 minut do 2 godzin.

---

**Pytanie: Czy mogę uzyskać Microsoft 365 dziennika aktywności za pośrednictwem Azure Portal?**

**A:** Mimo że Microsoft 365 aktywności i dzienniki aktywności usługi Azure AD współdzielą wiele zasobów katalogu, jeśli potrzebujesz pełnego widoku dzienników aktywności usługi Microsoft 365, przejdź do witryny [centrum administracyjne platformy Microsoft 365,](https://admin.microsoft.com) aby uzyskać informacje dziennika aktywności usługi Office 365.

---

**Pytanie: których interfejsów API używam do uzyskania informacji o Microsoft 365 aktywności?**

**A:** Użyj [interfejsów API Microsoft 365 Management,](/office/office-365-management-api/office-365-management-apis-overview) aby uzyskać dostęp do dzienników Microsoft 365 za pośrednictwem interfejsu API.

---

**Pytanie: Ile rekordów mogę pobrać z Azure Portal?**

**A:** Możesz pobrać maksymalnie 5000 rekordów z Azure Portal. Rekordy są sortowane według *najnowszych* i domyślnie jest 5000 najnowszych rekordów.

---

## <a name="risky-sign-ins"></a>Ryzykowne logowania

**Pytanie: W umacie Identity Protection istnieje wykrywanie ryzyka, ale nie widzę odpowiedniego logowania w raporcie logowania. Czy jest to oczekiwane?**

**A:** Tak, ochrona tożsamości ocenia ryzyko dla wszystkich przepływów uwierzytelniania, zarówno interakcyjnych, jak i nieinterakcyjnych. Jednak wszystkie logowania raport zawiera tylko logowania interakcyjne.

---

**Pytanie: Jak mogę wiedzieć, dlaczego logowanie lub użytkownik został oflagowany jako ryzykowny w Azure Portal?**

**A:** Jeśli masz subskrypcję **usługi Azure AD — wersja Premium,** możesz dowiedzieć się więcej o podstawowych  wykrywaniu ryzyka, wybierając użytkownika w  skrypcie Użytkownicy oflagowanych w związku z ryzykiem lub wybierając rekord w raporcie Ryzykowne logowania. Jeśli masz subskrypcję  **Bezpłatna** lub Podstawowa, możesz wyświetlać raporty dotyczące zagrożonych użytkowników i ryzykownych logów, ale nie widzisz podstawowych informacji dotyczących wykrywania ryzyka.

---

**Pytanie: Jak są obliczane adresy IP w raporcie logowania i ryzykownych logie?**

**A:** Adresy IP są wystawiane w taki sposób, że nie istnieje ostateczne połączenie między adresem IP a komputerem z tym adresem fizycznie zlokalizowanym. Mapowanie adresów IP jest bardziej skomplikowane przez czynniki takie jak dostawcy urządzeń przenośnych i sieci VPN wystawiające adresy IP z pul centralnych często bardzo daleko od miejsca, w którym faktycznie jest używane urządzenie klienckie. Obecnie w raportach usługi Azure AD konwersja adresu IP na lokalizację fizyczną jest najlepszym rozwiązaniem na podstawie śladów, danych rejestru, wyszukiwania wstecznego i innych informacji. 

---

**Pytanie: Co oznacza wykrycie ryzyka "Logowanie z wykrytym dodatkowym ryzykiem"?**

**A:** Aby uzyskać wgląd we wszystkie ryzykowne logowania w twoim środowisku, funkcja "Logowanie z wykrytym dodatkowym ryzykiem" pełni funkcję symbolu zastępczego logowania dla wykryć, które są dostępne wyłącznie dla Azure AD Identity Protection subskrybentów.

---

## <a name="conditional-access"></a>Dostęp warunkowy

**Pytanie: Co nowego w tej funkcji?**

**A:** Klienci mogą teraz rozwiązywać problemy z zasadami dostępu warunkowego za pośrednictwem wszystkich raportów logowania. Klienci mogą przejrzeć stan dostępu warunkowego i zapoznać się ze szczegółami zasad zastosowanych do logowania oraz wynikami poszczególnych zasad.

**Pytanie: Jak mogę rozpocząć?**

**A:** Aby rozpocząć pracę:

* Przejdź do raportu logowania w Azure Portal [.](https://portal.azure.com)
* Kliknij logowanie, które chcesz rozwiązać.
* Przejdź do karty **Dostęp warunkowy.** W tym miejscu możesz wyświetlić wszystkie zasady, które miały wpływ na logowanie, i wynik dla każdej zasady. 
    
**Pytanie: Jakie są wszystkie możliwe wartości stanu dostępu warunkowego?**

**A:** Stan dostępu warunkowego może mieć następujące wartości:

* **Nie zastosowano:** oznacza to, że nie było żadnych zasad dostępu warunkowego z użytkownikiem i aplikacją w zakresie. 
* **Powodzenie:** oznacza to, że zostały spełnione zasady dostępu warunkowego z użytkownikiem i aplikacją w zakresie oraz zasady dostępu warunkowego. 
* **Niepowodzenie:** Logowanie spełnia warunek użytkownika i aplikacji dla co najmniej jednej zasady dostępu warunkowego i nie spełniło warunków kontroli udzielenia dostępu albo nie zostało ustawione na blokowanie dostępu.
    
**Pytanie: Jakie są wszystkie możliwe wartości wyniku zasad dostępu warunkowego?**

**A:** Zasady dostępu warunkowego mogą mieć następujące wyniki:

* **Powodzenie:** zasady zostały pomyślnie spełnione.
* **Błąd:** Zasady nie zostały spełnione.
* **Nie zastosowano:** może to być spowodowane tym, że warunki zasad nie zostały spełnione.
* **Niewłączona:** jest to spowodowane tym, że zasady są w stanie wyłączonym. 
    
**Pytanie: Nazwa zasad w raporcie logowania wszystkich nie jest dopasowana do nazwy zasad w urzędu certyfikacji. Dlaczego?**

**A:** Nazwa zasad w raporcie logowania wszystkich jest oparta na nazwie zasad dostępu warunkowego w momencie logowania. Może to być niespójne z nazwą zasad w urzędu certyfikacji, jeśli nazwa zasad zostanie zaktualizowana później, czyli po zalogowaniu.

**Pytanie: Moje logowanie zostało zablokowane z powodu zasad dostępu warunkowego, ale raport aktywności logowania pokazuje, że logowanie zakończyło się pomyślnie. Dlaczego?**

**A:** Obecnie raport logowania może nie pokazywać dokładnych wyników dla scenariuszy programu Exchange ActiveSync w przypadku zastosowania dostępu warunkowego. W niektórych przypadkach wynik logowania w raporcie może oznaczać pomyślne zalogowanie, ale logowanie faktycznie nie powiodło się z powodu zasad dostępu warunkowego.
