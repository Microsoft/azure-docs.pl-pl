---
title: Korzystanie z uwierzytelniania wieloskładnikowego usługi AAD
description: Synapse połączenia obsługiwane przez program SQL Server z SQL Server Management Studio (SSMS) przy użyciu uwierzytelniania uniwersalnego Active Directory.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.custom: has-adal-ref
ms.openlocfilehash: a430bb41f88bfec1299d52d8237c9534b28acc5d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093382"
---
# <a name="use-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Korzystanie z uwierzytelniania wieloskładnikowego usługi AAD za pomocą Synapse SQL (Obsługa programu SSMS dla usługi MFA)

Synapse połączenia obsługiwane przez program SQL Server z SQL Server Management Studio (SSMS) przy użyciu *uwierzytelniania uniwersalnego Active Directory*. 

W tym artykule omówiono różnice między różnymi opcjami uwierzytelniania, a także ograniczenia związane z uwierzytelnianiem uniwersalnym. 

**Pobierz najnowszy program SSMS** — na komputerze klienckim Pobierz najnowszą wersję programu ssms z [SQL Server Management Studio pobierania (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Aby zapoznać się ze wszystkimi funkcjami omówionymi w tym artykule, należy użyć co najmniej 2017 lipca w wersji 17,2.  Ostatnie połączenie okno dialogowe powinno wyglądać podobnie do poniższej ilustracji:

![Zrzut ekranu przedstawia okno dialogowe łączenie z serwerem, w którym można wybrać nazwę serwera i opcję uwierzytelniania.](./media/mfa-authentication/1mfa-universal-connect.png "Kończy działanie pola Nazwa użytkownika.")  

## <a name="the-five-authentication-options"></a>Pięć opcji uwierzytelniania  

Active Directory uniwersalne uwierzytelnianie obsługuje dwie nieinteraktywne metody uwierzytelniania:
    - `Active Directory - Password` ponowne
    - `Active Directory - Integrated` ponowne

Istnieją również dwa nieinteraktywne modele uwierzytelniania, które mogą być używane w wielu różnych aplikacjach (ADO.NET, JDCB, ODC itp.). Te dwie metody nigdy nie powodują wyskakujących okien dialogowych:

- `Active Directory - Password`
- `Active Directory - Integrated`

Interaktywna Metoda obejmuje również obsługę uwierzytelniania wieloskładnikowego (MFA) w usłudze Azure:

- `Active Directory - Universal with MFA`

Usługa Azure MFA zabezpiecza dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania. Zapewnia silne uwierzytelnianie dzięki szerokiemu zakresowi opcji łatwej weryfikacji (połączenie telefoniczne, wiadomość tekstowa, kartach inteligentnych z numerem PIN lub powiadomieniem aplikacji mobilnej), co pozwala użytkownikom na wybranie preferowanej metody. Interaktywna usługa MFA z usługą Azure AD może spowodować wyskakujące okno dialogowe umożliwiające weryfikację.

Opis Multi-Factor Authentication można znaleźć w temacie [Multi-Factor Authentication](../../active-directory/authentication//concept-mfa-howitworks.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nazwa domeny usługi Azure AD lub parametr identyfikatora dzierżawy

Począwszy od programu [SSMS w wersji 17](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), użytkownicy, którzy zostali zaimportowani do bieżącego Active Directory z innych katalogów usługi Azure Active Directory jako gość, mogą podać nazwę domeny usługi Azure AD lub identyfikator dzierżawy podczas nawiązywania połączenia. 

Użytkownicy-Goście obejmują użytkowników zaproszonych z innych reklam systemu Azure, kont Microsoft, takich jak outlook.com, hotmail.com, live.com lub innych kont, takich jak gmail.com. Te informacje umożliwiają **Active Directory uniwersalnego z uwierzytelnianiem MFA** w celu zidentyfikowania prawidłowego urzędu uwierzytelniającego. Ta opcja jest również wymagana do obsługi kont Microsoft (MSA), takich jak outlook.com, hotmail.com, live.com lub non-MSA. 

Wszyscy ci użytkownicy, którzy chcą być uwierzytelniani przy użyciu uwierzytelniania uniwersalnego, muszą wprowadzić nazwę domeny usługi Azure AD lub identyfikator dzierżawy. Ten parametr reprezentuje bieżącą nazwę domeny i identyfikator dzierżawy usługi Azure AD, z którą jest połączony serwer platformy Azure. 

Na przykład jeśli serwer platformy Azure jest skojarzony z domeną usługi Azure AD `contosotest.onmicrosoft.com` , w której użytkownik `joe@contosodev.onmicrosoft.com` jest hostowany jako zaimportowany użytkownik z domeny usługi Azure AD `contosodev.onmicrosoft.com` , nazwa domeny wymagana do uwierzytelnienia tego użytkownika to `contosotest.onmicrosoft.com` . 

Jeśli użytkownik jest natywnym użytkownikiem usługi Azure AD połączonej z serwerem platformy Azure i nie jest kontem MSA, nie jest wymagana żadna nazwa domeny ani identyfikator dzierżawy. 

Aby wprowadzić parametr (począwszy od programu SSMS w wersji 17,2), w oknie dialogowym **łączenie z bazą danych** Uzupełnij okno dialogowe, wybierając pozycję **Active Directory-Universal z** uwierzytelnianiem MFA, wybierz pozycję **Opcje**, Wypełnij pole **Nazwa użytkownika** , a następnie wybierz kartę **Właściwości połączenia** . 

Sprawdź **nazwę domeny usługi AD lub pole Identyfikator dzierżawy** , a następnie podaj urząd uwierzytelniający, taki jak nazwa domeny (**contosotest.onmicrosoft.com**) lub identyfikator GUID identyfikatora dzierżawy.  

   ![Zrzut ekranu przedstawia pozycję Połącz z serwerem na karcie Właściwości połączenia z wprowadzonymi wartościami.](./media/mfa-authentication/mfa-tenant-ssms.png)

W przypadku korzystania z programu SSMS 18. x lub nowszego, nazwa domeny usługi AD lub identyfikator dzierżawy nie są już potrzebne dla użytkowników-Gości, ponieważ program automatycznie rozpoznaje ją.

   ![MFA — dzierżawca — SSMS](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Obsługa usługi Azure AD Business dla firm   
Użytkownicy usługi Azure AD obsługiwani przez scenariusze B2B usługi Azure AD jako użytkownicy-Goście (Zobacz artykuł [co to jest współpraca w usłudze Azure B2B](../../active-directory/b2b/what-is-b2b.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , która może łączyć się z usługą Synapse SQL tylko jako część elementów członkowskich grupy utworzonej w bieżącej usłudze Azure AD i zamapowana ręcznie przy użyciu instrukcji języka Transact-SQL `CREATE USER` w danej bazie danych. 

Na przykład jeśli `steve@gmail.com` zostanie zaproszony do usługi Azure AD `contosotest` (z domeną usługi Azure AD `contosotest.onmicrosoft.com` ), Grupa usługi Azure AD, taka jak `usergroup` musi zostać utworzona w usłudze Azure AD, która zawiera `steve@gmail.com` członka. Następnie należy utworzyć tę grupę dla konkretnej bazy danych (to jest baza danych) za pomocą administratora SQL usługi Azure AD lub roli DBO usługi Azure AD przez wykonanie instrukcji języka Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . 

Po utworzeniu użytkownika bazy danych użytkownik `steve@gmail.com` może zalogować się `MyDatabase` przy użyciu opcji uwierzytelniania programu SSMS `Active Directory – Universal with MFA support` . 

Domyślnie użytkownik ma tylko uprawnienie łączenie i dalsze dostęp do danych, które będą musiały zostać przyznane w zwykły sposób. 

Jako użytkownik-Gość `steve@gmail.com` należy zaznaczyć pole wyboru i dodać nazwę domeny usługi AD `contosotest.onmicrosoft.com` w oknie dialogowym **Właściwości połączenia** programu SSMS. Opcja **nazwy domeny lub identyfikatora dzierżawy usługi Active Directory** jest obsługiwana tylko dla opcji połączenia uniwersalnego z usługą MFA, w przeciwnym razie jest wyszarzona.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>Ograniczenia uwierzytelniania uniwersalnego dla Synapse SQL

- Narzędzie SSMS i SqlPackage.exe są jedynymi narzędziami obecnie włączonych dla usługi MFA za pomocą uwierzytelniania uniwersalnego Active Directory.
- Program SSMS w wersji 17,2 obsługuje współbieżne dostęp użytkowników przy użyciu uwierzytelniania uniwersalnego w ramach usługi MFA. Wersje 17,0 i 17,1, ograniczone logowanie do wystąpienia programu SSMS przy użyciu uwierzytelniania uniwersalnego na pojedynczym koncie Azure Active Directory. Aby zalogować się jako inne konto usługi Azure AD, musisz użyć innego wystąpienia programu SSMS. To ograniczenie jest ograniczone do Active Directory uwierzytelniania uniwersalnego. możesz zalogować się do różnych serwerów przy użyciu uwierzytelniania za pomocą hasła Active Directory, Active Directory uwierzytelniania zintegrowanego lub SQL Server uwierzytelniania).
- Program SSMS obsługuje uwierzytelnianie uniwersalne Active Directory w przypadku Eksplorator obiektów, edytora zapytań i wizualizacji magazynu zapytań.
- Program SSMS w wersji 17,2 zapewnia obsługę kreatora DacFx dla bazy danych eksport/Extract/Deploy. Gdy określony użytkownik zostanie uwierzytelniony za pośrednictwem okna dialogowego uwierzytelniania początkowego przy użyciu uwierzytelniania uniwersalnego, Kreator DacFx działa tak samo jak w przypadku wszystkich innych metod uwierzytelniania.
- Projektant tabel programu SSMS nie obsługuje uwierzytelniania uniwersalnego.
- Nie ma dodatkowych wymagań dotyczących oprogramowania dla Active Directory uwierzytelniania uniwersalnego, z tą różnicą, że należy użyć obsługiwanej wersji programu SSMS.  
- Wersja Active Directory Authentication Library (ADAL) dla uwierzytelniania uniwersalnego została zaktualizowana do najnowszej ADAL.dll 3.13.9 dostępna wersja udostępniona. Zobacz [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz artykuł [Connect to Synapse SQL with SQL Server Management Studio](get-started-ssms.md) . 

