---
title: Korzystanie z uwierzytelniania wieloskładnikowego Azure Active Directory
description: Azure SQL Database, wystąpienie zarządzane usługi Azure SQL i usługa Azure Synapse Analytics obsługują połączenia z SQL Server Management Studio (SSMS) przy użyciu uwierzytelniania uniwersalnego Active Directory.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: 2a3e1e7279e915c0c5992190ef0c8d1d83880dbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91596149"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Korzystanie z uwierzytelniania wieloskładnikowego Azure Active Directory
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, wystąpienie zarządzane usługi Azure SQL i usługa Azure Synapse Analytics obsługują połączenia z [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) przy użyciu uwierzytelniania *wieloskładnikowego Azure Active Directory* . W tym artykule omówiono różnice między różnymi opcjami uwierzytelniania, a także ograniczenia związane z użyciem uwierzytelniania uniwersalnego w Azure Active Directory (Azure AD) dla usługi Azure SQL.

**Pobierz najnowszy program SSMS** — na komputerze klienckim Pobierz najnowszą wersję programu ssms z [SQL Server Management Studio pobierania (SSMS)](https://aka.ms/ssms). 

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]


Aby zapoznać się ze wszystkimi funkcjami omówionymi w tym artykule, należy użyć co najmniej 2017 lipca w wersji 17,2. Ostatnie połączenie okno dialogowe powinno wyglądać podobnie do poniższej ilustracji:

  ![Zrzut ekranu okna dialogowego Łączenie z serwerem w SQL Server Management Studio, pokazujący ustawienia Typ serwera, nazwa serwera i uwierzytelnianie.](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)

## <a name="authentication-options"></a>Opcje uwierzytelniania

Istnieją dwa nieinteraktywne modele uwierzytelniania dla usługi Azure AD, które mogą być używane w wielu różnych aplikacjach (ADO.NET, JDCB, ODC itd.). Te dwie metody nigdy nie powodują wyskakujących okien dialogowych:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

Metoda interaktywna obsługująca również usługę Azure Multi-Factor Authentication (MFA) to: 

- `Azure Active Directory - Universal with MFA`

Usługa Azure MFA zabezpiecza dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania. Zapewnia silne uwierzytelnianie dzięki szerokiemu zakresowi opcji łatwej weryfikacji (połączenie telefoniczne, wiadomość tekstowa, kartach inteligentnych z numerem PIN lub powiadomieniem aplikacji mobilnej), co pozwala użytkownikom na wybranie preferowanej metody. Interaktywna usługa MFA z usługą Azure AD może spowodować wyskakujące okno dialogowe umożliwiające weryfikację.

Aby uzyskać opis Multi-Factor Authentication platformy Azure, zobacz [Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md).
Aby uzyskać instrukcje dotyczące konfiguracji, zobacz [konfigurowanie Azure SQL Database usługi uwierzytelniania wieloskładnikowego dla SQL Server Management Studio](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nazwa domeny usługi Azure AD lub parametr identyfikatora dzierżawy

Począwszy od programu [SSMS w wersji 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), użytkownicy, którzy zostali zaimportowani do bieżącej usługi Azure AD z innych aktywnych katalogów platformy Azure jako użytkownicy gościa, mogą podać nazwę domeny usługi Azure AD lub identyfikator dzierżawy po nawiązaniu połączenia. Użytkownicy-Goście obejmują użytkowników zaproszonych z innych reklam systemu Azure, kont Microsoft, takich jak outlook.com, hotmail.com, live.com lub innych kont, takich jak gmail.com. Te informacje umożliwiają `Azure Active Directory - Universal with MFA` uwierzytelnianie w celu zidentyfikowania prawidłowego urzędu uwierzytelniającego. Ta opcja jest również wymagana do obsługi kont Microsoft (MSA), takich jak outlook.com, hotmail.com, live.com lub non-MSA. 

Wszyscy użytkownicy-Goście, którzy chcą być uwierzytelniani przy użyciu uwierzytelniania uniwersalnego, muszą wprowadzić nazwę domeny usługi Azure AD lub identyfikator dzierżawy. Ten parametr reprezentuje bieżącą nazwę domeny usługi Azure AD lub identyfikator dzierżawy, z którym jest skojarzony serwer logiczny usługi Azure SQL. Na przykład jeśli serwer logiczny SQL jest skojarzony z domeną usługi Azure AD `contosotest.onmicrosoft.com` , gdzie użytkownik `joe@contosodev.onmicrosoft.com` jest hostowany jako zaimportowany użytkownik z domeny usługi Azure AD `contosodev.onmicrosoft.com` , nazwa domeny wymagana do uwierzytelnienia tego użytkownika to `contosotest.onmicrosoft.com` . Jeśli użytkownik jest natywnym użytkownikiem usługi Azure AD skojarzonego z serwerem logicznym SQL, a nie jest kontem MSA, nie jest wymagana żadna nazwa domeny ani identyfikator dzierżawy. Aby wprowadzić parametr (począwszy od programu SSMS w wersji 17,2):


1. Otwórz połączenie w programie SSMS. Wprowadź nazwę serwera, a następnie wybierz pozycję **Azure Active Directory — uniwersalna przy użyciu uwierzytelniania MFA** . Dodaj **nazwę użytkownika** , za pomocą którego chcesz się zalogować.
1. Zaznacz pole **opcji** i przejdź do karty **Właściwości połączenia** . W oknie dialogowym **łączenie z bazą danych** wypełnij okno dialogowe bazy danych. Sprawdź **nazwę domeny usługi AD lub pole Identyfikator dzierżawy** , a następnie podaj urząd uwierzytelniający, taki jak nazwa domeny (**contosotest.onmicrosoft.com**) lub identyfikator GUID identyfikatora dzierżawy. 

   ![Zrzut ekranu przedstawiający kartę właściwości połączenia z wyróżnioną ustawieniami połączenia z bazą danych i nazwą domeny usługi AD lub IDENTYFIKATORem dzierżawy.](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Jeśli używasz programu SSMS 18. x lub nowszego, nazwa domeny lub identyfikator dzierżawy usługi AD nie są już potrzebne dla użytkowników-Gości, ponieważ program automatycznie rozpoznaje ją.

   ![Zrzut ekranu karty właściwości połączenia w oknie dialogowym Łączenie z serwerem w S S M S. w polu Połącz z bazą danych jest wybrana wartość "Moja baza danych".](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Obsługa usługi Azure AD Business dla firm

> [!IMPORTANT]
> W **publicznej wersji zapoznawczej**użytkownicy-Goście mogą łączyć się z Azure SQL Database, wystąpieniem zarządzanym SQL i platformą Azure Synapse bez konieczności, aby była częścią grupy. Aby uzyskać więcej informacji, zobacz [Tworzenie użytkowników Gości usługi Azure AD i Ustawianie ich jako administratora usługi Azure AD](authentication-aad-guest-users.md).

Użytkownicy usługi Azure AD, które są obsługiwane przez scenariusze B2B usługi Azure AD jako użytkownicy-Goście (Zobacz artykuł [co to jest współpraca z usługą Azure B2B](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)), mogą łączyć się z SQL Database i Azure Synapse tylko jako część członków grupy utworzonej w skojarzonej usłudze Azure AD i zamapowane ręcznie przy użyciu instrukcji [Create User (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) w danej bazie danych. Na przykład jeśli `steve@gmail.com` zostanie zaproszony do usługi Azure AD `contosotest` (z domeną usługi Azure AD `contosotest.onmicrosoft.com` ), Grupa usługi Azure AD, taka jak `usergroup` musi zostać utworzona w usłudze Azure AD, która zawiera `steve@gmail.com` członka. Następnie należy utworzyć tę grupę dla konkretnej bazy danych (na przykład `MyDatabase` ) przez administratora SQL usługi Azure AD lub właściciela usługi Azure AD, wykonując instrukcję języka Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . 

Po utworzeniu użytkownika bazy danych użytkownik `steve@gmail.com` może zalogować się `MyDatabase` przy użyciu opcji uwierzytelniania programu SSMS `Azure Active Directory – Universal with MFA` . Domyślnie `usergroup` ma tylko uprawnienie łączenie. Każdy dodatkowy dostęp do danych musi zostać [udzielony](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) w bazie danych przez użytkownika z wystarczającymi uprawnieniami. 

> [!NOTE]
> W przypadku programu SSMS 17. x przy użyciu `steve@gmail.com` jako użytkownika-gościa należy zaznaczyć pole **nazwy domeny usługi AD lub identyfikatora dzierżawy** i dodać nazwę domeny usługi AD `contosotest.onmicrosoft.com` w oknie dialogowym **Właściwości połączenia** . Opcja **nazwy domeny usługi AD lub identyfikatora dzierżawy** jest obsługiwana tylko w przypadku uwierzytelniania **wieloskładnikowego Azure Active Directory uniwersalnym** . W przeciwnym razie pole wyboru jest wyszarzone.

## <a name="universal-authentication-limitations"></a>Ograniczenia uwierzytelniania uniwersalnego

- Narzędzie SSMS i SqlPackage.exe są jedynymi narzędziami obecnie włączonych dla usługi MFA za pomocą uwierzytelniania uniwersalnego Active Directory.
- Program SSMS w wersji 17,2 obsługuje współbieżny dostęp do wielodostępnego użytkownika przy użyciu uwierzytelniania uniwersalnego za pomocą usługi MFA. W przypadku programu SSMS w wersji 17,0 i 17,1 narzędzie ogranicza logowanie do wystąpienia programu SSMS przy użyciu uwierzytelniania uniwersalnego na pojedynczym koncie Azure Active Directory. Aby zalogować się jako inne konto usługi Azure AD, musisz użyć innego wystąpienia programu SSMS. To ograniczenie jest ograniczone do Active Directory uwierzytelniania uniwersalnego; Możesz zalogować się do innego serwera przy użyciu `Azure Active Directory - Password` uwierzytelniania, `Azure Active Directory - Integrated` uwierzytelniania lub `SQL Server Authentication` .
- Program SSMS obsługuje uwierzytelnianie uniwersalne Active Directory w przypadku Eksplorator obiektów, edytora zapytań i wizualizacji magazynu zapytań.
- Program SSMS w wersji 17,2 zapewnia obsługę kreatora DacFx dla bazy danych eksport/Extract/Deploy. Gdy określony użytkownik zostanie uwierzytelniony za pośrednictwem okna dialogowego uwierzytelniania początkowego przy użyciu uwierzytelniania uniwersalnego, Kreator DacFx działa tak samo jak w przypadku wszystkich innych metod uwierzytelniania.
- Projektant tabel programu SSMS nie obsługuje uwierzytelniania uniwersalnego.
- Nie ma dodatkowych wymagań dotyczących oprogramowania dla Active Directory uwierzytelniania uniwersalnego, z tą różnicą, że należy użyć obsługiwanej wersji programu SSMS.  
- Skorzystaj z następującego linku, aby uzyskać najnowszą wersję usługi Active Directory Authentication Library (ADAL) na potrzeby uwierzytelniania uniwersalnego: [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać instrukcje dotyczące konfiguracji, zobacz [konfigurowanie Azure SQL Database usługi uwierzytelniania wieloskładnikowego dla SQL Server Management Studio](authentication-mfa-ssms-configure.md).
- Przyznaj innym osobom dostęp do bazy danych: [SQL Database uwierzytelniania i autoryzacji: udzielanie dostępu](logins-create-manage.md)  
- Upewnij się, że inne osoby mogą łączyć się za pośrednictwem zapory: [Skonfiguruj regułę zapory na poziomie serwera przy użyciu Azure Portal](firewall-configure.md)  
- [Konfigurowanie i Zarządzanie uwierzytelnianiem Azure Active Directory za pomocą SQL Database lub Azure Synapse](authentication-aad-configure.md)
- [Tworzenie użytkowników-gości usługi Azure AD i ustawianie jako administratora usługi Azure AD](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importowanie pliku BACPAC do nowej bazy danych](database-import.md)  
- [Eksportowanie bazy danych do pliku BACPAC](database-export.md)  
- Interfejs C# interfejsu [IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- W przypadku korzystania **z Azure Active Directory — uniwersalne z** uwierzytelnianiem MFA, śledzenie ADAL jest dostępne od programu [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Domyślnie wyłączone jest śledzenie ADAL przy użyciu **narzędzi**, menu **opcji** , w obszarze **usługi platformy Azure**, **Azure Cloud**, **ADAL okno dane wyjściowe poziom śledzenia**, a następnie przez włączenie **danych wyjściowych**  w menu **Widok** . Ślady są dostępne w oknie danych wyjściowych w przypadku wybrania **opcji Azure Active Directory**.  
