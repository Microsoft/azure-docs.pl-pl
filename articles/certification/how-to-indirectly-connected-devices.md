---
title: Pakiety urządzeń Certifing i bezpośrednio połączone urządzenia
titleSuffix: Azure Certified
description: Zapoznaj się z artykułem Jak przesłać bezpośrednio połączone urządzenie w celu certyfikacji.
author: cbroad
ms.author: cbroad
ms.date: 02/23/2021
ms.topic: how-to
ms.service: certification
ms.openlocfilehash: 3a4fd2838c0ddf6d7d03d68f105fc59471b77dea
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304481"
---
# <a name="device-bundles-and-indirectly-connected-devices"></a>Pakiety urządzeń i bezpośrednio połączone urządzenia

Aby obsłużyć urządzenia, które współpracują z platformą Azure za pomocą oferowanych przez urządzenie, SaaS lub PaaS, naszego portalu do tworzenia i https://certify.azure.com/) wykazu urządzeń (należy https://devicecatalog.azure.com) włączyć koncepcje związane z przydzieleniem i włączeniem tych połączeń do naszego programu Device Certified platformy Azure.

W zależności od oferowanej linii i usług w danej sytuacji może być wymagana kombinacja następujących kroków:


![Utwórz zależności projektu](./media/indirect-connected-device/picture-1.png )
## <a name="sensors-and-indirect-devices"></a>Czujniki i urządzenia pośrednie
Wiele czujników wymaga, aby urządzenie łączyło się z platformą Azure. Ponadto może istnieć wiele zgodnych urządzeń, które będą współpracować z urządzeniem czujnika. **Aby uwzględnić te scenariusze, najpierw należy zatwierdzić urządzenia przed zatwierdzeniem czujnika, który przekaże informacje.**

Przykład macierzy przesłanej przykładowego ![ przesłania kombinacji](./media/indirect-connected-device/picture-2.png )

Aby przeprowadzić certyfikację czujnika, który wymaga oddzielnego urządzenia:
1.  Najpierw [Zatwierdź urządzenie](https://certify.azure.com) i Opublikuj je w katalogu certyfikowanych urządzeń platformy Azure
    - Jeśli masz wiele zgodnych urządzeń przekazujących (jak w powyższym przykładzie), prześlij je oddzielnie do certyfikacji i Opublikuj również w wykazie
2.  Za pomocą czujnika połączonego z urządzeniem Prześlij czujnik do certyfikacji
    * Na karcie "zależności&quot; w sekcji &quot;Szczegóły urządzenia&quot; Ustaw następujące wartości:
        * Typ zależności = &quot;Brama sprzętowa&quot;
        * Adres URL zależności = &quot;link adresu URL do urządzenia w wykazie urządzeń&quot;
        * Używane podczas testowania = &quot;tak&quot;
        * Dodaj komentarze dotyczące klienta, które powinny być udostępniane użytkownikowi, który widzi Opis produktu w katalogu urządzeń. (przykład: &quot;w przypadku urządzeń z serii 100 są wymagane do łączenia się z platformą Azure")

3.  Jeśli masz więcej urządzeń, które chcesz dodać jako opcjonalne dla tego urządzenia, możesz wybrać opcję "+ Dodaj dodatkową zależność". Następnie postępuj zgodnie z tymi samymi wskazówkami i pamiętaj, że nie były używane podczas testowania. W komentarzach przeznaczonych dla klienta upewnij się, że klienci mają świadomość, że inne urządzenia są skojarzone z tym czujnikiem (jako alternatywę dla urządzenia, które było używane podczas testowania).

![Tekst alternatywny](./media/indirect-connected-device/picture-3.png "Typ zależności sprzętowej")

## <a name="paas-and-saas-offerings"></a>Oferty PaaS i SaaS
W ramach portfolio produktu mogą znajdować się certyfikowane urządzenia, ale urządzenie wymaga również innych usług od firmy lub innych firm. Aby dodać tę zależność, wykonaj następujące kroki:
1. Rozpocznij proces wysyłania dla urządzenia
2. Na karcie "zależności" Ustaw następujące wartości
    - Typ zależności = "usługa oprogramowania"
    - Nazwa usługi = "[Nazwa produktu]"
    - Adres URL zależności = "URL linku do strony produktu opisującej usługę"
    - Dodaj wszelkie komentarze dotyczące klienta, które powinny być udostępniane użytkownikowi, który widzi Opis produktu w wykazie certyfikowanych urządzeń platformy Azure
3. Jeśli używasz innych programów, usług lub zależności sprzętowych, które chcesz dodać jako opcjonalne dla tego urządzenia, możesz wybrać opcję "+ Dodaj dodatkową zależność" i postępować zgodnie z tymi samymi wskazówkami.

![Typ zależności oprogramowania](./media/indirect-connected-device/picture-4.png )

## <a name="bundled-products"></a>Powiązane produkty
Powiązane z produktem oferty są po prostu pomyślnym zaświadczeniem urządzenia z innymi składnikami, które zostaną sprzedane jako część pakietu w jednej liście produktów. Można przesłać urządzenie, które obejmuje dodatkowe składniki, takie jak czujnik temperatury i czujnik aparatu (#1) lub można przesłać czujnik dotykowy zawierający urządzenie przekazujące (#2). Za pomocą funkcji "składnik" można dodawać wiele składników do listy.

Jeśli zamierzasz to zrobić, sformatuj obraz z listą produktów, aby wskazać, że ten produkt zawiera inne składniki.  Ponadto, jeśli pakiet wymaga zaświadczania dodatkowych usług, należy zidentyfikować je za pomocą zależności usług.
Przykładowa macierz powiązanych produktów

![Przykład przesłania pakietu](./media/indirect-connected-device/picture-5.png )

Aby uzyskać bardziej szczegółowy opis sposobu korzystania z funkcji składników w portalu certyfikowanych urządzeń platformy Azure, zapoznaj się z [dokumentacją pomocy](./how-to-using-the-components-feature.md). 

Jeśli urządzenie jest urządzeniem przekazującym z oddzielnym czujnikiem w tym samym produkcie, należy utworzyć jeden składnik, aby odzwierciedlić urządzenie przekazujące i inny składnik do odzwierciedlenia czujnika. Składniki można dodać do projektu na karcie Szczegóły produktu w sekcji Szczegóły urządzenia:

![Dodawanie składników](./media/indirect-connected-device/picture-6.png )

Dla urządzenia przekazującego Ustaw typ składnika jako produkt gotowy do klienta i wypełnij pozostałe pola odpowiednie dla danego produktu. Przykład:

![Szczegóły składnika](./media/indirect-connected-device/picture-7.png )

W przypadku czujnika Dodaj drugi składnik, ustawiając typ składnika jako metody peryferyjnej i załącznika jako dyskretny. Przykład:

![Szczegóły drugiego składnika](./media/indirect-connected-device/picture-8.png )

Po utworzeniu składnika czujnika Edytuj szczegóły, przejdź do karty czujniki, a następnie Dodaj szczegóły czujnika. Przykład:

![Szczegóły czujnika](./media/indirect-connected-device/picture-9.png )

Wypełnij szczegóły swoich projektów i prześlij urządzenie pod kątem certyfikacji jako normalne.

