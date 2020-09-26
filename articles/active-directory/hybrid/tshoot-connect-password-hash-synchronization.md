---
title: Rozwiązywanie problemów z synchronizacją skrótów haseł za pomocą synchronizacji Azure AD Connect | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z synchronizacją skrótów haseł.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77271679306b0fbde10c748afc7535f3ad3d0945
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317569"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Rozwiązywanie problemów z synchronizacją skrótów haseł za pomocą usługi synchronizacji programu Azure AD Connect

Ten temat zawiera instrukcje dotyczące rozwiązywania problemów z synchronizacją skrótów haseł. Jeśli hasła nie są synchronizowane zgodnie z oczekiwaniami, może to być zarówno dla podzbioru użytkowników, jak i dla wszystkich użytkowników.

W przypadku programu Azure Active Directory (Azure AD) Połącz wdrożenie z wersją 1.1.614.0 lub późniejszą, Użyj zadania rozwiązywania problemów w kreatorze, aby rozwiązać problemy z synchronizacją skrótów haseł:

* Jeśli masz problem z niezsynchronizowaniem haseł, zapoznaj się z komunikatem [nie są synchronizowane żadne hasła: Rozwiązywanie problemów za pomocą sekcji zadanie rozwiązywania problemów](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) .

* Jeśli wystąpi problem z poszczególnymi obiektami, zapoznaj się z jednym z nich, aby [nie synchronizować haseł: Rozwiązywanie problemów za pomocą sekcji zadanie rozwiązywania problemów](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) .

W przypadku wdrożenia z wersją 1.1.524.0 lub nowszą istnieje diagnostyczne polecenie cmdlet, którego można użyć do rozwiązywania problemów z synchronizacją skrótów haseł:

* Jeśli masz problem z niezsynchronizowaniem haseł, zapoznaj się z komunikatem [nie są synchronizowane żadne hasła: Rozwiązywanie problemów za pomocą diagnostycznej sekcji poleceń cmdlet](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) .

* Jeśli występuje problem z poszczególnymi obiektami, zapoznaj się z jednym z nich, aby [nie synchronizować haseł: Rozwiązywanie problemów przy użyciu diagnostycznej sekcji poleceń cmdlet](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) .

W przypadku starszych wersji Azure AD Connect wdrożenia:

* Jeśli masz problem z niezsynchronizowaniem haseł, zapoznaj się z sekcją [nie są synchronizowane żadne hasła: ręczne kroki rozwiązywania problemów](#no-passwords-are-synchronized-manual-troubleshooting-steps) .

* Jeśli wystąpi problem z poszczególnymi obiektami, zapoznaj się z jednym z nich, aby [nie synchronizować haseł: ręczne kroki rozwiązywania problemów](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) .



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Żadne hasła nie są synchronizowane: Rozwiązywanie problemów za pomocą zadania rozwiązywania problemów

Możesz użyć zadania rozwiązywania problemów, aby ustalić, dlaczego hasła nie są synchronizowane.

> [!NOTE]
> Zadanie rozwiązywania problemów jest dostępne tylko dla Azure AD Connect w wersji 1.1.614.0 lub nowszej.

### <a name="run-the-troubleshooting-task"></a>Uruchamianie zadania rozwiązywania problemów

Aby rozwiązać problemy, gdy nie są synchronizowane żadne hasła:

1. Otwórz nową sesję środowiska Windows PowerShell na serwerze Azure AD Connect przy użyciu opcji **Uruchom jako administrator** .

2. Uruchom system `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted` .

3. Uruchom Kreatora Azure AD Connect.

4. Przejdź do strony **dodatkowe zadania** , wybierz pozycję **Rozwiązywanie problemów**, a następnie kliknij przycisk **dalej**.

5. Na stronie Rozwiązywanie problemów kliknij przycisk **Uruchom** , aby uruchomić menu Rozwiązywanie problemów w programie PowerShell.

6. W menu głównym wybierz pozycję **Rozwiązywanie problemów synchronizacja skrótów haseł**.

7. W podmenu wybierz opcję **Synchronizacja skrótów haseł nie działa wcale**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Zrozumienie wyników zadania rozwiązywania problemów

Zadanie rozwiązywania problemów wykonuje następujące sprawdzenia:

* Sprawdza, czy funkcja synchronizacji skrótów haseł jest włączona dla dzierżawy usługi Azure AD.

* Sprawdza, czy serwer Azure AD Connect nie jest w trybie przejściowym.

* Dla każdego istniejącego lokalnego łącznika Active Directory (który odnosi się do istniejącego lasu Active Directory):

   * Sprawdza, czy funkcja synchronizacji skrótów haseł jest włączona.
   
   * Wyszukuje zdarzenia pulsu synchronizacji skrótów haseł w dziennikach zdarzeń aplikacji systemu Windows.

   * Dla każdej domeny Active Directory w ramach łącznika on-premises Active Directory:

      * Sprawdza, czy domena jest osiągalna z serwera Azure AD Connect.

      * Sprawdza, czy konta Active Directory Domain Services (AD DS) używane przez łącznik On-Premises Active Directory ma poprawną nazwę użytkownika, hasło i uprawnienia wymagane do synchronizacji skrótów haseł.

Na poniższym diagramie przedstawiono wyniki polecenia cmdlet dla jednodomenowej topologii Active Directoryowej:

![Dane wyjściowe diagnostyki dla synchronizacji skrótów haseł](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

W pozostałej części tej sekcji opisano konkretne wyniki, które są zwracane przez zadanie i odpowiednie problemy.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>Funkcja synchronizacji skrótów haseł nie jest włączona

Jeśli synchronizacja skrótów haseł nie została włączona przy użyciu kreatora Azure AD Connect, zwracany jest następujący błąd:

![Synchronizacja skrótów haseł nie jest włączona](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Serwer Azure AD Connect jest w trybie przejściowym

Jeśli serwer Azure AD Connect jest w trybie przejściowym, synchronizacja skrótów haseł jest tymczasowo wyłączona i zwracany jest następujący błąd:

![Serwer Azure AD Connect jest w trybie przejściowym](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Brak zdarzeń pulsu synchronizacji skrótów haseł

Każdy lokalny łącznik Active Directory ma własny kanał synchronizacji skrótów haseł. Po nawiązaniu kanału synchronizacji skrótów haseł, gdy nie zostaną zsynchronizowane żadne zmiany hasła, zdarzenie pulsu (EventId 654) jest generowane co 30 minut w dzienniku zdarzeń aplikacji systemu Windows. Dla każdego lokalnego łącznika Active Directory polecenie cmdlet wyszukuje odpowiednie zdarzenia pulsu w ciągu ostatnich trzech godzin. Jeśli nie zostanie znalezione żadne zdarzenie pulsu, zostanie zwrócony następujący błąd:

![Brak zdarzenia pulsu synchronizacji skrótów haseł](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>Konto AD DS nie ma prawidłowych uprawnień

Jeśli użycie konta AD DS, które jest używane przez łącznik On-Premises Active Directory do synchronizowania skrótów haseł nie ma odpowiednich uprawnień, zwracany jest następujący błąd:

![Zrzut ekranu pokazujący błąd zwracany, gdy konto AD DS ma niepoprawną nazwę użytkownika lub hasło.](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nieprawidłowa nazwa użytkownika lub hasło konta AD DS

Jeśli użycie konta AD DS przez łącznika on-premises Active Directory do synchronizowania skrótów haseł ma niepoprawną nazwę użytkownika lub hasło, zostanie zwrócony następujący błąd:

![Nieprawidłowe poświadczenie](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Jeden z obiektów nie synchronizuje haseł: Rozwiązywanie problemów przy użyciu zadania rozwiązywania problemów

Możesz użyć zadania rozwiązywania problemów, aby określić, dlaczego jeden obiekt nie synchronizuje haseł.

> [!NOTE]
> Zadanie rozwiązywania problemów jest dostępne tylko dla Azure AD Connect w wersji 1.1.614.0 lub nowszej.

### <a name="run-the-diagnostics-cmdlet"></a>Uruchom polecenie cmdlet diagnostyki

Aby rozwiązać problemy z określonym obiektem użytkownika:

1. Otwórz nową sesję środowiska Windows PowerShell na serwerze Azure AD Connect przy użyciu opcji **Uruchom jako administrator** .

2. Uruchom system `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted` .

3. Uruchom Kreatora Azure AD Connect.

4. Przejdź do strony **dodatkowe zadania** , wybierz pozycję **Rozwiązywanie problemów**, a następnie kliknij przycisk **dalej**.

5. Na stronie Rozwiązywanie problemów kliknij przycisk **Uruchom** , aby uruchomić menu Rozwiązywanie problemów w programie PowerShell.

6. W menu głównym wybierz pozycję **Rozwiązywanie problemów synchronizacja skrótów haseł**.

7. W podmenu wybierz pozycję **hasło nie jest zsynchronizowane dla określonego konta użytkownika**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Zrozumienie wyników zadania rozwiązywania problemów

Zadanie rozwiązywania problemów wykonuje następujące sprawdzenia:

* Bada stan obiektu Active Directory w przestrzeni łącznika Active Directory, Metaverse i Azure AD Connector.

* Sprawdza, czy istnieją reguły synchronizacji z włączoną synchronizacją skrótów haseł i stosowane do obiektu Active Directory.

* Próbuje pobrać i wyświetlić wyniki ostatniej próby synchronizacji hasła dla obiektu.

Na poniższym diagramie przedstawiono wyniki polecenia cmdlet w przypadku rozwiązywania problemów z synchronizacją skrótów haseł dla pojedynczego obiektu:

![Dane wyjściowe diagnostyki dla synchronizacji skrótów haseł — pojedynczy obiekt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

W pozostałej części tej sekcji opisano konkretne wyniki zwrócone przez polecenie cmdlet i odpowiednie problemy.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Obiekt Active Directory nie został wyeksportowany do usługi Azure AD

Synchronizacja skrótów haseł dla tego konta Active Directory lokalnego nie powiodła się, ponieważ nie ma odpowiedniego obiektu w dzierżawie usługi Azure AD. Zwracany jest następujący błąd:

![Brak obiektu usługi Azure AD](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Użytkownik ma hasło tymczasowe

Obecnie Azure AD Connect nie obsługuje synchronizowania haseł tymczasowych z usługą Azure AD. Hasło jest uznawane za tymczasowe, jeśli opcja **Zmień hasło przy następnym logowaniu** jest ustawiona na lokalnym Active Directory użytkownika. Zwracany jest następujący błąd:

![Nie wyeksportowano hasła tymczasowego](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Wyniki ostatniej próby synchronizacji hasła nie są dostępne

Domyślnie Azure AD Connect przechowuje wyniki synchronizacji skrótów haseł przez siedem dni. Jeśli dla wybranego obiektu Active Directory nie są dostępne żadne wyniki, zostanie zwrócone następujące ostrzeżenie:

![Dane wyjściowe diagnostyki dla pojedynczego obiektu — brak historii synchronizacji haseł](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Żadne hasła nie są synchronizowane: Rozwiązywanie problemów za pomocą diagnostycznego polecenia cmdlet

Za pomocą `Invoke-ADSyncDiagnostics` polecenia cmdlet można ustalić, dlaczego żadne hasła nie są synchronizowane.

> [!NOTE]
> `Invoke-ADSyncDiagnostics`Polecenie cmdlet jest dostępne tylko dla Azure AD Connect w wersji 1.1.524.0 lub nowszej.

### <a name="run-the-diagnostics-cmdlet"></a>Uruchom polecenie cmdlet diagnostyki

Aby rozwiązać problemy, gdy nie są synchronizowane żadne hasła:

1. Otwórz nową sesję środowiska Windows PowerShell na serwerze Azure AD Connect przy użyciu opcji **Uruchom jako administrator** .

2. Uruchom system `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted` .

3. Uruchom polecenie `Import-Module ADSyncDiagnostics`.

4. Uruchom polecenie `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Jeden z obiektów nie synchronizuje haseł: Rozwiązywanie problemów przy użyciu diagnostycznego polecenia cmdlet

Za pomocą `Invoke-ADSyncDiagnostics` polecenia cmdlet można określić, dlaczego jeden obiekt nie synchronizuje haseł.

> [!NOTE]
> `Invoke-ADSyncDiagnostics`Polecenie cmdlet jest dostępne tylko dla Azure AD Connect w wersji 1.1.524.0 lub nowszej.

### <a name="run-the-diagnostics-cmdlet"></a>Uruchom polecenie cmdlet diagnostyki

Aby rozwiązać problemy, gdy żadne hasła nie są synchronizowane dla użytkownika:

1. Otwórz nową sesję środowiska Windows PowerShell na serwerze Azure AD Connect przy użyciu opcji **Uruchom jako administrator** .

2. Uruchom system `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted` .

3. Uruchom polecenie `Import-Module ADSyncDiagnostics`.

4. Uruchom następujące polecenie cmdlet:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Na przykład:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Żadne hasła nie są synchronizowane: ręczne kroki rozwiązywania problemów

Wykonaj następujące kroki, aby określić, dlaczego hasła nie są synchronizowane:

1. Czy serwer Connect jest w [trybie przejściowym](how-to-connect-sync-staging-server.md)? Serwer w trybie przejściowym nie synchronizuje żadnych haseł.

2. Uruchom skrypt w sekcji [pobieranie stanu ustawień synchronizacji haseł](#get-the-status-of-password-sync-settings) . Zawiera omówienie konfiguracji synchronizacji haseł.  

    ![Dane wyjściowe skryptu programu PowerShell z ustawień synchronizacji haseł](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Jeśli ta funkcja nie jest włączona w usłudze Azure AD lub jeśli stan kanału synchronizacji nie jest włączony, uruchom Kreatora instalacji programu Connect. Wybierz opcję **Dostosuj opcje synchronizacji**i usuń zaznaczenie opcji Synchronizacja haseł. Ta zmiana tymczasowo wyłącza funkcję. Następnie ponownie uruchom kreatora i ponownie Włącz synchronizację haseł. Uruchom ponownie skrypt, aby sprawdzić, czy konfiguracja jest poprawna.

4. Sprawdź błędy w dzienniku zdarzeń. Poszukaj następujących zdarzeń, co może wskazywać na problem:
    * Źródło: "Synchronizacja katalogów" Identyfikator: 0, 611, 652, 655, Jeśli zobaczysz te zdarzenia, wystąpił problem z połączeniem. Komunikat dziennika zdarzeń zawiera informacje o lesie, w którym występuje problem. Aby uzyskać więcej informacji, zobacz [problem z łącznością](#connectivity problem).

5. Jeśli nie widzisz pulsu lub nic innego nie działało, uruchom [wyzwalanie pełnej synchronizacji wszystkich haseł](#trigger-a-full-sync-of-all-passwords). Uruchom skrypt tylko raz.

6. Zapoznaj się z sekcją Rozwiązywanie problemów z jednym obiektem, który nie synchronizuje haseł.

### <a name="connectivity-problems"></a>Problemy z łącznością

Czy masz połączenie z usługą Azure AD?

Czy konto ma wymagane uprawnienia do odczytu skrótów haseł we wszystkich domenach? Jeśli zainstalowano program Connect przy użyciu ustawień ekspresowych, uprawnienia powinny być już poprawne. 

Jeśli użyto instalacji niestandardowej, Ustaw uprawnienia ręcznie, wykonując następujące czynności:
    
1. Aby znaleźć konto używane przez łącznik Active Directory, Rozpocznij **Synchronization Service Manager**. 
 
2. Przejdź do **łączników**, a następnie wyszukaj lokalny Las Active Directory, którego dotyczy problem. 
 
3. Wybierz łącznik, a następnie kliknij przycisk **Właściwości**. 
 
4. Przejdź do obszaru **łączenie z Active Directory lasu**.  
    
    ![Konto używane przez łącznik Active Directory](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Zanotuj nazwę użytkownika i domenę, w której znajduje się konto.
    
5. Uruchom **Active Directory Użytkownicy i komputery**, a następnie sprawdź, czy konto, które zostało znalezione wcześniej, ma ustawione uprawnienia przestrzegania w katalogu głównym wszystkich domen w lesie:
    * Replikowanie zmian w katalogu
    * Replikuj wszystkie zmiany katalogu

6. Czy kontrolery domeny są osiągalne dla Azure AD Connect? Jeśli serwer Connect nie może połączyć się ze wszystkimi kontrolerami domeny, należy skonfigurować **tylko preferowany kontroler domeny**.  
    
    ![Kontroler domeny używany przez łącznik Active Directory](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  

7. Wróć do **Synchronization Service Manager** i **Skonfiguruj partycję katalogu**. 
 
8. W obszarze **Wybierz partycje katalogu**zaznacz domenę, zaznacz pole wyboru **Użyj tylko preferowanych kontrolerów domeny** , a następnie kliknij przycisk **Konfiguruj**. 

9. Na liście wprowadź kontrolery domeny, które mają być używane do synchronizacji haseł. Ta sama lista jest również używana do importowania i eksportowania. Wykonaj te kroki dla wszystkich domen.

> [!NOTE]
> Aby zastosować te zmiany, uruchom ponownie usługę **Microsoft Azure AD Sync** (ADSync).

10. Jeśli skrypt pokazuje, że nie ma pulsu, uruchom skrypt w [Wyzwól pełną synchronizację wszystkich haseł](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Jeden z obiektów nie synchronizuje haseł: ręczne kroki rozwiązywania problemów

Możesz łatwo rozwiązywać problemy z synchronizacją skrótów haseł, przeglądając stan obiektu.

1. W **Active Directory Użytkownicy i komputery**, Wyszukaj użytkownika, a następnie sprawdź, czy pole wyboru **użytkownik musi zmienić hasło przy następnym logowaniu** jest wyczyszczone.  

    ![Active Directory wydajne hasła](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Jeśli pole wyboru jest zaznaczone, poproszenie użytkownika o zalogowanie się i zmianę hasła. Tymczasowe hasła nie są synchronizowane z usługą Azure AD.

2. Jeśli hasło jest poprawne w Active Directory, postępuj zgodnie z użytkownikiem w aparacie synchronizacji. Korzystając z Active Directory lokalnych w usłudze Azure AD, możesz sprawdzić, czy w obiekcie występuje błąd opisowy.

    a. Uruchom [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

    b. Kliknij pozycję **Łączniki**.

    c. Wybierz **łącznik Active Directory** , w którym znajduje się użytkownik.

    d. Wybierz pozycję **obszar łącznika wyszukiwania**.

    e. W polu **zakres** wybierz pozycję **nazwa wyróżniająca lub zakotwiczenie**, a następnie wprowadź pełną nazwę wyróżniającą użytkownika, którego chcesz rozwiązać.

    ![Wyszukaj użytkownika w obszarze łącznika z nazwą wyróżniającą](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Znajdź użytkownika, którego szukasz, a następnie kliknij pozycję **Właściwości** , aby wyświetlić wszystkie atrybuty. Jeśli użytkownik nie znajduje się w wynikach wyszukiwania, zweryfikuj [reguły filtrowania](how-to-connect-sync-configure-filtering.md) i upewnij się, że zostały uruchomione polecenie [Zastosuj i Zweryfikuj zmiany](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) dla użytkownika, które mają być wyświetlane w obszarze Połącz.

    przykład Aby wyświetlić szczegóły synchronizacji haseł dla obiektu w ubiegłym tygodniu, kliknij przycisk **Dziennik**.  

    ![Szczegóły dziennika obiektów](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Jeśli dziennik obiektów jest pusty, Azure AD Connect nie mógł odczytać skrótu hasła z Active Directory. Kontynuuj Rozwiązywanie problemów z błędami łączności. Jeśli zobaczysz inną wartość niż **powodzenie**, zapoznaj się z tabelą w [dzienniku synchronizacji haseł](#password-sync-log).

    h. Wybierz kartę **elementy** powiązane i upewnij się, że co najmniej jedna reguła synchronizacji w kolumnie **PasswordSync** ma **wartość true**. W domyślnej konfiguracji Nazwa reguły synchronizacji należy **do usługi AD-User AccountEnabled**.  

    ![Informacje związane z użytkownikiem](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Kliknij pozycję **właściwości obiektu metaverse** , aby wyświetlić listę atrybutów użytkownika.  

    ![Zrzut ekranu pokazujący listę atrybutów użytkownika dla właściwości obiektu Metaverse.](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Sprawdź, czy nie ma atrybutu **cloudFiltered** . Upewnij się, że atrybuty domeny (domainFQDN i domainNetBios) mają oczekiwane wartości.

    j. Kliknij kartę **Łączniki** . Upewnij się, że łączniki są widoczne zarówno dla Active Directory lokalnych, jak i usługi Azure AD.

    ![Informacje o magazynie Metaverse](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Wybierz wiersz reprezentujący usługę Azure AD, kliknij pozycję **Właściwości**, a następnie kliknij **kartę elementy** powiązane. Obiekt przestrzeni łącznika powinien mieć regułę ruchu wychodzącego w kolumnie **PasswordSync** ustawioną na **wartość true**. W domyślnej konfiguracji Nazwa reguły synchronizacji jest **przyłączany do usługi AAD**.  

    ![Okno dialogowe właściwości obiektu obszaru łącznika](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Dziennik synchronizacji haseł

Kolumna stan może mieć następujące wartości:

| Stan | Opis |
| --- | --- |
| Powodzenie |Hasło zostało pomyślnie zsynchronizowane. |
| FilteredByTarget |Hasło jest ustawione na wartość **użytkownik musi zmienić hasło przy następnym logowaniu**. Hasło nie zostało zsynchronizowane. |
| NoTargetConnection |Brak obiektów w magazynie Metaverse lub w przestrzeni łącznika usługi Azure AD. |
| SourceConnectorNotPresent |Nie znaleziono obiektu w lokalnym obszarze łącznika Active Directory. |
| TargetNotExportedToDirectory |Obiekt w obszarze łącznika usługi Azure AD nie został jeszcze wyeksportowany. |
| MigratedCheckDetailsForMoreInfo |Wpis dziennika został utworzony przed kompilacją 1.0.9125.0 i jest wyświetlany w jego starszym stanie. |
| Error |Usługa zwróciła nieznany błąd. |
| Nieznane |Wystąpił błąd podczas próby przetworzenia partii skrótów haseł.  |
| Brakattribute |Określone atrybuty (na przykład skrót protokołu Kerberos) wymagane przez Azure AD Domain Services są niedostępne. |
| RetryRequestedByTarget |Określone atrybuty (na przykład skrót protokołu Kerberos) wymagane przez Azure AD Domain Services nie były wcześniej dostępne. Podjęto próbę ponownego zsynchronizowania skrótu hasła użytkownika. |

## <a name="scripts-to-help-troubleshooting"></a>Skrypty ułatwiające rozwiązywanie problemów

### <a name="get-the-status-of-password-sync-settings"></a>Pobieranie stanu ustawień synchronizacji haseł

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Wyzwalanie pełnej synchronizacji wszystkich haseł

> [!NOTE]
> Uruchom ten skrypt tylko raz. Jeśli musisz uruchomić go więcej niż raz, coś innego jest problem. Aby rozwiązać ten problem, skontaktuj się z pomocą techniczną firmy Microsoft.

Możesz wyzwolić pełną synchronizację wszystkich haseł, używając następującego skryptu:

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Następne kroki

* [Implementowanie synchronizacji skrótów haseł z synchronizacją Azure AD Connect](how-to-connect-password-hash-synchronization.md)
* [Synchronizacja Azure AD Connect: Dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
