---
title: Samouczek do oceny wystąpień SQL na potrzeby migracji do wystąpienia zarządzanego usługi Azure SQL i Azure SQL Database
description: Dowiedz się, jak utworzyć ocenę usługi Azure SQL w Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: 9b33890d53f67eee870b42462a65b4a0b7ba9981
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055533"
---
# <a name="tutorial-assess-sql-instances-for-migration-to-azure-sql"></a>Samouczek: ocenianie wystąpień SQL na potrzeby migracji do usługi Azure SQL

W ramach kursu migracji do platformy Azure oceniasz swoje obciążenia lokalne, aby mierzyć gotowość do chmury, identyfikować zagrożenia i oceniać koszty i złożoność.
W tym artykule opisano sposób oceny odnalezionych baz danych wystąpień SQL Server w przygotowaniu do migracji do usługi Azure SQL przy użyciu narzędzia Azure Migrate do odnajdywania i oceny.

> [!Note]
> Odnajdywanie i Ocena SQL Server wystąpień i baz danych działających w środowisku VMware jest teraz w wersji zapoznawczej. Aby wypróbować tę funkcję, użyj [**tego linku**](https://aka.ms/AzureMigrate/SQL) , aby utworzyć projekt w regionie **Australia Wschodnia** . Jeśli masz już projekt w Australii wschodniej i chcesz wypróbować tę funkcję, upewnij się, że zostały spełnione [**wymagania wstępne**](how-to-discover-sql-existing-project.md) w portalu.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Uruchom ocenę na podstawie konfiguracji serwera i danych wydajności.
> * Przegląd oceny usługi Azure SQL 

> [!NOTE]
> Samouczki przedstawiają najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych, tam gdzie to możliwe. 


## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

- Przed skorzystaniem z tego samouczka, aby ocenić wystąpienia SQL Server w celu przeprowadzenia migracji do usługi Azure SQL, upewnij się, że wykryto wystąpienia SQL, które chcesz ocenić przy użyciu urządzenia Azure Migrate, [postępuj zgodnie z tym samouczkiem](tutorial-discover-vmware.md)

## <a name="run-an-assessment"></a>Uruchamianie oceny
Uruchom ocenę w następujący sposób:
1. Na stronie **przegląd** > **Windows, Linux i SQL Server** kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Strona przeglądu Azure Migrate":::
2. Na **Azure Migrate: odnajdywanie i ocenianie** kliknij pozycję **Oceń** i wybierz typ oceny jako **Azure SQL**.
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="Lista rozwijana umożliwiająca wybranie typu oceny jako usługi Azure SQL":::
3. W obszarze **ocenianie serwerów** > będzie można zobaczyć typ oceny, który został wstępnie wybrany jako **usługa Azure SQL** i źródło odnajdowania domyślne dla **serwerów odnalezionych z urządzenia Azure Migrate**.

4. Kliknij przycisk **Edytuj** , aby przejrzeć właściwości oceny.
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="Przycisk edytowania, w którym można dostosować właściwości oceny":::
5. We właściwościach oceny > **właściwości elementu docelowego**:
    - W polu **Lokalizacja docelowa** Określ region platformy Azure, do którego chcesz przeprowadzić migrację. 
        - Konfiguracja usługi Azure SQL i zalecenia dotyczące kosztów są zależne od określonej lokalizacji. 
    - W **docelowym typie wdrożenia**
        - Wybierz pozycję **zalecane**, jeśli chcesz, aby Azure Migrate oceniać gotowość wystąpień SQL na potrzeby migracji do usługi Azure SQL i usługi Azure SQL DB oraz zalecać najlepiej dopasowaną docelową opcję wdrożenia, warstwę docelową, konfigurację SQL platformy Azure i szacunki miesięczne. [Więcej informacji](concepts-azure-sql-assessment-calculation.md)
        - Wybierz pozycję **Azure SQL DB**, jeśli chcesz ocenić gotowość wystąpień SQL do migracji do baz danych Azure SQL, a następnie przejrzyj warstwę docelową, konfigurację SQL platformy Azure i szacunki miesięczne.
        - Wybierz pozycję **Azure SQL mi**, jeśli chcesz ocenić gotowość wystąpień programu SQL do migracji do wystąpienia zarządzanego usługi Azure SQL, i przejrzyj warstwę docelową, konfigurację SQL platformy Azure i szacunki miesięczne.
    - W obszarze **zarezerwowana pojemność** Określ, czy program SQL Server ma być używany podczas migracji.
        - W przypadku wybrania opcji zarezerwowanej pojemności nie można określić "rabat (%)".

6. W obszarze właściwości oceny > **kryteria oceny**:
    - Kryteria ustalania rozmiaru są domyślnie **oparte na wydajności** , co oznacza, że usługa Azure migrowana będzie zbierać metryki wydajności odnoszące się do wystąpień SQL i baz danych zarządzanych przez nią, aby zalecać optymalny rozmiar Azure SQL Database i/lub konfigurację wystąpienia zarządzanego Azure SQL. Możesz określić następujące elementy:
        - **Historia wydajności** wskazująca czas trwania danych, na podstawie którego ma zostać obliczona Ocena. (Domyślnie jest to jeden dzień)
        - **Użycie percentyla**, aby wskazać wartość percentylu, która ma być używana dla przykładu wydajności. (Wartość domyślna to używany 95. percentyl)
    - W polu **czynnik komfortu** wskaż bufor, który ma być używany podczas oceny. Te konta dotyczące problemów, takich jak sezonowe użycie, krótka historia wydajności i prawdopodobnie wzrastają w przyszłości. Na przykład, jeśli jest używany współczynnik komfortu równy dwa: 
        
        **Składnik** | **Efektywne wykorzystanie** | **Dodaj współczynnik komfortu (2,0)**
        --- | --- | ---
        Rdzenie | 2  | 4
        Pamięć | 8 GB | 16 GB
   
7. W **cenniku**:
    - W programie **Oferta/Licencjonowanie** Określ ofertę platformy Azure, Jeśli zarejestrowano. Obecnie możesz wybrać tylko opcję płatność zgodnie z rzeczywistym użyciem i płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie. 
        - Dodatkowy rabat można uzyskać, korzystając z zarezerwowanej pojemności i Korzyść użycia hybrydowego platformy Azure w ramach oferty z płatność zgodnie z rzeczywistym użyciem. 
        - Możesz zastosować Korzyść użycia hybrydowego platformy Azure w oparciu o płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie. Ocena obecnie nie obsługuje zastosowania zarezerwowanej pojemności na podstawie oferty z opcją płatność zgodnie z rzeczywistym użyciem.
    - W obszarze **warstwa usług** wybierz najbardziej odpowiednią opcję warstwy usług, aby sprostać potrzebom biznesowym migracji do Azure SQL Database i/lub wystąpienia zarządzanego usługi Azure SQL: 
        - Wybierz pozycję **zalecane** , jeśli chcesz, aby Azure Migrate zalecać najlepszą dodaną warstwę usług dla serwerów. Może to być ogólne lub krytyczne dla działania firmy. Dowiedz się więcej
        - Wybierz pozycję **ogólnego przeznaczenia** , jeśli chcesz, aby konfiguracja usługi Azure SQL została zaprojektowana na potrzeby obciążeń zorientowanych na budżet.
        - Wybierz pozycję **krytyczne dla działania firmy** , jeśli chcesz, aby konfiguracja usługi Azure SQL została zaprojektowana dla obciążeń o małych opóźnieniach z wysoką odporność na awarie i szybkie przełączanie do trybu failover.
    - W polu **Rabat (%)** Dodaj wszelkie zniżki specyficzne dla subskrypcji otrzymane w oparciu o ofertę platformy Azure. Ustawienie domyślne to 0%.
    - W polu **Waluta** Wybierz walutę rozliczeń dla Twojego konta.
    - W polu **korzyść użycia hybrydowego platformy Azure** Określ, czy masz już licencję SQL Serverą. Jeśli to zrobisz, a zostaną one objęte aktywnym programem Software Assurance SQL Server subskrypcje, możesz zastosować Korzyść użycia hybrydowego platformy Azure po przeniesieniu licencji na platformę Azure.
    - Jeśli wprowadzisz zmiany, kliknij przycisk Zapisz.
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="Przycisk Zapisz na właściwościach oceny":::
8. W obszarze **ocenianie serwerów** > kliknij przycisk Dalej.
9.  Na stronie **Wybierz serwery do oceny**  >  **nazwy oceny** > Określ nazwę oceny.
10. W obszarze **Wybierz lub Utwórz grupę** > wybierz pozycję **Utwórz nową** i określ nazwę grupy.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="Przycisk nowej grupy":::
11. Wybierz urządzenie i wybierz serwery, które chcesz dodać do grupy. Następnie kliknij przycisk Dalej.
12. W obszarze **Przegląd + tworzenie oceny** Przejrzyj szczegóły oceny, a następnie kliknij pozycję Utwórz ocenę, aby utworzyć grupę i uruchomić ocenę.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="Lokalizacja przeglądu i przycisk Utwórz ocenę.":::
13. Po utworzeniu oceny przejdź do pozycji **Windows, Linux i SQL Server**  >  **Azure Migrate: kafelka odnajdywanie i Ocena** > kliknij liczbę obok pozycji Ocena usługi Azure SQL.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="Nawigacja do utworzonej oceny":::
15. Kliknij nazwę oceny, którą chcesz wyświetlić.

> [!NOTE]
> Ponieważ oceny usługi Azure SQL są oparte na wydajności, zalecamy odczekanie co najmniej pierwszego dnia po rozpoczęciu odnajdywania przed utworzeniem oceny. Zapewnia to czas na zbieranie danych o wydajności z wyższym zaufaniem. Jeśli odnajdywanie jest nadal w toku, gotowość wystąpień SQL zostanie oznaczona jako **nieznana**. Najlepiej po rozpoczęciu odnajdywania **poczekaj na określenie czasu trwania wydajności (dzień/tydzień/miesiąc)** , aby utworzyć lub ponownie obliczyć ocenę wysokiej pewności. 

## <a name="review-an-assessment"></a>Przegląd oceny

**Aby wyświetlić ocenę**:

1. **Windows, Linux i SQL Server**  >  **Azure Migrate: odnajdywanie i ocenianie** > kliknij liczbę obok funkcji oceny usługi Azure SQL.
2. Kliknij nazwę oceny, którą chcesz wyświetlić. Przykładowo (oszacowania i koszty tylko na przykład): :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="Przegląd oceny SQL":::
3. Przejrzyj podsumowanie oceny. Możesz również edytować właściwości oceny lub ponownie obliczyć ocenę.

#### <a name="discovered-items"></a>Odnalezione elementy

Wskazuje to liczbę serwerów SQL, wystąpień i baz danych, które zostały ocenione w tej ocenie.
    
#### <a name="azure-readiness"></a>Gotowość na platformę Azure

Wskazuje rozkład ocenianych wystąpień SQL: 
    
**Docelowy typ wdrożenia (we właściwościach oceny)** | **Gotowości**   
--- | --- |
**Zalecane** |  Gotowe do Azure SQL Database, gotowe do wystąpienia zarządzanego Azure SQL, potencjalnie gotowe dla maszyny wirtualnej platformy Azure, nieznane gotowość (w przypadku gdy trwa odnajdywanie lub wystąpiły problemy z odnajdywaniem)
**Usługa Azure SQL DB** lub **usługa Azure SQL mi** | Gotowe do Azure SQL Database lub wystąpienia zarządzanego Azure SQL, które nie jest gotowe do Azure SQL Database lub wystąpienia zarządzanego Azure SQL, gotowość nieznana (w przypadku gdy trwa odnajdywanie lub wystąpiły problemy z odnajdywaniem)
     
Możesz zapoznać się z szczegółowymi informacjami na temat problemów z migracją/ostrzeżeń, które można skorygować przed migracją do usługi Azure SQL. [Dowiedz się więcej](concepts-azure-sql-assessment-calculation.md) Możesz również przejrzeć zalecane konfiguracje usługi Azure SQL na potrzeby migracji do baz danych Azure SQL i/lub wystąpień zarządzanych.
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Szczegóły kosztu Azure SQL Database i wystąpienia zarządzanego

Szacowany koszt miesięczny obejmuje koszty obliczeń i magazynowania dla konfiguracji usługi Azure SQL odpowiadających zalecanemu typowi wdrożenia wystąpienia zarządzanego Azure SQL Database i/lub usługi Azure SQL. [Więcej informacji](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>Przegląd gotowości

1. Kliknij pozycję **gotowość SQL Azure**.
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Szczegóły gotowości usługi Azure SQL":::
1. W obszarze gotowość do usługi Azure SQL zapoznaj się z gotowością **usługi Azure SQL DB** i gotowość do oceny usługi Azure **SQL** :
    - **Gotowe**: wystąpienie jest gotowe do migracji do usługi Azure SQL DB/mi bez problemów lub ostrzeżeń dotyczących migracji. 
        - Gotowe (ikona informacji z linkiem i niebieską): wystąpienie jest gotowe do migracji do usługi Azure SQL DB/MI bez problemów z migracją, ale zawiera ostrzeżenia dotyczące migracji, które należy przejrzeć. Możesz kliknąć hiperlink, aby przejrzeć ostrzeżenia dotyczące migracji i zalecane wskazówki dotyczące korygowania: :::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="Ocena ze stanem gotowości":::       
    - **Niegotowe**: wystąpienie ma co najmniej jeden problem migracji do migracji do usługi Azure SQL DB/mi. Możesz kliknąć hiperlink i przejrzeć problemy z migracją oraz zalecane wskazówki dotyczące korygowania.
    - **Nieznany**: Azure Migrate nie może ocenić gotowości, ponieważ odnajdywanie jest w toku lub występują problemy podczas odnajdywania, które muszą zostać naprawione z bloku powiadomień. Jeśli problem będzie nadal występować, skontaktuj się z działem pomocy technicznej firmy Microsoft. 
1. Zapoznaj się z zalecanym typem wdrożenia dla wystąpienia serwera SQL, który jest określany na podstawie poniższej macierzy:

    - **Docelowy typ wdrożenia** (wybrany we właściwościach oceny): **zalecane**

        **Gotowość usługi Azure SQL DB** | **Gotowość do usługi Azure SQL** | **Zalecany typ wdrożenia** | **Obliczenia dotyczące konfiguracji i kosztów usługi Azure SQL.**
         --- | --- | --- | --- |
        Gotowy | Gotowy | Azure SQL DB lub Azure SQL MI [Dowiedz się więcej](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) | Tak
        Gotowy | Niegotowy lub nieznany | Azure SQL DB | Tak
        Niegotowy lub nieznany | Gotowy | Usługa Azure SQL MI | Tak
        Nie gotowy | Nie gotowy | Potencjalnie gotowa na maszynę wirtualną platformy Azure [Dowiedz się więcej](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | Nie
        Niegotowy lub nieznany | Niegotowy lub nieznany | Nieznane | Nie
    
    - **Docelowy typ wdrożenia** (wybrany we właściwościach oceny): **Azure SQL DB**
    
        **Gotowość usługi Azure SQL DB** | **Obliczenia dotyczące konfiguracji i kosztów usługi Azure SQL.**
        --- | --- |
        Gotowy | Tak
        Nie gotowy | Nie
        Nieznane | Nie
    
    - **Docelowy typ wdrożenia** (wybrany we właściwościach oceny): **Azure SQL mi**
    
        **Gotowość do usługi Azure SQL** | **Obliczenia dotyczące konfiguracji i kosztów usługi Azure SQL.**
         --- | --- |
        Gotowy | Tak
        Nie gotowy | Nie
        Nieznane | Nie

4. Kliknij nazwę wystąpienia przechodzenie do szczegółów, aby zobaczyć liczbę baz danych użytkowników, szczegóły wystąpienia, w tym właściwości wystąpienia, obliczenia (zakres do wystąpienia) i szczegóły magazynu źródłowej bazy danych.
5. Kliknij liczbę baz danych użytkowników, aby przejrzeć listę baz danych i ich szczegóły. Przykładowo (oszacowania i koszty tylko na przykład): :::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="szczegóły wystąpienia SQL":::
5. Kliknij pozycję Przejrzyj szczegóły w kolumnie problemy dotyczące migracji, aby przejrzeć problemy dotyczące migracji i ostrzeżenia dotyczące określonego typu wdrożenia docelowego. 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="Problemy i ostrzeżenia dotyczące migracji bazy danych":::

### <a name="review-cost-estimates"></a>Przegląd szacowanych kosztów
Podsumowanie oceny przedstawia szacowane miesięczne koszty obliczeniowe i magazynowe dla konfiguracji usługi Azure SQL odpowiadające zalecanym typem wdrożenia baz danych SQL Azure i/lub zarządzanych wystąpień.

1. Zapoznaj się z miesięczną sumą kosztów. Koszty są agregowane dla wszystkich wystąpień SQL w ocenianej grupie.
    - Oszacowania kosztów są oparte na zalecanej konfiguracji usługi Azure SQL dla danego wystąpienia. 
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu. Przykładowo (oszacowania i koszty tylko na przykład):
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="Szczegóły kosztu":::

1. Możesz przejść do szczegółów na poziomie wystąpienia, aby zobaczyć konfigurację i oszacowania kosztów usługi Azure SQL na poziomie wystąpienia.  
1. Możesz również przejść do listy baz danych, aby przejrzeć konfigurację usługi Azure SQL i szacunki kosztów dla bazy danych, gdy jest zalecana konfiguracja Azure SQL Database.

### <a name="review-confidence-rating"></a>Przegląd oceny zaufania
Azure Migrate przypisuje ocenę zaufania do wszystkich ocen usługi Azure SQL na podstawie dostępności punktów danych wydajności/wykorzystania potrzebnych do obliczenia oceny dla wszystkich ocenionych wystąpień i baz danych SQL. Klasyfikacja jest z jednej gwiazdki (najniższej) do pięciu gwiazdek (najwyższa).
Ocena zaufania pomaga oszacować niezawodność zaleceń dotyczących rozmiaru w ocenie. Klasyfikacje zaufania są następujące:

**Dostępność punktu danych** | **Ocena zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek

[Dowiedz się więcej](concepts-azure-sql-assessment-calculation.md#confidence-ratings) na temat klasyfikacji zaufania.


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej](concepts-azure-sql-assessment-calculation.md) na temat obliczania ocen usługi Azure SQL.
- Rozpocznij Migrowanie wystąpień i baz danych SQL przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
