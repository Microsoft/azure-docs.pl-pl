---
title: Punkty końcowe sieci wirtualnej i reguły dla pojedynczych i pulowych baz danych
description: Oznacz podsieć jako punkt końcowy usługi sieci wirtualnej. Następnie punkt końcowy jako reguła sieci wirtualnej do listy ACL usługi Azure SQL Database. Baza danych SQL następnie akceptuje komunikację ze wszystkich maszyn wirtualnych i innych węzłów w podsieci.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 7032f9e8f57ea9400bf6a92f89b13fa1866f8fc1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414391"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Używanie punktów końcowych usługi sieci wirtualnej i reguł dla serwerów baz danych

*Reguły sieci wirtualnej* to jedna funkcja zabezpieczeń zapory, która określa, czy serwer bazy danych dla pojedynczych baz danych i puli elastycznej w [usłudze](sql-database-technical-overview.md) Azure SQL Database lub dla baz danych w [magazynie SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) akceptuje komunikację, która jest wysyłana z określonych podsieci w sieciach wirtualnych. W tym artykule wyjaśniono, dlaczego funkcja reguły sieci wirtualnej jest czasami najlepszym rozwiązaniem do bezpiecznego zezwalania na komunikację z bazą danych SQL Azure i magazynem danych SQL.

> [!IMPORTANT]
> Ten artykuł dotyczy serwera SQL platformy Azure oraz baz danych SQL Database i baz danych SQL Data Warehouse utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database. Ten artykuł *nie* dotyczy wdrożenia **wystąpienia zarządzanego** w usłudze Azure SQL Database, ponieważ nie ma skojarzonego z nim punktu końcowego usługi.

Aby utworzyć regułę sieci wirtualnej, musi najpierw istnieć [punkt końcowy usługi sieci wirtualnej][vm-virtual-network-service-endpoints-overview-649d] dla reguły do odwołania.

## <a name="how-to-create-a-virtual-network-rule"></a>Jak utworzyć regułę sieci wirtualnej

Jeśli tworzysz tylko regułę sieci wirtualnej, możesz przejść do kroków i wyjaśnień [w dalszej części tego artykułu](#anchor-how-to-by-using-firewall-portal-59j).

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>Szczegółowe informacje o regułach sieci wirtualnej

W tej sekcji opisano kilka szczegółów dotyczących reguł sieci wirtualnej.

### <a name="only-one-geographic-region"></a>Tylko jeden region geograficzny

Każdy punkt końcowy usługi sieci wirtualnej ma zastosowanie tylko do jednego regionu platformy Azure. Punkt końcowy nie umożliwia innym regionom akceptowania komunikacji z podsieci.

Każda reguła sieci wirtualnej jest ograniczona do regionu, który ma zastosowanie do jej bazowego punktu końcowego.

### <a name="server-level-not-database-level"></a>Poziom serwera, a nie na poziomie bazy danych

Każda reguła sieci wirtualnej ma zastosowanie do całego serwera usługi Azure SQL Database, a nie tylko do jednej określonej bazy danych na serwerze. Innymi słowy reguła sieci wirtualnej ma zastosowanie na poziomie serwera, a nie na poziomie bazy danych.

- Natomiast reguły IP mogą mieć zastosowanie na każdym poziomie.

### <a name="security-administration-roles"></a>Role administracji zabezpieczeń

Istnieje oddzielenie ról zabezpieczeń w administrowaniu punktami końcowymi usługi sieci wirtualnej. Akcja jest wymagana od każdej z następujących ról:

- **Administrator sieci:** &nbsp; włącz punkt końcowy.
- **Administrator bazy danych:** &nbsp; Zaktualizuj listę kontroli dostępu (ACL), aby dodać daną podsiecię do serwera bazy danych SQL.

*Alternatywa RBAC:*

Role administratora sieci i administratora bazy danych mają więcej możliwości niż są potrzebne do zarządzania regułami sieci wirtualnej. Potrzebny jest tylko podzbiór ich możliwości.

Masz możliwość korzystania z [kontroli dostępu opartej na rolach (RBAC) na][rbac-what-is-813s] platformie Azure, aby utworzyć jedną rolę niestandardową, która ma tylko niezbędny podzbiór możliwości. Rola niestandardowa może być używana zamiast angażowania administratora sieci lub administratora bazy danych. Powierzchnia ekspozycji zabezpieczeń jest niższa, jeśli dodasz użytkownika do roli niestandardowej, w porównaniu do dodawania użytkownika do pozostałych dwóch głównych ról administratora.

> [!NOTE]
> W niektórych przypadkach usługa Azure SQL Database i podsieć sieci wirtualnej znajdują się w różnych subskrypcjach. W takich przypadkach należy upewnić się, że następujące konfiguracje:
>
> - Obie subskrypcje muszą znajdować się w tej samej dzierżawie usługi Azure Active Directory.
> - Użytkownik ma wymagane uprawnienia do inicjowania operacji, takich jak włączanie punktów końcowych usługi i dodawanie podsieci sieci wirtualnej do danego serwera.
> - Obie subskrypcje muszą mieć zarejestrowanego dostawcę microsoft.sql.

## <a name="limitations"></a>Ograniczenia

W przypadku usługi Azure SQL Database funkcja reguł sieci wirtualnej ma następujące ograniczenia:

- W zaporze bazy danych SQL każda reguła sieci wirtualnej odwołuje się do podsieci. Wszystkie te podsieci, do których istnieje odwołanie, muszą być hostowane w tym samym regionie geograficznym, w którym znajduje się baza danych SQL.

- Każdy serwer bazy danych SQL azure może mieć maksymalnie 128 wpisów ACL dla danej sieci wirtualnej.

- Reguły sieci wirtualnej mają zastosowanie tylko do sieci wirtualnych usługi Azure Resource Manager; a nie do klasycznych sieci [modeli wdrażania.][arm-deployment-model-568f]

- Włączenie punktów końcowych usługi sieci wirtualnej do usługi Azure SQL Database umożliwia również punkty końcowe dla usług MySQL i PostgreSQL Azure. Jednak z punktów końcowych wł., próby połączenia z punktów końcowych do wystąpień MySQL lub PostgreSQL może zakończyć się niepowodzeniem.
  - Podstawowym powodem jest to, że MySQL i PostgreSQL prawdopodobnie nie mają skonfigurowaną regułę sieci wirtualnej. Należy skonfigurować regułę sieci wirtualnej dla usługi Azure Database dla MySQL i PostgreSQL, a połączenie zakończy się pomyślnie.

- W zaporze zakresy adresów IP mają zastosowanie do następujących elementów sieci, ale reguły sieci wirtualnej nie:
  - [Wirtualna sieć prywatna (LOKACJa na lokację) (VPN)][vpn-gateway-indexmd-608y]
  - Lokalnie za pośrednictwem usługi [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Zagadnienia dotyczące korzystania z punktów końcowych usługi

Korzystając z punktów końcowych usługi dla usługi Azure SQL Database, przejrzyj następujące zagadnienia:

- **Wymagane są wychodzące z usługą Azure SQL Database Public IP:** sieciowe grupy zabezpieczeń muszą być otwarte dla witryn IP bazy danych SQL platformy Azure, aby umożliwić łączność. Można to zrobić przy użyciu [tagów usługi](../virtual-network/security-overview.md#service-tags) NSG dla usługi Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

jeśli korzystasz z usługi [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) w środowisku lokalnym na potrzeby publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, konieczne będzie określenie używanych adresów IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych stosowanych do ruchu w ramach usługi platformy Azure, gdy ruch trafia do sieci szkieletowej platformy Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP translatora adresów sieciowych są dostarczane przez klienta lub przez dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Aby zezwolić na komunikację z obwodu do usługi Azure SQL Database, należy utworzyć reguły sieci IP dla publicznych adresów IP translatora adresów sieciowych.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Wpływ używania punktów końcowych usługi sieci wirtualnej za pomocą magazynu platformy Azure

Usługa Azure Storage zaimplementowała tę samą funkcję, która umożliwia ograniczenie łączności do konta usługi Azure Storage. Jeśli zdecydujesz się użyć tej funkcji z kontem usługi Azure Storage, który jest używany przez usługę Azure SQL Server, można napotkać problemy. Dalej znajduje się lista i omówienie funkcji usługi Azure SQL Database i usługi Azure SQL Data Warehouse, na które ma to wpływ.

### <a name="azure-sql-data-warehouse-polybase"></a>PolyBase magazynu danych SQL platformy Azure

PolyBase jest często używany do ładowania danych do usługi Azure SQL Data Warehouse z kont usługi Azure Storage. Jeśli konto usługi Azure Storage, które ładujesz dane z limitów dostępu tylko do zestawu podsieci sieci wirtualnej, łączność z PolyBase do konta zostanie przerwana. Aby włączyć zarówno scenariusze importowania i eksportowania Bazy Danych PolyBase za pomocą usługi Azure SQL Data Warehouse łączącej się z usługą Azure Storage, która jest zabezpieczona w sieci wirtualnej, wykonaj kroki opisane poniżej:

#### <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj program Azure PowerShell przy użyciu tego [przewodnika](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Jeśli masz ogólne przeznaczenie v1 lub konta magazynu obiektów blob, należy najpierw uaktualnić do ogólnego przeznaczenia w wersji 2 za pomocą tego [przewodnika](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
- Musisz mieć **zezwalaj zaufanym usługom firmy Microsoft, aby uzyskać dostęp do tego konta magazynu** włączonego w menu Ustawienia konta usługi Azure Storage i sieci **wirtualnych.** Więcej informacji można znaleźć w tym [przewodniku.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)

> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów co najmniej do grudnia 2020.  Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Przedstawianie nowego modułu Az programu Azure PowerShell.](/powershell/azure/new-azureps-module-az)

#### <a name="steps"></a>Kroki

1. W programie PowerShell **zarejestruj serwer SQL Server platformy Azure,** hostując wystąpienie usługi Azure SQL Data Warehouse za pomocą usługi Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. Utwórz **konto magazynu ogólnego przeznaczenia w wersji 2,** korzystając z tego [przewodnika](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   > - Jeśli masz ogólne przeznaczenie v1 lub konta magazynu obiektów blob, należy **najpierw uaktualnić do wersji 2** za pomocą tego [przewodnika](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   > - Aby uzyskać informacje na temat znanych problemów z usługą Azure Data Lake Storage Gen2, zapoznaj się z tym [przewodnikiem.](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues)

1. W obszarze konta magazynu przejdź do **pozycji Kontrola dostępu (IAM)** i kliknij pozycję **Dodaj przypisanie roli**. Przypisz rolę RBAC **współautora danych obiektów do magazynowania magazynu magazynu** magazynu do serwera SQL, który hostuje magazyn danych SQL Azure, który został zarejestrowany w usłudze Azure Active Directory (AAD) zgodnie z krokiem #1.

   > [!NOTE]
   > Tylko członkowie z uprawnieniem Owner mogą wykonać ten krok. Aby uzyskać różne wbudowane role zasobów platformy Azure, zapoznaj się z tym [przewodnikiem.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
  
1. **Łączność Polybase z kontem usługi Azure Storage:**

   1. Utwórz **[klucz główny](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** bazy danych, jeśli nie utworzono go wcześniej:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Tworzenie poświadczeń o zakresie bazy danych za pomocą **tożsamości = "Tożsamość usługi zarządzanej":**

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       > - Nie ma potrzeby określania funkcji SECRET za pomocą klucza dostępu usługi Azure Storage, ponieważ ten mechanizm używa [tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) w ramach okładek.
       > - Nazwa tożsamości powinna być **"Tożsamość usługi zarządzanej"** dla łączności PolyBase do pracy z kontem usługi Azure Storage zabezpieczonym w sieci wirtualnej.

   1. Utwórz zewnętrzne `abfss://` źródło danych ze schematem łączenia się z kontem magazynu ogólnego przeznaczenia w wersji 2 przy użyciu bazy PolyBase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       > - Jeśli masz już tabele zewnętrzne skojarzone z kontem magazynu ogólnego przeznaczenia w wersji 1 lub blob, należy najpierw upuścić te tabele zewnętrzne, a następnie upuścić odpowiednie zewnętrzne źródło danych. Następnie utwórz zewnętrzne `abfss://` źródło danych ze schematem łączącym się z kontem magazynu ogólnego przeznaczenia w wersji 2, jak powyżej, i ponownie utwórz wszystkie tabele zewnętrzne przy użyciu tego nowego zewnętrznego źródła danych. [Kreator generowania i publikowania skryptów](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) służy do generowania skryptów tworzenia dla wszystkich tabel zewnętrznych w celu ułatwienia.
       > - Więcej informacji `abfss://` na temat schematu można znaleźć w tym [przewodniku](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Więcej informacji na temat TWORZENIA ZEWNĘTRZNEGO ŹRÓDŁA DANYCH można znaleźć w tym [przewodniku](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Kwerenda jako normalna przy użyciu [tabel zewnętrznych](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Inspekcja obiektów blob bazy danych SQL platformy Azure

Inspekcja obiektów blob wypycha dzienniki inspekcji do własnego konta magazynu. Jeśli to konto magazynu używa funkcji punktów końcowych usługi sieci wirtualnej, łączność z usługi Azure SQL Database do konta magazynu zostanie przerwana.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Dodawanie reguły zapory sieci wirtualnej do serwera bez włączania punktów końcowych usługi sieci wirtualnej

Dawno temu, zanim ta funkcja została ulepszona, trzeba było włączyć punkty końcowe usługi sieci wirtualnej, zanim można było zaimplementować regułę sieci wirtualnej na żywo w zaporze. Punkty końcowe związane danej podsieci sieci wirtualnej do bazy danych SQL azure. Ale teraz od stycznia 2018 r. można obejść to wymaganie, ustawiając **ignoreMissingVNetServiceEndpoint** flagi.

Samo ustawienie reguły zapory nie pomaga zabezpieczyć serwera. Należy również włączyć punkty końcowe usługi sieci wirtualnej, aby zabezpieczenia zostały zastosowane. Po włączeniu punktów końcowych usługi włącz, podsieć wirtualna doświadcza przestojów, dopóki nie zakończy przejścia z Off do On. Jest to szczególnie ważne w kontekście dużych sieci wirtualnych. Flaga **IgnoreMissingVNetServiceEndpoint** służy do zmniejszania lub eliminowania przestojów podczas przejścia.

Flagę **IgnoreMissingVNetServiceEndpoint** można ustawić przy użyciu programu PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Program PowerShell, aby utworzyć punkt końcowy usługi sieci wirtualnej i regułę dla bazy danych SQL Azure.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]

## <a name="errors-40914-and-40615"></a>Błędy 40914 i 40615

Błąd połączenia 40914 odnosi się do *reguł sieci wirtualnej*, jak określono w okienku zapory w witrynie Azure portal. Błąd 40615 jest podobny, z tą różnicą, że odnosi się do *reguł adresów IP* na zaporze.

### <a name="error-40914"></a>Błąd 40914

*Tekst wiadomości:* Nie można otworzyć serwera '*[nazwa serwera]*' żądanego przez login. Klient nie może uzyskać dostępu do serwera.

*Opis błędu:* Klient znajduje się w podsieci, która ma punkty końcowe serwera sieci wirtualnej. Serwer usługi Azure SQL Database nie ma jednak reguły sieci wirtualnej, która przyznaje podsieci prawo do komunikowania się z usługą SQL Database.

*Rozpoznawanie błędów:* W okienku Zapory witryny Azure portal użyj formantu reguł sieci [wirtualnej, aby dodać regułę sieci wirtualnej](#anchor-how-to-by-using-firewall-portal-59j) dla podsieci.

### <a name="error-40615"></a>Błąd 40615

*Tekst wiadomości:* Nie można{0}otworzyć serwera ' ' żądanego przez login. Klient z adresem{1}IP ' ' nie może uzyskać dostępu do serwera.

*Opis błędu:* Klient próbuje połączyć się z adresu IP, który nie jest autoryzowany do łączenia się z serwerem bazy danych SQL azure. Zapora serwera nie ma reguły adresu IP zezwalającej klientowi na komunikację z usługą SQL Database z tego adresu IP.

*Rozpoznawanie błędów:* Wprowadź adres IP klienta jako regułę IP. W tym celu użyj okienka Zapora w witrynie Azure Portal.

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal może utworzyć regułę sieci wirtualnej

W tej sekcji przedstawiono, jak można użyć [witryny Azure portal][http-azure-portal-link-ref-477t] do utworzenia *reguły sieci wirtualnej* w bazie danych SQL azure. Reguła nakazuje bazie danych SQL, aby zaakceptować komunikację z określonej podsieci, która została oznaczona jako *punkt końcowy usługi sieci wirtualnej.*

> [!NOTE]
> Jeśli zamierzasz dodać punkt końcowy usługi do reguł zapory sieci wirtualnej serwera usługi Azure SQL Database, najpierw upewnij się, że punkty końcowe usługi są włączone dla podsieci.
>
> Jeśli punkty końcowe usługi nie są włączone dla podsieci, portal prosi o ich włączenie. Kliknij przycisk **Włącz** na tym samym bloku, do którego dodasz regułę.

## <a name="powershell-alternative"></a>Alternatywa dla programu PowerShell

Skrypt można również utworzyć reguły sieci wirtualnej przy użyciu polecenia cmdlet programu PowerShell **New-AzSqlServerVirtualNetworkRule** lub [az sieci wirtualnej utworzyć](/cli/azure/network/vnet#az-network-vnet-create). Jeśli chcesz, zobacz [PowerShell, aby utworzyć punkt końcowy usługi sieci wirtualnej i regułę dla usługi Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API alternatywa

Wewnętrznie polecenia cmdlet programu PowerShell dla akcji sieci wirtualnej SQL wywołać interfejsy API REST. Interfejsy API REST można wywołać bezpośrednio.

- [Reguły sieci wirtualnej: operacje][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Wymagania wstępne

Musisz już mieć podsieć, która jest oznaczona określoną *nazwą punktu* końcowego usługi sieci wirtualnej odpowiednią dla usługi Azure SQL Database.

- Odpowiednią nazwą typu punktu końcowego jest **Microsoft.Sql**.
- Jeśli podsieć może nie być oznaczona nazwą typu, zobacz [Weryfikowanie podsieci jest punktem końcowym][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Kroki portalu platformy Azure

1. Zaloguj się w witrynie [Azure Portal][http-azure-portal-link-ref-477t].

2. Wyszukaj i wybierz **serwery SQL**, a następnie wybierz serwer. W obszarze **Zabezpieczenia**wybierz pozycję **Zapory i sieci wirtualne**.

3. Ustaw **formant Zezwalaj na dostęp do usług platformy Azure** na OFF.

    > [!IMPORTANT]
    > Jeśli pozostawisz formant ustawiony na ON, serwer usługi Azure SQL Database zaakceptuje komunikację z dowolnej podsieci wewnątrz granicy platformy Azure, czyli pochodzącej z jednego z adresów IP, który jest rozpoznawany jako w zakresach zdefiniowanych dla centrów danych platformy Azure. Pozostawienie formantu ustawionego na ON może być nadmiernym dostępem z punktu widzenia zabezpieczeń. Funkcja punktu końcowego usługi sieci wirtualnej platformy Microsoft Azure, w koordynacji z funkcją reguł sieci wirtualnej bazy danych SQL, razem można zmniejszyć obszar zabezpieczeń.

4. Kliknij **formant + Dodaj istniejący** w sekcji **Sieci wirtualne.**

    ![Kliknij dodaj istniejące (punkt końcowy podsieci, jako regułę SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. W nowym okienku **Utwórz/Aktualizuj** wypełnij formanty nazwami zasobów platformy Azure.

    > [!TIP]
    > Należy dołączyć poprawny **prefiks adresu** dla podsieci. Wartość można znaleźć w portalu.
    > Nawigacja **wszystkie zasoby** &gt; **Wszystkie typy** &gt; **Sieci wirtualne**. Filtr wyświetla sieci wirtualne. Kliknij sieć wirtualną, a następnie kliknij pozycję **Podsieci**. Kolumna **ZAKRES ADRESów** zawiera potrzebny prefiks adresu.

    ![Wypełnij pola dla nowej reguły.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Kliknij przycisk **OK** u dołu okienka.

7. Zobacz wynikową regułę sieci wirtualnej w okienku zapory.

    ![Zobacz nową regułę w okienku zapory.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Do reguły mają zastosowanie następujące stany lub stany:
>
> - **Gotowy:** Wskazuje, że zainicjowana operacja powiodła się.
> - **Nie powiodło się:** Wskazuje, że zainicjowana operacja nie powiodła się.
> - **Skasowano:** Dotyczy tylko operacji Delete i wskazuje, że reguła została usunięta i nie ma już zastosowania.
> - **InProgress:** Wskazuje, że operacja jest w toku. Stara reguła ma zastosowanie, gdy operacja jest w tym stanie.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Pokrewne artykuły:

- [Punkty końcowe usługi dla sieci wirtualnej platformy Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Reguły zapory na poziomie serwera i bazy danych usługi Azure SQL Database][sql-db-firewall-rules-config-715d]

Funkcja reguły sieci wirtualnej dla usługi Azure SQL Database stała się dostępna pod koniec września 2017 r.

## <a name="next-steps"></a>Następne kroki

- [Użyj programu PowerShell, aby utworzyć punkt końcowy usługi sieci wirtualnej, a następnie regułę sieci wirtualnej dla usługi Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Reguły sieci wirtualnej: operacje][rest-api-virtual-network-rules-operations-862r] z interfejsami API REST

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]: ../expressroute/index.yml
[rbac-what-is-813s]:../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
