---
title: Zastąpienie składnika sprzętowego serii StorSimple 8000 | Microsoft Docs
description: Opisuje sposób bezpiecznego zastępowania PCMs, baterii, modułów kontrolera, kontrolerów EBOD, stacji dysków i obudowy urządzenia StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 12ab5a9598cc0222f5a3e64985be2e2ea9e7e2fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014860"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Zastępowanie składnika sprzętowego na urządzeniu z serii StorSimple 8000

## <a name="overview"></a>Omówienie
Samouczki wymiany składników sprzętowych opisują składniki sprzętowe urządzenia z serii Microsoft Azure StorSimple 8000 i kroki niezbędne do ich usunięcia i zastępowania. W tym artykule opisano ikony bezpieczeństwa, dostępne są wskaźniki do szczegółowych samouczków i wymieniono składniki, które są wymienne.

> [!IMPORTANT]
> Przed podjęciem próby usunięcia lub zastąpienia dowolnego składnika StorSimple upewnij się, że zawarto przegląd [Konwencji ikon bezpieczeństwa](#safety-icon-conventions) i innych [środków bezpieczeństwa](storsimple-8000-safety.md).

### <a name="safety-icon-conventions"></a>Konwencje ikon bezpieczeństwa

W poniższej tabeli opisano ikony bezpieczeństwa używane w tych samouczkach. Należy zwrócić szczególną uwagę na te ikony bezpieczeństwa podczas wykonywania kroków związanych z usuwaniem i zastępowaniem składników urządzeń.

| Ikona | Tekst | Dodatkowe informacje |
|:--- |:--- |:--- |
| ![Ikona ostrzeżenia](./media/storsimple-hardware-component-replacement/Warning.png) |**STANOWIĄ!** |Wskazuje niebezpieczną sytuację, która w przypadku braku problemów spowoduje śmierć lub poważną szkodę. Ten program sygnału jest ograniczony do najbardziej skrajnych sytuacji. |
| ![Ikona ostrzeżenia](./media/storsimple-hardware-component-replacement/Warning.png) |**WYŚWIETLANIA!** |Wskazuje niebezpieczną sytuację, która w przypadku braku problemów może spowodować śmierć lub poważną szkodę. |
| ![Ikona przestroga](./media/storsimple-hardware-component-replacement/Caution.png) |**Ostrzeżenie!** |Wskazuje niebezpieczną sytuację, która w przypadku braku problemów może skutkować drobną lub średnią szkodą. |
| ![Ikona powiadomienia](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**WZORY** |Wskazuje informacje uznawane za ważne, ale nie związane z zagrożeniami. |
| ![Ikona porażenia energii elektrycznej](./media/storsimple-hardware-component-replacement/Electric.png) |**Zagrożenie porażenie elektryczne** |Wskazuje wysoki poziom napięcia. |
| ![Ikona dużej wagi](./media/storsimple-hardware-component-replacement/Weight.png) |**Wysoka Waga** | |
| ![Ikona żadnej części usługi użytkownika](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Brak części z obsługą użytkownika** |Nie należy uzyskiwać dostępu, o ile nie jest prawidłowo szkolony. |
| ![Ikona Odczytaj instrukcje](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Najpierw przeczytaj wszystkie instrukcje** | |
| ![Ikona zagrożenia Porada](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Niebezpieczeństwo Porada** | |

### <a name="before-you-begin"></a>Zanim rozpoczniesz

Zapoznaj się z informacjami o bezpieczeństwie dotyczącymi Twojego urządzenia i ikon bezpieczeństwa używanych w tym samouczku. Aby uzyskać pełne informacje, przejdź do [bezpiecznej instalacji i obsłudze urządzenia StorSimple](storsimple-8000-safety.md) . Przed zainstalowaniem urządzenia StorSimple należy zapoznać się z jego [bezpieczeństwem](storsimple-8000-safety.md#handling-precautions) .

Przed podjęciem próby zamiany składnika należy wziąć pod uwagę następujące informacje.

![Ikona ostrzeżenia ](./media/storsimple-hardware-component-replacement/Warning.png) ![ ikona wstrząsu instalacji elektrycznej ](./media/storsimple-hardware-component-replacement/Electric.png) **WARNING!** .

* Poprawna, przy użyciu zrzutów elektrostatyczne lub do niestatycznego mat podczas obsługi modułów i składników urządzenia StorSimple.
* Nie dotykaj żadnego obwodu. Użyj dostarczonych dojść i prowadnic podczas obsługi składników, które mogą mieć narażony obwód.

![Ikona ostrzeżenia ostrzeżenie ](./media/storsimple-hardware-component-replacement/Warning.png) ![ ](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **:**

Gdy zastąpisz moduł, **nigdy nie opuszczaj pustej kieszeni w tylnej części obudowy**. Przed usunięciem części problemu Uzyskaj Moduł zastępczy lub pusty.

## <a name="hardware-component-replacement-procedures"></a>Procedury zastępcze składników sprzętowych

Urządzenie z serii StorSimple 8000 składa się z kilku modułów wtyczek w obudowach podstawowych i/lub EBOD. 8100 ma jedną obudowę podstawową, a 8600 to urządzenie dwuobudówowe z obudową podstawową i obudową EBOD.

Główne składniki sprzętowe w urządzeniu są podsumowane w poniższych tabelach. Kliknij link w kolumnie **Procedura zastępcza** , aby przejść do skojarzonego samouczka.

| Składniki | # Obecne | Moduł wtyczki? | Procedura zastępująca |
|:--- |:--- |:--- |:--- |
| Obudowa |1 |Nie |[Zastępowanie obudowy na urządzeniu StorSimple](storsimple-8000-chassis-replacement.md) |
| Kontrolery podstawowe |2 |Tak |[Zastępowanie modułu kontrolera na urządzeniu StorSimple](storsimple-8000-controller-replacement.md) |
| Moduły 764W i chłodzące (PCMs) |2 |Tak |[Wymiana modułu zasilania i chłodzenia w urządzeniu StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Zapasowy akumulator |2 |Tak |[Wymiana modułu baterii zapasowej w urządzeniu StorSimple](storsimple-8000-battery-replacement.md) |
| Stacje dysków |12 |Tak |[Zastępowanie dysku na urządzeniu StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 1** Składniki sprzętowe w obudowie podstawowej

Obudowa podstawowa i obudowa EBOD różnią się w zależności od ich modułów we/wy. Ponadto PCMs ma inną moc. PCMs w podstawowej obudowie to 764 w, a te w obudowie EBOD są 580 W. PCMs w obudowie podstawowej również zawiera moduł baterii tworzenia kopii zapasowych.

| Składniki | # Obecne | Moduł wtyczki? | Procedura zastępująca |
|:--- |:--- |:--- |:--- |
| Obudowa |1 |Nie |[Zastępowanie obudowy na urządzeniu StorSimple](storsimple-8000-chassis-replacement.md) |
| Kontrolery EBOD |2 |Tak |[Zastępowanie kontrolera EBOD na urządzeniu StorSimple](storsimple-8000-ebod-controller-replacement.md) |
| Moduły 580W i chłodzące (PCMs) |2 |Tak |[Wymiana modułu zasilania i chłodzenia w urządzeniu StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Stacje dysków |12 |Tak |[Zastępowanie dysku na urządzeniu StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 2** Składniki sprzętowe w obudowie EBOD

Moduły wtyczki na urządzeniu są wyróżnione na następujących diagramach przednich i tylnych. Przy użyciu tych diagramów można określić lokalizację różnych modułów wtyczek, jeśli wymagane jest zastąpienie. Na diagramie przednim są wyświetlane stacje dysków, a tylne diagramy obudowy EBOD i obudowy głównej pokazują moduły wtyczki.

![Panel przedni urządzenia z dyskami](./media/storsimple-hardware-component-replacement/IC741028.png)

**Rysunek 1** Przód urządzenia

| Etykieta | Opis |
|:--- |:--- |
| 0 - 11 |Stacje dysków (łącznie 12) |

Zarówno obudowa podstawowa, jak i obudowa EBOD mają moduły nośne dysków. Obudowy obudów z 12 3,5 "są ułożone w formacie 3 i 4.

![Planowanie planu podstawowego modułów obudowy urządzenia](./media/storsimple-hardware-component-replacement/IC740994.png)

**Rysunek 2** Tył obudowy podstawowej

| Etykieta | Opis |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Kontroler 0 |
| 4 |Kontroler 1 |

![Płyta montażowa modułów wtyczek EBOD obudowy urządzeń](./media/storsimple-hardware-component-replacement/IC769599.png)

**Rysunek 3** Z tyłu obudowy EBOD

| Etykieta | Opis |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Kontroler EBOD 0 |
| 4 |Kontroler EBOD 1 |

## <a name="field-replaceable-units"></a>Jednostki przemieszczenia pól

Następujące pola (FRUs) są dostępne dla urządzenia StorSimple:

* Obudowa (łącznie z panelem zintegrowanych operacji)
* 764 W MODULE PCM
* 580 W MODULE PCM
* Dysk twardy z modułem nośnej dysków
* Moduł kontrolera
* Moduł kontrolera EBOD
* Tworzenie kopii zapasowej modułu baterii
* Montaż zestawu szyny stojaka

[Skontaktuj się pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , aby zamówić dowolne z tych jednostek zamiennych.

## <a name="next-steps"></a>Następne kroki

Przed podjęciem próby zamiany składnika sprzętowego StorSimple Przejrzyj wszystkie [Informacje o zabezpieczeniach](storsimple-8000-safety.md) .
