---
title: Jak nawiązać połączenie ze źródłami danych w Azure Data Catalog
description: Artykuł z instrukcje dotyczące nawiązywania połączenia ze źródłami danych odnalezionymi za pomocą Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: da3302ac25aaac6202f42cee400b307fc701fe50
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674975"
---
# <a name="how-to-connect-to-data-sources"></a>Jak nawiązywać połączenie ze źródłami danych

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Wprowadzenie
**Microsoft Azure Data Catalog** to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i odnajdywania źródeł danych w przedsiębiorstwie. Innymi słowy, **Azure Data Catalog** to pomoc dla osób, które odnajdują i korzystają ze źródeł danych oraz pomagają organizacjom w uzyskaniu większej korzyści z istniejących danych. Kluczowym aspektem tego scenariusza jest użycie danych — gdy użytkownik odnajduje źródło danych i rozumie jego przeznaczenie, następnym krokiem jest nawiązanie połączenia ze źródłem danych, aby umieścić dane do użycia.

## <a name="data-source-locations"></a>Lokalizacje źródła danych
Podczas rejestracji źródła danych **Azure Data Catalog** otrzymuje metadane dotyczące źródła danych. Metadane zawierają szczegóły lokalizacji źródła danych. Szczegóły lokalizacji będą się różnić w zależności od źródła danych ze źródłem danych, ale zawsze zawierają informacje niezbędne do nawiązania połączenia. Na przykład lokalizacja tabeli SQL Server zawiera nazwę serwera, nazwę bazy danych, nazwę schematu i nazwę tabeli, podczas gdy Lokalizacja raportu SQL Server Reporting Services zawiera nazwę serwera i ścieżkę do raportu. Inne typy źródeł danych będą mieć lokalizacje odzwierciedlające strukturę i możliwości systemu źródłowego.

## <a name="integrated-client-tools"></a>Zintegrowane narzędzia klienckie
Najprostszym sposobem nawiązywania połączenia ze źródłem danych jest użycie "Otwórz w..." w portalu **Azure Data Catalog** . To menu wyświetla listę opcji łączenia z wybranym zasobem danych.
W przypadku korzystania z domyślnego widoku kafelków to menu jest dostępne na każdym kafelku.

 ![Otwieranie tabeli SQL Server w programie Excel z kafelka zasobów danych](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

W przypadku korzystania z widoku listy menu jest dostępne na pasku wyszukiwania w górnej części okna portalu.

 ![Otwieranie raportu SQL Server Reporting Services w programie Menedżer raportów](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Obsługiwane aplikacje klienckie
W przypadku korzystania z funkcji "Otwórz w..." dla źródeł danych w portalu Azure Data Catalog należy zainstalować na komputerze klienckim poprawną aplikację kliencką.

| Otwórz w aplikacji | Rozszerzenie/protokół pliku | Obsługiwane wersje aplikacji |
| --- | --- | --- |
| Excel |odc |Excel 2010 lub nowszy |
| Excel (najpopularniejsze 1000) |odc |Excel 2010 lub nowszy |
| Power Query |xlsx |Excel 2016 lub Excel 2010 lub Excel 2013 z zainstalowanym dodatkiem Power Query for Excel |
| Power BI Desktop |pbix |Power BI Desktop lipca 2016 lub nowszej |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 Update 4 lub nowszy z zainstalowanymi narzędziami SQL Server |
| Menedżer raportów |http:// |Zobacz [wymagania dotyczące przeglądarki dla SQL Server Reporting Services](/sql/reporting-services/browser-support-for-reporting-services-and-power-view) |

## <a name="your-data-your-tools"></a>Twoje dane, Twoje narzędzia
Opcje dostępne w menu będą zależeć od typu aktualnie wybranego zasobu danych. Oczywiście nie wszystkie możliwe narzędzia zostaną uwzględnione w "Otwórz w..." , ale nadal łatwo Nawiązuj połączenie ze źródłem danych przy użyciu dowolnego narzędzia klienckiego. Po wybraniu zasobu danych w portalu **Azure Data Catalog** pełna lokalizacja zostanie wyświetlona w okienku właściwości.

 ![Informacje o połączeniu dla tabeli SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Szczegóły informacji o połączeniu różnią się od typu źródła danych do typu źródła danych, ale informacje zawarte w portalu będą zawierać wszystko, co jest potrzebne do nawiązania połączenia ze źródłem danych za pomocą dowolnego narzędzia klienckiego. Użytkownicy mogą kopiować szczegóły połączenia dla źródeł danych, które zostały odnalezione przy użyciu **Azure Data Catalog**, umożliwiając im współpracują z danymi w wybranym przez siebie narzędziu.

## <a name="connecting-and-data-source-permissions"></a>Łączenie i uprawnienia do źródła danych
Mimo że **Azure Data Catalog** umożliwia odnajdywanie źródeł danych, dostęp do samych danych pozostaje pod kontrolą właściciela źródła danych lub administratora. Odnajdywanie źródła danych w **Azure Data Catalog** nie daje użytkownikowi żadnych uprawnień dostępu do samego źródła danych.

Aby ułatwić użytkownikom, którzy odnajdują źródło danych, ale nie ma uprawnień dostępu do jego danych, użytkownicy mogą podawać informacje we właściwościach żądania dostępu podczas dodawania adnotacji do źródła danych. Informacje podane w tym miejscu — w tym linki do procesu lub punktu kontaktu w celu uzyskania dostępu do źródła danych — są wyświetlane obok informacji o lokalizacji źródła danych w portalu.

 ![Informacje o połączeniu z podanymi instrukcjami dostępu żądania](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Podsumowanie
Zarejestrowanie źródła danych za pomocą **Azure Data Catalog** umożliwia odnajdywanie danych przez kopiowanie strukturalnych i opisowych metadanych ze źródła danych do usługi katalogowej. Gdy źródło danych zostanie zarejestrowane i odnalezione, użytkownicy mogą nawiązywać połączenia ze źródłem danych z portalu **Azure Data Catalog** "Otwórz w..." " lub przy użyciu wybranych przez siebie narzędzi do pracy z danymi.

## <a name="see-also"></a>Zobacz też
* [Zacznij korzystać z](data-catalog-get-started.md) samouczka Azure Data Catalog, aby uzyskać szczegółowe informacje na temat sposobu nawiązywania połączenia ze źródłami danych.