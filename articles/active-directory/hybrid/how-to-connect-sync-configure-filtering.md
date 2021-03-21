---
title: 'Azure AD Connect Sync: Konfigurowanie filtrowania | Microsoft Docs'
description: Wyjaśnia, jak skonfigurować filtrowanie w Azure AD Connect synchronizacji.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 595cf2c1dbc105634d33b426c67e5123b9751e6e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996546"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Synchronizacja programu Azure AD Connect: konfigurowanie filtrowania
Korzystając z funkcji filtrowania, można kontrolować, które obiekty są wyświetlane w Azure Active Directory (Azure AD) z katalogu lokalnego. Konfiguracja domyślna pobiera wszystkie obiekty we wszystkich domenach w skonfigurowanych lasach. Ogólnie rzecz biorąc jest to zalecana konfiguracja. Użytkownicy korzystający z obciążeń Microsoft 365, takich jak Exchange Online i Skype dla firm, korzystają z kompletnej globalnej listy adresów, aby mogli wysyłać wiadomości e-mail i wywoływać wszystkich użytkowników. W przypadku konfiguracji domyślnej mogą one korzystać z tego samego środowiska z lokalną implementacją programu Exchange lub Lync.

W niektórych przypadkach wymagane jest wprowadzenie pewnych zmian w konfiguracji domyślnej. Oto kilka przykładów:

* Planujesz użycie [topologii katalogu usługi AD systemu Azure](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Następnie należy zastosować filtr w celu kontrolowania, które obiekty są synchronizowane z określonym katalogiem usługi Azure AD.
* Uruchamiasz program pilotażowy dla systemu Azure lub Microsoft 365 i potrzebujesz tylko podzbioru użytkowników w usłudze Azure AD. W małych pilotażach nie ma konieczności posiadania kompletnej globalnej listy adresów, aby zademonstrować tę funkcjonalność.
* Masz wiele kont usług i innych kont nienależących do użytkownika, które nie są potrzebne w usłudze Azure AD.
* Ze względów zgodności nie należy usuwać żadnych kont użytkowników lokalnie. Można je wyłączyć. Jednak w usłudze Azure AD chcesz mieć tylko aktywne konta.

W tym artykule opisano sposób konfigurowania różnych metod filtrowania.

> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikowania ani działania synchronizacji programu Azure AD Connect poza akcjami, które zostały formalnie udokumentowane. Każda z tych akcji może skutkować niespójnością lub nieobsługiwanym stanem synchronizacji Azure AD Connect. W związku z tym firma Microsoft nie może zapewnić pomocy technicznej dotyczącej takich wdrożeń.

## <a name="basics-and-important-notes"></a>Podstawowe i ważne uwagi
W przypadku synchronizacji Azure AD Connect można włączyć filtrowanie w dowolnym momencie. Jeśli zaczniesz od domyślnej konfiguracji synchronizacji katalogów, a następnie skonfigurujesz filtrowanie, obiekty, które są odfiltrowane, nie są już synchronizowane z usługą Azure AD. Ze względu na tę zmianę wszystkie obiekty w usłudze Azure AD, które zostały wcześniej zsynchronizowane, ale zostały następnie odfiltrowane, są usuwane w usłudze Azure AD.

Przed rozpoczęciem wprowadzania zmian w filtrowaniu upewnij się, że zostało [wyłączone zaplanowane zadanie](#disable-the-scheduled-task) , aby nie eksportować zmian, które nie zostały jeszcze zweryfikowane, aby były poprawne.

Ponieważ filtrowanie może usunąć wiele obiektów w tym samym czasie, przed rozpoczęciem eksportowania zmian do usługi Azure AD upewnij się, że nowe filtry są poprawne. Po wykonaniu kroków konfiguracyjnych zdecydowanie zalecamy wykonanie [kroków weryfikacji](#apply-and-verify-changes) przed wyeksportowaniem i wprowadzeniem zmian w usłudze Azure AD.

Aby chronić przed przypadkowym usunięciem wielu obiektów, funkcja "[Zapobiegaj przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md)" jest domyślnie włączona. Jeśli usuniesz wiele obiektów ze względu na filtrowanie (domyślnie 500), musisz wykonać kroki opisane w tym artykule, aby umożliwić usuwanie w usłudze Azure AD.

Jeśli używasz kompilacji przed listopadem 2015 ([1.0.9125](reference-connect-version-history.md)), wprowadź zmiany w konfiguracji filtru i użyj synchronizacji skrótów haseł, a następnie musisz wyzwolić pełną synchronizację wszystkich haseł po zakończeniu konfiguracji. Aby dowiedzieć się, jak wyzwolić pełną synchronizację hasła, zobacz [wyzwalanie pełnej synchronizacji wszystkich haseł](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Jeśli korzystasz z kompilacji 1.0.9125 lub nowszej, zwykła Akcja **pełna synchronizacja** oblicza również, czy hasła powinny być zsynchronizowane i czy ten dodatkowy krok nie jest już wymagany.

Jeśli obiekty **użytkownika** zostały przypadkowo usunięte w usłudze Azure AD z powodu błędu filtrowania, możesz ponownie utworzyć obiekty użytkownika w usłudze Azure AD, usuwając konfiguracje filtrowania. Następnie można zsynchronizować katalogi ponownie. Ta akcja spowoduje przywrócenie użytkowników z Kosza w usłudze Azure AD. Nie można jednak cofnąć usunięcia innych typów obiektów. Jeśli na przykład przypadkowo usuniesz grupę zabezpieczeń, która została użyta do zasobu listy ACL, nie można odzyskać grupy i jej list ACL.

Azure AD Connect usuwa tylko te obiekty, które zostały uznane za należące do zakresu. Jeśli istnieją obiekty w usłudze Azure AD, które zostały utworzone przez inny aparat synchronizacji i te obiekty nie znajdują się w zakresie, dodanie filtrowania nie powoduje jego usunięcia. Na przykład, jeśli zaczniesz od serwera DirSync, który utworzył kompletną kopię całego katalogu w usłudze Azure AD, i instalujesz nowy serwer synchronizacji Azure AD Connect równolegle z włączonym filtrowaniem, Azure AD Connect nie usunie dodatkowych obiektów utworzonych przez narzędzie DirSync.

Konfiguracja filtrowania jest zachowywana podczas instalowania lub uaktualniania do nowszej wersji Azure AD Connect. Najlepszym rozwiązaniem jest upewnienie się, że konfiguracja nie została przypadkowo zmieniona po uaktualnieniu do nowszej wersji przed uruchomieniem pierwszego cyklu synchronizacji.

Jeśli masz więcej niż jeden las, należy zastosować konfiguracje filtrowania, które są opisane w tym temacie, do każdego lasu (przy założeniu, że dla wszystkich z nich ma być taka sama konfiguracja).

### <a name="disable-the-scheduled-task"></a>Wyłącz zaplanowane zadanie
Aby wyłączyć wbudowany harmonogram wyzwalający cykl synchronizacji co 30 minut, wykonaj następujące czynności:

1. Przejdź do wiersza polecenia programu PowerShell.
2. Uruchom, `Set-ADSyncScheduler -SyncCycleEnabled $False` Aby wyłączyć harmonogram.
3. Wprowadź zmiany, które zostały opisane w tym artykule.
4. Uruchom, `Set-ADSyncScheduler -SyncCycleEnabled $True` Aby ponownie włączyć harmonogram.

**Jeśli używasz kompilacji Azure AD Connect przed 1.1.105.0**  
Aby wyłączyć zaplanowane zadanie wyzwalające cykl synchronizacji co trzy godziny, wykonaj następujące czynności:

1. Rozpocznij **harmonogram zadań** z menu **Start** .
2. Bezpośrednio w obszarze **biblioteka harmonogram zadań** Znajdź zadanie o nazwie **Harmonogram Azure AD Sync**, kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Wyłącz**.  
   ![Harmonogram zadań](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Teraz można wprowadzać zmiany konfiguracji i uruchamiać aparat synchronizacji ręcznie z poziomu konsoli **Synchronization Service Manager** .

Po zakończeniu wszystkich zmian filtrowania nie zapomnij wrócić i ponownie **włączyć** zadanie.

## <a name="filtering-options"></a>Opcje filtrowania
Do narzędzia synchronizacji katalogów można zastosować następujące typy konfiguracji filtrowania:

* [**Oparte na grupach**](#group-based-filtering): filtrowanie na podstawie pojedynczej grupy można skonfigurować tylko podczas instalacji początkowej przy użyciu Kreatora instalacji.
* [**Oparte na domenie**](#domain-based-filtering): korzystając z tej opcji, można wybrać, które domeny mają być synchronizowane z usługą Azure AD. Możesz również dodawać i usuwać domeny z konfiguracji aparatu synchronizacji po wprowadzeniu zmian w infrastrukturze lokalnej po zainstalowaniu synchronizacji Azure AD Connect.
* [**Jednostka organizacyjna — oparta na**](#organizational-unitbased-filtering): przy użyciu tej opcji można wybrać, które jednostki organizacyjne są synchronizowane z usługą Azure AD. Ta opcja dotyczy wszystkich typów obiektów w wybranych jednostkach organizacyjnych.
* [**Oparte na atrybutach**](#attribute-based-filtering): za pomocą tej opcji można filtrować obiekty na podstawie wartości atrybutów obiektów. Można również mieć różne filtry dla różnych typów obiektów.

Można użyć wielu opcji filtrowania jednocześnie. Na przykład można użyć filtrowania opartego na jednostce organizacyjnej, aby uwzględnić tylko obiekty w jednej jednostce organizacyjnej. W tym samym czasie można użyć filtrowania opartego na atrybutach, aby dodatkowo odfiltrować obiekty. W przypadku korzystania z wielu metod filtrowania filtry używają logicznego "i" między filtrami.

## <a name="domain-based-filtering"></a>Filtrowanie oparte na domenie
Ta sekcja zawiera instrukcje konfigurowania filtru domeny. W przypadku dodania lub usunięcia domen w lesie po zainstalowaniu Azure AD Connect należy również zaktualizować konfigurację filtrowania.

Preferowanym sposobem zmiany filtrowania opartego na domenie jest uruchomienie Kreatora instalacji i zmiana [filtrowania domen i jednostek organizacyjnych](how-to-connect-install-custom.md#domain-and-ou-filtering). Kreator instalacji automatyzuje wszystkie zadania opisane w tym temacie.

Należy wykonać następujące czynności tylko wtedy, gdy z jakiegoś powodu nie można uruchomić Kreatora instalacji.

Konfiguracja filtrowania oparta na domenie obejmuje następujące kroki:

1. Wybierz domeny, które chcesz dołączyć do synchronizacji.
2. Dla każdej dodanej i usuniętej domeny Dopasuj profile uruchamiania.
3. [Zastosuj i Sprawdź zmiany](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Wybierz domeny do zsynchronizowania
Istnieją dwa sposoby wybierania domen do zsynchronizowania:
    - Korzystanie z usługi synchronizacji
    - Korzystanie z Kreatora Azure AD Connect.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Wybierz domeny, które mają być synchronizowane przy użyciu usługi synchronizacji
Aby ustawić filtr domeny, wykonaj następujące czynności:

1. Zaloguj się na serwerze z uruchomioną Azure AD Connect synchronizacji przy użyciu konta, które jest członkiem grupy zabezpieczeń **ADSyncAdmins** .
2. Uruchom **usługę synchronizacji** z menu **Start** .
3. Wybierz pozycję **Łączniki**, a następnie na liście **Łączniki** wybierz łącznik z typem **Active Directory Domain Services**. W obszarze **Akcje** wybierz pozycję **Właściwości**.  
   ![Właściwości łącznika](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Kliknij pozycję **Konfiguruj partycje katalogu**.
5. Z listy **Wybierz partycje katalogów** wybierz i usuń zaznaczenie domen zgodnie z wymaganiami. Sprawdź, czy wybrano tylko partycje, które chcesz synchronizować.  
   ![Zrzut ekranu pokazujący partycje katalogu w oknie "właściwości".](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Jeśli została zmieniona lokalna infrastruktura Active Directory i dodano lub usunięto domeny z lasu, kliknij przycisk **Odśwież** , aby wyświetlić zaktualizowaną listę. Po odświeżeniu zostanie wyświetlony monit o podanie poświadczeń. Podaj wszelkie poświadczenia z dostępem do odczytu do systemu Windows Server Active Directory. Nie musi to być użytkownik, który jest wstępnie wypełniony w oknie dialogowym.  
   ![Wymagana odświeżenie](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Gdy skończysz, Zamknij okno dialogowe **Właściwości** , klikając przycisk **OK**. W przypadku usunięcia domen z lasu zostanie wyświetlony komunikat z informacją, że domena została usunięta i że konfiguracja zostanie oczyszczona.
7. Kontynuuj dostosowywanie profilów uruchamiania.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Wybierz domeny do zsynchronizowania za pomocą Kreatora Azure AD Connect
Aby ustawić filtr domeny, wykonaj następujące czynności:

1.  Uruchom Kreatora Azure AD Connect
2.  Kliknij przycisk **Konfiguruj**.
3.  Wybierz pozycję **Dostosuj opcje synchronizacji** , a następnie kliknij przycisk **dalej**.
4.  Wprowadzanie poświadczeń usługi Azure AD
5.  Na ekranie **podłączone katalogi** kliknij przycisk **dalej**.
6.  Na **stronie filtrowanie domen i jednostek organizacyjnych** kliknij przycisk **Odśwież**.  Zostaną wyświetlone nowe domeny, a usunięte domeny znikną.
   ![Partycje](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Aktualizowanie profilów uruchamiania
Jeśli filtr domeny został zaktualizowany, należy również zaktualizować profile uruchamiania.

1. Upewnij się, że na liście **łączników** została wybrana wartość łącznika, który został zmieniony w poprzednim kroku. W obszarze **Akcje** wybierz pozycję **Konfiguruj profile uruchamiania**.  
   ![Profile uruchamiania łącznika 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Znajdź i zidentyfikuj następujące profile:
    * Pełny import
    * Pełna synchronizacja
    * Import zmian
    * Synchronizacja zmian
    * Eksportowanie
3. Dla każdego profilu Dostosuj **dodane** i **usunięte** domeny.
    1. Dla każdego z pięciu profilów wykonaj następujące kroki dla każdej **dodanej** domeny:
        1. Wybierz profil uruchomienia, a następnie kliknij przycisk **nowy krok**.
        2. Na stronie **Konfiguruj krok** z menu rozwijanego **Typ** wybierz typ kroku o takiej samej nazwie jak konfigurowany profil. Następnie kliknij przycisk **Dalej**.  
        ![Profile uruchamiania łączników 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Na stronie **Konfiguracja łącznika** w menu rozwijanym **partycji** wybierz nazwę domeny, która została dodana do filtru domeny.  
        ![Profile uruchamiania łączników 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Aby zamknąć okno dialogowe **Konfigurowanie profilu uruchamiania** , kliknij przycisk **Zakończ**.
    2. Dla każdego z pięciu profilów wykonaj następujące czynności dla każdej **usuniętej** domeny:
        1. Wybierz profil uruchomienia.
        2. Jeśli **wartość** atrybutu **partycji** jest identyfikatorem GUID, wybierz krok Uruchom i kliknij polecenie **Usuń krok**.  
        ![Profile uruchamiania łączników 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Sprawdź zmianę. Każda domena, która ma zostać zsynchronizowana, powinna być wymieniona jako krok w każdym profilu przebiegu.
4. Aby zamknąć okno dialogowe **Konfigurowanie profili uruchamiania** , kliknij przycisk **OK**.
5.  Aby ukończyć konfigurację, należy uruchomić **pełny import** i **synchronizację Delta**. Kontynuuj odczytywanie sekcji [stosowanie i weryfikowanie zmian](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtrowanie na podstawie jednostki organizacyjnej
Preferowanym sposobem zmiany filtrowania opartego na jednostce organizacyjnej jest uruchomienie Kreatora instalacji i zmiana [filtrowania domen i jednostek organizacyjnych](how-to-connect-install-custom.md#domain-and-ou-filtering). Kreator instalacji automatyzuje wszystkie zadania opisane w tym temacie.

Należy wykonać następujące czynności tylko wtedy, gdy z jakiegoś powodu nie można uruchomić Kreatora instalacji.

Aby skonfigurować filtrowanie na podstawie jednostki organizacyjnej, wykonaj następujące czynności:

1. Zaloguj się na serwerze z uruchomioną Azure AD Connect synchronizacji przy użyciu konta, które jest członkiem grupy zabezpieczeń **ADSyncAdmins** .
2. Uruchom **usługę synchronizacji** z menu **Start** .
3. Wybierz pozycję **Łączniki**, a następnie na liście **Łączniki** wybierz łącznik z typem **Active Directory Domain Services**. W obszarze **Akcje** wybierz pozycję **Właściwości**.  
   ![Właściwości łącznika](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Kliknij pozycję **Konfiguruj partycje katalogu**, wybierz domenę, którą chcesz skonfigurować, a następnie kliknij pozycję **kontenery**.
5. Po wyświetleniu monitu podaj poświadczenia z dostępem do odczytu do lokalnego Active Directory. Nie musi to być użytkownik, który jest wstępnie wypełniony w oknie dialogowym.
6. W oknie dialogowym **Wybieranie kontenerów** Wyczyść jednostki organizacyjne, które nie mają być synchronizowane z katalogiem w chmurze, a następnie kliknij przycisk **OK**.  
   ![Jednostki organizacyjne w oknie dialogowym Wybierz kontenery](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * Aby komputery z systemem Windows 10 zostały pomyślnie zsynchronizowane z usługą Azure AD, należy wybrać kontener **komputery** . Jeśli komputery przyłączone do domeny znajdują się w innych jednostkach organizacyjnych, upewnij się, że są zaznaczone.
   * Należy wybrać kontener **ForeignSecurityPrincipals** w przypadku używania wielu lasów z relacjami zaufania. Ten kontener umożliwia rozpoznanie członkostwa w grupie zabezpieczeń obejmującej wiele lasów.
   * Jeśli włączono funkcję zapisywania zwrotnego urządzeń, należy wybrać jednostkę organizacyjną **RegisteredDevices** . Jeśli używasz innej funkcji zapisywania zwrotnego, takiej jak zapisywanie zwrotne grup, upewnij się, że te lokalizacje są zaznaczone.
   * Wybierz dowolną inną jednostkę organizacyjną, w której znajdują się użytkownicy, obiekty iNetOrgPerson, grupy, kontakty i komputery. Na obrazie wszystkie te jednostki organizacyjne znajdują się w jednostce organizacyjnej ManagedObjects.
   * Jeśli używasz filtrowania opartego na grupach, jednostka organizacyjna, w której znajduje się grupa, musi być uwzględniona.
   * Należy pamiętać, że można określić, czy nowe jednostki organizacyjne, które są dodawane po zakończeniu konfiguracji filtrowania, są synchronizowane lub niezsynchronizowane. Szczegółowe informacje są podane w następnej sekcji.
7. Gdy skończysz, Zamknij okno dialogowe **Właściwości** , klikając przycisk **OK**.
8. Aby ukończyć konfigurację, należy uruchomić **pełny import** i **synchronizację Delta**. Kontynuuj odczytywanie sekcji [stosowanie i weryfikowanie zmian](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Synchronizuj nowe jednostki organizacyjne
Nowe jednostki organizacyjne, które są tworzone po filtrowaniu, są domyślnie synchronizowane. Ten stan jest wskazywany przez zaznaczone pole wyboru. Można również usunąć zaznaczenie niektórych podrzędnych jednostek organizacyjnych. Aby uzyskać takie zachowanie, kliknij pole do momentu, aż stanie się biały z niebieskim znacznikiem wyboru (jego stan domyślny). Następnie usuń zaznaczenie wszystkich podrzędnych jednostek organizacyjnych, które nie mają być synchronizowane.

Jeśli wszystkie podjednostki organizacyjne są zsynchronizowane, pole jest białe z niebieskim znacznikiem wyboru.  
![Jednostka organizacyjna z wybranymi wszystkimi polami](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Jeśli niektóre podjednostki organizacyjne nie zostały zaznaczone, to pole jest szare z białym znacznikiem wyboru.  
![Jednostka organizacyjna z niezaznaczonymi podrzędnymi jednostkami organizacyjnymi](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

W przypadku tej konfiguracji jest synchronizowana nowa jednostka organizacyjna utworzona w obszarze ManagedObjects.

Kreator instalacji Azure AD Connect zawsze tworzy tę konfigurację.

### <a name="dont-synchronize-new-ous"></a>Nie Synchronizuj nowych jednostek organizacyjnych
Aparat synchronizacji można skonfigurować tak, aby nie synchronizować nowych jednostek organizacyjnych po zakończeniu konfiguracji filtrowania. Ten stan jest wskazany w interfejsie użytkownika przez pole, w którym jest wyświetlany kolor szary bez znacznika wyboru. Aby uzyskać takie zachowanie, kliknij pole do momentu, gdy zostanie ono białe bez znacznika wyboru. Następnie wybierz podrzędne jednostki organizacyjne, które chcesz synchronizować.

![Jednostka organizacyjna z niezaznaczonym elementem głównym](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

W przypadku tej konfiguracji nowa jednostka organizacyjna utworzona w obszarze ManagedObjects nie jest zsynchronizowana.

## <a name="attribute-based-filtering"></a>Filtrowanie oparte na atrybutach
Upewnij się, że korzystasz z 2015 listopada ([1.0.9125](reference-connect-version-history.md)) lub późniejszej kompilacji, aby wykonać te kroki.

> [!IMPORTANT]
>Firma Microsoft zaleca, aby nie modyfikować reguł domyślnych utworzonych przez **Azure AD Connect**. Jeśli chcesz zmodyfikować regułę, Sklonuj ją i Wyłącz pierwotną regułę. Wprowadź zmiany w sklonowanej regule. Należy pamiętać, że przez wykonanie tej czynności (wyłączenie oryginalnej reguły) spowoduje to pominięcie wszelkich poprawek lub funkcji włączonych w ramach tej reguły.

Filtrowanie na podstawie atrybutu to najbardziej elastyczny sposób filtrowania obiektów. Możesz użyć mocy [deklaracyjnej aprowizacji](concept-azure-ad-connect-sync-declarative-provisioning.md) , aby kontrolować niemal każdy aspekt, gdy obiekt jest synchronizowany z usługą Azure AD.

Filtrowanie [przychodzące](#inbound-filtering) można zastosować z Active Directory do magazynu Metaverse oraz filtrowanie [wychodzące](#outbound-filtering) z METAVERSE do usługi Azure AD. Zalecamy stosowanie filtrowania przychodzącego, ponieważ jest to najłatwiejsze w obsłudze. Filtrowanie wychodzące powinno być używane tylko wtedy, gdy wymagane jest dołączenie obiektów z więcej niż jednego lasu, aby można było przeprowadzić ocenę.

### <a name="inbound-filtering"></a>Filtrowanie przychodzące
Filtrowanie przychodzące używa konfiguracji domyślnej, w której obiekty przechodzące do usługi Azure AD muszą mieć atrybut Metaverse cloudFiltered nie jest ustawiony na wartość do zsynchronizowania. Jeśli wartość tego atrybutu jest równa **true**, obiekt nie jest synchronizowany. Nie powinna być ustawiona na **wartość false**, przez zaprojektowanie. Aby upewnić się, że inne reguły mają możliwość współtworzenia wartości, ten atrybut ma tylko **wartość PRAWDA** lub **wartość null** (brak).

W przypadku filtrowania przychodzącego można użyć możliwości **zakresu** , aby określić, które obiekty mają być synchronizowane lub niezsynchronizowane. Jest to miejsce, w którym wprowadzane są zmiany spełniające wymagania organizacji. Moduł zakresu ma **grupę** i **klauzulę** , aby określić, kiedy reguła synchronizacji znajduje się w zakresie. Grupa zawiera jedną lub wiele klauzul. Istnieje koniunkcja logiczna "i" między wieloma klauzulami, a logiczna "OR" między wieloma grupami.

Poczekaj na przykład:  
![Zrzut ekranu przedstawiający przykład dodawania filtrów zakresu.](./media/how-to-connect-sync-configure-filtering/scope.png)  
Powinno to być odczytane jako **(dział = IT) lub (Department = Sales i c = US)**.

W poniższych przykładach i krokach użyto obiektu użytkownika, ale można go użyć dla wszystkich typów obiektów.

W poniższych przykładach wartość pierwszeństwa rozpoczyna się od 50. Może to być dowolny numer nieużywany, ale powinien być niższy niż 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Filtrowanie ujemne: "nie Synchronizuj"
W poniższym przykładzie można odfiltrować (nie synchronizować) wszystkich użytkowników, których **extensionAttribute15** ma wartość **nosync**.

1. Zaloguj się na serwerze z uruchomioną Azure AD Connect synchronizacji przy użyciu konta, które jest członkiem grupy zabezpieczeń **ADSyncAdmins** .
2. Uruchom **Edytor reguł synchronizacji** z menu **Start** .
3. Upewnij się, że wybrano pozycję **przychodzące** , a następnie kliknij pozycję **Dodaj nową regułę**.
4. Nadaj regule nazwę opisową, taką jak "*w programie AD — User DoNotSyncFilter*". Wybierz odpowiedni Las, wybierz pozycję **użytkownik** jako **Typ obiektu CS**, a następnie wybierz pozycję **osoba** jako **Typ obiektu MV**. W obszarze **Typ łącza** wybierz pozycję **Dołącz**. W obszarze **pierwszeństwo** wpisz wartość, która nie jest obecnie używana przez inną regułę synchronizacji (na przykład 50), a następnie kliknij przycisk **dalej**.  
   ![Opis przychodzących 1](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. W obszarze **Filtr określania zakresu** kliknij pozycję **Dodaj grupę**, a następnie kliknij pozycję **Dodaj klauzulę**. W polu **atrybut** wybierz pozycję **ExtensionAttribute15**. Upewnij się, że **operator** jest ustawiony na wartość **równe**, a w polu **wartość** wpisz wartości **nosync** . Kliknij przycisk **Dalej**.  
   ![Zakres ruchu przychodzącego 2](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Pozostaw puste reguły **sprzężenia** , a następnie kliknij przycisk **dalej**.
7. Kliknij pozycję **Dodaj transformację**, wybierz pozycję **flowtype** jako **stałą**, a następnie wybierz pozycję **cloudFiltered** jako **atrybut docelowy**. W polu tekstowym **Źródło** wpisz **true**. Kliknij przycisk **Dodaj** , aby zapisać regułę.  
   ![Transformacje przychodzące 3](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Aby ukończyć konfigurację, należy przeprowadzić **pełną synchronizację**. Kontynuuj odczytywanie sekcji [stosowanie i weryfikowanie zmian](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Filtrowanie pozytywne: "Synchronizuj tylko te"
Wyrażanie pozytywnego filtrowania może być trudniejsze, ponieważ należy również rozważyć obiekty, które nie są oczywiste do zsynchronizowania, takie jak pokoje konferencyjne. Należy również przesłonić domyślny filtr w regule "out-of-Box" **w temacie from AD-User Join**. Podczas tworzenia niestandardowego filtru upewnij się, że nie obejmują obiektów systemu krytycznego, obiektów powodujących konflikty replikacji, specjalnych skrzynek pocztowych i kont usługi dla Azure AD Connect.

Opcja filtrowania pozytywnego wymaga dwóch reguł synchronizacji. Wymagana jest jedna reguła (lub kilka) z prawidłowym zakresem obiektów do zsynchronizowania. Potrzebna jest także druga reguła synchronizacji dla wszystkich obiektów, która filtruje wszystkie obiekty, które nie zostały jeszcze zidentyfikowane jako obiekt, który powinien zostać zsynchronizowany.

W poniższym przykładzie tylko zsynchronizujesz obiekty użytkownika, w których atrybut działu ma wartość **Sales**.

1. Zaloguj się na serwerze z uruchomioną Azure AD Connect synchronizacji przy użyciu konta, które jest członkiem grupy zabezpieczeń **ADSyncAdmins** .
2. Uruchom **Edytor reguł synchronizacji** z menu **Start** .
3. Upewnij się, że wybrano pozycję **przychodzące** , a następnie kliknij pozycję **Dodaj nową regułę**.
4. Nadaj regule nazwę opisową, taką jak "*w programie AD — synchronizacja sprzedaży użytkownika*". Wybierz odpowiedni Las, wybierz pozycję **użytkownik** jako **Typ obiektu CS**, a następnie wybierz pozycję **osoba** jako **Typ obiektu MV**. W obszarze **Typ łącza** wybierz pozycję **Dołącz**. W obszarze **pierwszeństwo** wpisz wartość, która nie jest obecnie używana przez inną regułę synchronizacji (na przykład 51), a następnie kliknij przycisk **dalej**.  
   ![Opis przychodzący 4](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. W obszarze **Filtr określania zakresu** kliknij pozycję **Dodaj grupę**, a następnie kliknij pozycję **Dodaj klauzulę**. W polu **atrybut** wybierz pozycję **dział**. Upewnij się, że operator ma ustawioną wartość **równą** i wpisz wartości **Sales** w polu **wartość** . Kliknij przycisk **Dalej**.  
   ![Zakres ruchu przychodzącego 5](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Pozostaw puste reguły **sprzężenia** , a następnie kliknij przycisk **dalej**.
7. Kliknij pozycję **Dodaj transformację**, wybierz pozycję **stała** jako wartość **flowtype**, a następnie wybierz **cloudFiltered** jako **atrybut docelowy**. W polu **Źródło** wpisz **false**. Kliknij przycisk **Dodaj** , aby zapisać regułę.  
   ![Przekształcenie przychodzące 6](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Jest to specjalny przypadek, w którym jawnie ustawiono **wartość false** dla cloudFiltered.
8. Teraz trzeba utworzyć regułę synchronizacji catch-all. Nadaj regule nazwę opisową, taką jak "*w from AD – User catch-all Filter*". Wybierz odpowiedni Las, wybierz pozycję **użytkownik** jako **Typ obiektu CS**, a następnie wybierz pozycję **osoba** jako **Typ obiektu MV**. W obszarze **Typ łącza** wybierz pozycję **Dołącz**. W obszarze **pierwszeństwo** wpisz wartość, która nie jest obecnie używana przez inną regułę synchronizacji (na przykład 99). Wybrano wyższą wartość pierwszeństwa (niższy priorytet) niż reguła synchronizacji poprzedniej. Ale pozostawiono również kilka miejsca, aby można było później dodać kolejne reguły synchronizacji filtrowania podczas synchronizacji dodatkowych działów. Kliknij przycisk **Dalej**.  
   ![Opis ruchu przychodzącego 7](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Pozostaw pusty **Filtr zakresu** , a następnie kliknij przycisk **dalej**. Pusty filtr wskazuje, że reguła ma być stosowana do wszystkich obiektów.
10. Pozostaw puste reguły **sprzężenia** , a następnie kliknij przycisk **dalej**.
11. Kliknij pozycję **Dodaj transformację**, wybierz pozycję **stała** jako wartość **flowtype**, a następnie wybierz pozycję **cloudFiltered** jako **atrybut docelowy**. W polu **Źródło** wpisz **true**. Kliknij przycisk **Dodaj** , aby zapisać regułę.  
    ![Transformacje przychodzące 3](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Aby ukończyć konfigurację, należy przeprowadzić **pełną synchronizację**. Kontynuuj odczytywanie sekcji [stosowanie i weryfikowanie zmian](#apply-and-verify-changes).

Jeśli zachodzi taka potrzeba, można utworzyć więcej reguł pierwszego typu, w których dołączysz więcej obiektów w synchronizacji.

### <a name="outbound-filtering"></a>Filtrowanie wychodzące
W niektórych przypadkach należy wykonać filtrowanie dopiero po przyłączeniu obiektów do Metaverse. Na przykład może być konieczne wyszukanie atrybutu mail z lasu zasobów i atrybutu userPrincipalName z lasu kont, aby określić, czy obiekt powinien być synchronizowany. W takich przypadkach można utworzyć filtrowanie dla reguły ruchu wychodzącego.

W tym przykładzie zmienisz filtrowanie tak, aby były synchronizowane tylko użytkownicy, którzy mają zarówno wiadomość e-mail, jak i element userPrincipalName kończący @contoso.com się na:

1. Zaloguj się na serwerze z uruchomioną Azure AD Connect synchronizacji przy użyciu konta, które jest członkiem grupy zabezpieczeń **ADSyncAdmins** .
2. Uruchom **Edytor reguł synchronizacji** z menu **Start** .
3. W obszarze **Typ zasad** kliknij pozycję **wychodzące**.
4. W zależności od używanej wersji programu Connect można znaleźć regułę o nazwie do usługi **Azure AD — przyłączanie do użytkownika** lub **do zewnątrz do usługi Azure AD — dołączanie użytkownika do SOAInAD**, a następnie kliknij przycisk **Edytuj**.
5. W oknie podręcznym odpowiedź **tak** , aby utworzyć kopię reguły.
6. Na stronie **Opis** Zmień **pierwszeństwo** na nieużywaną wartość, na przykład 50.
7. Kliknij pozycję **Filtr zakresu** w obszarze nawigacji po lewej stronie, a następnie kliknij pozycję **Dodaj klauzulę**. W polu **atrybut** wybierz opcję **poczta**. W **operatorze** wybierz pozycję **ENDSWITH**. W **wartość**, wpisz **\@ contoso.com**, a następnie kliknij przycisk **Dodaj klauzulę**. W polu **atrybut** wybierz element **userPrincipalName**. W **operatorze** wybierz pozycję **ENDSWITH**. W polu **wartość** wpisz **\@ contoso.com**.
8. Kliknij pozycję **Zapisz**.
9. Aby ukończyć konfigurację, należy przeprowadzić **pełną synchronizację**. Kontynuuj odczytywanie sekcji [stosowanie i weryfikowanie zmian](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Zastosuj i Weryfikuj zmiany
Po wprowadzeniu zmian w konfiguracji należy zastosować je do obiektów, które są już obecne w systemie. Może to być również, że obiekty, które nie są obecnie w aparacie synchronizacji, powinny być przetwarzane (a aparat synchronizacji musi ponownie odczytać system źródłowy, aby zweryfikować jego zawartość).

Jeśli konfiguracja została zmieniona przy użyciu funkcji filtrowania **domeny** lub **jednostki organizacyjnej** , należy wykonać **pełny import**, a następnie **synchronizację Delta**.

W przypadku zmiany konfiguracji przy użyciu funkcji filtrowania **atrybutów** należy wykonać **pełną synchronizację**.

Wykonaj następujące czynności:

1. Uruchom **usługę synchronizacji** z menu **Start** .
2. Wybierz pozycję **Łączniki**. Na liście **Łączniki** wybierz łącznik, w którym została wcześniej wprowadzona zmiana konfiguracji. W obszarze **Akcje** wybierz pozycję **Uruchom**.  
   ![Uruchomienie łącznika](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. W obszarze **Profile uruchamiania** wybierz operację, która została wymieniona w poprzedniej sekcji. Jeśli musisz uruchomić dwie akcje, uruchom sekundę po zakończeniu pierwszego z nich. (Kolumna **stanu** jest **bezczynna** dla wybranego łącznika).

Po synchronizacji wszystkie zmiany zostaną przygotowane do wyeksportowania. Przed faktycznym wprowadzeniem zmian w usłudze Azure AD należy sprawdzić, czy wszystkie te zmiany są poprawne.

1. Uruchom wiersz polecenia i przejdź do `%ProgramFiles%\Microsoft Azure AD Sync\bin` .
2. Uruchom polecenie `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Nazwa łącznika znajduje się w usłudze synchronizacji. Ma nazwę podobną do "contoso.com – Azure AD" dla usługi Azure AD.
3. Uruchom polecenie `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Masz teraz plik w katalogu% Temp% o nazwie export.csv, który można sprawdzić w programie Microsoft Excel. Ten plik zawiera wszystkie zmiany, które mają zostać wyeksportowane.
5. Wprowadź niezbędne zmiany w danych lub konfiguracji, a następnie ponownie wykonaj te kroki (zaimportuj, zsynchronizuj i sprawdź), aż zmiany, które mają zostać wyeksportowane, są oczekiwane.

Gdy skończysz, Eksportuj zmiany do usługi Azure AD.

1. Wybierz pozycję **Łączniki**. Na liście **Łączniki** wybierz pozycję Łącznik usługi Azure AD. W obszarze **Akcje** wybierz pozycję **Uruchom**.
2. W obszarze **Profile uruchamiania** wybierz pozycję **Eksportuj**.
3. Jeśli konfiguracja zmieni się na Usuń wiele obiektów, podczas eksportowania zostanie wyświetlony komunikat o błędzie, gdy liczba jest większa niż skonfigurowany próg (domyślnie 500). W przypadku wyświetlenia tego błędu należy tymczasowo wyłączyć funkcję "[Zapobiegaj przypadkowe usuwanie](how-to-connect-sync-feature-prevent-accidental-deletes.md)".

Teraz można ponownie włączyć harmonogram.

1. Rozpocznij **harmonogram zadań** z menu **Start** .
2. Bezpośrednio w obszarze **biblioteka harmonogram zadań** Znajdź zadanie o nazwie **Harmonogram Azure AD Sync**, kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Włącz**.

## <a name="group-based-filtering"></a>Filtrowanie na podstawie grupy
Filtrowanie oparte na grupach można skonfigurować przy pierwszej instalacji Azure AD Connect przy użyciu [instalacji niestandardowej](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Jest on przeznaczony do wdrożenia pilotażowego, w którym ma być synchronizowany tylko niewielki zestaw obiektów. Wyłączenie filtrowania opartego na grupach nie jest możliwe. Użycie filtrowania opartego na grupach *nie jest obsługiwane* w konfiguracji niestandardowej. Ta funkcja jest obsługiwana tylko przez Kreatora instalacji programu. Po zakończeniu pilotażu należy użyć jednej z innych opcji filtrowania w tym temacie. W przypadku używania filtrowania opartego na jednostce organizacyjnej w połączeniu z filtrowaniem opartym na grupach, należy uwzględnić jednostki organizacyjne, w których znajdują się grupa i jej członkowie.

Podczas synchronizowania wielu lasów usługi AD można skonfigurować filtrowanie na podstawie grup, określając inną grupę dla każdego łącznika usługi AD. Jeśli chcesz synchronizować użytkownika w jednym lesie usługi AD, a ten sam użytkownik ma jeden lub więcej odpowiadających obiektów w innych lasach usługi AD, musisz się upewnić, że obiekt użytkownika i wszystkie odpowiednie obiekty znajdują się w zakresie filtrowania na podstawie grupy. Przykłady:

* Użytkownik ma w jednym lesie, który ma odpowiadający mu obiekt FSP (podmiot zabezpieczeń obcych) w innym lesie. Oba obiekty muszą znajdować się w zakresie filtrowania na podstawie grupy. W przeciwnym razie użytkownik nie będzie synchronizowany z usługą Azure AD.

* Użytkownik znajduje się w jednym lesie, który ma odpowiednie konto zasobu (np. połączoną skrzynkę pocztową) w innym lesie. Dodatkowo skonfigurowano Azure AD Connect do łączenia użytkownika z kontem zasobu. Oba obiekty muszą znajdować się w zakresie filtrowania na podstawie grupy. W przeciwnym razie użytkownik nie będzie synchronizowany z usługą Azure AD.

* Użytkownik znajduje się w jednym lesie, który ma odpowiednią osobę kontaktową poczty w innym lesie. Dodatkowo skonfigurowano Azure AD Connect do łączenia użytkownika z kontaktem z pocztą. Oba obiekty muszą znajdować się w zakresie filtrowania na podstawie grupy. W przeciwnym razie użytkownik nie będzie synchronizowany z usługą Azure AD.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o konfiguracji [synchronizacji Azure AD Connect](how-to-connect-sync-whatis.md) .
- Dowiedz się więcej [na temat integrowania tożsamości lokalnych z usługą Azure AD](whatis-hybrid-identity.md).
