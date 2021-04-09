---
title: Instalowanie narzędzi Azure Data Lake Tools for Visual Studio
description: W tym artykule opisano sposób instalowania programu Azure Data Lake Tools for Visual Studio.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: cb0183ae229c328588a31c2c0549e7e93fd19b78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019008"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Instalacja narzędzi Data Lake Tools for Visual Studio

Dowiedz się, jak tworzyć konta Azure Data Lake Analytics przy użyciu programu Visual Studio. Możesz definiować zadania w [języku U-SQL](data-lake-analytics-u-sql-get-started.md) i przesyłać zadania do usługi Data Lake Analytics. Aby uzyskać więcej informacji na temat Data Lake Analytics, zobacz [Azure Data Lake Analytics Omówienie](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Visual Studio**: obsługiwane są wszystkie wersje z wyjątkiem Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Zestaw Microsoft Azure SDK dla platformy .NET** w wersji 2.7.1 lub nowszej. Można go zainstalować przy użyciu [Instalatora platformy internetowej](https://www.microsoft.com/web/downloads/platform.aspx).
* Konto usługi **Data Lake Analytics**. Aby utworzyć konto, zobacz artykuł [Wprowadzenie do pracy z usługą Azure Data Lake Analytics za pomocą witryny Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Zainstaluj Azure Data Lake Tools for Visual Studio 2017 lub Visual Studio 2019

Azure Data Lake Tools for Visual Studio jest obsługiwane w programie Visual Studio 2017 15,3 lub nowszym. Jest to narzędzie, które jest częścią **magazynu i przetwarzania danych** oraz obciążeń związanych z **programowaniem na platformie Azure** . Włącz jedno z tych dwóch obciążeń w ramach instalacji programu Visual Studio.

Włącz obciążenie **magazynu i przetwarzania danych** , jak pokazano poniżej:

![Włączanie obciążenia magazynu i przetwarzania danych](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Włącz obciążenie **Programowanie na platformie Azure** , jak pokazano:

![Wybierz obciążenie Programowanie na platformie Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instalowanie narzędzi Azure Data Lake Tools for Visual Studio w wersji 2013 i 2015

Pobierz i zainstaluj [narzędzia Microsoft Azure Data Lake i Stream Analytics Tools for Visual Studio ](https://aka.ms/adltoolsvs). Po zakończeniu instalacji program Visual Studio ma następujące zmiany:

* Węzeł **Eksplorator serwera**  >  **platformy Azure** zawiera węzeł **Data Lake Analytics** .
* Menu **Narzędzia** zawiera element **Data Lake**.

## <a name="next-steps"></a>Następne kroki

* Aby rejestrować informacje diagnostyczne, zobacz [Uzyskiwanie dostępu do dzienników diagnostycznych dla Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Aby uzyskać informacje na temat bardziej złożonego zapytania, zobacz temat [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby skorzystać z widoku wykonywania wierzchołków, zobacz temat [Używanie widoku wykonywania wierzchołka w Data Lake narzędzia dla programu Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
