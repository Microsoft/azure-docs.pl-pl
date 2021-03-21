---
title: Rozwiązywanie problemów z kompresją plików na platformie Azure — warstwa standardowa/Premium
description: Dowiedz się, jak rozwiązywać problemy z kompresją plików na platformie Azure. W tym artykule opisano kilka możliwych przyczyn.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100232"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Rozwiązywanie problemów z kompresją plików w warstwie Standardowa/Premium platformy Azure

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Ten artykuł pomaga w rozwiązywaniu problemów z kompresją plików w warstwie Standardowa/Premium platformy Azure.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="symptom"></a>Objaw

Kompresja dla trasy jest włączona, ale pliki są zwracane jako nieskompresowane.

> [!TIP]
> Aby sprawdzić, czy pliki są zwracane jako skompresowane, musisz użyć narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler) lub [Narzędzia deweloperskie](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)w przeglądarce.  Sprawdź nagłówki odpowiedzi HTTP zwrócone przez zawartość pamięci podręcznej sieci CDN.  Jeśli istnieje nagłówek o nazwie `Content-Encoding` z wartością **gzip**, **bzip2** lub **Wklęśnięcie**, zawartość jest skompresowana.
> 
> ![Nagłówek Content-Encoding](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>Przyczyna

Istnieje kilka możliwych przyczyn, w tym:

* Żądana zawartość nie kwalifikuje się do kompresji.
* Kompresja nie jest włączona dla żądanego typu pliku.
* Żądanie HTTP nie zawierało nagłówka żądającego prawidłowego typu kompresji.
* Źródło wysyła zawartość fragmentaryczną.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

> [!TIP]
> Podobnie jak w przypadku wdrażania nowych punktów końcowych, AFD zmiany konfiguracji są czasochłonne.  Zwykle zmiany są stosowane w ciągu 90 minut.  Jeśli jest to pierwsze skonfigurowanie kompresji punktu końcowego usługi CDN, należy rozważyć oczekiwanie 1-2 godzin, aby upewnić się, że ustawienia kompresji zostały przekazane do punktów obecności. 
> 

### <a name="verify-the-request"></a>Weryfikowanie żądania

Najpierw należy sprawdzić żądanie. Możesz użyć **[narzędzi programistycznych](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** przeglądarki, aby wyświetlić wykonywane żądania.

* Sprawdź, czy żądanie jest wysyłane na adres URL punktu końcowego, `<endpointname>.z01.azurefd.net` a nie do źródła.
* Sprawdź, czy żądanie zawiera nagłówek **Accept-Encoding** , a wartość tego nagłówka zawiera **gzip**, **Wklęśnięcie** lub **bzip2**.

![Nagłówki żądań usługi CDN](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>Weryfikuj ustawienia kompresji

Przejdź do punktu końcowego w [Azure Portal](https://portal.azure.com) i wybierz przycisk **Konfiguruj** w panelu trasy. Sprawdź, czy kompresja jest **włączona**.

![Ustawienia kompresji usługi CDN](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Sprawdź żądanie na serwerze pochodzenia dla nagłówka **Via**

Nagłówek **za pośrednictwem** protokołu HTTP wskazuje serwerowi sieci Web, że żądanie jest przesyłane przez serwer proxy.  Serwery sieci Web programu Microsoft IIS domyślnie nie Kompresuj odpowiedzi, gdy żądanie zawiera nagłówek **Via** .  Aby zastąpić to zachowanie, wykonaj następujące czynności:

* **IIS 6**: Ustaw HcNoCompressionForProxies = "false" we właściwościach METABAZY usług IIS. Aby uzyskać więcej informacji, zobacz [kompresja usług IIS 6](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90)).
* **IIS 7 i w górę**: w konfiguracji serwera należy ustawić zarówno **noCompressionForHttp10** , jak i **noCompressionForProxies** na *false* . Aby uzyskać więcej informacji, zobacz, [kompresja HTTP](https://www.iis.net/configreference/system.webserver/httpcompression).
