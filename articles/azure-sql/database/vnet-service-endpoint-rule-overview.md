---
title: Punkty końcowe sieci wirtualnej i reguły dla baz danych w Azure SQL Database
description: Oznacz podsieć jako punkt końcowy usługi sieci wirtualnej. Następnie Dodaj punkt końcowy jako regułę sieci wirtualnej do listy ACL dla bazy danych. Twoja baza danych akceptuje komunikację ze wszystkimi maszynami wirtualnymi i innymi węzłami w podsieci.
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
ms.openlocfilehash: d480239c0eb99ed48c13ec2fdb5b052574acc318
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092503"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Korzystanie z punktów końcowych usługi sieci wirtualnej i reguł dla serwerów w Azure SQL Database

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*Reguły sieci wirtualnej* to funkcja zabezpieczeń zapory, która kontroluje, czy serwer baz danych i pul elastycznych w [Azure SQL Database](sql-database-paas-overview.md) lub dla baz danych w [usłudze Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) akceptuje komunikaty wysyłane z określonych podsieci w sieciach wirtualnych. W tym artykule opisano, dlaczego reguły sieci wirtualnej są czasami najlepszą opcją bezpiecznego zezwalania na komunikację z bazą danych w SQL Database i analizą Azure Synapse.

> [!NOTE]
> Ten artykuł dotyczy programu SQL Database i usługi Azure Synapse Analytics. Dla uproszczenia termin *baza danych* odnosi się do obu baz danych w SQL Database i Azure Synapse Analytics. Podobnie wszystkie odwołania do *serwera* odnoszą się do [logicznego serwera SQL](logical-servers.md) , który hostuje SQL Database i usługi Azure Synapse Analytics.

Aby można było utworzyć regułę sieci wirtualnej, musi ona być [punktem końcowym usługi sieci wirtualnej][vm-virtual-network-service-endpoints-overview-649d] , aby uzyskać odwołanie do reguły.

## <a name="create-a-virtual-network-rule"></a>Tworzenie reguły sieci wirtualnej

Jeśli chcesz utworzyć tylko regułę sieci wirtualnej, możesz przejść do kroków i wyjaśnień [w dalszej części tego artykułu](#anchor-how-to-by-using-firewall-portal-59j).

## <a name="details-about-virtual-network-rules"></a>Szczegóły dotyczące reguł sieci wirtualnej

W tej sekcji opisano kilka szczegółowych informacji o regułach sieci wirtualnej.

### <a name="only-one-geographic-region"></a>Tylko jeden region geograficzny

Każdy punkt końcowy usługi sieci wirtualnej ma zastosowanie tylko do jednego regionu platformy Azure. Punkt końcowy nie umożliwia innym regionom akceptowania komunikacji z podsieci.

Każda reguła sieci wirtualnej jest ograniczona do regionu, do którego odnosi się podstawowy punkt końcowy.

### <a name="server-level-not-database-level"></a>Poziom serwera, nie na poziomie bazy danych

Każda reguła sieci wirtualnej ma zastosowanie do całego serwera, a nie tylko do jednej konkretnej bazy danych na serwerze. Innymi słowy reguły sieci wirtualnej są stosowane na poziomie serwera, a nie na poziomie bazy danych.

Z kolei reguły adresów IP mogą być stosowane na dowolnym poziomie.

### <a name="security-administration-roles"></a>Role administrowania zabezpieczeniami

Istnieje separacja ról zabezpieczeń w administrowaniu punktami końcowymi usługi sieci wirtualnej. Wymagana jest akcja z każdej z następujących ról:

- **Administrator sieci (rola [współautor sieci](../../role-based-access-control/built-in-roles.md#network-contributor) ):** &nbsp; Włącz punkt końcowy.
- **Administrator bazy danych (rola [współautora SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) ):** &nbsp; Zaktualizuj listę kontroli dostępu (ACL), aby dodać daną podsieć do serwera.

#### <a name="azure-rbac-alternative"></a>Alternatywa dla usługi Azure RBAC

Role administratora sieci i administratora bazy danych mają więcej możliwości niż jest to konieczne do zarządzania regułami sieci wirtualnej. Wymagany jest tylko podzestaw ich możliwości.

Istnieje możliwość korzystania z [kontroli dostępu opartej na rolach (RBAC)][rbac-what-is-813s] na platformie Azure w celu utworzenia jednej roli niestandardowej, która ma tylko niezbędny podzbiór funkcji. Rola niestandardowa może być używana zamiast obejmuje administratora sieci lub administratora bazy danych. Powierzchnia obszaru ekspozycji zabezpieczeń jest niższa w przypadku dodania użytkownika do roli niestandardowej, a nie dodania użytkownika do innych dwóch głównych ról administratora.

> [!NOTE]
> W niektórych przypadkach baza danych w SQL Database i podsieć sieci wirtualnej znajdują się w różnych subskrypcjach. W takich przypadkach należy zapewnić następujące konfiguracje:
>
> - Obie subskrypcje muszą znajdować się w tej samej dzierżawie usługi Azure Active Directory (Azure AD).
> - Użytkownik ma wymagane uprawnienia do inicjowania operacji, takich jak Włączanie punktów końcowych usługi i Dodawanie podsieci sieci wirtualnej do danego serwera.
> - Obie subskrypcje muszą mieć zarejestrowany dostawca Microsoft. SQL.

## <a name="limitations"></a>Ograniczenia

W przypadku SQL Database funkcja reguł sieci wirtualnej ma następujące ograniczenia:

- W zaporze dla bazy danych w SQL Database Każda reguła sieci wirtualnej odwołuje się do podsieci. Wszystkie te podsieci, do których istnieją odwołania, muszą być hostowane w tym samym regionie geograficznym, który hostuje bazę danych.
- Każdy serwer może mieć do 128 wpisów listy ACL dla dowolnej sieci wirtualnej.
- Reguły sieci wirtualnej mają zastosowanie tylko do Azure Resource Manager sieci wirtualnych, a nie do [klasycznych sieci modeli wdrażania][arm-deployment-model-568f] .
- Włączenie punktów końcowych usługi sieci wirtualnej do SQL Database umożliwia również punkty końcowe dla Azure Database for MySQL i Azure Database for PostgreSQL. W przypadku punktów końcowych ustawionych na wartość **on** program próbuje nawiązać połączenie z punktów końcowych do Azure Database for MySQL lub wystąpienia Azure Database for PostgreSQL mogą się nie powieść.
  - Podstawowym powodem jest to, że Azure Database for MySQL i Azure Database for PostgreSQL prawdopodobnie nie mają skonfigurowanej reguły sieci wirtualnej. Należy skonfigurować regułę sieci wirtualnej dla Azure Database for MySQL i Azure Database for PostgreSQL, a połączenie zakończy się pomyślnie.
  - Aby zdefiniować reguły zapory sieci wirtualnej na serwerze logicznym SQL, który jest już skonfigurowany za pomocą prywatnych punktów końcowych, ustaw opcję **Odmów dostępu do sieci publicznej** na wartość **nie**.
- Na zaporze zakresy adresów IP dotyczą następujących elementów sieciowych, ale reguły sieci wirtualnej nie są następujące:
  - [Wirtualna sieć prywatna (VPN) typu lokacja-lokacja (S2S)][vpn-gateway-indexmd-608y]
  - Lokalna za pośrednictwem [usługi Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Zagadnienia dotyczące korzystania z punktów końcowych usługi

Korzystając z punktów końcowych usługi dla SQL Database, zapoznaj się z następującymi kwestiami:

- **Wymagane jest wychodzące do Azure SQL Database publicznych adresów IP.** Grupy zabezpieczeń sieci (sieciowych grup zabezpieczeń) muszą zostać otwarte, aby SQL Database adresy IP w celu umożliwienia łączności. Można to zrobić za pomocą [tagów usługi](../../virtual-network/network-security-groups-overview.md#service-tags) sieciowej grupy zabezpieczeń dla SQL Database.

### <a name="expressroute"></a>ExpressRoute

W przypadku korzystania z [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) z lokalnego elementu równorzędnego lub komunikacji równorzędnej firmy Microsoft należy zidentyfikować używane adresy IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych stosowanych do ruchu w ramach usługi platformy Azure, gdy ruch trafia do sieci szkieletowej platformy Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP translatora adresów sieciowych są udostępniane przez klienta lub dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Aby dowiedzieć się więcej o translatorze adresów sieciowych dla usługi ExpressRoute Public i komunikacji równorzędnej firmy Microsoft, zobacz [wymagania NAT dotyczące publicznej komunikacji równorzędnej Azure](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

Aby umożliwić komunikację z obwodu do SQL Database, należy utworzyć reguły sieci IP dla publicznych adresów IP translatora adresów sieciowych.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Wpływ używania punktów końcowych usługi sieci wirtualnej w usłudze Azure Storage

Usługa Azure Storage zaimplementowała tę samą funkcję, która umożliwia ograniczenie łączności z kontem usługi Azure Storage. Jeśli zdecydujesz się używać tej funkcji z kontem usługi Azure Storage używanym do SQL Database, możesz napotkać problemy. Poniżej znajduje się lista i Omówienie funkcji SQL Database i usługi Azure Synapse Analytics, których to dotyczy.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Zestawienie bazowe i COPY usługi Azure Synapse Analytics

Instrukcje Base i COPY są często używane do ładowania danych do usługi Azure Synapse Analytics z kont usługi Azure Storage w celu pozyskiwania danych o wysokiej przepływności. Jeśli konto usługi Azure Storage, z którego są ładowane dane, ogranicza dostęp tylko do zestawu podsieci sieci wirtualnej, połączenie w przypadku używania bazy danych i instrukcji COPY do konta magazynu zostanie przerwane. Aby umożliwić Importowanie i eksportowanie scenariuszy przy użyciu kopii i bazy danych z usługą Azure Synapse Analytics łączącej się z usługą Azure Storage, która jest zabezpieczona w sieci wirtualnej, wykonaj kroki opisane w tej sekcji.

#### <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj Azure PowerShell, korzystając z [tego przewodnika](/powershell/azure/install-az-ps).
- Jeśli masz konto ogólnego przeznaczenia w wersji 1 lub Azure Blob Storage, musisz najpierw przeprowadzić uaktualnienie do ogólnego przeznaczenia w wersji 2, wykonując czynności opisane w sekcji [uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](../../storage/common/storage-account-upgrade.md).
- Musisz **zezwolić zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** , włączone w menu ustawienia **zapory i sieci wirtualnych** konta usługi Azure Storage. Włączenie tej konfiguracji umożliwi użycie instrukcji Base i COPY w celu nawiązania połączenia z kontem magazynu przy użyciu silnego uwierzytelniania w przypadku, gdy ruch sieciowy pozostaje w sieci szkieletowej platformy Azure. Aby uzyskać więcej informacji, zobacz [ten przewodnik](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020. Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Kroki

1. Jeśli masz autonomiczną dedykowaną pulę SQL, zarejestruj swój program SQL Server w usłudze Azure AD przy użyciu programu PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Ten krok nie jest wymagany w przypadku dedykowanych pul SQL w obszarze roboczym usługi Azure Synapse Analytics.

1. Jeśli masz obszar roboczy usługi Azure Synapse Analytics, zarejestruj tożsamość zarządzaną przez system obszaru roboczego:

   1. Przejdź do obszaru roboczego usługi Azure Synapse Analytics w Azure Portal.
   2. Przejdź do okienka **zarządzane tożsamości** .
   3. Upewnij się, że opcja **Zezwalaj na potoki** jest włączona.
   
1. Utwórz **konto magazynu ogólnego przeznaczenia w wersji 2** , wykonując kroki opisane w temacie [Tworzenie konta magazynu](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Jeśli masz konto ogólnego przeznaczenia w wersji 1 lub Blob Storage, musisz *najpierw przeprowadzić uaktualnienie do wersji 2* , wykonując czynności opisane w sekcji [uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](../../storage/common/storage-account-upgrade.md).
   > - Znane problemy dotyczące Azure Data Lake Storage Gen2 można znaleźć w temacie [znane problemy z Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md).

1. W obszarze konto magazynu przejdź do pozycji **Access Control (IAM)**, a następnie wybierz pozycję **Dodaj przypisanie roli**. Przypisz rolę **współautor danych obiektów BLOB usługi Storage** do serwera lub obszaru roboczego obsługującego dedykowaną pulę SQL, która została zarejestrowana w usłudze Azure AD.

   > [!NOTE]
   > Tylko członkowie z uprawnieniami właściciela na koncie magazynu mogą wykonać ten krok. W przypadku różnych wbudowanych ról platformy Azure Zapoznaj się z [wbudowanymi rolami platformy Azure](../../role-based-access-control/built-in-roles.md).
  
1. Aby włączyć połączenie wielopodstawowe z kontem usługi Azure Storage:

   1. Utwórz [klucz główny](/sql/t-sql/statements/create-master-key-transact-sql) bazy danych, jeśli nie został utworzony wcześniej.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Utwórz poświadczenie o zakresie bazy danych o **tożsamości = "tożsamość usługi zarządzanej"**.

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Nie ma potrzeby określania wpisu TAJNego za pomocą klucza dostępu do usługi Azure Storage, ponieważ ten mechanizm używa [tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md) w ramach okładek.
       > - Nazwa tożsamości powinna mieć **wartość "tożsamość usługi zarządzanej"** dla łączności wielopodstawowej do pracy z kontem usługi Azure Storage zabezpieczonym w sieci wirtualnej.

   1. Utwórz zewnętrzne źródło danych ze `abfss://` schematem do nawiązania połączenia z kontem magazynu ogólnego przeznaczenia w wersji 2 za pomocą sieci podstawowej.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Jeśli masz już tabele zewnętrzne skojarzone z kontem ogólnego przeznaczenia w wersji 1 lub Blob Storage, musisz najpierw porzucić te tabele zewnętrzne. Następnie Porzuć odpowiednie zewnętrzne źródło danych. Następnie utwórz zewnętrzne źródło danych ze `abfss://` schematem, który łączy się z kontem magazynu ogólnego przeznaczenia w wersji 2, jak pokazano wcześniej. Następnie utwórz ponownie wszystkie tabele zewnętrzne przy użyciu tego nowego zewnętrznego źródła danych. [Kreatora generowania i publikowania skryptów](/sql/ssms/scripting/generate-and-publish-scripts-wizard) można użyć do łatwego generowania skryptów Create-scripts dla wszystkich tabel zewnętrznych.
       > - Aby uzyskać więcej informacji na temat `abfss://` schematu, zobacz [Korzystanie z identyfikatora URI Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Aby uzyskać więcej informacji na temat tworzenia zewnętrznego źródła danych, zobacz [ten przewodnik](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Wykonaj zapytanie jako normalne przy użyciu [tabel zewnętrznych](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="sql-database-blob-auditing"></a>Inspekcja SQL Database obiektów BLOB

Inspekcja obiektów BLOB powoduje wypychanie dzienników inspekcji na własne konto magazynu. Jeśli to konto magazynu używa funkcji punktów końcowych usługi sieci wirtualnej, nastąpi przerwanie łączności z SQL Database na konto magazynu.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Dodawanie reguły zapory sieci wirtualnej do serwera

Dawno temu przed zwiększeniem tej funkcji, przed zaimplementowaniem reguły sieci wirtualnej na żywo w zaporze musisz włączyć punkty końcowe usługi sieci wirtualnej. Punkty końcowe powiązane z określoną podsiecią sieci wirtualnej do bazy danych w SQL Database. Od stycznia 2018 można obejść to wymaganie, ustawiając flagę **IgnoreMissingVNetServiceEndpoint** . Teraz możesz dodać regułę zapory sieci wirtualnej do serwera bez włączania punktów końcowych usługi sieci wirtualnej.

Samo ustawienie reguły zapory nie zabezpiecza serwera. Aby zabezpieczenia zaczęły obowiązywać, należy również włączyć punkty końcowe usługi sieci wirtualnej. Po włączeniu punktów końcowych usługi podsieć sieci wirtualnej będzie przestojować do momentu zakończenia przejścia z wyłączone na włączone. Ten okres przestoju jest szczególnie prawdziwy w kontekście dużych sieci wirtualnych. Możesz użyć flagi **IgnoreMissingVNetServiceEndpoint** , aby zmniejszyć lub wyeliminować przestoje podczas przejścia.

Flagę **IgnoreMissingVNetServiceEndpoint** można ustawić przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz Program [PowerShell, aby utworzyć punkt końcowy usługi sieci wirtualnej i regułę dla SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Błędy 40914 i 40615

Błąd połączenia 40914 odnosi się do *reguł sieci wirtualnej* określonych w okienku **Zapora** w Azure Portal. Błąd 40615 jest podobny, z tą różnicą, że odnosi się do *reguł adresów IP* w zaporze.

### <a name="error-40914"></a>Błąd 40914

**Tekst komunikatu:** "Nie można otworzyć serwera"*[nazwa serwera]*"żądanego podczas logowania. Klient nie może uzyskać dostępu do serwera ".

**Opis błędu:** Klient należy do podsieci, która ma punkty końcowe serwera sieci wirtualnej. Ale serwer nie ma reguły sieci wirtualnej, która przyznaje podsieci prawo do komunikacji z bazą danych.

**Rozwiązywanie błędów:** W okienku **Zapora** w Azure Portal użyj kontrolki reguły sieci wirtualnej, aby [dodać regułę sieci wirtualnej](#anchor-how-to-by-using-firewall-portal-59j) dla podsieci.

### <a name="error-40615"></a>Błąd 40615

**Tekst komunikatu:** "Nie można otworzyć serwera" {0} "żądanego podczas logowania. Klient z adresem IP " {1} " nie może uzyskać dostępu do serwera ".

**Opis błędu:** Klient próbuje nawiązać połączenie z adresu IP, który nie jest autoryzowany do łączenia się z serwerem. Zapora serwera nie ma żadnej reguły adresu IP, która umożliwia klientowi komunikowanie się z podanym adresem IP z bazą danych.

**Rozwiązywanie błędów:** Wprowadź adres IP klienta jako regułę adresu IP. Użyj okienka **Zapora** w Azure Portal, aby wykonać ten krok.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Tworzenie reguły sieci wirtualnej przy użyciu portalu

W tej sekcji pokazano, jak można użyć [Azure Portal][http-azure-portal-link-ref-477t] do utworzenia *reguły sieci wirtualnej* w bazie danych programu SQL Database. Reguła nakazuje bazie danych akceptowanie komunikacji z określonej podsieci, która jest oznaczona jako *punkt końcowy usługi sieci wirtualnej*.

> [!NOTE]
> Jeśli zamierzasz dodać punkt końcowy usługi do reguł zapory sieci wirtualnej serwera, najpierw upewnij się, że punkty końcowe usługi są włączone dla tej podsieci.
>
> Jeśli punkty końcowe usługi nie są włączone dla podsieci, w portalu zostanie wyświetlony monit o ich włączenie. Wybierz przycisk **Włącz** w tym samym okienku, w którym została dodana reguła.

## <a name="powershell-alternative"></a>Alternatywa dla programu PowerShell

Skrypt może również tworzyć reguły sieci wirtualnej przy użyciu polecenia cmdlet programu PowerShell **New-AzSqlServerVirtualNetworkRule** lub [AZ Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create). Jeśli interesuje Cię, zobacz Program [PowerShell, aby utworzyć punkt końcowy usługi sieci wirtualnej i regułę dla SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternatywa dla interfejsu API REST

Wewnętrznie polecenia cmdlet programu PowerShell dla akcji sieci wirtualnej SQL wywołują interfejsy API REST. Interfejsy API REST można wywołać bezpośrednio.

- [Reguły sieci wirtualnej: operacje][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć już podsieć oznakowaną *nazwą określonego typu* punktu końcowego usługi sieci wirtualnej, która ma zastosowanie do SQL Database.

- Odpowiednia nazwa typu punktu końcowego to **Microsoft. SQL**.
- Jeśli podsieć może nie być otagowana przy użyciu nazwy typu, zobacz [Sprawdź, czy podsieć jest punktem końcowym][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal kroki

1. Zaloguj się w witrynie [Azure Portal][http-azure-portal-link-ref-477t].

1. Wyszukaj i wybierz pozycję **serwery SQL**, a następnie wybierz serwer. W obszarze **zabezpieczenia** wybierz pozycję **zapory i sieci wirtualne**.

1. Ustaw opcję **Zezwalaj na dostęp do usług platformy Azure** **.**

    > [!IMPORTANT]
    > Jeśli pozostawisz formant ustawiony na wartość **włączone**, serwer akceptuje komunikację z dowolnych podsieci w ramach granicy platformy Azure. Jest to komunikacja, która pochodzi z jednego z adresów IP, który jest rozpoznawany jako te w ramach zakresów zdefiniowanych dla centrów danych platformy Azure. Pozostawienie kontrolki ustawionej **na włączone** może być nadmierne dostęp z punktu widzenia zabezpieczeń. Funkcja punktu końcowego usługi Microsoft Azure Virtual Network w koordynacji z funkcją reguł sieci wirtualnej SQL Database jednocześnie umożliwia zredukowanie obszaru zabezpieczeń.

1. Wybierz pozycję **+ Dodaj istniejące** w sekcji **sieci wirtualne** .

    ![Zrzut ekranu, który pokazuje Wybieranie i Dodawanie istniejącego (punkt końcowy podsieci, jako regułę SQL).][image-portal-firewall-vnet-add-existing-10-png]

1. W okienku Nowy **Utwórz/zaktualizuj** Wypełnij pola nazwami zasobów platformy Azure.

    > [!TIP]
    > Należy podać prawidłowy prefiks adresu dla podsieci. Wartość **prefiksu adresu** można znaleźć w portalu. Przejdź do pozycji **wszystkie zasoby** &gt; **wszystkie typy** &gt; **sieci wirtualne**. Filtr Wyświetla sieci wirtualne. Wybierz sieć wirtualną, a następnie wybierz pozycję **podsieci**. Kolumna **zakres adresów** ma prefiks adresu, którego potrzebujesz.

    ![Zrzut ekranu pokazujący wypełnienie pól dla nowej reguły.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Wybierz przycisk **OK** w dolnej części okienka.

1. Zobacz zasadę reguły sieci wirtualnej w okienku **Zapora** .

    ![Zrzut ekranu pokazujący nową regułę w okienku zapory.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Następujące stany lub Stany stosują się do zasad:
>
> - **Gotowe**: wskazuje, że zainicjowana operacja zakończyła się pomyślnie.
> - **Niepowodzenie**: wskazuje, że zainicjowana operacja zakończyła się niepowodzeniem.
> - **Usunięte**: dotyczy tylko operacji usuwania i wskazuje, że reguła została usunięta i nie ma już zastosowania.
> - W **toku**: wskazuje, że operacja jest w toku. Stara reguła jest stosowana, gdy ta operacja jest w tym stanie.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Pokrewne artykuły:

- [Punkty końcowe usługi dla sieci wirtualnej platformy Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Reguły zapory na poziomie serwera i na poziomie bazy danych][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Następne kroki

- [Użyj programu PowerShell, aby utworzyć punkt końcowy usługi sieci wirtualnej, a następnie regułę sieci wirtualnej dla SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Reguły sieci wirtualnej: operacje][rest-api-virtual-network-rules-operations-862r] przy użyciu interfejsów API REST

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
