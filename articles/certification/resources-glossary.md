---
title: Słownik programu certyfikowanych urządzeń platformy Azure
description: Lista typowych terminów używanych w programie certyfikowanego urządzenia platformy Azure
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/03/2021
ms.custom: template-concept
ms.openlocfilehash: 7dfdd4a89535621e08db8a8924282febdafd1569
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969386"
---
# <a name="azure-certified-device-program-glossary"></a>Słownik programu certyfikowanych urządzeń platformy Azure

Ten przewodnik zawiera definicje terminów często używanych w programie i portalu certyfikatu platformy Azure. Zapoznaj się z tym słownikiem, aby uzyskać wyjaśnienie procesu certyfikacji. Dla wygody ten słownik jest kategoryzowany na podstawie głównych koncepcji certyfikacji, na które mogą się pojawić pytania.

## <a name="device-class"></a>Klasa urządzenia

Podczas tworzenia projektu certyfikacji zostanie wyświetlony monit o określenie klasy urządzeń. Klasa urządzenia odnosi się do współczynnika lub klasyfikacji formularza, która najlepiej reprezentuje urządzenie.

- **Brama**

    Urządzenie, które przetwarza dane wysyłane za pośrednictwem sieci IoT.

- **Czujnik**

    Urządzenie, które wykrywa zmiany w środowisku i reaguje na nie, a następnie łączy się z bramami, aby przetworzyć zmiany.

- **Inne**

    Jeśli wybierzesz opcję inne, Dodaj opis klasy urządzenia do własnych słów. W miarę upływu czasu możemy nadal dodawać nowe wartości do tej listy, w szczególności w miarę dalszego monitorowania opinii naszych partnerów.

## <a name="device-type"></a>Typ urządzenia

Podczas procesu certyfikacji zostanie również wyświetlony monit o wybranie jednego z dwóch typów urządzeń.

- **Gotowy produkt**

    Urządzenie, które jest gotowe i gotowe do wdrożenia produkcyjnego. Zwykle w gotowym składniku formularza z oprogramowaniem układowym i systemem operacyjnym. Mogą to być urządzenia ogólnego przeznaczenia wymagające dodatkowych dostosowań lub wyspecjalizowanych urządzeń, które nie wymagają żadnych modyfikacji do użycia.
- **Zestaw deweloperski gotowy do rozwiązania**

    Zestaw deweloperski zawierający sprzęt i oprogramowanie idealne do łatwego tworzenia prototypów, zwykle nie jest to gotowy do działania. Zwykle zawiera przykładowy kod i samouczki umożliwiające szybkie tworzenie prototypów.

## <a name="component-type"></a>Typ składnika

W sekcji Szczegóły urządzenia można opisać urządzenie, wyświetlając listę składników według typu składnika. Więcej wskazówek dotyczących składników można znaleźć [tutaj](./how-to-using-the-components-feature.md).

- **Produkt gotowy do sprzedaży**

    Reprezentacja składnika na urządzeniu ogólnym lub podstawowym. Różni się to od **gotowego produktu**, który jest klasyfikacją urządzenia jako gotowego do użycia przez klienta bez konieczności dalszej rozwoju. Gotowy produkt będzie zawierać składnik produkt gotowy do obsługi klienta.
- **Tablica programistyczna**

    Zintegrowana lub odłączona tablica z mikroprocesorem do łatwego dostosowania.
- **Urządzenia**

    Zintegrowane lub odłączalne uzupełnienie produktu (na przykład akcesoriów). Zazwyczaj są to urządzenia, które łączą się z urządzeniem głównym, ale nie przyczyniają się do podstawowych funkcji urządzeń. Zamiast tego udostępnia dodatkowe funkcje. Pamięć, pamięć RAM, magazyn, dyski twarde i procesory CPU nie są uważane za urządzenia peryferyjne (zamiast tego powinny znajdować się na liście dodatkowych specyfikacji składnika produktu gotowego dla klienta).
- **System-on-module**  

    Obwód na poziomie tablicy, który integruje funkcję systemową w pojedynczym module.

## <a name="component-attachment-method"></a>Metoda załącznika składnika

Metoda załącznika składnika jest innym składnikiem, który informuje klienta o sposobie integracji składnika z ogólnym produktem.

- **Integracja**
 
    Odnosi się do sytuacji, gdy składnik urządzenia jest częścią głównej obudowy produktu. Najczęściej dotyczy to typu składnika zewnętrznego, który nie może zostać usunięty z urządzenia.  
    Przykład: zintegrowany czujnik temperatury wewnątrz obudowy bramy.

- **Dyskretnych**

    Odnosi się do sytuacji, gdy składnik **nie** jest częścią głównej obudowy produktu.  
    Przykład: zewnętrzny czujnik temperatury, który musi zostać dołączony do urządzenia.


## <a name="next-steps"></a>Następne kroki

Ten słownik przeprowadzi Cię przez proces certyfikowania projektu w portalu. Wszystko jest teraz gotowe do rozpoczęcia pracy nad projektem.
- [Samouczek: Tworzenie projektu](./tutorial-01-creating-your-project.md)
