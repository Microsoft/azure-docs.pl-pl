---
title: 'Azure AD Connect Sync: wprowadź zmianę konfiguracji domyślnej'
description: Przeprowadzi Cię przez proces wprowadzania zmian w konfiguracji w Azure AD Connect synchronizacji.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a2036086cfb6da0d7807d4752a5911a358d3c47
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420652"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect Sync: wprowadź zmianę konfiguracji domyślnej
W tym artykule opisano sposób wprowadzania zmian w konfiguracji domyślnej w programie Azure Active Directory (Azure AD) Connect Sync. Zawiera kroki dla niektórych typowych scenariuszy. Korzystając z tej wiedzy, powinno być możliwe wprowadzanie prostych zmian do własnej konfiguracji w oparciu o własne reguły biznesowe.

> [!WARNING]
> Po wprowadzeniu zmian w domyślnych regułach synchronizacji, te zmiany zostaną nadpisywane przy następnym aktualizowaniu Azure AD Connect, co skutkuje nieoczekiwanymi i prawdopodobnie niepożądanymi wynikami synchronizacji.
>
> Domyślne reguły synchronizacji spoza pola mają odcisk palca. Jeśli wprowadzisz zmiany w tych regułach, odcisk palca nie będzie już dopasowywany. Podczas próby zastosowania nowej wersji Azure AD Connect mogą wystąpić problemy w przyszłości. Wprowadzać zmiany tylko w sposób opisany w tym artykule.

## <a name="synchronization-rules-editor"></a>Edytor reguł synchronizacji
Edytor reguł synchronizacji służy do wyświetlania i zmieniania konfiguracji domyślnej. Można go znaleźć w menu **Start** w grupie **Azure AD Connect** .  
![Menu Start z edytorem reguł synchronizacji](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Po otwarciu edytora są wyświetlane domyślne reguły, które są wbudowane.

![Edytor reguł synchronizacji](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Nawigowanie w edytorze
Korzystając z listy rozwijanej w górnej części edytora, można szybko znaleźć określoną regułę. Na przykład jeśli chcesz zobaczyć reguły, w których uwzględniony jest atrybut proxyAddresses, możesz zmienić listę rozwijaną na następujące:  
![Filtrowanie SRE](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Aby zresetować filtrowanie i załadować nową konfigurację, naciśnij klawisz F5 na klawiaturze.

W prawym górnym rogu jest przycisk **Dodaj nową regułę** . Ten przycisk służy do tworzenia własnej reguły niestandardowej.

U dołu są przyciski służące do działania na wybranej regule synchronizacji. **Edytuj** i **Usuń** zrób to, czego oczekują. **Eksport** powoduje utworzenie skryptu programu PowerShell na potrzeby ponownego tworzenia reguły synchronizacji. Za pomocą tej procedury można przenieść regułę synchronizacji z jednego serwera na inny.

## <a name="create-your-first-custom-rule"></a>Tworzenie pierwszej reguły niestandardowej
Najczęstszymi zmianami są przepływy atrybutów. Dane w katalogu źródłowym mogą nie być takie same jak w usłudze Azure AD. W przykładzie w tej sekcji upewnij się, że dana nazwa użytkownika jest zawsze w *odpowiednim przypadku*.

### <a name="disable-the-scheduler"></a>Wyłączanie harmonogramu
[Harmonogram](how-to-connect-sync-feature-scheduler.md) jest domyślnie uruchamiany co 30 minut. Upewnij się, że nie jest on uruchamiany podczas wprowadzania zmian i rozwiązywania problemów dotyczących nowych reguł. Aby tymczasowo wyłączyć harmonogram, uruchom program PowerShell i uruchom polecenie `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![Wyłączanie harmonogramu](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Utwórz regułę
1. Kliknij pozycję **Dodaj nową regułę**.
2. Na stronie **Opis** wprowadź następujące elementy:  
   ![Filtrowanie reguł ruchu przychodzącego](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Nazwa** : nadaj regule nazwę opisową.
   * **Opis** : Udziel pewnej wyjaśnień, aby ktoś inny mógł zrozumieć, co to jest reguła.
   * **Połączony system** : jest to system, w którym można znaleźć obiekt. W takim przypadku wybierz pozycję **łącznik Active Directory**.
   * **Typ połączonego systemu/obiektu metaverse** : wybierz odpowiednio **użytkownika** i **osobę**.
   * **Typ łącza** : Zmień tę wartość, aby **dołączyć**.
   * **Priorytet** : Podaj wartość, która jest unikatowa w systemie. Dolna wartość liczbowa wskazuje wyższy priorytet.
   * **Tag** : pozostaw to pole puste. To pole powinno mieć wartość tylko dla wstępnie utworzonych reguł firmy Microsoft.
3. Na stronie **Filtr zakresu** wprowadź nazwę **ISNOTNULL**.  
   ![Filtr określania zakresu reguł ruchu przychodzącego](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Ta sekcja służy do definiowania obiektów, do których mają być stosowane reguły. Jeśli ta wartość jest pusta, reguła będzie miała zastosowanie do wszystkich obiektów użytkownika. Jednak może to obejmować pokoje konferencyjne, konta usług i inne obiekty użytkownika niebędące użytkownikami.
4. Na stronie **reguły sprzężenia** pozostaw pole puste.
5. Na stronie **przekształcenia** Zmień wartość **flowtype** na **wyrażenie**. Dla **atrybutu Target** wybierz **danąname**. I dla **źródła** wprowadź **PCase ([podaną nazwę])**.
   ![Przekształcenia reguły ruchu przychodzącego](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   W aparacie synchronizacji jest rozróżniana wielkość liter zarówno dla nazwy funkcji, jak i nazwy atrybutu. Jeśli wpiszesz coś niewłaściwego, zobaczysz ostrzeżenie podczas dodawania reguły. Możesz zapisać i kontynuować, ale musisz ponownie otworzyć i poprawić regułę.
6. Kliknij przycisk **Dodaj** , aby zapisać regułę.

Nowa reguła niestandardowa powinna być widoczna z innymi regułami synchronizacji w systemie.

### <a name="verify-the-change"></a>Sprawdź zmianę
Ta nowa zmiana ma na celu upewnienie się, że działa zgodnie z oczekiwaniami i nie zgłasza błędów. W zależności od liczby posiadanych obiektów istnieją dwa sposoby wykonania tego kroku:

- Uruchom pełną synchronizację wszystkich obiektów.
- Uruchom podgląd i pełną synchronizację dla pojedynczego obiektu.

Otwórz **usługę synchronizacji** z menu **Start** . Kroki opisane w tej sekcji znajdują się w tym narzędziu.

**Pełna synchronizacja wszystkich obiektów**  

   1. Wybierz pozycję **Łączniki** u góry. Zidentyfikuj łącznik, który został zmieniony w poprzedniej sekcji (w tym przypadku Active Directory Domain Services) i wybierz go. 
   2. W obszarze **Akcje** wybierz pozycję **Uruchom**.
   3. Wybierz pozycję **pełna synchronizacja** , a następnie wybierz przycisk **OK**.
   ![Pełna synchronizacja](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Obiekty są teraz aktualizowane w magazynie Metaverse. Sprawdź zmiany, przeglądając obiekt w obiekcie Metaverse.

**Podgląd i pełna synchronizacja pojedynczego obiektu**  

   1. Wybierz pozycję **Łączniki** u góry. Zidentyfikuj łącznik, który został zmieniony w poprzedniej sekcji (w tym przypadku Active Directory Domain Services) i wybierz go.
   2. Wybierz pozycję **obszar łącznika wyszukiwania**. 
   3. Użyj **zakresu** , aby znaleźć obiekt, którego chcesz użyć do przetestowania zmiany. Zaznacz obiekt i kliknij przycisk **Podgląd**. 
   4. Na nowym ekranie wybierz pozycję **Podgląd zatwierdzania**.  
   ![Zatwierdź Podgląd](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   Zmiana jest teraz przekazana do obiektu Metaverse.

**Wyświetlanie obiektu w magazynie Metaverse**  

1. Wybierz kilka przykładowych obiektów, aby upewnić się, że wartość jest oczekiwana i że reguła została zastosowana. 
2. Wybierz pozycję **Wyszukiwanie Metaverse** z góry. Dodaj dowolny filtr, który jest potrzebny do znalezienia odpowiednich obiektów. 
3. W wyniku wyszukiwania Otwórz obiekt. Sprawdź wartości atrybutów, a także sprawdź, czy w kolumnie **reguły synchronizacji** zastosowana została reguła zgodnie z oczekiwaniami.  
![Wyszukiwanie magazynu metaverse](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Włącz harmonogram
Jeśli wszystko jest w oczekiwany sposób, możesz włączyć harmonogram ponownie. W programie PowerShell uruchom polecenie `Set-ADSyncScheduler -SyncCycleEnabled $true` .

## <a name="other-common-attribute-flow-changes"></a>Inne typowe zmiany przepływu atrybutów
W poprzedniej sekcji opisano, jak wprowadzać zmiany w przepływie atrybutu. W tej sekcji podano kilka dodatkowych przykładów. Procedura tworzenia reguły synchronizacji jest skracana, ale można znaleźć wszystkie kroki opisane w poprzedniej sekcji.

### <a name="use-an-attribute-other-than-the-default"></a>Użyj atrybutu innego niż domyślny
W tym scenariuszu firmy Fabrikam istnieje Las, w którym użyto alfabetu lokalnego dla podaną nazwę, nazwisko i nazwę wyświetlaną. Reprezentacja znaków łacińskich tych atrybutów znajduje się w atrybutach rozszerzenia. Aby utworzyć globalną listę adresów w usłudze Azure AD i Microsoft 365, organizacja chce używać tych atrybutów zamiast tego.

W przypadku konfiguracji domyślnej obiekt z lasu lokalnego wygląda następująco:  
![Przepływ atrybutów 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Aby utworzyć regułę z innymi przepływami atrybutów, wykonaj następujące czynności:

1. Otwórz **Edytor reguł synchronizacji** z menu **Start** .
2. Po wybraniu **ruchu przychodzącego** z lewej strony kliknij przycisk **Dodaj nową regułę** .
3. Nadaj regule nazwę i opis. Wybierz lokalne wystąpienie Active Directory i odpowiednie typy obiektów. W obszarze **Typ łącza** wybierz pozycję **Dołącz**. W obszarze **pierwszeństwo** wybierz liczbę, która nie jest używana przez inną regułę. Początkowe reguły zaczynają się od 100, więc w tym przykładzie można użyć wartości 50.
  ![Przepływ atrybutów 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Pozostaw pusty **Filtr zakresu** . (Oznacza to, że powinna być stosowana do wszystkich obiektów użytkownika w lesie).
5. Pozostaw puste **reguły sprzężenia** . (Oznacza to, że można dołączać do dowolnych reguł out-of-Box).
6. W **transformacjach** Utwórz następujące przepływy:  
  ![Przepływ atrybutów 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Kliknij przycisk **Dodaj** , aby zapisać regułę.
8. Przejdź do **Synchronization Service Manager**. Na **łącznikach** wybierz łącznik, do którego dodano regułę. Wybierz pozycję **Uruchom** , a następnie wybierz pozycję **pełna synchronizacja**. Pełna synchronizacja ponownie oblicza wszystkie obiekty przy użyciu bieżących reguł.

Jest to wynik tego samego obiektu z tą regułą niestandardową:  
![Przepływ atrybutów 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Długość atrybutów
Atrybuty ciągu można indeksować domyślnie, a maksymalna długość to 448 znaków. Jeśli pracujesz z atrybutami ciągu, które mogą zawierać więcej, upewnij się, że w przepływie atrybutów zostały uwzględnione następujące elementy:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Zmiana userPrincipalSuffix
Atrybut userPrincipalName w Active Directory nie jest zawsze znany przez użytkowników i może nie być odpowiedni jako identyfikator logowania. Za pomocą Kreatora instalacji Azure AD Connect Sync można wybrać inny atrybut — na przykład *poczta*. Jednak w niektórych przypadkach atrybut musi być obliczony.

Przykładowo firma Contoso ma dwa katalogi usługi Azure AD, jeden do produkcji i jeden do testowania. Chcą, aby użytkownicy w swojej dzierżawie testu używali innego sufiksu w IDENTYFIKATORze logowania:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

W tym wyrażeniu podejmij wszystkie pozostałe @-sign (słowa) i Połącz ze stałym ciągiem.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Konwertuj atrybut o wartości wielowartościowej na pojedynczą wartość
Niektóre atrybuty w Active Directory są wielowartościowe w schemacie, nawet jeśli szukają pojedynczej wartości w Active Directory Użytkownicy i komputery. Przykładem jest atrybut Description:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

W tym wyrażeniu, jeśli atrybut ma wartość, weź pierwszy element ( *element* ) w atrybucie, Usuń spacje wiodące i końcowe ( *Trim* ), a następnie pozostaw pierwsze 448 znaków ( *Left* ) w ciągu.

### <a name="do-not-flow-an-attribute"></a>Nie przepływaj atrybutu
Aby uzyskać ogólne instrukcje dotyczące scenariusza dla tej sekcji, zobacz [kontrolowanie procesu przepływu atrybutów](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Istnieją dwa sposoby, aby nie przepływać atrybutu. Pierwszy z nich to przy użyciu Kreatora instalacji, aby [usunąć wybrane atrybuty](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Ta opcja działa, jeśli nigdy nie zsynchronizowano atrybutu. Jeśli jednak rozpoczęto synchronizowanie tego atrybutu i później usuniesz go za pomocą tej funkcji, aparat synchronizacji zatrzyma zarządzanie atrybutem, a istniejące wartości są pozostawione w usłudze Azure AD.

Jeśli chcesz usunąć wartość atrybutu i upewnić się, że nie jest on przepływem w przyszłości, musisz utworzyć regułę niestandardową.

W tym scenariuszu firmy Fabrikam wprowadziliśmy, że niektóre z atrybutów, które są synchronizowane z chmurą, nie powinny znajdować się w tym miejscu. Chcemy upewnić się, że te atrybuty zostały usunięte z usługi Azure AD.  
![Złe atrybuty rozszerzenia](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Utwórz nową regułę synchronizacji ruchu przychodzącego i wypełnij opis.
  ![Znajduje](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Utwórz przepływy atrybutów z **wyrażeniem** dla elementu **flowtype** oraz z **AuthoritativeNull** dla **źródła**. Literał **AuthoritativeNull** wskazuje, że wartość powinna być pusta w magazynie Metaverse, nawet jeśli reguła synchronizacji o niższym priorytecie próbuje wypełnić wartość.
  ![Przekształcanie atrybutów rozszerzenia](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Zapisz regułę synchronizacji. Uruchom **usługę synchronizacji** , Znajdź łącznik, wybierz pozycję **Uruchom** , a następnie wybierz pozycję **pełna synchronizacja**. Ten krok ponownie oblicza wszystkie przepływy atrybutów.
4. Sprawdź, czy zamierzone zmiany zostaną wyeksportowane przez przeszukiwanie obszaru łącznika.
  ![Przemieszczono usunięcie](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Tworzenie reguł przy użyciu programu PowerShell
Korzystanie z edytora reguł synchronizacji działa prawidłowo, gdy tylko wprowadzono kilka zmian. Jeśli trzeba wprowadzić wiele zmian, program PowerShell może być lepszym rozwiązaniem. Niektóre funkcje zaawansowane są dostępne tylko w programie PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Pobierz skrypt programu PowerShell dla wbudowanej reguły
Aby wyświetlić skrypt programu PowerShell, który utworzył regułę out-of-Box, wybierz regułę w edytorze reguły synchronizacji, a następnie kliknij przycisk **Eksportuj**. Ta akcja zapewnia skrypt programu PowerShell, który utworzył regułę.

### <a name="advanced-precedence"></a>Pierwszeństwo zaawansowane
Reguły synchronizacji out-of-Box zaczynają się od wartości priorytetu 100. Jeśli masz wiele lasów i musisz wprowadzić wiele zmian niestandardowych, 99 reguły synchronizacji mogą być niewystarczające.

Możesz nakazać aparatowi synchronizacji, który ma dodać dodatkowe reguły przed regułami, które są używane. Aby uzyskać takie zachowanie, wykonaj następujące kroki:

1. Oznacz pierwszą regułę synchronizacji poza ramką ( **w z AD-User Join** ) w Edytorze reguł synchronizacji i wybierz pozycję **Eksportuj**. Skopiuj wartość identyfikatora SR.  
![PowerShell przed zmianą](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Utwórz nową regułę synchronizacji. Aby go utworzyć, można użyć edytora reguł synchronizacji. Wyeksportuj regułę do skryptu programu PowerShell.
3. We właściwości **PrecedenceBefore** Wstaw wartość identyfikatora z reguły out-of-box. Ustaw dla **pierwszeństwa** wartość **0**. Upewnij się, że atrybut identyfikatora jest unikatowy i że nie używasz identyfikatora GUID z innej reguły. Upewnij się również, że właściwość **ImmutableTag** nie jest ustawiona. Ta właściwość powinna być ustawiona tylko dla reguły wbudowanej.
4. Zapisz skrypt programu PowerShell i uruchom go. W efekcie reguła niestandardowa ma przypisaną wartość pierwszeństwa 100, a wszystkie inne reguły gotowe są zwiększane.  
![PowerShell po zmianie](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

W razie konieczności można mieć wiele niestandardowych reguł synchronizacji przy użyciu tej samej wartości **PrecedenceBefore** .

## <a name="enable-synchronization-of-usertype"></a>Włącz synchronizację elementu UserType
Azure AD Connect obsługuje synchronizację atrybutu **UserType** dla obiektów **użytkownika** w wersji 1.1.524.0 i nowszych. Dokładniej wprowadzono następujące zmiany:

- Schemat typu obiektu **użytkownika** w łączniku usługi Azure AD został rozszerzony w celu uwzględnienia atrybutu UserType, który jest typu String i jest pojedynczą wartością.
- Schemat **typu obiektu** w obiekcie Metaverse został rozszerzony tak, aby obejmował atrybut UserType, który jest typu String i ma wartość pojedynczą.

Domyślnie atrybut UserType nie jest włączony dla synchronizacji, ponieważ nie ma odpowiedniego atrybutu UserType w Active Directory lokalnym. Należy ręcznie włączyć synchronizację. Przed wykonaniem tej czynności należy wziąć pod uwagę następujące działania wymuszane przez usługę Azure AD:

- Usługa Azure AD akceptuje tylko dwie wartości atrybutu UserType: **member** i **gość**.
- Jeśli atrybut UserType nie jest włączony do synchronizacji w Azure AD Connect, użytkownicy usługi Azure AD utworzeni za pomocą synchronizacji katalogów będą mieli atrybut UserType ustawiony jako **member**.
- W wersji starszej niż 1.5.30.0 usługa Azure AD nie zezwolił na zmianę atrybutu UserType dla istniejących użytkowników usługi Azure AD przez Azure AD Connect. W starszych wersjach można było ustawić je tylko podczas tworzenia użytkowników usługi Azure AD i [zmieniać je za pomocą programu PowerShell](/powershell/module/azuread/set-azureaduser?view=azureadps-2.0).

Przed włączeniem synchronizacji atrybutu UserType należy najpierw zdecydować, jak atrybut pochodzi z Active Directory lokalnego. Poniżej przedstawiono najbardziej typowe podejścia:

- Wyznacz nieużywany lokalny atrybut usługi AD (na przykład extensionAttribute1), który ma być używany jako atrybut source. Wyznaczony lokalny atrybut usługi AD powinien być typu **String** , być jednowartościowy i zawierać **element członkowski** wartości lub **gościa**. 

    W przypadku wybrania tej metody należy upewnić się, że wyznaczono atrybut jest wypełniony poprawną wartością dla wszystkich istniejących obiektów użytkownika w Active Directory lokalnym, które są synchronizowane z usługą Azure AD przed włączeniem synchronizacji atrybutu UserType.

- Alternatywnie można utworzyć wartość atrybutu UserType z innych właściwości. Na przykład chcesz zsynchronizować wszystkich użytkowników jako **gościa** , jeśli ich lokalny atrybut AD userPrincipalName kończy się częścią domeny <em>@partners.fabrikam123.org</em> . 

    Jak wspomniano wcześniej, starsze wersje Azure AD Connect nie zezwalają na zmianę atrybutu UserType dla istniejących użytkowników usługi Azure AD przez Azure AD Connect. Z tego względu należy upewnić się, że ustalona logika jest zgodna ze sposobem, w jaki atrybut UserType jest już skonfigurowany dla wszystkich istniejących użytkowników usługi Azure AD w dzierżawie.

Procedurę włączania synchronizacji atrybutu UserType można podsumować jako:

1.  Wyłącz harmonogram synchronizacji i sprawdź, czy synchronizacja nie jest w toku.
2.  Dodaj atrybut source do schematu lokalnego łącznika usługi AD.
3.  Dodaj użytkownika do schematu łącznika usługi Azure AD.
4.  Utwórz regułę synchronizacji ruchu przychodzącego, aby przepływać wartość atrybutu z Active Directory lokalnego.
5.  Utwórz regułę synchronizacji danych wychodzących, aby przepływać wartość atrybutu do usługi Azure AD.
6.  Uruchom pełny cykl synchronizacji.
7.  Włączenie harmonogramu synchronizacji.

>[!NOTE]
> Pozostała część tej sekcji obejmuje te kroki. Są one opisane w kontekście wdrożenia usługi Azure AD z topologią jednego lasu i bez niestandardowych reguł synchronizacji. Jeśli istnieje topologia z obsługą kilku lasów, skonfigurowano niestandardowe reguły synchronizacji lub serwer przejściowy, należy odpowiednio dostosować kroki.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1. wyłączenie harmonogramu synchronizacji i sprawdzenie, czy synchronizacja nie jest w toku
Aby uniknąć eksportowania niezamierzonych zmian do usługi Azure AD, należy się upewnić, że synchronizacja nie odbywa się w trakcie aktualizacji reguł synchronizacji. Aby wyłączyć wbudowany harmonogram synchronizacji:

 1. Uruchom sesję programu PowerShell na serwerze Azure AD Connect.
 2. Aby wyłączyć synchronizację zaplanowaną, należy uruchomić polecenie cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 3. Otwórz Synchronization Service Manager, aby **uruchomić**  >  **usługę synchronizacji**.
 4. Przejdź do karty **operacje** i upewnij się, że nie ma operacji o stanie *w toku*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Krok 2. Dodawanie atrybutu source do schematu lokalnego łącznika usługi AD
Nie wszystkie atrybuty usługi Azure AD są importowane do lokalnego obszaru łącznika usługi AD. Aby dodać atrybut source do listy importowanych atrybutów:

 1. Przejdź do karty **Łączniki** w Synchronization Service Manager.
 2. Kliknij prawym przyciskiem myszy lokalny łącznik usługi AD i wybierz pozycję **Właściwości**.
 3. W podręcznym oknie dialogowym przejdź do karty **Wybierz atrybuty** .
 4. Upewnij się, że atrybut źródłowy jest zaznaczony na liście atrybutów.
 5. Kliknij przycisk **OK** , aby zapisać.
![Dodaj atrybut źródłowy do schematu lokalnego łącznika usługi AD](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-attribute-to-the-azure-ad-connector-schema"></a>Krok 3. Dodawanie atrybutu UserType do schematu łącznika usługi Azure AD
Domyślnie atrybut UserType nie jest importowany do obszaru Azure AD Connect. Aby dodać atrybut UserType do listy importowanych atrybutów:

 1. Przejdź do karty **Łączniki** w Synchronization Service Manager.
 2. Kliknij prawym przyciskiem myszy **Łącznik usługi Azure AD** , a następnie wybierz pozycję **Właściwości**.
 3. W podręcznym oknie dialogowym przejdź do karty **Wybierz atrybuty** .
 4. Upewnij się, że atrybut UserType jest zaznaczony na liście atrybutów.
 5. Kliknij przycisk **OK** , aby zapisać.

![Dodaj atrybut źródłowy do schematu łącznika usługi Azure AD](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Krok 4. Tworzenie reguły synchronizacji ruchu przychodzącego w celu przepływania wartości atrybutu z lokalnego Active Directory
Reguła synchronizacji ruchu przychodzącego zezwala na przepływ wartości atrybutu z atrybutu Source z lokalnego Active Directory do obiektu metaverse:

1. Otwórz Edytor **reguł synchronizacji, przechodząc do**  >  **edytora reguł synchronizacji**.
2. Ustaw **kierunek** filtrowania wyszukiwania na **ruch przychodzący**.
3. Kliknij przycisk **Dodaj nową regułę** , aby utworzyć nową regułę ruchu przychodzącego.
4. Na karcie **Opis** podaj następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | --- | --- | --- |
    | Nazwa | *Podaj nazwę* | Na przykład *w programie z usługi AD — User UserType* |
    | Opis | *Podaj opis* |  |
    | Połączony system | *Wybieranie lokalnego łącznika usługi AD* |  |
    | Typ połączonego obiektu systemu | **Użytkownik** |  |
    | Typ obiektu metaverse | **Person (Osoba)** |  |
    | Typ łącza | **Dołącz** |  |
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 do 99* | 1 – 99 jest zarezerwowany dla reguł synchronizacji niestandardowej. Nie wybieraj wartości, która jest używana przez inną regułę synchronizacji. |

5. Przejdź do karty **Filtr zakresu** i Dodaj **pojedynczą grupę filtrów określania zakresu** z następującą klauzulą:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Użytkownik\_ |

    Filtr określania zakresu określa, do których lokalnych obiektów usługi AD jest stosowana ta reguła synchronizacji ruchu przychodzącego. W tym przykładzie korzystamy z tego samego filtru określania zakresu, który jest używany w regule *w programie z usługi AD — wspólna* wartość synchronizacji użytkownika, która uniemożliwia stosowanie reguły synchronizacji do obiektów użytkownika utworzonych za pomocą funkcji zapisywania zwrotnego użytkownika usługi Azure AD. Może być konieczne dostosowanie filtru określania zakresu zgodnie ze wdrożeniem Azure AD Connect.

6. Przejdź do karty **transformacja** i zaimplementuj żądaną regułę transformacji. Na przykład jeśli w atrybucie sourceType wyznaczono nieużywany lokalny atrybut usługi AD (na przykład extensionAttribute1), można zaimplementować bezpośredni przepływ atrybutów:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Zastosuj raz | Typ scalania |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | extensionAttribute1 | Unchecked | Aktualizacja |

    W innym przykładzie, chcesz utworzyć wartość atrybutu UserType z innych właściwości. Na przykład chcesz zsynchronizować wszystkich użytkowników jako gościa, jeśli ich lokalny atrybut AD userPrincipalName kończy się częścią domeny <em>@partners.fabrikam123.org</em> . Można zaimplementować wyrażenie podobne do tego:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Zastosuj raz | Typ scalania |
    | --- | --- | --- | --- | --- |
    | Wyrażenie | UserType | IIF (isobecny ([userPrincipalName]), IIF (CBool (InStr (LCase ([userPrincipalName]), " @partners.fabrikam123.org ") = 0), "member", "Gość"), błąd ("userPrincipalName nie jest obecny w celu określenia typu użytkownika")) | Unchecked | Aktualizacja |

7. Kliknij przycisk **Dodaj** , aby utworzyć regułę ruchu przychodzącego.

![Utwórz regułę synchronizacji ruchu przychodzącego](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Krok 5. Tworzenie reguły synchronizacji ruchu wychodzącego w celu przepływania wartości atrybutu do usługi Azure AD
Reguła synchronizacji danych wychodzących zezwala na przepływ wartości atrybutu z Metaverse do atrybutu UserType w usłudze Azure AD:

1. Przejdź do edytora reguł synchronizacji.
2. Ustaw **kierunek** filtrowania wyszukiwania na **wychodzący**.
3. Kliknij przycisk **Dodaj nową regułę** .
4. Na karcie **Opis** podaj następującą konfigurację:

    | Atrybut | Wartość | Szczegóły |
    | ----- | ------ | --- |
    | Nazwa | *Podaj nazwę* | Na przykład *do usługi AAD — User UserType* |
    | Opis | *Podaj opis* ||
    | Połączony system | *Wybieranie łącznika usługi AAD* ||
    | Typ połączonego obiektu systemu | **Użytkownik** ||
    | Typ obiektu metaverse | **Person (Osoba)** ||
    | Typ łącza | **Dołącz** ||
    | Pierwszeństwo | *Wybierz liczbę z zakresu od 1 do 99* | 1 – 99 jest zarezerwowany dla reguł synchronizacji niestandardowej. Nie wybieraj wartości, która jest używana przez inną regułę synchronizacji. |

5. Przejdź do karty **Filtr zakresu** i Dodaj **pojedynczą grupę filtrów określania zakresu** przy użyciu dwóch klauzul:

    | Atrybut | Operator | Wartość |
    | --- | --- | --- |
    | sourceObjectType | WIĘKSZY | Użytkownik |
    | cloudMastered | NOTEQUAL | Prawda |

    Filtr określania zakresu określa, do których obiektów usługi Azure AD jest stosowana ta reguła synchronizacji danych wychodzących. W tym przykładzie używamy tego samego filtru określania zakresu z reguły synchronizacji Out to on-of-Box z *tożsamością użytkownika* . Uniemożliwia stosowanie reguły synchronizacji do obiektów użytkownika, które nie są zsynchronizowane z Active Directory lokalnych. Może być konieczne dostosowanie filtru określania zakresu zgodnie ze wdrożeniem Azure AD Connect.

6. Przejdź do karty **transformacja** i zaimplementuj następującą regułę przekształcania:

    | Typ przepływu | Atrybut docelowy | Element źródłowy | Zastosuj raz | Typ scalania |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | UserType | Unchecked | Aktualizacja |

7. Kliknij przycisk **Dodaj** , aby utworzyć regułę wychodzącą.

![Utwórz regułę synchronizacji ruchu wychodzącego](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Krok 6. Uruchamianie pełnego cyklu synchronizacji
Ogólnie rzecz biorąc, wymagany jest pełny cykl synchronizacji, ponieważ dodano nowe atrybuty do schematów Active Directory i łącznika usługi Azure AD oraz wprowadzono niestandardowe reguły synchronizacji. Chcesz zweryfikować zmiany przed ich eksportowaniem do usługi Azure AD. 

Aby sprawdzić zmiany podczas ręcznego wykonywania kroków, które składają się na pełny cykl synchronizacji, można wykonać poniższe czynności.

1. Uruchom **pełny import** do **lokalnego łącznika usługi AD** :

   1. Przejdź do karty **Łączniki** w Synchronization Service Manager.
   2. Kliknij prawym przyciskiem myszy **lokalny łącznik usługi AD** i wybierz polecenie **Uruchom**.
   3. W podręcznym oknie dialogowym wybierz pozycję **pełny import** , a następnie kliknij przycisk **OK**.
   4. Poczekaj na zakończenie operacji.

      > [!NOTE]
      > Możesz pominąć pełny import w lokalnym łączniku usługi AD, jeśli atrybut Source już znajduje się na liście importowanych atrybutów. Innymi słowy, nie musisz wprowadzać żadnych zmian w [kroku 2: Dodaj atrybut source do schematu lokalnego łącznika usługi AD](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Uruchom **pełny import** z **łącznika usługi Azure AD** :

   1. Kliknij prawym przyciskiem myszy **Łącznik usługi Azure AD** , a następnie wybierz polecenie **Uruchom**.
   2. W podręcznym oknie dialogowym wybierz pozycję **pełny import** , a następnie kliknij przycisk **OK**.
   3. Poczekaj na zakończenie operacji.

3. Sprawdź zmiany reguły synchronizacji w istniejącym obiekcie użytkownika:

    Atrybut source z lokalnych Active Directory i UserType z usługi Azure AD został zaimportowany do odpowiednich miejsc łączników. Przed kontynuowaniem pełnej synchronizacji wykonaj **Podgląd** istniejącego obiektu użytkownika w lokalnym obszarze łącznika usługi AD. Wybrany obiekt powinien mieć atrybut Source wypełniony.
    
    Pomyślna **wersja zapoznawcza** z elementem UserType wypełnionym w magazynie Metaverse jest dobrym wskaźnikiem, że reguły synchronizacji zostały prawidłowo skonfigurowane. Aby uzyskać informacje na temat sposobu wykonania **wersji zapoznawczej** , zapoznaj się z sekcją [weryfikacja zmiany](#verify-the-change).

4. Uruchom **pełną synchronizację** na **lokalnym łączniku usługi AD** :

   1. Kliknij prawym przyciskiem myszy **lokalny łącznik usługi AD** i wybierz polecenie **Uruchom**.
   2. W podręcznym oknie dialogowym wybierz pozycję **pełna synchronizacja** , a następnie kliknij przycisk **OK**.
   3. Poczekaj na zakończenie operacji.

5. Sprawdź **oczekujące eksporty** do usługi Azure AD:

   1. Kliknij prawym przyciskiem myszy **Łącznik usługi Azure AD** , a następnie wybierz pozycję **obszar łącznika wyszukiwania**.
   2. W wyskakującym oknie dialogowym **Wyszukiwanie w miejscu łącznika wyszukiwania** :

      - Ustaw **zakres** na **eksport oczekujący**.
      - Zaznacz wszystkie trzy pola wyboru: **Dodaj** , **Modyfikuj** i **Usuń**.
      - Kliknij przycisk **Wyszukaj** , aby uzyskać listę obiektów ze zmianami, które mają zostać wyeksportowane. Aby przejrzeć zmiany dla danego obiektu, kliknij dwukrotnie obiekt.
      - Sprawdź, czy zmiany są oczekiwane.

6. Uruchom **Eksportowanie** z **łącznika usługi Azure AD** :

   1. Kliknij prawym przyciskiem myszy **Łącznik usługi Azure AD** , a następnie wybierz polecenie **Uruchom**.
   2. W oknie dialogowym **Uruchamianie łącznika** wybierz opcję **Eksportuj** , a następnie kliknij przycisk **OK**.
   3. Poczekaj na zakończenie eksportu do usługi Azure AD.

> [!NOTE]
> Te kroki nie obejmują pełnej synchronizacji i eksportowania kroków na łączniku usługi Azure AD. Te kroki nie są wymagane, ponieważ wartości atrybutów są przepływane tylko z Active Directory lokalnego do usługi Azure AD.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Krok 7. ponowne włączenie harmonogramu synchronizacji
Włącz ponownie wbudowany harmonogram synchronizacji:

1. Rozpocznij sesję programu PowerShell.
2. Ponownie włącz zaplanowaną synchronizację, uruchamiając polecenie cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true` .


## <a name="next-steps"></a>Następne kroki
* Więcej informacji na temat modelu konfiguracji można znaleźć w temacie [Inicjowanie obsługi administracyjnej](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Przeczytaj więcej na temat języka wyrażeń w temacie [Opis wyrażeń aprowizacji deklaracyjnej](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Tematy dotyczące omówienia**

* [Synchronizacja programu Azure AD Connect: omówienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
