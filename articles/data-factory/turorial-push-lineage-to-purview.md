---
title: Wypychanie danych o pochodzeniu z usługi Data Factory do usługi Azure Purview
description: Dowiedz się więcej na temat wypychania Data Factory danych kontrolą do platformy Azure
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 3f2297ae619145ec19b53ba79d70b7c085cbcaab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361455"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Wypchnij Data Factory dane pochodzenie do usługi Azure kontrolą (wersja zapoznawcza)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku użyjesz interfejsu użytkownika Data Factory (UI) do utworzenia potoku, który uruchamia działania i raportuje dane odtworzone na koncie usługi Azure kontrolą. Następnie możesz wyświetlić wszystkie informacje dotyczące elementów dodanych na koncie usługi Azure kontrolą.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* **Azure Data Factory**. Jeśli nie masz Azure Data Factory, zobacz [tworzenie Azure Data Factory](./quickstart-create-data-factory-portal.md).
* **Konto usługi Azure kontrolą**. Konto kontrolą przechwytuje wszystkie dane oddzielone generowane przez fabrykę danych. Jeśli nie masz konta usługi Azure kontrolą, zobacz [Tworzenie usługi Azure kontrolą](../purview/create-catalog-portal.md).


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Uruchamianie działań Data Factory i wypychanie danych z kontrolą do platformy Azure
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>Krok 1. Łączenie Data Factory z kontem kontrolą
Zaloguj się do swojego konta kontrolą w portalu kontrolą, przejdź do **centrum zarządzania**. Wybierz **Data Factory** w **połączeniach zewnętrznych** , a następnie kliknij przycisk **Nowy** , aby utworzyć połączenie z nowym Data Factory. 
[![Zrzut ekranu przedstawiający tworzenie połączenia między Data Factory i kontem ](./media/data-factory-purview/connect-adf-to-purview.png) kontrolą ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

Na stronie podręcznej możesz wybrać Data Factory, które chcesz połączyć z tym kontem kontrolą. 
![Zrzut ekranu przedstawiający nowe połączenie](./media/data-factory-purview/new-adf-purview-connection.png)

Stan można sprawdzić po utworzeniu połączenia. **Połączenie oznacza,** że połączenie między Data Factory i tym kontroląm zostało pomyślnie nawiązane. 
> [!NOTE]
> Aby utworzyć połączenie między Data Factory i Azure kontrolą, należy przypisać **do Data Factory konta** kontrolą następujące role.
> - Właściciel
> - Administrator dostępu użytkowników

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>Krok 2. wykonywanie operacji copy i przepływu danych w Data Factory
W Data Factory mogą być tworzone potoki, działania kopiowania i działania przepływu danych. Nie jest potrzebna dodatkowa konfiguracja przechwytywania danych. Dane są automatycznie przechwytywane podczas wykonywania działań.
![Zrzut ekranu działania kopiowania i przepływu danych ](./media/data-factory-purview/adf-activities-for-lineage.png) , jeśli nie wiesz, jak tworzyć działania kopiowania i przepływu danych, zobacz [Kopiowanie danych z usługi Azure Blob Storage do bazy danych w Azure SQL Database przy użyciu Azure Data Factory](./tutorial-copy-data-portal.md) i [przekształcania danych przy użyciu mapowania przepływów danych](./tutorial-data-flow.md).

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>Krok 3. Uruchamianie działań wykonywania pakietów usług SSIS w Data Factory
Możesz tworzyć potoki, wykonywać działania pakietu SSIS w Data Factory. Nie jest potrzebna dodatkowa konfiguracja przechwytywania danych. Dane są automatycznie przechwytywane podczas wykonywania działań.
![Zrzut ekranu działania wykonywania pakietu SSIS](./media/data-factory-purview/ssis-activities-for-lineage.png)

Jeśli nie wiesz, jak tworzyć działania wykonywania pakietów usług SSIS, zobacz temat [uruchamianie pakietów SSIS na platformie Azure](./tutorial-deploy-ssis-packages-azure.md).

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Krok 4. Wyświetlanie informacji o pozostałej na koncie kontrolą
Wróć do konta kontrolą. Na stronie głównej wybierz pozycję **Przeglądaj zasoby**. Wybierz odpowiedni zasób, a następnie kliknij pozycję Karta elementy powiązane. Zostaną wyświetlone wszystkie informacje dotyczące programu.
[![Zrzut ekranu konta ](./media/data-factory-purview/view-dataset.png) kontrolą ](./media/data-factory-purview/view-dataset.png#lightbox)

Możesz zobaczyć dane elementów współużytkujących dla działania kopiowania.
[![Zrzut ekranu przedstawiający kopiowanie ](./media/data-factory-purview/copy-lineage.png) elementów odnoszących ](./media/data-factory-purview/copy-lineage.png#lightbox)

Można także zobaczyć dane niepowiązane dla działania przepływu danych.
[![Zrzut ekranu przedstawiający elementy przepływu danych ](./media/data-factory-purview/dataflow-lineage.png)](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> W przypadku działania elementu przepływu danych obsługiwane są tylko źródła i ujścia. Elementy przepływu danych transformacji nie są jeszcze obsługiwane.

Można także zobaczyć dane niepowiązane dla działania wykonywania pakietu SSIS.
[![Zrzut ekranu przedstawiający elementy programu SSIS ](./media/data-factory-purview/ssis-lineage.png)](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> W przypadku działania związanego z wykonywaniem pakietu SSIS obsługiwane są tylko elementy źródłowe i docelowe. Elementy powiązane dla transformacji nie są jeszcze obsługiwane.

## <a name="next-steps"></a>Następne kroki
[Podręcznik użytkownika z wykazem](../purview/catalog-lineage-user-guide.md)

[Łączenie Data Factory z usługą Azure kontrolą](connect-data-factory-to-azure-purview.md)