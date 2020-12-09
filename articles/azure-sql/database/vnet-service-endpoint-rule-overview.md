---
title: Punkty końcowe sieci wirtualnej i reguły dla baz danych w Azure SQL Database
description: Oznacz podsieć jako punkt końcowy usługi Virtual Network. Następnie punkt końcowy jako reguła sieci wirtualnej do listy ACL bazy danych. Twoja baza danych akceptuje komunikację ze wszystkimi maszynami wirtualnymi i innymi węzłami w podsieci.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 96030f0e74c29848f675ae2e0867eefa2e79a8a7
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920405"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Korzystanie z punktów końcowych usługi sieci wirtualnej i reguł dla serwerów w Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*Reguły sieci wirtualnej* to jedna funkcja zabezpieczeń zapory, która kontroluje, czy serwer baz danych i pul elastycznych w [Azure SQL Database](sql-database-paas-overview.md) lub dla baz danych w [usłudze Azure Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) akceptuje komunikację wysyłaną z określonych podsieci w sieciach wirtualnych. W tym artykule wyjaśniono, dlaczego funkcja reguły sieci wirtualnej jest czasami najlepszą opcją bezpiecznego zezwalania na komunikację z bazą danych w Azure SQL Database i analizą Azure Synapse.

> [!NOTE]
> Ten artykuł dotyczy programu Azure SQL Database i usługi Azure Synapse Analytics. Dla uproszczenia termin "baza danych" odnosi się do obu baz danych w Azure SQL Database i Azure Synapse Analytics. Podobnie wszystkie odwołania do "serwer" odwołują się do [logicznego serwera SQL](logical-servers.md) , który hostuje Azure SQL Database i usługi Azure Synapse Analytics.

Aby można było utworzyć regułę sieci wirtualnej, musi ona być [punktem końcowym usługi sieci wirtualnej][vm-virtual-network-service-endpoints-overview-649d] , aby uzyskać odwołanie do reguły.

## <a name="how-to-create-a-virtual-network-rule"></a>Jak utworzyć regułę sieci wirtualnej

Jeśli tworzysz tylko regułę sieci wirtualnej, możesz przejść do kroków i wyjaśnień [w dalszej części tego artykułu](#anchor-how-to-by-using-firewall-portal-59j).

## <a name="details-about-virtual-network-rules"></a>Szczegóły dotyczące reguł sieci wirtualnej

W tej sekcji opisano kilka szczegółowych informacji o regułach sieci wirtualnej.

### <a name="only-one-geographic-region"></a>Tylko jeden region geograficzny

Każdy punkt końcowy usługi Virtual Network ma zastosowanie tylko do jednego regionu platformy Azure. Punkt końcowy nie umożliwia innym regionom akceptowania komunikacji z podsieci.

Każda reguła sieci wirtualnej jest ograniczona do regionu, do którego odnosi się podstawowy punkt końcowy.

### <a name="server-level-not-database-level"></a>Poziom serwera, nie na poziomie bazy danych

Każda reguła sieci wirtualnej ma zastosowanie do całego serwera, a nie tylko do jednej konkretnej bazy danych na serwerze. Innymi słowy reguła sieci wirtualnej jest stosowana na poziomie serwera, a nie na poziomie bazy danych.

- Z kolei reguły adresów IP mogą być stosowane na dowolnym poziomie.

### <a name="security-administration-roles"></a>Role administrowania zabezpieczeniami

Istnieje separacja ról zabezpieczeń w administrowaniu punktami końcowymi usługi Virtual Network. Wymagana jest akcja z każdej z następujących ról:

- **Administrator sieci (rola [współautor sieci](../../role-based-access-control/built-in-roles.md#network-contributor) ):** &nbsp; Włącz punkt końcowy.
- **Administrator bazy danych (rola [współautora SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) ):** &nbsp; Zaktualizuj listę kontroli dostępu (ACL), aby dodać daną podsieć do serwera.

*Alternatywa dla usługi Azure RBAC:*

Role administratora sieci i administratora bazy danych mają więcej możliwości niż jest to konieczne do zarządzania regułami sieci wirtualnej. Wymagany jest tylko podzestaw ich możliwości.

Możesz skorzystać z funkcji [kontroli dostępu opartej na rolach (Azure RBAC)][rbac-what-is-813s] na platformie Azure, aby utworzyć pojedynczą rolę niestandardową, która ma tylko niezbędny podzbiór funkcji. Rola niestandardowa może być używana zamiast obejmuje administratora sieci lub administratora bazy danych. Powierzchnia obszaru ekspozycji zabezpieczeń jest niższa w przypadku dodania użytkownika do roli niestandardowej, a nie dodania użytkownika do innych dwóch głównych ról administratora.

> [!NOTE]
> W niektórych przypadkach baza danych w Azure SQL Database i podsieć wirtualna znajdują się w różnych subskrypcjach. W takich przypadkach należy zapewnić następujące konfiguracje:
>
> - Obie subskrypcje muszą znajdować się w tej samej dzierżawie Azure Active Directory.
> - Użytkownik ma wymagane uprawnienia do inicjowania operacji, takich jak Włączanie punktów końcowych usługi i Dodawanie podsieci wirtualnej do danego serwera.
> - Obie subskrypcje muszą mieć zarejestrowany dostawca Microsoft. SQL.

## <a name="limitations"></a>Ograniczenia

W przypadku Azure SQL Database funkcja reguł sieci wirtualnej ma następujące ograniczenia:

- W zaporze dla bazy danych w Azure SQL Database Każda reguła sieci wirtualnej odwołuje się do podsieci. Wszystkie te podsieci, do których istnieją odwołania, muszą być hostowane w tym samym regionie geograficznym, który hostuje bazę danych.

- Każdy serwer może mieć maksymalnie 128 wpisów listy ACL dla danej sieci wirtualnej.

- Reguły sieci wirtualnej mają zastosowanie tylko do Azure Resource Manager sieci wirtualnych; i nie do [klasycznych sieci modeli wdrażania][arm-deployment-model-568f] .

- Włączenie punktów końcowych usługi sieci wirtualnej w celu Azure SQL Database również umożliwia korzystanie z punktów końcowych dla usług MySQL i PostgreSQL platformy Azure. Jednak w przypadku punktów końcowych w systemie próby nawiązania połączenia z punktami końcowymi z wystąpieniami MySQL lub PostgreSQL mogą zakończyć się niepowodzeniem.
  - Podstawowym powodem jest to, że dla programu MySQL i PostgreSQL prawdopodobnie nie skonfigurowano reguły sieci wirtualnej. Należy skonfigurować regułę sieci wirtualnej dla Azure Database for MySQL i PostgreSQL, a połączenie powiedzie się.
  - Aby zdefiniować reguły zapory sieci wirtualnej na serwerze logicznym SQL, który jest już skonfigurowany za pomocą prywatnych punktów końcowych, ustaw opcję **Odmów dostępu do sieci publicznej** na wartość **nie**.

- Na zaporze zakresy adresów IP dotyczą następujących elementów sieciowych, ale reguły sieci wirtualnej nie są:
  - [Wirtualna sieć prywatna (VPN) typu lokacja-lokacja (S2S)][vpn-gateway-indexmd-608y]
  - Lokalna za pośrednictwem [ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-using-service-endpoints"></a>Zagadnienia dotyczące korzystania z punktów końcowych usługi

Korzystając z punktów końcowych usługi dla Azure SQL Database, zapoznaj się z następującymi kwestiami:

- **Wychodzące do Azure SQL Database publicznych adresów IP jest wymagane**: aby umożliwić łączność, należy otworzyć sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) w celu Azure SQL Database adresów IP. Można to zrobić za pomocą [tagów usługi](../../virtual-network/network-security-groups-overview.md#service-tags) sieciowej grupy zabezpieczeń dla Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

jeśli korzystasz z usługi [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) w środowisku lokalnym na potrzeby publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, konieczne będzie określenie używanych adresów IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych stosowanych do ruchu w ramach usługi platformy Azure, gdy ruch trafia do sieci szkieletowej platformy Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP translatora adresów sieciowych są dostarczane przez klienta lub przez dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

Aby umożliwić komunikację z obwodu do Azure SQL Database, należy utworzyć reguły sieci IP dla publicznych adresów IP translatora adresów sieciowych.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Wpływ używania punktów końcowych usługi sieci wirtualnej z usługą Azure Storage

Usługa Azure Storage zaimplementowała tę samą funkcję, która umożliwia ograniczenie łączności z kontem usługi Azure Storage. Jeśli zdecydujesz się używać tej funkcji z kontem usługi Azure Storage, które jest używane przez usługę Azure SQL Database, możesz napotkać problemy. Poniżej znajduje się lista i Omówienie funkcji Azure SQL Database i usługi Azure Synapse Analytics, których dotyczy problem.

### <a name="azure-synapse-polybase-and-copy-statement"></a>Azure Synapse — instrukcja Base i COPY

Baza i instrukcja COPY są często używane do ładowania danych do usługi Azure Synapse Analytics z kont usługi Azure Storage w celu pozyskiwania danych o wysokiej przepływności. Jeśli konto usługi Azure Storage, z którego są ładowane dane, ogranicza dostęp tylko do zestawu podsieci sieci wirtualnej, nastąpi przerwanie łączności przy użyciu bazy danych i instrukcji COPY do konta magazynu. Aby włączyć Importowanie i eksportowanie scenariuszy przy użyciu kopii i bazy danych z usługą Azure Synapse Analytics łączącej się z usługą Azure Storage, która jest zabezpieczona w sieci wirtualnej, wykonaj kroki opisane poniżej:

#### <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj program Azure PowerShell, korzystając z tego [przewodnika](/powershell/azure/install-az-ps).
- Jeśli masz konto ogólnego przeznaczenia w wersji 1 lub konto magazynu blob, najpierw musisz przeprowadzić uaktualnienie do konta ogólnego przeznaczenia w wersji 2, korzystając z tego [przewodnika](../../storage/common/storage-account-upgrade.md).
- Musisz **zezwolić zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** , włączone w obszarze zapory konta usługi Azure Storage i menu ustawienia **sieci wirtualnych** . Włączenie tej konfiguracji umożliwi użycie instrukcji Base i COPY w celu nawiązania połączenia z kontem magazynu przy użyciu silnego uwierzytelniania w przypadku, gdy ruch sieciowy pozostaje w sieci szkieletowej platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z tym [przewodnikiem](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020.  Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Kroki

1. Jeśli masz autonomiczną dedykowaną pulę SQL, zarejestruj swój serwer SQL w usłudze Azure Active Directory (AAD) przy użyciu programu PowerShell: 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Ten krok nie jest wymagany w przypadku dedykowanych pul SQL w obszarze roboczym Synapse.

1. Jeśli masz obszar roboczy Synapse, zarejestruj tożsamość zarządzaną przez system obszaru roboczego:

   1. Przejdź do obszaru roboczego Synapse w Azure Portal
   2. Przejdź do bloku zarządzane tożsamości 
   3. Upewnij się, że opcja "Zezwalaj na potoki" jest włączona
   
1. Utwórz **konto magazynu ogólnego przeznaczenia w wersji 2** za pomocą tego [przewodnika](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Jeśli masz konto usługi Magazyn ogólnego przeznaczenia w wersji 1 lub BLOB, musisz **najpierw przeprowadzić uaktualnienie do wersji 2** przy użyciu tego [przewodnika](../../storage/common/storage-account-upgrade.md).
   > - Aby uzyskać znane problemy z Azure Data Lake Storage Gen2, zapoznaj się z tym [przewodnikiem](../../storage/blobs/data-lake-storage-known-issues.md).

1. W obszarze konto magazynu przejdź do pozycji **Access Control (IAM)**, a następnie wybierz pozycję **Dodaj przypisanie roli**. Przypisywanie **danych obiektów blob magazynu współautor** roli platformy Azure do serwera lub obszaru roboczego hostującym dedykowaną pulę SQL, która została zarejestrowana w usłudze Azure Active Directory (AAD).

   > [!NOTE]
   > Tylko członkowie z uprawnieniami właściciela na koncie magazynu mogą wykonać ten krok. W przypadku różnych wbudowanych ról platformy Azure Zapoznaj się z tym [przewodnikiem](../../role-based-access-control/built-in-roles.md).
  
1. **Połączenie z kontem usługi Azure Storage:**

   1. Utwórz **[klucz główny](/sql/t-sql/statements/create-master-key-transact-sql)** bazy danych, jeśli nie został utworzony wcześniej:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Utwórz poświadczenie o zakresie bazy danych o **tożsamości = "tożsamość usługi zarządzanej"**:

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Nie ma potrzeby określania wpisu TAJNego za pomocą klucza dostępu do usługi Azure Storage, ponieważ ten mechanizm używa [tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md) w ramach okładek.
       > - Nazwa tożsamości powinna mieć nazwę **"tożsamość usługi zarządzanej"** , aby nawiązać połączenie z kontem usługi Azure Storage zabezpieczone do sieci wirtualnej.

   1. Utwórz zewnętrzne źródło danych ze `abfss://` schematem do łączenia się z kontem magazynu ogólnego przeznaczenia w wersji 2 za pomocą bazy danych:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Jeśli masz już tabele zewnętrzne skojarzone z kontem ogólnego przeznaczenia w wersji 1 lub BLOB, należy najpierw porzucić te tabele zewnętrzne, a następnie porzucić odpowiednie zewnętrzne źródło danych. Następnie utwórz zewnętrzne źródło danych ze `abfss://` schematem łączącym się z kontem magazynu ogólnego przeznaczenia w wersji 2 jak powyżej i ponownie Utwórz wszystkie tabele zewnętrzne przy użyciu tego nowego zewnętrznego źródła danych. [Kreatora generowania i publikowania skryptów](/sql/ssms/scripting/generate-and-publish-scripts-wizard) można użyć do łatwego generowania skryptów Create-scripts dla wszystkich tabel zewnętrznych.
       > - Aby uzyskać więcej informacji na temat `abfss://` schematu, zapoznaj się z tym [przewodnikiem](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Aby uzyskać więcej informacji na temat tworzenia zewnętrznego źródła danych, zapoznaj się z tym [przewodnikiem](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Zapytanie jako normalne przy użyciu [tabel zewnętrznych](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Inspekcja Azure SQL Database obiektów BLOB

Inspekcja obiektów BLOB powoduje wypychanie dzienników inspekcji na własne konto magazynu. Jeśli to konto magazynu używa funkcji punktów końcowych usługi sieci wirtualnej, nastąpi przerwanie łączności z Azure SQL Database na koncie magazynu.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Dodawanie reguły zapory sieci wirtualnej do serwera bez włączania punktów końcowych usługi sieci wirtualnej

Dawniej przed zwiększeniem tej funkcji przed implementacją reguły sieci wirtualnej na żywo w zaporze musisz włączyć punkty końcowe usługi sieci wirtualnej. Punkty końcowe powiązane z daną podsiecią sieci wirtualnej do bazy danych w Azure SQL Database. Jednak od stycznia 2018 można obejść to wymaganie, ustawiając flagę **IgnoreMissingVNetServiceEndpoint** .

Ustawienie reguły zapory nie pomaga w zabezpieczeniu serwera. Aby zabezpieczenia zaczęły obowiązywać, należy również włączyć punkty końcowe usługi sieci wirtualnej. Po włączeniu punktów końcowych usługi w usłudze Sieć wirtualna jest przestojem do momentu zakończenia przejścia z trybu do trybu. Jest to szczególnie prawdziwe w kontekście dużych sieci wirtualnych. Możesz użyć flagi **IgnoreMissingVNetServiceEndpoint** , aby zmniejszyć lub wyeliminować przestoje podczas przejścia.

Flagę **IgnoreMissingVNetServiceEndpoint** można ustawić przy użyciu programu PowerShell. Aby uzyskać szczegółowe informacje, zobacz Program [PowerShell, aby utworzyć Virtual Network punkt końcowy usługi i regułę dla Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Błędy 40914 i 40615

Błąd połączenia 40914 odnosi się do *reguł sieci wirtualnej* określonych w okienku zapora w Azure Portal. Błąd 40615 jest podobny, z tą różnicą, że odnosi się do *reguł adresów IP* w zaporze.

### <a name="error-40914"></a>Błąd 40914

*Tekst komunikatu:* Nie można otworzyć serwera "*[nazwa serwera]*" żądanego podczas logowania. Klient nie może uzyskać dostępu do serwera.

*Opis błędu:* Klient należy do podsieci, która ma punkty końcowe serwera sieci wirtualnej. Ale serwer nie ma reguły sieci wirtualnej, która przyznaje podsieci prawo do komunikacji z bazą danych.

*Rozwiązywanie błędów:* W okienku Zapora w Azure Portal użyj kontrolki reguły sieci wirtualnej, aby [dodać regułę sieci wirtualnej](#anchor-how-to-by-using-firewall-portal-59j) dla podsieci.

### <a name="error-40615"></a>Błąd 40615

*Tekst komunikatu:* Nie można otworzyć serwera " {0} " żądanego przez nazwę logowania. Klient z adresem IP " {1} " nie może uzyskać dostępu do serwera.

*Opis błędu:* Klient próbuje nawiązać połączenie z adresu IP, który nie jest autoryzowany do łączenia się z serwerem. Zapora serwera nie ma żadnej reguły adresu IP, która umożliwia klientowi komunikowanie się z podanym adresem IP z bazą danych.

*Rozwiązywanie błędów:* Wprowadź adres IP klienta jako regułę adresu IP. W tym celu użyj okienka Zapora w witrynie Azure Portal.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal może utworzyć regułę sieci wirtualnej

W tej sekcji pokazano, jak można użyć [Azure Portal][http-azure-portal-link-ref-477t] do utworzenia *reguły sieci wirtualnej* w bazie danych programu Azure SQL Database. Reguła nakazuje bazie danych akceptowanie komunikacji z określonej podsieci, która została oznaczona jako *punkt końcowy usługi Virtual Network*.

> [!NOTE]
> Jeśli zamierzasz dodać punkt końcowy usługi do reguł zapory sieci wirtualnej serwera, najpierw upewnij się, że punkty końcowe usługi są włączone dla tej podsieci.
>
> Jeśli punkty końcowe usługi nie są włączone dla podsieci, w portalu zostanie wyświetlony monit o ich włączenie. Kliknij przycisk **Włącz** w tym samym bloku, w którym została dodana reguła.

## <a name="powershell-alternative"></a>Alternatywa dla programu PowerShell

Skrypt może również tworzyć reguły sieci wirtualnej przy użyciu polecenia cmdlet programu PowerShell **New-AzSqlServerVirtualNetworkRule** lub [AZ Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create). Jeśli chcesz, zobacz Program [PowerShell, aby utworzyć Virtual Network punkt końcowy usługi i regułę dla Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternatywa dla interfejsu API REST

Wewnętrznie polecenia cmdlet programu PowerShell dla akcji sieci wirtualnej SQL wywołują interfejsy API REST. Interfejsy API REST można wywołać bezpośrednio.

- [Reguły Virtual Network: operacje][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć już podsieć, która jest otagowana przy użyciu konkretnej *nazwy typu* punktu końcowego usługi Virtual Network odpowiedniej dla Azure SQL Database.

- Odpowiednia nazwa typu punktu końcowego to **Microsoft. SQL**.
- Jeśli podsieć może nie być otagowana przy użyciu nazwy typu, zobacz [Sprawdź, czy podsieć jest punktem końcowym][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal kroki

1. Zaloguj się w witrynie [Azure Portal][http-azure-portal-link-ref-477t].

2. Wyszukaj i wybierz pozycję **serwery SQL**, a następnie wybierz serwer. W obszarze **zabezpieczenia** wybierz pozycję **zapory i sieci wirtualne**.

3. Ustaw ustawienie **Zezwalaj na dostęp do usług platformy Azure** .

    > [!IMPORTANT]
    > W przypadku pozostawienia kontrolki ustawionej na wartość ON serwer akceptuje komunikację z dowolnej podsieci w ramach granicy platformy Azure, czyli pochodzącej z jednego z adresów IP, który jest rozpoznawany jako te w ramach zakresów zdefiniowanych dla centrów danych platformy Azure. Pozostawienie kontrolki ustawionej na włączone może być nadmierne dostęp z punktu widzenia zabezpieczeń. Funkcja punktu końcowego usługi Microsoft Azure Virtual Network, w połączeniu z funkcją reguły sieci wirtualnej SQL Database, umożliwia zredukowanie obszaru zabezpieczeń.

4. Kliknij przycisk **+ Dodaj istniejący** formant, w sekcji **sieci wirtualne** .

    ![Kliknij pozycję Dodaj istniejący (punkt końcowy podsieci, jako regułę SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. W okienku Nowy **Utwórz/zaktualizuj** Wypełnij kontrolki nazwami zasobów platformy Azure.

    > [!TIP]
    > Należy podać prawidłowy **prefiks adresu** dla podsieci. Wartość można znaleźć w portalu.
    > Przejdź do **wszystkich zasobów** &gt; **wszystkie typy** &gt; **sieci wirtualne**. Filtr Wyświetla sieci wirtualne. Kliknij sieć wirtualną, a następnie kliknij pozycję **podsieci**. Kolumna **zakres adresów** ma prefiks adresu, którego potrzebujesz.

    ![Wypełnij pola dla nowej reguły.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Kliknij przycisk **OK** w dolnej części okienka.

7. Zobacz zasadę reguły sieci wirtualnej w okienku Zapora.

    ![Zapoznaj się z nową regułą w okienku Zapora.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Następujące stany lub Stany stosują się do zasad:
>
> - **Gotowe:** Wskazuje, że zainicjowana operacja zakończyła się pomyślnie.
> - **Nie powiodło się:** Wskazuje, że zainicjowana operacja zakończyła się niepowodzeniem.
> - **Usunięte:** Dotyczy tylko operacji usuwania i wskazuje, że reguła została usunięta i nie ma już zastosowania.
> - W **toku:** Wskazuje, że operacja jest w toku. Stara reguła jest stosowana, gdy ta operacja jest w tym stanie.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Pokrewne artykuły:

- [Punkty końcowe usługi dla sieci wirtualnej platformy Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Reguły zapory na poziomie serwera i na poziomie bazy danych][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Następne kroki

- [Użyj programu PowerShell, aby utworzyć punkt końcowy usługi sieci wirtualnej, a następnie regułę sieci wirtualnej dla Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Reguły Virtual Network: operacje][rest-api-virtual-network-rules-operations-862r] przy użyciu interfejsów API REST

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
