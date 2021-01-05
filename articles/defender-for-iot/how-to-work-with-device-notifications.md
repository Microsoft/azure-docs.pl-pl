---
title: Pracuj z powiadomieniami o urządzeniach
description: Powiadomienia zawierają informacje o aktywności sieciowej, które mogą wymagać uwagi, wraz z zaleceniami dotyczącymi obsługi tego działania.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d113805322bd45584987460d57ad6bdba241ec10
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97842827"
---
# <a name="work-with-device-notifications"></a>Pracuj z powiadomieniami o urządzeniach

Powiadomienia zawierają informacje o aktywności sieciowej, które mogą wymagać uwagi, wraz z zaleceniami dotyczącymi obsługi tego działania. Na przykład może zostać wyświetlone powiadomienie dotyczące:

- Nieaktywne urządzenie. Jeśli urządzenie nie jest już częścią sieci, można je usunąć. Jeśli urządzenie jest nieaktywne, na przykład z powodu błędnego odłączenia od sieci, należy ponownie podłączyć urządzenie i odrzucić powiadomienie.

- Wykryto adres IP na urządzeniu, które jest obecnie identyfikowane tylko przez adres MAC. Odpowiadaj, autoryzując adres IP urządzenia.

Reagowanie na powiadomienia ulepsza informacje zawarte w temacie Mapa urządzeń, spis urządzeń i zapytania i raporty wyszukiwania danych. Zapewnia również wgląd w wiarygodne zmiany w sieci i potencjalne konfiguracje sieci.

Aby uzyskać dostęp do powiadomień:

- Wybierz pozycję **Ustawienia systemowe** , a następnie wybierz pozycję **rozszerzenie danych**.

## <a name="notifications-vs-alerts"></a>Powiadomienia a alerty

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
| Nowe adresy IP | Z urządzeniem jest skojarzony nowy adres IP. Można wykryć pięć scenariuszy: <br /><br /> Z urządzeniem skojarzono dodatkowy adres IP. To urządzenie jest również skojarzone z istniejącym adresem MAC.<br /><br /> Wykryto nowy adres IP dla urządzenia korzystającego z istniejącego adresu MAC. Obecnie urządzenie nie komunikuje się za pomocą adresu IP.<br /> <br /> Wykryto nowy adres IP dla urządzenia korzystającego z nazwy NetBIOS. <br /><br /> Wykryto adres IP jako interfejs zarządzania dla urządzenia skojarzonego z adresem MAC. <br /><br /> Wykryto nowy adres IP dla urządzenia, które używa wirtualnego adresu IP. | **Ustawianie dodatkowego adresu IP na urządzenie** (Scalanie urządzeń) <br /> <br />**Zastąp istniejący adres IP** <br /> <br /> **Odrzuć**<br /> Usuń powiadomienie. |
| Nieaktywne urządzenia | Nie wykryto ruchu na urządzeniu przez ponad 60 dni. | **Usuwanie** <br /> Jeśli to urządzenie nie jest częścią sieci, usuń je. <br /><br />**Odrzuć** <br /> Usuń powiadomienie, jeśli urządzenie jest częścią Twojej sieci. Jeśli urządzenie jest nieaktywne (na przykład dlatego, że zostało nieprzerwanie odłączone od sieci), Odrzuć powiadomienie i ponownie podłącz urządzenie. |
| Nowe urządzenie nieposiadane | Podsieć zawiera urządzenie, które nie jest zdefiniowane w podsieci ICS. <br /><br /> Każda podsieć zawierająca co najmniej jedno z urządzeń można zdefiniować jako podsieć usługi ICS. Pozwala to na rozróżnienie między urządzeniami z systemem i na mapie. | **Ustaw jako podsieć usługi ICS** <br /> <br /> **Odrzuć** <br />Usuń powiadomienie, jeśli urządzenie nie jest częścią podsieci. |
| Nie skonfigurowano żadnych podsieci | W sieci nie ma obecnie skonfigurowanych podsieci. <br /><br /> Skonfiguruj podsieci, aby zapewnić lepszą reprezentację w mapie i możliwość rozróżnienia między urządzeniami NieA a IT. | **Otwórz konfigurację podsieci** i Skonfiguruj podsieci. <br /><br />**Odrzuć** <br /> Usuń powiadomienie. |
| Zmiany systemu operacyjnego | Z urządzeniem skojarzono co najmniej jeden nowy system operacyjny. | Wybierz nazwę nowego systemu operacyjnego, który chcesz skojarzyć z urządzeniem.<br /><br /> **Odrzuć** <br /> Usuń powiadomienie. |
| Wykryto podsieci | Wykryto nowe podsieci. | **Learn**<br />Automatycznie Dodaj podsieć.<br />**Otwórz konfigurację podsieci**<br />Dodaj wszystkie brakujące informacje o podsieci.<br />**Odrzuć**<br />Usuń powiadomienie. |
| Wykryto zmianę typu urządzenia | Z urządzeniem skojarzono nowy typ urządzenia. | **Ustaw jako {...}**<br />Skojarz nowy typ z urządzeniem.<br />**Odrzuć**<br />Usuń powiadomienie. |

## <a name="respond-to-many-notifications-simultaneously"></a>Reaguj jednocześnie na wiele powiadomień

Może być konieczne równoczesne obsłużenie kilku powiadomień. Na przykład:

- Jeśli przeprowadzono uaktualnienie systemu operacyjnego do dużego zestawu serwerów sieciowych, można nakazać czujnika, aby poznać nowe wersje serwera dla wszystkich uaktualnionych serwerów. 

- Jeśli grupa urządzeń w określonym wierszu została wycofana i nie jest już aktywna, można nakazać czujnikowi Usunięcie tych urządzeń z konsoli programu.

Można nakazać czujnikowi zastosowanie nowo wykrytych informacji na wielu urządzeniach lub ich zignorowanie.   

Aby wyświetlić powiadomienia i obsłużyć powiadomienia:

1. Użyj opcji **Filtruj według typu, zakresu dat** lub opcji **Zaznacz wszystko** . Anuluj wybór powiadomień zgodnie z potrzebami.

2. Nakazuje czujnikowi zastosowanie nowo wykrytych informacji do wybranych urządzeń, wybierając pozycję **nauka**. Lub poinstruuj czujnik, aby ignorował nowo wykryte informacje, wybierając pozycję **Odrzuć**. Zostanie wyświetlona liczba powiadomień, które można jednocześnie poznać i odrzucić wraz z liczbą powiadomień, które należy obsłużyć pojedynczo.

Nie można jednocześnie obsłużyć **nowych adresów IP** ani zdarzeń ze skonfigurowanymi **podsieciami** . Wymagają one potwierdzenia ręcznego.

## <a name="improve-device-os-classification-data-enhancement"></a>Ulepszanie klasyfikacji systemu operacyjnego urządzeń: rozszerzanie danych 

Czujnik stale odnajduje nowe urządzenia. Umożliwia ona również Autowykrywanie zmian wcześniej odnalezionych urządzeń, w tym typów systemów operacyjnych.

W pewnych okolicznościach w odnalezionych systemach operacyjnych mogą zostać wykryte konflikty. Przyczyną może być wersja systemu operacyjnego odwołująca się do systemów klasycznych lub serwerów. Jeśli tak się stanie, otrzymasz powiadomienie z opcjonalnymi klasyfikacjami systemu operacyjnego.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Zwiększ dane.":::

Zbadaj zalecenia w celu wzbogacania klasyfikacji systemu operacyjnego. Te informacje są wyświetlane w raportach spisu urządzeń, wyszukiwania danych i innych. Może również poprawić dokładność alertów, zagrożeń i analizy ryzyka.

Po zaakceptowaniu zalecenia informacje o typie systemu operacyjnego zostaną zaktualizowane w czujniku.

## <a name="see-also"></a>Zobacz także

[Wyświetlanie alertów](how-to-view-alerts.md)
