---
title: 'Azure Portal: Tworzenie wystąpienia zarządzanego'
description: Utwórz SQL Database wystąpienie zarządzane, środowisko sieciowe i maszynę wirtualną klienta, aby uzyskać dostęp.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: e8a0b27f055f39186371e23e46c8b41679e05dea
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770112"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Szybki Start: Tworzenie Azure SQL Database wystąpienia zarządzanego

Ten przewodnik Szybki Start przeprowadzi Cię przez proces tworzenia Azure SQL Database [wystąpienia zarządzanego](sql-database-managed-instance.md) w programie Azure Portal.

> [!IMPORTANT]
> Aby uzyskać ograniczenia, zobacz [Obsługiwane regiony](sql-database-managed-instance-resource-limits.md#supported-regions) i [obsługiwane typy subskrypcji](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).

Zaloguj się do [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego

Poniższe kroki pokazują, jak utworzyć wystąpienie zarządzane:

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie Azure Portal. Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie w polu wyszukiwania wpisz **SQL Azure** .
2. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Wybieranie opcji wdrożenia SQL** . Można wyświetlić dodatkowe informacje o Azure SQL Database wystąpieniu zarządzanym, wybierając pozycję **Pokaż szczegóły** na kafelku **wystąpienia zarządzane** .
3. Wybierz przycisk **Utwórz**.

   ![Tworzenie wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Użyj kart w formularzu **utwórz Azure SQL Database aprowizacji wystąpienia zarządzanego** , aby dodać wymagane i opcjonalne informacje. Poniższe sekcje zawierają opis tych kart.

### <a name="basics"></a>Podstawy

- Wypełnij obowiązkowe informacje wymagane na karcie **podstawy** . Jest to minimalny zestaw informacji wymaganych do aprowizacji wystąpienia zarządzanego.

   ![Karta "podstawowe" na potrzeby tworzenia wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Skorzystaj z poniższej tabeli jako odniesienia do informacji wymaganych na tej karcie.

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Subskrypcja** | Twoja subskrypcja. | Subskrypcja, która zapewnia uprawnienia do tworzenia nowych zasobów. |
   | **Grupa zasobów** | nowa lub istniejąca grupa zasobów.|Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Reguły i ograniczenia nazewnictwa).|
   | **Nazwa wystąpienia zarządzanego** | Dowolna prawidłowa nazwa.|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/resource-naming).|
   | **Region** |Region, w którym chcesz utworzyć wystąpienie zarządzane.|Aby uzyskać informacje na temat regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).|
   | **Identyfikator logowania administratora wystąpienia zarządzanego** | Dowolna prawidłowa nazwa użytkownika. | Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](/azure/architecture/best-practices/resource-naming). Nie należy używać elementu "serveradmin", ponieważ jest to zastrzeżona rola na poziomie serwera.|
   | **Hasło** | Dowolne prawidłowe hasło.| Hasło musi mieć co najmniej 16 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Wybierz pozycję **Konfiguruj wystąpienie zarządzane** , aby zmienić rozmiar zasobów obliczeniowych i magazynu oraz przejrzeć warstwy cenowe. Użyj suwaków lub pól tekstowych, aby określić ilość pamięci i liczbę rdzeni wirtualnych. Po zakończeniu wybierz pozycję **Zastosuj** , aby zapisać swój wybór. 

   ![Formularz wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Aby przejrzeć wybór przed utworzeniem wystąpienia zarządzanego, możesz wybrać pozycję **Przegląd + Utwórz**. Lub skonfiguruj opcje sieci, wybierając pozycję **Dalej: sieć**.

### <a name="networking"></a>Networking

- Wypełnij informacje opcjonalne na karcie **Sieć** . W przypadku pominięcia tych informacji Portal będzie stosował ustawienia domyślne.

   ![Karta "Sieć" służąca do tworzenia wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Skorzystaj z poniższej tabeli jako odniesienia do informacji wymaganych na tej karcie.

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Sieć wirtualna** | Wybierz opcję **Utwórz nową sieć wirtualną** lub prawidłową sieć wirtualną i podsieć.| Jeśli sieć lub podsieć jest niedostępna, należy ją [zmodyfikować, aby spełniała wymagania sieciowe](sql-database-managed-instance-configure-vnet-subnet.md) przed wybraniem jej jako docelowej dla nowego wystąpienia zarządzanego. Aby uzyskać informacje o wymaganiach dotyczących konfigurowania środowiska sieciowego dla wystąpienia zarządzanego, zobacz [Konfigurowanie sieci wirtualnej dla wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md). |
   | **Typ połączenia** | Wybierz między serwerem proxy a typem połączenia przekierowania.|Aby uzyskać więcej informacji na temat typów połączeń, zobacz [Azure SQL Database Policy Connection](sql-database-connectivity-architecture.md#connection-policy).|
   | **Publiczny punkt końcowy**  | Wybierz pozycję **Włącz**. | Aby wystąpienie zarządzane było dostępne za pomocą publicznego punktu końcowego danych, należy włączyć tę opcję. | 
   | **Zezwalaj na dostęp z** (Jeśli **publiczny punkt końcowy** jest włączony) | Wybierz jedną z opcji.   |Środowisko portalu umożliwia skonfigurowanie grupy zabezpieczeń z publicznym punktem końcowym. </br> </br> Na podstawie Twojego scenariusza wybierz jedną z następujących opcji: </br> <ul> <li>**Usługi platformy Azure**: zalecamy korzystanie z tej opcji w przypadku łączenia się z Power BI lub z innej usługi wielodostępnej. </li> <li> **Internet**: służy do celów testowych, gdy chcesz szybko utworzyć wystąpienie zarządzane. Nie zalecamy jej w środowiskach produkcyjnych. </li> <li> **Brak dostępu**: Ta opcja tworzy regułę zabezpieczeń **Odmów** . Zmodyfikuj tę regułę, aby umożliwić dostęp do wystąpienia zarządzanego za pomocą publicznego punktu końcowego. </li> </ul> </br> Aby uzyskać więcej informacji o zabezpieczeniach publicznego punktu końcowego, zobacz [bezpieczne używanie Azure SQL Database wystąpienia zarządzanego z publicznym punktem końcowym](sql-database-managed-instance-public-endpoint-securely.md).|

- Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje przed utworzeniem wystąpienia zarządzanego. Lub skonfiguruj więcej ustawień niestandardowych, wybierając pozycję **Dalej: Ustawienia dodatkowe**.

### <a name="additional-settings"></a>Dodatkowe ustawienia

- Wypełnij opcjonalne informacje na karcie **Ustawienia dodatkowe** . W przypadku pominięcia tych informacji Portal będzie stosował ustawienia domyślne.

   ![Karta "dodatkowe ustawienia" na potrzeby tworzenia wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Skorzystaj z poniższej tabeli jako odniesienia do informacji wymaganych na tej karcie.

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   | **Sortowanie** | Wybierz sortowanie, które ma być używane dla wystąpienia zarządzanego. W przypadku migrowania baz danych z SQL Server sprawdź sortowanie źródłowe za pomocą `SELECT SERVERPROPERTY(N'Collation')` i Użyj tej wartości.| Aby uzyskać informacje o sortowaniu, zobacz [Ustawianie lub zmienianie sortowania serwera](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Strefa czasowa** | Wybierz strefę czasową, której będzie przestrzegać wystąpienie zarządzane.|Aby uzyskać więcej informacji, zobacz [strefy czasowe](sql-database-managed-instance-timezone.md).|
   | **Użyj jako dodatkowego trybu failover** | Wybierz pozycję **tak**. | Włącz tę opcję, aby użyć wystąpienia zarządzanego jako dodatkowej grupy trybu failover.|
   | **Podstawowe wystąpienie zarządzane** (Jeśli **użycie jako ustawienia pomocnicze trybu failover** jest ustawione na **wartość tak**) | Wybierz istniejące podstawowe wystąpienie zarządzane, które zostanie przyłączone do tej samej strefy DNS z tworzonym wystąpieniem zarządzanym. | W tym kroku zostanie włączona konfiguracja po utworzeniu grupy trybu failover. Aby uzyskać więcej informacji, zobacz [Samouczek: dodawanie wystąpienia zarządzanego SQL Database do grupy trybu failover](sql-database-managed-instance-failover-group-tutorial.md).|

### <a name="review--create"></a>Przegląd + tworzenie

5. Wybierz pozycję **Recenzja + Utwórz** kartę, aby przejrzeć opcje przed utworzeniem wystąpienia zarządzanego.

   ![Karta do przeglądania i tworzenia wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Wybierz pozycję **Utwórz** , aby rozpocząć Inicjowanie obsługi administracyjnej wystąpienia zarządzanego.

> [!IMPORTANT]
> Wdrożenie wystąpienia zarządzanego jest długotrwałą operacją. Wdrożenie pierwszego wystąpienia w podsieci zwykle trwa znacznie dłużej niż wdrażanie w podsieci z istniejącymi wystąpieniami zarządzanymi. Aby uzyskać średni czas udostępniania, zobacz [operacje zarządzania wystąpieniami zarządzanymi](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Monitorowanie postępu wdrażania

7. Wybierz ikonę **powiadomienia** , aby wyświetlić stan wdrożenia.

   ![Postęp wdrażania wdrożenia wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Wybierz pozycję **wdrożenie w toku** w powiadomieniu, aby otworzyć okno wystąpienia zarządzane i kontynuować monitorowanie postępu wdrażania. 

> [!TIP]
> Jeśli zamknięto przeglądarkę internetową lub przeniesiono ją z ekranu postęp wdrażania, wykonaj następujące kroki, aby odszukać ekran postępu wdrażania:
> 1. W Azure Portal Otwórz grupę zasobów (na karcie **podstawowe** ), w której wdrażasz wystąpienie zarządzane.
> 2. Wybierz pozycję **wdrożenia**.
> 3. Wybierz operację wdrażania wystąpienia zarządzanego w toku.

> [!IMPORTANT]
> Aby można było uzyskać stan tworzenia wystąpienia zarządzanego, należy mieć **uprawnienia do odczytu** grupy zasobów. Jeśli nie masz tego uprawnienia lub nie odwołajsz go, gdy wystąpienie zarządzane jest w procesie tworzenia, może to spowodować, że wystąpienie zarządzane nie będzie widoczne na liście wdrożeń grup zasobów.
>

## <a name="post-deployment-operations"></a>Operacje po wdrożeniu

Aby przejrzeć utworzone zasoby, Dostosuj ustawienia sieci i pobrać szczegóły połączenia hosta (FQDN), postępuj zgodnie z instrukcjami opisanymi w tej sekcji.

### <a name="view-resources-created"></a>Wyświetl utworzone zasoby

Po pomyślnym wdrożeniu wystąpienia zarządzanego w celu wyświetlenia utworzonych zasobów:

1. Otwórz grupę zasobów dla wystąpienia zarządzanego. Wyświetl zasoby, które zostały utworzone dla Ciebie, w oknie [Tworzenie wystąpienia zarządzanego](#create-a-managed-instance) — Szybki Start.

   ![Zasoby wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Wyświetlanie i dostosowywanie ustawień sieci

Aby Opcjonalnie dostosować ustawienia sieci, należy sprawdzić następujące kwestie:

1. Wybierz tabelę tras, aby przejrzeć zdefiniowaną przez użytkownika trasę (UDR), która została utworzona dla Ciebie.

   ![Tabela tras](./media/sql-database-managed-instance-get-started/route-table.png)

2. W tabeli tras Przejrzyj wpisy, aby kierować ruchem z i w ramach sieci wirtualnej wystąpienia zarządzanego. W przypadku ręcznego tworzenia lub konfigurowania tabeli tras upewnij się, że te wpisy zostały utworzone w tabeli tras wystąpienia zarządzanego.

   ![Wpis dla podsieci wystąpienia zarządzanego do lokalnego](./media/sql-database-managed-instance-get-started/udr.png)

3. Wróć do grupy zasobów i wybierz grupę zabezpieczeń sieci.

   ![Sieciowa grupa zabezpieczeń](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Zapoznaj się z zasadami zabezpieczeń dla ruchu przychodzącego i wychodzącego. 

   ![Reguły zabezpieczeń](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Jeśli skonfigurowano publiczny punkt końcowy dla wystąpienia zarządzanego, należy otworzyć porty, aby zezwolić na ruch sieciowy zezwalający na połączenia z wystąpieniem zarządzanym z publicznej sieci Internet. Aby uzyskać więcej informacji, zobacz [Konfigurowanie publicznego punktu końcowego dla wystąpienia zarządzanego](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) .
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Pobierz szczegóły połączenia z wystąpieniem zarządzanym

Aby nawiązać połączenie z wystąpieniem zarządzanym, wykonaj następujące kroki, aby pobrać nazwę hosta i w pełni kwalifikowaną nazwę domeny (FQDN):

1. Wróć do grupy zasobów i wybierz swoje wystąpienie zarządzane.

   ![Wystąpienie zarządzane w grupie zasobów](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. Na karcie **Przegląd** Znajdź właściwość **host** . Skopiuj nazwę hosta dla wystąpienia zarządzanego do użycia w następnym przewodniku Szybki Start.

   ![Nazwa hosta](./media/sql-database-managed-instance-get-started/host-name.png)

   Skopiowana wartość reprezentuje w pełni kwalifikowaną nazwę domeny (FQDN), która może być używana do nawiązywania połączenia z wystąpieniem zarządzanym. Jest podobny do następującego przykładu adresu: *your_host_name. a1b2c3d4e5f6. Database. Windows. NET*.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak nawiązać połączenie z wystąpieniem zarządzanym:
- Aby zapoznać się z omówieniem opcji połączenia dla aplikacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md).
- Aby zapoznać się z przewodnikiem Szybki Start, który pokazuje, jak nawiązać połączenie z wystąpieniem zarządzanym z maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie połączenia z maszyną wirtualną platformy Azure](sql-database-managed-instance-configure-vm.md).
- Aby zapoznać się z przewodnikiem Szybki Start, który pokazuje, jak połączyć się z wystąpieniem zarządzanym z lokalnego komputera klienckiego przy użyciu połączenia typu punkt-lokacja, zobacz [Konfigurowanie połączenia punkt-lokacja](sql-database-managed-instance-configure-p2s.md).

Aby przywrócić istniejącą bazę danych SQL Server z lokalnego do wystąpienia zarządzanego: 
- Użyj [Azure Database Migration Service do migracji](../dms/tutorial-sql-server-to-managed-instance.md) , aby przywrócić z pliku kopii zapasowej bazy danych. 
- Użyj [polecenia Przywróć T-SQL](sql-database-managed-instance-get-started-restore.md) , aby przywrócić plik kopii zapasowej bazy danych.

Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpień zarządzanych przy użyciu wbudowanej analizy rozwiązywania problemów, zobacz [monitorowanie Azure SQL Database przy użyciu Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
