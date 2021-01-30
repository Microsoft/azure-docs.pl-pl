---
title: 'Szybki Start: Tworzenie wystąpienia zarządzanego usługi Azure SQL (Portal)'
description: Utwórz wystąpienie zarządzane, środowisko sieciowe i maszynę wirtualną klienta, aby uzyskać dostęp za pomocą Azure Portal w tym przewodniku Szybki Start.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 1/29/2021
ms.openlocfilehash: 95b721f12e4818a77f18d01c99a5c6d60b881169
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089707"
---
# <a name="quickstart-create-an-azure-sql-managed-instance"></a>Szybki Start: Tworzenie wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ten przewodnik Szybki Start uczy się, jak utworzyć [wystąpienie zarządzane Azure SQL](sql-managed-instance-paas-overview.md) w Azure Portal.

> [!IMPORTANT]
> Aby uzyskać ograniczenia, zobacz [Obsługiwane regiony](resource-limits.md#supported-regions) i [obsługiwane typy subskrypcji](resource-limits.md#supported-subscription-types).

## <a name="create-an-azure-sql-managed-instance"></a>Tworzenie wystąpienia zarządzanego Azure SQL

Aby utworzyć wystąpienie zarządzane SQL, wykonaj następujące kroki: 

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz pozycję **Azure SQL** w menu po lewej stronie Azure Portal. Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie w polu wyszukiwania wpisz **SQL Azure** .
1. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Wybieranie opcji wdrożenia SQL** . Aby wyświetlić dodatkowe informacje na temat wystąpienia zarządzanego Azure SQL, wybierz pozycję **Pokaż szczegóły** na kafelku **wystąpienia zarządzane SQL** .
1. Wybierz przycisk **Utwórz**.

   ![Tworzenie wystąpienia zarządzanego](./media/instance-create-quickstart/create-azure-sql-managed-instance.png)

4. Karty w formularzu **Tworzenie wystąpienia zarządzanego usługi Azure SQL** umożliwiają dodawanie wymaganych i opcjonalnych informacji. Poniższe sekcje zawierają opis tych kart.

### <a name="basics-tab"></a>Karta Podstawowe

- Wypełnij obowiązkowe informacje wymagane na karcie **podstawy** . Jest to minimalny zestaw informacji wymaganych do aprowizacji wystąpienia zarządzanego SQL.

   ![Karta "podstawowe" służąca do tworzenia wystąpienia zarządzanego SQL](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-basics.png)

   Skorzystaj z poniższej tabeli jako odniesienia do informacji wymaganych na tej karcie.

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Subskrypcja** | Twoja subskrypcja. | Subskrypcja, która zapewnia uprawnienia do tworzenia nowych zasobów. |
   | **Grupa zasobów** | nowa lub istniejąca grupa zasobów.|Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Reguły i ograniczenia nazewnictwa).|
   | **Nazwa wystąpienia zarządzanego** | Dowolna prawidłowa nazwa.|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/resource-naming).|
   | **Region** |Region, w którym chcesz utworzyć wystąpienie zarządzane.|Aby uzyskać informacje na temat regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).|
   | **Identyfikator logowania administratora wystąpienia zarządzanego** | Dowolna prawidłowa nazwa użytkownika. | Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/resource-naming). Nie należy używać elementu "serveradmin", ponieważ jest to zastrzeżona rola na poziomie serwera.|
   | **Password** (Hasło) | Dowolne prawidłowe hasło.| Hasło musi mieć co najmniej 16 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Wybierz pozycję **Konfiguruj wystąpienie zarządzane** , aby zmienić rozmiar zasobów obliczeniowych i magazynu oraz przejrzeć warstwy cenowe. Użyj suwaków lub pól tekstowych, aby określić ilość pamięci i liczbę rdzeni wirtualnych. Po zakończeniu wybierz pozycję **Zastosuj** , aby zapisać swój wybór. 

   ![Formularz wystąpienia zarządzanego](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-configure-performance.png)

| Ustawienie| Sugerowana wartość | Opis |
| ------ | --------------- | ----------- |
| **Warstwa usług** | Wybierz jedną z opcji. | Na podstawie Twojego scenariusza wybierz jedną z następujących opcji: </br> <ul><li>**Ogólnego przeznaczenia**: w przypadku większości obciążeń produkcyjnych i opcji domyślnej.</li><li>**Krytyczne dla działania firmy**: zaprojektowana do obsługi obciążeń o małym opóźnieniu i wysokiej odporności na awarie i szybkie przełączanie do trybu failover.</li></ul><BR>Aby uzyskać więcej informacji, zobacz [Azure SQL Database i warstwy usługi wystąpienia zarządzanego Azure SQL](../../azure-sql/database/service-tiers-general-purpose-business-critical.md) . Zapoznaj się [z omówieniem limitów zasobów wystąpienia zarządzanego Azure SQL](../../azure-sql/managed-instance/resource-limits.md).|
| **Generowanie sprzętu** | Wybierz jedną z opcji. | Generowanie sprzętu zwykle definiuje limity obliczeń i pamięci oraz inne właściwości, które wpływają na wydajność obciążenia. Wartość domyślna to **5 rdzeń** .|
| **model obliczeniowy rdzeń wirtualny** | Wybierz opcję. | Rdzeni wirtualnych reprezentuje dokładną ilość zasobów obliczeniowych, które są zawsze obsługiwane dla obciążenia. Wartością domyślną jest **osiem rdzeni wirtualnych** .|
| **Magazyn w GB** | Wybierz opcję. | Rozmiar magazynu w GB zaznacz na podstawie oczekiwanego rozmiaru danych. W przypadku migrowania istniejących danych z lokalnego lub różnych platform w chmurze zobacz [Omówienie migracji: SQL Server do wystąpienia zarządzanego SQL](../../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-overview.md).|
| **Korzyść użycia hybrydowego platformy Azure** | Zaznacz opcję Jeśli ma zastosowanie. | Korzystanie z istniejącej licencji na platformę Azure. Aby uzyskać więcej informacji, zobacz [korzyść użycia hybrydowego platformy Azure-Azure SQL Database & wystąpienie zarządzane SQL](../../azure-sql/azure-hybrid-benefit.md). |
| **Nadmiarowość magazynu kopii zapasowych** | Wybierz **Geograficznie nadmiarowy magazyn kopii zapasowych**. | Nadmiarowość magazynu na platformie Azure na potrzeby magazynu kopii zapasowych. Należy pamiętać, że tej wartości nie można zmienić później. Geograficznie nadmiarowy magazyn kopii zapasowych jest domyślny i zalecany, chociaż strefa i lokalna nadmiarowość zapewniają większą elastyczność kosztów i miejsce zamieszkania danych w jednym regionie. Aby uzyskać więcej informacji, zobacz [nadmiarowość magazynu kopii zapasowych](../database/automated-backups-overview.md?tabs=managed-instance#backup-storage-redundancy).|


- Aby przejrzeć wybory przed utworzeniem wystąpienia zarządzanego SQL, możesz wybrać pozycję **Przegląd + Utwórz**. Lub skonfiguruj opcje sieci, wybierając pozycję **Dalej: sieć**.

### <a name="networking-tab"></a>Karta Sieć

- Wypełnij informacje opcjonalne na karcie **Sieć** . W przypadku pominięcia tych informacji Portal będzie stosował ustawienia domyślne.

   ![Karta "Sieć" służąca do tworzenia wystąpienia zarządzanego](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-networking.png)

   Skorzystaj z poniższej tabeli jako odniesienia do informacji wymaganych na tej karcie.

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Sieć wirtualna** | Wybierz opcję **Utwórz nową sieć wirtualną** lub prawidłową sieć wirtualną i podsieć.| Jeśli sieć lub podsieć jest niedostępna, należy ją [zmodyfikować, aby spełniała wymagania sieciowe](vnet-existing-add-subnet.md) przed wybraniem jej jako docelowej dla nowego wystąpienia zarządzanego. Aby uzyskać informacje o wymaganiach dotyczących konfigurowania środowiska sieciowego dla wystąpienia zarządzanego SQL, zobacz [Konfigurowanie sieci wirtualnej dla wystąpienia zarządzanego SQL](connectivity-architecture-overview.md). |
   | **Connection type** (Typ połączenia) | Wybierz między serwerem proxy a typem połączenia przekierowania.|Aby uzyskać więcej informacji na temat typów połączeń, zobacz [Typ połączenia wystąpienia zarządzanego Azure SQL](../database/connectivity-architecture.md#connection-policy).|
   | **Publiczny punkt końcowy**  | Wybierz pozycję **Wyłącz**. | Aby wystąpienie zarządzane było dostępne za pomocą publicznego punktu końcowego danych, należy włączyć tę opcję. | 
   | **Zezwalaj na dostęp z** (Jeśli **publiczny punkt końcowy** jest włączony) | Wybierz **Brak dostępu**  |Środowisko portalu umożliwia skonfigurowanie grupy zabezpieczeń z publicznym punktem końcowym. </br> </br> Na podstawie Twojego scenariusza wybierz jedną z następujących opcji: </br> <ul> <li>**Usługi platformy Azure**: zalecamy korzystanie z tej opcji w przypadku łączenia się z Power BI lub z innej usługi wielodostępnej. </li> <li> **Internet**: służy do celów testowych, gdy chcesz szybko utworzyć wystąpienie zarządzane. Nie zalecamy jej w środowiskach produkcyjnych. </li> <li> **Brak dostępu**: Ta opcja tworzy regułę zabezpieczeń **Odmów** . Zmodyfikuj tę regułę, aby umożliwić dostęp do wystąpienia zarządzanego za pomocą publicznego punktu końcowego. </li> </ul> </br> Aby uzyskać więcej informacji o zabezpieczeniach publicznego punktu końcowego, zobacz [bezpieczne używanie wystąpienia zarządzanego usługi Azure SQL z publicznym punktem końcowym](public-endpoint-overview.md).|

- Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje przed utworzeniem wystąpienia zarządzanego. Lub skonfiguruj więcej ustawień niestandardowych, wybierając pozycję **Dalej: Ustawienia dodatkowe**.


### <a name="additional-settings"></a>Ustawienia dodatkowe

- Wypełnij opcjonalne informacje na karcie **Ustawienia dodatkowe** . W przypadku pominięcia tych informacji Portal będzie stosował ustawienia domyślne.

   ![Karta "dodatkowe ustawienia" na potrzeby tworzenia wystąpienia zarządzanego](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-additional-settings.png)

   Skorzystaj z poniższej tabeli jako odniesienia do informacji wymaganych na tej karcie.

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Sortowanie** | Wybierz sortowanie, które ma być używane dla wystąpienia zarządzanego. W przypadku migrowania baz danych z SQL Server sprawdź sortowanie źródłowe za pomocą `SELECT SERVERPROPERTY(N'Collation')` i Użyj tej wartości.| Aby uzyskać informacje o sortowaniu, zobacz [Ustawianie lub zmienianie sortowania serwera](/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Strefa czasowa** | Wybierz strefę czasową, której będzie przestrzegać wystąpienie zarządzane.|Aby uzyskać więcej informacji, zobacz [strefy czasowe](timezones-overview.md).|
   | **Użyj jako dodatkowego trybu failover** | Wybierz pozycję **Tak**. | Włącz tę opcję, aby użyć wystąpienia zarządzanego jako dodatkowej grupy trybu failover.|
   | **Główne wystąpienie zarządzane SQL** (jeśli jest **używane jako pomocnicza praca awaryjna** jest ustawiona na **wartość tak**) | Wybierz istniejące podstawowe wystąpienie zarządzane, które zostanie przyłączone do tej samej strefy DNS z tworzonym wystąpieniem zarządzanym. | W tym kroku zostanie włączona konfiguracja po utworzeniu grupy trybu failover. Aby uzyskać więcej informacji, zobacz [Samouczek: dodawanie wystąpienia zarządzanego do grupy trybu failover](failover-group-add-instance-tutorial.md).|

- Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje przed utworzeniem wystąpienia zarządzanego. Lub skonfiguruj Tagi platformy Azure, wybierając kolejno pozycje **Dalej: Tagi** (zalecane).

### <a name="tags"></a>Tagi

- Dodawanie tagów do zasobów w szablonie Azure Resource Manager (szablon ARM). [Tagi](/azure/azure-resource-manager/management/tag-resources) ułatwiają logiczne organizowanie zasobów. Wartości tagów są wyświetlane w raportach kosztów i zezwalają na inne działania związane z zarządzaniem przez tag. 

- Rozważ co najmniej tagowanie nowego wystąpienia zarządzanego SQL za pomocą tagu Owner, aby zidentyfikować, kto utworzył, i znacznik środowiska, aby określić, czy ten system jest produkcyjny, programistyczny itp. Aby uzyskać więcej informacji, zobacz [opracowywanie strategii nazewnictwa i tagowania dla zasobów platformy Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
 
- Wybierz pozycję **Recenzja + Utwórz** , aby rozpocząć.

## <a name="review--create"></a>Przeglądanie i tworzenie

1. Wybierz pozycję **Recenzja + Utwórz** kartę, aby przejrzeć opcje przed utworzeniem wystąpienia zarządzanego.

   ![Karta do przeglądania i tworzenia wystąpienia zarządzanego](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-review-create.png)

1. Wybierz pozycję **Utwórz** , aby rozpocząć Inicjowanie obsługi administracyjnej wystąpienia zarządzanego.

> [!IMPORTANT]
> Wdrożenie wystąpienia zarządzanego jest długotrwałą operacją. Wdrożenie pierwszego wystąpienia w podsieci zwykle trwa znacznie dłużej niż wdrażanie w podsieci z istniejącymi wystąpieniami zarządzanymi. Aby uzyskać średni czas udostępniania, zobacz [Omówienie operacji zarządzania wystąpieniami zarządzanymi przez usługę Azure SQL](management-operations-overview.md#duration).

## <a name="monitor-deployment-progress"></a>Monitorowanie postępu wdrażania

1. Wybierz ikonę **powiadomienia** , aby wyświetlić stan wdrożenia.

   ![Postęp wdrażania wdrożenia wystąpienia zarządzanego SQL](./media/instance-create-quickstart/azure-sql-managed-instance-create-deployment-in-progress.png)

1. Wybierz pozycję **wdrożenie w toku** w powiadomieniu, aby otworzyć okno wystąpienia zarządzane SQL, a następnie ponownie monitorować postęp wdrażania. 

> [!TIP]
> - Jeśli zamknięto przeglądarkę sieci Web lub przeniesiono ją z ekranu postęp wdrażania, można monitorować operację aprowizacji za pośrednictwem strony **omówienia** wystąpienia zarządzanego lub za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie operacji](management-operations-monitor.md#monitor-operations). 
> - Proces aprowizacji można anulować za pośrednictwem Azure Portal lub za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure lub innych narzędzi przy użyciu interfejsu API REST. Zobacz [anulowanie operacji zarządzania wystąpieniami zarządzanymi przez usługę Azure SQL](management-operations-cancel.md).

> [!IMPORTANT]
> - Rozpoczęcie tworzenia wystąpienia zarządzanego przez program SQL może być opóźnione w przypadkach, gdy istnieją inne operacje, takie jak operacje przywracania lub skalowania na innych wystąpieniach zarządzanych w tej samej podsieci. Aby dowiedzieć się więcej, zobacz [wpływ na operacje zarządzania](management-operations-overview.md#management-operations-cross-impact).
> - Aby można było uzyskać stan tworzenia wystąpienia zarządzanego, należy mieć **uprawnienia do odczytu** grupy zasobów. Jeśli nie masz tego uprawnienia lub nie odwołajsz go podczas procesu tworzenia wystąpienia zarządzanego, może to spowodować, że wystąpienie zarządzane SQL nie będzie widoczne na liście wdrożeń grup zasobów.
>

## <a name="view-resources-created"></a>Wyświetl utworzone zasoby

Po pomyślnym wdrożeniu wystąpienia zarządzanego w celu wyświetlenia utworzonych zasobów:

1. Otwórz grupę zasobów dla wystąpienia zarządzanego. 

   ![Zasoby wystąpienia zarządzanego SQL](./media/instance-create-quickstart/azure-sql-managed-instance-resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Wyświetlanie i dostosowywanie ustawień sieci

Aby Opcjonalnie dostosować ustawienia sieci, należy sprawdzić następujące kwestie:

1. Na liście zasobów wybierz tabelę tras, aby przejrzeć utworzony obiekt tabeli tras zdefiniowanych przez użytkownika (UDR).

2. W tabeli trasa Sprawdź wpisy, które umożliwiają kierowanie ruchu z i do sieci wirtualnej wystąpienia zarządzanego SQL. W przypadku ręcznego tworzenia lub konfigurowania tabeli tras należy utworzyć te wpisy w tabeli tras wystąpienia zarządzanego SQL.

   ![Wpis dla podsieci wystąpienia zarządzanego SQL do lokalnego](./media/instance-create-quickstart/azure-sql-managed-instance-route-table-user-defined-route.png)

    Aby zmienić lub dodać trasy, Otwórz **trasy** w ustawieniach tabeli tras.

3. Wróć do grupy zasobów i wybierz utworzony obiekt sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń).

4. Zapoznaj się z zasadami zabezpieczeń dla ruchu przychodzącego i wychodzącego. 

   ![Reguły zabezpieczeń](./media/instance-create-quickstart/azure-sql-managed-instance-security-rules.png)

    Aby zmienić lub dodać reguły, Otwórz reguły zabezpieczeń **ruchu przychodzącego** i **reguły zabezpieczeń ruchu wychodzącego** w ustawieniach sieciowej grupy zabezpieczeń.

> [!IMPORTANT]
> Jeśli skonfigurowano publiczny punkt końcowy dla wystąpienia zarządzanego SQL, należy otworzyć porty, aby zezwolić na ruch sieciowy umożliwiający nawiązywanie połączeń z wystąpieniem zarządzanym SQL z publicznego Internetu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie publicznego punktu końcowego dla wystąpienia zarządzanego SQL](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Pobieranie szczegółów połączenia z usługą SQL Managed Instance

Aby nawiązać połączenie z wystąpieniem zarządzanym SQL, wykonaj następujące kroki, aby pobrać nazwę hosta i w pełni kwalifikowaną nazwę domeny (FQDN):

1. Wróć do grupy zasobów i wybierz obiekt wystąpienia zarządzanego SQL, który został utworzony.

2. Na karcie **Przegląd** Znajdź właściwość **host** . Skopiuj nazwę hosta do Schowka w celu użycia wystąpienia zarządzanego w następnym przewodniku Szybki Start, klikając przycisk **Kopiuj do schowka** .

   ![Nazwa hosta](./media/instance-create-quickstart/azure-sql-managed-instance-host-name.png)

   Skopiowana wartość reprezentuje w pełni kwalifikowaną nazwę domeny (FQDN), która może być używana do nawiązywania połączenia z wystąpieniem zarządzanym SQL. Jest podobny do następującego przykładu adresu: *your_host_name. a1b2c3d4e5f6. Database. Windows. NET*.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak nawiązać połączenie z wystąpieniem zarządzanym SQL:
- Aby zapoznać się z omówieniem opcji połączenia dla aplikacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym SQL](connect-application-instance.md).
- Aby zapoznać się z przewodnikiem Szybki Start, który pokazuje, jak połączyć się z wystąpieniem zarządzanym SQL z maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie połączenia z maszyną wirtualną platformy Azure](connect-vm-instance-configure.md).
- Aby zapoznać się z przewodnikiem Szybki Start, który pokazuje, jak połączyć się z wystąpieniem zarządzanym SQL z lokalnego komputera klienckiego przy użyciu połączenia typu punkt-lokacja, zobacz [Konfigurowanie połączenia punkt-lokacja](point-to-site-p2s-configure.md).

Aby przywrócić istniejącą bazę danych SQL Server z wystąpienia zarządzanego na serwerze SQL: 
- Użyj [Azure Database Migration Service do migracji](../../dms/tutorial-sql-server-to-managed-instance.md) , aby przywrócić z pliku kopii zapasowej bazy danych. 
- Użyj [polecenia Przywróć T-SQL](restore-sample-database-quickstart.md) , aby przywrócić plik kopii zapasowej bazy danych.

Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpień zarządzanych przez usługę SQL z wbudowaną analizą rozwiązywania problemów, zobacz [monitorowanie wystąpienia zarządzanego Azure SQL przy użyciu Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
