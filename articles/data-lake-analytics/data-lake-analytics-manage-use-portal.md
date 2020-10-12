---
title: Zarządzanie Azure Data Lake Analytics przy użyciu Azure Portal
description: W tym artykule opisano, jak używać Azure Portal do zarządzania kontami Data Lake Analytics, źródłami danych, użytkownikami i zadaniami &.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: cbce903bc7463cd917d48e341614afbe18536262
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87531216"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Manage Azure Data Lake Analytics using the Azure portal (Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal)
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

W tym artykule opisano sposób zarządzania kontami Azure Data Lake Analytics, źródłami danych, użytkownikami i zadaniami przy użyciu Azure Portal.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Zarządzanie kontami usługi Data Lake Analytics

### <a name="create-an-account"></a>Tworzenie konta

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij kolejno pozycje **Utwórz zasób**  >  **Analiza i analiza**  >  **Data Lake Analytics**.
3. Wybierz wartości dla następujących elementów: 
   1. **Nazwa**: nazwa konta Data Lake Analytics.
   2. **Subskrypcja**: subskrypcja platformy Azure użyta dla tego konta.
   3. **Grupa zasobów**: Grupa zasobów platformy Azure, w której ma zostać utworzone konto. 
   4. **Lokalizacja**: centrum danych platformy Azure dla konta Data Lake Analytics. 
   5. **Data Lake Store**: magazyn domyślny, który ma być używany dla konta Data Lake Analytics. Konto Azure Data Lake Store i konto Data Lake Analytics muszą znajdować się w tej samej lokalizacji.
4. Kliknij przycisk **Utwórz**. 

### <a name="delete-a-data-lake-analytics-account"></a>Usuwanie konta Data Lake Analytics

Przed usunięciem konta Data Lake Analytics usuń jego domyślne konto Data Lake Store.

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.
2. Kliknij polecenie **Usuń**.
3. Wpisz nazwę konta.
4. Kliknij polecenie **Usuń**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Zarządzanie źródłami danych

Data Lake Analytics obsługuje następujące źródła danych:

* Data Lake Store
* Azure Storage

Za pomocą Eksplorator danych można przeglądać źródła danych i wykonywać podstawowe operacje zarządzania plikami. 

### <a name="add-a-data-source"></a>Dodawanie źródła danych

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.
2. Kliknij pozycję **źródła danych**.
3. Kliknij pozycję **Dodaj źródło danych**.
    
   * Aby dodać konto Data Lake Store, potrzebna jest nazwa konta i dostęp do konta, aby można było wysłać do niego zapytanie.
   * Aby dodać usługę Azure Blob Storage, musisz mieć konto magazynu i klucz konta. Aby je znaleźć, przejdź do konta magazynu w portalu.

## <a name="set-up-firewall-rules"></a>Konfigurowanie reguł zapory

Możesz użyć Data Lake Analytics, aby zapewnić dalsze blokowanie dostępu do konta Data Lake Analytics na poziomie sieci. Możesz włączyć zaporę, określić adres IP lub zdefiniować zakres adresów IP dla zaufanych klientów. Po włączeniu tych miar tylko klienci, którzy mają adresy IP w określonym zakresie, mogą łączyć się ze sklepem.

Jeśli inne usługi platformy Azure, takie jak Azure Data Factory lub maszyny wirtualne, łączą się z kontem Data Lake Analytics, upewnij się, że **On**włączono opcję Zezwalaj na korzystanie z **usług platformy Azure** . 

### <a name="set-up-a-firewall-rule"></a>Konfigurowanie reguły zapory

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.
2. W menu po lewej stronie kliknij pozycję **Zapora**.

## <a name="add-a-new-user"></a>Dodawanie nowego użytkownika

Za pomocą **Kreatora dodawania użytkownika** można łatwo inicjować obsługę nowych użytkowników Data Lake.

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.
2. Po lewej stronie w obszarze **wprowadzenie**kliknij pozycję **Kreator dodawania użytkownika**.
3. Wybierz użytkownika, a następnie kliknij przycisk **Wybierz**.
4. Wybierz rolę, a następnie kliknij przycisk **Wybierz**. Aby skonfigurować nowego dewelopera do korzystania z Azure Data Lake, wybierz **Data Lake Analytics rolę dewelopera** .
5. Wybierz listy kontroli dostępu (ACL) dla baz danych U-SQL. Po spełnieniu wybranych opcji kliknij pozycję **Wybierz**.
6. Wybierz listy ACL dla plików. W przypadku magazynu domyślnego nie należy zmieniać list ACL dla folderu głównego "/" i folderu/odzyskiwanie. Kliknij pozycję **Wybierz**.
7. Przejrzyj wszystkie wybrane zmiany, a następnie kliknij przycisk **Uruchom**.
8. Po zakończeniu pracy kreatora kliknij przycisk **gotowe**.

## <a name="manage-role-based-access-control"></a>Zarządzanie Access Control Role-Based

Podobnie jak w przypadku innych usług platformy Azure, można użyć Access Control Role-Based (RBAC) do kontrolowania sposobu, w jaki użytkownicy pracują z usługą.

Standardowe role platformy Azure mają następujące możliwości:
* **Właściciel**: może przesyłać zadania, monitorować zadania, anulować zadania z dowolnego użytkownika i konfigurować je.
* **Współautor**: można przesyłać zadania, monitorować zadania, anulować zadania z dowolnego użytkownika i konfigurować je.
* **Czytelnik**: może monitorować zadania.

Użyj roli deweloper Data Lake Analytics, aby umożliwić deweloperom U-SQL korzystanie z usługi Data Lake Analytics. Roli deweloper Data Lake Analytics można użyć do:
* Przesyłanie zadań.
* Monitoruj stan zadania i postęp zadań przesłanych przez dowolnego użytkownika.
* Zobacz skrypty U-SQL z zadań przesłanych przez dowolnego użytkownika.
* Anuluj tylko własne zadania.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Dodawanie użytkowników lub grup zabezpieczeń do konta Data Lake Analytics

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.
2. Kliknij pozycję **Kontrola dostępu (IAM)**  >  **Dodaj przypisanie roli**.
3. Wybierz rolę.
4. Dodaj użytkownika.
5. Kliknij przycisk **OK**.

>[!NOTE]
>Jeśli użytkownik lub grupa zabezpieczeń musi przesłać zadania, musi również mieć uprawnienia do konta magazynu. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych przechowywanych w Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Zarządzanie zadaniami

### <a name="submit-a-job"></a>Przesyłanie zadania

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.

2. Kliknij pozycję **nowe zadanie**. Dla każdego zadania skonfiguruj następujące polecenie:

    1. **Nazwa zadania**: Nazwa zadania.
    2. **Priorytet**: niższe numery mają wyższy priorytet. Jeśli dwa zadania są umieszczane w kolejce, najpierw zostanie uruchomiona wartość o niższym priorytecie.
    3. **Równoległość**: Maksymalna liczba procesów obliczeniowych do zarezerwowania dla tego zadania.

3. Kliknij przycisk **Prześlij zadanie**.

### <a name="monitor-jobs"></a>Monitorowanie zadań

1. W Azure Portal przejdź do swojego konta Data Lake Analytics.
2. Kliknij pozycję **Wyświetl wszystkie zadania**. Zostanie wyświetlona lista wszystkich aktywnych i ostatnio zakończonych zadań w ramach konta.
3. Opcjonalnie kliknij przycisk **Filtruj** , aby pomóc znaleźć zadania według **zakresu czasu**, **nazwy zadania**i wartości **autora** . 

### <a name="monitoring-pipeline-jobs"></a>Monitorowanie zadań potoku
Zadania, które są częścią potoku, działają razem, zwykle sekwencyjnie, w celu wykonania konkretnego scenariusza. Można na przykład utworzyć potok, który czyści, wyodrębnia, przekształca, agreguje użycie dla szczegółowych informacji o klientach. Zadania potoku są identyfikowane przy użyciu właściwości "potok", gdy zadanie zostało przesłane. Dla zadań zaplanowanych za pomocą usługi ADF w wersji 2 automatycznie zostanie wypełniona ta właściwość. 

Aby wyświetlić listę zadań U-SQL, które są częścią potoków: 

1. W Azure Portal przejdź do konta Data Lake Analytics.
2. Kliknij pozycję **szczegółowe informacje o zadaniu**. Karta "wszystkie zadania" zostanie ustawiona domyślnie, pokazując listę uruchomionych zadań, umieszczonych w kolejce i zakończonych.
3. Kliknij kartę **zadania potoku** . Zostanie wyświetlona lista zadań potoku wraz ze zagregowanymi statystykami dla każdego potoku.

### <a name="monitoring-recurring-jobs"></a>Monitorowanie zadań cyklicznych
Zadanie cykliczne to takie, które ma tę samą logikę biznesową, ale używa różnych danych wejściowych przy każdym uruchomieniu. W idealnym przypadku zadania cykliczne powinny zawsze być pomyślne i mieć stosunkowo stabilny czas wykonania. monitorowanie tych zachowań pomaga upewnić się, że zadanie jest w dobrej kondycji. Zadania cykliczne są identyfikowane przy użyciu właściwości "cykl". Dla zadań zaplanowanych za pomocą usługi ADF w wersji 2 automatycznie zostanie wypełniona ta właściwość.

Aby wyświetlić listę zadań U-SQL, które są cykliczne: 

1. W Azure Portal przejdź do konta Data Lake Analytics.
2. Kliknij pozycję **szczegółowe informacje o zadaniu**. Karta "wszystkie zadania" zostanie ustawiona domyślnie, pokazując listę uruchomionych zadań, umieszczonych w kolejce i zakończonych.
3. Kliknij kartę **zadania cykliczne** . Zostanie wyświetlona lista zadań cyklicznych wraz ze zagregowanymi statystykami dla każdego zadania cyklicznego.

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Zarządzanie Azure Data Lake Analytics przy użyciu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu zasad](data-lake-analytics-account-policies.md)
