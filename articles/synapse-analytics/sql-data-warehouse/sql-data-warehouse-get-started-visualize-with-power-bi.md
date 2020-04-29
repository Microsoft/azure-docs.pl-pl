---
title: Wizualizuj dane przy użyciu Power BI Microsoft Azure
description: Wizualizacja danych usługi SQL Data Warehouse przy użyciu usługi Power BI
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e764eeab6f681d90e1a602f02cdb03330d6fd3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80350945"
---
# <a name="visualize-data-with-power-bi"></a>Wizualizacja danych przy użyciu usługi Power BI
Ten samouczek przedstawia sposób nawiązania połączenia z usługą SQL Data Warehouse przy użyciu usługi Power BI oraz tworzenia podstawowych wizualizacji.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych w tym samouczku potrzebne są:

* Baza danych usługi SQL Data Warehouse ze wstępnie załadowaną bazą danych AdventureWorksDW. Aby zainicjować obsługę administracyjną magazynu danych, zobacz [tworzenie SQL Data Warehouse](create-data-warehouse-portal.md) i wybieranie ładowania przykładowych danych. Jeśli masz już magazyn danych, ale nie masz przykładowych danych, możesz [załadować WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Nawiąż połączenie z bazą danych
Aby otworzyć usługę Power BI nawiązać połączenie się z bazą danych AdventureWorksDW:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. Kliknij pozycję **Bazy danych SQL** i wybierz bazę danych AdventureWorks usługi SQL Data Warehouse.
   
    ![Znajdowanie bazy danych](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Kliknij przycisk „Otwórz w usłudze Power BI”.
   
    ![Przycisk usługi Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Powinna zostać wyświetlona strona połączenia z usługą SQL Data Warehouse przedstawiająca adres sieci Web bazy danych. Kliknij przycisk Dalej.
   
    ![Połączenie z usługą Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Wprowadź nazwę użytkownika i hasło do serwera SQL platformy Azure.
   
    ![Logowanie Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Aby otworzyć bazę danych, kliknij zestaw danych AdventureWorksDW w lewym bloku.
   
    ![Otwieranie bazy danych AdventureWorksDW w usłudze Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Tworzenie raportu
Teraz możesz przystąpić do analizowania przykładowych danych bazy AdventureWorksDW przy użyciu usługi Power BI. Do wykonywania analizy służy widok bazy danych AdventureWorksDW o nazwie AggregateSales (Łączna sprzedaż). Ten widok zawiera kilka kluczowych metryk do analizowania sprzedaży firmy.

1. Aby utworzyć mapę kwot sprzedaży na podstawie kodów pocztowych, w okienku pól po prawej stronie kliknij widok AggregateSales (Łączna sprzedaż), aby go rozwinąć. Kliknij kolumny PostalCode (Kod pocztowy) and SalesAmount (Kwota sprzedaży), aby je zaznaczyć.
   
    ![Power BI wybiera AggregateSales](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI automatycznie rozpoznane dane geograficzne i umieść je w mapie.
   
    ![Mapa w usłudze Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. W tym kroku zostanie utworzony wykres słupkowy przedstawiający kwotę sprzedaży w odniesieniu do dochodu klienta. Aby utworzyć wykres słupkowy, przejdź do rozwiniętego widoku AggregateSales. Kliknij pole SalesAmount (Kwota sprzedaży). Przeciągnij pole Customer Income (Dochód klienta) w lewo i upuść go w sekcji Axis (Oś).
   
    ![Power BI wybiera oś](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Wykres słupkowy po lewej stronie.
   
    ![Wykres słupkowy w usłudze Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. W tym kroku utworzymy wykres liniowy, który przedstawia kwoty sprzedaży według dat zamówienia. Aby utworzyć wykres liniowy, przejdź do widoku rozwinięte AggregateSales. Kliknij pola SalesAmount (Kwota sprzedaży) i OrderDate (Data zamówienia). W kolumnie wizualizacje kliknij ikonę wykres liniowy, która jest pierwszą ikoną w drugim wierszu w obszarze wizualizacje.
   
    ![Power BI wybiera wykres liniowy](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Masz teraz raport wyświetlający trzy różne wizualizacje danych.
   
    ![Wykres liniowy w usłudze Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

W dowolnym momencie możesz kliknąć menu **Plik** i wybrać polecenie **Zapisz**, aby zapisać postęp.

## <a name="using-direct-connect"></a>Używanie bezpośredniego połączenia
Podobnie jak w przypadku Azure SQL Database, funkcja SQL Data Warehouse Direct Connect umożliwia przekazywanie logiczne przy użyciu funkcji analitycznych Power BI. Za pomocą bezpośredniego połączenia zapytania są wysyłane z powrotem do Azure SQL Data Warehouse w czasie rzeczywistym podczas eksplorowania danych.  Ta funkcja, w połączeniu z skalą SQL Data Warehouse, umożliwia tworzenie dynamicznych raportów w ciągu kilku minut przed terabajtami danych. Ponadto wprowadzenie przycisku Otwórz w Power BI umożliwia użytkownikom bezpośrednie łączenie Power BI ze swoimi SQL Data Warehouse bez zbierania informacji z innych części platformy Azure.

W przypadku korzystania z funkcji bezpośrednie łączenie:

* Określ w pełni kwalifikowaną nazwę serwera podczas nawiązywania połączenia.
* Upewnij się, że reguły zapory dla bazy danych są skonfigurowane tak, aby zezwalały na dostęp do usług platformy Azure.
* Każda akcja, taka jak wybór kolumny lub dodanie filtru, bezpośrednio wysyła zapytanie do magazynu danych.
* Kafelki są odświeżane automatycznie i co około 15 minut.
* Polecenie Q&A nie jest dostępne dla bezpośrednich połączeń zestawów danych.
* Zmiany schematu są włączane automatycznie.
* Wszystkie zapytania bezpośredniego łączenia będą przekroczyć limit czasu po 2 minutach.

Te ograniczenia i uwagi mogą ulec zmianie w miarę poprawy środowiska.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy podaliśmy Ci trochę czasu na rozgrzanie danych przykładowych, zobacz jak [opracowywać](sql-data-warehouse-overview-develop.md) lub [ładować](design-elt-data-loading.md). Lub zapoznaj się z [witryną sieci web Power BI](https://www.powerbi.com/).
