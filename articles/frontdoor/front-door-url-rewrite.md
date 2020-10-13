---
title: Moje drzwi na platformie Azure — ponowne zapisywanie adresów URL | Microsoft Docs
description: Ten artykuł pomaga zrozumieć, w jaki sposób usługa Azure Front drzwiczki ponownie zapisuje adresy URL dla tras, jeśli są skonfigurowane.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445493"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Ponowne zapisywanie adresów URL (niestandardowa ścieżka przesyłania dalej)
Funkcja Front-drzwiczki platformy Azure obsługuje ponowne zapisywanie adresów URL przez skonfigurowanie opcjonalnej **niestandardowej ścieżki przesyłania dalej** , która będzie używana podczas konstruowania żądania do przesyłania dalej do zaplecza. Domyślnie, jeśli nie podano niestandardowej ścieżki przekazywania, drzwi z przodu skopiują ścieżkę przychodzącego adresu URL do adresu URL używanego w żądaniu przekazywanym dalej. Nagłówek Host użyty w przesłanym dalej żądaniu jest skonfigurowany dla wybranego zaplecza. Odczytaj [nagłówek hosta zaplecza](front-door-backend-pool.md#hostheader) , aby dowiedzieć się, co robi i jak można go skonfigurować.

Niestandardową częścią funkcji ponownego zapisywania adresów URL jest skopiowanie dowolnej części ścieżki przychodzącej, która pasuje do ścieżki wieloznacznej do przesłanej dalej ścieżki (te segmenty ścieżki są **zielonymi** segmentami w poniższym przykładzie):
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Ponowne zapisywanie adresu URL usługi Azure Front-drzwiczk":::

## <a name="url-rewrite-example"></a>Przykład ponownego zapisywania adresu URL
Rozważ użycie reguły routingu z następującą kombinacją skonfigurowanych hostów frontonu i ścieżek:

| Hosts      | Ścieżki       |
|------------|-------------|
| \.contoso.com www | /\*   |
|            | /foo        |
|            | Foo\*     |
|            | /foo/bar/\* |

W pierwszej kolumnie poniższej tabeli przedstawiono przykłady żądań przychodzących, a druga kolumna pokazuje, co to jest "najbardziej specyficzna" ścieżka do trasy.  Trzecia i kolumnowa kolumna tabeli to przykłady skonfigurowanych **niestandardowych ścieżek przesyłania dalej**.

Na przykład jeśli odczytasz w drugim wierszu, oznacza to, że w przypadku żądania przychodzącego `www.contoso.com/sub` , jeśli niestandardowa ścieżka przesyłania dalej to `/` , przekazana ścieżka będzie `/sub` . W przypadku niestandardowej ścieżki przesyłania dalej `/fwd/` zostanie przekazana ścieżka `/fwd/sub` . I tak dalej, dla pozostałych kolumn. **Wyróżnione** fragmenty ścieżek poniżej reprezentują fragmenty, które są częścią dopasowania symboli wieloznacznych.

| Żądanie przychodzące       | Najbardziej Szczegółowa ścieżka dopasowania | /          | /fwd/          | Foo          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| \.contoso.com/www            | /\*                      | /          | /fwd/          | Foo          | /foo/bar/          |
| \.contoso.com/www**sub**     | /\*                      | /**Sub**   | /FWD/**Sub**   | /foo/**Sub**   | /foo/bar/**Sub**   |
| www \. contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /FWD/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| \.contoso.com/foo www         | /foo                     | /          | /fwd/          | Foo          | /foo/bar/          |
| \.contoso.com/foo/www        | Foo\*                  | /          | /fwd/          | Foo          | /foo/bar/          |
| \.**pasek** contoso.com/foo/www | Foo\*                  | /**słup**   | **pasek** /FWD/   | **pasek** /foo/   | **pasek** /foo/bar/   |

## <a name="optional-settings"></a>Ustawienia opcjonalne
Dostępne są dodatkowe ustawienia opcjonalne, które można także określić dla określonych ustawień reguły routingu:

* **Konfiguracja pamięci podręcznej** — Jeśli ta wartość jest wyłączona lub nie została określona, żądania zgodne z tą regułą routingu nie będą próbować korzystać z pamięci podręcznej, a zamiast tego będą zawsze pobierane z zaplecza. Przeczytaj więcej [na temat buforowania z przednimi drzwiami](front-door-caching.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
