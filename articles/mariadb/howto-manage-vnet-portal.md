---
title: Zarządzanie punktami końcowymi sieci wirtualnej — Azure Portal-Azure Database for MariaDB
description: Twórz Azure Database for MariaDB punkty końcowe usługi sieci wirtualnej i zarządzaj nimi, korzystając z Azure Portal
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 8e32b2b2e619d37323b2516178ab2b24cede4ee4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86101345"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Tworzenie punktów końcowych usługi sieci wirtualnej i zarządzanie nimi Azure Database for MariaDB przy użyciu Azure Portal

Punkty końcowe i reguły usługi sieci wirtualnej rozszerzają prywatną przestrzeń adresową sieci wirtualnej na serwer usługi Azure Database for MariaDB. Omówienie punktów końcowych usługi sieci wirtualnej Azure Database for MariaDB, w tym ograniczeń, znajduje się w temacie [punkty końcowe usługi sieci wirtualnej Azure Database for MariaDB Server](concepts-data-access-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionach dla Azure Database for MariaDB.

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest obsługiwana tylko w przypadku serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Tworzenie reguły sieci wirtualnej i włączanie punktów końcowych usługi

1. Na stronie serwer MariaDB w obszarze Ustawienia kliknij pozycję **zabezpieczenia połączeń** , aby otworzyć okienko zabezpieczenia połączenia dla Azure Database for MariaDB.

2. Upewnij się, że ustawienie Zezwalaj na dostęp do usług platformy Azure jest **wyłączone**.

> [!Important]
> Jeśli ustawisz ją na wartość ON, serwer bazy danych usługi Azure MariaDB akceptuje komunikację z dowolnych podsieci. Pozostawienie kontrolki ustawionej na włączone może być nadmierne dostęp z punktu widzenia zabezpieczeń. Funkcja punktu końcowego usługi Microsoft Azure Virtual Network, w połączeniu z funkcją reguły sieci wirtualnej Azure Database for MariaDB, umożliwia zredukowanie obszaru zabezpieczeń.

3. Następnie kliknij pozycję **+ Dodaj istniejącą sieć wirtualną**. Jeśli nie masz istniejącej sieci wirtualnej, możesz kliknąć pozycję **+ Utwórz nową sieć wirtualną** , aby ją utworzyć. Zobacz [Szybki Start: tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md)

   ![Azure Portal kliknij pozycję zabezpieczenia połączeń](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Wprowadź nazwę reguły sieci wirtualnej, wybierz subskrypcję, sieć wirtualną i nazwę podsieci, a następnie kliknij pozycję **Włącz**. Spowoduje to automatyczne włączenie punktów końcowych usługi sieci wirtualnej w podsieci przy użyciu tagu usługi **Microsoft. SQL** .

   ![Azure Portal — Konfigurowanie sieci wirtualnej](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Konto musi mieć niezbędne uprawnienia do tworzenia sieci wirtualnej i punktu końcowego usługi.

   Punkty końcowe usługi można niezależnie konfigurować w sieciach wirtualnych, przez użytkownika z dostępem do zapisu do sieci wirtualnej.
    
   Aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej, użytkownik musi mieć uprawnienia do "Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.
    
   Dowiedz się więcej na temat [wbudowanych ról](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby sieci wirtualnej i usługi platformy Azure znajdują się w różnych subskrypcjach, zasoby powinny znajdować się w tej samej dzierżawie Active Directory (AD). Upewnij się, że obie subskrypcje mają zarejestrowany dostawca zasobów **Microsoft. SQL** . Aby uzyskać więcej informacji, zobacz temat [Resource-Manager-Registration][resource-manager-portal]

   > [!IMPORTANT]
   > Zdecydowanie zaleca się zapoznanie się z tym artykułem dotyczącym konfiguracji punktów końcowych usługi i zagadnień przed skonfigurowaniem punktów końcowych usługi. **Virtual Network punkt końcowy usługi:** [Punkt końcowy usługi Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwę typu usługi platformy Azure. Punkty końcowe usług wirtualnych używają nazwy typu usługi **Microsoft. SQL**, która odnosi się do usługi platformy Azure o nazwie SQL Database. Ten tag usługi ma również zastosowanie do usług Azure SQL Database, Azure Database for MariaDB, PostgreSQL i MySQL. Należy pamiętać, że podczas stosowania znacznika usługi **Microsoft. SQL** do punktu końcowego usługi sieci wirtualnej konfiguruje ruch usługi punktu końcowego dla wszystkich usług Azure Database, w tym Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB i Azure Database for MySQL serwerów w podsieci.
   > 

5. Po włączeniu kliknij przycisk **OK** , aby zobaczyć, że punkty końcowe usługi sieci wirtualnej są włączone wraz z regułą sieci wirtualnej.

   ![Włączono punkty końcowe usługi sieci wirtualnej i utworzono regułę sieci wirtualnej](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [konfigurowaniu protokołu SSL na Azure Database for MariaDB](howto-configure-ssl.md)
- Analogicznie, można utworzyć skrypt, aby [włączyć punkty końcowe usługi sieci wirtualnej oraz regułę sieci wirtualnej dla Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-vnet-cli.md).

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md