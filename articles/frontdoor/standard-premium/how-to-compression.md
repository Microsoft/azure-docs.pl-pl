---
title: Poprawa wydajności dzięki kompresji plików na platformie Azure Front-Standard/Premium (wersja zapoznawcza)
description: Dowiedz się, jak poprawić szybkość transferu plików i zwiększyć wydajność ładowania strony dzięki kompresji plików na platformie Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4b526d82465862b1c0d27aed6443c6d7199bfb5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099676"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Poprawa wydajności dzięki kompresji plików na platformie Azure Front-Standard/Premium (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Kompresja plików to efektywna metoda zwiększania szybkości transferu plików i zwiększania wydajności ładowania strony. Kompresja zmniejsza rozmiar pliku przed jego wysłaniem przez serwer. Kompresja plików pozwala zmniejszyć koszty przepustowości i zapewnić lepsze środowisko pracy użytkowników.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
Istnieją dwa sposoby włączania kompresji plików:

- Włączanie kompresji na serwerze źródłowym. Drzwi frontonu platformy Azure są przekazywane do skompresowanych plików i umożliwiają klientom, którzy je żądają.
- Włączenie kompresji bezpośrednio na serwerach POP (na bieżąco) na platformie Azure (*kompresja w locie*). W takim przypadku drzwi frontonu platformy Azure kompresują pliki i wysyłają je do użytkowników końcowych.

> [!IMPORTANT]
> Zmiany konfiguracji dla drzwi z platformy Azure mogą być propagowane do 10 minut w całej sieci. W przypadku konfigurowania kompresji po raz pierwszy w punkcie końcowym usługi CDN należy rozważyć oczekiwanie 1-2 godzin przed rozpoczęciem rozwiązywania problemów, aby upewnić się, że ustawienia kompresji zostały przekazane do wszystkich punktów obecności.

## <a name="enabling-compression"></a>Włączanie kompresji

> [!Note]
> W przypadku drzwi platformy Azure z przodu kompresja jest częścią **włączania buforowania** w marszrucie. Tylko po **włączeniu buforowania** można korzystać z kompresji na platformie Azure.

Kompresję można włączyć w następujący sposób:
* Podczas szybkiego tworzenia — po włączeniu buforowania można włączyć kompresję.
* W trakcie niestandardowego tworzenia — Włącz buforowanie i kompresję podczas dodawania trasy. 
* W marszrucie Menedżera punktów końcowych.
* Na stronie Optymalizacja.

### <a name="enable-compression-in-endpoint-manager"></a>Włącz kompresję w programie Endpoint Manager

1. Na stronie profil usługi Azure Front-Standard/Premium przejdź do **Menedżera punktów końcowych** i wybierz punkt końcowy, w którym ma zostać włączona kompresja.

1. Wybierz pozycję **Edytuj punkt końcowy**, a następnie wybierz **trasę** , w której ma zostać włączona kompresja. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="Zrzut ekranu strony docelowej Menedżera punktów końcowych." lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. Upewnij się, że jest zaznaczona opcja **Włącz buforowanie** , a następnie zaznacz pole wyboru **Włącz kompresję**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Włącz kompresję w Menedżerze punktów końcowych.":::   

1. Wybierz pozycję **Aktualizuj** , aby zapisać konfigurację.

### <a name="enable-compression-in-optimization"></a>Włącz kompresję w optymalizacji

1. Na stronie profil warstwy usług Azure Front-Standard/Premium przejdź do opcji **optymalizacje** w obszarze Ustawienia. Rozwiń punkt końcowy, aby wyświetlić listę tras. 

1. Wybierz trzy kropki obok **trasy** , która ma *wyłączoną* kompresję. Następnie wybierz pozycję **Konfiguruj trasę**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="Ekran włączania kompresji na stronie Optymalizacja." lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. Upewnij się, że jest zaznaczona opcja **Włącz buforowanie** , a następnie zaznacz pole wyboru **Włącz kompresję**.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Zrzut ekranu przedstawiający Włączanie kompresji w Menedżerze punktów końcowych."::: 

1. Kliknij przycisk **Update** (Aktualizuj).

## <a name="modify-compression-content-type"></a>Modyfikuj typ zawartości kompresji

Możesz zmodyfikować domyślną listę typów MIME na stronie optymalizacje.

1. Na stronie profil warstwy usług Azure Front-Standard/Premium przejdź do opcji **optymalizacje** w obszarze Ustawienia. Następnie wybierz **trasę** z *włączoną* kompresją.

1. Wybierz trzy kropki obok **trasy** z *włączoną* kompresją. Następnie wybierz pozycję **Wyświetl skompresowane typy plików**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="Zrzut ekranu przedstawiający stronę Optymalizacja." lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. Usuń formaty domyślne lub wybierz pozycję **Dodaj** , aby dodać nowe typy zawartości.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="Zrzut ekranu przedstawiający stronę Dostosowywanie kompresji plików."::: 

1. Wybierz pozycję **Zapisz**, aby zaktualizować konfigurację kompresji.

## <a name="disabling-compression"></a>Wyłączanie kompresji

Kompresję można wyłączyć w następujący sposób:
* Wyłącz kompresję w marszrucie Menedżera punktów końcowych.
* Wyłącz kompresję na stronie optymalizacji.

### <a name="disable-compression-in-endpoint-manager"></a>Wyłącz kompresję w programie Endpoint Manager

1. Na stronie profil usługi Azure Front-Standard/Premium przejdź do pozycji **Menedżer punktów końcowych** w obszarze Ustawienia. Wybierz punkt końcowy, dla którego chcesz wyłączyć kompresję.

1. Wybierz pozycję **Edytuj punkt końcowy** , a następnie wybierz **trasę** , dla której chcesz wyłączyć kompresję. Usuń zaznaczenie pola **Włącz kompresję** .

1. Wybierz pozycję **Aktualizuj** , aby zapisać konfigurację.

### <a name="disable-compression-in-optimizations"></a>Wyłącz kompresję w przypadku optymalizacji

1. Na stronie profil warstwy usług Azure Front-Standard/Premium przejdź do opcji **optymalizacje** w obszarze Ustawienia. Następnie wybierz **trasę** z *włączoną* kompresją.

1. Wybierz trzy kropki obok **trasy** z *włączoną* kompresją, a następnie wybierz pozycję *Konfiguruj trasę*.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="Zrzut ekranu przedstawiający funkcję Wyłącz kompresję na stronie Optymalizacja."::: 

1. Usuń zaznaczenie pola **Włącz kompresję** .

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="Zrzut ekranu strony aktualizacji trasy do wyłączania kompresji."::: 

1. Wybierz pozycję **Aktualizuj** , aby zapisać konfigurację.

## <a name="compression-rules"></a>Reguły kompresji

W przypadku drzwi frontonu platformy Azure skompresowane są tylko odpowiednie pliki. Aby można było kwalifikować się do kompresji, plik musi:
* Być typu MIME 
* Większy niż 1 KB
* Być mniejsze niż 8 MB

Te profile obsługują następujące kodowania kompresji:
* gzip (GNU zip)
* brotli 

Jeśli żądanie obsługuje więcej niż jeden typ kompresji, ma pierwszeństwo kompresja brotli.

Gdy żądanie dotyczące elementu zawartości określa kompresję gzip, a żądanie spowoduje odrzucenie pamięci podręcznej, drzwi platformy Azure przenoszą kompresję strumienia zasobów bezpośrednio na serwerze POP. Następnie skompresowany plik jest obsługiwany z pamięci podręcznej.

Jeśli w pochodzeniu są używane kodowanie transferu fragmentarycznego (CTE) do wysyłania skompresowanych danych do punktu obecności platformy Azure, wówczas rozmiary odpowiedzi większe niż 8 MB nie są obsługiwane. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować swój pierwszy [zestaw reguł](how-to-configure-rule-set.md)
- Dowiedz się więcej o [warunkach dopasowania zestawu reguł](concept-rule-set-match-conditions.md)
- Dowiedz się więcej o [zestawie reguł dla drzwi platformy Azure](concept-rule-set.md)
