---
title: Dostęp warunkowy
description: Dowiedz się, jak skonfigurować dostęp warunkowy dla Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i usługi Azure Synapse Analytics.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: ad80f68c1ab3b3583c5a22de49b77211571f345e
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443989"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Dostęp warunkowy przy użyciu Azure SQL Database i usługi Azure Synapse Analytics

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md), [wystąpienie zarządzane usługi Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md)i [usługa Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) obsługują dostęp warunkowy do firmy Microsoft.

Poniższe kroki pokazują, jak skonfigurować Azure SQL Database, wystąpienie zarządzane SQL lub usługę Azure Synapse w celu wymuszenia zasad dostępu warunkowego (CA).  

## <a name="prerequisites"></a>Wymagania wstępne

- Aby obsłużyć uwierzytelnianie Azure Active Directory (Azure AD), należy skonfigurować Azure SQL Database, wystąpienie zarządzane usługi Azure SQL lub pulę Azure SQL w usłudze Azure Synapse. Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie uwierzytelniania Azure Active Directory i zarządzanie nim za pomocą SQL Database lub Azure Synapse](authentication-aad-configure.md).  
- Po włączeniu Multi-Factor Authentication należy nawiązać połączenie za pomocą obsługiwanego narzędzia, takiego jak Najnowsza SQL Server Management Studio (SSMS). Aby uzyskać więcej informacji, zobacz [konfigurowanie Azure SQL Database uwierzytelniania wieloskładnikowego dla SQL Server Management Studio](authentication-mfa-ssms-configure.md).  

## <a name="configure-conditional-access"></a>Konfigurowanie zasad dostępu warunkowego

> [!NOTE]
> Poniższy przykład używa Azure SQL Database, ale należy wybrać odpowiedni produkt, dla którego chcesz skonfigurować dostęp warunkowy.

1. Zaloguj się do Azure Portal, wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **dostęp warunkowy**. Aby uzyskać więcej informacji, zobacz [Azure Active Directory informacje techniczne dotyczące dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Blok dostępu warunkowego](./media/conditional-access-configure/conditional-access-blade.png)

2. W bloku **zasady dostępu warunkowego** kliknij pozycję **nowe zasady**, podaj nazwę, a następnie kliknij pozycję **Konfiguruj reguły**.  
3. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**, zaznacz pole wyboru **Użytkownicy i grupy**, a następnie wybierz użytkownika lub grupę do dostępu warunkowego. Kliknij pozycję **Wybierz**, a następnie kliknij pozycję **gotowe** , aby zaakceptować wybór.  
   ![Wybieranie użytkowników i grup](./media/conditional-access-configure/select-users-and-groups.png)  

4. Wybierz pozycję **aplikacje w chmurze**, a następnie kliknij pozycję **Wybierz aplikacje**. Zobaczysz wszystkie aplikacje dostępne dla dostępu warunkowego. Wybierz pozycję **Azure SQL Database**, a następnie kliknij pozycję **Wybierz**, a następnie kliknij pozycję **gotowe**.  
   ![Wybierz SQL Database](./media/conditional-access-configure/select-sql-database.png)  
   Jeśli nie możesz znaleźć **Azure SQL Database** wymienionych na poniższym, trzecim zrzucie ekranu, wykonaj następujące czynności:
   - Nawiąż połączenie z bazą danych w Azure SQL Database przy użyciu programu SSMS z kontem administratora usługi Azure AD.  
   - Wykonaj `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` .  
   - Zaloguj się do usługi Azure AD i sprawdź, czy Azure SQL Database, wystąpienie zarządzane SQL lub usługa Azure Synapse są wymienione w aplikacjach w wystąpieniu usługi Azure AD.  

5. Wybierz pozycję **Kontrola dostępu**, wybierz pozycję **Udziel**, a następnie sprawdź zasady, które chcesz zastosować. W tym przykładzie wybieramy opcję **Wymagaj uwierzytelniania wieloskładnikowego**.  
   ![Wybierz pozycję Udziel dostępu](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Podsumowanie

Wybrana aplikacja (Azure SQL Database) używająca Azure AD — wersja Premium, teraz wymusza wybrane zasady dostępu warunkowego, **wymaga uwierzytelniania wieloskładnikowego.**

Pytania dotyczące Azure SQL Database i usługi Azure Synapse dotyczącej uwierzytelniania wieloskładnikowego, skontaktuj się z firmą <MFAforSQLDB@microsoft.com> .  

## <a name="next-steps"></a>Następne kroki  

Aby zapoznać się z samouczkiem, zobacz temat [Zabezpieczanie bazy danych w SQL Database](secure-database-tutorial.md).
