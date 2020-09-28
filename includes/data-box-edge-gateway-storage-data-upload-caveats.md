---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 6dc201af2271909de15af9bac1a2e2bb68faed1a
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91401010"
---
Poniższe zastrzeżenia dotyczą danych w miarę ich przenoszenia na platformę Azure.

- Sugerujemy, aby więcej niż jedno urządzenie nie było zapisywać w tym samym kontenerze.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt BLOB lub plik) w chmurze o takiej samej nazwie jak kopiowany obiekt, urządzenie zastąpi plik w chmurze.
- Pusta hierarchia katalogów (bez plików) utworzonych w obszarze foldery udostępniania nie została przekazana do kontenerów obiektów BLOB.
- Dane można skopiować za pomocą przeciągania i upuszczania w Eksploratorze plików lub za pomocą wiersza polecenia. Jeśli zagregowany rozmiar kopiowanych plików jest większy niż 10 GB, zalecamy użycie programu do kopiowania zbiorczego, takiego jak Robocopy lub rsync. Narzędzia kopiowania zbiorczego ponawiają operację kopiowania w przypadku sporadycznych błędów i zapewniają dodatkową odporność.
- Jeśli udział skojarzony z kontenerem usługi Azure Storage przekazuje obiekty blob, które nie pasują do typu obiektów BLOB zdefiniowanych dla udziału w czasie tworzenia, wówczas takie obiekty blob nie są aktualizowane. Na przykład utworzysz na urządzeniu udział blokowy obiekt BLOB. Skojarz udział z istniejącym kontenerem chmury, który ma stronicowe obiekty blob. Odśwież ten udział, aby pobrać pliki. Zmodyfikuj niektóre odświeżone pliki, które są już przechowywane jako stronicowe obiekty blob w chmurze. Zobaczysz błędy przekazywania.
- Po utworzeniu pliku w udziałach zmiana nazwy pliku nie jest obsługiwana.
- Usunięcie pliku z udziału nie powoduje usunięcia wpisu na koncie magazynu.
- W przypadku używania rsync do kopiowania danych, `rsync -a` opcja nie jest obsługiwana.

