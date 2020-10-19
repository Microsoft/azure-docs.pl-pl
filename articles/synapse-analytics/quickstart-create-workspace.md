---
title: 'Szybki Start: Tworzenie obszaru roboczego Synapse'
description: Utwórz obszar roboczy Synapse, wykonując czynności opisane w tym przewodniku.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 9780a2adb60c690abda6880157252aaa0c562f18
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171787"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Szybki Start: Tworzenie obszaru roboczego Synapse
W tym przewodniku szybki start opisano kroki tworzenia obszaru roboczego usługi Azure Synapse za pomocą Azure Portal.

## <a name="create-a-synapse-workspace"></a>Tworzenie obszaru roboczego usługi Synapse

1. Otwórz [Azure Portal](https://portal.azure.com)i w górnej części Szukaj **Synapse**.
1. W wynikach wyszukiwania w obszarze **usługi**wybierz pozycję **Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)**.
1. Wybierz pozycję **Dodaj** , aby utworzyć obszar roboczy przy użyciu tych ustawień:

    |Tab|Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|---|
    |Podstawy|**Nazwa obszaru roboczego**|Możesz nadać mu nazwę.| W tym dokumencie będziemy używać funkcji My **Workspace**.|
    |Podstawy|**Region**|Dopasuj region konta magazynu.|

1. Do utworzenia obszaru roboczego jest potrzebne konto ADLSGEN2. Najprostszą opcją jest utworzenie nowej. Jeśli chcesz ponownie użyć istniejącego, musisz wykonać dodatkową konfigurację. 
1. Opcja 1 — Tworzenie nowego konta ADLSGEN2 
    1. W obszarze **wybierz Data Lake Storage Gen 2**kliknij pozycję **Utwórz nową** i nadaj jej nazwę **contosolake**.
    1. W obszarze **wybierz Data Lake Storage Gen 2**kliknij pozycję **system plików** i nadaj jej nazwę **Użytkownicy**.
1. Opcja 2 zapoznaj się z instrukcjami **przygotowywania konta magazynu** w dolnej części tego dokumentu.
1. Obszar roboczy usługi Azure Synapse będzie używać tego konta magazynu jako konta magazynu "podstawowe" i kontenera do przechowywania danych obszaru roboczego. Obszar roboczy przechowuje dane w tabelach Apache Spark. Przechowuje dzienniki aplikacji platformy Spark w folderze o nazwie **/Synapse/WorkspaceName**.
1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Obszar roboczy jest gotowy w ciągu kilku minut.

## <a name="open-synapse-studio"></a>Otwórz Synapse Studio

Po utworzeniu obszaru roboczego usługi Azure Synapse dostępne są dwa sposoby otwierania programu Synapse Studio:

* Otwórz obszar roboczy Synapse w [Azure Portal](https://portal.azure.com). W górnej części sekcji **Przegląd** wybierz pozycję Uruchom program **Synapse Studio**.
* Przejdź do `https://web.azuresynapse.net` obszaru roboczego i zaloguj się do niego.

## <a name="prepare-an-existing-storage-account-for-use-with-synapse-analytics"></a>Przygotuj istniejące konto magazynu do użycia z usługą Synapse Analytics

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Przejdź do istniejącego konta magazynu ADLSGEN2
1. Wybierz pozycję **Kontrola dostępu (IAM)** w okienku po lewej stronie. Następnie przypisz następujące role lub upewnij się, że są już przypisane:
    * Przypisz siebie do roli **właściciela** .
    * Przypisz siebie do roli **właściciela danych obiektu blob magazynu** .
1. W okienku po lewej stronie wybierz pozycję **kontenery** i Utwórz kontener.
1. Można nadać kontenerowi dowolną nazwę. W tym dokumencie zmienimy nazwy **użytkowników**kontenera.
1. Zaakceptuj domyślne ustawienie **poziomu dostępu publicznego**, a następnie wybierz pozycję **Utwórz**.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>Konfigurowanie dostępu do konta magazynu z obszaru roboczego

Zarządzane tożsamości dla obszaru roboczego usługi Azure Synapse prawdopodobnie mają już dostęp do konta magazynu. Wykonaj następujące kroki, aby upewnić się, że:

1. Otwórz [Azure Portal](https://portal.azure.com) i konto magazynu podstawowego wybrane dla Twojego obszaru roboczego.
1. Wybierz pozycję **Kontrola dostępu (IAM)** w okienku po lewej stronie.
1. Przypisz poniższe role lub upewnij się, że są już przypisane. Używamy tej samej nazwy dla tożsamości obszaru roboczego i nazwy obszaru roboczego.
    * W przypadku roli **współautor danych obiektów blob magazynu** na koncie magazynu należy przypisać obszar **roboczy** jako tożsamość obszaru roboczego.
    * Przypisz **jako nazwę obszaru roboczego.**

1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie puli SQL](quickstart-create-sql-pool-studio.md) 
* [Tworzenie puli Apache Spark](quickstart-create-apache-spark-pool-portal.md)
* [Korzystanie z bazy danych SQL na żądanie](quickstart-sql-on-demand.md)
