---
title: Opcje usługi Azure Data Transfer dla małych zestawów danych z niską przepustowością sieci | Microsoft Docs
description: Dowiedz się, jak wybrać rozwiązanie platformy Azure na potrzeby transferu danych, gdy masz niską przepustowość sieci w środowisku i planujesz transfer małych zestawów danych.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: f59d1e297ba4d7607d7abd07a78da4784f55d20f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023231"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Transfer małych zestawów danych w przypadku niskiej lub umiarkowanej przepustowości sieci
 
Ten artykuł zawiera omówienie rozwiązań transferu danych w przypadku niskiej do umiarkowanej przepustowości sieci w środowisku i planowania przenoszenia małych zestawów danych. W tym scenariuszu opisano również zalecane opcje transferu danych oraz odpowiednie kluczowe klasy macierzy.

Aby zapoznać się z omówieniem wszystkich dostępnych opcji transferu danych, przejdź do pozycji [Wybierz rozwiązanie do transferu danych Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Opis scenariusza

Małe zestawy danych oznaczają zestawy o rozmiarze od kilku gigabajtów do kilku terabajtów. Niska lub średnia przepustowość sieci oznacza przepustowość od 45 Mb/s (połączenie T3 w centrum danych) do 1 Gb/s.

- W przypadku transferu tylko kilku plików i nie trzeba automatyzować transferu danych, należy rozważyć narzędzia z interfejsem graficznym.
- Jeśli masz doświadczenie w administrowaniu systemami, rozważ narzędzia wiersza polecenia albo narzędzia programistyczne lub do obsługi skryptów.

## <a name="recommended-options"></a>Zalecane opcje

W tym scenariuszu zalecane są następujące opcje:

- **Narzędzia interfejsu graficznego** , takie jak Eksplorator usługi Azure Storage i usługa Azure Storage w Azure Portal. Zapewniają one łatwy sposób wyświetlania danych i szybkiego przesyłania kilku plików.

    - **Eksplorator usługi Azure Storage** — to narzędzie Międzyplatformowe umożliwia zarządzanie zawartością kont usługi Azure Storage. Umożliwia przekazywanie i pobieranie obiektów blob, plików, kolejek, tabel i jednostek bazy danych Azure Cosmos DB, a także zarządzanie nimi. Użyj go w usłudze Blob Storage, aby zarządzać obiektami blob i folderami oraz przekazywać i pobierać obiekty blob pomiędzy lokalnym systemem plików a usługą Blob Storage lub pomiędzy kontami magazynu.
    - **Azure Portal** — usługa Azure Storage w Azure Portal udostępnia interfejs oparty na sieci Web do eksplorowania plików i przekazywania nowych plików pojedynczo. Jest to dobra opcja do szybkiego eksplorowania plików, jeśli nie chcesz instalować żadnych narzędzi lub wydawać poleceń, albo gdy chcesz łatwo przekazać kilka nowych plików.

- **Skrypty/narzędzia programistyczne** , takie jak AzCopy/PowerShell/interfejs wiersza polecenia platformy Azure i interfejsy API REST usługi Azure Storage.

    - **AzCopy** — za pomocą tego narzędzia wiersza polecenia można łatwo kopiować dane do i z obiektów blob platformy Azure, plików i magazynu tabel z optymalną wydajnością. Narzędzie AzCopy obsługuje współbieżność i równoległość oraz umożliwia wznawianie operacji kopiowania, gdy zostaną przerwane.
    - **Azure PowerShell** — w przypadku użytkowników mających doświadczenie z administracją systemu należy użyć modułu usługi Azure Storage w usłudze Azure PowerShell do transferowania danych.
    - **Interfejs wiersza polecenia platformy Azure** — używanie tego narzędzia dla wielu platform do zarządzania usługami platformy Azure i przekazywania danych do usługi Azure Storage.
    - **Interfejsy API REST usługi Azure Storage/zestawy SDK** — podczas kompilowania aplikacji można opracowywać aplikację pod kątem interfejsów API REST usługi Azure Storage/zestawów SDK i korzystać z bibliotek klienckich platformy Azure oferowanych w wielu językach.


## <a name="comparison-of-key-capabilities"></a>Porównanie kluczowych możliwości

W poniższej tabeli podsumowano różnice w zakresie kluczowych funkcji.

| Cechy | Eksplorator usługi Azure Storage | Azure Portal | AzCopy<br>Azure PowerShell<br>Interfejs wiersza polecenia platformy Azure | Interfejsy API REST usługi Azure Storage lub zestawy SDK |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Dostępność | Pobieranie i instalowanie <br>Autonomiczne narzędzie | Narzędzia eksploracji oparte na sieci Web w Azure Portal | Narzędzie wiersza polecenia |Interfejsy programowalne w językach .NET, Java, Python, JavaScript, C++, go, Ruby i PHP |
| Interfejs graficzny | Tak | Tak | Nie | Nie |
| Obsługiwane platformy | Windows, Mac, Linux | Oparte na sieci Web |Windows, Mac, Linux |Wszystkie platformy |
| Dozwolone operacje magazynu obiektów BLOB<br>dla obiektów blob i folderów | Przekazywanie<br>Pobierz<br>Zarządzanie | Przekazywanie<br>Pobierz<br>Zarządzanie |Przekazywanie<br>Pobierz<br>Zarządzanie | Tak, dostosowywalne |
| Dozwolony Data Lake magazyn Gen1<br>operacje dotyczące plików i folderów | Przekazywanie<br>Pobierz<br>Zarządzanie | Nie |Przekazywanie<br>Pobierz<br>Zarządzanie                   | Nie |
| Dozwolone operacje magazynowania plików<br>dla plików i katalogów | Przekazywanie<br>Pobierz<br>Zarządzanie | Przekazywanie<br>Pobierz<br>Zarządzanie   |Przekazywanie<br>Pobierz<br>Zarządzanie | Tak, dostosowywalne |
| Dozwolone operacje magazynu tabel<br>dla tabel |Zarządzanie | Nie |Obsługa tabel w programie AzCopy wersji 7 |Tak, dostosowywalne|
| Dozwolony magazyn kolejek | Zarządzanie | Nie  |Nie | Tak, można dostosowywać|


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [transferować dane za pomocą Eksplorator usługi Azure Storage](../../machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer.md).
- [Transferowanie danych za pomocą narzędzia AzCopy](./storage-use-azcopy-v10.md)