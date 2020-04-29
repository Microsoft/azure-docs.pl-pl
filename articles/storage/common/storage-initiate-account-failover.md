---
title: Inicjowanie trybu failover konta magazynu (wersja zapoznawcza) — Azure Storage
description: Dowiedz się, jak zainicjować pracę w trybie failover w przypadku, gdy podstawowy punkt końcowy konta magazynu stał się niedostępny. Tryb failover aktualizuje region pomocniczy, aby stał się regionem podstawowym konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0c619224201d6225d5e5c127b342f71f2f7fced9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79535356"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Inicjowanie trybu failover konta magazynu (wersja zapoznawcza)

Jeśli podstawowy punkt końcowy dla konta magazynu geograficznie nadmiarowego stanie się niedostępny z dowolnego powodu, możesz zainicjować tryb failover konta (wersja zapoznawcza). Tryb failover konta aktualizuje pomocniczy punkt końcowy, aby stał się podstawowym punktem końcowym dla konta magazynu. Po zakończeniu pracy w trybie failover klienci mogą zacząć zapisywać w nowym regionie podstawowym. Wymuszone przejście w tryb failover pozwala zachować wysoką dostępność aplikacji.

W tym artykule pokazano, jak zainicjować tryb failover konta dla konta magazynu za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej o pracy awaryjnej konta, zobacz [odzyskiwanie po awarii i tryb failover konta (wersja zapoznawcza) w usłudze Azure Storage](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Przejście w tryb failover na koncie zazwyczaj skutkuje utratą danych. Aby zrozumieć konsekwencje przejścia do trybu failover dla konta i przygotować się do utraty danych, zapoznaj [się z tematem proces przełączania do trybu failover dla konta](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed przełączeniem w tryb failover na koncie magazynu upewnij się, że wykonano następujące czynności:

- Upewnij się, że konto magazynu jest skonfigurowane do korzystania z magazynu geograficznie nadmiarowego (GRS) lub magazynu geograficznie nadmiarowego do odczytu (RA-GRS). Aby uzyskać więcej informacji na temat magazynu geograficznie nadmiarowego, zobacz [nadmiarowość usługi Azure Storage](storage-redundancy.md).

## <a name="important-implications-of-account-failover"></a>Ważne konsekwencje przełączenia w tryb failover

Po zainicjowaniu konta magazynu w trybie failover rekordy DNS dla pomocniczego punktu końcowego są aktualizowane, tak aby pomocniczy punkt końcowy stał się podstawowym punktem końcowym. Przed zainicjowaniem trybu failover upewnij się, że rozumiesz potencjalny wpływ na konto magazynu.

Aby oszacować zakres prawdopodobnej utraty danych przed zainicjowaniem trybu failover, należy sprawdzić Właściwość **godzina ostatniej synchronizacji** przy użyciu `Get-AzStorageAccount` polecenia cmdlet programu PowerShell i uwzględnić `-IncludeGeoReplicationStats` parametr. Następnie sprawdź `GeoReplicationStats` Właściwość konta. \

Po przejściu w tryb failover typ konta magazynu zostanie automatycznie przekonwertowany na magazyn lokalnie nadmiarowy (LRS) w nowym regionie podstawowym. Można ponownie włączyć magazyn Geograficznie nadmiarowy (GRS) lub magazyn Geograficznie nadmiarowy do odczytu (RA-GRS) dla konta. Należy pamiętać, że konwersja z LRS na GRS lub RA-GRS wiąże się z dodatkowymi kosztami. Aby uzyskać dodatkowe informacje, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/)dotyczącego przepustowości.

Po ponownym włączeniu GRS dla konta magazynu firma Microsoft rozpocznie replikowanie danych z konta do nowego regionu pomocniczego. Czas replikacji zależy od ilości replikowanych danych.  

## <a name="portal"></a>[Portal](#tab/azure-portal)

Aby zainicjować tryb failover konta z Azure Portal, wykonaj następujące kroki:

1. Przejdź do swojego konta magazynu.
2. W obszarze **Ustawienia**wybierz pozycję **replikacja geograficzna**. Na poniższej ilustracji przedstawiono stan replikacji geograficznej i trybu failover dla konta magazynu.

    ![Zrzut ekranu przedstawiający replikację geograficzną i stan trybu failover](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Sprawdź, czy konto magazynu jest skonfigurowane pod kątem magazynu geograficznie nadmiarowego (GRS) lub magazynu geograficznie nadmiarowego do odczytu (RA-GRS). Jeśli tak nie jest, wybierz pozycję **Konfiguracja** w obszarze **Ustawienia** , aby zaktualizować konto tak, aby było geograficznie nadmiarowe. 
4. Właściwość **czas ostatniej synchronizacji** wskazuje, jak daleko pomocnicza jest poza podstawową. **Czas ostatniej synchronizacji** to oszacowanie zakresu utraty danych, który będzie dostępny po zakończeniu pracy w trybie failover.
5. Wybierz pozycję **Przygotuj do trybu failover (wersja zapoznawcza)**. 
6. Przejrzyj okno dialogowe potwierdzenia. Gdy wszystko będzie gotowe, wprowadź **wartość tak** , aby potwierdzić i zainicjować tryb failover.

    ![Zrzut ekranu przedstawiający okno dialogowe potwierdzenia dla konta w trybie failover](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>[Narzędzia](#tab/azure-powershell)

Aby można było zainicjować pracę w trybie failover przy użyciu programu PowerShell, należy najpierw zainstalować moduł 6.0.1 w wersji zapoznawczej. Wykonaj następujące kroki, aby zainstalować moduł:

1. Odinstaluj wszystkie poprzednie instalacje Azure PowerShell:

    - Usuń wszystkie poprzednie instalacje Azure PowerShell z systemu Windows za pomocą ustawienia **aplikacje & funkcje** w obszarze **Ustawienia**.
    - Usuń wszystkie moduły **platformy Azure** z programu `%Program Files%\WindowsPowerShell\Modules`.

1. Upewnij się, że masz zainstalowaną najnowszą wersję programu PowerShellGet. Otwórz okno programu Windows PowerShell i uruchom następujące polecenie, aby zainstalować najnowszą wersję:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Zamknij i ponownie otwórz okno programu PowerShell po zainstalowaniu PowerShellGet. 

1. Zainstaluj najnowszą wersję Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Zainstaluj moduł usługi Azure Storage w wersji zapoznawczej obsługujący tryb failover konta:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. Zamknij i ponownie otwórz okno programu PowerShell.
 
Aby zainicjować tryb failover konta z programu PowerShell, wykonaj następujące polecenie:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zainicjować tryb failover na koncie przy użyciu interfejsu wiersza polecenia platformy Azure, wykonaj następujące polecenia:

```azurecli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>Następne kroki

- [Odzyskiwanie po awarii i tryb failover konta (wersja zapoznawcza) w usłudze Azure Storage](storage-disaster-recovery-guidance.md)
- [Projektowanie wysoko dostępnych aplikacji przy użyciu magazynu RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Samouczek: Tworzenie aplikacji o wysokiej dostępności przy użyciu magazynu obiektów BLOB](../blobs/storage-create-geo-redundant-storage.md) 
