---
title: Dowiedz się, kiedy określony użytkownik będzie mógł uzyskać dostęp do aplikacji
description: Jak dowiedzieć się, kiedy krytycznie ważny jest dostęp do aplikacji skonfigurowanej do aprowizacji użytkowników w usłudze Azure AD
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
ms.openlocfilehash: 890c4a8a5aec3b15b150908c64bb114bd85a61a1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256766"
---
# <a name="check-the-status-of-user-provisioning"></a>Sprawdź stan aprowizacji użytkowników

Usługa Azure AD Provisioning uruchamia początkowy cykl aprowizacji dla systemu źródłowego i systemu docelowego, a następnie okresowe cykle przyrostowe. Podczas konfigurowania aprowizacji dla aplikacji można sprawdzić bieżący stan usługi aprowizacji i zobaczyć, kiedy użytkownik będzie mógł uzyskać dostęp do aplikacji.

## <a name="view-the-provisioning-progress-bar"></a>Wyświetl pasek postępu aprowizacji

 Na stronie **aprowizacji** aplikacji można wyświetlić stan usługi Azure AD Provisioning. **Bieżąca sekcja stanu** w dolnej części strony pokazuje, czy cykl aprowizacji rozpoczął Inicjowanie obsługi kont użytkowników. Postęp cyklu można obejrzeć, sprawdzić, ilu użytkowników i grup została zainicjowana, i zobaczyć, ile ról zostało utworzonych.

Podczas pierwszej konfiguracji automatycznej aprowizacji, **Bieżąca sekcja stanu** w dolnej części strony pokazuje stan początkowego cyklu aprowizacji. Ta sekcja jest aktualizowana za każdym razem, gdy cykl zostanie uruchomiony przyrostowo. Przedstawiono następujące szczegóły:
- Typ cyklu aprowizacji (początkowy lub przyrostowy), który jest aktualnie uruchomiony lub ostatnio zakończony.
- **Pasek postępu** przedstawiający procent cyklu aprowizacji, który został ukończony. Wartość procentowa odzwierciedla liczbę zainicjowanych stron. Należy pamiętać, że każda strona może zawierać wielu użytkowników lub grup, więc wartość procentowa nie jest bezpośrednio skorelowana z liczbą użytkowników, grup lub ról, które są obsługiwane.
- Przycisk **odświeżania** , którego można użyć, aby zachować zaktualizowany widok.
- Liczba **użytkowników** i **grup** w magazynie danych łącznika. Liczba rośnie wszędzie po dodaniu obiektu do zakresu aprowizacji. Ta liczba nie zostanie wyłączona, jeśli użytkownik zostanie usunięty lub usunięty trwale, ponieważ nie spowoduje to usunięcia obiektu z magazynu danych łącznika. Liczba zostanie ponownie obliczona po pierwszej synchronizacji po [zresetowaniu](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta) dysków CD 
- Łącze **Wyświetl dzienniki inspekcji** , które otwiera dzienniki aprowizacji usługi Azure AD, aby uzyskać szczegółowe informacje o wszystkich operacjach wykonywanych przez usługę aprowizacji użytkowników, w tym o stanie aprowizacji poszczególnych użytkowników (zobacz sekcję korzystanie z [dzienników aprowizacji](#use-provisioning-logs-to-check-a-users-provisioning-status) poniżej).

Po zakończeniu cyklu aprowizacji sekcja **statystyki do daty** pokazuje skumulowaną liczbę użytkowników i grup, których zainicjowano na dzień, wraz z datą ukończenia i czasem trwania ostatniego cyklu. **Identyfikator działania** jednoznacznie identyfikuje najnowszy cykl aprowizacji. **Identyfikator zadania** jest unikatowym identyfikatorem zadania aprowizacji i jest specyficzny dla aplikacji w dzierżawie.

Postęp aprowizacji można wyświetlić w Azure Portal z poziomu karty **&gt; aplikacje Azure Active Directory Enterprise &gt; \[ aplikacji \] &gt;** .

![Pasek postępu strony aprowizacji](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Korzystanie z dzienników aprowizacji w celu sprawdzenia stanu aprowizacji użytkownika

Aby sprawdzić stan aprowizacji wybranego użytkownika, zapoznaj się z [dziennikami aprowizacji (wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) w usłudze Azure AD. Wszystkie operacje wykonywane przez usługę aprowizacji użytkowników są rejestrowane w dziennikach aprowizacji usługi Azure AD. Obejmuje to wszystkie operacje odczytu i zapisu dokonane w systemach źródłowych i docelowych oraz dane użytkownika, które zostały odczytane lub zapisane podczas każdej operacji.

Możesz uzyskać dostęp do dzienników aprowizacji w Azure Portal, wybierając pozycję **Azure Active Directory** &gt; dzienniki aprowizacji **aplikacji w przedsiębiorstwie** &gt; **(wersja zapoznawcza)** w sekcji **działanie** . Możesz przeszukiwać dane aprowizacji na podstawie nazwy użytkownika lub identyfikatora w systemie źródłowym lub docelowym. Aby uzyskać szczegółowe informacje, zobacz [dzienniki aprowizacji (wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Dzienniki aprowizacji rejestrują wszystkie operacje wykonywane przez usługę aprowizacji, w tym:

* Wykonywanie zapytania dotyczącego usługi Azure AD dla przypisanych użytkowników, którzy znajdują się w zakresie aprowizacji
* Wykonywanie zapytania dotyczącego aplikacji docelowej pod kątem istnienia tych użytkowników
* Porównywanie obiektów użytkownika między systemem
* Dodawanie, aktualizowanie lub wyłączanie konta użytkownika w systemie docelowym na podstawie porównania

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji w Azure Portal, zobacz [Przewodnik po raportowaniu obsługi administracyjnej](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Jak długo trwa inicjowanie obsługi administracyjnej użytkowników?
W przypadku korzystania z automatycznej aprowizacji użytkowników w aplikacji usługa Azure AD automatycznie inicjuje i aktualizuje konta użytkowników w aplikacji na podstawie takich elementów, jak [przypisywanie użytkowników i grup](../manage-apps/assign-user-or-group-access-portal.md) w regularnych zaplanowanych odstępach czasu, zazwyczaj co 40 minut.

Czas, w którym dany użytkownik ma zostać zainicjowany, zależy głównie od tego, czy zadanie aprowizacji wykonuje cykl początkowy czy cykl przyrostowy.

- W przypadku **cyklu początkowego** czas zadania zależy od wielu czynników, takich jak liczba użytkowników i grup w zakresie dla aprowizacji oraz łączna liczba użytkowników i grup w systemie źródłowym. Pierwsza synchronizacja między usługą Azure AD a aplikacją może zająć od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie aprowizacji. Kompleksowa lista czynników wpływających na wydajność cyklu początkowego znajduje się w dalszej części tej sekcji.

- W przypadku **cykli przyrostowych** po cyklu początkowym czasy zadań mają być szybsze (np. w ciągu 10 minut), ponieważ usługa aprowizacji przechowuje znaki wodne, które reprezentują stan obu systemów po cyklu początkowym, co poprawia wydajność kolejnych synchronizacji. Czas zadania zależy od liczby zmian wykrytych w tym cyklu aprowizacji. Jeśli istnieje mniej niż 5 000 zmian członkostwa użytkowników lub grup, zadanie może zakończyć się w ramach pojedynczego przyrostowego cyklu udostępniania. 

Poniższa tabela zawiera podsumowanie czasów synchronizacji typowych scenariuszy aprowizacji. W tych scenariuszach system źródłowy jest usługą Azure AD, a system docelowy to aplikacja SaaS. Czasy synchronizacji są uzyskiwane z analizy statystycznej zadań synchronizacji dla aplikacji SaaS usługi ServiceNow, miejsc pracy, usług Salesforce i G Suite.


| Konfiguracja zakresu | Użytkownicy, grupy i członkowie w zakresie | Czas wstępnego cyklu | Przyrostowy czas cyklu |
| -------- | -------- | -------- | -------- |
| Synchronizuj tylko przypisanych użytkowników i grupy |  < 1 000 |  < 30 minut | < 30 minut |
| Synchronizuj tylko przypisanych użytkowników i grupy |  1 000 – 10 000 | 142 – 708 minut | < 30 minut |
| Synchronizuj tylko przypisanych użytkowników i grupy |   10 000 – 100 000 | 1 170 – 2 340 minut | < 30 minut |
| Synchronizuj wszystkich użytkowników i grupy w usłudze Azure AD |  < 1 000 | < 30 minut  | < 30 minut |
| Synchronizuj wszystkich użytkowników i grupy w usłudze Azure AD |  1 000 – 10 000 | < 30-120 minut | < 30 minut |
| Synchronizuj wszystkich użytkowników i grupy w usłudze Azure AD |  10 000 – 100 000  | 713 – 1 425 minut | < 30 minut |
| Synchronizuj wszystkich użytkowników w usłudze Azure AD|  < 1 000  | < 30 minut | < 30 minut |
| Synchronizuj wszystkich użytkowników w usłudze Azure AD | 1 000 – 10 000  | 43 – 86 minut | < 30 minut |

W przypadku **tylko przypisanego użytkownika i grup dla synchronizacji** konfiguracji można użyć następujących formuł, aby określić przybliżoną minimalną i maksymalną oczekiwany czas **cykli początkowej** :

- Minimum minut = 0,01 x [liczba przypisanych użytkowników, grup i członków grupy]
- Maksymalna liczba minut = 0,08 x [liczba przypisanych użytkowników, grup i członków grupy]

Podsumowanie czynników wpływających na czas trwania **cyklu początkowego**:

- Łączna liczba użytkowników i grup w zakresie aprowizacji.

- Łączna liczba użytkowników, grup i członków grupy obecnych w systemie źródłowym (Azure AD).

- Określa, czy użytkownicy w zakresie aprowizacji są dopasowywani do istniejących użytkowników w aplikacji docelowej, czy też muszą być tworzone po raz pierwszy. Zadania synchronizacji, dla których wszyscy użytkownicy są tworzone po raz pierwszy, są wykonywane *dwa razy* , o ile zadania synchronizacji, dla których wszyscy użytkownicy są dopasowane do istniejących użytkowników.

- Liczba błędów w [dziennikach aprowizacji](check-status-user-account-provisioning.md). Wydajność jest wolniejsza, jeśli wystąpi wiele błędów, a usługa aprowizacji przestała się na stan kwarantanny. 

- Limity szybkości żądań i ograniczanie wydajności zaimplementowane przez system docelowy. Niektóre systemy docelowe implementują limity szybkości żądań i ograniczania przepustowości, co może mieć wpływ na wydajność podczas dużych operacji synchronizacji. W tych warunkach aplikacja, która otrzymuje zbyt wiele żądań zbyt szybko, może spowalniać swoją szybkość reakcji lub zamknąć połączenie. Aby zwiększyć wydajność, łącznik musi zostać dostosowany przez niewysyłanie żądań aplikacji szybciej niż aplikacja może je przetworzyć. Dla łączników aprowizacji utworzonych przez firmę Microsoft należy wprowadzić tę korektę. 

- Liczba i rozmiary przypisanych grup. Synchronizowanie przypisanych grup trwa dłużej niż synchronizowanie użytkowników. Zarówno liczba, jak i rozmiary przypisanych grup wpływają na wydajność. Jeśli aplikacja ma [mapowania włączone dla synchronizacji obiektów grup](customize-application-attributes.md#editing-group-attribute-mappings), w dodatku do użytkowników są synchronizowane właściwości grupy, takie jak nazwy grup i członkostwa. Te dodatkowe synchronizacje będą trwać dłużej niż tylko synchronizowanie obiektów użytkownika.

- Jeśli wydajność jest problemem i podjęto próbę aprowizacji większości użytkowników i grup w dzierżawie, użyj filtrów zakresu. Filtry zakresu umożliwiają precyzyjne dostosowywanie danych, które usługa aprowizacji wyodrębnia z usługi Azure AD przez filtrowanie użytkowników na podstawie określonych wartości atrybutów. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz Tworzenie [aplikacji opartych na atrybutach przy użyciu filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Następne kroki
[Automatyzacja aprowizacji i anulowania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](user-provisioning.md)