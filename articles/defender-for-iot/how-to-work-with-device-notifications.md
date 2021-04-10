---
title: Praca z powiadomieniami urządzeń
description: Powiadomienia zawierają informacje o aktywności sieciowej, które mogą wymagać uwagi, wraz z zaleceniami dotyczącymi obsługi tego działania.
ms.date: 12/12/2020
ms.topic: how-to
ms.openlocfilehash: c0c2fc5a4c01a8a31512cd43c340bf3fadc259b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781334"
---
# <a name="work-with-device-notifications"></a>Praca z powiadomieniami urządzeń

Powiadomienia zawierają informacje o aktywności sieciowej, które mogą wymagać uwagi, wraz z zaleceniami dotyczącymi obsługi tego działania. Na przykład może zostać wyświetlone powiadomienie dotyczące:

- Nieaktywne urządzenie. Jeśli urządzenie nie jest już częścią sieci, można je usunąć. Jeśli urządzenie jest nieaktywne, na przykład z powodu błędnego odłączenia od sieci, należy ponownie podłączyć urządzenie i odrzucić powiadomienie.

- Wykryto adres IP na urządzeniu, które jest obecnie identyfikowane tylko przez adres MAC. Odpowiadaj, autoryzując adres IP urządzenia.

Reagowanie na powiadomienia ulepsza informacje zawarte w temacie Mapa urządzeń, spis urządzeń i zapytania i raporty wyszukiwania danych. Zapewnia również wgląd w wiarygodne zmiany w sieci i potencjalne konfiguracje sieci.

**Powiadomienia a alerty**

Oprócz otrzymywania powiadomień dotyczących aktywności sieciowej, mogą zostać wyświetlone *alerty*. Powiadomienia zawierają informacje o zmianach w sieci lub nierozwiązanych właściwościach urządzenia, które nie stwarzają zagrożenia. Alerty zawierają informacje o odchyleniach sieci i zmianach, które mogą stanowić zagrożenie dla sieci.

Aby wyświetlić powiadomienia:

1. Wybierz pozycję **Mapa urządzenia** w okienku menu po lewej stronie.

2. Wybierz ikonę **powiadomienia** . Czerwona liczba powyżej ikony wskazuje liczbę powiadomień.

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="Ikona powiadomienia.":::

   W oknie **powiadomienia** są wyświetlane wszystkie powiadomienia wykryte przez czujnik.

   :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="Powiadomienia.":::

## <a name="filter-the-notifications-window"></a>Filtrowanie okna powiadomień

Użyj filtrów wyszukiwania, aby wyświetlić interesujące Cię powiadomienia.

| Filtruj według | Opis |
|--|--|
| Filtruj według typu | Wyświetl powiadomienia, które obejmują określony obszar zainteresowania. Na przykład Wyświetl tylko powiadomienia dla nieaktywnych urządzeń. |
| Filtruj według zakresu dat | Wyświetl powiadomienia, które obejmują określony przedział czasu. Na przykład Wyświetl powiadomienia wysyłane w ostatnim tygodniu. |
| Wyszukaj określone informacje | Wyszukaj określone powiadomienia. |

## <a name="notification-events-and-responses"></a>Zdarzenia i odpowiedzi powiadomień

W poniższej tabeli opisano typy zdarzeń powiadomień, które mogą zostać wyświetlone wraz z opcjami ich obsługi. Możesz zaktualizować informacje o urządzeniu przy użyciu zalecanej wartości lub odrzucić powiadomienie. Po odrzuceniu powiadomienia informacje o urządzeniu nie są aktualizowane przy użyciu zalecanych informacji. Jeśli ruch zostanie ponownie wykryty, powiadomienie zostanie wysłane ponownie.

| Typy zdarzeń powiadomień | Opis | Odpowiedzi |
|--|--|--|
| Wykryto nowy adres IP | Z urządzeniem jest skojarzony nowy adres IP. Można wykryć pięć scenariuszy: <br /><br /> Z urządzeniem skojarzono dodatkowy adres IP. To urządzenie jest również skojarzone z istniejącym adresem MAC.<br /><br /> Wykryto nowy adres IP dla urządzenia korzystającego z istniejącego adresu MAC. Obecnie urządzenie nie komunikuje się za pomocą adresu IP.<br /> <br /> Wykryto nowy adres IP dla urządzenia korzystającego z nazwy NetBIOS. <br /><br /> Wykryto adres IP jako interfejs zarządzania dla urządzenia skojarzonego z adresem MAC. <br /><br /> Wykryto nowy adres IP dla urządzenia, które używa wirtualnego adresu IP. | **Ustawianie dodatkowego adresu IP na urządzenie** (Scalanie urządzeń) <br /> <br />**Zastąp istniejący adres IP** <br /> <br /> **Odrzuć**<br /> Usuń powiadomienie. |
| Nieaktywne urządzenia | Nie wykryto ruchu na urządzeniu przez ponad 60 dni. | **Usuwanie** <br /> Jeśli to urządzenie nie jest częścią sieci, usuń je. <br /><br />**Odrzuć** <br /> Usuń powiadomienie, jeśli urządzenie jest częścią Twojej sieci. Jeśli urządzenie jest nieaktywne (na przykład dlatego, że zostało nieprzerwanie odłączone od sieci), Odrzuć powiadomienie i ponownie podłącz urządzenie. |
| Nowe urządzenia | Podsieć zawiera urządzenie, które nie jest zdefiniowane w podsieci ICS. <br /><br /> Każda podsieć zawierająca co najmniej jedno z urządzeń można zdefiniować jako podsieć usługi ICS. Pozwala to na rozróżnienie między urządzeniami z systemem i na mapie. | **Ustaw jako podsieć usługi ICS** <br /> <br /> **Odrzuć** <br />Usuń powiadomienie, jeśli urządzenie nie jest częścią podsieci. |
| Nie skonfigurowano żadnych podsieci | W sieci nie ma obecnie skonfigurowanych podsieci. <br /><br /> Skonfiguruj podsieci, aby zapewnić lepszą reprezentację w mapie i możliwość rozróżnienia między urządzeniami NieA a IT. | **Otwórz konfigurację podsieci** i Skonfiguruj podsieci. <br /><br />**Odrzuć** <br /> Usuń powiadomienie. |
| Zmiany systemu operacyjnego | Z urządzeniem skojarzono co najmniej jeden nowy system operacyjny. | Wybierz nazwę nowego systemu operacyjnego, który chcesz skojarzyć z urządzeniem.<br /><br /> **Odrzuć** <br /> Usuń powiadomienie. |
| Nowe podsieci | Wykryto nowe podsieci. | **Learn**<br />Automatycznie Dodaj podsieć.<br />**Otwórz konfigurację podsieci**<br />Dodaj wszystkie brakujące informacje o podsieci.<br />**Odrzuć**<br />Usuń powiadomienie. |
| Zmiany typu urządzenia | Z urządzeniem skojarzono nowy typ urządzenia. | **Ustaw jako {...}**<br />Skojarz nowy typ z urządzeniem.<br />**Odrzuć**<br />Usuń powiadomienie. |

## <a name="respond-to-many-notifications-simultaneously"></a>Reaguj jednocześnie na wiele powiadomień

Może być konieczne równoczesne obsłużenie kilku powiadomień. Na przykład:

- Jeśli przeprowadzono uaktualnienie systemu operacyjnego do dużego zestawu serwerów sieciowych, można nakazać czujnika, aby poznać nowe wersje serwera dla wszystkich uaktualnionych serwerów. 

- Jeśli grupa urządzeń w określonym wierszu została wycofana i nie jest już aktywna, można nakazać czujnikowi Usunięcie tych urządzeń z konsoli programu.

Można nakazać czujnikowi zastosowanie nowo wykrytych informacji na wielu urządzeniach lub ich zignorowanie.   

Aby wyświetlić powiadomienia i obsłużyć powiadomienia:

1. Użyj opcji **Filtruj według typu, zakresu dat** lub opcji **Zaznacz wszystko** . Anuluj wybór powiadomień zgodnie z potrzebami.

2. Nakazuje czujnikowi zastosowanie nowo wykrytych informacji do wybranych urządzeń, wybierając pozycję **nauka**. Lub poinstruuj czujnik, aby ignorował nowo wykryte informacje, wybierając pozycję **Odrzuć**. Zostanie wyświetlona liczba powiadomień, które można jednocześnie poznać i odrzucić wraz z liczbą powiadomień, które należy obsłużyć pojedynczo.

Nie można jednocześnie obsłużyć **nowych adresów IP** ani zdarzeń ze skonfigurowanymi **podsieciami** . Wymagają one potwierdzenia ręcznego.

## <a name="see-also"></a>Zobacz też

[Wyświetlanie alertów](how-to-view-alerts.md)
