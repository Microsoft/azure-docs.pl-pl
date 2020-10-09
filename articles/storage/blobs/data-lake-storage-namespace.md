---
title: Azure Data Lake Storage Gen2 hierarchiczna przestrzeń nazw
description: Opisuje koncepcję hierarchicznej przestrzeni nazw dla Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6ce94590a1d0de6941c27d972bdd1c4194080e95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77153081"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 hierarchiczna przestrzeń nazw

Kluczowym mechanizmem umożliwiającym Azure Data Lake Storage Gen2 zapewnianie wydajności systemu plików przy skali i cenach magazynu obiektów jest dodanie **hierarchicznej przestrzeni nazw**. Pozwala to na organizowanie kolekcji obiektów/plików w ramach konta w hierarchię katalogów i zagnieżdżonych podkatalogów w taki sam sposób, w jaki system plików na komputerze jest zorganizowany. Po włączeniu hierarchicznej przestrzeni nazw konto magazynu jest w stanie zapewnić skalowalność i efektywność kosztową magazynu obiektów przy użyciu semantyki systemu plików, które są znane do silników i struktur analizy.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Zalety hierarchicznej przestrzeni nazw

Następujące korzyści są skojarzone z systemami plików, które implementują hierarchiczną przestrzeń nazw za pośrednictwem danych obiektów blob:

- **Manipulowanie katalogami niepodzielnymi:** Obiekt przechowuje przybliżoną hierarchię katalogów, przyjmując Konwencję osadzania ukośników (/) w nazwie obiektu, aby zauważyć segmenty ścieżki. Chociaż ta konwencja działa w przypadku organizowania obiektów, Konwencja nie zapewnia pomocy dla akcji takich jak przeniesienie, zmiana nazwy lub usunięcie katalogów. W przypadku braku katalogów aplikacje muszą przetwarzać potencjalnie miliony pojedynczych obiektów blob, aby osiągnąć zadania na poziomie katalogu. Z kolei hierarchiczna przestrzeń nazw przetwarza te zadania przez aktualizację pojedynczego wpisu (katalogu nadrzędnego).

    Ta znacząca Optymalizacja jest szczególnie istotna dla wielu struktur analizy danych Big Data. Narzędzia, takie jak Hive, Spark itp., często zapisują dane wyjściowe do lokalizacji tymczasowych, a następnie zmieniają nazwę lokalizacji na końcu zadania. Bez hierarchicznej przestrzeni nazw ta zmiana nazwy może często trwać dłużej niż proces analizy. Mniejsze opóźnienie zadania to niższy całkowity koszt posiadania (TCO) dla obciążeń analitycznych.

- **Przyjazny styl interfejsu:** Systemy plików są dobrze zrozumiałe dla programistów i użytkowników. Nie ma potrzeby uczenia się nowego modelu magazynu podczas przechodzenia do chmury jako interfejs systemu plików uwidoczniony przez Data Lake Storage Gen2 jest to ten sam model używany przez komputery, duże i małe.

Jednym z powodów, w których magazyny obiektów nie są w przeszłości obsługiwane w hierarchicznej przestrzeni nazw, jest skalowanie hierarchicznej przestrzeni nazw. Niemniej jednak Data Lake Storage Gen2 hierarchiczną przestrzeń nazw skaluje się liniowo i nie obniży wydajności danych.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Decydowanie o tym, czy włączyć hierarchiczną przestrzeń nazw

Po włączeniu hierarchicznej przestrzeni nazw na Twoim koncie nie można przywrócić jej z powrotem do płaskiej przestrzeni nazw. Z tego względu należy rozważyć, czy warto włączyć hierarchiczną przestrzeń nazw na podstawie charakteru obciążeń magazynu obiektów.

Niektóre obciążenia mogą nie przynieść korzyści przez włączenie hierarchicznej przestrzeni nazw. Przykładami mogą być kopie zapasowe, magazyn obrazów i inne aplikacje, w których organizacja obiektów jest przechowywana niezależnie od samych obiektów (na przykład w oddzielnej bazie danych). 

Mimo że obsługa funkcji magazynu obiektów blob i ekosystem usługi platformy Azure nadal rośnie, nadal istnieją pewne funkcje i usługi platformy Azure, które nie są jeszcze obsługiwane na kontach z hierarchiczną przestrzenią nazw. Zobacz [znane problemy](data-lake-storage-known-issues.md). 

Ogólnie rzecz biorąc zalecamy włączenie hierarchicznej przestrzeni nazw dla obciążeń magazynu przeznaczonych dla systemów plików, które manipulują katalogami. Obejmuje to wszystkie obciążenia, które są głównie przeznaczone do przetwarzania analiz. Zestawy danych, które wymagają wysokiego stopnia organizacji, mogą również umożliwić włączenie hierarchicznej przestrzeni nazw.

Przyczyny włączenia hierarchicznej przestrzeni nazw są określane przez analizę całkowitego kosztu posiadania. Ogólnie mówiąc, ulepszenia opóźnienia obciążenia ze względu na przyspieszenie magazynu wymagają zasobów obliczeniowych w krótszym czasie. Opóźnienie wielu obciążeń może być ulepszone ze względu na manipulowanie katalogiem, który jest włączony przez hierarchiczną przestrzeń nazw. W wielu obciążeniach zasób obliczeniowy reprezentuje > 85% łącznego kosztu, a więc nawet nieznacznie zmniejszanie opóźnień obciążenia jest równe znacznej ilości oszczędności kosztów. Nawet w przypadkach, gdy włączenie hierarchicznej przestrzeni nazw zwiększa koszty magazynowania, całkowity koszt posiadania jest nadal obniżony ze względu na zmniejszenie kosztów obliczeniowych.

Aby analizować różnice w cenach magazynu danych, cenach transakcji i cenach rezerwacji pojemności magazynu między kontami, które mają płaską hierarchiczną przestrzeń nazw, a hierarchiczną przestrzenią nazw, zobacz [Azure Data Lake Storage Gen2 Cennik](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie konta magazynu](./data-lake-storage-quickstart-create-account.md)
