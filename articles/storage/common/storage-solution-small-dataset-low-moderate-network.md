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
ms.openlocfilehash: 4f21e7f64338b7d50ca401081bf73ca0c1a1c88f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85504308"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Transfer małych zestawów danych w przypadku niskiej lub umiarkowanej przepustowości sieci
 
Ten artykuł zawiera omówienie rozwiązań transferu danych w przypadku niskiej do umiarkowanej przepustowości sieci w środowisku i planowania przenoszenia małych zestawów danych. W tym scenariuszu opisano również zalecane opcje transferu danych oraz odpowiednie kluczowe klasy macierzy.

Aby zapoznać się z omówieniem wszystkich dostępnych opcji transferu danych, przejdź do pozycji [Wybierz rozwiązanie do transferu danych Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Opis scenariusza

Małe zestawy danych odnoszą się do rozmiarów, w kolejności od GB do kilku usług TBs. Niska do umiarkowana przepustowość sieci oznacza 45 MB/s (połączenie T3 w centrum danych) do 1 GB/s.

- W przypadku transferu tylko kilku plików i nie trzeba automatyzować transferu danych, należy rozważyć narzędzia z interfejsem graficznym.
- Jeśli masz doświadczenie z administracją systemu, rozważ użycie wiersza polecenia lub narzędzi programistycznych/skryptowych.

## <a name="recommended-options"></a>Zalecane opcje

W tym scenariuszu zalecane są następujące opcje:

- **Narzędzia interfejsu graficznego** , takie jak Eksplorator usługi Azure Storage i usługa Azure Storage w Azure Portal. Zapewniają one łatwy sposób wyświetlania danych i szybkiego przesyłania kilku plików.

    - **Eksplorator usługi Azure Storage** — to narzędzie Międzyplatformowe umożliwia zarządzanie zawartością kont usługi Azure Storage. Umożliwia przekazywanie i pobieranie obiektów blob, plików, kolejek, tabel i Azure Cosmos DB jednostek oraz zarządzanie nimi. Służy do zarządzania obiektami BLOB i folderami przy użyciu magazynu obiektów blob, a także do przekazywania i pobierania obiektów BLOB między lokalnym systemem plików i magazynem obiektów blob lub między kontami magazynu.
    - **Azure Portal** — usługa Azure Storage w Azure Portal udostępnia interfejs oparty na sieci Web do eksplorowania plików i przekazywania nowych plików pojedynczo. Jest to dobra opcja, jeśli nie chcesz instalować żadnych narzędzi ani wydawać poleceń, aby szybko eksplorować pliki, lub po prostu Przekaż kilku nowe.

- **Skrypty/narzędzia programistyczne** , takie jak AzCopy/PowerShell/interfejs wiersza polecenia platformy Azure i interfejsy API REST usługi Azure Storage.

    - **AzCopy** — za pomocą tego narzędzia wiersza polecenia można łatwo kopiować dane do i z obiektów blob platformy Azure, plików i magazynu tabel z optymalną wydajnością. AzCopy obsługuje współbieżność i równoległość oraz możliwość wznowienia operacji kopiowania po przerwaniu.
    - **Azure PowerShell** — w przypadku użytkowników mających doświadczenie z administracją systemu należy użyć modułu usługi Azure Storage w usłudze Azure PowerShell do transferowania danych.
    - **Interfejs wiersza polecenia platformy Azure** — używanie tego narzędzia dla wielu platform do zarządzania usługami platformy Azure i przekazywania danych do usługi Azure Storage.
    - **Interfejsy API REST usługi Azure Storage/zestawy SDK** — podczas kompilowania aplikacji można opracowywać aplikację pod kątem interfejsów API REST usługi Azure Storage/zestawów SDK i korzystać z bibliotek klienckich platformy Azure oferowanych w wielu językach.


## <a name="comparison-of-key-capabilities"></a>Porównanie kluczowych możliwości

Poniższa tabela zawiera podsumowanie różnic między kluczowymi funkcjami.

| Cecha | Eksplorator usługi Azure Storage | Azure Portal | Narzędzie AzCopy<br>Azure PowerShell<br>Interfejs wiersza polecenia platformy Azure | Interfejsy API REST usługi Azure Storage lub zestawy SDK |
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

- Dowiedz się, jak [transferować dane za pomocą Eksplorator usługi Azure Storage](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Transferowanie danych za pomocą narzędzia AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

