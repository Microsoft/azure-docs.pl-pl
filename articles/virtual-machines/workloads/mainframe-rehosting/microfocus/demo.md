---
title: Konfigurowanie mikrofokusu CICS BankDemo dla deweloperów Enterprise Developer 4,0 na platformie Azure Virtual Machines
description: Uruchom aplikację BankDemo firmy Micro Focus na platformie Azure Virtual Machines (VM), aby dowiedzieć się, jak korzystać z programu Micro Focus Enterprise Server i Enterprise Developer.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 2d48c767b836771600b3491299e0bdb33aacb75b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733153"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Konfigurowanie mikrofokusu CICS BankDemo dla deweloperów Enterprise Developer 4,0 na platformie Azure

Podczas konfigurowania programu Micro Focus Enterprise Server 4,0 i Enterprise Developer 4,0 na platformie Azure można testować wdrożenia obciążeń firmy IBM z/OS. W tym artykule pokazano, jak skonfigurować CICS BankDemo, przykładową aplikację, która jest dostarczana z deweloperem przedsiębiorstwa.

CICs oznacza system kontroli informacji klienta, platformę transakcji używaną przez wiele aplikacji mainframe w trybie online. Aplikacja BankDemo doskonale nadaje się do uczenia się, w jaki sposób działa deweloper Enterprise Server i Enterprise oraz jak zarządzać i wdrażać rzeczywistą aplikację z użyciem zielonych terminali ekranu.

> [!NOTE]
> Wkrótce: instrukcje dotyczące konfigurowania programu [Micro Focus Enterprise Server 5,0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) na maszynach wirtualnych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Maszyna wirtualna z [deweloperem przedsiębiorstwa](set-up-micro-focus-azure.md). Należy pamiętać, że deweloper przedsiębiorstwa ma kompletne wystąpienie serwera korporacyjnego na potrzeby projektowania i testowania. To wystąpienie jest wystąpieniem programu Enterprise Server używanym do pokazania.

- [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Pobierz i zainstaluj go na maszynie wirtualnej dewelopera przedsiębiorstwa. Serwer przedsiębiorstwa wymaga bazy danych do zarządzania regionami CICS, a aplikacja BankDemo używa również bazy danych SQL Server o nazwie BANKDEMO. W tej wersji demonstracyjnej założono, że używasz SQL Server Express dla obu baz danych. W przypadku instalowania programu wybierz instalację podstawową.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Program SSMS służy do zarządzania bazami danych i uruchamiania skryptu T-SQL. Pobierz i zainstaluj go na maszynie wirtualnej dewelopera przedsiębiorstwa.

- [Program Visual studio 2019](https://azure.microsoft.com/downloads/) z najnowszym dodatkiem Service Pack lub programem [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), który można pobrać bezpłatnie.

- Rumba Desktop lub inny emulator 3270.

## <a name="configure-the-windows-environment"></a>Konfigurowanie środowiska systemu Windows

Po zainstalowaniu dewelopera Enterprise 4,0 na maszynie wirtualnej należy skonfigurować wystąpienie programu Enterprise Server, które jest z nim dołączone. W tym celu należy zainstalować kilka dodatkowych funkcji systemu Windows w następujący sposób.

1. Użyj protokołu RDP, aby zalogować się do utworzonej maszyny wirtualnej z systemem Enterprise Server 4,0.

2. Kliknij ikonę **wyszukiwania** obok przycisku **Start** i wpisz **funkcje systemu Windows**. Zostanie otwarty Kreator dodawania ról i funkcji Menedżer serwera.

3. Wybierz **rolę serwer sieci Web (IIS)**, a następnie sprawdź następujące opcje:

    - Narzędzia do zarządzania siecią Web
    - Zgodność z zarządzaniem usługami IIS w wersji 6 (Wybierz wszystkie dostępne funkcje)
    - Konsola zarządzania usługami IIS
    - Narzędzia i skrypty zarządzania usługami IIS
    - Usługa zarządzania usługami IIS

4. Wybierz pozycję **World Wide Web Services** i sprawdź następujące opcje:

     Funkcje projektowania aplikacji:
    - Rozszerzenia architektury .NET
    - ASP.NET
    - Typowe funkcje HTTP: Dodaj wszystkie dostępne funkcje
    - Kondycja i Diagnostyka: Dodaj wszystkie dostępne funkcje
    - Bezpieczeństw
        - Uwierzytelnianie podstawowe
        - Uwierzytelnianie systemu Windows

5. Wybierz pozycję **Usługa aktywacji procesów systemu Windows** i jej wszystkie elementy podrzędne.

6. W przypadku **funkcji** Sprawdź **Microsoft .NET Framework 3.5.1** i sprawdź następujące opcje:

    - Windows Communication Foundation Aktywacja HTTP
    - Windows Communication Foundation Aktywacja bez HTTP

7. W przypadku **funkcji** Sprawdź **Microsoft .NET Framework 4,6** i sprawdź następujące opcje:

   - Aktywacja nazwanego potoku
   - Aktywacja TCP
   - Udostępnianie portów TCP

     ![Kreator dodawania ról i funkcji: usługi ról](media/01-demo-roles.png)

8. Po wybraniu wszystkich opcji kliknij przycisk **dalej** , aby zainstalować.

9. Po włączeniu funkcji systemu Windows przejdź do pozycji **Panel sterowania \> system i zabezpieczenia \> Narzędzia administracyjne**, a następnie wybierz pozycję **usługi**. Przewiń w dół i upewnij się, że następujące usługi działają i mają ustawioną wartość **automatyczny**:

    - **NetTcpPortSharing**
    - **Adapter odbiornika NET. pipe**
    - **Adapter odbiornika NET. TCP**

10. Aby skonfigurować usługi IIS i obsługiwały je, w menu Znajdź polecenie **Micro Focus Enterprise wiersz polecenia dla deweloperów (64-bitowe)** i Uruchom jako **administrator**.

11. Wpisz **wassetup —** i naciśnij klawisz **Enter**.

12. Po uruchomieniu skryptu można zamknąć okno.

## <a name="configure-the-local-system-account-for-sql-server"></a>Skonfiguruj konto systemu lokalnego dla SQL Server

Niektóre procesy serwera przedsiębiorstwa muszą mieć możliwość logowania SQL Server i tworzenia baz danych i innych obiektów. Te procesy korzystają z lokalnego konta systemowego, dlatego należy nadać uprawnienia administratora systemu dla tego konta.

1. Uruchom program **SSMS** i kliknij pozycję **Połącz** , aby nawiązać połączenie z lokalnym serwerem SQLExpress przy użyciu uwierzytelniania systemu Windows. Powinna być dostępna na liście **Nazwa serwera** .

2. Po lewej stronie rozwiń folder **zabezpieczenia** i wybierz pozycję **logowania**.

3. Wybierz pozycję **\\ system uwierzytelniania NT** i wybierz pozycję **Właściwości**.

4. Wybierz pozycję **role serwera** i sprawdź **administratora** systemu.

     ![Okno programu SSMS Eksplorator obiektów: Właściwości logowania](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Tworzenie bazy danych BankDemo i wszystkich jej obiektów

1. Otwórz **Eksploratora Windows** i przejdź do **C: \\ Użytkownicy \\ publiczne \\ dokumenty \\ Micro Focus \\ Enterprise Developer \\ przykłady \\ mainframe \\ CICS \\ dotnet \\ BankDemo \\ SQL**.

2. Skopiuj zawartość pliku **BankDemoCreateAll. SQL** do Schowka.

3. Otwórz narzędzie **SSMS**. Po prawej stronie kliknij pozycję **serwer** , a następnie wybierz pozycję **nowe zapytanie**.

4. Wklej zawartość schowka w polu **nowe zapytanie** .

5. Wykonaj instrukcję SQL, klikając pozycję **Wykonaj** na karcie **polecenia** nad zapytaniem.

Zapytanie powinno być uruchamiane bez błędów. Po zakończeniu będzie dostępna Przykładowa baza danych dla aplikacji BankDemo.

![SQLQuery1. SQL — dane wyjściowe](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Sprawdź, czy tabele i obiekty bazy danych zostały utworzone

1. Kliknij prawym przyciskiem myszy bazę danych **BANKDEMO** , a następnie wybierz pozycję **Odśwież**.

2. Rozwiń **bazę danych** i wybierz pozycję **tabele**. Powinien wyglądać podobnie do poniższego.

     ![Rozwinięto tabelę BANKDEMO w Eksplorator obiektów](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Kompilowanie aplikacji w programie Enterprise Developer

1. Otwórz program Visual Studio i zaloguj się.

2. W menu **plik** wybierz pozycję **Otwórz projekt/rozwiązanie**, przejdź do **C: \\ Użytkownicy \\ publiczne \\ dokumenty \\ Micro Focus \\ Enterprise Developer \\ przykłady \\ mainframe \\ CICS \\ dotnet \\ BankDemo** i wybierz plik **sln** .

3. Poświęć trochę czasu na przeanalizowanie obiektów. Programy COBOL są wyświetlane w Eksplorator rozwiązań z rozszerzeniem CBL wraz z CopyBooks (CPY) i JCL.

4. Kliknij prawym przyciskiem myszy projekt **BankDemo2** i wybierz pozycję **Ustaw jako projekt startowy**.

    > [!NOTE]
    > Projekt BankDemo korzysta z HCOSS (opcja zgodności hosta dla SQL Server), która nie jest używana w tej wersji demonstracyjnej.

5. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **BankDemo2** i wybierz polecenie **Kompiluj**.

    > [!NOTE]
    > Kompilowanie na poziomie rozwiązania powoduje błędy, ponieważ HCOSS nie został skonfigurowany.

6. Po skompilowaniu projektu, przejrzyj okno **dane wyjściowe** . Powinna ona teraz wyglądać podobnie jak na poniższym obrazie.

     ![Okno danych wyjściowych z pomyślnym kompilacją](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Wdrażanie aplikacji BankDemo w bazie danych regionów

1. Otwórz wiersz polecenia dla deweloperów przedsiębiorstwa (64 bit) jako administrator.

2. Przejdź do **% Public% \\ Documents \\ Micro Focus \\ Enterprise Developer \\ przykłady \\ mainframe \\ CICS \\ dotnet \\ BankDemo**.

3. W wierszu polecenia wykonaj **bankdemodbdeploy** i Uwzględnij parametr bazy danych do wdrożenia, na przykład:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Upewnij się, że używasz ukośnika (/), a nie ukośnika odwrotnego ( \\ ). Ten skrypt jest uruchamiany przez pewien czas.

![Administracja: okno wiersz polecenia dla deweloperów przedsiębiorstwa](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Tworzenie regionu BankDemo w Administratorze przedsiębiorstwa dla platformy .NET

1. Otwórz interfejs użytkownika **do administrowania serwerem przedsiębiorstwa dla platformy .NET** .

2. Aby uruchomić przystawkę MMC, w menu **Start** systemu Windows wybierz pozycję **mikrofokus konfiguracja Enterprise Developer \> Enterprise \> Server dla programu .NET admin**. (W przypadku systemu Windows Server wybierz pozycję **Micro Focus Enterprise Developer Enterprise \> Server for .NET admin**).

3. Rozwiń kontener **regiony** w lewym okienku, a następnie kliknij prawym przyciskiem myszy pozycję **CICS**.

4. Wybierz pozycję **Definiuj region** , aby utworzyć nowy region CICS o nazwie **BANKDEMO**, który jest hostowany w bazie danych (lokalnej).

5. Podaj wystąpienie serwera bazy danych, kliknij przycisk **dalej**, a następnie wprowadź nazwę regionu **BANKDEMO**.

     ![Definiowanie regionu — okno dialogowe](media/07-demo-cics.png)

6. Aby wybrać plik definicji regionu dla bazy danych między regionami, Znajdź **region \_ bankdemo \_db.config** w **C: \\ Użytkownicy \\ publiczne \\ dokumenty \\ Micro Focus \\ Enterprise Developer \\ przykłady \\ mainframe \\ CICS \\ dotnet \\ bankdemo**.

     ![Zdefiniuj nazwę regionu region: BANKDEMO](media/08-demo-cics.png)

7. Kliknij przycisk **Finish** (Zakończ).

## <a name="create-xa-resource-definitions"></a>Utwórz definicje zasobów XA

1. W lewym okienku interfejsu użytkownika **Administracja .NET dla przedsiębiorstw** rozwiń węzeł **system**, a następnie pozycję **definicje zasobów XA**. To ustawienie określa sposób, w jaki region współdziała z serwerem przedsiębiorstwa i bazami danych aplikacji.

2. Kliknij prawym przyciskiem myszy pozycję **definicje zasobów XA** i wybierz polecenie **Dodaj wystąpienie serwera**.

3. W polu listy rozwijanej wybierz pozycję **wystąpienie usługi bazy danych**. Będzie to komputer lokalny SQLEXPRESS.

4. Wybierz wystąpienie z w kontenerze **definicje zasobów XA (MachineName \\ SQLExpress)** , a następnie kliknij przycisk **Dodaj**.

5. Wybierz opcję **Definicja zasobu XA bazy danych** , a następnie wpisz **BANKDEMO** dla **nazwy** i **regionu**.

     ![Ekran definicji zasobu XA nowej bazy danych](media/09-demo-xa.png)

6. Kliknij przycisk wielokropka (**...**), aby wyświetlić Kreatora parametrów połączenia. W obszarze **Nazwa serwera** wpisz **(local) \\ SQLExpress**. W obszarze **Logowanie** wybierz pozycję **uwierzytelnianie systemu Windows**. W obszarze Nazwa bazy danych wpisz **BANKDEMO**

     ![Ekran edycji parametrów połączenia](media/10-demo-string.png)

7. Przetestuj połączenie.

## <a name="start-the-bankdemo-region"></a>Uruchamianie regionu BANKDEMO

> [!NOTE]
> Pierwszy krok jest ważny: należy ustawić region, aby używał właśnie utworzonej definicji zasobu XA.

1. Przejdź do **regionu BANDEMO CICS** w obszarze **kontenera regiony**, a następnie wybierz pozycję **Edytuj plik startowy regionu** z okienka **Akcje** . Przewiń w dół do właściwości SQL i wprowadź **bankdemo** dla **nazwy zasobu XA** lub użyj wielokropka, aby go zaznaczyć.

2. Kliknij ikonę **Zapisz** , aby zapisać zmiany.

3. W okienku **konsoli** kliknij prawym przyciskiem myszy **region BANKDEMO CICS** , a następnie wybierz **region Start/Stop**.

4. W dolnej części pola **Start/Stop region** , które pojawia się w środkowym okienku, wybierz pozycję **Rozpocznij**. Po kilku sekundach zostanie uruchomiony region.

     ![Okno uruchamiania/zatrzymywania SQL](media/11-demo-sql.png)

     ![CICS region BANKDEMO — ekran uruchomiony](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Tworzenie odbiornika

Utwórz odbiornik dla sesji protokołu TN3270, które uzyskują dostęp do aplikacji BankDemo.

1. W okienku po lewej stronie rozwiń pozycję **edytory konfiguracji** i wybierz pozycję **odbiornik**.

2. Kliknij ikonę **Otwórz plik** i wybierz plik **seelistener.exe.config** . Ten plik zostanie wyedytowany i będzie ładowany za każdym razem, gdy zostanie uruchomiony serwer przedsiębiorstwa.

3. Zwróć uwagę na to, że dwa zdefiniowane wcześniej regiony (ESDEMO i JCLDEMO).

4. Aby utworzyć nowy region dla BANKDEMO, kliknij prawym przyciskiem myszy **regiony** i wybierz polecenie **Dodaj region**.

5. Wybierz **region BANKDEMO**.

6. Dodaj kanał protokołu TN3270, klikając prawym przyciskiem myszy **region BANKDEMO** i wybierając pozycję **Dodaj kanał**.

7. W obszarze **Nazwa** wprowadź **protokołu TN3270**. W obszarze **port** wprowadź **9024**. Aplikacja ESDEMO korzysta z portu 9230, dlatego należy użyć innego portu.

8. Aby zapisać plik, kliknij ikonę **zapisywania** lub wybierz **plik** \> **Zapisz**.

9. Aby uruchomić odbiornik, kliknij ikonę **Rozpocznij odbiornik** lub wybierz pozycję **Opcje** \> **Uruchom odbiornik**.

     ![Okna edytora konfiguracji odbiornika](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Konfigurowanie Rumba w celu uzyskania dostępu do aplikacji BankDemo

Ostatnim krokiem, który należy wykonać, jest skonfigurowanie sesji 3270 przy użyciu rumba, emulatora 3270. Ten krok pozwala uzyskać dostęp do aplikacji BankDemo za pomocą utworzonego odbiornika.

1. Z menu **Start** systemu Windows uruchom program Rumba Desktop.

2. W obszarze menu **połączenia** wybierz pozycję **protokołu TN3270**.

3. Kliknij przycisk **Wstaw** i wpisz **127.0.0.1** dla adresu IP i **9024** dla portu zdefiniowanego przez użytkownika.

4. W dolnej części okna dialogowego kliknij pozycję **Połącz**. Zostanie wyświetlony ekran Black CICS.

5. Wpisz **Bank** , aby wyświetlić początkowy ekran 3270 dla aplikacji BankDemo.

6. W polu Identyfikator użytkownika wpisz **B0001** i hasło, wpisz dowolne. Zostanie otwarty pierwszy ekran BANK20.

![Mainframe Wyświetl ekran powitalny ekranu ](media/14-demo.png)
 ![ wyświetlania-Rumba-podsystem demonstracyjny](media/15-demo.png)

Gratulacje! Teraz uruchamiasz aplikację CICS na platformie Azure przy użyciu programu Micro Focus Enterprise Server.

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie serwera korporacyjnego w kontenerach platformy Docker na platformie Azure](run-enterprise-server-container.md)
- [Migracja komputera mainframe — Portal](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [Virtual Machines](../../../linux/overview.md)
- [Rozwiązywanie problemów](../../../troubleshooting/index.yml)
- [Sztuczna komputera mainframe do migracji na platformę Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
