---
title: Reguły i punkty końcowe sieci wirtualnej dla baz danych w Azure SQL Database
description: Oznacz podsieć jako punkt końcowy usługi dla sieci wirtualnej. Następnie dodaj punkt końcowy jako regułę sieci wirtualnej do listy ACL dla bazy danych. Następnie baza danych akceptuje komunikację ze wszystkich maszyn wirtualnych i innych węzłów w podsieci.
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
ms.openlocfilehash: 67e807e948caf1fec014457814c1b7f105630f9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784429"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Używanie reguł i punktów końcowych usługi sieci wirtualnej dla serwerów w przypadku usługi Azure SQL Database

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

 Reguły sieci wirtualnej to funkcja zabezpieczeń zapory, która kontroluje, czy serwer baz danych i pul elastycznych w programie [Azure SQL Database](sql-database-paas-overview.md) czy dla dedykowanych baz danych puli SQL (dawniej SQL DW) w programie [Azure Synapse Analytics akceptuje](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) komunikację, która jest wysyłana z określonych podsieci w sieciach wirtualnych. W tym artykule wyjaśniono, dlaczego reguły sieci wirtualnej są czasami najlepszą opcją umożliwiającą bezpieczną komunikację z bazą danych w SQL Database i Azure Synapse Analytics.

> [!NOTE]
> Ten artykuł dotyczy zarówno SQL Database, jak i Azure Synapse Analytics. Dla uproszczenia termin baza danych *odnosi* się do obu baz danych w SQL Database i Azure Synapse Analytics. Podobnie wszelkie odwołania do serwera *odnoszą się do* logicznego serwera [SQL,](logical-servers.md) który hostuje SQL Database i Azure Synapse Analytics.

Aby utworzyć regułę sieci wirtualnej, [][vm-virtual-network-service-endpoints-overview-649d] należy najpierw utworzyć punkt końcowy usługi dla sieci wirtualnej, do których reguła ma się odwoływać.

## <a name="create-a-virtual-network-rule"></a>Tworzenie reguły sieci wirtualnej

Jeśli chcesz utworzyć tylko regułę sieci wirtualnej, możesz przejść od razu do kroków i wyjaśnienia w [dalszej części tego artykułu.](#anchor-how-to-by-using-firewall-portal-59j)

## <a name="details-about-virtual-network-rules"></a>Szczegółowe informacje o zasadach sieci wirtualnej

W tej sekcji opisano kilka szczegółów dotyczących reguł sieci wirtualnej.

### <a name="only-one-geographic-region"></a>Tylko jeden region geograficzny

Każdy punkt końcowy usługi dla sieci wirtualnej ma zastosowanie tylko do jednego regionu świadczenia usługi Azure. Punkt końcowy nie umożliwia innym regionom akceptowania komunikacji z podsieci.

Każda reguła sieci wirtualnej jest ograniczona do regionu, do których ma zastosowanie jej podstawowy punkt końcowy.

### <a name="server-level-not-database-level"></a>Poziom serwera, a nie poziom bazy danych

Każda reguła sieci wirtualnej ma zastosowanie do całego serwera, a nie tylko do jednej konkretnej bazy danych na serwerze. Innymi słowy, reguły sieci wirtualnej są stosowane na poziomie serwera, a nie na poziomie bazy danych.

Z kolei reguły adresów IP mogą być stosowane na każdym poziomie.

### <a name="security-administration-roles"></a>Role administracji zabezpieczeniami

Istnieje separacja ról zabezpieczeń w administrowaniu punktami końcowymi usługi dla sieci wirtualnej. Wymagana jest akcja dla każdej z następujących ról:

- **Administrator sieci [(rola Współautor sieci):](../../role-based-access-control/built-in-roles.md#network-contributor)** &nbsp; Włącz punkt końcowy.
- **Administrator bazy danych [(SQL Server współautora):](../../role-based-access-control/built-in-roles.md#sql-server-contributor)** &nbsp; Zaktualizuj listę kontroli dostępu (ACL), aby dodać podsieć do serwera.

#### <a name="azure-rbac-alternative"></a>Alternatywa RBAC platformy Azure

Role administratora sieci i administratora bazy danych mają więcej możliwości niż jest to konieczne do zarządzania regułami sieci wirtualnej. Wymagany jest tylko podzbiór ich możliwości.

Możesz użyć kontroli dostępu opartej na rolach [(RBAC)][rbac-what-is-813s] na platformie Azure, aby utworzyć jedną rolę niestandardową, która ma tylko niezbędny podzbiór możliwości. Roli niestandardowej można użyć zamiast angażowania administratora sieci lub administratora bazy danych. Obszar powierzchni zagrożenia bezpieczeństwa jest mniejszy, jeśli dodasz użytkownika do roli niestandardowej, a nie dodasz użytkownika do dwóch pozostałych głównych ról administratora.

> [!NOTE]
> W niektórych przypadkach baza danych w SQL Database i podsieć sieci wirtualnej znajdują się w różnych subskrypcjach. W takich przypadkach należy zapewnić następujące konfiguracje:
>
> - Obie subskrypcje muszą znajdować się w tej samej Azure Active Directory dzierżawy (Azure AD).
> - Użytkownik ma uprawnienia wymagane do inicjowania operacji, takich jak włączanie punktów końcowych usługi i dodawanie podsieci sieci wirtualnej do danego serwera.
> - Obie subskrypcje muszą mieć zarejestrowanego dostawcę Microsoft.Sql.

## <a name="limitations"></a>Ograniczenia

W SQL Database reguły sieci wirtualnej mają następujące ograniczenia:

- W zaporze dla bazy danych w SQL Database każda reguła sieci wirtualnej odwołuje się do podsieci. Wszystkie te podsieci, do których się odwołujesz, muszą być hostowane w tym samym regionie geograficznym, który hostuje bazę danych.
- Każdy serwer może mieć maksymalnie 128 wpisów listy ACL dla dowolnej sieci wirtualnej.
- Reguły sieci wirtualnej mają zastosowanie tylko do Azure Resource Manager wirtualnych, a nie do sieci [klasycznego modelu wdrażania.][arm-deployment-model-568f]
- Włączenie punktów końcowych usługi dla sieci wirtualnej w celu SQL Database umożliwia również korzystanie z punktów końcowych Azure Database for MySQL i Azure Database for PostgreSQL. Jeśli punkty końcowe są **ustawione na wartość ON**, próby nawiązania połączenia z punktów końcowych do Azure Database for MySQL lub Azure Database for PostgreSQL mogą się nie powieść.
  - Podstawowa przyczyna jest taka, Azure Database for MySQL i Azure Database for PostgreSQL prawdopodobnie nie mają skonfigurowanej reguły sieci wirtualnej. Należy skonfigurować regułę sieci wirtualnej dla Azure Database for MySQL i Azure Database for PostgreSQL, aby połączenie powiodło się.
  - Aby zdefiniować reguły zapory sieci wirtualnej na serwerze logicznym SQL,  który jest już skonfigurowany z prywatnymi punktami końcowymi, ustaw wartość Nie dla ustawienia Odmów dostępu do sieci **publicznej.**
- W zaporze zakresy adresów IP mają zastosowanie do następujących elementów sieci, ale reguły sieci wirtualnej nie:
  - [Wirtualna sieć prywatna (VPN) typu lokacja-lokacja (S2S)][vpn-gateway-indexmd-608y]
  - Lokalnie za pośrednictwem [usługi Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Zagadnienia dotyczące korzystania z punktów końcowych usługi

Jeśli używasz punktów końcowych usługi na SQL Database, zapoznaj się z następującymi zagadnieniami:

- **Wymagany jest ruch wychodzący Azure SQL Database publicznych ip.** Aby umożliwić łączność, sieciowe grupy zabezpieczeń muszą być otwarte SQL Database IP. Można to zrobić przy użyciu tagów usługi sieciowej [SQL Database.](../../virtual-network/network-security-groups-overview.md#service-tags)

### <a name="expressroute"></a>ExpressRoute

Jeśli używasz usługi [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ze swojej firmy na potrzeby publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, musisz zidentyfikować używane adresy IP nat. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych stosowanych do ruchu w ramach usługi platformy Azure, gdy ruch trafia do sieci szkieletowej platformy Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP nat są udostępniane przez klienta lub dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Aby dowiedzieć się więcej na temat nat dla publicznej komunikacji równorzędnej ExpressRoute i komunikacji równorzędnej firmy Microsoft, zobacz Wymagania dotyczące nat dla publicznej [komunikacji równorzędnej Azure.](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

Aby umożliwić komunikację z obwodu do SQL Database, należy utworzyć reguły sieci IP dla publicznych adresów IP twojego nat.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Wpływ używania punktów końcowych usługi dla sieci wirtualnej w usłudze Azure Storage

Usługa Azure Storage zaimplementowała tę samą funkcję, która umożliwia ograniczenie łączności z kontem usługi Azure Storage. Jeśli zdecydujesz się używać tej funkcji z kontem usługi Azure Storage, z SQL Database korzystasz, możesz mieć problemy. Następnie znajduje się lista i omówienie SQL Database i Azure Synapse Analytics, na które ma to wpływ.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase i instrukcja COPY

Program PolyBase i instrukcja COPY są często używane do ładowania danych Azure Synapse Analytics z kont usługi Azure Storage w celu pozyskiwania danych o wysokiej przepływności. Jeśli konto usługi Azure Storage ładowane z limitów uzyskuje dostęp tylko do zestawu podsieci sieci wirtualnej, łączność podczas korzystania z programu PolyBase i instrukcji COPY z kontem magazynu zostanie zerwana. W przypadku włączania scenariuszy importowania i eksportowania przy użyciu funkcji COPY i PolyBase z programem Azure Synapse Analytics łączy się z usługą Azure Storage zabezpieczoną z siecią wirtualną, wykonaj kroki opisane w tej sekcji.

#### <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj Azure PowerShell, korzystając z [tego przewodnika.](/powershell/azure/install-az-ps)
- Jeśli masz konto ogólnego przeznaczenia w wersji 1 lub Azure Blob Storage, musisz najpierw wykonać uaktualnienie do konta ogólnego przeznaczenia w wersji 2, korzystając z procedury Uaktualnienia do konta magazynu ogólnego przeznaczenia w wersji [2.](../../storage/common/storage-account-upgrade.md)
- Opcja **Zezwalaj** zaufanym usługi firmy Microsoft na dostęp do tego konta magazynu musi być włączona w menu ustawień Zapory i sieci **wirtualne** konta usługi Azure Storage. Włączenie tej konfiguracji umożliwi programowi PolyBase i instrukcji COPY łączenie się z kontem magazynu przy użyciu silnego uwierzytelniania, w którym ruch sieciowy pozostaje w sieci szkieletowej platformy Azure. Aby uzyskać więcej informacji, zobacz [ten przewodnik](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> Moduł powershell Azure Resource Manager jest nadal obsługiwany przez SQL Database, ale cały przyszły rozwój jest dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Argumenty poleceń w module Az i modułach AzureRm są w znacznym stopniu identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Wprowadzenie do nowego modułu Azure PowerShell Az.](/powershell/azure/new-azureps-module-az)

#### <a name="steps"></a>Kroki

1. Jeśli masz autonomiczną dedykowaną pulę SQL, zarejestruj serwer SQL w usłudze Azure AD przy użyciu programu PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Ten krok nie jest wymagany w przypadku dedykowanych pul SQL w Azure Synapse Analytics roboczym.

1. Jeśli masz obszar Azure Synapse Analytics, zarejestruj tożsamość zarządzaną przez system obszaru roboczego:

   1. Przejdź do swojego Azure Synapse Analytics roboczego w Azure Portal.
   2. Przejdź do **okienka Tożsamości zarządzane.**
   3. Upewnij się, **że opcja Zezwalaj na potoki** jest włączona.
   
1. Utwórz konto **magazynu ogólnego przeznaczenia w wersji 2,** korzystając z procedury opisanej w tece Tworzenie konta [magazynu.](../../storage/common/storage-account-create.md)

   > [!NOTE]
   >
   > - Jeśli masz konto ogólnego przeznaczenia w wersji 1 lub Blob Storage, musisz najpierw wykonać uaktualnienie do wersji *2,* korzystając z procedury uaktualnienia do konta magazynu ogólnego przeznaczenia w [wersji 2.](../../storage/common/storage-account-upgrade.md)
   > - Aby uzyskać informacje o znanych problemach z Azure Data Lake Storage Gen2, [zobacz Znane problemy z Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md).

1. W obszarze konta magazynu przejdź do Access Control **(IAM)** i wybierz **pozycję Dodaj przypisanie roli.** Przypisz rolę **Współautor danych obiektu blob** usługi Storage na platformie Azure do serwera lub obszaru roboczego hostowania dedykowanej puli SQL zarejestrowanej w usłudze Azure AD.

   > [!NOTE]
   > Ten krok mogą wykonać tylko członkowie z uprawnieniami właściciela na koncie magazynu. Aby uzyskać informacje na temat różnych wbudowanych ról platformy Azure, zobacz [Role wbudowane platformy Azure.](../../role-based-access-control/built-in-roles.md)
  
1. Aby włączyć łączność programu PolyBase z kontem usługi Azure Storage:

   1. Utwórz klucz [główny bazy danych,](/sql/t-sql/statements/create-master-key-transact-sql) jeśli jeszcze go nie utworzono.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Utwórz poświadczenie o zakresie bazy danych z **tożsamością = "Tożsamość usługi zarządzanej".**

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Nie ma potrzeby określania klucza SECRET z kluczem [](../../active-directory/managed-identities-azure-resources/overview.md) dostępu usługi Azure Storage, ponieważ ten mechanizm korzysta z tożsamości zarządzanej.
       > - Nazwa TOŻSAMOŚCI powinna mieć nazwę **"Tożsamość usługi zarządzanej",** aby łączność programu PolyBase działała z kontem usługi Azure Storage zabezpieczonym w sieci wirtualnej.

   1. Utwórz zewnętrzne źródło danych ze schematem łączenia się z kontem magazynu ogólnego przeznaczenia w wersji 2 przy `abfss://` użyciu technologii PolyBase.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Jeśli masz już tabele zewnętrzne skojarzone z kontem ogólnego przeznaczenia w wersji 1 lub Blob Storage, najpierw należy usunąć te tabele zewnętrzne. Następnie upuść odpowiednie zewnętrzne źródło danych. Następnie utwórz zewnętrzne źródło danych ze schematem, który łączy się z kontem magazynu ogólnego przeznaczenia w wersji `abfss://` 2, jak pokazano wcześniej. Następnie utwórz ponownie wszystkie tabele zewnętrzne przy użyciu tego nowego zewnętrznego źródła danych. W celu ułatwienia [generowania](/sql/ssms/scripting/generate-and-publish-scripts-wizard) skryptów dla wszystkich tabel zewnętrznych można użyć Kreatora generowania i publikowania skryptów.
       > - Aby uzyskać więcej informacji na temat `abfss://` schematu, zobacz [Używanie Azure Data Lake Storage Gen2 URI](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Aby uzyskać więcej informacji na temat TWORZENIA ZEWNĘTRZNEGO ŹRÓDŁA DANYCH, zobacz [ten przewodnik](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Wykonywanie zapytań w zwykły sposób przy użyciu [tabel zewnętrznych](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="sql-database-blob-auditing"></a>SQL Database inspekcji obiektów blob

Inspekcja obiektów blob wypycha dzienniki inspekcji na własne konto magazynu. Jeśli to konto magazynu używa funkcji punktów końcowych usługi dla sieci wirtualnej, łączność SQL Database z kontem magazynu zostanie zerwana.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Dodawanie reguły zapory sieci wirtualnej do serwera

Wcześniej, zanim ta funkcja została rozszerzona, trzeba było włączyć punkty końcowe usługi dla sieci wirtualnej, zanim można było zaimplementować regułę sieci wirtualnej na żywo w zaporze. Punkty końcowe powiązane z podsieć danej sieci wirtualnej z bazą danych w SQL Database. Od stycznia 2018 r. można obejść to wymaganie, ustawiając flagę **IgnoreMissingVNetServiceEndpoint.** Teraz możesz dodać regułę zapory sieci wirtualnej do serwera bez włączania punktów końcowych usługi dla sieci wirtualnej.

Samo ustawienie reguły zapory nie pomaga zabezpieczyć serwera. Aby zabezpieczenia obowiązywały, należy również włączyć punkty końcowe usługi dla sieci wirtualnej. Po włączeniu punktów końcowych usługi podsieć sieci wirtualnej ma przestój do momentu ukończenia przejścia z wyłączonej do włączonej. Ten okres przestoju jest szczególnie prawdziwy w kontekście dużych sieci wirtualnych. Możesz użyć **flagi IgnoreMissingVNetServiceEndpoint,** aby zmniejszyć lub wyeliminować przestój podczas przejścia.

Flagę **IgnoreMissingVNetServiceEndpoint** można ustawić przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [PowerShell, aby utworzyć punkt końcowy][sql-db-vnet-service-endpoint-rule-powershell-md-52d]usługi dla sieci wirtualnej i regułę dla SQL Database .

## <a name="errors-40914-and-40615"></a>Błędy 40914 i 40615

Błąd połączenia 40914 jest związany z *regułami* sieci wirtualnej określonymi w okienku Zapora w Azure Portal.  Błąd 40615 jest podobny, z tą różnicą, że odnosi się do *reguł adresów IP* w zaporze.

### <a name="error-40914"></a>Błąd 40914

**Tekst komunikatu:** "Nie można otworzyć serwera *' [nazwa-serwera]*' żądanego podczas logowania. Klient nie może uzyskać dostępu do serwera".

**Opis błędu:** Klient znajduje się w podsieci, która ma punkty końcowe serwera sieci wirtualnej. Jednak serwer nie ma reguły sieci wirtualnej, która przyznaje podsieci prawo do komunikowania się z bazą danych.

**Rozwiązanie błędu:** W **okienku** Zapora Azure Portal użyj kontrolki reguły sieci wirtualnej, aby [dodać](#anchor-how-to-by-using-firewall-portal-59j) regułę sieci wirtualnej dla podsieci.

### <a name="error-40615"></a>Błąd 40615

**Tekst komunikatu:** "Nie można otworzyć serwera {0} ' ' żądanego podczas logowania. Klient o adresie IP {1} " " nie może uzyskać dostępu do serwera".

**Opis błędu:** Klient próbuje nawiązać połączenie z adresu IP, który nie ma autoryzacji do nawiązywania połączenia z serwerem. Zapora serwera nie ma reguły adresu IP, która umożliwia klientowi komunikację z danego adresu IP do bazy danych.

**Rozwiązanie błędu:** Wprowadź adres IP klienta jako regułę adresów IP. W tym **kroku** użyj okienka Azure Portal na stronie.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Tworzenie reguły sieci wirtualnej przy użyciu portalu

W tej sekcji pokazano, jak za pomocą  Azure Portal [utworzyć][http-azure-portal-link-ref-477t] regułę sieci wirtualnej w bazie danych w SQL Database. Reguła nakazuje bazie danych akceptowanie komunikacji z określonej podsieci, która została oznaczona jako punkt końcowy usługi *dla sieci wirtualnej.*

> [!NOTE]
> Jeśli zamierzasz dodać punkt końcowy usługi do reguł zapory sieci wirtualnej serwera, najpierw upewnij się, że punkty końcowe usługi są włączone dla podsieci.
>
> Jeśli punkty końcowe usługi nie są włączone dla podsieci, portal poprosi o ich włączenie. Wybierz przycisk **Włącz** w tym samym okienku, w którym dodajesz regułę.

## <a name="powershell-alternative"></a>Alternatywa dla programu PowerShell

Skrypt może również tworzyć reguły sieci wirtualnej za pomocą polecenia cmdlet programu PowerShell **New-AzSqlServerVirtualNetworkRule** lub [az network vnet create.](/cli/azure/network/vnet#az_network_vnet_create) Jeśli Cię to interesuje, zobacz [PowerShell,][sql-db-vnet-service-endpoint-rule-powershell-md-52d]aby utworzyć punkt końcowy usługi dla sieci wirtualnej i regułę dla SQL Database .

## <a name="rest-api-alternative"></a>Alternatywa dla interfejsu API REST

Wewnętrznie polecenia cmdlet programu PowerShell dla akcji sieci wirtualnej SQL wywołują interfejsy API REST. Interfejsy API REST można wywołać bezpośrednio.

- [Reguły sieci wirtualnej: Operacje][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć już podsieć oznaczoną określoną nazwą typu  punktu końcowego usługi dla sieci wirtualnej odpowiednią dla SQL Database.

- Odpowiednią nazwą typu punktu końcowego jest **Microsoft.Sql.**
- Jeśli podsieć może nie być oznaczona za pomocą nazwy typu, zobacz Weryfikowanie, czy [podsieć jest punktem końcowym][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal kroki

1. Zaloguj się w witrynie [Azure Portal][http-azure-portal-link-ref-477t].

1. Wyszukaj i wybierz pozycję **Serwery SQL,** a następnie wybierz serwer. W **obszarze Zabezpieczenia** wybierz pozycję Zapory i sieci **wirtualne.**

1. Dla **ustawienia Zezwalaj na dostęp do usług platformy Azure** ustaw wartość **WYŁ.**

    > [!IMPORTANT]
    > Jeśli pozostawisz kontrolkę ustawioną **na WŁ.,** serwer zaakceptuje komunikację z dowolnej podsieci w granicach platformy Azure. Jest to komunikacja pochodząca z jednego z adresów IP rozpoznanych jako adresy w zakresach zdefiniowanych dla centrów danych platformy Azure. Pozostawienie kontrolki ustawionej na **WARTOŚĆ WŁ.** może być nadmiernym dostępem z punktu widzenia zabezpieczeń. Funkcja Microsoft Azure Virtual Network punktu końcowego usługi we współpracy z funkcją reguł sieci wirtualnej SQL Database może zmniejszyć obszar powierzchni zabezpieczeń.

1. Wybierz **pozycję + Dodaj istniejące** w sekcji **Sieci** wirtualne.

    ![Zrzut ekranu przedstawiający wybieranie + Dodaj istniejący (punkt końcowy podsieci jako regułę SQL).][image-portal-firewall-vnet-add-existing-10-png]

1. W nowym **okienku Tworzenie/aktualizowanie** wypełnij pola nazwami zasobów platformy Azure.

    > [!TIP]
    > Należy dołączyć poprawny prefiks adresu dla podsieci. Wartość Prefiks **adresu można** znaleźć w portalu. Przejdź do **wszystkich zasobów Wszystkie** &gt; **typy** Sieci &gt; **wirtualne.** Filtr wyświetla sieci wirtualne. Wybierz sieć wirtualną, a następnie wybierz **pozycję Podsieci.** Kolumna **ZAKRES ADRESÓW** zawiera potrzebny prefiks adresu.

    ![Zrzut ekranu przedstawiający wypełnianie pól dla nowej reguły.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Wybierz przycisk **OK** w dolnej części okienka.

1. Zobacz wynikową regułę sieci wirtualnej w **okienku** Zapora.

    ![Zrzut ekranu przedstawiający nową regułę w okienku Zapora.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Następujące stany mają zastosowanie do reguł:
>
> - **Gotowe:** wskazuje, że zainicjowana operacja zakończyła się pomyślnie.
> - **Niepowodzenie:** wskazuje, że zainicjowana operacja nie powiodła się.
> - **Usunięto:** dotyczy tylko operacji Usuwania i wskazuje, że reguła została usunięta i nie ma już jej zastosowanie.
> - **InProgress:** wskazuje, że operacja jest w toku. Stara reguła ma zastosowanie, gdy operacja jest w tym stanie.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Pokrewne artykuły:

- [Punkty końcowe usługi dla sieci wirtualnej platformy Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Reguły zapory na poziomie serwera i na poziomie bazy danych][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Następne kroki

- [Użyj programu PowerShell, aby utworzyć punkt końcowy usługi dla sieci wirtualnej, a następnie regułę sieci wirtualnej dla SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Reguły sieci wirtualnej: Operacje z][rest-api-virtual-network-rules-operations-862r] interfejsami API REST

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
