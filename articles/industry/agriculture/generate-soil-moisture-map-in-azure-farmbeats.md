---
title: Generuj mapę cieplną wilgoci
description: Opisuje sposób generowania mapę cieplną wilgotności gleby na platformie Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75482571"
---
# <a name="generate-soil-moisture-heatmap"></a>Generuj mapę cieplną wilgoci

Wilgotność gleby jest wodą, która znajduje się w odstępach między cząstkami glebowymi.Wilgotność gleby mapę cieplną pomaga zrozumieć dane wilgoci na dowolnej głębokości i w dużej rozdzielczości w farmach. Aby wygenerować dokładną i przydatną wilgotność gleby mapę cieplną, wymagana jest jednolite wdrożenie czujników z tego samego dostawcy. Różni dostawcy będą mieć różnice w sposobie mierzenia wilgoci w glebie wraz z różnicami w kalibracji. Mapę cieplną jest generowany z określoną głębokością przy użyciu czujników wdrożonych na tej głębokości.

W tym artykule opisano proces generowania mapę cieplną wilgoci w farmie przy użyciu akceleratora usługi Azure FarmBeats. W tym artykule dowiesz się, jak:

- [Tworzenie Farm](#create-a-farm)
- [Przypisywanie czujników do Farm](#get-soil-moisture-sensor-data-from-partner)
- [Generuj mapę cieplną wilgoci](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Uwaga:  

- Subskrypcja platformy Azure.
- Uruchomione wystąpienie usługi Azure FarmBeats.
- Dla farmy dostępne są co najmniej trzy czujniki wilgoci.

## <a name="create-a-farm"></a>Tworzenie farmy

Farma jest obszarem geograficznym zainteresowania, dla którego chcesz utworzyć wilgotność gleby mapę cieplną. Farmę można utworzyć przy użyciu [interfejsu API Farm](https://aka.ms/FarmBeatsDatahubSwagger) lub [interfejsu użytkownika akceleratora FarmsBeats](manage-farms-in-azure-farmbeats.md#create-farms)

## <a name="deploy-sensors"></a>Wdróż czujniki

Należy fizycznie wdrożyć czujniki wilgoci gleby w farmie. Czujniki wilgoci gleby można zakupić od dowolnego z naszych zatwierdzonych partnerów — [instrumentów](https://www.davisinstruments.com/product/enviromonitor-gateway/) i [Teralytic](https://teralytic.com/). Należy koordynować z dostawcą czujnika, aby przeprowadzić konfigurację fizyczną w farmie.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Pobieranie danych z czujnika wilgotności gleby od partnera

Ponieważ czujniki zaczynają przesyłać strumieniowo, dane do pulpitu nawigacyjnego danych partnera, włączają dane do usługi Azure FarmBeats. Można to zrobić z poziomu aplikacji partnerskiej.

Jeśli na przykład zakupiono czujniki usługi Davis, zaloguj się do swojego konta linku pogody i podaj wymagane poświadczenia, aby umożliwić przesyłanie strumieniowe danych do usługi Azure FarmBeats. Aby uzyskać wymagane poświadczenia, postępuj zgodnie z instrukcjami z okna [pobieranie danych z czujnika](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners).

Po wprowadzeniu poświadczeń i wybraniu opcji **Prześlij** w aplikacji partnerskiej dane będą przepływane do usługi Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Przypisywanie czujników wilgoci gleby do farmy

Po połączeniu konta czujnika z usługą Azure FarmBeats należy przypisać czujniki wilgoci w glebie do interesującej farmy.

1.  Na stronie głównej wybierz pozycję **farmy** z menu, zostanie wyświetlona strona listy **farmy** .
2.  Wybierz pozycję Moja **Farma**  >  **Dodaj urządzenia**.
3.  Zostanie wyświetlone okno **Dodawanie urządzeń** . Wybierz dowolne urządzenie, które jest połączone z czujnikami wilgotności gleby dla farmy.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Wybierz pozycję **Dodaj urządzenia**.     

## <a name="generate-soil-moisture-heatmap"></a>Generuj mapę cieplną wilgoci

Ten krok polega na utworzeniu zadania lub długotrwałej operacji, która spowoduje wygenerowanie mapę cieplną wilgoci dla farmy.

1.  Na stronie głównej przejdź do obszaru **farmy** z menu nawigacji po lewej stronie, aby wyświetlić stronę farmy.
2.  Wybierz pozycję Moja **Farma**.
3.  Na stronie **szczegóły farmy** wybierz pozycję **Generuj mapę precyzji**.
4.  Z menu rozwijanego wybierz pozycję **wilgotność gleby**.
5.  W oknie **wilgotność gleby** wybierz **ten tydzień**.
6.  W polu **pomiar czujnika** **wilgotności gleby** wprowadź miarę, która ma być używana dla mapy.
    Aby znaleźć miarę czujnika, w obszarze **czujniki**wybierz czujnik wilgoci gleby. We **właściwościach czujnika**Użyj wartości **nazwa miary** .

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Wybierz pozycję **Generuj mapy**.
    Zostanie wyświetlony komunikat z potwierdzeniem zawierającym szczegóły zadania. Aby uzyskać więcej informacji, zobacz stan zadania w obszarze zadania.

    >[!NOTE]
    > Ukończenie zadania trwa około 3 do 4 godzin.

### <a name="download-the-soil-moisture-heatmap"></a>Pobierz wilgotność gleby mapę cieplną

Wykonaj następujące kroki:

1. Na stronie **zadania** Sprawdź **stan zadania** dla zadania utworzonego w ostatniej procedurze.
2. Po **pomyślnym**wyświetleniu stanu zadania wybierz pozycję **mapy** w menu.
3. Wyszukaj mapę według dnia, w której został utworzony, w formacie <gleba moisture_MyFarm_YYYY-MM-DD>.
4. Wybierz mapę w kolumnie **Nazwa** , okno podręczne wyświetla podgląd wybranej mapy.
5. Kliknij pozycję **Pobierz**. Mapa zostanie pobrana i zapisana do folderu lokalnego komputera.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Następne kroki

Teraz, po pomyślnym wygenerowaniu wilgoci glebowej mapę cieplną, Dowiedz się, jak [wygenerować umieszczanie czujnika](generate-maps-in-azure-farmbeats.md#sensor-placement-map) i uzyskać [historyczne dane telemetryczne](ingest-historical-telemetry-data-in-azure-farmbeats.md). 
