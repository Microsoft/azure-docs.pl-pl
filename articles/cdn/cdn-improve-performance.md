---
title: Poprawianie wydajności dzięki kompresji plików w Azure CDN | Microsoft Docs
description: Dowiedz się, jak poprawić szybkość transferu plików i zwiększyć wydajność ładowania strony, kompresując pliki w Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: 3b8ce5b82b7d2022fd7feea1cd9efe8d524ee6a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358291"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Poprawianie wydajności poprzez kompresowanie plików w usłudze Azure CDN
Kompresja plików to prosta i skuteczna metoda zwiększania szybkości transferu plików i zwiększania wydajności ładowania strony poprzez zmniejszenie rozmiaru pliku przed jego wysłaniem z serwera. Kompresja plików pozwala zmniejszyć koszty przepustowości i zapewnić użytkownikom większą wydajność.

Istnieją dwa sposoby włączania kompresji plików:

- Włącz kompresję na serwerze źródłowym. W takim przypadku Azure CDN są przekazywane do skompresowanych plików i dostarcza klientom żądania.
- Włącz kompresję bezpośrednio na serwerach POP usługi CDN (*kompresja na bieżąco*). W takim przypadku Usługa CDN kompresuje pliki i udostępnia je użytkownikom końcowym, nawet jeśli nie zostały skompresowane przez serwer źródłowy.

> [!IMPORTANT]
> Azure CDN zmiany konfiguracji mogą zająć trochę czasu w sieci: 
> - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
> - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
> - W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut. 
> 
> W przypadku konfigurowania kompresji po raz pierwszy w punkcie końcowym usługi CDN należy rozważyć oczekiwanie 1-2 godzin przed rozpoczęciem rozwiązywania problemów, aby upewnić się, że ustawienia kompresji zostały przekazane do punktów obecności.

## <a name="enabling-compression"></a>Włączanie kompresji
Warstwy usługi CDN w warstwie Standardowa i Premium zapewniają te same funkcje kompresji, ale interfejs użytkownika różni się. Aby uzyskać więcej informacji o różnicach między warstwami usługi CDN w warstwie Standardowa i Premium, zobacz [Azure CDN omówienie](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Profile standardowego sieci CDN 
> [!NOTE]
> Ta sekcja ma zastosowanie do **Azure CDN Standard od firmy Microsoft**, **Azure CDN Standard from Verizon**i **Azure CDN Standard from Akamai** profile.
> 
> 

1. Na stronie profil usługi CDN wybierz punkt końcowy usługi CDN, którym chcesz zarządzać.

    ![Punkty końcowe profilu usługi CDN](./media/cdn-file-compression/cdn-endpoints.png)

    Zostanie otwarta strona punkt końcowy usługi CDN.
2. Wybierz opcję **kompresja**.

    ![Zrzut ekranu przedstawia punkt końcowy z kompresją wybraną z menu Portal.](./media/cdn-file-compression/cdn-compress-select-std.png)

    Zostanie otwarta strona kompresja.
3. Wybierz pozycję **włączone** , aby włączyć kompresję.

    ![Zrzut ekranu przedstawia włączenie kompresji.](./media/cdn-file-compression/cdn-compress-standard.png)
4. Użyj domyślnych typów MIME lub zmodyfikuj listę przez dodanie lub usunięcie typów MIME.

   > [!TIP]
   > Chociaż jest to możliwe, nie zaleca się stosowania kompresji do skompresowanych formatów. Na przykład, ZIP, MP3, MP4 lub JPG.
   > 

   > [!NOTE]
   > Modyfikowanie domyślnej listy typów MIME nie jest obecnie obsługiwane w programie Azure CDN Standard od firmy Microsoft.
   > 

5. Po wprowadzeniu zmian wybierz pozycję **Zapisz**.

### <a name="premium-cdn-profiles"></a>Profile usługi CDN w warstwie Premium
> [!NOTE]
> Ta sekcja ma zastosowanie tylko do **Azure CDN Premium z profilów Verizon** .
> 

1. Na stronie profil usługi CDN wybierz pozycję **Zarządzaj**.

    ![Wybór zarządzania usługą CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    Zostanie otwarty portal zarządzania sieci CDN.
2. Umieść wskaźnik myszy na **dużej karcie http** , a następnie umieść wskaźnik myszy nad menu wysuwanym **Ustawienia pamięci podręcznej** . Wybierz opcję **kompresja**.

    ![Wybór kompresji sieci CDN](./media/cdn-file-compression/cdn-compress-select.png)

    Wyświetlane są opcje kompresji.

    ![Opcje kompresji plików CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Włącz kompresję, wybierając **kompresję włączona**. Wprowadź typy MIME, które chcesz skompresować jako listę rozdzielaną przecinkami (bez spacji) w polu **typy plików** .

   > [!TIP]
   > Chociaż jest to możliwe, nie zaleca się stosowania kompresji do skompresowanych formatów. Na przykład, ZIP, MP3, MP4 lub JPG.
   > 

4. Po wprowadzeniu zmian wybierz pozycję **Aktualizuj**.

## <a name="compression-rules"></a>Reguły kompresji

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Standard Azure CDN z profilów firmy Microsoft

W przypadku usługi **Azure CDN standard w profilach firmy Microsoft** skompresowane są tylko odpowiednie pliki. Aby można było kwalifikować się do kompresji, plik musi:
- Być typu MIME, który został [skonfigurowany do kompresji](#enabling-compression).
- Większy niż 1 KB
- Być mniejsze niż 8 MB

Te profile obsługują następujące kodowania kompresji:
- gzip (GNU zip)
- brotli 

Jeśli żądanie obsługuje więcej niż jeden typ kompresji, ma pierwszeństwo kompresja brotli.

Gdy żądanie dla elementu zawartości określa kompresję gzip, a żądanie spowoduje odrzucenie pamięci podręcznej, Azure CDN wykonuje kompresję strumienia gzip bezpośrednio na serwerze POP. Następnie skompresowany plik jest obsługiwany z pamięci podręcznej.

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN z profilów Verizon

W przypadku **Azure CDN Standard from Verizon** i **Azure CDN Premium z profilów Verizon** są kompresowane tylko odpowiednie pliki. Aby można było kwalifikować się do kompresji, plik musi:
- Przekracza 128 bajtów
- Być mniejsze niż 3 MB

Te profile obsługują następujące kodowania kompresji:
- gzip (GNU zip)
- WKLĘŚNIĘCIE
- bzip2
- brotli 

Jeśli żądanie obsługuje więcej niż jeden typ kompresji, te typy kompresji mają pierwszeństwo przed kompresją brotli.

Gdy żądanie dla elementu zawartości określa kompresję brotli (nagłówek HTTP is `Accept-Encoding: br` ), a żądanie powoduje odrzucenie pamięci podręcznej, Azure CDN wykonuje kompresję brotli elementu zawartości bezpośrednio na serwerze pop. Następnie skompresowany plik jest obsługiwany z pamięci podręcznej.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Standard Azure CDN z profilów Akamai

W przypadku **Azure CDN standardowych z profilów Akamai** wszystkie pliki kwalifikują się do kompresji. Jednak plik musi być typu MIME, który został [skonfigurowany do kompresji](#enabling-compression).

Te profile obsługują tylko kodowanie kompresji gzip. Gdy punkt końcowy profilu żąda pliku zakodowanego w formacie gzip, jest on zawsze żądany ze źródła, niezależnie od żądania klienta. 

## <a name="compression-behavior-tables"></a>Tabele zachowań kompresji
W poniższych tabelach opisano zachowanie kompresji Azure CDN w każdym scenariuszu:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Kompresja jest wyłączona lub plik nie kwalifikuje się do kompresji
| Format żądany przez klienta (za pośrednictwem nagłówka Accept-Encoding) | Format pliku w pamięci podręcznej | Odpowiedź usługi CDN na klienta | &nbsp; &nbsp; &nbsp; &nbsp; Uwagi &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Skompresowane |Skompresowane |Skompresowane | |
| Skompresowane |Nieskompresowanych |Nieskompresowanych | |
| Skompresowane |Niebuforowane |Skompresowane lub nieskompresowane |Odpowiedź pochodzenia określa, czy Usługa CDN wykonuje kompresję. |
| Nieskompresowanych |Skompresowane |Nieskompresowanych | |
| Nieskompresowanych |Nieskompresowanych |Nieskompresowanych | |
| Nieskompresowanych |Niebuforowane |Nieskompresowanych | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Kompresja jest włączona i plik kwalifikuje się do kompresji
| Format żądany przez klienta (za pośrednictwem nagłówka Accept-Encoding) | Format pliku w pamięci podręcznej | Odpowiedź usługi CDN na klienta | Uwagi |
| --- | --- | --- | --- |
| Skompresowane |Skompresowane |Skompresowane |Transkodowanie między obsługiwanymi formatami przez sieć CDN. |
| Skompresowane |Nieskompresowanych |Skompresowane |Usługa CDN wykonuje kompresję. |
| Skompresowane |Niebuforowane |Skompresowane |Usługa CDN wykonuje kompresję, jeśli źródło zwróci nieskompresowany plik. <br/>**Azure CDN z Verizon** przekazuje nieskompresowany plik pierwszego żądania, a następnie kompresuje i buforuje plik pod kątem kolejnych żądań. <br/>Pliki z `Cache-Control: no-cache` nagłówkiem nigdy nie są kompresowane. |
| Nieskompresowanych |Skompresowane |Nieskompresowanych |Usługa CDN wykonuje dekompresowanie. |
| Nieskompresowanych |Nieskompresowanych |Nieskompresowanych | |
| Nieskompresowanych |Niebuforowane |Nieskompresowanych | |

## <a name="media-services-cdn-compression"></a>Media Services kompresję sieci CDN
W przypadku punktów końcowych włączonych do Media Services przesyłania strumieniowego w sieci CDN kompresja jest domyślnie włączona dla następujących typów MIME: 
- application/vnd. MS-SStR + XML 
- Aplikacja/łącznik + XML
- adres URL aplikacji/vnd.apple.mpeg
- Application/f4m + XML 

## <a name="see-also"></a>Zobacz też
* [Rozwiązywanie problemów związanych z kompresją pliku CDN](cdn-troubleshoot-compression.md)    

