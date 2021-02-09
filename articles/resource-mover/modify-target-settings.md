---
title: Modyfikowanie ustawień docelowych podczas przenoszenia maszyn wirtualnych platformy Azure między regionami przy użyciu usługi Azure Resource przenoszącej
description: Dowiedz się, jak modyfikować ustawienia docelowe podczas przenoszenia maszyn wirtualnych platformy Azure między regionami przy użyciu usługi Azure Resource przenoszącej.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: eb28e4c8f6b465e2a9b38cc4571bc4a00baf4ef7
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979627"
---
# <a name="modify-target-settings"></a>Modyfikowanie ustawień elementu docelowego

W tym artykule opisano sposób modyfikowania ustawień docelowych podczas przenoszenia zasobów między regionami platformy Azure za pomocą [usługi Azure Resource Recovery](overview.md).


## <a name="modify-vm-settings"></a>Modyfikowanie ustawień maszyny wirtualnej

Podczas przechodzenia do maszyn wirtualnych platformy Azure i skojarzonych zasobów można modyfikować ustawienia docelowe. 

- Zalecamy zmianę ustawień docelowych tylko po sprawdzeniu poprawności kolekcji przenoszenia.
- Zalecamy modyfikację ustawień przed przygotowaniem zasobów, ponieważ niektóre właściwości docelowe mogą być niedostępne do edycji po zakończeniu przygotowywania.

Ale
- W przypadku przenoszenia zasobu źródłowego można zwykle modyfikować ustawienia docelowe do momentu rozpoczęcia procesu inicjowania przenoszenia.
- Jeśli w regionie źródłowym zostanie przypisany istniejący zasób, można modyfikować ustawienia docelowe do momentu ukończenia zatwierdzania przeniesienia.

### <a name="settings-you-can-modify"></a>Ustawienia, które można modyfikować

Ustawienia konfiguracji, które można modyfikować, są zestawione w tabeli.

**Zasób** | **Opcje** 
--- | --- | --- 
**Nazwa maszyny wirtualnej** | Opcje:<br/><br/> -Utwórz nową maszynę wirtualną o tej samej nazwie w regionie docelowym.<br/><br/> -Utwórz nową maszynę wirtualną z inną nazwą w regionie docelowym.<br/><br/> — Użyj istniejącej maszyny wirtualnej w regionie docelowym.<br/><br/> Jeśli tworzysz nową maszynę wirtualną, z wyjątkiem modyfikowanych ustawień, Nowa docelowa maszyna wirtualna ma przypisane te same ustawienia co źródło.
**Strefa dostępności maszyny wirtualnej** | Strefa dostępności, w której zostanie umieszczona docelowa maszyna wirtualna. Wybierz pozycję **nie dotyczy** , jeśli nie chcesz zmieniać ustawień źródłowych lub jeśli nie chcesz umieszczać maszyny wirtualnej w strefie dostępności.
**Jednostka SKU maszyny wirtualnej** | [Typ maszyny wirtualnej](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (dostępny w regionie docelowym), który będzie używany dla docelowej maszyny wirtualnej.<br/><br/> Wybrana docelowa maszyna wirtualna nie powinna być mniejsza niż źródłowa maszyna wirtualna.
* * Zestaw dostępności maszyny wirtualnej | Zestaw dostępności, w którym zostanie umieszczona docelowa maszyna wirtualna. Wybierz pozycję **nie dotyczy**  , nie chcę zmieniać ustawień źródłowych lub jeśli nie chcesz umieszczać maszyny wirtualnej w zestawie dostępności.
**Magazyn kluczy maszyny wirtualnej** | Skojarzony Magazyn kluczy po włączeniu usługi Azure Disk Encryption na maszynie wirtualnej.
**Zestaw szyfrowania dysków** | Powiązane z szyfrowaniem dysków, jeśli maszyna wirtualna używa klucza zarządzanego przez klienta do szyfrowania po stronie serwera.
**Grupa zasobów** | Grupa zasobów, w której zostanie umieszczona docelowa maszyna wirtualna.
**Zasoby sieciowe** | Opcje dotyczące interfejsów sieciowych, sieci wirtualnych (sieci wirtualnych/) i sieciowych grup zabezpieczeń/interfejsów sieciowych:<br/><br/> — Utwórz nowy zasób o tej samej nazwie w regionie docelowym.<br/><br/> — Utwórz nowy zasób o innej nazwie w regionie docelowym.<br/><br/> — Użyj istniejącego zasobu sieciowego w regionie docelowym.<br/><br/> W przypadku utworzenia nowego zasobu docelowego, z wyjątkiem modyfikowanych ustawień, przypisane są te same ustawienia co zasób źródłowy.
**Nazwa publicznego adresu IP, jednostka SKU i strefa** | Określa nazwę, [jednostkę SKU](../virtual-network/public-ip-addresses.md#sku)i [strefę](../virtual-network/public-ip-addresses.md#standard) dla standardowych publicznych adresów IP.<br/><br/> Jeśli chcesz, aby ta strefa była nadmiarowa, wprowadź ją jako **nadmiarową strefę**.
* * Nazwa usługi równoważenia obciążenia, jednostka SKU i strefa * * | Określa nazwę, jednostkę SKU (podstawowa lub standardowa) i strefę modułu równoważenia obciążenia.<br/><br/> Zalecamy użycie standardowej jednostki sKU.<br/><br/> Jeśli chcesz, aby strefa była nadmiarowa, określ jako **strefę nadmiarową**.
**Zależności zasobów** | Opcje dla każdej zależności:<br/><br/>-Zasób używa źródłowych zasobów zależnych, które zostaną przeniesione do regionu docelowego.<br/><br/> -Zasób używa różnych zasobów zależnych znajdujących się w regionie docelowym. W takim przypadku można wybrać spośród podobnych zasobów w regionie docelowym.

### <a name="edit-vm-target-settings"></a>Edytuj ustawienia elementu docelowego maszyny wirtualnej

Jeśli nie chcesz używać zasobów zależnych od regionu źródłowego do obiektu docelowego, masz kilka innych opcji:

- Utwórz nowy zasób w regionie docelowym. Jeśli nie określisz innych ustawień, nowy zasób będzie miał te same ustawienia co zasób źródłowy.
- Użyj istniejącego zasobu w regionie docelowym.

Dokładne zachowanie zależy od typu zasobu. [Dowiedz się więcej](modify-target-settings.md) o modyfikowaniu ustawień docelowych.

Ustawienia docelowe dla zasobu należy zmodyfikować przy użyciu wpisu **Konfiguracja docelowa** w kolekcji przenoszenie zasobów. 

Aby zmodyfikować ustawienie: 

1. Na stronie **między regionami** > kolumny **Konfiguracja docelowa** kliknij link do pozycji zasób.
2. W obszarze **Ustawienia konfiguracji** można utworzyć nową maszynę wirtualną w regionie docelowym.
3. Przypisz nową strefę dostępności, zestaw dostępności lub jednostkę SKU do docelowej maszyny wirtualnej. **Strefa dostępności** i **jednostka SKU**.

Zmiany są wykonywane tylko dla edytowanego zasobu. Należy osobno zaktualizować każdy zależny zasób.


## <a name="modify-sql-settings"></a>Modyfikowanie ustawień SQL

Podczas przenoszenia Azure SQL Database zasobów można modyfikować ustawienia docelowe przenoszenia. 

- W przypadku usługi SQL Database:
    - Przed przystąpieniem do przenoszenia zaleca się zmodyfikowanie ustawień konfiguracji docelowej.
    - Istnieje możliwość zmodyfikowania ustawień docelowej bazy danych i nadmiarowości strefy dla bazy danych.
- W przypadku pul elastycznych:
    -  Konfigurację docelową można zmodyfikować w dowolnym momencie przed zainicjowaniem przenoszenia.
    - Można zmodyfikować docelową pulę elastyczną i nadmiarowość strefy dla puli. 

### <a name="sql-settings-you-can-modify"></a>Ustawienia SQL, które można modyfikować

**Ustawienie** | **Baza danych SQL** | **Pula elastyczna**
--- | --- | ---
**Nazwa** | Utwórz nową bazę danych o tej samej nazwie w regionie docelowym.<br/><br/> Utwórz nową bazę danych o innej nazwie w regionie docelowym.<br/><br/> Użyj istniejącej bazy danych w regionie docelowym. | Utwórz nową pulę elastyczną o tej samej nazwie w regionie docelowym.<br/><br/> Utwórz nową pulę elastyczną z inną nazwą w regionie docelowym.<br/><br/> Użyj istniejącej puli elastycznej w regionie docelowym.
**Nadmiarowość stref** | Aby przenieść z regionu, który obsługuje nadmiarowość strefy, do regionu, który nie, wpisz **disable** w ustawieniach strefy.<br/><br/> Aby przenieść z regionu, który nie obsługuje nadmiarowości strefy, do regionu, który to robi, wpisz **enable** w ustawieniach strefy. | Aby przenieść z regionu, który obsługuje nadmiarowość strefy, do regionu, który nie, wpisz **disable** w ustawieniach strefy.<br/><br/> Aby przenieść z regionu, który nie obsługuje nadmiarowości strefy, do regionu, który to robi, wpisz **enable** w ustawieniach strefy.

### <a name="edit-sql-target-settings"></a>Edytuj ustawienia elementu docelowego SQL

Ustawienia docelowe dla zasobu Azure SQL Database są modyfikowane w następujący sposób: 

1. W obszarze **między regionami** dla zasobu, który chcesz zmodyfikować, kliknij wpis **Konfiguracja docelowa** .
2. W obszarze **Ustawienia konfiguracji** Określ ustawienia docelowe zestawione w powyższej tabeli.

## <a name="next-steps"></a>Następne kroki

[Przenieś maszynę wirtualną platformy Azure](tutorial-move-region-virtual-machines.md) do innego regionu.