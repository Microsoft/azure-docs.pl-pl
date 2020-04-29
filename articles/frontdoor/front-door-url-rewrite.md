---
title: Moje drzwi na platformie Azure — ponowne zapisywanie adresów URL | Microsoft Docs
description: Ten artykuł pomaga zrozumieć, w jaki sposób usługa Azure Front drzwiczki ponownie zapisuje adresy URL dla tras, jeśli są skonfigurowane.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 1e5bd565be7a1cabf08ddf33c65eb12b5294249f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471476"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Ponowne zapisywanie adresów URL (ścieżka do przesyłania dalej)
Funkcja Azure Front Drzwiczks obsługuje ponowne zapisywanie adresów URL, umożliwiając Konfigurowanie opcjonalnej **niestandardowej ścieżki przekazywania** , która będzie używana podczas konstruowania żądania do przesyłania dalej do zaplecza. Domyślnie jeśli nie ma określonej niestandardowej ścieżki przesyłania dalej, usługa Front Door kopiuje przychodzącą ścieżkę adresu URL do adresu URL użytego w przesłanym dalej żądaniu. Nagłówek Host użyty w przesłanym dalej żądaniu jest skonfigurowany dla wybranego zaplecza. Odczytaj [nagłówek hosta zaplecza](front-door-backend-pool.md#hostheader) , aby dowiedzieć się, co robi i jak można go skonfigurować.

Zaawansowana część adresu URL do ponownego zapisu przy użyciu ścieżki niestandardowego przesyłania dalej polega na skopiowaniu dowolnej części ścieżki przychodzącej dopasowanej do ścieżki wieloznacznej do przesłanej dalej ścieżki (te segmenty ścieżki są **zielonymi** segmentami w poniższym przykładzie):
</br>
![Ponowne zapisywanie adresu URL usługi Azure Front-drzwiczk][1]

## <a name="url-rewrite-example"></a>Przykład ponownego zapisywania adresu URL
Rozważ użycie reguły routingu z skonfigurowanymi następującymi hostami frontonu i ścieżkami:

| Hosts      | Ścieżki       |
|------------|-------------|
| contoso.com\.www | /\*         |
|            | /foo        |
|            | Foo\*     |
|            | /foo/bar/\* |

W pierwszej kolumnie poniższej tabeli przedstawiono przykłady żądań przychodzących, a druga kolumna pokazuje, co to jest "najbardziej specyficzna" ścieżka do trasy.  Trzecia i kolejna kolumna pierwszego wiersza tabeli to przykłady skonfigurowanych **niestandardowych ścieżek przesyłania dalej**, z pozostałymi wierszami w tych kolumnach, które reprezentują przykłady ścieżki przesłane dalej, jeśli pasują do żądania w tym wierszu.

Na przykład jeśli odczytasz w drugim wierszu, oznacza to, że w przypadku żądania `www.contoso.com/sub`przychodzącego, jeśli niestandardowa ścieżka `/`przesyłania dalej to, przekazana ścieżka `/sub`będzie. W przypadku niestandardowej ścieżki `/fwd/`przesyłania dalej zostanie `/fwd/sub`przekazana ścieżka. I tak dalej, dla pozostałych kolumn. **Wyróżnione** fragmenty ścieżek poniżej reprezentują fragmenty, które są częścią dopasowania symboli wieloznacznych.


| Żądanie przychodzące       | Najbardziej Szczegółowa ścieżka dopasowania | /          | /fwd/          | Foo          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| contoso.com/\.www            | /\*                      | /          | /fwd/          | Foo          | /foo/bar/          |
| contoso.com/\.www**sub**     | /\*                      | /**Sub**   | /FWD/**Sub**   | /foo/**Sub**   | /foo/bar/**Sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /FWD/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| contoso.com/foo\.www         | /foo                     | /          | /fwd/          | Foo          | /foo/bar/          |
| contoso.com/foo/\.www        | Foo\*                  | /          | /fwd/          | Foo          | /foo/bar/          |
| pasek\.contoso.com/foo/**bar** www | Foo\*                  | /**słup**   | **pasek** /FWD/   | **pasek** /foo/   | **pasek** /foo/bar/   |


## <a name="optional-settings"></a>Ustawienia opcjonalne
Dostępne są dodatkowe ustawienia opcjonalne, które można także określić dla określonych ustawień reguły routingu:

* **Konfiguracja pamięci podręcznej** — Jeśli ta wartość jest wyłączona lub nie została określona, żądania zgodne z tą regułą routingu nie będą próbować korzystać z zawartości w pamięci podręcznej, a zamiast tego będą zawsze pobierane z zaplecza. Przeczytaj więcej [na temat buforowania z przednimi drzwiami](front-door-caching.md).



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg