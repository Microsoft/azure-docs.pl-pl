---
title: Samouczek dotyczący konfigurowania tożsamości N8 przy użyciu Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Samouczek dotyczący konfigurowania narzędzia administracyjnego TheAccessHub z Azure Active Directory B2C do obsługi migracji kont klientów i administrowania żądaniami obsługi klienta (CSR).
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 337275cef0f2159cb5fac40ac0435408baf3bbef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96170926"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Samouczek dotyczący konfigurowania narzędzia administracyjnego TheAccessHub z Azure Active Directory B2C

W tym przykładowym samouczku przedstawiono wskazówki dotyczące integrowania Azure Active Directory (AD) B2C z [narzędziem administracyjnym TheAccessHub](https://n8id.com/products/theaccesshub-admintool/) z tożsamości N8. To rozwiązanie dotyczy migracji kont klientów i administrowania żądaniami obsługi klienta (CSR).  

To rozwiązanie jest odpowiednie dla Ciebie, jeśli masz co najmniej jeden z następujących wymagań:

- Masz istniejącą witrynę i chcesz przeprowadzić migrację do Azure AD B2C. Jednak zoptymalizowaniem się z migracją kont klientów, w tym haseł

- Do administrowania kontami Azure AD B2C jest wymagane narzędzie dla CSR.

- Wymagane jest użycie administracji delegowanej dla danego działu obsługi klienta.

- Chcesz synchronizować i scalać dane z wielu repozytoriów w Azure AD B2C.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](./tutorial-create-tenant.md). Dzierżawca musi być połączony z subskrypcją platformy Azure.

- Środowisko narzędzia administracyjnego TheAccessHub: kontakt z [tożsamością N8](https://n8id.com/contact/) w celu aprowizacji nowego środowiska.

- Obowiązkowe Informacje dotyczące połączenia i poświadczeń dla wszystkich baz danych lub protokołów LDAP (Lightweight Directory Access Protocol), z których mają być migrowane dane klientów.

- Obowiązkowe Skonfigurowane środowisko Azure AD B2C do używania [zasad niestandardowych](./custom-policy-get-started.md), jeśli chcesz zintegrować narzędzie administracyjne TheAccessHub z przepływem zasad rejestrowania.

## <a name="scenario-description"></a>Opis scenariusza

Narzędzie administracyjne TheAccessHub działa podobnie jak każda inna aplikacja na platformie Azure. Można go uruchomić w ramach subskrypcji platformy Azure N8 Identity lub subskrypcji klienta. Na poniższym diagramie architektury przedstawiono implementację.

![Obraz przedstawiający diagram architektury n8identity](./media/partner-n8identity/n8identity-architecture-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik dotarł do strony logowania. Użytkownicy wybierają konto, aby utworzyć nowe konto, a następnie wprowadzić informacje na stronie. Azure AD B2C zbiera atrybuty użytkownika.
| 2. | Azure AD B2C wywołuje narzędzie administratora TheAccessHub i przekazuje atrybuty użytkownika
| 3. | Narzędzie administracyjne TheAccessHub sprawdza istniejącą bazę danych pod kątem bieżących informacji o użytkowniku.  
| 4. | Rekord użytkownika jest synchronizowany z bazy danych do narzędzia administracyjnego TheAccessHub.
| 5. | Narzędzie administracyjne TheAccessHub udostępnia dane za pomocą delegowanego administratora CSR/pomocy technicznej.
| 6. | Narzędzie administracyjne TheAccessHub synchronizuje rekordy użytkowników z Azure AD B2C.
| 7. |Na podstawie odpowiedzi o sukces/niepowodzenie z narzędzia administracyjnego TheAccessHub Azure AD B2C wysyła do użytkownika dostosowanego powitalnej wiadomości e-mail.

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Tworzenie administratora globalnego w dzierżawie Azure AD B2C

Narzędzie administracyjne TheAccessHub wymaga uprawnień do działania w imieniu administratora globalnego w celu odczytywania informacji o użytkowniku i wprowadzania zmian w dzierżawie Azure AD B2C. Wprowadzono zmiany w Twoich zwykłych administratorów; t wpływ narzędzia administratora TheAccessHub na zdolność do korzystania z dzierżawy.

Aby utworzyć administratora globalnego, wykonaj następujące kroki:

1. W Azure Portal Zaloguj się do dzierżawy Azure AD B2C jako administrator. Przejdź do **Azure Active Directory**  >  **użytkowników**
2. Wybierz **nowego użytkownika**
3. Wybierz pozycję **Utwórz użytkownika** , aby utworzyć użytkownika w katalogu regularnym, a nie klienta
4. Wypełnij formularz informacji o tożsamości

   a. Wprowadź nazwę użytkownika, taką jak "theaccesshub"

   b. Wprowadź nazwę, na przykład "konto usługi TheAccessHub"

5. Wybierz pozycję **Pokaż hasło** i skopiuj początkowe hasło do późniejszego użycia
6. Przypisywanie roli administratora globalnego

   a. Wybierz **użytkownika** bieżące role użytkownika, aby go zmienić

   b. Sprawdź rekord dla administratora globalnego

   c. **Wybierz pozycję**  >  **Utwórz**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>Połącz narzędzie administracyjne TheAccessHub z dzierżawcą Azure AD B2C

Narzędzie administracyjne TheAccessHub używa interfejs API programu Graph do odczytywania i wprowadzania zmian w katalogu. Pełni rolę administratora globalnego w dzierżawie. Do narzędzia administratora TheAccessHub są potrzebne dodatkowe uprawnienia, które można przyznać za pomocą narzędzia.

Aby autoryzować narzędzie administracyjne TheAccessHub do uzyskiwania dostępu do katalogu, wykonaj następujące kroki:

1. Zaloguj się do narzędzia administracyjnego TheAccessHub przy użyciu poświadczeń dostarczonych przez N8 tożsamość

2. Przejdź do   >  **Azure AD B2C konfiguracji** administratora systemu

3. Wybierz opcję **Autoryzuj połączenie**

4. W nowym oknie Zaloguj się przy użyciu konta administratora globalnego. Jeśli logujesz się po raz pierwszy przy użyciu nowego konta usługi, może zostać wyświetlony monit o zresetowanie hasła.

5. Postępuj zgodnie z monitami i wybierz pozycję **Akceptuj** , aby przyznać narzędziu administratora TheAccessHub żądane uprawnienia.

## <a name="configure-a-new-csr-user-using-your-enterprise-identity"></a>Konfigurowanie nowego użytkownika usługi CSR przy użyciu tożsamości przedsiębiorstwa

Utwórz użytkownika usługi CSR/pomocy technicznej, który uzyskuje dostęp do narzędzia administratora TheAccessHub przy użyciu istniejących poświadczeń Azure Active Directory przedsiębiorstwa.

Aby skonfigurować użytkownika CSR/pomocy technicznej przy użyciu logowania jednokrotnego (SSO), zalecane są następujące kroki:

1. Zaloguj się do narzędzia administracyjnego TheAccessHub przy użyciu poświadczeń dostarczonych przez N8 tożsamość.

2. Przejdź do **narzędzia Menedżer**  >  **Zarządzaj współpracownikami**

3. Wybierz pozycję **Dodaj współpracownika**

4. Wybierz **Typ współpracownika administrator platformy Azure**

5. Wprowadź informacje o profilu współpracownika

   a. Wybranie organizacji domowej spowoduje kontrolę nad tym, kto ma uprawnienia do zarządzania tym użytkownikiem.

   b. W polu Identyfikator logowania/Nazwa użytkownika usługi Azure AD Podaj główną nazwę użytkownika na podstawie konta Azure Active Directory użytkownika.

   c. Na karcie role TheAccessHub Sprawdź pomoc techniczną roli zarządzanej. Umożliwi użytkownikowi dostęp do widoku Zarządzanie współpracownikami. Użytkownik będzie nadal musiał umieścić w grupie lub być właścicielem organizacji, aby mógł działać na klientach.

6. Wybierz pozycję **Prześlij**.

## <a name="configure-a-new-csr-user-using-a-new-identity"></a>Konfigurowanie nowego użytkownika usługi CSR przy użyciu nowej tożsamości

Utwórz użytkownika CSR/pomocy technicznej, który będzie miał dostęp do narzędzia administracyjnego TheAccessHub z nowymi poświadczeniami lokalnymi, które są unikatowe dla narzędzia administratora TheAccessHub. Ta funkcja będzie używana głównie przez organizacje, które nie korzystają z usługi Azure AD dla przedsiębiorstwa.

Aby [skonfigurować użytkownika CSR/pomocy technicznej](https://youtu.be/iOpOI2OpnLI) bez rejestracji jednokrotnej, wykonaj następujące czynności:

1. Zaloguj się do narzędzia administracyjnego TheAccessHub przy użyciu poświadczeń dostarczonych przez tożsamość N8

2. Przejdź do **narzędzia Menedżer**  >  **Zarządzaj współpracownikami**

3. Wybierz pozycję **Dodaj współpracownika**

4. Wybierz **Typ współpracownika administrator lokalny**

5. Wprowadź informacje o profilu współpracownika

   a. Wybranie organizacji domowej spowoduje kontrolę nad tym, kto ma uprawnienia do zarządzania tym użytkownikiem.

   b. Na karcie **role TheAccessHub** wybierz dział **pomocy technicznej** roli zarządzanej. Umożliwi użytkownikowi dostęp do widoku Zarządzanie współpracownikami. Użytkownik będzie nadal musiał umieścić w grupie lub być właścicielem organizacji, aby mógł działać na klientach.

6. Skopiuj atrybuty **Identyfikator logowania/adres e-mail** i **hasło jednorazowe** . Podaj go nowemu użytkownikowi. Te poświadczenia będą używane do logowania się do narzędzia administracyjnego TheAccessHub. Użytkownik zostanie poproszony o podanie nowego hasła podczas pierwszego logowania.

7. Wybierz pozycję **Prześlij**

## <a name="configure-partitioned-csr-administration"></a>Konfigurowanie administrowania podzielonym CSR

Uprawnienia do zarządzania użytkownikami klienta i CSR/pomoc techniczna w narzędziu administratora TheAccessHub są zarządzane przy użyciu hierarchii organizacji. Wszyscy współpracownicy i klienci mają główną organizację, w której się znajdują. Określeni współpracownicy lub grupy współpracowników mogą być przypisane jako właściciele organizacji.  Właściciele organizacji mogą zarządzać (wprowadzać zmiany w) współpracowników i klientów w organizacjach lub podorganizacjach. Aby umożliwić wielu współpracownikom zarządzanie zbiorem użytkowników, można utworzyć grupę z wieloma elementami członkowskimi. Grupę można następnie przypisać jako właściciela organizacji, a wszyscy członkowie grupy mogą zarządzać współpracownikami i klientami w organizacji.

### <a name="create-a-new-group"></a>Tworzenie nowej grupy

1. Zaloguj się do narzędzia administracyjnego TheAccessHub przy użyciu **poświadczeń** dostarczonych przez N8 tożsamość

2. Przejdź do **organizacji > zarządzanie grupami**

3. Wybierz > **Dodaj grupę**

4. Wprowadź **nazwę grupy**, **Opis grupy** i **właściciela grupy**

5. Wyszukaj i zaznacz pola współpracowników, które mają należeć do grupy, a następnie wybierz pozycję >**Dodaj**

6. W dolnej części strony można zobaczyć wszystkich członków grupy.

7. Jeśli wymaganych elementów członkowskich można usunąć, zaznaczając **znak x** na końcu wiersza.

8. Wybierz pozycję **Prześlij**

### <a name="create-a-new-organization"></a>Tworzenie nowej organizacji

1. Zaloguj się do narzędzia administracyjnego TheAccessHub przy użyciu poświadczeń dostarczonych przez N8 tożsamość

2. Przejdź do organizacji > **zarządzanie organizacjami**

3. Wybierz > **Dodaj organizację**

4. Podaj **nazwę organizacji**, **właściciela organizacji** i **organizację nadrzędną**.

    a. Nazwa organizacji idealnie nanosi wartość odpowiadającą danym klienta. Podczas ładowania danych o współpracownikach i klientach w przypadku podania nazwy organizacji w obciążeniu, współpracownik może zostać automatycznie umieszczony w organizacji.

    b. Właściciel reprezentuje osobę lub grupę, która będzie zarządzać klientami i współpracownikami w tej organizacji oraz dowolną podorganizacją w ramach programu.

    c. Organizacja nadrzędna wskazuje, która inna organizacja jest również odpowiedzialna za tę organizację.

5. Wybierz pozycję **Prześlij**.

### <a name="modify-the-hierarchy-via-the-tree-view"></a>Modyfikowanie hierarchii za pomocą widoku drzewa

1. Zaloguj się do narzędzia administracyjnego TheAccessHub przy użyciu poświadczeń dostarczonych przez N8 tożsamość

2. Przejdź do   >  **widoku drzewa** narzędzi Menedżera

3. W tej reprezentacji można wizualizować, które współpracownicy i grupy mogą zarządzać organizacjami.

4. Hierarchie można modyfikować, przeciągając organizacje overtop organizacje, dla których mają być nadrzędne.

5. Wybierz pozycję **Zapisz** po zakończeniu zmiany hierarchii.

## <a name="customize-welcome-notification"></a>Dostosuj powiadomienie powitalne

Gdy korzystasz z usługi TheAccessHub do migrowania użytkowników z poprzedniego rozwiązania do uwierzytelniania do Azure AD B2C, możesz chcieć dostosować powiadomienie powitalne użytkownika, które jest wysyłane do użytkownika przez TheAccessHub podczas migracji. Ten komunikat zwykle zawiera link do klienta, aby ustawić nowe hasło w katalogu Azure AD B2C.

Aby dostosować powiadomienie:

1. Zaloguj się do TheAccessHub przy użyciu poświadczeń dostarczonych przez N8 tożsamość

2. Przejdź do **powiadomień administratora systemu**  >  

3. Wybieranie **szablonu tworzenia współpracownika**

4. Wybierz pozycję **Edytuj**

5. W razie potrzeby zmień pola komunikatu i szablonu. Pole szablonu jest oparte na kodzie HTML i może wysyłać powiadomienia e-mail w formacie HTML do klientów.

6. Po zakończeniu wybierz pozycję **Zapisz** .

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>Migrowanie danych z zewnętrznych źródeł danych do Azure AD B2C

Za pomocą narzędzia administracyjnego TheAccessHub można importować dane z różnych baz danych, LDAPs i plików CSV, a następnie wypchnąć je do dzierżawy Azure AD B2C. Jest to wykonywane przez załadowanie danych do typu współpracowników Azure AD B2C użytkownika w ramach narzędzia administracyjnego TheAccessHub.  Jeśli źródło danych nie jest samym platformą Azure, dane zostaną umieszczone zarówno w narzędziu administratora TheAccessHub, jak i w Azure AD B2C. Jeśli źródło danych zewnętrznych nie jest prostym plikiem CSV na komputerze, Skonfiguruj źródło danych przed wykonaniem ładowania danych. Poniższe kroki opisują tworzenie źródła danych i wykonywanie ładowania danych.

### <a name="configure-a-new-data-source"></a>Konfiguruj nowe źródło danych

1. Zaloguj się do narzędzia administracyjnego TheAccessHub przy użyciu poświadczeń dostarczonych przez N8 tożsamość

2. Przejdź do   >  **źródeł danych** administratora systemu

3. Wybierz pozycję **Dodaj źródło danych**

4. Podaj **nazwę** i **Typ** dla tego źródła danych

5. Wypełnij dane formularza, w zależności od typu źródła danych:

   **Dla baz danych**

   a. **Typ** — baza danych

   b. **Typ bazy danych** — wybierz bazę danych z jednego z obsługiwanych typów baz danych.

   c. **Adres URL połączenia** — wprowadź poprawnie sformatowane parametry połączenia JDBC. Takie jak: ``jdbc:postgresql://myhost.com:5432/databasename``

   d. **Nazwa użytkownika** — wprowadź nazwę użytkownika, aby uzyskać dostęp do bazy danych

   e. **Hasło** — wprowadź hasło, aby uzyskać dostęp do bazy danych

   f. **Zapytanie** — wprowadź zapytanie SQL, aby wyodrębnić szczegóły klienta. Takie jak: ``SELECT * FROM mytable;``

   przykład Wybierz pozycję **Testuj połączenie**, zobaczysz przykład danych, aby upewnić się, że połączenie działa.

   **Dla LDAPs**

   a. **Typ** — LDAP

   b. **Host** — wprowadź nazwę hosta lub adres IP komputera, na którym jest uruchomiony serwer LDAP. Takie jak: ``mysite.com``

   c. **Port** — wprowadź numer portu, w którym serwer LDAP nasłuchuje.

   d. **SSL** — zaznacz pole wyboru, jeśli narzędzie administracyjne TheAccessHub ma bezpiecznie komunikować się z protokołem LDAP przy użyciu protokołu SSL. Używanie protokołu SSL jest zdecydowanie zalecane.

   e. **Nazwa wyróżniająca logowania** — wprowadź nazwę wyróżniającą konta użytkownika, aby się zalogować, i PRZEszukiwanie LDAP

   f. **Hasło** — wprowadź hasło dla użytkownika

   przykład **Podstawowa nazwa wyróżniająca** — wprowadź nazwę wyróżniającą w górnej części hierarchii, w której chcesz wykonać wyszukiwanie

   h. **Filter** — wprowadź ciąg filtru LDAP, który umożliwi uzyskanie rekordów klientów

   i. **Atrybuty** — wprowadź rozdzieloną przecinkami listę atrybutów z rekordów klientów do przekazania do narzędzia administracyjnego TheAccessHub

   j. Wybierz **połączenie testowe**, aby zobaczyć przykładowe dane, aby upewnić się, że połączenie działa.

   **Dla usługi OneDrive**

   a. **Typ** — OneDrive dla firm

   b. Wybierz opcję **Autoryzuj połączenie**

   c. Nowe okno wyświetli monit o zalogowanie do **usługi OneDrive**, zaloguj się przy użyciu użytkownika z dostępem do odczytu do konta w usłudze OneDrive. Narzędzie administracyjne TheAccessHub, będzie działać dla tego użytkownika w celu odczytania plików ładowania woluminu CSV.

   d. Postępuj zgodnie z monitami i wybierz pozycję **Akceptuj** , aby przyznać narzędziu administratora TheAccessHub żądane uprawnienia.

6. Po zakończeniu wybierz pozycję **Zapisz** .  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>Synchronizuj dane ze źródła danych w Azure AD B2C

1. Zaloguj się do narzędzia administracyjnego TheAccessHub przy użyciu poświadczeń dostarczonych przez N8 tożsamość

2. Przejdź do   >  **synchronizacji danych** administratora systemu

3. Wybierz **nowe obciążenie**

4. Wybierz **Typ współpracownika** Azure AD B2C użytkownika

5. Wybierz pozycję **Źródło**, w oknie dialogowym podręcznym wybierz źródło danych. Jeśli utworzono źródło danych w usłudze OneDrive, należy również wybrać plik.

6. Jeśli nie chcesz tworzyć nowych kont klienta przy użyciu tego obciążenia, Zmień pierwsze zasady: **Jeśli współpracownik nie został znaleziony w TheAccessHub** , aby **nic nie robić**

7. Jeśli nie chcesz zaktualizować istniejących kont klienta przy użyciu tego obciążenia, Zmień drugie zasady, **Jeśli źródło i TheAccessHub danych** **nie są jeszcze nic**

8. Wybierz pozycję **Dalej**

9. W **konfiguracji mapowania wyszukiwania** można określić sposób skorelowania rekordów ładowania z klientami załadowanymi już do narzędzia administracyjnego TheAccessHub. Wybierz co najmniej jeden atrybut identyfikujący w źródle. Dopasowuje atrybuty z atrybutem w narzędziu administracyjnym TheAccessHub, które zawiera te same wartości. Jeśli zostanie znalezione dopasowanie, istniejący rekord zostanie zastąpiony; w przeciwnym razie zostanie utworzony nowy klient. Można sekwencjonować liczbę tych sprawdzeń. Na przykład można najpierw sprawdzić pocztę e-mail, a następnie imię i nazwisko.

10. W menu po lewej stronie wybierz pozycję **Mapowanie danych**.

11. W konfiguracji Data-Mapping należy przypisać, które atrybuty narzędzia administratora TheAccessHub powinny być wypełnione na podstawie atrybutów źródłowych. Nie ma potrzeby mapowania wszystkich atrybutów. Niezamapowane atrybuty pozostaną niezmienione dla istniejących klientów.

12. W przypadku mapowania na org_name atrybutu o wartości, która jest nazwą istniejącej organizacji, nowo utworzone klienci będą umieszczani w tej organizacji.

13. Wybierz pozycję **Dalej**

14. Jeśli to obciążenie ma być wykonywane, można określić harmonogram dzienny/tygodniowy lub miesięczny. W **przeciwnym razie pozostaw wartość domyślną.**

15. Wybierz pozycję **Prześlij**

16. Jeśli **harmonogram** został wybrany, nowy rekord zostanie natychmiast dodany do ekranu synchronizacji danych. Po osiągnięciu przez fazę walidacji 100% wybierz **nowy rekord** , aby zobaczyć oczekiwany wynik ładowania. W przypadku zaplanowanych obciążeń te rekordy zostaną wyświetlone dopiero po upływie zaplanowanego czasu.

17. Jeśli nie ma żadnych błędów, wybierz pozycję **Uruchom** , aby zatwierdzić zmiany. W przeciwnym razie wybierz pozycję **Usuń** z menu **więcej** , aby usunąć obciążenie. Następnie można poprawić dane źródłowe lub załadować mapowania i spróbować ponownie. Zamiast tego, jeśli liczba błędów jest mała, można ręcznie zaktualizować rekordy i wybrać opcję **Aktualizuj** dla każdego rekordu, aby wprowadzić poprawki. Na koniec możesz kontynuować wszelkie błędy i rozwiązać je później, jako **interwencje wspierające** w narzędziu administracyjnym TheAccessHub.

18. Gdy rekord **synchronizacji danych** zmieni się na 100% w fazie ładowania, zostaną zainicjowane wszystkie zmiany wynikłe z obciążenia. Klienci powinni zacząć rozpoczynać pracę lub otrzymywać zmiany w Azure AD B2C.

## <a name="synchronize-azure-ad-b2c-customer-data"></a>Synchronizuj Azure AD B2C dane klienta 

W ramach jednorazowej lub trwającej operacji Narzędzie administracyjne TheAccessHub może synchronizować wszystkie informacje o klientach z Azure AD B2C do narzędzia administracyjnego TheAccessHub. Dzięki temu administratorzy obsługi klienta i pomocy technicznej będą wyświetlać aktualne informacje o klientach.

Aby synchronizować dane z Azure AD B2C do narzędzia administracyjnego TheAccessHub:

1. Zaloguj się do narzędzia administracyjnego TheAccessHub przy użyciu poświadczeń dostarczonych przez N8 tożsamość

2. Przejdź do   >  **synchronizacji danych** administratora systemu

3. Wybierz **nowe obciążenie**

4. Wybierz **Typ współpracownika** Azure AD B2C użytkownika

5. Dla kroku **Opcje** pozostaw wartości domyślne.

6. Wybierz pozycję **Dalej**

7. W przypadku **mapowania danych & krok wyszukiwania** pozostaw wartości domyślne. Z wyjątkiem sytuacji, w której mapowanie do atrybutu **org_name** z wartością, która jest nazwą istniejącej organizacji, nowo utworzone klienci będą umieszczani w tej organizacji.

8. Wybierz pozycję **Dalej**

9. Jeśli to obciążenie ma być wykonywane, można określić harmonogram dzienny/tygodniowy lub miesięczny. W przeciwnym razie Zachowaj wartość domyślną: **teraz**. W regularnych odstępach czasu zalecamy synchronizację z Azure AD B2C.

10. Wybierz pozycję **Prześlij**

11. Jeśli harmonogram **został wybrany, nowy** rekord zostanie natychmiast dodany do ekranu synchronizacji danych. Po osiągnięciu przez fazę walidacji 100% wybierz nowy rekord, aby zobaczyć oczekiwany wynik ładowania. W przypadku zaplanowanych obciążeń te rekordy zostaną wyświetlone dopiero po upływie zaplanowanego czasu.

12. Jeśli nie ma żadnych błędów, wybierz pozycję **Uruchom** , aby zatwierdzić zmiany. W przeciwnym razie wybierz pozycję **Usuń** z menu więcej, aby usunąć obciążenie. Następnie można poprawić dane źródłowe lub załadować mapowania i spróbować ponownie. Zamiast tego, jeśli liczba błędów jest mała, można ręcznie zaktualizować rekordy i wybrać opcję **Aktualizuj** dla każdego rekordu, aby wprowadzić poprawki. Na koniec możesz kontynuować wszelkie błędy i rozwiązać je później, jako interwencje wspierające w narzędziu administracyjnym TheAccessHub.

13. Gdy rekord **synchronizacji danych** zmieni się na 100% w fazie ładowania, zostaną zainicjowane wszystkie zmiany wynikłe z obciążenia.

## <a name="configure-azure-ad-b2c-policies"></a>Konfigurowanie zasad Azure AD B2C

Okresowe synchronizowanie narzędzia administracyjnego TheAccessHub jest ograniczone w celu zachowania aktualności stanu w Azure AD B2C. Firma Microsoft może korzystać z interfejsu API i zasad Azure AD B2C narzędzia administracyjnego TheAccessHub, aby informować narzędzie administracyjne TheAccessHub o zmianach w miarę ich występowania. To rozwiązanie wymaga wiedzy technicznej dotyczącej [Azure AD B2C zasad niestandardowych](./custom-policy-get-started.md). W następnej sekcji przedstawimy przykładowe kroki zasad i bezpieczny certyfikat do powiadamiania narzędzia administracyjnego TheAccessHub o nowych kontach w zasadach niestandardowych Sign-Up.

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>Utwórz bezpieczne poświadczenie, aby wywołać interfejs API narzędzia administracyjnego TheAccessHub

1. Zaloguj się do narzędzia administracyjnego TheAccessHub przy użyciu poświadczeń dostarczonych przez N8 tożsamość

2. Przejdź do narzędzia administracyjnego **administratora systemu**  >  **narzędzi**  >  **zabezpieczenia API**

3. Wybierz pozycję **Generuj**

4. Kopiuj **hasło certyfikatu**

5. Wybierz pozycję **Pobierz** , aby pobrać certyfikat klienta.

6. Postępuj zgodnie z tym [samouczkiem](./secure-rest-api.md#https-client-certificate-authentication ) , aby dodać certyfikat klienta do Azure AD B2C.

### <a name="retrieve-your-custom-policy-examples"></a>Pobierz niestandardowe przykłady zasad

1. Zaloguj się do narzędzia administracyjnego TheAccessHub przy użyciu poświadczeń dostarczonych przez N8 tożsamość

2. Przejdź do programu **system** administrator administracyjne  >  **Narzędzia**  >  **usługi Azure B2C zasady**

3. Podaj domenę dzierżawy Azure AD B2C i dwa identyfikatory platformy obsługi tożsamości z konfiguracji platformy obsługi tożsamości

4. Wybierz pozycję **Zapisz**

5. Wybierz pozycję **Pobierz** , aby pobrać plik zip z podstawowymi zasadami, które umożliwiają dodawanie klientów do narzędzia administracyjnego TheAccessHub podczas rejestrowania klientów.

6. Postępuj zgodnie z tym [samouczkiem](./custom-policy-get-started.md) , aby rozpocząć projektowanie zasad niestandardowych w Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](./custom-policy-get-started.md?tabs=applications)