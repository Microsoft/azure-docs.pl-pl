---
title: Dowiedz się, kiedy określony użytkownik będzie mógł uzyskać dostęp do aplikacji
description: Jak dowiedzieć się, kiedy niezwykle ważny użytkownik może uzyskać dostęp do aplikacji skonfigurowanej do aprowizowania użytkowników w usłudze Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9835ba2b6db2d71d0ff5825f2eb1996133e75537
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530818"
---
# <a name="check-the-status-of-user-provisioning"></a>Sprawdzanie stanu aprowizowania użytkowników

Usługa aprowizowania Azure AD uruchamia początkowy cykl inicjowania obsługi dla systemu źródłowego i docelowego, po którym następuje okresowe cykle przyrostowe. Konfigurując aprowizowanie dla aplikacji, możesz sprawdzić bieżący stan usługi aprowizowania i sprawdzić, kiedy użytkownik będzie mógł uzyskać dostęp do aplikacji.

## <a name="view-the-provisioning-progress-bar"></a>Wyświetlanie paska postępu aprowizowania

 Na **stronie Aprowizowanie** dla aplikacji można wyświetlić stan usługi aprowności Azure AD. W **sekcji Bieżący** stan w dolnej części strony pokazano, czy cykl aprowizowania rozpoczął aprowizowanie kont użytkowników. Możesz obserwować postęp cyklu, zobaczyć, ilu użytkowników i grup zostało aprowowanych, i zobaczyć, ile ról zostało utworzonych.

Po pierwszym skonfigurowaniu automatycznego  aprowizowania w sekcji Bieżący stan w dolnej części strony jest przedstawiany stan początkowego cyklu aprowizowania. Ta sekcja jest aktualizowana przy każdym uruchamianiu cyklu przyrostowego. Wyświetlane są następujące szczegóły:
- Typ cyklu inicjowania obsługi administracyjnej (początkowy lub przyrostowy), który jest aktualnie uruchomiony lub został ostatnio ukończony.
- Pasek **postępu przedstawiający** procent ukończonego cyklu inicjowania obsługi administracyjnej. Wartość procentowa odzwierciedla liczbę aprowowanych stron. Należy pamiętać, że każda strona może zawierać wielu użytkowników lub grupy, więc wartość procentowa nie jest bezpośrednio skorelowana z liczbą aprowizowanych użytkowników, grup lub ról.
- Przycisk **Odśwież** umożliwia zaktualizowanie widoku.
- Liczba użytkowników **i grup** **w** magazynie danych łącznika. Liczba zwiększa się za każdym razem, gdy obiekt zostanie dodany do zakresu aprowizowania. Liczba nie zejdzie, jeśli użytkownik zostanie usunięty nieułamkowo lub trwale, ponieważ nie spowoduje to usunięcia obiektu z magazynu danych łącznika. Po zresetowaniu usługi CDS liczba zostanie ponownie obliczona podczas pierwszej [synchronizacji](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true) 
- Link **Wyświetl** dzienniki inspekcji, który otwiera dzienniki aprowizowania usługi Azure AD, aby uzyskać szczegółowe informacje o [](#use-provisioning-logs-to-check-a-users-provisioning-status) wszystkich operacjach uruchamianych przez usługę aprowiowania użytkowników, w tym o stanie inicjowania obsługi dla poszczególnych użytkowników (zobacz sekcję Korzystanie z dzienników aprowności poniżej).

Po zakończeniu cyklu aprowizowania sekcja Statystyki do tej pory zawiera zbiorczą liczbę użytkowników i grup, które do tej pory zostały zaaprowizowane, wraz z datą ukończenia i czasem trwania ostatniego cyklu.  Identyfikator **działania jednoznacznie** identyfikuje najnowszy cykl aprowrowowania. Identyfikator **zadania jest** unikatowym identyfikatorem zadania aprowizowania i jest specyficzny dla aplikacji w dzierżawie.

Postęp aprowizowania można wyświetlić na stronie Azure Portal na karcie Aprowowanie aplikacji Azure Active Directory **&gt; Enterprise Apps. &gt; \[ \] &gt;**

![Pasek postępu strony aprowizowania](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Sprawdzanie stanu aprowizowania użytkownika za pomocą dzienników aprowiwizowania

Aby wyświetlić stan aprowizowania dla wybranego użytkownika, zapoznaj się z dziennikami [aprowności (wersja zapoznawcza) w](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) usłudze Azure AD. Wszystkie operacje uruchamiane przez usługę aprowizowania użytkowników są rejestrowane w dziennikach aprowności usługi Azure AD. Obejmuje to wszystkie operacje odczytu i zapisu wykonywane w systemach źródłowym i docelowym oraz dane użytkownika, które zostały odczytane lub zapisane podczas każdej operacji.

Aby uzyskać dostęp do dzienników aprowizowania w oknie Azure Portal, wybierz pozycję **dzienniki** aprow Azure Active Directory &gt; **Enterprise Apps** (wersja zapoznawcza) w &gt;  **sekcji** Aktywność. Dane aprowizowania można przeszukiwać na podstawie nazwy użytkownika lub identyfikatora w systemie źródłowym lub docelowym. Aby uzyskać szczegółowe informacje, zobacz [Dzienniki aprowowania (wersja zapoznawcza).](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 

Dzienniki aprowizowania rejestruje wszystkie operacje wykonywane przez usługę aprowiwizowania, w tym:

* Wykonywanie zapytania do usługi Azure AD dla przypisanych użytkowników, którzy znajdują się w zakresie aprowności
* Wykonywanie zapytania o istnienie tych użytkowników w aplikacji docelowej
* Porównywanie obiektów użytkownika między systemem
* Dodawanie, aktualizowanie lub wyłączanie konta użytkownika w systemie docelowym na podstawie porównania

Aby uzyskać więcej informacji na temat odczytywania dzienników aprowizowania w Azure Portal, zobacz [przewodnik po raportowaniu aprowizowania.](check-status-user-account-provisioning.md)

## <a name="how-long-will-it-take-to-provision-users"></a>Jak długo potrwa aprowizować użytkowników?
W przypadku korzystania z automatycznego aprowizowania użytkowników za pomocą aplikacji usługa [](../manage-apps/assign-user-or-group-access-portal.md) Azure AD automatycznie aprowizuje i aktualizuje konta użytkowników w aplikacji na podstawie takich czynności, jak przypisywanie użytkowników i grup w zaplanowanym odstępie czasu, zwykle co 40 minut.

Czas aprowizowania danego użytkownika zależy głównie od tego, czy w zadaniu aprowowania jest uruchomiony cykl początkowy, czy przyrostowy.

- W **przypadku początkowego** cyklu czas zadania zależy od wielu czynników, w tym liczby użytkowników i grup w zakresie aprowowania oraz całkowitej liczby użytkowników i grup w systemie źródłowym. Pierwsza synchronizacja między usługą Azure AD i aplikacją może potrwać od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie aprowowania. Pełna lista czynników, które mają wpływ na wydajność cyklu początkowego, znajduje się w dalszej części tej sekcji.

- W  przypadku cykli przyrostowych po cyklu początkowym czasy zadań są szybsze (np. w ciągu 10 minut), ponieważ usługa aprowizacji przechowuje znaki wodne reprezentujące stan obu systemów po początkowym cyklu, co poprawia wydajność kolejnych synchronizacji. Czas zadania zależy od liczby zmian wykrytych w tym cyklu inicjowania obsługi administracyjnej. Jeśli liczba zmian członkostwa użytkownika lub grupy jest mniejsza niż 5000, zadanie można zakończyć w ramach jednego przyrostowego cyklu inicjowania obsługi administracyjnej. 

W poniższej tabeli podsumowano czasy synchronizacji dla typowych scenariuszy aprowowania. W tych scenariuszach systemem źródłowym jest usługa Azure AD, a systemem docelowym jest aplikacja SaaS. Czasy synchronizacji pochodzą z analizy statystycznej zadań synchronizacji dla aplikacji SaaS ServiceNow, Workplace, Salesforce i G Suite.


| Konfiguracja zakresu | Użytkownicy, grupy i członkowie w zakresie | Początkowy czas cyklu | Czas cyklu przyrostowego |
| -------- | -------- | -------- | -------- |
| Synchronizuj tylko przypisanych użytkowników i grupy |  < 1000 |  < 30 minut | < 30 minut |
| Synchronizuj tylko przypisanych użytkowników i grupy |  1,000 - 10,000 | 142–708 min | < 30 minut |
| Synchronizuj tylko przypisanych użytkowników i grupy |   10,000 - 100,000 | 1170–2340 minut | < 30 minut |
| Synchronizowanie wszystkich użytkowników i grup w usłudze Azure AD |  < 1000 | < 30 minut  | < 30 minut |
| Synchronizowanie wszystkich użytkowników i grup w usłudze Azure AD |  1,000 - 10,000 | < 30–120 minut | < 30 minut |
| Synchronizowanie wszystkich użytkowników i grup w usłudze Azure AD |  10,000 - 100,000  | 713–1425 min | < 30 minut |
| Synchronizowanie wszystkich użytkowników w usłudze Azure AD|  < 1000  | < 30 minut | < 30 minut |
| Synchronizowanie wszystkich użytkowników w usłudze Azure AD | 1,000 - 10,000  | 43–86 min | < 30 minut |

W przypadku konfiguracji **Synchronizuj** tylko przypisanych użytkowników i grup można użyć następujących formuł do określenia przybliżonego minimalnego i maksymalnego oczekiwanego **początkowego czasu cyklu:**

- Minimalna liczba minut = 0,01 x [Liczba przypisanych użytkowników, grup i członków grupy]
- Maksymalna liczba minut = 0,08 x [Liczba przypisanych użytkowników, grup i członków grupy]

Podsumowanie czynników, które mają wpływ na czas trwania **początkowego cyklu:**

- Łączna liczba użytkowników i grup w zakresie aprowizowania.

- Całkowita liczba użytkowników, grup i członków grupy obecnych w systemie źródłowym (Azure AD).

- Określa, czy użytkownicy w zakresie aprowizowania są do siebie domierzone z istniejącymi użytkownikami w aplikacji docelowej, czy muszą zostać utworzeni po raz pierwszy. Zadania synchronizacji, dla których po raz pierwszy utworzono wszystkich użytkowników, trwa około *dwa* razy więcej niż zadania synchronizacji, dla których wszyscy użytkownicy są do siebie dopasowani z istniejącymi użytkownikami.

- Liczba błędów w dziennikach [aprowizowania.](check-status-user-account-provisioning.md) Wydajność jest niższa, jeśli wystąpiło wiele błędów, a usługa aprowicyjna przeszła w stan kwarantanny. 

- Limity szybkości żądań i ograniczanie przepustowości zaimplementowane przez system docelowy. Niektóre systemy docelowe implementują limity liczby żądań i ograniczanie przepustowości, co może mieć wpływ na wydajność podczas dużych operacji synchronizacji. W tych warunkach aplikacja, która odbiera zbyt wiele żądań zbyt szybko, może spowolnić jej odsetek odpowiedzi lub zamknąć połączenie. Aby zwiększyć wydajność, łącznik musi dostosowywać się, nie wysyłając żądań aplikacji szybciej, niż aplikacja może je przetworzyć. Aprowizowanie łączników sbudowaną przez firmę Microsoft pozwala wprowadzić to dostosowanie. 

- Liczba i rozmiary przypisanych grup. Synchronizowanie przypisanych grup trwa dłużej niż synchronizowanie użytkowników. Zarówno liczba, jak i rozmiar przypisanych grup mają wpływ na wydajność. Jeśli aplikacja ma włączone [mapowania](customize-application-attributes.md#editing-group-attribute-mappings)dla synchronizacji obiektów grupy, właściwości grupy, takie jak nazwy grup i członkostwa, są synchronizowane oprócz użytkowników. Te dodatkowe synchronizacje będą trwać dłużej niż tylko synchronizacja obiektów użytkownika.

- Jeśli wydajność staje się problemem i próbujesz aprowizować większość użytkowników i grup w dzierżawie, użyj filtrów zakresu. Filtry zakresu umożliwiają dostosowanie danych wyodrębnianych z usługi Azure AD przez odfiltrowanie użytkowników na podstawie określonych wartości atrybutów. Aby uzyskać więcej informacji na temat filtrów zakresu, zobacz Aprowizowanie aplikacji opartej na atrybutach [za pomocą filtrów zakresu.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

## <a name="next-steps"></a>Następne kroki
[Automatyzacja aprowizacji i anulowania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](user-provisioning.md)