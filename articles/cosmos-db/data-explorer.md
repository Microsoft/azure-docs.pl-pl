---
title: Zarządzanie danymi za pomocą Eksploratora Azure Cosmos DB
description: Azure Cosmos DB Explorer to autonomiczny interfejs oparty na sieci Web, który umożliwia wyświetlanie danych przechowywanych w Azure Cosmos DB i zarządzanie nimi.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.author: dech
ms.openlocfilehash: d4baa92fe4aa2ed402c394198684c4deec2bf9f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348606"
---
# <a name="work-with-data-using-azure-cosmos-db-explorer"></a>Pracuj z danymi za pomocą Eksploratora Azure Cosmos DB 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB Explorer to autonomiczny interfejs oparty na sieci Web, który umożliwia wyświetlanie danych przechowywanych w Azure Cosmos DB i zarządzanie nimi. Eksplorator Azure Cosmos DB jest odpowiednikiem istniejącej karty **Eksplorator danych** dostępnej w Azure Portal podczas tworzenia konta Azure Cosmos DB. Kluczowe zalety Eksploratora Azure Cosmos DB za pośrednictwem istniejącego Eksploratora danych są następujące:

* Masz pełny ekran, aby wyświetlić dane, uruchamiać zapytania, procedury składowane, wyzwalacze i przeglądać ich wyniki.  

* Możesz zapewnić tymczasowy lub stały dostęp do odczytu lub zapisu do konta bazy danych oraz jego kolekcje innym użytkownikom, którzy nie mają dostępu do Azure Portal lub subskrypcji.  

* Wyniki zapytania można udostępniać innym użytkownikom, którzy nie mają dostępu do Azure Portal lub subskrypcji.  

## <a name="access-azure-cosmos-db-explorer"></a>Dostęp do Eksploratora Azure Cosmos DB

1. Zaloguj się do [Azure Portal](https://portal.azure.com/). 

2. W obszarze **wszystkie zasoby** Znajdź i przejdź do konta Azure Cosmos DB, wybierz pozycję klucze i skopiuj **podstawowe parametry połączenia**.  

3. Przejdź do lokalizacji https://cosmos.azure.com/ , wklej parametry połączenia i wybierz pozycję **Połącz**. Korzystając z parametrów połączenia, można uzyskać dostęp do Eksploratora Azure Cosmos DB bez żadnych limitów czasu.  

   Jeśli chcesz udostępnić innym użytkownikom tymczasowy dostęp do konta Azure Cosmos DB, możesz to zrobić za pomocą adresów URL odczytu i zapisu i odczytu. 

4. Otwórz blok **Eksplorator danych** , wybierz pozycję **Otwórz pełny ekran**. W wyskakującym oknie dialogowym można wyświetlić dwa adresy URL dostępu — **Odczyt i zapis** i **Odczyt**. Te adresy URL umożliwiają tymczasowe udostępnianie konta Azure Cosmos DB innym użytkownikom. Dostęp do konta wygasa przez 24 godziny, po którym można ponownie nawiązać połączenie przy użyciu nowego adresu URL dostępu lub parametrów połączenia. 

   Do **odczytu i zapisu** — gdy udostępniasz adres URL Read-Write innym użytkownikom, mogą oni wyświetlać i modyfikować bazy danych, kolekcje, zapytania i inne zasoby skojarzone z danym kontem.

   **Odczyt** — w przypadku udostępniania adresu URL tylko do odczytu innym użytkownikom mogą wyświetlać bazy danych, kolekcje, zapytania i inne zasoby skojarzone z tym konkretnym kontem. Na przykład jeśli chcesz udostępnić wyniki zapytania członkom zespołu, którzy nie mają dostępu do Azure Portal lub konta Azure Cosmos DB, możesz podać te adresy URL.

   Wybierz typ dostępu, za pomocą którego chcesz otworzyć konto, a następnie kliknij przycisk **Otwórz**. Po otwarciu Eksploratora środowisko działa tak samo, jak w przypadku korzystania z karty Eksplorator danych w Azure Portal.

   :::image type="content" source="./media/data-explorer/open-data-explorer-with-access-url.png" alt-text="Otwieranie eksploratora usługi Azure Cosmos DB":::

## <a name="known-issues"></a>Znane problemy

Obecnie **otwarte pełne środowisko ekranu** , które umożliwia udostępnianie tymczasowego dostępu do odczytu lub zapisu, nie jest jeszcze obsługiwane w przypadku kont Azure Cosmos DB Gremlin i interfejs API tabel. Nadal można wyświetlać konta Gremlin i interfejs API tabel, przekazując parametry połączenia do Eksploratora Azure Cosmos DB. 

Obecnie wyświetlanie dokumentów zawierających identyfikator UUID nie jest obsługiwane w Eksplorator danych. Nie ma to wpływu na ładowanie kolekcji, tylko Wyświetlanie pojedynczych dokumentów lub zapytań zawierających te dokumenty. Aby wyświetlić te dokumenty i zarządzać nimi, użytkownicy powinni nadal korzystać z narzędzia, które było pierwotnie używane do tworzenia tych dokumentów.

Klienci otrzymujący błędy HTTP-401 mogą być z powodu niewystarczających uprawnień RBAC platformy Azure dla konta platformy Azure klienta, szczególnie jeśli konto ma rolę niestandardową. Wszelkie role niestandardowe muszą mieć `Microsoft.DocumentDB/databaseAccounts/listKeys/*` Akcja, aby użyć Eksplorator danych, jeśli logujesz się przy użyciu poświadczeń Azure Active Directory.

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy wiesz już, jak rozpocząć pracę z programem Azure Cosmos DB Explorer, aby zarządzać danymi, dalej możesz:

* Rozpocznij Definiowanie [zapytań](./sql-query-getting-started.md) przy użyciu składni SQL i przeprowadzanie [programowania po stronie serwera](stored-procedures-triggers-udfs.md) za pomocą procedur składowanych, UDF i wyzwalaczy.
