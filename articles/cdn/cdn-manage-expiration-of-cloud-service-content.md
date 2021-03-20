---
title: Zarządzanie wygasaniem zawartości sieci Web w Azure CDN | Microsoft Docs
description: Dowiedz się, jak zarządzać wygasaniem zawartości usługi Azure Web Apps/Cloud Services, ASP.NET lub IIS w Azure CDN.
services: cdn
documentationcenter: .NET
author: asudbring
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: how-to
ms.date: 02/15/2018
ms.author: allensu
ms.openlocfilehash: d8eb450d2010bf2a525a26f1c5ff48f59732ce43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93240974"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Zarządzanie wygasaniem zawartości internetowej w usłudze Azure CDN
> [!div class="op_single_selector"]
> * [Zawartość sieci Web platformy Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Pliki z publicznie dostępnych źródeł serwerów sieci Web mogą być buforowane w usłudze Azure Content Delivery Network (CDN), dopóki nie upłynie czas wygaśnięcia (TTL). Czas wygaśnięcia jest określany przez `Cache-Control` nagłówek w odpowiedzi HTTP z serwera pochodzenia. W tym artykule opisano sposób ustawiania `Cache-Control` nagłówków dla Web Apps funkcji Microsoft Azure App Service, Cloud Services platformy Azure, aplikacji ASP.NET oraz witryn Internet Information Services (IIS), które są skonfigurowane w podobny sposób. Nagłówek można ustawić przy `Cache-Control` użyciu plików konfiguracyjnych lub programowo. 

Ustawienia pamięci podręcznej można również kontrolować przy użyciu Azure Portal przez ustawienie [reguł buforowania usługi CDN](cdn-caching-rules.md). Jeśli utworzysz co najmniej jedną regułę buforowania i ustawisz zachowanie buforowania w celu **przesłonięcia** lub **obejścia pamięci podręcznej**, ustawienia buforowania podane w tym artykule zostaną zignorowane. Aby uzyskać informacje na temat ogólnych pojęć dotyczących buforowania, zobacz [jak działa buforowanie](cdn-how-caching-works.md).

> [!TIP]
> Możesz ustawić brak czasu wygaśnięcia dla pliku. W takim przypadku Azure CDN automatycznie stosuje domyślny czas wygaśnięcia o wartości siedem dni, chyba że w Azure Portal skonfigurowano reguły buforowania. Ten domyślny czas TTL dotyczy tylko optymalizacji ogólnego dostarczania w sieci Web. W przypadku optymalizacji dużych plików domyślny czas wygaśnięcia wynosi jeden dzień, a w przypadku optymalizacji przesyłania strumieniowego multimediów domyślny czas wygaśnięcia wynosi jeden rok.
> 
> Aby uzyskać więcej informacji o tym, jak działa Azure CDN, aby przyspieszyć dostęp do plików i innych zasobów, zobacz [Omówienie usługi Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ustawianie nagłówków Cache-Control przy użyciu reguł buforowania usługi CDN
Preferowaną metodą ustawiania nagłówka serwera sieci Web `Cache-Control` jest użycie reguł buforowania w Azure Portal. Aby uzyskać więcej informacji na temat reguł buforowania usługi CDN, zobacz [kontrola Azure CDN buforowania przy użyciu reguł buforowania](cdn-caching-rules.md).

> [!NOTE] 
> Reguły buforowania są dostępne tylko dla **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profile. W przypadku **Azure CDN Premium z profilów Verizon** należy użyć [aparatu reguł Azure CDN](./cdn-verizon-premium-rules-engine.md) w portalu **zarządzania** , aby korzystać z podobnych funkcji.

**Aby przejść do strony reguły buforowania usługi CDN**:

1. W Azure Portal wybierz profil CDN, a następnie wybierz punkt końcowy dla serwera sieci Web.

1. W lewym okienku w obszarze Ustawienia zaznacz pole **Reguły buforowania**.

   ![Przycisk reguły buforowania usługi CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Zostanie wyświetlona strona **Reguły buforowania**.

   ![Strona buforowania usługi CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Aby ustawić nagłówki Cache-Control serwera sieci Web przy użyciu globalnych zasad buforowania:**

1. W obszarze **globalne reguły buforowania** Ustaw **zachowanie buforowania ciągu zapytania** , aby **zignorować ciągi zapytań** i ustawić **zachowanie buforowania** w celu **przesłonięcia**.
      
1. W przypadku **czasu wygaśnięcia pamięci podręcznej** wprowadź 3600 w polu **sekundy** lub 1 w polu **godziny** . 

   ![Przykład globalnych zasad buforowania usługi CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Ta Globalna reguła buforowania ustawia czas trwania pamięci podręcznej o jedną godzinę i ma wpływ na wszystkie żądania do punktu końcowego. Zastępuje wszystkie `Cache-Control` nagłówki lub `Expires` http, które są wysyłane przez serwer pochodzenia określony przez punkt końcowy.   

1. Wybierz pozycję **Zapisz**.

**Aby ustawić nagłówki Cache-Control pliku serwera sieci Web przy użyciu niestandardowych reguł buforowania:**

1. W obszarze **niestandardowe reguły buforowania** Utwórz dwa warunki dopasowywania:

     a. W przypadku pierwszego warunku dopasowywania Ustaw **warunek dopasowania** na wartość **Path** , a następnie wprowadź `/webfolder1/*` dla **wartości dopasowywania**. Ustaw **zachowanie buforowania** w celu **przesłonięcia** i wprowadź 4 w polu **dni** .

     b. W przypadku drugiego warunku dopasowywania Ustaw **warunek dopasowania** na wartość **Path** i wprowadź `/webfolder1/file1.txt` dla **wartości dopasowywania**. Ustaw **zachowanie buforowania** w celu **przesłonięcia** i wprowadź wartość 2 w polu **dni** .

    ![Przykład niestandardowych reguł buforowania usługi CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Pierwsza Niestandardowa reguła buforowania ustawia czas trwania pamięci podręcznej wynoszący cztery godziny dla wszystkich plików w `/webfolder1` folderze na serwerze źródłowym określonym przez punkt końcowy. Druga reguła zastępuje pierwszą regułę `file1.txt` tylko dla pliku i ustawia dla niej okres istnienia pamięci podręcznej wynoszący 2 godziny.

1. Wybierz pozycję **Zapisz**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Ustawianie nagłówków Cache-Control przy użyciu plików konfiguracji
W przypadku zawartości statycznej, takiej jak obrazy i arkusze stylów, można kontrolować częstotliwość aktualizacji, modyfikując **applicationHost.config** lub **Web.config** pliki konfiguracji dla aplikacji sieci Web. Aby ustawić `Cache-Control` nagłówek zawartości, użyj `<system.webServer>/<staticContent>/<clientCache>` elementu w obu plikach.

### <a name="using-applicationhostconfig-files"></a>Korzystanie z plików ApplicationHost.config
Plik **ApplicationHost.config** jest głównym plikiem systemu KONFIGURACYJNEGO usług IIS. Ustawienia konfiguracji w pliku **ApplicationHost.config** mają wpływ na wszystkie aplikacje w lokacji, ale są przesłonięte przez ustawienia wszystkich **Web.config** plików istniejących dla aplikacji sieci Web.

### <a name="using-webconfig-files"></a>Korzystanie z plików Web.config
Za pomocą pliku **Web.config** można dostosować sposób zachowania całej aplikacji sieci Web lub określonego katalogu w aplikacji sieci Web. Zazwyczaj masz co najmniej jeden plik **Web.config** w folderze głównym aplikacji sieci Web. Dla każdego pliku **Web.config** w określonym folderze Ustawienia konfiguracji mają wpływ na wszystko w tym folderze i jego podfolderach, chyba że zostaną zastąpione na poziomie podfolderu przez inny plik **Web.config** . 

Na przykład można ustawić `<clientCache>` element w pliku **Web.config** w folderze głównym aplikacji sieci Web w celu buforowania całej zawartości statycznej w aplikacji sieci Web przez trzy dni. Możesz również dodać plik **Web.config** w podfolderze o większej liczbie zmiennych (na przykład `\frequent` ) i ustawić jego `<clientCache>` element do buforowania zawartości podfolderu przez sześć godzin. Wynikiem jest to, że zawartość całej witryny sieci Web jest buforowana przez trzy dni, z wyjątkiem zawartości w `\frequent` katalogu, która jest buforowana przez zaledwie sześć godzin.  

Poniższy przykład pliku konfiguracji XML pokazuje, jak ustawić element, `<clientCache>` Aby określić maksymalny wiek trzech dni:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Aby użyć atrybutu **cacheControlMaxAge** , należy ustawić wartość atrybutu **cacheControlMode** na `UseMaxAge` . To ustawienie spowodowało dodanie nagłówka HTTP i dyrektywy `Cache-Control: max-age=<nnn>` do odpowiedzi. Format wartości TimeSpan dla atrybutu **cacheControlMaxAge** jest `<days>.<hours>:<min>:<sec>` . Jego wartość jest konwertowana na sekundy i jest używana jako wartość `Cache-Control` `max-age` dyrektywy. Aby uzyskać więcej informacji na temat `<clientCache>` elementu, zobacz [pamięć \<clientCache> podręczna klienta ](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Programowe Ustawianie nagłówków Cache-Control
W przypadku aplikacji ASP.NET można programowo kontrolować zachowanie buforowania usługi CDN przez ustawienie właściwości **HttpResponse. cache** interfejsu API platformy .NET. Aby uzyskać informacje na temat właściwości **HttpResponse. cache** , zobacz [HttpResponse. cache Property](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) i [HttpCachePolicy class](/dotnet/api/system.web.httpcachepolicy).  

Aby programowo przechować zawartość aplikacji w ASP.NET, wykonaj następujące kroki:
   1. Sprawdź, czy zawartość jest oznaczona jako buforowana przez ustawienie `HttpCacheability` do `Public` . 
   1. Ustaw moduł sprawdzania poprawności pamięci podręcznej, wywołując jedną z następujących `HttpCachePolicy` metod:
      - Wywołaj, `SetLastModified` Aby ustawić wartość sygnatury czasowej dla `Last-Modified` nagłówka.
      - Wywołanie `SetETag` ustawiania wartości dla `ETag` nagłówka.
   1. Opcjonalnie określ czas wygaśnięcia pamięci podręcznej, wywołując, `SetExpires` Aby ustawić wartość dla `Expires` nagłówka. W przeciwnym razie stosowane są domyślne algorytmy heurystyczne pamięci podręcznej opisane wcześniej w tym dokumencie.

Na przykład, aby buforować zawartość przez jedną godzinę, Dodaj następujący kod w języku C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testowanie nagłówka Cache-Control
Można łatwo zweryfikować ustawienia czasu wygaśnięcia zawartości sieci Web. Za pomocą [narzędzi deweloperskich](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)przeglądarki Sprawdź, czy zawartość sieci Web zawiera `Cache-Control` Nagłówek odpowiedzi. Do sprawdzenia nagłówków odpowiedzi można także użyć narzędzia, takiego jak **Wget**, [Poster](https://www.getpostman.com/)lub [programu Fiddler](https://www.telerik.com/fiddler) .

## <a name="next-steps"></a>Następne kroki
* [Przeczytaj szczegóły dotyczące elementu **clientCache**](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Zapoznaj się z dokumentacją właściwości **HttpResponse. cache**](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Zapoznaj się z dokumentacją **klasy HttpCachePolicy**](/dotnet/api/system.web.httpcachepolicy)  
* [Informacje o pojęciach dotyczących buforowania](cdn-how-caching-works.md)
