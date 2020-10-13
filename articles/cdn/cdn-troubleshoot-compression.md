---
title: Rozwiązywanie problemów z kompresją plików w Azure CDN | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z kompresją plików w usłudze Azure Content Delivery Network. W tym artykule opisano kilka możliwych przyczyn.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f49af1488a0c044639a72fc2ea52ba0a47727a24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89433674"
---
# <a name="troubleshooting-cdn-file-compression"></a>Rozwiązywanie problemów związanych z kompresją pliku CDN
Ten artykuł pomaga w rozwiązywaniu problemów z [kompresją plików](cdn-improve-performance.md)w sieci CDN.

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure w [witrynie MSDN Azure i Stack Overflow forów](https://azure.microsoft.com/support/forums/). Alternatywnie możesz także zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i kliknij pozycję **Uzyskaj pomoc techniczną**.

## <a name="symptom"></a>Objaw
Kompresja dla punktu końcowego jest włączona, ale pliki są zwracane jako nieskompresowane.

> [!TIP]
> Aby sprawdzić, czy pliki są zwracane jako skompresowane, musisz użyć narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler) lub [Narzędzia deweloperskie](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)w przeglądarce.  Sprawdź nagłówki odpowiedzi HTTP zwrócone przez zawartość pamięci podręcznej sieci CDN.  Jeśli istnieje nagłówek o nazwie `Content-Encoding` z wartością **gzip**, **bzip2**lub **Wklęśnięcie**, zawartość jest skompresowana.
> 
> ![Nagłówek Content-Encoding](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Przyczyna
Istnieje kilka możliwych przyczyn, w tym:

* Żądana zawartość nie kwalifikuje się do kompresji.
* Kompresja nie jest włączona dla żądanego typu pliku.
* Żądanie HTTP nie zawierało nagłówka żądającego prawidłowego typu kompresji.
* Źródło wysyła zawartość fragmentaryczną.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
> [!TIP]
> Podobnie jak w przypadku wdrażania nowych punktów końcowych, zmiany konfiguracji usługi CDN trwają trochę czasu, aby propagować je w sieci.  Zwykle zmiany są stosowane w ciągu 90 minut.  Jeśli jest to pierwsze skonfigurowanie kompresji punktu końcowego usługi CDN, należy rozważyć oczekiwanie 1-2 godzin, aby upewnić się, że ustawienia kompresji zostały przekazane do punktów obecności. 
> 
> 

### <a name="verify-the-request"></a>Weryfikowanie żądania
Najpierw należy wykonać szybkie sprawdzanie Sanity na żądanie.  Możesz użyć [narzędzi programistycznych](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) przeglądarki, aby wyświetlić wykonywane żądania.

* Sprawdź, czy żądanie jest wysyłane na adres URL punktu końcowego, `<endpointname>.azureedge.net` a nie do źródła.
* Sprawdź, czy żądanie zawiera nagłówek **Accept-Encoding** , a wartość tego nagłówka zawiera **gzip**, **Wklęśnięcie**lub **bzip2**.

> [!NOTE]
> **Azure CDN z profilów Akamai** obsługują tylko kodowanie **gzip** .
> 
> 

![Nagłówki żądań usługi CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Weryfikowanie ustawień kompresji (standardowe profile usługi CDN)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy profil usługi CDN jest **standardem Azure CDN firmy Microsoft**, **Azure CDN Standard from Verizon**lub **Azure CDN Standard from Akamai** profile. 
> 
> 

Przejdź do punktu końcowego w [Azure Portal](https://portal.azure.com) i kliknij przycisk **Konfiguruj** .

* Sprawdź, czy kompresja jest włączona.
* Sprawdź, czy typ MIME dla zawartości do skompresowania jest uwzględniony na liście skompresowanych formatów.

![Ustawienia kompresji usługi CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Weryfikowanie ustawień kompresji (profile usługi CDN w warstwie Premium)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy profil usługi CDN jest w **Azure CDN Premium z poziomu profilu Verizon** .
> 
> 

Przejdź do punktu końcowego w [Azure Portal](https://portal.azure.com) i kliknij przycisk **Zarządzaj** .  Zostanie otwarty dodatkowy Portal.  Umieść wskaźnik myszy na **dużej karcie http** , a następnie umieść wskaźnik myszy nad menu wysuwanym **Ustawienia pamięci podręcznej** .  Kliknij pozycję **kompresja**. 

* Sprawdź, czy kompresja jest włączona.
* Sprawdź, czy lista **typów plików** zawiera rozdzielaną przecinkami listę typów MIME (bez spacji).
* Sprawdź, czy typ MIME dla zawartości do skompresowania jest uwzględniony na liście skompresowanych formatów.

![Ustawienia kompresji CDN Premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Sprawdź, czy zawartość jest buforowana (Verizon profile sieci CDN)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy profil usługi CDN jest **standardem Azure CDN Verizon** lub **Azure CDN Premium z profilu Verizon** .
> 
> 

Za pomocą narzędzi deweloperskich w przeglądarce sprawdź nagłówki odpowiedzi, aby upewnić się, że plik jest buforowany w regionie, w którym jest żądane.

* Sprawdź nagłówek odpowiedzi **serwera** .  Nagłówek powinien mieć format **platformy (pop/Server ID)**, jak pokazano w poniższym przykładzie.
* Sprawdź nagłówek odpowiedzi **X-cache** .  Nagłówek powinien zostać **osiągnięty**.  

![Nagłówki odpowiedzi CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Sprawdź, czy plik spełnia wymagania dotyczące wielkości (Verizon profile sieci CDN)
> [!NOTE]
> Ten krok ma zastosowanie tylko wtedy, gdy profil usługi CDN jest **standardem Azure CDN Verizon** lub **Azure CDN Premium z profilu Verizon** .
> 
> 

Aby można było kwalifikować się do kompresji, plik musi spełniać następujące wymagania dotyczące rozmiaru:

* Więcej niż 128 bajtów (długość zawartości: 128)
* Mniejsze niż 3 MB

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Sprawdź żądanie na serwerze pochodzenia dla nagłówka **Via**
Nagłówek **za pośrednictwem** protokołu HTTP wskazuje serwerowi sieci Web, że żądanie jest przesyłane przez serwer proxy.  Serwery sieci Web programu Microsoft IIS domyślnie nie kompresują odpowiedzi, gdy żądanie zawiera nagłówek **Via** .  Aby zastąpić to zachowanie, wykonaj następujące czynności:

* **IIS 6**: [Ustaw HCNOCOMPRESSIONFORPROXIES = "false" we właściwościach metabazy usług IIS](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 i up**: [w konfiguracji serwera należy ustawić wartość false dla opcji **noCompressionForHttp10** i **noCompressionForProxies** ](https://www.iis.net/configreference/system.webserver/httpcompression)

