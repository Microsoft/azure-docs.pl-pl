---
title: Użyj Synapse Studio (wersja zapoznawcza)
description: W tym przewodniku Szybki start zobaczysz i dowiesz się, jak łatwo jest wyszukiwać różne typy plików za pomocą Synapse Studio.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 294f53fe929343708bdbb9564b23c46865cf02ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423866"
---
# <a name="quickstart-using-synapse-studio-preview"></a>Szybki start: korzystanie z Synapse Studio (wersja zapoznawcza)

W tym przewodniku Szybki start dowiesz się, jak wyszukiwać pliki za pomocą Synapse Studio.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz obszar roboczy Usługi Azure Synapse i skojarzone konto magazynu](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>Uruchom Synapse Studio

W obszarze roboczym Usługi Azure Synapse w portalu Azure kliknij pozycję **Uruchom studio Synapse Studio**.

![Uruchom Synapse Studio](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

Alternatywnie można uruchomić Synapse Studio, klikając [usługę Azure Synapse Analytics](https://web.azuresynapse.net) i podając odpowiednie wartości dzierżawy, subskrypcji i obszaru roboczego.

## <a name="browse-storage-accounts"></a>Przeglądanie kont magazynu

Po otwarciu synapse studio przejdź do **usługi Dane,** a następnie rozwiń **pozycję Konta magazynu,** aby wyświetlić konto magazynu w obszarze roboczym.

![Przeglądanie plików w pamięci masowej](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Można tworzyć nowe foldery i przekazywać pliki za pomocą łączy na pasku narzędzi w celu organizowania plików.

## <a name="query-files-on-storage-account"></a>Pliki kwerend na koncie magazynu

> [!IMPORTANT]
> Musisz być członkiem `Storage Blob Reader` roli w magazynie źródłowym, aby móc wysyłać zapytania do plików. Dowiedz się, jak [przypisać uprawnienia RBAC **czytnika danych obiektów blob magazynu** lub źródło danych **magazynu** w usłudze Azure Storage.](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)

1. Prześlij kilka `PARQUET` plików.
2. Wybierz jeden lub więcej plików, a następnie utwórz nowy skrypt SQL lub notes platformy Spark, aby wyświetlić zawartość plików. Jeśli chcesz utworzyć notes, musisz utworzyć [pulę Platformy Spark Apache w obszarze roboczym](spark/apache-spark-notebook-create-spark-use-sql.md).

   ![Pliki zapytań o przechowywanie](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Uruchom wygenerowaną kwerendę lub notes, aby wyświetlić zawartość pliku:

   ![Zobacz zawartość pliku](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. Kwerendę można zmienić w celu filtrowania i sortowania wyników. Znajdź funkcje językowe, które są dostępne w sql na żądanie w [omówienie funkcji SQL](sql/overview-features.md).

## <a name="next-steps"></a>Następne kroki

- Włączanie użytkowników usługi Azure AD do wykonywania zapytań o pliki [przez przypisywanie uprawnień RBAC **programu Blob programu Storage lub** **współautora danych magazynu** w usłudze Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)
- [Pliki zapytań w usłudze Azure Storage przy użyciu języka SQL on-demand](sql/on-demand-workspace-overview.md)
- [Tworzenie puli platformy Spark Apache](spark/apache-spark-notebook-create-spark-use-sql.md)
- [Tworzenie raportu usługi Power BI dla plików przechowywanych w usłudze Azure Storage](sql/tutorial-connect-power-bi-desktop.md)
