---
title: plik dołączany
description: plik dołączany
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 12c9b1226e3ba928a4062049c7839d4e46ef727d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91024970"
---
## <a name="for-users-in-your-directory"></a>Dla użytkowników w katalogu

Wykonaj te kroki, jeśli chcesz zezwolić użytkownikom w katalogu na żądanie tego pakietu dostępu. Podczas definiowania zasad żądań można określić poszczególnych użytkowników lub bardziej często grup użytkowników. Na przykład organizacja może mieć już grupę, taką jak **Wszyscy pracownicy**.  Jeśli ta grupa zostanie dodana w zasadach dla użytkowników, którzy mogą żądać dostępu, wówczas każdy członek tej grupy może następnie zażądać dostępu.

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu** , kliknij pozycję **dla użytkowników w katalogu**.

    Po wybraniu tej opcji pojawią się nowe opcje, które umożliwią dokładniejsze określenie, kto w katalogu może zażądać tego pakietu dostępu.

    ![Dostęp do pakietów — dla użytkowników w katalogu](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Wybierz jedną z następujących opcji:

    |  |  |
    | --- | --- |
    | **Konkretni użytkownicy i grupy** | Wybierz tę opcję, jeśli chcesz, aby tylko użytkownicy i grupy w Twoim katalogu mogli żądać tego pakietu dostępu. |
    | **Wszyscy członkowie (z wyłączeniem Gości)** | Wybierz tę opcję, jeśli chcesz, aby wszyscy użytkownicy należący do katalogu mogli żądać tego pakietu dostępu. Ta opcja nie obejmuje żadnych użytkowników-Gości, którzy zostali zaproszeni do katalogu. |
    | **Wszyscy użytkownicy (w tym Goście)** | Wybierz tę opcję, jeśli chcesz, aby wszyscy użytkownicy i Goście w katalogu mogli żądać tego pakietu dostępu. |

    Użytkownicy-Goście odwołują się do użytkowników zewnętrznych, którzy zostali zaproszeni do katalogu za pomocą [usługi Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md). Aby uzyskać więcej informacji o różnicach między użytkownikami należącymi do członków i użytkownikami-Gośćmi, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. W przypadku wybrania **określonych użytkowników i grup**kliknij pozycję **Dodaj użytkowników i grupy**.

1. W okienku wybierz użytkowników i grupy wybierz użytkowników i grupy, które chcesz dodać.

    ![Dostęp do pakietu-żądania — Wybieranie użytkowników i grup](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Kliknij pozycję **Wybierz** , aby dodać użytkowników i grupy.

1. Przejdź do sekcji [zatwierdzenie](#approval) .

## <a name="for-users-not-in-your-directory"></a>Dla użytkowników nieznajdujących się w katalogu

 Użytkownicy, którzy nie znajdują się **w katalogu** , odwołują użytkowników znajdujących się w innym katalogu lub domenie usługi Azure AD. Ci użytkownicy mogą nie zostać zaproszeni do katalogu. Katalogi usługi Azure AD muszą być skonfigurowane tak, aby zezwalać na zaproszenia w **ograniczeniach współpracy**. Aby uzyskać więcej informacji, zobacz [Włączanie współpracy zewnętrznej B2B i zarządzanie osobami, które mogą zapraszać Gości](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Konto użytkownika-gościa zostanie utworzone dla użytkownika, który jeszcze nie znajduje się w katalogu, którego żądanie jest zatwierdzone lub zaakceptowane. Gość zostanie zaproszony, ale nie otrzyma wiadomości e-mail z zaproszeniem. Zamiast tego otrzymają wiadomość e-mail, gdy zostanie dostarczone przypisanie pakietu dostępu. Domyślnie, gdy użytkownik tego gościa nie ma już żadnych przypisań pakietów dostępu, ponieważ ostatnie przypisanie wygasło lub zostało anulowane, konto użytkownika Gość zostanie zablokowane przed zalogowaniem się i usunięciem. Jeśli chcesz, aby użytkownicy-Goście pozostali w Twoim katalogu przez czas nieokreślony, nawet jeśli nie mają przypisanych pakietów dostępu, możesz zmienić ustawienia konfiguracji zarządzania uprawnieniami. Aby uzyskać więcej informacji na temat obiektu użytkownika-gościa, zobacz [właściwości Azure Active Directory użytkownika współpracy B2B](../articles/active-directory/b2b/user-properties.md).

Wykonaj następujące kroki, aby zezwolić użytkownikom, którzy nie należą do katalogu, na żądanie tego pakietu dostępu:

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu** , kliknij pozycję **dla użytkowników, którzy nie są w Twoim katalogu**.

    Po wybraniu tej opcji pojawiają się nowe opcje.

    ![Dostęp do pakietów — dla użytkowników spoza katalogu](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Wybierz jedną z następujących opcji:

    |  |  |
    | --- | --- |
    | **Określone organizacje połączone** | Wybierz tę opcję, jeśli chcesz wybrać z listy organizacji, które wcześniej dodaliśmy do administratora. Wszyscy użytkownicy wybranych organizacji mogą zażądać tego pakietu dostępu. |
    | **Wszystkie połączone organizacje** | Wybierz tę opcję, jeśli wszyscy użytkownicy ze wszystkich połączonych organizacji mogą zażądać tego pakietu dostępu. |
    | **Wszyscy użytkownicy (wszystkie połączone organizacje + Wszyscy nowi użytkownicy zewnętrzni)** | Wybierz tę opcję, jeśli wszyscy użytkownicy ze wszystkich połączonych organizacji mogą zażądać tego pakietu dostępu, a ustawienia listy dozwolonych lub zablokowanych B2B powinny mieć pierwszeństwo dla każdego nowego użytkownika zewnętrznego. |

    Połączona organizacja to zewnętrzny katalog usługi Azure AD lub domena, z którą istnieje relacja.

1. W przypadku wybrania opcji **określone połączone organizacje**kliknij pozycję **Dodaj katalogi** , aby wybrać z listy połączonych organizacji, które zostały wcześniej dodane przez administratora.

1. Wpisz nazwę lub nazwę domeny, aby wyszukać połączoną wcześniej organizację.

    ![Dostęp do pakietu-żądania — wybierz katalogi](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Jeśli organizacja, z którą chcesz współpracować, nie znajduje się na liście, możesz poprosił administratora, aby dodał ją jako połączoną organizację. Aby uzyskać więcej informacji, zobacz [Dodawanie połączonej organizacji](../articles/active-directory/governance/entitlement-management-organization.md).

1. Po wybraniu wszystkich połączonych organizacji kliknij pozycję **Wybierz**.

    > [!NOTE]
    > Wszyscy użytkownicy z wybranych połączonych organizacji będą mogli zażądać tego pakietu dostępu. Obejmuje to użytkowników w usłudze Azure AD ze wszystkich poddomen skojarzonych z organizacją, chyba że te domeny są blokowane przez listę dozwolonych lub zablokowanych warunków B2B platformy Azure. Aby uzyskać więcej informacji, zobacz [Zezwalanie lub blokowanie zaproszeń użytkownikom B2B z określonych organizacji](../articles/active-directory/b2b/allow-deny-list.md).

1. Przejdź do sekcji [zatwierdzenie](#approval) .

## <a name="none-administrator-direct-assignments-only"></a>Brak (tylko przypisania bezpośrednie do administratorów)

Wykonaj następujące kroki, aby obejść żądania dostępu i umożliwić administratorom bezpośrednie przypisanie określonych użytkowników do tego pakietu dostępu. Użytkownicy nie będą musieli żądać pakietu dostępu. Nadal można ustawiać ustawienia cyklu życia, ale nie ma żadnych ustawień żądania.

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu** , kliknij pozycję **Brak (tylko przypisania bezpośrednie administratora**).

    ![Dostęp do pakietów — brak przypisań tylko administrator](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Po utworzeniu pakietu dostępu można bezpośrednio przypisać określonych użytkowników wewnętrznych i zewnętrznych do pakietu dostępu. W przypadku określenia użytkownika zewnętrznego w katalogu zostanie utworzone konto użytkownika-gościa. Aby uzyskać informacje na temat bezpośredniego przypisywania użytkownika, zobacz [Wyświetlanie, Dodawanie i usuwanie przypisań dla pakietu dostępu](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Przejdź do sekcji [Włączanie żądań](#enable-requests) .

## <a name="approval"></a>Zatwierdzenie

W sekcji zatwierdzenie należy określić, czy zatwierdzenie ma być wymagane, jeśli użytkownicy zażądają tego pakietu dostępu. Ustawienia zatwierdzania działają w następujący sposób:

- Tylko jeden z wybranych osób zatwierdzających lub zatwierdzeń powrotu musi zatwierdzić żądanie zatwierdzenia pojedynczego etapu. 
- Tylko jedna z wybranych osób zatwierdzających z poszczególnych etapów musi zatwierdzić żądanie zatwierdzenia 2-etapowe.
- Osoba zatwierdzająca może być kierownikiem, wewnętrznym sponsorem lub zewnętrznym sponsorem w zależności od tego, kto ma dostęp do zasad.
- Zatwierdzenie od każdej wybranej osoby zatwierdzającej nie jest wymagane w przypadku zatwierdzania jednego lub 2-etapowego.
- Decyzja o zatwierdzeniu zależy od osoby zatwierdzającej, która najpierw przegląda żądanie.

Aby zapoznać się z pokazem dodawania osób zatwierdzających do zasad żądań, Obejrzyj następujące wideo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Aby zapoznać się z przykładem dodawania wieloetapowego zatwierdzania do zasad żądania, Obejrzyj następujące wideo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Wykonaj następujące kroki, aby określić ustawienia zatwierdzania dla żądań pakietu dostępu:

1. Aby wymagać zatwierdzenia dla żądań od wybranych użytkowników, ustaw przełącznik **Wymagaj zatwierdzenia** na **wartość tak**. Lub, aby żądania były automatycznie zatwierdzane, ustaw przełącznik na wartość **nie**.

1. Aby wymagać od użytkowników podania uzasadnienia żądania pakietu dostępu, ustaw przełącznik Żądaj **uzasadnienia żądającego** na **wartość tak**.
    
1. Teraz Ustal, czy żądania będą wymagały zatwierdzenia pojedynczego lub 2-etapowego. Ustaw liczbę przełączeń między **etapami** na **1** w celu zatwierdzenia pojedynczego etapu lub ustaw przełącznik na **2** na potrzeby zatwierdzania dwuetapowego.

    ![Dostęp do pakietów — ustawienia zatwierdzenia](./media/active-directory-entitlement-management-request-policy/approval.png)

Aby dodać osoby zatwierdzające, należy wykonać następujące czynności: 

### <a name="single-stage-approval"></a>Zatwierdzenie na jednym etapie

1. Dodaj **pierwszą osobę zatwierdzającą**:
    
    Jeśli zasady są skonfigurowane do zarządzania dostępem dla użytkowników w katalogu, można wybrać pozycję **Menedżer jako osoba zatwierdzająca**. Lub Dodaj określonego użytkownika, klikając przycisk **Dodaj osoby zatwierdzające** po wybraniu opcji wybierz określonych osoby zatwierdzające z menu rozwijanego.
    
    ![Dostęp do pakietów — dla użytkowników w katalogu — w pierwszej kolejności](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Jeśli te zasady mają ustawiony dostęp do zarządzania użytkownikami spoza katalogu, możesz wybrać **zewnętrznego sponsora** lub **wewnętrznego sponsora**. Lub Dodaj określonego użytkownika, klikając pozycję **Dodaj osoby zatwierdzające** lub grupy w obszarze Wybierz określone osoby zatwierdzające.
    
    ![Dostęp do pakietów żądań — dla użytkowników poza katalogiem — pierwszej osoby zatwierdzającej](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. W przypadku wybrania opcji **Menedżer** jako pierwszej osoby zatwierdzającej kliknij pozycję **Dodaj rezerwę** , aby wybrać co najmniej jednego użytkownika lub grupę w katalogu jako osobę zatwierdzającą. Osoby zatwierdzające rezerwy otrzymują żądanie, jeśli Zarządzanie uprawnieniami nie może znaleźć Menedżera dla użytkownika żądającego dostępu.

    Menedżer został znaleziony przez Zarządzanie uprawnieniami przy użyciu atrybutu **Menedżera** . Ten atrybut znajduje się w profilu użytkownika w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Dodawanie lub aktualizowanie informacji o profilu użytkownika przy użyciu Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. W przypadku wybrania **opcji wybierz określone osoby zatwierdzające**kliknij przycisk **Dodaj osoby zatwierdzające** , aby wybrać co najmniej jednego użytkownika lub grupę w katalogu, który ma zostać zatwierdzony.

1. W polu w obszarze **decyzja musi być podejmowana w ciągu kilku dni?** Określ liczbę dni, przez które osoba zatwierdzająca musi przejrzeć żądanie tego pakietu dostępu.

    Jeśli żądanie nie zostanie zatwierdzone w tym okresie, zostanie automatycznie odrzucone. Użytkownik będzie musiał przesłać kolejne żądanie dotyczące pakietu dostępu.

1. Aby wymagać, aby osoby zatwierdzające zapewnią uzasadnienie swojej decyzji, należy ustawić opcję Wymagaj uzasadnienia osoby zatwierdzającej na **wartość tak**.

    Uzasadnienie jest widoczne dla innych osób zatwierdzających i osoby zgłaszającej żądanie.

### <a name="2-stage-approval"></a>2 — zatwierdzenie na etapie

W przypadku wybrania 2-etapowego zatwierdzania należy dodać drugą osobę zatwierdzającą.

1. Dodaj **drugą osobę zatwierdzającą**: 
    
    Jeśli użytkownicy znajdują się w katalogu, należy dodać określonego użytkownika jako drugiej osoby zatwierdzającej, klikając przycisk **Dodaj osoby zatwierdzające** w obszarze Wybieranie określonych osób zatwierdzających.

    ![Dostęp do pakietu-żądania — dla użytkowników w katalogu sekundy osoby zatwierdzającej](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Jeśli użytkownicy nie znajdują się w katalogu, wybierz **wewnętrznej sponsora** lub **zewnętrzny sponsor** jako drugą osobę zatwierdzającą. Po wybraniu osoby zatwierdzającej Dodaj osoby zatwierdzające rezerw.

    ![Dostęp do pakietu-żądania — dla użytkowników poza katalogiem w drugiej osoby zatwierdzającej](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Określ liczbę dni, przez którą druga osoba zatwierdzająca musi zatwierdzić żądanie w polu objętym **decyzją musi być podjęta w ciągu kilku dni?**. 

1. Ustaw opcję Wymagaj uzasadnienia osoby zatwierdzającej na **wartość tak** lub **nie**.

### <a name="alternate-approvers"></a>Alternatywne osoby zatwierdzające

Można określić alternatywne osoby zatwierdzające podobne do określenia pierwszego i drugiego zatwierdzenia, które mogą zatwierdzać żądania. Posiadanie zastępców osób zatwierdzających zagwarantuje, że żądania są zatwierdzane lub odrzucane przed wygaśnięciem (limit czasu). Można wyświetlić listę zastępczych osób zatwierdzających pierwszej osoby zatwierdzającej i drugiej osoby zatwierdzającej na potrzeby zatwierdzania dwuetapowego. 

Określając alternatywne osoby zatwierdzające, w przypadku, gdy pierwszej lub drugiej osoby zatwierdzającej nie mogły zatwierdzić lub odrzucić żądania, oczekujące żądanie zostanie przekazane do alternatywnej osoby zatwierdzającej zgodnie z harmonogramem przekazywania określonym podczas konfigurowania zasad. Otrzymają wiadomość e-mail z prośbą o zatwierdzenie lub odrzucenie oczekującego żądania.

Po przejściu żądania do zastępców osoby zatwierdzające mogą nadal zatwierdzać lub odrzucać żądania. Alternatywne osoby zatwierdzające używają tej samej witryny z dostępem w celu zatwierdzenia lub odrzucenia oczekującego żądania.

Możemy wyświetlić listę osób lub grup osób, które mają być osobami zatwierdzającymi i alternatywnymi osobami zatwierdzającymi. Upewnij się, że podajesz listę różnych zestawów osób jako pierwszej, drugiej i alternatywnej osoby zatwierdzającej.
Na przykład, jeśli w pierwszej osoby zatwierdzającej wystawiłeś Alicja i Robert, lista Karoliny i Dave jako alternatywne osoby zatwierdzające. Wykonaj następujące kroki, aby dodać alternatywne osoby zatwierdzające do pakietu dostępu:

1. W obszarze pierwszej osoby zatwierdzającej, drugiej osoby zatwierdzającej lub obu, kliknij przycisk **Pokaż zaawansowane ustawienia żądania**.

    ![Dostęp do pakietu-zasady-Pokaż zaawansowane ustawienia żądania](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Ustaw **, jeśli nie wykonano żadnej akcji, przechodź do innej osoby zatwierdzającej?** Przełącz na **wartość tak**.

1. Kliknij przycisk **Dodaj alternatywne osoby zatwierdzające** i wybierz z listy alternatywne osoby zatwierdzające.

    ![Dostęp do pakietu — zasady Dodaj alternatywne osoby zatwierdzające](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. W polu **Przekaż do alternatywnych osób zatwierdzających po** liczbie dni wprowadź liczbę dni, przez które osoby zatwierdzające muszą zatwierdzić lub odrzucić żądanie. Jeśli żadna osoba zatwierdzająca nie zatwierdziła lub nie odrzuciła żądania przed upływem czasu żądania, żądanie wygasa (timeout), a użytkownik będzie musiał przesłać kolejne żądanie dotyczące pakietu dostępu. 

    Żądania mogą być przekazywane do alternatywnych osób zatwierdzających dziennie po upływie czasu trwania żądania, a decyzja głównych osób zatwierdzających musi przekroczyć limit czasu po upływie co najmniej 4 dni. Jeśli limit czasu żądania jest mniejszy lub równy 3, nie jest wystarczająco dużo czasu na przesłanie żądania do alternatywnej osoby zatwierdzającej. W tym przykładzie czas trwania żądania wynosi 14 dni. W związku z tym czas trwania żądania osiągnie połowę okresu ważności w dniu 7. Dlatego nie można przesłać dalej żądania przed dniem 8. Ponadto żądania nie mogą być przekazywane w ostatnim dniu czasu trwania żądania. W tym przykładzie najnowsze żądanie może być przekazywane z dniem 13.

## <a name="enable-requests"></a>Włącz żądania

1. Jeśli chcesz, aby pakiet dostępu był natychmiast dostępny dla użytkowników w zasadach żądania, przesuń przełącznik Enable na **wartość tak**.

    Zawsze możesz włączyć ją w przyszłości po zakończeniu tworzenia pakietu dostępu.

    W przypadku wybrania opcji **Brak (tylko przypisania bezpośrednie do administratorów)** i skonfigurowania opcji Włącz na **nie**Administratorzy nie mogą bezpośrednio przypisywać tego pakietu dostępu.

    ![Dostęp do pakietu — ustawienie zasad włączania zasad](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Kliknij przycisk **Dalej**.

## <a name="add-requestor-information-preview-to-an-access-package"></a>Dodawanie informacji o żądającym (wersja zapoznawcza) do pakietu dostępu

1. Przejdź do karty **Informacje o żądającym** i kliknij kartę **pytania** podrzędne.
 
1. Wpisz tekst, w którym chcesz zadać żądanie, znanego również jako ciąg wyświetlania, dla pytania w polu **pytania** .

    ![Dostęp — ustawienie informacji na temat żądania włączenia zasad](./media/active-directory-entitlement-management-request-policy/add-requestor-info-question.png)

1. Jeśli chcesz dodać własne opcje lokalizacji, kliknij pozycję **Dodaj lokalizację**.
    1. W okienku **Dodaj lokalizacje dla pytania** wybierz **kod języka** dla języka, w którym ma zostać zlokalizowane pytanie.
    1. W skonfigurowanym języku wpisz pytanie w **zlokalizowanym polu tekstowym** .
    1. Po dodaniu wszystkich wymaganych lokalizacji kliknij przycisk **Zapisz**.

    ![Dostęp do pakietu — zasady — Konfigurowanie zlokalizowanego tekstu](./media/active-directory-entitlement-management-request-policy/add-localization-question.png)

1. Wybierz **format odpowiedzi** , w którym chcesz odpowiedzieć żądającym. Formaty odpowiedzi obejmują: *krótki tekst*, *wielokrotne wybór*i *długi tekst*.
 
    ![Dostęp do pakietu — zasady — wybierz pozycję Wyświetl i Edytuj format odpowiedzi z wieloma opcjami](./media/active-directory-entitlement-management-request-policy/answer-format-view-edit.png)
 
1. W przypadku wybrania wielu opcji kliknij przycisk **Wyświetl i edytuj** , aby skonfigurować opcje odpowiedzi.
    1. Po wybraniu opcji Wyświetl i edytuj okienko **pytania Wyświetl/Edytuj** zostanie otwarte.
    1. Wpisz opcje odpowiedzi, które chcesz nadać żądającemu w odpowiedzi na pytanie w polach **wartości odpowiedzi** .
    1. Wpisz tyle odpowiedzi, ile potrzebujesz, a następnie kliknij przycisk **Zapisz**.
    
    ![Dostęp do pakietu — zasady — wprowadzanie wielu opcji wyboru](./media/active-directory-entitlement-management-request-policy/answer-multiple-choice.png)
  
1. Aby wymagać od zleceniodawców odpowiedzi na to pytanie podczas żądania dostępu do pakietu dostępu, kliknij pole wyboru w obszarze **wymagane**.

1. Kliknij przycisk Dalej
