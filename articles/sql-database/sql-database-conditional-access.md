---
title: Dostęp warunkowy
description: Dowiedz się, jak skonfigurować dostęp warunkowy dla Azure SQL Database i Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124906"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Dostęp warunkowy (MFA) z usługami Azure SQL Database i Azure Synapse Analytics

Usługa Azure [SQL Database](sql-database-technical-overview.md), [wystąpienie zarządzane](sql-database-managed-instance.md)i [usługa Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) obsługują dostęp warunkowy do firmy Microsoft. 

> [!NOTE]
> Ten temat ma zastosowanie do programu Azure SQL Server oraz do SQL Database i Azure Synapse, które są tworzone na serwerze Azure SQL. Dla uproszczenia SQL Database jest używany podczas odwoływania się do SQL Database i usługi Azure Synapse.

Poniższe kroki pokazują, jak skonfigurować SQL Database, aby wymusić zasady dostępu warunkowego.  

## <a name="prerequisites"></a>Wymagania wstępne  
- Należy skonfigurować SQL Database lub pulę SQL na platformie Azure Synapse, aby obsługiwały Azure Active Directory uwierzytelnianie. Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie uwierzytelniania Azure Active Directory i zarządzanie nim za pomocą SQL Database lub Azure Synapse](sql-database-aad-authentication-configure.md).  
- W przypadku włączenia uwierzytelniania wieloskładnikowego należy nawiązać połączenie za pomocą obsługiwanego narzędzia, takiego jak najnowszy program SSMS. Aby uzyskać więcej informacji, zobacz [konfigurowanie Azure SQL Database uwierzytelniania wieloskładnikowego dla SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurowanie urzędu certyfikacji dla usługi Azure SQL DB/DW  
1. Zaloguj się do portalu, wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **dostęp warunkowy**. Aby uzyskać więcej informacji, zobacz [Azure Active Directory informacje techniczne dotyczące dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Blok dostępu warunkowego](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. W bloku **zasady dostępu warunkowego** kliknij pozycję **nowe zasady**, podaj nazwę, a następnie kliknij pozycję **Konfiguruj reguły**.  
3. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**, zaznacz pole wyboru **Użytkownicy i grupy**, a następnie wybierz użytkownika lub grupę do dostępu warunkowego. Kliknij pozycję **Wybierz**, a następnie kliknij pozycję **gotowe** , aby zaakceptować wybór.  
   ![Wybieranie użytkowników i grup](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Wybierz pozycję **aplikacje w chmurze**, a następnie kliknij pozycję **Wybierz aplikacje**. Zobaczysz wszystkie aplikacje dostępne dla dostępu warunkowego. Wybierz pozycję **Azure SQL Database**, a następnie kliknij pozycję **Wybierz**, a następnie kliknij pozycję **gotowe**.  
   ![Wybierz SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   Jeśli nie możesz znaleźć **Azure SQL Database** wymienionych na poniższym, trzecim zrzucie ekranu, wykonaj następujące czynności:   
   - Zaloguj się do wystąpienia usługi Azure SQL DB/DW przy użyciu programu SSMS z kontem administratora AAD.  
   - Wykonaj `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Zaloguj się w usłudze AAD i sprawdź, czy Azure SQL Database i Azure Synapse są wymienione w aplikacjach w usłudze AAD.  

5. Wybierz pozycję **Kontrola dostępu**, wybierz pozycję **Udziel**, a następnie sprawdź zasady, które chcesz zastosować. W tym przykładzie wybieramy opcję **Wymagaj uwierzytelniania wieloskładnikowego**.  
   ![Wybierz pozycję Udziel dostępu](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Podsumowanie  
Wybrana aplikacja (Azure SQL Database) umożliwiająca nawiązywanie połączenia z usługą Azure SQL DB/DW przy użyciu Azure AD — wersja Premium, teraz wymusza wybrane zasady dostępu warunkowego, **wymaga uwierzytelniania wieloskładnikowego.**  
Pytania dotyczące Azure SQL Database i usługi Azure Synapse dotyczącej uwierzytelniania wieloskładnikowego, MFAforSQLDB@microsoft.comskontaktuj się z firmą.  

## <a name="next-steps"></a>Następne kroki  

Aby zapoznać się z samouczkiem, zobacz temat [zabezpieczanie Azure SQL Database](sql-database-security-tutorial.md).
