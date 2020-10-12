---
title: Zarządzanie punktami końcowymi sieci wirtualnej — Azure Portal-Azure Database for MySQL
description: Twórz Azure Database for MySQL punkty końcowe usługi sieci wirtualnej i zarządzaj nimi, korzystając z Azure Portal
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 61b08514f8d2989736ff2505f0446937757ddb99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90884687"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Tworzenie punktów końcowych usługi sieci wirtualnej i zarządzanie nimi Azure Database for MySQL przy użyciu Azure Portal
Punkty końcowe i reguły usługi sieci wirtualnej rozszerzają prywatną przestrzeń adresową sieci wirtualnej na serwer usługi Azure Database for MySQL. Omówienie punktów końcowych usługi sieci wirtualnej Azure Database for MySQL, w tym ograniczeń, znajduje się w temacie [punkty końcowe usługi sieci wirtualnej Azure Database for MySQL Server](concepts-data-access-and-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionach dla Azure Database for MySQL.

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest obsługiwana tylko w przypadku serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.
> W przypadku komunikacji równorzędnej sieci wirtualnych, jeśli ruch odbywa się za pomocą wspólnej bramy sieci wirtualnej z punktami końcowymi usługi i powinien przepływać do elementu równorzędnego, Utwórz regułę listy ACL/sieci wirtualnej, aby umożliwić usłudze Azure Virtual Machines w sieci wirtualnej bramy dostęp do serwera Azure Database for MySQL.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Tworzenie reguły sieci wirtualnej i włączanie punktów końcowych usługi w Azure Portal

1. Na stronie serwer MySQL w obszarze Ustawienia kliknij pozycję **zabezpieczenia połączeń** , aby otworzyć okienko zabezpieczenia połączenia dla Azure Database for MySQL. 

2. Upewnij się, że ustawienie Zezwalaj na dostęp do usług platformy Azure jest **wyłączone**.

> [!Important]
> Jeśli pozostawisz formant ustawiony na wartość włączone, serwer bazy danych programu Azure MySQL akceptuje komunikację z dowolnych podsieci. Pozostawienie kontrolki ustawionej na włączone może być nadmierne dostęp z punktu widzenia zabezpieczeń. Funkcja punktu końcowego usługi Microsoft Azure Virtual Network, w połączeniu z funkcją reguły sieci wirtualnej Azure Database for MySQL, umożliwia zredukowanie obszaru zabezpieczeń.

3. Następnie kliknij pozycję **+ Dodaj istniejącą sieć wirtualną**. Jeśli nie masz istniejącej sieci wirtualnej, możesz kliknąć pozycję **+ Utwórz nową sieć wirtualną** , aby ją utworzyć. Zobacz [Szybki Start: tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md)

   :::image type="content" source="./media/howto-manage-vnet-using-portal/1-connection-security.png" alt-text="Azure Portal kliknij pozycję zabezpieczenia połączeń":::

4. Wprowadź nazwę reguły sieci wirtualnej, wybierz subskrypcję, sieć wirtualną i nazwę podsieci, a następnie kliknij pozycję **Włącz**. Spowoduje to automatyczne włączenie punktów końcowych usługi sieci wirtualnej w podsieci przy użyciu tagu usługi **Microsoft. SQL** .

   :::image type="content" source="./media/howto-manage-vnet-using-portal/2-configure-vnet.png" alt-text="Azure Portal kliknij pozycję zabezpieczenia połączeń" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.
    
   Dowiedz się więcej na temat [wbudowanych ról](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby sieci wirtualnej i usługi platformy Azure znajdują się w różnych subskrypcjach, zasoby powinny znajdować się w tej samej dzierżawie Active Directory (AD). Upewnij się, że obie subskrypcje mają zarejestrowany dostawca zasobów **Microsoft. SQL** . Aby uzyskać więcej informacji, zobacz temat [Resource-Manager-Registration][resource-manager-portal]

   > [!IMPORTANT]
   > Zdecydowanie zaleca się zapoznanie się z tym artykułem dotyczącym konfiguracji punktów końcowych usługi i zagadnień przed skonfigurowaniem punktów końcowych usługi. **Virtual Network punkt końcowy usługi:** [Punkt końcowy usługi Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwę typu usługi platformy Azure. Punkty końcowe usług wirtualnych używają nazwy typu usługi **Microsoft. SQL**, która odnosi się do usługi platformy Azure o nazwie SQL Database. Ten tag usługi dotyczy także usług Azure SQL Database, Azure Database for PostgreSQL i MySQL. Należy pamiętać, że podczas stosowania znacznika usługi **Microsoft. SQL** do punktu końcowego usługi sieci wirtualnej konfigurowany jest ruch punktu końcowego usługi dla wszystkich usług Azure Database, w tym Azure SQL Database, Azure Database for PostgreSQL i Azure Database for MySQL serwerów w podsieci. 
   > 

5. Po włączeniu kliknij przycisk **OK** , aby zobaczyć, że punkty końcowe usługi sieci wirtualnej są włączone wraz z regułą sieci wirtualnej.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png" alt-text="Azure Portal kliknij pozycję zabezpieczenia połączeń":::

## <a name="next-steps"></a>Następne kroki
- Analogicznie, można utworzyć skrypt, aby [włączyć punkty końcowe usługi sieci wirtualnej oraz regułę sieci wirtualnej dla Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-vnet-using-cli.md).
- Aby uzyskać pomoc w połączeniu z serwerem Azure Database for MySQL, zobacz [biblioteki połączeń dla Azure Database for MySQL](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md