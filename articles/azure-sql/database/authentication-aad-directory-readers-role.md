---
title: Rola czytelnicy Directory w Azure Active Directory dla usługi Azure SQL
description: Dowiedz się więcej o roli czytnika katalogów w usłudze Azure AD dla usługi Azure SQL.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: 5764a8df862610fc076ce2810fcc0d4bf8dbda3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094560"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Rola czytelnicy Directory w Azure Active Directory dla usługi Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Ta funkcja jest dostępna w **publicznej wersji zapoznawczej**.

Usługa Azure Active Directory (Azure AD) została wprowadzona [przy użyciu grup w chmurze do zarządzania przypisaniami ról w Azure Active Directory (wersja zapoznawcza)](../../active-directory/roles/groups-concept.md). Dzięki temu można przypisywać role usługi Azure AD do grup.

W przypadku włączenia [zarządzanej tożsamości](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) dla Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL lub usługi Azure Synapse Analytics należy przypisać rolę [**czytelnicy**](../../active-directory/roles/permissions-reference.md#directory-readers) usługi Azure AD do tożsamości, aby zezwolić na dostęp do odczytu do [interfejs API programu Graph usługi Azure AD](../../active-directory/develop/active-directory-graph-api.md). Zarządzana tożsamość SQL Database i Azure Synapse jest określana jako tożsamość serwera. Zarządzana tożsamość wystąpienia zarządzanego SQL jest określana jako tożsamość wystąpienia zarządzanego i automatycznie przypisywana podczas tworzenia wystąpienia. Aby uzyskać więcej informacji na temat przypisywania tożsamości serwera do SQL Database lub Azure Synapse, zobacz [Włączanie nazw głównych usług w celu tworzenia użytkowników usługi Azure AD](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

Rola **czytelnicy Directory** jest wymagana do:

- Tworzenie nazw logowania usługi Azure AD dla wystąpienia zarządzanego SQL
- Personifikowanie użytkowników usługi Azure AD w usłudze Azure SQL
- Migrowanie SQL Server użytkowników korzystających z uwierzytelniania systemu Windows do wystąpienia zarządzanego SQL przy użyciu uwierzytelniania usługi Azure AD (za pomocą polecenia [ALTER User (Transact-SQL)](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) )
- Zmień administratora usługi Azure AD dla wystąpienia zarządzanego SQL
- Zezwalaj [podmiotom usługi (Applications)](authentication-aad-service-principal.md) na tworzenie użytkowników usługi Azure AD w usłudze Azure SQL

## <a name="assigning-the-directory-readers-role"></a>Przypisywanie roli czytelnicy katalogów

Aby można było przypisać rolę [**czytelnicy Directory**](../../active-directory/roles/permissions-reference.md#directory-readers) do tożsamości, wymagany jest użytkownik z uprawnieniami [administratora globalnego](../../active-directory/roles/permissions-reference.md#global-administrator) lub [administratora roli uprzywilejowanej](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) . Użytkownicy, którzy często zarządzają lub wdrażają SQL Database, wystąpienie zarządzane SQL lub usługa Azure Synapse, mogą nie mieć dostępu do tych ról o wysokim poziomie uprawnień. Może to często prowadzić do komplikacji dla użytkowników, którzy tworzą nieplanowane zasoby usługi Azure SQL lub potrzebują pomocy z wysoce uprzywilejowanych członków roli, które często są niedostępne w dużych organizacjach.

W przypadku wystąpienia zarządzanego SQL rola **czytelnicy Directory** musi być przypisana do tożsamości wystąpienia zarządzanego, aby można było [skonfigurować administratora usługi Azure AD dla wystąpienia zarządzanego](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). 

Przypisanie roli **czytniki katalogów** do tożsamości serwera nie jest wymagane dla SQL Database lub Azure Synapse podczas konfigurowania administratora usługi Azure AD dla serwera logicznego. Aby umożliwić tworzenie obiektów usługi Azure AD w SQL Database lub Azure Synapse w imieniu aplikacji usługi Azure AD, wymagana jest rola **czytelnicy katalogów** . Jeśli rola nie jest przypisana do tożsamości serwera logicznego SQL, tworzenie użytkowników usługi Azure AD w usłudze Azure SQL zakończy się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [Azure Active Directory jednostki usługi przy użyciu usługi Azure SQL](authentication-aad-service-principal.md).

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>Przyznawanie roli czytniki katalogów do grupy usługi Azure AD

Obecnie w **publicznej wersji zapoznawczej** można mieć uprawnienia administratora [globalnego](../../active-directory/roles/permissions-reference.md#global-administrator) lub [administratora roli uprzywilejowanej](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) , aby utworzyć grupę usługi Azure AD i przypisać do niej uprawnienie do [**odczytywania katalogów**](../../active-directory/roles/permissions-reference.md#directory-readers) . Umożliwi to dostęp do interfejs API programu Graph usługi Azure AD dla członków tej grupy. Ponadto użytkownicy usługi Azure AD będący właścicielami tej grupy mogą przypisywać nowych członków tej grupy, w tym tożsamości serwerów logicznych usługi Azure SQL.

To rozwiązanie nadal wymaga, aby użytkownik o wysokim poziomie uprawnień (Administrator globalny lub administrator ról uprzywilejowanych) utworzył grupę i przypisał użytkowników jako jednorazowe działanie, ale właściciele grup usługi Azure AD będą mogli przypisywać dodatkowe elementy członkowskie do przodu. Eliminuje to konieczność poniesienia wysokiego uprawnienia użytkownikowi w przyszłości, aby skonfigurować wszystkie bazy danych SQL, wystąpienia zarządzane SQL lub serwery Azure Synapse w swojej dzierżawie usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Przypisywanie roli czytelnicy katalogów do grupy usługi Azure AD i zarządzanie przypisaniami ról](authentication-aad-directory-readers-role-tutorial.md)