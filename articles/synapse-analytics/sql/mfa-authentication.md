---
title: Korzystanie z wieloskładnikowego uwierzytelniania AAD
description: Synapse SQL obsługuje połączenia z programu SQL Server Management Studio (SSMS) przy użyciu uwierzytelniania uniwersalnego usługi Active Directory.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 8e8f17f54f52aef6f552b7c211aa8f0e498b94df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424923"
---
# <a name="using-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Korzystanie z wieloczynnikowego uwierzytelniania AAD z synapse SQL (obsługa SSMS dla usługi MFA)

Synapse SQL obsługuje połączenia z programu SQL Server Management Studio (SSMS) przy użyciu *uwierzytelniania uniwersalnego usługi Active Directory*. 

W tym artykule omówiono różnice między różnymi opcjami uwierzytelniania, a także ograniczenia związane z używaniem uwierzytelniania uniwersalnego. 

**Pobierz najnowsze usługi SSMS** — na komputerze klienckim pobierz najnowszą wersję programu SSMS z [programu Download SQL Server Management Studio (SSMS).](https://msdn.microsoft.com/library/mt238290.aspx) 

**Pobierz najnowsze usługi SSMS** — na komputerze klienckim pobierz najnowszą wersję programu SSMS z [programu Download SQL Server Management Studio (SSMS).](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

W przypadku wszystkich funkcji opisanych w tym artykule należy użyć co najmniej lipca 2017 r., wersja 17.2.  Okno dialogowe Najnowsze połączenie powinno wyglądać podobnie do następującego obrazu:

![1mfa-universal-connect](./media/mfa-authentication/1mfa-universal-connect.png "Kończy pole Nazwa użytkownika.")  

## <a name="the-five-authentication-options"></a>Pięć opcji uwierzytelniania  

Uwierzytelnianie uniwersalne usługi Active Directory obsługuje dwie metody uwierzytelniania nieinterakcyjnego:
    - `Active Directory - Password`Uwierzytelniania
    - `Active Directory - Integrated`Uwierzytelniania

Istnieją również dwa nieinterakcyjne modele uwierzytelniania, które mogą być używane w wielu różnych aplikacjach (ADO.NET, JDCB, ODC itp.). Te dwie metody nigdy nie powodują wyskakujących okienek dialogowych:

- `Active Directory - Password`
- `Active Directory - Integrated`

Interaktywna metoda jest, że obsługuje również azure uwierzytelnianie wieloskładnikowe (MFA) jest:

- `Active Directory - Universal with MFA`

Usługa Azure MFA zabezpiecza dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania. Zapewnia silne uwierzytelnianie dzięki szerokiej gamie łatwych opcji weryfikacji (rozmowa telefoniczna, wiadomość tekstowa, karty inteligentne z pinem lub powiadomienie aplikacji mobilnej), dzięki czemu użytkownicy mogą wybrać preferowaną metodę. Interaktywne uwierzytelnianie wieloskładnikowe z usługą Azure AD może spowodować wyświetlenie okna dialogowego wyskakującym okienkiem w celu weryfikacji.

Aby uzyskać opis uwierzytelniania wieloskładnikowego, zobacz [Uwierzytelnianie wieloskładnikowe](../../active-directory/authentication//concept-mfa-howitworks.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nazwa domeny usługi Azure AD lub parametr identyfikatora dzierżawy

Począwszy od [usługi SSMS w wersji 17](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), użytkownicy zaimportowani do bieżącej usługi Active Directory z innych usług Azure Active Directories jako użytkownicy-goście, mogą podać nazwę domeny usługi Azure AD lub identyfikator dzierżawy podczas nawiązywania połączenia. 

Użytkownicy-goście to użytkownicy zaproszeni z innych usług Ad platformy Azure, kont Microsoft, takich jak outlook.com, hotmail.com, live.com lub innych kont, takich jak gmail.com. Te informacje umożliwiają **programowi Active Directory Universal z uwierzytelnianiem usługi MFA** identyfikowanie prawidłowego urzędu uwierzytelniającego. Ta opcja jest również wymagana do obsługi kont Microsoft (MSA), takich jak konta outlook.com, hotmail.com, live.com lub inne niż MSA. 

Wszyscy ci użytkownicy, którzy chcą być uwierzytelnieni przy użyciu uwierzytelniania uniwersalnego, muszą wprowadzić swoją nazwę domeny usługi Azure AD lub identyfikator dzierżawy. Ten parametr reprezentuje bieżący identyfikator domeny/dzierżawy usługi Azure AD, z który jest połączony serwer Azure Server. 

Jeśli na przykład serwer Azure Server jest `contosotest.onmicrosoft.com` skojarzony z domeną usługi Azure AD, w której użytkownik `joe@contosodev.onmicrosoft.com` jest hostowany jako importowany użytkownik z domeny `contosodev.onmicrosoft.com`usługi Azure AD, nazwa domeny wymagana do uwierzytelnienia tego użytkownika to `contosotest.onmicrosoft.com`. 

Gdy użytkownik jest natywnym użytkownikiem usługi Azure AD połączonym z usługą Azure Server i nie jest kontem MSA, nie jest wymagana nazwa domeny ani identyfikator dzierżawy. 

Aby wprowadzić parametr (począwszy od programu SSMS w wersji 17.2), w oknie dialogowym Łączenie z **bazą danych** wypełnij okno dialogowe, wybierając pozycję **Active Directory — Uniwersalne z** uwierzytelnianiem usługi MFA, kliknij pozycję **Opcje**, wypełnij pole **Nazwa użytkownika,** a następnie kliknij kartę **Właściwości połączenia.** 

Sprawdź **pole Nazwa domeny usługi AD lub identyfikator dzierżawy** i podaj upoważnienie uwierzytelniające, takie jak nazwa domeny **(contosotest.onmicrosoft.com)** lub identyfikator GUID identyfikatora dzierżawy.  

   ![mfa-tenant-ssms](./media/mfa-authentication/mfa-tenant-ssms.png)

Jeśli używasz systemu SSMS 18.x lub nowszego, nazwa domeny usługi AD lub identyfikator dzierżawy nie są już potrzebne użytkownikom-gościom, ponieważ program 18.x lub nowszy automatycznie je rozpoznaje.

   ![mfa-tenant-ssms](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Pomoc techniczna dla firm usługi Azure AD dla firm   
Użytkownicy usługi Azure AD obsługiwane dla scenariuszy usługi Azure AD B2B jako użytkownicy-goście (zobacz [Co to jest współpraca Usługi Azure B2B](../../active-directory/b2b/what-is-b2b.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) można połączyć się z Synapse SQL tylko jako część członków grupy utworzonej w bieżącej usługi Azure AD i mapowane ręcznie przy użyciu instrukcji Transact-SQL `CREATE USER` w danej bazie danych. 

Na przykład `steve@gmail.com` jeśli jest proszony `contosotest` o usługę Azure `contosotest.onmicrosoft.com`AD (z domeną `usergroup` reklamy platformy Azure), grupa `steve@gmail.com` usługi Azure AD, na przykład musi zostać utworzona w usłudze Azure AD, która zawiera członka. Następnie ta grupa musi zostać utworzona dla określonej bazy danych (czyli MyDatabase) przez administratora usługi Azure `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` AD SQL lub usługi Azure AD DBO przez wykonanie instrukcji Transact-SQL. 

Po utworzeniu użytkownika bazy danych `steve@gmail.com` użytkownik może `MyDatabase` zalogować się do `Active Directory – Universal with MFA support`opcji uwierzytelniania SSMS . 

Grupa użytkowników domyślnie ma tylko uprawnienie connect i wszelkie dalsze dostęp do danych, które będą musiały być przyznane w normalny sposób. 

Należy zauważyć, że użytkownik `steve@gmail.com` jako użytkownik-gość musi `contosotest.onmicrosoft.com` zaznaczyć to pole i dodać nazwę domeny usługi AD w oknie dialogowym **Właściwości połączenia** SSMS. Nazwa domeny usługi AD lub identyfikator **dzierżawy** opcja jest obsługiwana tylko dla opcji połączenia uniwersalnego z uwierzytelniać wieloskładnikowej, w przeciwnym razie jest wyszarzona.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>Ograniczenia uwierzytelniania uniwersalnego dla synapse SQL

- SSMS i SqlPackage.exe są jedynymi narzędziami aktualnie włączonymi dla usługi MFA za pośrednictwem uniwersalnego uwierzytelniania usługi Active Directory.
- Usługa SSMS w wersji 17.2 obsługuje równoczesny dostęp dla wielu użytkowników przy użyciu uwierzytelniania uniwersalnego z uwierzytelnianiem wieloskładnikowym. Wersja 17.0 i 17.1 ograniczyły logowanie dla wystąpienia usługi SSMS przy użyciu uwierzytelniania uniwersalnego na jednym koncie usługi Azure Active Directory. Aby zalogować się jako inne konto usługi Azure AD, należy użyć innego wystąpienia usługi SSMS. (To ograniczenie jest ograniczone do uwierzytelniania uniwersalnego usługi Active Directory; można zalogować się do różnych serwerów przy użyciu uwierzytelniania hasłem usługi Active Directory, zintegrowanego uwierzytelniania usługi Active Directory lub uwierzytelniania serwera SQL Server).
- Usługa SSMS obsługuje uniwersalne uwierzytelnianie usługi Active Directory dla Eksploratora obiektów, Edytor zapytań i wizualizację magazynu zapytań.
- Usługa SSMS w wersji 17.2 zapewnia obsługę bazy danych danych programu DacFx Kreatora programu DacFx. Gdy określony użytkownik jest uwierzytelniony za pośrednictwem okna dialogowego uwierzytelniania początkowego przy użyciu uwierzytelniania uniwersalnego, Kreator programu DacFx działa tak samo jak w przypadku wszystkich innych metod uwierzytelniania.
- Projektant tabel SSMS nie obsługuje uwierzytelniania uniwersalnego.
- Nie ma żadnych dodatkowych wymagań dotyczących oprogramowania dla uwierzytelniania uniwersalnego usługi Active Directory, z wyjątkiem tego, że należy użyć obsługiwanej wersji usługi SSMS.  
- Wersja biblioteki uwierzytelniania usługi Active Directory (ADAL) dla uwierzytelniania uniwersalnego została zaktualizowana do najnowszej wersji dostępnej w pliku ADAL.dll 3.13.9. Zobacz [Biblioteka uwierzytelniania usługi Active Directory 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [artykuł Połącz z programem Synapse SQL z programem SQL Server Management Studio.](get-started-ssms.md) 

