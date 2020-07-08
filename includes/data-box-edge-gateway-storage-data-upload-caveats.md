---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67183686"
---
Poniższe zastrzeżenia dotyczą danych w miarę ich przenoszenia na platformę Azure.

- Sugerujemy, aby więcej niż jedno urządzenie nie było zapisywać w tym samym kontenerze.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt BLOB lub plik) w chmurze o takiej samej nazwie jak kopiowany obiekt, urządzenie zastąpi plik w chmurze.
- Pusta hierarchia katalogów (bez plików) utworzonych w obszarze foldery udostępniania nie została przekazana do kontenerów obiektów BLOB.
- Dane można skopiować za pomocą przeciągania i upuszczania w Eksploratorze plików lub za pomocą wiersza polecenia. Jeśli zagregowany rozmiar kopiowanych plików jest większy niż 10 GB, zalecamy użycie programu do kopiowania zbiorczego, takiego jak Robocopy lub rsync. Narzędzia kopiowania zbiorczego ponawiają operację kopiowania w przypadku sporadycznych błędów i zapewniają dodatkową odporność.
- Jeśli udział skojarzony z kontenerem usługi Azure Storage przekazuje obiekty blob, które nie pasują do typu obiektów BLOB zdefiniowanych dla udziału w czasie tworzenia, wówczas takie obiekty blob nie są aktualizowane. Na przykład utworzysz na urządzeniu udział blokowy obiekt BLOB. Skojarz udział z istniejącym kontenerem chmury, który ma stronicowe obiekty blob. Odśwież ten udział, aby pobrać pliki. Zmodyfikuj niektóre odświeżone pliki, które są już przechowywane jako stronicowe obiekty blob w chmurze. Zobaczysz błędy przekazywania.
