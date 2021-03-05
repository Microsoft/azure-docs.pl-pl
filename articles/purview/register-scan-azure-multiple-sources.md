---
title: Jak skanować wiele źródeł na platformie Azure
description: Dowiedz się, jak skanować całą subskrypcję lub grupę zasobów platformy Azure w usłudze Azure kontrolą Data Catalog.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: 098f62365971fd634001706ab99fd414a6b25056
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123568"
---
# <a name="register-and-scan-azure-multiple-sources"></a>Rejestrowanie i skanowanie wielu źródeł na platformie Azure

W tym artykule opisano sposób rejestrowania wielu źródeł platformy Azure (subskrypcji platformy Azure lub grup zasobów) w programie kontrolą i konfigurowania na niej skanowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Usługa Azure Multiple Source obsługuje skanowanie w celu przechwytywania metadanych i schematów na większości typów zasobów platformy Azure obsługiwanych przez kontrolą. Automatycznie klasyfikuje dane w oparciu o system i niestandardowe reguły klasyfikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed zarejestrowaniem źródeł danych Utwórz konto usługi Azure kontrolą. Aby uzyskać więcej informacji na temat tworzenia konta kontrolą, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).
- Musisz być administratorem źródła danych usługi Azure kontrolą
- Konfigurowanie uwierzytelniania zgodnie z opisem w poniższych sekcjach

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>Konfigurowanie uwierzytelniania do wyliczania zasobów w ramach subskrypcji lub grupy zasobów

1. Przejdź do subskrypcji lub grupy zasobów w Azure Portal.  
1. Wybierz pozycję **Access Control (IAM)**   z menu nawigacji po lewej stronie 
1. Aby dodać rolę do subskrypcji lub grupy zasobów, musisz być administratorem dostępu właściciela lub użytkownika. Wybierz przycisk *+ Dodaj* . 
1. Ustaw rolę **czytelnika** i wprowadź nazwę konta usługi Azure kontrolą (która reprezentuje jego plik msi) w obszarze Wybieranie pola wejściowego. Kliknij przycisk *Zapisz* , aby zakończyć przypisanie roli.

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>Konfigurowanie uwierzytelniania do skanowania zasobów w ramach subskrypcji lub grupy zasobów

Istnieją dwa sposoby konfigurowania uwierzytelniania dla wielu źródeł na platformie Azure:

- Tożsamość zarządzana
- Jednostka usługi

> [!NOTE]
> Należy skonfigurować uwierzytelnianie dla każdego zasobu w ramach subskrypcji lub grupy zasobów, które mają zostać zarejestrowane i przeskanowane. Typy zasobów usługi Azure Storage (Azure Blob Storage i Azure Data Lake Storage Gen2) ułatwiają dodawanie MSI lub nazwy głównej usługi na poziomie subskrypcji/grupy zasobów jako czytnika danych obiektów blob magazynu. Następnie uprawnienia Trickle do każdego konta magazynu w ramach tej subskrypcji lub grupy zasobów. W przypadku wszystkich innych typów zasobów należy zastosować plik MSI lub nazwę główną usługi dla każdego zasobu lub urządzenie, aby to zrobić. Poniżej przedstawiono sposób dodawania uprawnień do każdego typu zasobu w ramach subskrypcji lub grupy zasobów.
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Usługa Azure Data Lake Storage 1. generacji](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database wystąpienie zarządzane](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-an-azure-multiple-source"></a>Rejestrowanie wielu źródeł na platformie Azure

Aby zarejestrować nowe wiele źródeł platformy Azure w usłudze Data Catalog, wykonaj następujące czynności:

1. Przejdź do konta kontrolą
1. Wybierz **źródła** na lewym pasku nawigacyjnym
1. Wybierz pozycję **zarejestruj**
1. W obszarze **Rejestruj źródła** wybierz pozycję **Azure (wiele)**
1. Wybierz przycisk **Kontynuuj**

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Rejestrowanie wielu źródeł na platformie Azure":::

Na ekranie **Rejestr sources (Azure Multiple)** wykonaj następujące czynności:

1. Wprowadź **nazwę** , z którą zostanie wyświetlone źródło danych w wykazie 
1. Opcjonalnie wybierz **grupę zarządzania** , do której chcesz odfiltrować
1. **Wybierz subskrypcję lub określoną grupę zasobów** w ramach danej subskrypcji na liście rozwijanej. Zakres rejestracji zostanie ustawiony na wybraną subskrypcję lub grupę zasobów.  
1. Wybierz **kolekcję** lub Utwórz nową (opcjonalnie)
1. **Zakończ** , aby zarejestrować źródło danych

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Konfigurowanie wielu źródeł na platformie Azure":::

## <a name="creating-and-running-a-scan"></a>Tworzenie i Uruchamianie skanowania

Aby utworzyć i uruchomić nowe skanowanie, wykonaj następujące czynności:

1. Przejdź do sekcji **źródeł**

1. Wybierz zarejestrowane źródło danych

1. Kliknij pozycję Wyświetl szczegóły i wybierz pozycję **+ Nowe skanowanie** lub użyj ikony Skanuj szybką akcję na kafelku źródłowym

1. Wypełnij pola *Nazwa* i wybierz wszystkie typy zasobów, które mają zostać przeskanowane w ramach tego źródła

    1. Można to pozostawić jako *wszystko* (obejmuje to przyszłe typy zasobów, które mogą nie istnieć w ramach tej subskrypcji lub grupy zasobów).
    1. Można **wybrać typy zasobów** , które mają zostać przeskanowane. W przypadku wybrania tej opcji przyszłe typy zasobów, które mogą zostać utworzone w ramach tej subskrypcji lub grupy zasobów, nie będą uwzględniane do skanowania, chyba że skanowanie zostanie jawnie edytowane w przyszłości
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Skanowanie z wieloma źródłami na platformie Azure":::

1. Wybierz poświadczenie, aby połączyć się z zasobami w ramach źródła danych. 
    1. Możesz wybrać **poświadczenie na poziomie nadrzędnym** jako plik MSI lub konkretne poświadczenie typu podmiotu usługi, którego możesz użyć dla wszystkich typów zasobów w ramach subskrypcji lub grupy zasobów
    1. Można również **wybrać typ zasobu i zastosować inne poświadczenie** dla tego typu zasobu.
    1. Każde poświadczenie będzie uznawane za metodę uwierzytelniania dla wszystkich zasobów w ramach określonego typu
    1. Należy ustawić wybrane poświadczenia dla zasobów, aby pomyślnie je przeskanować zgodnie z opisem w tej [sekcji](#setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group) .
1. W każdym typie można wybrać opcję skanowania wszystkich zasobów lub ich podzbioru według nazwy.
    1. Jeśli opuścisz opcję, a **wszystkie** przyszłe zasoby tego typu zostaną również przeskanowane w przyszłych przebiegach skanowania
    1. W przypadku wybrania określonych kont magazynu lub baz danych SQL, przyszłe zasoby utworzone w ramach tej subskrypcji lub grupy zasobów nie będą uwzględniane do skanowania, chyba że skanowanie zostanie jawnie edytowane w przyszłości
 
1.  Kliknij przycisk **Kontynuuj** Aby kontynuować. Będziemy testować dostęp, aby sprawdzić, czy program kontrolą MSI został zastosowany jako czytelnik w ramach subskrypcji lub grupy zasobów. Jeśli zostanie zgłoszony komunikat o błędzie, wykonaj instrukcje [tutaj](#setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)

1.  Wybierz opcję **Skanuj zestawy reguł** dla każdego typu zasobu wybranego w poprzednim kroku. Możesz również utworzyć wbudowane zestawy reguł skanowania.
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Wybór zestawu reguł skanowania wielokrotnego platformy Azure":::

1. Wybierz wyzwalacz skanowania. Można zaplanować uruchamianie go **co tydzień/miesięcznie** lub **raz**

1. Przejrzyj skanowanie i wybierz pozycję Zapisz, aby ukończyć konfigurację   

## <a name="viewing-your-scans-and-scan-runs"></a>Wyświetlanie skanów i uruchomień skanowania

1. Wyświetl szczegóły źródła, klikając pozycję **Wyświetl szczegóły** na kafelku w sekcji źródła. 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Szczegóły dotyczące wielu źródeł platformy Azure"::: 

1. Wyświetl szczegóły uruchomienia skanowania, przechodząc do strony **szczegółów skanowania** .
    1. *Pasek stanu* to krótkie podsumowanie stanu działania zasobów podrzędnych. Zostanie ona wyświetlona na poziomie subskrypcji lub grupy zasobów
    1. Zielony oznacza powodzenie, a czerwony oznacza, że nie powiodło się. Szare oznacza, że skanowanie jest nadal w toku
    1. Możesz kliknąć poszczególne skanowania, aby wyświetlić bardziej szczegółowe szczegóły

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Szczegóły dotyczące wielu skanów platformy Azure":::

1. Wyświetl podsumowanie ostatnich nieudanych uruchomień skanowania w dolnej części strony szczegółów źródła. Możesz również kliknąć pozycję aby wyświetlić bardziej szczegółowe szczegóły dotyczące tych przebiegów.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Zarządzanie skanowaniem — Edytuj, Usuń lub Anuluj
Aby zarządzać skanowaniem lub usunąć je, wykonaj następujące czynności:

- Przejdź do centrum zarządzania. Wybierz pozycję źródła danych w sekcji źródła i skanowanie, a następnie wybierz odpowiednie źródło danych.

- Wybierz skanowanie, którymi chcesz zarządzać. Skanowanie można edytować, wybierając pozycję Edytuj.

- Możesz usunąć skanowanie, wybierając pozycję Usuń.
- Jeśli skanowanie jest uruchomione, można je anulować również

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)    
