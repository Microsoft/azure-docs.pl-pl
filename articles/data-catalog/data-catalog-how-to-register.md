---
title: Rejestrowanie źródeł danych w Azure Data Catalog
description: W tym artykule opisano sposób rejestrowania źródeł danych w Azure Data Catalog, w tym pól metadanych wyodrębnionych podczas rejestracji.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: fe132c5f80e138ef47db758ad42c04b8e854ca00
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674805"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Rejestrowanie źródeł danych w Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Wprowadzenie
Azure Data Catalog to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i odnajdywania źródeł danych przedsiębiorstwa. Innymi słowy, Data Catalog ułatwia osobom odkrywanie i używanie źródeł danych oraz korzystanie z nich w celu uzyskania większej wartości z istniejących danych. Pierwszym krokiem do przeprowadzenia odnajdywania źródła danych za pośrednictwem Data Catalog jest zarejestrowanie tego źródła danych.

## <a name="register-data-sources"></a>Rejestrowanie źródeł danych (Rejestrowanie źródeł danych)
Rejestracja to proces wyodrębniania metadanych ze źródła danych i kopiowania tych danych do usługi Data Catalog. Dane pozostają w miejscu, w którym aktualnie się znajdują, i podlegają kontroli administratorów i zasadom obowiązującym w danym systemie.

Aby zarejestrować źródło danych, wykonaj następujące czynności:
1. W portalu Azure Data Catalog Uruchom Narzędzie rejestracji Data Catalog źródła danych. 
2. Zaloguj się za pomocą konta służbowego z tymi samymi poświadczeniami Azure Active Directory, których używasz do logowania się w portalu.
3. Wybierz źródło danych, które chcesz zarejestrować.

Aby uzyskać szczegółowe informacje krok po kroku, zobacz samouczek wprowadzenie [do Azure Data Catalog](data-catalog-get-started.md) .

Po zarejestrowaniu źródła danych katalog śledzi jego lokalizację i indeksuje jego metadane. Użytkownicy mogą wyszukiwać, przeglądać i odnajdować źródło danych, a następnie używać ich do łączenia się z nim przy użyciu wybranej aplikacji lub narzędzia.

## <a name="supported-data-sources"></a>Obsługiwane źródła danych
Aby zapoznać się z listą obecnie obsługiwanych źródeł danych, zobacz [Data Catalog DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Metadane strukturalne
Po zarejestrowaniu źródła danych narzędzie do rejestracji wyodrębnia informacje o strukturze wybranych obiektów. Te informacje są określane jako metadane strukturalne.

W przypadku wszystkich obiektów te metadane strukturalne obejmują lokalizację obiektu, dzięki czemu użytkownicy, którzy odnajdują dane mogą używać tych informacji do łączenia się z obiektem w wybranych narzędziach klienta. Inne metadane strukturalne obejmują nazwę i typ obiektu oraz nazwę atrybutu/kolumny i typ danych.

## <a name="descriptive-metadata"></a>Metadane opisowe
Oprócz podstawowych metadanych strukturalnych, które zostały wyodrębnione ze źródła danych, narzędzie do rejestracji źródła danych wyodrębnia metadane opisowe. W przypadku SQL Server Analysis Services i SQL Server Reporting Services te metadane są pobierane z właściwości opisu udostępnianych przez te usługi. W przypadku SQL Server wartości podane przy użyciu \_ rozszerzonej właściwości opisu MS Description są wyodrębniane. W przypadku Oracle Database Narzędzie rejestracji źródła danych wyodrębnia kolumnę Komentarze z \_ \_ widoku komentarze wszystkich kart.

Oprócz metadanych opisowych, które zostały wyodrębnione ze źródła danych, użytkownicy mogą wprowadzać opisowe metadane za pomocą narzędzia rejestracji źródła danych. Użytkownicy mogą dodawać Tagi i identyfikować ekspertów dla zarejestrowanych obiektów. Wszystkie te metadane opisowe są kopiowane do usługi Data Catalog wraz z metadanymi strukturalnymi.

## <a name="include-previews"></a>Uwzględnij podglądy
Domyślnie tylko metadane są wyodrębniane ze źródeł danych i kopiowane do usługi Data Catalog, ale zrozumienie źródła danych jest często łatwiejsze, gdy można wyświetlić przykład danych, które zawiera.

Za pomocą narzędzia rejestracji źródła danych Data Catalog, można dołączyć Podgląd migawek danych w każdej zarejestrowanej tabeli i widoku. Jeśli użytkownik zdecyduje się na uwzględnienie podglądów podczas rejestracji, Narzędzie rejestracji będzie zawierać do 20 rekordów z każdej tabeli i widoku. Ta migawka jest następnie kopiowana do wykazu wraz z metadanymi strukturalnymi i opisowymi.

> [!NOTE]
> Szerokie tabele z dużą liczbą kolumn mogą zawierać mniej niż 20 rekordów uwzględnionych w wersji zapoznawczej.
>
>

## <a name="include-data-profiles"></a>Uwzględnij profile danych
Podobnie jak wersje zapoznawcze mogą zapewnić cenny kontekst użytkownikom, którzy wyszukują źródła danych w Data Catalog, w tym profil danych, można ułatwić zrozumienie odnalezionych źródeł danych.

Za pomocą narzędzia rejestracji źródła danych Data Catalog, można dołączyć profil danych dla każdej zarejestrowanej tabeli i widoku. Jeśli wybierzesz opcję dołączenia profilu danych podczas rejestracji, Narzędzie rejestracji zawiera zagregowane statystyki dotyczące danych w poszczególnych tabelach i widokach, w tym:

* Liczba wierszy i rozmiar danych w obiekcie.
* Data ostatniej aktualizacji danych i schematu obiektu.
* Liczba rekordów o wartości null i różne wartości kolumn.
* Wartości minimalna, maksymalna, średnia i odchylenie standardowe dla kolumn.

Te statystyki są następnie kopiowane do wykazu wraz z metadanymi strukturalnymi i opisowymi.

> [!NOTE]
> Kolumny tekstu i daty nie zawierają statystyk odchyleń średniej lub standardowej w swoim profilu danych.
>
>

## <a name="update-registrations"></a>Aktualizowanie rejestracji
Zarejestrowanie źródła danych umożliwia jego odnajdywanie w Data Catalog w przypadku użycia metadanych i opcjonalnej wersji zapoznawczej wyodrębnionej podczas rejestracji. Jeśli źródło danych musi zostać zaktualizowane w wykazie (na przykład jeśli schemat obiektu został zmieniony, wcześniej wykluczone tabele powinny być dołączone lub chcesz zaktualizować dane zawarte w wersjach zapoznawczych), można ponownie uruchomić Narzędzie rejestracji źródła danych.

Ponowne rejestrowanie już zarejestrowanego źródła danych wykonuje operację scalania "upsert": istniejące obiekty są aktualizowane i tworzone są nowe obiekty. Wszystkie metadane udostępniane przez użytkowników za pomocą portalu Data Catalog są zachowywane.

## <a name="summary"></a>Podsumowanie
Ze względu na to, że program kopiuje metadane strukturalne i opisowe ze źródła danych do usługi katalogowej, rejestrowanie źródła danych w Data Catalog ułatwia odnajdywanie i zrozumienie danych. Po zarejestrowaniu źródła danych można dodawać do nich adnotacje, zarządzać nimi i odnajdywać je przy użyciu portalu Data Catalog.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rejestrowania źródeł danych, zobacz samouczek wprowadzenie [do Azure Data Catalog](data-catalog-get-started.md) .
