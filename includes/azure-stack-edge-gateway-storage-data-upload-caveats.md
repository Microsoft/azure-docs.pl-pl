---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dc18efe03cbc8a3f657ae4afc781941e8e76611c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467613"
---
Poniższe zastrzeżenia dotyczą danych w miarę ich przenoszenia na platformę Azure.

- Sugerujemy, aby więcej niż jedno urządzenie nie było zapisywać w tym samym kontenerze.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt BLOB lub plik) w chmurze o takiej samej nazwie jak kopiowany obiekt, urządzenie zastąpi plik w chmurze.
- Pusta hierarchia katalogów (bez plików) utworzonych w obszarze foldery udostępniania nie została przekazana do kontenerów obiektów BLOB.
- Dane można skopiować metodą przeciągania i upuszczania w Eksploratorze plików lub za pośrednictwem wiersza polecenia. Jeśli zagregowany rozmiar kopiowanych plików przekracza 10 GB, zalecamy użycie programu do kopiowania zbiorczego, takiego jak Robocopy lub rsync. Narzędzia do kopiowania zbiorczego ponawiają operację kopiowania w przypadku sporadycznych błędów i zapewniają dodatkową odporność. Jeśli używasz usługi BLOB Storage za pośrednictwem REST, AzCopy lub Eksplorator usługi Azure Storage mogą być używane.
- Jeśli udział skojarzony z kontenerem usługi Azure Storage przekazuje obiekty blob, które nie pasują do typu obiektów BLOB zdefiniowanych dla udziału w czasie tworzenia, wówczas takie obiekty blob nie są aktualizowane. Na przykład utwórz na urządzeniu udział blokowych obiektów blob. Skojarz udział z istniejącym kontenerem chmury zawierającym stronicowe obiekty blob. Odśwież ten udział, aby pobrać pliki. Zmodyfikuj niektóre odświeżone pliki, które są już przechowywane jako stronicowe obiekty blob w chmurze. Zostaną wyświetlone błędy przekazywania.
- Po utworzeniu pliku w udziałach zmiana nazwy pliku nie jest obsługiwana.
- Usunięcie pliku z udziału nie powoduje usunięcia wpisu na koncie magazynu.
- W przypadku używania rsync do kopiowania danych, `rsync -a` opcja nie jest obsługiwana.
