---
title: Omówienie zestawów zasobów
description: W tym artykule opisano zestawy zasobów i sposób ich tworzenia przez usługę Azure kontrolą.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 55efa9443fd59b66a7677c9c460e473715f201df
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553987"
---
# <a name="understanding-resource-sets"></a>Omówienie zestawów zasobów

Ten artykuł pomaga zrozumieć, w jaki sposób usługa Azure kontrolą korzysta z zestawów zasobów do mapowania zasobów danych na zasoby logiczne.

## <a name="background-info"></a>Informacje ogólne

Systemy przetwarzania danych w skali zwykle przechowują pojedynczą tabelę na dysku jako wiele plików. Pojęcie to jest reprezentowane w usłudze Azure kontrolą za pomocą zestawów zasobów. Zestaw zasobów jest pojedynczym obiektem w katalogu, który reprezentuje dużą liczbę zasobów w magazynie.

Załóżmy na przykład, że klaster Spark spowodował trwałą ramkę danych w ADLS Gen2m. Chociaż w platformie Spark, tabela wygląda jak pojedynczy zasób logiczny, na dysku prawdopodobnie tysiące plików Parquet, z których każdy reprezentuje partycję całkowitej zawartości ramki danych. Dane dotyczące danych IoT i dzienników sieci Web są takie same. Wyobraź sobie, że masz czujnik, który wyprowadza pliki dzienników kilka razy na sekundę. Nie zajmie to nawet setek tysięcy plików dziennika z tego samego czujnika.

Aby rozwiązać problem związany z mapowaniem dużej liczby zasobów danych do pojedynczego zasobu logicznego, usługa Azure kontrolą używa zestawów zasobów.

## <a name="how-azure-purview-detects-resource-sets"></a>Jak usługa Azure kontrolą wykrywa zestawy zasobów

Usługa Azure kontrolą obsługuje wykrywanie zestawów zasobów tylko w obiektach Blob platformy Azure, ADLS Gen1 i ADLS Gen2.

Usługa Azure kontrolą automatycznie wykrywa zestawy zasobów przy użyciu funkcji o nazwie zautomatyzowane odnajdywanie zestawu zasobów. Ta funkcja sprawdza wszystkie dane, które są pozyskiwane za pośrednictwem skanowania i porównuje je z zestawem zdefiniowanych wzorców.

Załóżmy na przykład, że przeskanujesz źródło danych, którego adres URL to `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Usługa Azure kontrolą analizuje segmenty ścieżki i określa, czy są one zgodne ze wszystkimi wbudowanymi wzorcami. Ma wbudowane wzorce dla identyfikatorów GUID, liczb, formatów dat, kodów lokalizacji (na przykład en-US) i tak dalej. W takim przypadku wzorzec liczby pasuje do *23*. Usługa Azure kontrolą zakłada, że ten plik jest częścią zestawu zasobów o nazwie `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Lub, w przypadku adresu URL `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` , na przykład, usługa Azure kontrolą dopasowuje wzorzec lokalizacji i wzorzec numeru, generując zestaw zasobów o nazwie `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Korzystając z tej strategii, usługa Azure kontrolą mapuje następujące zasoby na ten sam zestaw zasobów `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

> [!Note]
> Usługa Azure Data Lake Storage Gen2 jest teraz ogólnie dostępna. Zalecamy, aby zacząć z niej korzystać już dziś. Aby uzyskać więcej informacji, zobacz [stronę produktu](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Typy plików, które usługa Azure kontrolą nie będzie wykrywać jako zestawów zasobów

Kontrolą celowo nie próbuje sklasyfikować większości typów plików dokumentów, takich jak Word, Excel lub PDF jako zestawy zasobów. Wyjątkiem są CSV, ponieważ jest to typowy format partycjonowanego pliku.

## <a name="how-azure-purview-scans-resource-sets"></a>Jak usługa Azure kontrolą skanuje zestawy zasobów

Gdy usługa Azure kontrolą wykrywa zasoby, które są przez nie uważane są częścią zestawu zasobów, przełącza z pełnego skanowania do przykładowego skanowania. W przypadku przykładowego skanowania zostanie otwarty tylko podzbiór plików, które uzna za odpowiednie, w zestawie zasobów. Dla każdego pliku, który jest otwarty, używa jego schematu i uruchamia jego klasyfikatory. Usługa Azure kontrolą następnie znajdzie najnowszy zasób spośród otwartych zasobów i użyje schematu i klasyfikacji tego zasobu we wpisie dla całego zestawu zasobów w katalogu.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Co są przechowywane w usłudze Azure kontrolą na temat zestawów zasobów

Oprócz pojedynczych schematów i klasyfikacji usługa Azure kontrolą przechowuje następujące informacje dotyczące zestawów zasobów:

- Dane z najnowszych zasobów partycji, które zostały głęboko przeskanowane.
- Agregowanie informacji o zasobach partycji, które tworzą zestaw zasobów.
- Liczba partycji, która pokazuje liczbę znalezionych zasobów partycji.
- Liczba schematów, która pokazuje, ile unikatowych schematów znajduje się w zestawie próbek, na których zawarto głębokie skanowania. Ta wartość jest liczbą z zakresu od 1 do 5 lub dla wartości większych niż 5, 5 +.
- Lista typów partycji, gdy w zestawie zasobów znajduje się więcej niż jeden typ partycji. Na przykład czujnik IoT może wyprowadzać zarówno pliki XML, jak i JSON, chociaż obie są logicznie częścią tego samego zestawu zasobów.

## <a name="built-in-resource-set-patterns"></a>Wbudowane wzorce zestawu zasobów

Usługa Azure kontrolą obsługuje następujące wzorce zestawu zasobów. Wzorce te mogą pojawić się jako nazwa w katalogu lub jako część nazwy pliku.

| Nazwa wzorca | Nazwa wyświetlana | Opis |
|--------------|--------------|-------------|
| GUID         | IDENT       | Unikatowy identyfikator globalny, zgodnie z definicją w [dokumencie RFC 4122](https://tools.ietf.org/html/rfc4122). |
| Liczba       | Azotan          | Co najmniej jedna cyfra. |
| Formaty daty i godziny | Azotan     | Usługa Azure kontrolą obsługuje różne rodzaje formatów daty i godziny, ale wszystkie są skracane do serii {N} s. |
| 4ByteHex     | SZESNASTKOWY        | Czterocyfrowy numer szesnastkowy. |
| Lokalizacja | Loc        | Tag języka, zgodnie z definicją w [BCP 47](https://tools.ietf.org/html/bcp47). Usługa Azure kontrolą obsługuje tagi, które zawierają łącznik (-) lub podkreślenie (_). Na przykład en_ca i en-ca. |

## <a name="issues-with-resource-sets"></a>Problemy z zestawami zasobów

Chociaż zestawy zasobów działają dobrze w większości przypadków, mogą wystąpić następujące problemy, w których usługa Azure kontrolą:

- Nieprawidłowe oznaczenie elementu zawartości jako zestawu zasobów
- Umieszcza element zawartości w niewłaściwym zestawie zasobów
- Nieprawidłowe oznaczenie elementu zawartości jako niebędącego zestawem zasobów

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Data Catalog, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).
