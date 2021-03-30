---
title: Instalowanie Azure AD Connect przy użyciu istniejącej bazy danych ADSync | Microsoft Docs
description: W tym temacie opisano, jak używać istniejącej bazy danych ADSync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8324b82a05d7e78772e0b0b6de3a9bfaa183411
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91265395"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Instalowanie programu Azure AD Connect przy użyciu istniejącej bazy danych programu ADSync
Azure AD Connect wymaga bazy danych SQL Server do przechowywania danych. Możesz użyć domyślnego SQL Server 2012 Express LocalDB z zainstalowanym programem Azure AD Connect lub użyć własnej pełnej wersji programu SQL. Wcześniej podczas instalacji Azure AD Connect była zawsze tworzona nowa baza danych o nazwie ADSync. W przypadku Azure AD Connect w wersji 1.1.613.0 (lub późniejsza) można zainstalować Azure AD Connect, wskazując na istniejącą bazę danych ADSync.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Zalety korzystania z istniejącej bazy danych ADSync
Wskazując na istniejącą bazę danych ADSync:

- Z wyjątkiem informacji o poświadczeniach Konfiguracja synchronizacji przechowywana w bazie danych ADSync (w tym niestandardowe reguły synchronizacji, łączniki, filtrowanie i konfiguracja funkcji opcjonalnych) jest automatycznie odzyskiwana i używana podczas instalacji. Poświadczenia używane przez Azure AD Connect do synchronizowania zmian z lokalnymi usługami AD i Azure AD są szyfrowane i są dostępne tylko na poprzednim serwerze Azure AD Connect.
- Odzyskane zostaną również wszystkie dane tożsamości (skojarzone ze spacjami łączników i Metaverse) oraz pliki cookie synchronizacji przechowywane w bazie danych ADSync. Nowo zainstalowany serwer Azure AD Connect może kontynuować synchronizację z miejsca, w którym poprzedni serwer Azure AD Connect pozostawiony, zamiast konieczności przeprowadzania pełnej synchronizacji.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scenariusze, w których jest korzystne użycie istniejącej bazy danych ADSync
Te korzyści są przydatne w następujących scenariuszach:


- Masz istniejące wdrożenie Azure AD Connect. Istniejący serwer Azure AD Connect nie działa już, ale serwer SQL zawierający bazę danych ADSync nadal działa. Można zainstalować nowy serwer Azure AD Connect i wskazać go w istniejącej bazie danych ADSync. 
- Masz istniejące wdrożenie Azure AD Connect. Program SQL Server zawierający bazę danych ADSync nie działa. Jednak masz ostatnią kopię zapasową bazy danych programu. Bazę danych ADSync można przywrócić najpierw na nowy serwer SQL. Następnie można zainstalować nowy serwer Azure AD Connect i wskazać go przywróconą bazę danych ADSync.
- Istnieje już wdrożenie Azure AD Connect, które korzysta z LocalDB. Ze względu na limit 10 GB narzucony przez LocalDB, chcesz przeprowadzić migrację do pełnego języka SQL. Można utworzyć kopię zapasową bazy danych ADSync z LocalDB i przywrócić ją do programu SQL Server. Następnie można ponownie zainstalować nowy serwer Azure AD Connect i wskazać go przywróconą bazę danych ADSync.
- Podjęto próbę skonfigurowania serwera przejściowego i upewnienia się, że jego konfiguracja jest zgodna z bieżącym aktywnym serwerem. Można utworzyć kopię zapasową bazy danych ADSync i przywrócić ją do innego programu SQL Server. Następnie można ponownie zainstalować nowy serwer Azure AD Connect i wskazać go przywróconą bazę danych ADSync.

## <a name="prerequisite-information"></a>Informacje wymagane wstępnie

Ważne uwagi, które należy wziąć pod uwagę przed kontynuowaniem:

- Upewnij się, że zapoznaj się z wymaganiami wstępnymi dotyczącymi instalacji Azure AD Connect na sprzęcie i wymaganiach wstępnych oraz kont i uprawnień wymaganych do zainstalowania Azure AD Connect. Uprawnienia wymagane do zainstalowania Azure AD Connect przy użyciu trybu "Użyj istniejącej bazy danych" są takie same jak w przypadku instalacji "Custom".
- Wdrażanie Azure AD Connect dla istniejącej bazy danych ADSync jest obsługiwane tylko w przypadku używania pełnego języka SQL. Nie jest on obsługiwany w programie SQL Express LocalDB. Jeśli masz istniejącą bazę danych ADSync w LocalDB, której chcesz użyć, musisz najpierw utworzyć kopię zapasową bazy danych ADSync (LocalDB) i przywrócić ją do pełnego języka SQL. Następnie można wdrożyć Azure AD Connect dla przywróconej bazy danych za pomocą tej metody.
- Wersja Azure AD Connect używana do instalacji musi spełniać następujące kryteria:
    - 1.1.613.0 lub nowszy, i
    - Ta sama lub większa niż wersja Azure AD Connect ostatnio używana z bazą danych ADSync. Jeśli wersja Azure AD Connect używana do instalacji jest wyższa niż wersja ostatnio używana z bazą danych ADSync, może być wymagana pełna synchronizacja.  Pełna synchronizacja jest wymagana w przypadku zmiany schematu lub reguły synchronizacji między dwiema wersjami. 
- Użyta baza danych ADSync powinna zawierać stan synchronizacji stosunkowo niedawniej. Ostatnia aktywność synchronizacji z istniejącą bazą danych ADSync powinna przypadać w ciągu ostatnich trzech tygodni.
- Podczas instalowania Azure AD Connect przy użyciu metody "Użyj istniejącej bazy danych" metoda logowania skonfigurowana na poprzednim serwerze Azure AD Connect nie jest zachowywana. Ponadto nie można skonfigurować metody logowania podczas instalacji. Metodę logowania można skonfigurować tylko po zakończeniu instalacji.
- Wiele serwerów Azure AD Connect współużytkuje tę samą bazę danych ADSync. Metoda "Użyj istniejącej bazy danych" umożliwia ponowne użycie istniejącej bazy danych ADSync na nowym serwerze Azure AD Connect. Nie obsługuje udostępniania.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Procedura instalacji Azure AD Connect z trybem "Użyj istniejącej bazy danych"
1.  Pobierz instalatora Azure AD Connect (AzureADConnect.MSI) do systemu Windows Server. Kliknij dwukrotnie Instalatora Azure AD Connect, aby rozpocząć instalowanie Azure AD Connect.
2.  Po zakończeniu instalacji pliku MSI kreator programu Azure AD Connect rozpocznie instalację w trybie ekspresowym. Zamknij ekran, klikając ikonę zakończenia.
![Zrzut ekranu przedstawiający stronę "Witamy w usłudze Azure A D Connect" z opcją "Ustawienia ekspresowe" w wyróżnionym menu po lewej stronie.](./media/how-to-connect-install-existing-database/db1.png)
3.  Uruchom nowy wiersz polecenia lub sesję programu PowerShell. Przejdź do folderu "C:\Program Files\Microsoft Azure Active Directory Connect". Uruchom polecenie .\AzureADConnect.exe /useexistingdatabase w celu uruchomienia kreatora programu Azure AD Connect w trybie instalacji „Użyj istniejącej bazy danych”.

> [!NOTE]
> Użyj przełącznika **/UseExistingDatabase** tylko wtedy, gdy baza danych zawiera już dane z wcześniejszej instalacji Azure AD Connect. Na przykład podczas przechodzenia z lokalnej bazy danych do pełnej bazy danych SQL Server lub gdy serwer Azure AD Connect został odbudowany i przywrócono kopię zapasową bazy danych SQL ADSync z wcześniejszej instalacji programu Azure AD Connect. Jeśli baza danych jest pusta, czyli nie zawiera żadnych danych z poprzedniej instalacji Azure AD Connect, Pomiń ten krok.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. Zobaczysz ekran powitalny programu Azure AD Connect. Gdy zaakceptujesz postanowienia licencyjne i uwagi na temat ochrony prywatności, kliknij pozycję **Kontynuuj**.
   ![Zrzut ekranu przedstawiający stronę "Witamy w usłudze Azure A D Connect"](./media/how-to-connect-install-existing-database/db3.png)
1. Na ekranie **Instalowanie wymaganych składników** włączona jest opcja **Użyj istniejącego serwera SQL Server**. Określ nazwę serwera SQL Server hostującego bazę danych programu ADSync. Jeśli wystąpienie aparatu SQL używane do hostowania bazy danych programu ADSync nie jest domyślnym wystąpieniem serwera SQL Server, musisz określić nazwę wystąpienia aparatu SQL. Ponadto jeśli nie jest włączone przeglądanie SQL, musisz też określić numer portu wystąpienia aparatu SQL. Na przykład:         
   ![Zrzut ekranu przedstawiający stronę "Instalowanie składników wymaganych".](./media/how-to-connect-install-existing-database/db4.png)           

1. Na ekranie **Łączenie z usługą Azure AD** musisz podać poświadczenia administratora globalnego katalogu usługi Azure AD. Zaleca się użycie konta w domyślnej domenie onmicrosoft.com. To konto służy tylko do tworzenia konta usługi w usłudze Azure AD i nie jest używane po zakończeniu pracy kreatora.
   ![Połączenie](./media/how-to-connect-install-existing-database/db5.png)
 
1. Na ekranie **Podłączanie katalogów** istniejący las usługi AD skonfigurowany na potrzeby synchronizacji katalogów jest wyświetlany z ikoną czerwonego krzyżyka. Aby zsynchronizować zmiany z lokalnego lasu usługi AD, wymagane jest konto usługi AD DS. Kreator programu Azure AD Connect nie może uzyskać poświadczeń konta usługi AD DS przechowywanych w bazie danych programu ADSync, ponieważ poświadczenia są zaszyfrowane i mogą zostać odszyfrowane tylko przez poprzedni serwer programu Azure AD Connect. Kliknij pozycję **Zmień poświadczenia**, aby określić konto usługi AD DS dla lasu usługi AD.
   ![Katalogi](./media/how-to-connect-install-existing-database/db6.png)
 
1. W wyskakującym oknie dialogowym możesz albo (i) podać poświadczenia administratora przedsiębiorstwa i pozwolić programowi Azure AD Connect utworzyć konto usługi AD DS, albo (ii) samodzielnie utworzyć konto usługi AD DS i podać jego poświadczenia w programie Azure AD Connect. Po wybraniu opcji i podaniu niezbędnych poświadczeń kliknij przycisk **OK** w celu zamknięcia wyskakującego okna dialogowego.
   ![Zrzut ekranu przedstawiający okno dialogowe wyskakujące "A D konto lasu" z wybraną opcją "Utwórz nowe konto D".](./media/how-to-connect-install-existing-database/db7.png)
 
1. Po podaniu poświadczeń ikona czerwonego krzyżyka jest zastępowana ikoną zielonego znacznika wyboru. Kliknij przycisk **Dalej**.
   ![Zrzut ekranu przedstawiający stronę "łączenie katalogów".](./media/how-to-connect-install-existing-database/db8.png)
 
1. Na ekranie **gotowy do konfiguracji** kliknij przycisk **Instaluj**.
   ![Powitanie](./media/how-to-connect-install-existing-database/db9.png)
 
1. Po zakończeniu instalacji serwer programu Azure AD Connect jest automatycznie włączany dla trybu przejściowego. Przed wyłączeniem trybu przejściowego zaleca się przejrzenie konfiguracji serwera i oczekujących operacji eksportowania pod kątem nieoczekiwanych zmian. 

## <a name="post-installation-tasks"></a>Zadania poinstalacyjne
Podczas przywracania kopii zapasowej bazy danych utworzonej przez wersję Azure AD Connect wcześniejszą niż 1.2.65.0 serwer tymczasowy automatycznie wybierze metodę logowania **nie Konfiguruj**. Mimo że preferencje synchronizacji skrótów haseł i zapisywania zwrotnego haseł zostaną przywrócone, należy zmienić metodę logowania, aby odpowiadała innym zasadom obowiązującym dla aktywnego serwera synchronizacji.  Niewykonanie tych kroków może uniemożliwić użytkownikom zalogowanie się, jeśli ten serwer przestanie być aktywny.  

Poniższa tabela umożliwia zweryfikowanie wszelkich dodatkowych kroków, które są wymagane.

|Cecha|Kroki|
|-----|-----|
|Synchronizacja skrótów haseł| Ustawienia synchronizacji skrótów haseł i zapisywania zwrotnego haseł są w pełni przywrócone dla wersji Azure AD Connect począwszy od 1.2.65.0.  Jeśli przywracasz przy użyciu starszej wersji Azure AD Connect, przejrzyj ustawienia opcji synchronizacji tych funkcji, aby upewnić się, że są one zgodne z aktywnym serwerem synchronizacji.  Nie trzeba wykonywać żadnych innych czynności konfiguracyjnych.|
|Federacja z usługami AD FS|Uwierzytelnianie platformy Azure będzie nadal korzystać z zasad AD FS skonfigurowanych dla aktywnego serwera synchronizacji.  Jeśli używasz Azure AD Connect do zarządzania farmą AD FS, możesz opcjonalnie zmienić metodę logowania na AD FS Federacji w przygotowaniu dla serwera rezerwy jako aktywnego wystąpienia synchronizacji.   Jeśli na serwerze aktywnej synchronizacji są włączone opcje urządzenia, skonfiguruj te opcje na tym serwerze, uruchamiając zadanie "Konfiguruj opcje urządzenia".|
|Uwierzytelnianie przekazywane i pojedyncze Sign-On pulpitu|Zaktualizuj metodę logowania, aby była zgodna z konfiguracją na serwerze aktywnej synchronizacji.  Jeśli nie zostanie to wykonane przed podwyższeniem poziomu serwera do podstawowego, uwierzytelnianie przekazywane wraz z bezproblemowym logowaniem jednokrotnym zostanie wyłączone, a dzierżawa może zostać zablokowana, jeśli nie masz synchronizacji skrótów haseł. Należy również pamiętać, że po włączeniu uwierzytelniania przekazywanego w trybie przejściowym zostanie zainstalowany, zarejestrowany i uruchomiony jako agent wysokiej dostępności, który będzie akceptował żądania logowania.|
|Federacja z serwerem PingFederate|Uwierzytelnianie platformy Azure będzie nadal korzystać z zasad serwera pingfederate skonfigurowanych dla aktywnego serwera synchronizacji.  Opcjonalnie można zmienić metodę logowania na serwera pingfederate w przygotowaniu dla serwera rezerwy jako aktywnego wystąpienia synchronizacji.  Ten krok może zostać odroczony, dopóki nie trzeba będzie sfederować dodatkowych domen z serwera pingfederate.|

## <a name="next-steps"></a>Następne kroki

- Po zainstalowaniu programu Azure AD Connect możesz [zweryfikować instalację i przypisać licencje](how-to-connect-post-installation.md).
- Dowiedz się więcej na temat funkcji włączonych w ramach instalacji: [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md) oraz [Azure AD Connect Health](how-to-connect-health-sync.md).
- Dowiedz się więcej na te popularne tematy: [harmonogram i sposób włączania synchronizacji](how-to-connect-sync-feature-scheduler.md).
- Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
