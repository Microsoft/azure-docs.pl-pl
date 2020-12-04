---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 385ebffb4780543b532c81b89f1847f5c84cd0ac
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582663"
---
Poniższe zastrzeżenia dotyczą danych w miarę ich przenoszenia na platformę Azure.

- Sugerujemy, aby więcej niż jedno urządzenie nie było zapisywać w tym samym kontenerze.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt BLOB lub plik) w chmurze o takiej samej nazwie jak kopiowany obiekt, urządzenie zastąpi plik w chmurze.
- Pusta hierarchia katalogów (bez plików) utworzonych w obszarze foldery udostępniania nie została przekazana do kontenerów obiektów BLOB.
- Dane można skopiować za pomocą przeciągania i upuszczania w Eksploratorze plików lub za pomocą wiersza polecenia. Jeśli zagregowany rozmiar kopiowanych plików jest większy niż 10 GB, zalecamy użycie programu do kopiowania zbiorczego, takiego jak `Robocopy` lub `rsync` . Narzędzia do kopiowania zbiorczego próbują wykonać operację kopiowania dla sporadycznych błędów i zapewnić dodatkową odporność.
- Jeśli udział skojarzony z kontenerem usługi Azure Storage przekazuje obiekty blob, które nie pasują do typu obiektów BLOB zdefiniowanych dla udziału w czasie tworzenia, wówczas takie obiekty blob nie są aktualizowane. Jeśli na przykład utworzysz udział blokowych obiektów BLOB na urządzeniu, skojarz go z istniejącym kontenerem w chmurze zawierającym stronicowe obiekty blob, Odśwież ten udział w celu pobrania plików, a następnie zmodyfikuj niektóre odświeżone pliki, które są już przechowywane jako stronicowe obiekty blob w chmurze, zobaczysz błędy przekazywania.
- Po utworzeniu pliku w udziałach zmiana nazwy pliku nie jest obsługiwana.
- Usunięcie pliku z udziału nie powoduje usunięcia wpisu na koncie magazynu.
- W przypadku kopiowania danych za pomocą narzędzia `rsync` opcja `rsync -a` nie jest obsługiwana.