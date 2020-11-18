---
title: Konfigurowanie uwierzytelniania Multi-Factor Authentication
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Dowiedz się, jak używać uwierzytelniania wieloskładnikowego w programie SSMS dla Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i usługi Azure Synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 094e40fe55e1ba51b0539d740ecb449a8327d6a6
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841244"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurowanie uwierzytelniania wieloskładnikowego dla SQL Server Management Studio i usługi Azure AD
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym artykule pokazano, jak używać usługi uwierzytelniania wieloskładnikowego (MFA) w usłudze Azure Active Directory (Azure AD) z programem SQL Server Management Studio (SSMS). Usługi Azure AD MFA można używać podczas nawiązywania połączenia z programem SSMS lub SqlPackage.exe do [Azure SQL Database](sql-database-paas-overview.md), [wystąpienia zarządzanego usługi Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) i [usługi Azure Synapse Analytics (dawniej SQL Data Warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Aby zapoznać się z omówieniem uwierzytelniania wieloskładnikowego, zobacz [uniwersalne uwierzytelnianie przy użyciu SQL Database, wystąpienia zarządzanego SQL i Azure Synapse (Obsługa programu SSMS dla usługi MFA)](../database/authentication-mfa-ssms-overview.md).

> [!IMPORTANT]
> Bazy danych w Azure SQL Database, wystąpienia zarządzane usługi Azure SQL i Azure Synapse (wcześniej SQL Data Warehouse) są określane zbiorczo w pozostałej części tego artykułu jako bazy danych, a serwer odwołuje się do [serwera](logical-servers.md) , który obsługuje bazy danych dla Azure SQL Database i Azure Synapse.

## <a name="configuration-steps"></a>Kroki konfiguracji

1. **Skonfiguruj Azure Active Directory** — Aby uzyskać więcej informacji, zobacz [administrowanie katalogiem usługi Azure AD](/previous-versions/azure/azure-services/hh967611(v=azure.100)), [integrowanie tożsamości lokalnych z usługą Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Dodawanie własnej nazwy domeny do usługi Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure obsługuje teraz federacyjnego z systemem Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)i [Zarządzanie usługą Azure AD przy użyciu programu Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)).
2. **Konfigurowanie uwierzytelniania wieloskładnikowego** — Aby uzyskać instrukcje krok po kroku, zobacz artykuł [co to jest usługa Azure AD Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md), [dostęp warunkowy (MFA) z usługą Azure SQL Database i magazynem danych](conditional-access-configure.md). (Pełny dostęp warunkowy wymaga Azure Active Directory w warstwie Premium. Ograniczone uwierzytelnianie MFA jest dostępne w przypadku standardowej usługi Azure AD.
3. **Konfigurowanie uwierzytelniania usługi Azure AD** — Aby uzyskać instrukcje krok po kroku, zobacz [nawiązywanie połączenia z usługą SQL Database, wystąpieniem zarządzanym SQL lub usługą Azure Synapse przy użyciu uwierzytelniania Azure Active Directory](authentication-aad-overview.md).
4. **Pobierz program SSMS** — na komputerze klienckim Pobierz najnowszą wersję programu ssms z [SQL Server Management Studio pobierania (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Nawiązywanie połączenia przy użyciu uwierzytelniania uniwersalnego za pomocą programu SSMS

Poniższe kroki pokazują, jak nawiązać połączenie przy użyciu najnowszego programu SSMS.

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

1. Aby nawiązać połączenie przy użyciu uwierzytelniania uniwersalnego, w oknie dialogowym **łączenie z serwerem** w SQL Server Management Studio (SSMS) wybierz pozycję **Active Directory — uniwersalna z obsługą usługi MFA**. (Jeśli widzisz **Active Directory uniwersalnego uwierzytelniania** , nie jesteś w najnowszej wersji programu SSMS).

   ![Zrzut ekranu karty właściwości połączenia w oknie dialogowym Łączenie z serwerem w S S M S. "Moja baza danych" została wybrana z listy rozwijanej Połącz z bazą danych.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Wypełnij pole **Nazwa użytkownika** z poświadczeniami Azure Active Directory w formacie `user_name@domain.com` .

   ![Zrzut ekranu przedstawiający ustawienia okna dialogowego Łączenie z serwerem dla typu serwera, nazwy serwera, uwierzytelniania i nazwy użytkownika.](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Jeśli łączysz się jako użytkownik-Gość, nie musisz już kończyć pola nazwy domeny usługi AD lub identyfikatora dzierżawy dla użytkowników-Gości, ponieważ program SSMS 18. x lub nowszy automatycznie go rozpoznaje. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie uniwersalne przy użyciu SQL Database, wystąpienia zarządzanego SQL i usługi Azure Synapse (Obsługa programu SSMS)](../database/authentication-mfa-ssms-overview.md).

   ![Zrzut ekranu karty właściwości połączenia w oknie dialogowym Łączenie z serwerem w S S M S. "Moja baza danych" została wybrana z listy rozwijanej Połącz z bazą danych.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   Jeśli jednak łączysz się jako użytkownik Gość przy użyciu programu SSMS 17. x lub starszej wersji, musisz kliknąć przycisk **Opcje**, a następnie w oknie dialogowym **Właściwości połączenia** wprowadzić wartość w polu **nazwa domeny usługi AD lub identyfikator dzierżawy** .

   ![Zrzut ekranu karty właściwości połączenia w oknie dialogowym Łączenie z serwerem w S S M S. opcja nazwa domeny usługi AD lub właściwość identyfikatora dzierżawy jest wypełniana.](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. Wybierz **Opcje** i określ bazę danych w oknie dialogowym **Opcje** . (Jeśli połączony użytkownik jest użytkownikiem-gościem (tj. joe@outlook.com ), należy zaznaczyć pole wyboru i dodać bieżącą nazwę domeny usługi AD lub identyfikator dzierżawy jako część opcji. Zobacz [uwierzytelnianie uniwersalne przy użyciu SQL Database i usługi Azure Synapse Analytics (Obsługa programu SSMS dla usług MFA)](../database/authentication-mfa-ssms-overview.md). Następnie kliknij przycisk **Connect** (Połącz).  
5. Gdy pojawi się okno dialogowe **Logowanie do konta** , podaj konto i hasło tożsamości Azure Active Directory. Jeśli użytkownik jest częścią domeny federacyjnej z usługą Azure AD, nie jest wymagane hasło.

   ![Zrzut ekranu przedstawiający okno dialogowe logowania do konta dla Azure SQL Database i magazynu danych. Konto i hasło są wypełnione.](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > W przypadku uwierzytelniania uniwersalnego przy użyciu konta, które nie wymaga uwierzytelniania MFA, można nawiązać połączenie w tym punkcie. W przypadku użytkowników wymagających uwierzytelniania wieloskładnikowego wykonaj następujące czynności:
   >  

6. Mogą pojawić się dwa okna dialogowe konfiguracji usługi MFA. Ta operacja jednorazowa zależy od ustawienia administratora usługi MFA i dlatego może być opcjonalna. W przypadku domeny z obsługą usługi MFA ten krok jest czasami wstępnie zdefiniowany (na przykład domena wymaga od użytkowników korzystania z karty inteligentnej i numeru PIN).

   ![Zrzut ekranu przedstawiający okno dialogowe logowania do konta dla Azure SQL Database i magazynu danych z monitem o skonfigurowanie dodatkowej weryfikacji zabezpieczeń.](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. Drugie możliwe okno dialogowe jednorazowe pozwala wybrać szczegóły metody uwierzytelniania. Możliwe opcje są konfigurowane przez administratora.

   ![Zrzut ekranu przedstawiający okno dialogowe dodatkowa Weryfikacja zabezpieczeń z opcjami wyboru i konfigurowania metody uwierzytelniania.](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. Azure Active Directory wysyła do Ciebie informacje potwierdzające. Po otrzymaniu kodu weryfikacyjnego wprowadź go w polu **Wprowadź kod weryfikacyjny** , a następnie kliknij przycisk **Zaloguj**.

   ![Zrzut ekranu przedstawiający okno dialogowe logowania do konta dla Azure SQL Database i magazynu danych z monitem o wprowadzenie kodu weryfikacyjnego.](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

Po zakończeniu weryfikacji program SSMS nawiązuje połączenie zwykle z założeniem prawidłowych poświadczeń i dostępu do zapory.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem uwierzytelniania wieloskładnikowego, zobacz [uniwersalne uwierzytelnianie przy użyciu SQL Database, wystąpienia zarządzanego SQL i Azure Synapse (Obsługa programu SSMS dla usługi MFA)](../database/authentication-mfa-ssms-overview.md).  
- Przyznaj innym osobom dostęp do bazy danych: [SQL Database uwierzytelniania i autoryzacji: udzielanie dostępu](logins-create-manage.md)  
- Upewnij się, że inne osoby mogą łączyć się za pośrednictwem zapory: [Skonfiguruj regułę zapory na poziomie serwera przy użyciu Azure Portal](./firewall-configure.md)  
- W przypadku korzystania **z Active Directory — uniwersalne z** uwierzytelnianiem MFA, śledzenie ADAL jest dostępne od programu [SSMS 17,3](/sql/ssms/download-sql-server-management-studio-ssms). Domyślnie wyłączone jest śledzenie ADAL przy użyciu **narzędzi**, menu **opcji** , w obszarze **usługi platformy Azure**, **Azure Cloud**, **ADAL okno dane wyjściowe poziom śledzenia**, a następnie przez włączenie **danych wyjściowych**  w menu **Widok** . Ślady są dostępne w oknie danych wyjściowych w przypadku wybrania **opcji Azure Active Directory**.