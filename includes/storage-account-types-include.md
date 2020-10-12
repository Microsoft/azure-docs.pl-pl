---
title: plik dołączany
description: plik dołączany
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 726fc9cc0e4eb5a8f9bed5cca8dd4d3df588ac7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79371560"
---
Usługa Azure Storage oferuje kilka typów kont magazynu. Każdy typ obsługuje różne funkcje i ma własny model cen. Te różnice należy wziąć pod uwagę przed utworzeniem konta magazynu w celu określenia typu konta najlepszego dla aplikacji. Oto typy kont magazynu:

- **Konta ogólnego przeznaczenia w wersji 2**: typ konta magazynu podstawowego dla obiektów blob, plików, kolejek i tabel. Zalecane w przypadku większości scenariuszy korzystających z usługi Azure Storage.
- **Konta ogólnego przeznaczenia w wersji 1**: typ konta starsze dla obiektów blob, plików, kolejek i tabel. Zamiast tego używaj kont ogólnego przeznaczenia w wersji 2.
- **Konta BlockBlobStorage**: konta magazynu z charakterystyką wydajności Premium dla blokowych obiektów blob i dołączanie obiektów BLOB. Zalecane w scenariuszach mających wysokie stawki transakcji lub scenariusze, które używają mniejszych obiektów lub wymagają spójnego niskiego opóźnienia magazynu.
- **Konta FileStorage**: tylko pliki magazynu z charakterystyką wydajności Premium. Zalecane w przypadku aplikacji do skalowania w przedsiębiorstwie lub wysokiej wydajności.
- **Konta BlobStorage**: starsze konta magazynu tylko na poziomie obiektów BLOB. Zamiast tego używaj kont ogólnego przeznaczenia w wersji 2.

W poniższej tabeli opisano typy kont magazynu i ich możliwości:

| Typ konta magazynu | Obsługiwane usługi                       | Obsługiwane warstwy wydajności      | Obsługiwane warstwy dostępu         | Opcje replikacji               | Model wdrażania<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Szyfrowanie<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Ogólnego przeznaczenia w wersji 2   | Obiekty blob, pliki, kolejki, tabele, dyski i Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Gorąca, chłodna, archiwalna<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (wersja zapoznawcza), RA-GZRS (wersja zapoznawcza)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Zaszyfrowane              |
| Ogólnego przeznaczenia w wersji 1   | Obiekt BLOB, plik, kolejka, tabela i dysk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Nie dotyczy                            | LRS, GRS, RA-GRS                  | Menedżer zasobów, klasyczny    | Zaszyfrowane              |
| BlockBlobStorage   | Obiekt BLOB (Blokuj obiekty blob i Dołącz tylko obiekty blob) | Premium                       | Nie dotyczy                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Zaszyfrowane              |
| FileStorage   | Tylko plik | Premium                       | Nie dotyczy                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Zaszyfrowane              |
| BlobStorage         | Obiekt BLOB (Blokuj obiekty blob i Dołącz tylko obiekty blob) | Standardowa                      | Gorąca, chłodna, archiwalna<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Zaszyfrowane              |

<div id="deployment-model"><sup>1</sup> Zalecane jest korzystanie z modelu wdrażania Azure Resource Manager. Konta magazynu korzystające z klasycznego modelu wdrażania można nadal tworzyć w niektórych lokalizacjach, a istniejące konta klasyczne są nadal obsługiwane. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager a wdrożenie klasyczne: Omówienie modeli wdrażania i stanu zasobów</a>.</div><br/>

<div id="encryption"><sup>2</sup> Wszystkie konta magazynu są szyfrowane przy użyciu szyfrowanie usługi Storage (SSE) dla danych przechowywanych. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">szyfrowanie usługi Storage platformy Azure dla danych przechowywanych w spoczynku</a>.</div><br/>

<div id="archive"><sup>3</sup> magazyny archiwum i Obsługa warstw na poziomie obiektów BLOB obsługują tylko blokowe obiekty blob. Warstwa archiwum jest dostępna tylko na poziomie pojedynczego obiektu BLOB, a nie na poziomie konta magazynu. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob Storage: warstwy magazynowania gorąca, chłodna i archiwalna</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup> Magazyn strefowo nadmiarowy (ZRS) i geograficznie nadmiarowy magazyn (GZRS/RA-GZRS) (wersja zapoznawcza) są dostępne tylko dla kont standardowych ogólnego przeznaczenia w wersji 2, BlockBlobStorage i FileStorage w określonych regionach. Aby uzyskać więcej informacji na temat opcji nadmiarowości usługi Azure Storage, zobacz <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">nadmiarowość usługi Azure Storage</a>.</div><br/>

<div id="premium-performance"><sup>5</sup> Wydajność warstwy Premium dla kont ogólnego przeznaczenia w wersji 2 i ogólnego przeznaczenia w wersji 1 jest dostępna tylko dla dysków i stron obiektów BLOB. Wydajność warstwy Premium dla obiektów BLOB bloków lub dołączania jest dostępna tylko na kontach BlockBlobStorage. Wydajność warstwy Premium dla plików jest dostępna tylko na kontach FileStorage.</div><br/>

<div id="data-lake-gen2"><sup>6</sup> Azure Data Lake Storage Gen2 to zestaw funkcji przeznaczonych do analizy danych Big Data, opartych na usłudze Azure Blob Storage. Data Lake Storage Gen2 jest obsługiwana tylko na kontach magazynu ogólnego przeznaczenia w wersji 2 z włączoną hierarchiczną przestrzenią nazw. Aby uzyskać więcej informacji na temat Data Lake Storage Gen2, zobacz <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">wprowadzenie do Azure Data Lake Storage Gen2</a>.</div>
