---
title: Korzystanie z programu Synapse Studio (wersja zapoznawcza)
description: W tym przewodniku szybki start zobaczysz i dowiesz się, jak łatwo można wysyłać zapytania o różne typy plików przy użyciu programu Synapse Studio.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a29fd66cf264b09cc5e0db7ac1a329be3f297bb8
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096337"
---
# <a name="quickstart-using-synapse-studio-preview"></a>Szybki Start: korzystanie z programu Synapse Studio (wersja zapoznawcza)

W tym przewodniku szybki start dowiesz się, jak wykonywać zapytania dotyczące plików przy użyciu programu Synapse Studio.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz obszar roboczy usługi Azure Synapse i skojarzone konto magazynu](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>Uruchom Synapse Studio

W obszarze roboczym usługi Azure Synapse w Azure Portal kliknij pozycję **Uruchom Synapse Studio**.

![Uruchom Synapse Studio](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

Alternatywnie możesz uruchomić program Synapse Studio, klikając pozycję [Azure Synapse Analytics](https://web.azuresynapse.net) i podając odpowiednie wartości dzierżawców, subskrypcji i obszaru roboczego.

## <a name="browse-storage-accounts"></a>Przeglądaj konta magazynu

Po otwarciu programu Synapse Studio przejdź do obszaru **dane** , a następnie rozwiń węzeł **konta magazynu** , aby wyświetlić konto magazynu w obszarze roboczym.

![Przeglądanie plików w magazynie](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Możesz tworzyć nowe foldery i przekazywać pliki przy użyciu linków na pasku narzędzi, aby organizować pliki.

## <a name="query-files-on-storage-account"></a>Pliki zapytań na koncie magazynu

> [!IMPORTANT]
> Musisz być członkiem `Storage Blob Reader` roli w magazynie bazowym, aby można było wykonywać zapytania dotyczące plików. Dowiedz się, jak [przypisać do usługi Azure Storage uprawnienia kontroli dostępu do magazynu **danych obiektów BLOB** lub **magazynu obiektów BLOB** ](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role).

1. Przekaż niektóre `PARQUET` pliki.
2. Wybierz co najmniej jeden plik, a następnie utwórz nowy skrypt SQL lub Notes Spark, aby zobaczyć zawartość plików. Jeśli chcesz utworzyć Notes, musisz utworzyć [pulę Apache Spark w obszarze roboczym](quickstart-create-apache-spark-pool.md).

   ![Wysyłanie zapytań do plików w magazynie](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Uruchom wygenerowane zapytanie lub Notes, aby zobaczyć zawartość pliku:

   ![Zobacz zawartość pliku](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. Można zmienić zapytanie, aby filtrować i sortować wyniki. Funkcje języka dostępne w programie SQL na żądanie w temacie [funkcje SQL — Omówienie](sql/overview-features.md).

## <a name="next-steps"></a>Następne kroki

- Włącz użytkownikom usługi Azure AD wykonywanie zapytań [dotyczących plików, przypisując im uprawnienia kontrola dostępu do **magazynu danych** lub **Magazyn danych obiektów BLOB** w usłudze Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)
- [Wysyłanie zapytań do plików w usłudze Azure Storage przy użyciu programu SQL na żądanie](sql/on-demand-workspace-overview.md)
- [Utwórz pulę Apache Spark](quickstart-create-apache-spark-pool.md)
- [Tworzenie raportu Power BI dla plików przechowywanych w usłudze Azure Storage](sql/tutorial-connect-power-bi-desktop.md)
