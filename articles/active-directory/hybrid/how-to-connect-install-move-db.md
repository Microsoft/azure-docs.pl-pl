---
title: Przenoszenie bazy danych programu Azure AD Connect z serwera SQL Server Express do serwera SQL Server. | Microsoft Docs
description: W tym dokumencie opisano sposób przenoszenia bazy danych programu Azure AD Connect z lokalnego serwera SQL Server Express do zdalnego serwera SQL Server.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 742bc307c90ad58b83b7d4c92f9546b87c163c3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77019285"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>Przenoszenie bazy danych programu Azure AD Connect z serwera SQL Server Express do serwera SQL Server 

W tym dokumencie opisano sposób przenoszenia bazy danych programu Azure AD Connect z lokalnego serwera SQL Server Express do zdalnego serwera SQL Server.  Do wykonania tego zadania można użyć poniższych procedur.

## <a name="about-this-scenario"></a>Informacje o tym scenariuszu
Poniżej przedstawiono krótkie informacje na temat tego scenariusza.  W tym scenariuszu program Azure AD Connect w wersji (1.1.819.0) jest instalowany na pojedynczym kontrolerze domeny z systemem Windows Server 2016.  Do przechowywania bazy danych używa on serwera SQL Server 2012 Express Edition.  Baza danych zostanie przeniesiona na serwer SQL Server 2017.

![Architektura scenariusza](media/how-to-connect-install-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>Przenoszenie bazy danych programu Azure AD Connect
Wykonaj następujące kroki, aby przenieść bazę danych programu Azure AD Connect na zdalny serwer SQL Server.

1. Na serwerze programu Azure AD Connect przejdź do pozycji **Usługi** i zatrzymaj usługę **Microsoft Azure AD Sync**.
2. Zlokalizuj folder **%ProgramFiles%\Microsoft Azure AD Sync\Data** i skopiuj pliki **ADSync. mdf** i **ADSync_log. ldf** do SQL Server zdalnego.
3. Uruchom ponownie usługę **Microsoft Azure AD Sync** na serwerze programu Azure AD Connect.
4. Odinstaluj program Azure AD Connect, przechodząc do pozycji Panel sterowania — Programy — Programy i funkcje.  Wybierz pozycję Microsoft Azure AD Connect i kliknij u góry pozycję Odinstaluj.
5. Na zdalnym serwerze SQL Server otwórz program SQL Server Management Studio.
6. W sekcji Bazy danych kliknij prawym przyciskiem myszy i wybierz pozycję Dołącz.
7. Na ekranie **Dołączanie baz danych** kliknij pozycję **Dodaj** i przejdź do pliku ADSync.mdf.  Kliknij przycisk **OK**.
   ![Dołącz bazę danych](media/how-to-connect-install-move-db/move2.png)

8. Po dołączeniu bazy danych wróć do serwera programu Azure AD Connect i zainstaluj program Azure AD Connect.
9. Po zakończeniu instalacji pliku MSI kreator programu Azure AD Connect rozpocznie instalację w trybie ekspresowym. Zamknij ekran, klikając ikonę zakończenia.
   ![Powitanie](./media/how-to-connect-install-move-db/db1.png)
10. Uruchom nowy wiersz polecenia lub sesję programu PowerShell. Przejdź do folderu \<dysk> \program files\microsoft Azure AD Connect. Uruchom polecenie .\AzureADConnect.exe /useexistingdatabase w celu uruchomienia kreatora programu Azure AD Connect w trybie instalacji „Użyj istniejącej bazy danych”.
    ![Program PowerShell](./media/how-to-connect-install-move-db/db2.png)
11. Zobaczysz ekran powitalny programu Azure AD Connect. Gdy zaakceptujesz postanowienia licencyjne i uwagi na temat ochrony prywatności, kliknij pozycję **Kontynuuj**.
    ![Powitanie](./media/how-to-connect-install-move-db/db3.png)
12. Na ekranie **Instalowanie wymaganych składników** włączona jest opcja **Użyj istniejącego serwera SQL Server**. Określ nazwę serwera SQL Server hostującego bazę danych programu ADSync. Jeśli wystąpienie aparatu SQL używane do hostowania bazy danych programu ADSync nie jest domyślnym wystąpieniem serwera SQL Server, musisz określić nazwę wystąpienia aparatu SQL. Ponadto jeśli nie jest włączone przeglądanie SQL, musisz też określić numer portu wystąpienia aparatu SQL. Przykład:         
    ![Powitanie](./media/how-to-connect-install-move-db/db4.png)           

13. Na ekranie **Łączenie z usługą Azure AD** musisz podać poświadczenia administratora globalnego katalogu usługi Azure AD. Zaleca się użycie konta w domyślnej domenie onmicrosoft.com. To konto służy tylko do tworzenia konta usługi w usłudze Azure AD i nie jest używane po zakończeniu pracy kreatora.
    ![Połączenie](./media/how-to-connect-install-move-db/db5.png)
 
14. Na ekranie **Podłączanie katalogów** istniejący las usługi AD skonfigurowany na potrzeby synchronizacji katalogów jest wyświetlany z ikoną czerwonego krzyżyka. Aby zsynchronizować zmiany z lokalnego lasu usługi AD, wymagane jest konto usługi AD DS. Kreator programu Azure AD Connect nie może uzyskać poświadczeń konta usługi AD DS przechowywanych w bazie danych programu ADSync, ponieważ poświadczenia są zaszyfrowane i mogą zostać odszyfrowane tylko przez poprzedni serwer programu Azure AD Connect. Kliknij pozycję **Zmień poświadczenia**, aby określić konto usługi AD DS dla lasu usługi AD.
    ![Katalogi](./media/how-to-connect-install-move-db/db6.png)
 
 
15. W wyskakującym oknie dialogowym możesz albo (i) podać poświadczenia administratora przedsiębiorstwa i pozwolić programowi Azure AD Connect utworzyć konto usługi AD DS, albo (ii) samodzielnie utworzyć konto usługi AD DS i podać jego poświadczenia w programie Azure AD Connect. Po wybraniu opcji i podaniu niezbędnych poświadczeń kliknij przycisk **OK** w celu zamknięcia wyskakującego okna dialogowego.
    ![Powitanie](./media/how-to-connect-install-move-db/db7.png)
 
 
16. Po podaniu poświadczeń ikona czerwonego krzyżyka jest zastępowana ikoną zielonego znacznika wyboru. Kliknij przycisk **Dalej**.
    ![Powitanie](./media/how-to-connect-install-move-db/db8.png)
 
 
17. Na ekranie **gotowy do konfiguracji** kliknij przycisk **Instaluj**.
    ![Powitanie](./media/how-to-connect-install-move-db/db9.png)
 
 
18. Po zakończeniu instalacji serwer programu Azure AD Connect jest automatycznie włączany dla trybu przejściowego. Przed wyłączeniem trybu przejściowego zaleca się przejrzenie konfiguracji serwera i oczekujących operacji eksportowania pod kątem nieoczekiwanych zmian. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
- [Instalowanie programu Azure AD Connect przy użyciu istniejącej bazy danych programu ADSync](how-to-connect-install-existing-database.md)
- [Instalowanie programu Azure AD Connect za pomocą delegowanych uprawnień administratora usługi SQL](how-to-connect-install-sql-delegation.md)

