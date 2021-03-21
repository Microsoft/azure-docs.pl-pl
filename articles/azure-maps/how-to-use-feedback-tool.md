---
title: Podaj dane zwrotne do Azure Maps | Mapy Microsoft Azure
description: Prześlij opinię na temat danych za pomocą narzędzia do Microsoft Azure Maps — Opinie.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4be775d8a6a8c476bcc659a902c7f43c12a6d6d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905336"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Podaj dane zwrotne do Azure Maps

Azure Maps jest dostępna od czasu do 2018. Azure Maps udostępniał dane z danych dotyczących mapy, łatwe do użycia interfejsy API REST i zaawansowane zestawy SDK do obsługi naszych klientów korporacyjnych z różnymi rodzajami przypadków użycia biznesowego. Rzeczywista część świata jest zmieniana co sekundę i ma kluczowe znaczenie dla naszych klientów. Nasi klienci, którzy planują otwieranie i zamykanie udogodnień, potrzebują naszych map do natychmiastowego aktualizowania. Dzięki temu mogą efektywnie planować dostarczanie, konserwację lub obsługę klienta w odpowiednich obiektach. Utworzyliśmy witrynę opinii o danych Azure Maps, aby umożliwić naszym klientom dostarczanie bezpośrednich opinii o danych. Opinie o danych klientów przechodzą bezpośrednio do naszych dostawców danych i ich edytorów map. Mogą szybko oszacować i dołączyć Opinie do naszych produktów mapowania.  

[Witryna opinii o danych Azure Maps (wersja zapoznawcza)](https://feedback.azuremaps.com) zapewnia klientom łatwą w obsłudze opinię na temat danych mapy, szczególnie w kwestiach związanych z zainteresowaniami i adresami lokalnymi. W tym artykule opisano sposób udostępniania różnych rodzajów opinii przy użyciu witryny Azure Maps opinię.

## <a name="add-a-business-place-or-a-residential-address"></a>Dodaj miejsce biznesowe lub adres mieszkalny 

Możesz chcieć przekazać opinię o brakującym punkcie zainteresowania lub adresie zamieszkania. Istnieją dwa sposoby, aby to zrobić. Otwórz witrynę opinii o danych mapy platformy Azure, Wyszukaj współrzędne brakującej lokalizacji, a następnie kliknij pozycję "Dodaj miejsce".

  ![Wyszukaj brakującą lokalizację](./media/how-to-use-feedback-tool/search-poi.png)

Lub można korzystać z mapy. Kliknij lokalizację, aby porzucić numer PIN na współrzędnej, a następnie kliknij pozycję "Dodaj miejsce".

  ![Dodaj kod PIN](./media/how-to-use-feedback-tool/add-poi.png)

Po kliknięciu nastąpi przekierowanie do formularza, aby podać odpowiednie szczegóły dotyczące miejsca.

  ![Dodaj miejsce](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Napraw miejsce biznesowe lub adres mieszkalny 

Witryna opinii umożliwia także wyszukiwanie i lokalizowanie miejsca biznesowego lub adresu. Możesz podać Opinie, aby naprawić adres lub lokalizację numeru PIN, jeśli nie są poprawne. Aby przekazać opinię dotyczącą naprawienia adresu, użyj paska wyszukiwania, aby wyszukać miejsce pracy lub adres zamieszkania. Kliknij lokalizację zainteresowania z listy wyników. Kliknij pozycję "Napraw to miejsce".

  ![Wyszukaj miejsce do rozwiązania](./media/how-to-use-feedback-tool/fix-place.png)

Aby przesłać opinię dotyczącą naprawienia adresu, Wypełnij formularz "Napraw miejsce", a następnie kliknij przycisk "Prześlij".

  ![Napraw formularz](./media/how-to-use-feedback-tool/fix-form.png)

Jeśli lokalizacja numeru PIN dla miejsca jest nieprawidłowa, zaznacz pole wyboru w formularzu "Napraw miejsce", w którym znajduje się komunikat "Lokalizacja numeru PIN jest niepoprawna". Przenieś kod PIN do odpowiedniej lokalizacji, a następnie kliknij przycisk "Prześlij".

  ![Przenieś lokalizację numeru PIN](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Dodaj komentarz 

Oprócz wyszukania lokalizacji w narzędziu do przesyłania opinii można także dodać bezpłatny komentarz do tekstu formularza, aby uzyskać szczegółowe informacje dotyczące lokalizacji. Aby dodać komentarz, Wyszukaj lokalizację lub kliknij lokalizację. Kliknij pozycję "Dodaj komentarz", Napisz komentarz, a następnie kliknij pozycję "Prześlij".

  ![Dodaj komentarz](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Śledzenie stanu 

Możesz również śledzić stan żądania, sprawdzając pole "chcę śledzić status" i dostarczając swój adres e-mail podczas wykonywania żądania. W wiadomości e-mail zostanie wyświetlony link do śledzenia z aktualnym stanem żądania. 

  ![stan opinii](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>Następne kroki

Aby ogłosić wszystkie pytania techniczne dotyczące Azure Maps, odwiedź stronę:

* [Microsoft Q & A](/answers/topics/azure-maps.html)