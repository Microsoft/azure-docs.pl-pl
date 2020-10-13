---
title: Wykonaj transmisję strumieniową na żywo za pomocą Azure Media Services, aby utworzyć strumienie o szybkości transmisji bitów z Azure Portal | Microsoft Docs
description: Ten samouczek przedstawia tworzenie kanału, który odbiera strumień na żywo o jednej szybkości transmisji bitów i koduje go jako strumień o różnych szybkościach transmisji bitów przy użyciu witryny Azure Portal.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 504f74c2-3103-42a0-897b-9ff52f279e23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: inhenkel
ms.openlocfilehash: 826fda62f9c5c97d045f6dc31189b26255e72f33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532707"
---
# <a name="perform-live-streaming-using-media-services-to-create-multi-bitrate-streams-with-azure-portal"></a>Wykonaj transmisję strumieniową na żywo za pomocą Media Services, aby utworzyć strumienie o szybkości transmisji bitów z Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [Interfejs API REST](/rest/api/media/operations/channel)
> 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

Ten samouczek przedstawia tworzenie **kanału**, który odbiera strumień na żywo o pojedynczej szybkości transmisji bitów i koduje go jako strumień o wielokrotnej szybkości transmisji bitów.

Aby uzyskać więcej informacji o pojęciach związanych z kanałami obsługującymi kodowanie na żywo, zobacz temat [Korzystanie z usługi Azure Media Services do prowadzenia transmisji strumieniowych na żywo ze strumieniami o wielokrotnej szybkości transmisji bitów](media-services-manage-live-encoder-enabled-channels.md).

## <a name="common-live-streaming-scenario"></a>Typowy scenariusz transmisji strumieniowej na żywo

Poniżej przedstawiono ogólne etapy tworzenia typowych aplikacji transmisji strumieniowej na żywo.

> [!NOTE]
> Obecnie maksymalny zalecany czas trwania wydarzenia na żywo wynosi 8 godzin. Napisz na adres amshelp@microsoft.com, jeśli potrzebujesz uruchomić kanał na dłuższy czas.

1. Podłącz kamerę wideo do komputera. <br/>Aby zapoznać się z pomysłami dotyczącymi konfiguracji, zapoznaj się z [konfiguracją prostego i przenośnego sprzętu wideo]( https://link.medium.com/KNTtiN6IeT).

    Jeśli nie masz dostępu do aparatu, można użyć narzędzi, takich jak [Wirecast usługi NetStream](media-services-configure-wirecast-live-encoder.md) , wygenerowania na żywo kanału informacyjnego z pliku wideo.
1. Uruchom i skonfiguruj lokalny koder na żywo, który wysyła strumień o pojedynczej szybkości transmisji bitów przy użyciu jednego z następujących protokołów: RTMP lub Smooth Streaming. Aby uzyskać więcej informacji, zobacz temat [Obsługa protokołu RTMP i kodery na żywo w usłudze Azure Media Services](https://go.microsoft.com/fwlink/?LinkId=532824). <br/>Zapoznaj się również z tym blogiem: [produkcja przesyłania strumieniowego na żywo z obs](https://link.medium.com/ttuwHpaJeT).

    Ten krok można również wykonać po utworzeniu kanału.
1. Utwórz i uruchom kanał.
1. Pobierz adres URL pozyskiwania kanału.

    Koder na żywo używa adresu URL pozyskiwania do wysyłania strumienia do kanału.
1. Pobierz adres URL podglądu kanału.

    Użyj tego adresu URL, aby sprawdzić, czy kanał prawidłowo odbiera strumień na żywo.
1. Utwórz wydarzenie/program (to spowoduje również utworzenie elementu zawartości).
1. Opublikuj wydarzenie (to spowoduje utworzenie lokalizatora OnDemand dla skojarzonego elementu zawartości).
1. Uruchom wydarzenie, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji.
1. Opcjonalnie można przesłać do kodera na żywo sygnał o rozpoczęciu reklamy. Reklama jest wstawiana do strumienia wyjściowego.
1. Zatrzymaj wydarzenie w dowolnym momencie, w którym zechcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.
1. Usuń wydarzenie (opcjonalnie możesz również usunąć element zawartości).

## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy są wymagane do wykonania czynności przedstawionych w samouczku.

* Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut.
  Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Konto usługi Media Services. Aby utworzyć konto usługi Media Services, zobacz temat [Tworzenie konta](media-services-portal-create-account.md).
* Kamera internetowa i koder, który może wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów.

## <a name="create-a-channel"></a>Tworzenie kanału

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz pozycję Media Services, a następnie kliknij nazwę konta usługi Media Services.
2. Wybierz pozycję **Transmisja strumieniowa na żywo**.
3. Wybierz pozycję **Tworzenie niestandardowe**. Ta opcja umożliwi utworzenie kanału, który jest skonfigurowany do przeprowadzania kodowania na żywo.

    ![Tworzenie kanału](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
4. Kliknij pozycję **Settings** (Ustawienia).

   1. Wybierz typ kanału **Kodowanie na żywo**. Ten typ określa, że chcesz utworzyć kanał obsługujący kodowanie na żywo. Oznacza to, że przychodzący strumień o pojedynczej szybkości transmisji bitów jest wysyłany do kanału i kodowany do strumienia o wielokrotnej szybkości transmisji bitów przy użyciu określonych ustawień kodera na żywo. Aby uzyskać więcej informacji na ten temat, zobacz artykuł [Korzystanie z usługi Azure Media Services do prowadzenia transmisji strumieniowych na żywo ze strumieniami o wielokrotnej szybkości transmisji bitów](media-services-manage-live-encoder-enabled-channels.md). Kliknij przycisk OK.
   2. Określ nazwę kanału.
   3. Kliknij przycisk OK w dolnej części ekranu.
5. Wybierz kartę **Pozyskiwanie**.

   1. Na tej stronie można wybrać protokół przesyłania strumieniowego. W przypadku kanału typu **Kodowanie na żywo** prawidłowe opcje protokołu są następujące:

      * Pojedyncza szybkość transmisji bitów podzielonej zawartości w formacie MP4 (Smooth Streaming)
      * Pojedyncza szybkość transmisji bitów w formacie RTMP

        Aby uzyskać szczegółowe informacje na temat wszystkich protokołów, zobacz artykuł [Korzystanie z usługi Azure Media Services do prowadzenia transmisji strumieniowych na żywo ze strumieniami o wielokrotnej szybkości transmisji bitów](media-services-manage-live-encoder-enabled-channels.md).

        Nie można zmienić opcji protokołu, gdy kanał lub skojarzone z nim wydarzenia/programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu przesyłania strumieniowego.  
   2. Można zastosować ograniczenie adresów IP dotyczące pozyskiwania.

       Można zdefiniować adresy IP, które mogą pozyskiwać pliki wideo w tym kanale. Dozwolone adresy IP można określić jako pojedynczy adres IP (np. "10.0.0.1"), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (np. "10.0.0.1/22") lub zakres adresów IP przy użyciu adresu IP i maski podsieci z kropką dziesiętną (np. "10.0.0.1 (255.255.252.0)").

       Jeśli adresy IP nie zostaną określone i brakuje definicji reguły, to żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.
6. Na karcie **Podgląd** zastosuj ograniczenie adresów IP do podglądu.
7. Na karcie **Kodowanie** określ ustawienie wstępne kodowania.

    Obecnie jedyną wartością ustawienia wstępnego systemu, którą można wybrać, jest **Domyślna rozdzielczość 720p**. Aby określić niestandardowe ustawienie domyślne, otwórz bilet pomocy technicznej firmy Microsoft. Następnie wprowadź nazwę utworzonego ustawienia wstępnego.

> [!NOTE]
> Uruchomienie kanału może obecnie potrwać do 30 minut. Resetowanie kanału może potrwać maksymalnie 5 minut.

Po utworzeniu kanału można go kliknąć i wybrać pozycję **Ustawienia**, aby wyświetlić konfiguracje kanału.

Aby uzyskać więcej informacji na ten temat, zobacz artykuł [Korzystanie z usługi Azure Media Services do prowadzenia transmisji strumieniowych na żywo ze strumieniami o wielokrotnej szybkości transmisji bitów](media-services-manage-live-encoder-enabled-channels.md).

## <a name="get-ingest-urls"></a>Pobieranie adresów URL pozyskiwania

Po utworzeniu kanału można pobrać adresy URL pozyskiwania, które należy udostępnić koderowi na żywo. Koder używa tych adresów URL do wprowadzenia strumienia na żywo.

![adresy URL pozyskiwania](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)

## <a name="create-and-manage-events"></a>Tworzenie wydarzeń i zarządzanie nimi

### <a name="overview"></a>Omówienie

Kanał jest skojarzony z wydarzeniami/programami, które umożliwiają kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają wydarzeniami/programami. Relacja kanału i programu jest bardzo podobna do relacji w tradycyjnych multimediach, gdzie kanał ma stały strumień zawartości, a program obejmuje niektóre zdarzenia czasowe na tym kanale.

Można określić, przez ile godzin ma być zachowywana zarejestrowana zawartość na potrzeby wydarzenia, ustawiając długość **Okna archiwum**. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin. Długość okna archiwum określa również dostępny dla klientów zakres cofania odtwarzania pliku od bieżącego momentu transmisji na żywo. Wydarzenia mogą być uruchamiane w określonym czasie, ale zawartość, która wykracza poza długość okna jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każde wydarzenie jest skojarzone z elementem zawartości. Aby opublikować wydarzenie, należy utworzyć lokalizator OnDemand dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione wydarzenia, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja 6 godzin wydarzenia, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione wydarzenia. Jedno z wydarzeń jest skonfigurowane do archiwizacji 6 godzin wydarzenia, ale ten program nie zostanie publikowany. Drugie wydarzenie jest skonfigurowane do archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

Nie należy używać ponownie istniejących programów dla nowych zdarzeń. Zamiast tego należy utworzyć i uruchomić nowy program dla każdego zdarzenia.

Uruchom wydarzenie/program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji. Zatrzymaj wydarzenie w dowolnym momencie, w którym zechcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.

Aby usunąć zarchiwizowaną zawartość, zatrzymaj i usuń wydarzenie, a następnie usuń skojarzony element zawartości. Nie można usunąć elementu zawartości, jeśli jest on używany przez wydarzenie. Najpierw należy usunąć wydarzenie.

Nawet po zatrzymaniu i usunięciu wydarzenia użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość wideo na żądanie tak długo, jak zasoby nie zostaną usunięte.

Jeśli chcesz zachować zarchiwizowaną zawartość, ale bez udostępniania jej do przesyłania strumieniowego, usuń lokalizator przesyłania strumieniowego.

### <a name="createstartstop-events"></a>Tworzenie, uruchamianie i zatrzymywanie wydarzeń

Po przesłaniu strumienia do kanału można rozpocząć zdarzenie przesyłania strumieniowego poprzez utworzenie elementu zawartości, programu i lokalizatora przesyłania strumieniowego. Spowoduje to archiwizację strumienia i udostępni go użytkownikom za pośrednictwem punktu końcowego przesyłania strumieniowego.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

Istnieją dwa sposoby rozpoczęcia zdarzenia:

1. Na stronie **Kanał** kliknij pozycję **Wydarzenie na żywo**, aby dodać nowe wydarzenie.

    Określ nazwę wydarzenia, nazwę elementu zawartości, okno archiwum i opcję szyfrowania.

    ![Utwórz program](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)

    Jeśli zaznaczono opcję **Opublikuj to wydarzenie na żywo teraz**, zostaną utworzone ADRESY URL PUBLIKOWANIA.

    Naciśnij przycisk **Start**, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego wydarzenia.

    Po uruchomieniu wydarzenia możesz nacisnąć przycisk **Obejrzyj**, aby rozpocząć odtwarzanie zawartości.
2. Możesz również użyć skrótu i nacisnąć przycisk **Na żywo** na stronie **Kanał**. Spowoduje to utworzenie domyślnych elementów treści, programu i lokalizatora przesyłania strumieniowego.

    Wydarzenie będzie miało nazwę **domyślne** i okno archiwum zostanie ustawione na 8 godzin.

Możesz obejrzeć opublikowane wydarzenie na stronie **Wydarzenie na żywo**.

Jeśli klikniesz pozycję **Zakończ transmisję na żywo**, wszystkie wydarzenia na żywo zostaną zatrzymane.

## <a name="watch-the-event"></a>Oglądanie wydarzenia

Aby oglądać wydarzenie, kliknij przycisk **Oglądaj** w witrynie Azure Portal lub skopiuj adres URL przesyłania strumieniowego i użyj wybranego odtwarzacza.

![Utworzone](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Po zatrzymaniu wydarzenia na żywo wydarzenie jest automatycznie konwertowane na zawartość na żądanie.

## <a name="clean-up"></a>Czyszczenie

Aby po zakończeniu strumieniowego przesyłania zdarzeń, wyczyścić udostępnione wcześniej zasoby, postępuj zgodnie z poniższą procedurą.

* Zatrzymaj wypychanie strumienia z kodera.
* Zatrzymaj kanał. Po zatrzymaniu kanału opłaty nie będą naliczane. W razie potrzeby ponownego uruchomienia kanał będzie miał ten sam adres URL pozyskiwania, więc nie trzeba będzie ponownie konfigurować kodera.
* Można zatrzymać punkt końcowy przesyłania strumieniowego, chyba że chcesz nadal udostępniać archiwum zdarzenia na żywo w formie przesyłania strumieniowego na żądanie. Jeśli kanał jest zatrzymany, żadne opłaty nie będą naliczane.

## <a name="view-archived-content"></a>Wyświetlanie zarchiwizowanej zawartości

Nawet po zatrzymaniu i usunięciu wydarzenia użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość wideo na żądanie tak długo, jak zasoby nie zostaną usunięte.

> [!WARNING]
> **Nie należy** usuwać elementu zawartości, jeśli jest on używany przez zdarzenie. najpierw należy usunąć zdarzenie.

Aby zarządzać elementami zawartości, wybierz **ustawienie** i kliknij przycisk **elementy zawartości**.

![Elementy zawartości](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

## <a name="considerations"></a>Zagadnienia do rozważenia

* Obecnie maksymalny zalecany czas trwania wydarzenia na żywo wynosi 8 godzin. Napisz na adres amshelp@microsoft.com, jeśli potrzebujesz uruchomić kanał na dłuższy czas.
* Upewnij się, że punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać swoją zawartość, ma stan **Uruchomiony**.

## <a name="next-steps"></a>Następne kroki

Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
