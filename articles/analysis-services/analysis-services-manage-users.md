---
title: Azure Analysis Services uwierzytelnianie i uprawnienia użytkowników | Microsoft Docs
description: W tym artykule opisano, jak Azure Analysis Services używa Azure Active Directory (Azure AD) do zarządzania tożsamościami i uwierzytelniania użytkowników.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 032b63700f2842826de916a8f077975689d56911
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014906"
---
# <a name="authentication-and-user-permissions"></a>Uwierzytelnianie i uprawnienia użytkownika

Azure Analysis Services używa Azure Active Directory (Azure AD) do zarządzania tożsamościami i uwierzytelniania użytkowników. Każdy użytkownik tworzący, zarządzający lub łączący się z serwerem Azure Analysis Services musi mieć prawidłową tożsamość użytkownika w [dzierżawie usługi Azure AD](../active-directory/fundamentals/active-directory-whatis.md) w tej samej subskrypcji.

Azure Analysis Services obsługuje funkcję [współpracy B2B usługi Azure AD](../active-directory/external-identities/what-is-b2b.md). Dzięki B2B użytkownicy spoza organizacji mogą być zapraszani jako użytkownicy-Goście w katalogu usługi Azure AD. Goście mogą znajdować się w innym katalogu dzierżawy usługi Azure AD lub na dowolnym prawidłowym adresie e-mail. Po zaproszeniu i zaakceptowaniu przez użytkownika zaproszenia wysłanego pocztą e-mail z platformy Azure tożsamość użytkownika zostanie dodana do katalogu dzierżawy. Tożsamości te mogą być dodawane do grup zabezpieczeń lub jako członkowie roli administratora serwera lub bazy danych.

![Architektura uwierzytelniania Azure Analysis Services](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Uwierzytelnianie

Wszystkie aplikacje i narzędzia klienckie używają co najmniej jednej z Analysis Services [bibliotek klienckich](/analysis-services/client-libraries?view=azure-analysis-services-current) (AMO, MSOLAP, ADOMD) w celu nawiązania połączenia z serwerem. 

Wszystkie trzy biblioteki klienta obsługują zarówno przepływ interaktywny usługi Azure AD, jak i nieinteraktywne metody uwierzytelniania. W aplikacjach korzystających z AMOMD i MSOLAP można używać dwóch metod nieinteraktywnych, hasła Active Directory i Active Directory zintegrowanych metod uwierzytelniania. Te dwie metody nigdy nie powodują wyskakujących okien dialogowych.

Aplikacje klienckie, takie jak program Excel i Power BI Desktop, oraz narzędzia, takie jak SSMS i Analysis Services projekty, dla programu Visual Studio instalują najnowsze wersje bibliotek po aktualizacji do najnowszej wersji. Power BI Desktop, SSMS i Analysis Services projekty są aktualizowane co miesiąc. Program Excel jest [aktualizowany przy użyciu Microsoft 365](https://support.microsoft.com/office/when-do-i-get-the-newest-features-for-microsoft-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Aktualizacje Microsoft 365 są rzadziej, a niektóre organizacje korzystają z odroczonego kanału, co oznacza, że aktualizacje są odroczone do trzech miesięcy.

W zależności od używanej aplikacji lub narzędzia klienckiego typ uwierzytelniania oraz sposób logowania mogą się różnić. Każda aplikacja może obsługiwać różne funkcje do łączenia się z usługami w chmurze, takimi jak Azure Analysis Services.

Power BI Desktop, Visual Studio i SSMS obsługują Active Directory uwierzytelnianie uniwersalne, interaktywną metodę, która również obsługuje usługę Azure Multi-Factor Authentication (MFA). Usługa Azure MFA pomaga w zabezpieczeniu dostępu do danych i aplikacji przy jednoczesnym zapewnieniu prostego procesu logowania. Zapewnia silne uwierzytelnianie z kilkoma opcjami weryfikacji (połączenie telefoniczne, wiadomość tekstowa, karty inteligentne z numerem PIN lub powiadomieniem aplikacji mobilnej). Interaktywna usługa MFA z usługą Azure AD może spowodować wyskakujące okno dialogowe umożliwiające weryfikację. **Zalecane jest uwierzytelnianie uniwersalne**.

Jeśli logujesz się do platformy Azure przy użyciu konta systemu Windows, a uwierzytelnianie uniwersalne nie jest wybrane lub jest niedostępne (Excel), jest wymagane [Active Directory Federation Services (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) . W przypadku federacji usługi Azure AD i Microsoft 365 są uwierzytelniani przy użyciu poświadczeń lokalnych i mogą uzyskiwać dostęp do zasobów platformy Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Serwery Azure Analysis Services obsługują połączenia z programu [SSMS v 17.1](/sql/ssms/download-sql-server-management-studio-ssms) lub nowszego przy użyciu uwierzytelniania systemu Windows, Active Directory uwierzytelniania hasła i Active Directory uwierzytelniania uniwersalnego. Ogólnie rzecz biorąc, zaleca się używanie Active Directory uniwersalnego uwierzytelniania, ponieważ:

*  Obsługuje interaktywne i nieinteraktywne metody uwierzytelniania.

*  Obsługuje użytkowników systemu Azure B2B zalogowanych do platformy Azure jako dzierżawca. Podczas nawiązywania połączenia z serwerem użytkownicy-Goście muszą wybrać Active Directory uniwersalnego uwierzytelniania podczas nawiązywania połączenia z serwerem.

*  Obsługuje Multi-Factor Authentication (MFA). Usługa Azure MFA pomaga w zabezpieczeniu dostępu do danych i aplikacji z zakresu opcji weryfikacji: połączenia telefonicznego, wiadomości tekstowej, kart inteligentnych z numerem PIN lub powiadomień aplikacji mobilnej. Interaktywna usługa MFA z usługą Azure AD może spowodować wyskakujące okno dialogowe umożliwiające weryfikację.

### <a name="visual-studio"></a>Visual Studio

Program Visual Studio nawiązuje połączenie z Azure Analysis Services przy użyciu uwierzytelniania uniwersalnego Active Directory z obsługą usługi MFA. Użytkownicy są monitowani o zalogowanie się do platformy Azure przy pierwszym wdrożeniu. Użytkownicy muszą zalogować się do platformy Azure przy użyciu konta z uprawnieniami administratora serwera na serwerze, na którym są wdrażane. Po pierwszym zalogowaniu się do platformy Azure jest przypisywany token. Token znajduje się w pamięci podręcznej w przypadku przyszłych ponownych połączeń.

### <a name="power-bi-desktop"></a>Program Power BI Desktop

Power BI Desktop nawiązuje połączenie z Azure Analysis Services przy użyciu uwierzytelniania uniwersalnego Active Directory z obsługą usługi MFA. Użytkownicy są monitowani o zalogowanie się do platformy Azure przy pierwszym połączeniu. Użytkownicy muszą zalogować się do platformy Azure przy użyciu konta należącego do administratora serwera lub roli bazy danych.

### <a name="excel"></a>Excel

Użytkownicy programu Excel mogą łączyć się z serwerem przy użyciu konta systemu Windows, identyfikatora organizacji (adresu e-mail) lub zewnętrznego adresu e-mail. Tożsamości zewnętrznej poczty e-mail muszą znajdować się w usłudze Azure AD jako użytkownik-Gość.

## <a name="user-permissions"></a>Uprawnienia użytkowników

**Administratorzy serwera** są określeni dla wystąpienia serwera Azure Analysis Services. Łączą się z narzędziami takimi jak Azure Portal, SSMS i Visual Studio, aby wykonywać zadania takie jak dodawanie baz danych i zarządzanie rolami użytkowników. Domyślnie użytkownik tworzący serwer jest automatycznie dodawany jako administrator serwera Analysis Services. Innych administratorów można dodać za pomocą Azure Portal lub SSMS. Administratorzy serwera muszą mieć konto w dzierżawie usługi Azure AD w tej samej subskrypcji. Aby dowiedzieć się więcej, zobacz [Zarządzanie administratorami serwerów](analysis-services-server-admins.md). 

**Użytkownicy bazy danych** nawiązują połączenie z modelowymi bazami danych przy użyciu aplikacji klienckich, takich jak Excel lub Power BI. Należy dodać użytkowników do ról bazy danych. Role bazy danych definiują uprawnienia administratora, procesu lub odczytu bazy danych. Ważne jest, aby zrozumieć użytkowników bazy danych w roli z uprawnieniami administratora, które różnią się od administratorów serwerów. Jednak domyślnie Administratorzy serwera są również administratorami baz danych. Aby dowiedzieć się więcej, zobacz [Zarządzanie rolami bazy danych i użytkownikami](analysis-services-database-users.md).

**Właściciele zasobów platformy Azure**. Właściciele zasobów zarządzają zasobami w ramach subskrypcji platformy Azure. Właściciele zasobów mogą dodawać tożsamości użytkowników usługi Azure AD do ról właściciela lub współautora w ramach subskrypcji przy użyciu funkcji **kontroli dostępu** w Azure Portal lub szablonów Azure Resource Manager. 

![Kontrola dostępu w Azure Portal](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Role na tym poziomie dotyczą użytkowników lub kont wymagających wykonywania zadań, które można wykonać w portalu lub za pomocą szablonów Azure Resource Manager. Aby dowiedzieć się więcej, zobacz [Kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Role bazy danych

 Role zdefiniowane dla modelu tabelarycznego to role bazy danych. Oznacza to, że role zawierają składowe składające się z użytkowników usługi Azure AD i grup zabezpieczeń z określonymi uprawnieniami, które definiują akcję, którą mogą wykonać te elementy członkowskie w bazie danych modelu. Rola bazy danych jest tworzona jako oddzielny obiekt w bazie danych i ma zastosowanie tylko do bazy danych, w której została utworzona.   
  
 Domyślnie podczas tworzenia nowego projektu modelu tabelarycznego projekt modelu nie ma żadnych ról. Role można definiować przy użyciu okna dialogowego Menedżer ról w programie Visual Studio. Gdy role są zdefiniowane w projekcie projektu modelu, są stosowane tylko do bazy danych obszaru roboczego modelu. Po wdrożeniu modelu te same role są stosowane do wdrożonego modelu. Po wdrożeniu modelu Administratorzy serwerów i baz danych mogą zarządzać rolami i członkami przy użyciu programu SSMS. Aby dowiedzieć się więcej, zobacz [Zarządzanie rolami bazy danych i użytkownikami](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Następne kroki

[Zarządzanie dostępem do zasobów za pomocą grup Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Zarządzanie rolami i użytkownikami bazy danych](analysis-services-database-users.md)  
[Zarządzanie administratorami serwerów](analysis-services-server-admins.md)  
[Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)](../role-based-access-control/overview.md)