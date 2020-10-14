---
title: Samouczek — łączenie Azure Analysis Services z Power BI Desktop | Microsoft Docs
author: minewiskan
description: W tym samouczku dowiesz się, jak uzyskać Analysis Services nazwę serwera z Azure Portal a następnie połączyć się z serwerem przy użyciu Power BI Desktop.
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 46805427e6d00e858cf869eef68bf68549f57fb9
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018121"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Samouczek: nawiązywanie połączenia za pomocą programu Power BI Desktop

W tym samouczku użyjesz programu Power BI Desktop do nawiązania połączenia z przykładową modelową bazą danych adventureworks na serwerze. Zadania, które należy wykonać, symulują typowe połączenie użytkownika z modelem i tworzenie podstawowego raportu na podstawie danych z modelu.

> [!div class="checklist"]
> * Uzyskiwanie nazwy serwera z portalu
> * Nawiązywanie połączenia przy użyciu programu Power BI Desktop
> * Tworzenie podstawowego raportu

## <a name="prerequisites"></a>Wymagania wstępne

- [Dodaj przykładową modelową bazą danych adventureworks](../analysis-services-create-sample-model.md) do serwera.
- Nadaj uprawnienia do [*odczytu*](../analysis-services-server-admins.md) przykładowej modelowej bazy danych adventureworks.
- [Zainstaluj najnowszy program Power BI Desktop](https://powerbi.microsoft.com/desktop).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
W tym samouczku logowanie do portalu jest konieczne tylko w celu uzyskania nazwy serwera. Zazwyczaj użytkownicy uzyskują nazwę serwera od administratora serwera.

Zaloguj się do [portalu](https://portal.azure.com/).

## <a name="get-server-name"></a>Uzyskiwanie nazwy serwera
Aby móc nawiązać połączenie z serwerem z poziomu programu Power BI Desktop, najpierw należy uzyskać nazwę serwera. Możesz uzyskać nazwę serwera w portalu.

Skopiuj nazwę serwera z **portalu Azure** > serwer > **Omówienie** > **Nazwa serwera**.
   
   ![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Nawiązywanie połączenie w programie Power BI Desktop

1. W Power BI Desktop kliknij pozycję **Pobierz dane**  >  **Azure**  >  **Azure Analysis Services Database**.

   ![Łączenie w oknie Pobieranie danych](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. W polu **Serwer** wklej nazwę serwera, w polu **Baza danych** wprowadź **adventureworks** i kliknij przycisk **OK**.

   ![Określanie nazwy serwera i modelowej bazy danych](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. Po wyświetleniu monitu wprowadź poświadczenia. Używane konto musi mieć co najmniej uprawnienia do odczytu przykładowej modelowej bazy danych adventureworks.

    Model adventureworks zostanie otwarty w programie Power BI Desktop z pustym raportem w widoku Raport. Na liście **Pola** zostaną wyświetlone wszystkie nieukryte obiekty modelu. Stan połączenia jest wyświetlany w prawym dolnym rogu.

4. Na panelu **WIZUALIZACJE** wybierz pozycję **Wykres słupkowy grupowany**, a następnie kliknij przycisk **Format** (ikona wałka malarskiego) i włącz **Etykiety danych**. 

   ![Wizualizacje](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. W obszarze **pola**  >  tabela**Sprzedaż internetowa** wybierz pozycję miary **Sales Total** i **Margin** . W tabeli **Product Category** wybierz miarę **Product Category Name**.

   ![Ukończony raport](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Poświęć kilka minut, aby poznać przykładowy model adventureworks. Możesz utworzyć różne wizualizacje i wyciąć niektóre dane oraz metryki. Po zakończeniu modyfikowania raportu pamiętaj, aby go zapisać.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli raport nie będzie już potrzebny, nie zapisuj go lub usuń pliki, jeśli raport został już zapisany.

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób użycia programu Power BI Desktop do nawiązywania połączenia z modelem danych na serwerze i tworzenia podstawowego raportu. Jeśli nie znasz sposobu tworzenia modelu danych, zapoznaj się z [samouczkiem dotyczącym modelowania danych tabelarycznych sprzedaży internetowej firmy Adventure Works](/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) w dokumentacji SQL Server Analysis Services.