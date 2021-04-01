---
title: Samouczek — dodawanie usługi Azure CDN do aplikacji internetowej usługi Azure App Service | Microsoft Docs
description: W tym samouczku usługa Azure Content Delivery Network (CDN) jest dodawana do aplikacji internetowej usługi Azure App Service, aby buforować i dostarczać pliki statyczne z serwerów znajdujących się blisko klientów na całym świecie.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 3f1dacb57931913edfb181f023bdf98717777d50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97562246"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Samouczek: dodawanie usługi Azure CDN do aplikacji internetowej usługi Azure App Service

W tym samouczku przedstawiono sposób dodawania usługi [Microsoft Azure Content Delivery Network (CDN)](cdn-overview.md) do [aplikacji internetowej w usłudze Azure App Service](../app-service/overview.md). Web Apps to usługa do hostowania aplikacji internetowych, interfejsów API REST i zapleczy aplikacji mobilnych. 

Oto strona główna przykładowej statycznej witryny HTML, z którą będziesz pracować:

![Strona główna przykładowej aplikacji](media/cdn-add-to-web-app/sample-app-home-page.png)

Zawartość:

> [!div class="checklist"]
> * Tworzenie punktu końcowego usługi CDN.
> * Odświeżanie elementów zawartości zapisanych w pamięci podręcznej.
> * Używanie ciągów zapytań do sterowania zbuforowanymi wersjami.


## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- [Zainstaluj oprogramowanie Git](https://git-scm.com/)
- [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Tworzenie aplikacji internetowej

Aby utworzyć aplikację internetową, z którą będziesz pracować, postępuj zgodnie z instrukcjami opisanymi w artykule [Tworzenie statycznej witryny HTML — szybki start](../app-service/quickstart-html.md) do kroku **Przechodzenie do aplikacji**.

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Otwórz przeglądarkę i przejdź do witryny [Azure Portal](https://portal.azure.com).

### <a name="dynamic-site-acceleration-optimization"></a>Optymalizacja przyspieszania witryn dynamicznych
Aby zoptymalizować punkt końcowy usługi CDN na potrzeby przyspieszania witryn dynamicznych (DSA), należy użyć [portalu CDN](cdn-create-new-endpoint.md) do utworzenia profilu i punktu końcowego. Dzięki [optymalizacji DSA](cdn-dynamic-site-acceleration.md) wydajność stron internetowych z zawartością dynamiczną jest zauważalnie większa. Aby uzyskać instrukcje dotyczące sposobu optymalizacji punktu końcowego usługi CDN na potrzeby funkcji DSA w portalu usługi CDN, zobacz [Konfigurowanie punktu końcowego usługi CDN w celu przyspieszenia dostarczania plików dynamicznych](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files). Jeśli jednak nie chcesz zoptymalizować nowego punktu końcowego, możesz skorzystać z portalu aplikacji internetowych, aby go utworzyć, wykonując kroki znajdujące się w następnej sekcji. Należy zauważyć, że w przypadku profilów usługi **Azure CDN from Verizon** nie można zmienić ustawień optymalizacji punktu końcowego usługi CDN po jego utworzeniu.

## <a name="create-a-cdn-profile-and-endpoint"></a>Tworzenie profilu i punktu końcowego usługi CDN

Na lewym panelu nawigacyjnym wybierz pozycję **App Services**, a następnie wybierz aplikację utworzoną w samouczku [tworzenie statycznej witryny HTML — szybki start](../app-service/quickstart-html.md).

![Wybieranie aplikacji usługi App Service w portalu](media/cdn-add-to-web-app/portal-select-app-services.png)

Na stronie **App Service** w sekcji **Ustawienia** wybierz pozycję **Sieć > Skonfiguruj usługę Azure CDN dla aplikacji**.

![Wybieranie usługi CDN w portalu](media/cdn-add-to-web-app/portal-select-cdn.png)

Na stronie **Azure Content Delivery Network** określ ustawienia dla **nowego punktu końcowego** zgodnie z informacjami podanymi w tabeli.

![Tworzenie profilu i punktu końcowego w portalu](media/cdn-add-to-web-app/portal-new-endpoint.png)

| Ustawienie | Sugerowana wartość | Opis |
| ------- | --------------- | ----------- |
| **Profil CDN** | myCDNProfile | Profil CDN jest kolekcją punktów końcowych usługi CDN znajdujących się w tej samej warstwie cenowej. |
| **Warstwa cenowa** | Akamai — Standardowa | [Warstwa cenowa](cdn-features.md) określa dostawcę i dostępne funkcje. W tym samouczku jest używana warstwa *Standard Akamai*. |
| **Nazwa punktu końcowego usługi CDN** | Dowolna unikatowa nazwa w domenie azureedge.net | Dostęp do buforowanych zasobów można uzyskać w domenie *&lt; EndpointName &gt;*. azureedge.NET.

Wybierz pozycję **Utwórz**, aby utworzyć profil CDN.

Na platformie Azure zostanie utworzony profil i punkt końcowy. Nowy punkt końcowy zostanie wyświetlony na liście **Punkty końcowe**, a kiedy zostanie zaprowizowany, będzie miał stan **Uruchomiono**.

![Nowy punkt końcowy na liście](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Testowanie punktu końcowego usługi CDN

 Punkt końcowy nie jest natychmiast dostępny do użycia, ponieważ propagacja rejestracji zajmuje trochę czasu: 
   - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
   - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
   - W przypadku profili usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 90 minut. 

Przykładowa aplikacja ma plik *index.html* i foldery *css*, *img* i *js*, które zawierają inne statyczne elementy zawartości. Ścieżki zawartości dla wszystkich tych plików są takie same w punkcie końcowym usługi CDN. Na przykład oba następujące adresy URL umożliwiają dostęp do pliku *bootstrap.css* w folderze *css*:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

W przeglądarce przejdź pod następujący adres URL:

```
http://<endpointname>.azureedge.net/index.html
```

![Strona główna aplikacji przykładowej udostępnianej przez usługę CDN](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 Zobaczysz tę samą stronę, którą uruchomiono wcześniej w aplikacji internetowej platformy Azure. Usługa Azure CDN pobrała elementy zawartości oryginalnej aplikacji internetowej i udostępnia je za pośrednictwem punktu końcowego usługi CDN.

Odśwież tę stronę, aby się upewnić, że jest ona buforowana w usłudze CDN. Aby usługa CDN umieściła żądaną zawartość w pamięci podręcznej, czasami wymagane są dwa żądania tego samego elementu zawartości.

Aby uzyskać więcej informacji o sposobie tworzenia profilów i punktów końcowych usługi Azure CDN, zobacz [Wprowadzenie do usługi Azure CDN](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Przeczyszczanie usługi CDN

Usługa CDN okresowo odświeża swoje zasoby z oryginalnej aplikacji internetowej zgodnie z konfiguracją czasu wygaśnięcia (TTL). Domyślny czas wygaśnięcia wynosi siedem dni.

Czasami może zajść konieczność odświeżenia usługi CDN przed upływem czasu wygaśnięcia — na przykład podczas wdrażania zaktualizowanej zawartości w aplikacji internetowej. Aby wyzwolić odświeżanie, ręcznie przeczyść zasoby usługi CDN. 

W tej części samouczka zostanie wdrożona zmiana w aplikacji internetowej i nastąpi przeczyszczenie usługi CDN w celu wyzwolenia odświeżenia pamięci podręcznej tej usługi.

### <a name="deploy-a-change-to-the-web-app"></a>Wdrażanie zmiany w aplikacji internetowej

Otwórz plik *index.html* i dodaj ciąg *- V2* do nagłówka H1, jak pokazano w poniższym przykładzie: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Zatwierdź zmianę i wdróż ją w aplikacji internetowej.

```bash
git commit -am "version 2"
git push azure main
```

Po zakończeniu wdrażania przejdź do adresu URL aplikacji internetowej, aby zobaczyć zmianę.

```
http://<appname>.azurewebsites.net/index.html
```

![Ciąg V2 w tytule aplikacji internetowej](media/cdn-add-to-web-app/v2-in-web-app-title.png)

Jeśli przejdziesz do adresu URL punktu końcowego usługi CDN dla strony głównej, nie zobaczysz zmiany, ponieważ wersja buforowana w usłudze CDN jeszcze nie wygasła. 

```
http://<endpointname>.azureedge.net/index.html
```

![Brak ciągu V2 w tytule w usłudze CDN](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Przeczyszczanie usługi CDN w portalu

Aby w usłudze CDN wyzwolić zaktualizowanie zbuforowanej wersji, przeczyść usługę CDN.

Na lewym panelu nawigacyjnym portalu wybierz pozycję **Grupy zasobów**, a następnie wybierz grupę zasobów utworzoną na potrzeby aplikacji internetowej (myResourceGroup).

![Wybieranie grupy zasobów](media/cdn-add-to-web-app/portal-select-group.png)

Na liście zasobów wybierz punkt końcowy usługi CDN.

![Wybieranie punktu końcowego](media/cdn-add-to-web-app/portal-select-endpoint.png)

W górnej części strony **Punkt końcowy** wybierz pozycję **Przeczyść**.

![Wybieranie pozycji Przeczyść](media/cdn-add-to-web-app/portal-select-purge.png)

Wprowadź ścieżki zawartości, które chcesz przeczyścić. Możesz podać pełną ścieżkę, aby przeczyścić pojedynczy plik, lub segment ścieżki, aby przeczyścić i odświeżyć całą zawartość folderu. Ponieważ zmieniono plik *index.html*, upewnij się, że znajduje się na jednej ze ścieżek.

W dolnej części strony wybierz pozycję **Przeczyść**.

![Strona przeczyszczania](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Sprawdzanie zaktualizowania usługi CDN

Poczekaj na zakończenie przetwarzania żądania przeczyszczenia, co zwykle trwa kilka minut. Aby wyświetlić bieżący stan, wybierz ikonę dzwonka w górnej części strony. 

![Powiadomienie o przeczyszczaniu](media/cdn-add-to-web-app/portal-purge-notification.png)

Po przejściu do adresu URL punktu końcowego usługi CDN dla pliku *index.html* zobaczysz element *V2* dodany do tytułu na stronie głównej, co oznacza, że pamięć podręczna usługi CDN została odświeżona.

```
http://<endpointname>.azureedge.net/index.html
```

![Ciąg V2 w tytule w usłudze CDN](media/cdn-add-to-web-app/v2-in-cdn-title.png)

Aby uzyskać więcej informacji, zobacz [Przeczyszczanie punktu końcowego usługi Azure CDN](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Używanie ciągów zapytań do sterowania wersjami zawartości

Usługa Azure CDN oferuje następujące opcje zachowania podczas buforowania:

* Ignoruj ciągi zapytań
* Pomiń buforowanie dla ciągów zapytań
* Buforuj każdy unikatowy adres URL 

Pierwsza opcja to opcja domyślna. W tym przypadku istnieje tylko jedna zbuforowana wersja elementu zawartości, niezależnie od tego, jakiego ciągu zapytania użyto w adresie URL. 

W tej części samouczka zostanie zmienione zachowanie buforowania w taki sposób, że w pamięci podręcznej będzie umieszczany każdy unikatowy adres URL.

### <a name="change-the-cache-behavior"></a>Zmienianie zachowania buforowania

W witrynie Azure Portal na stronie **Punkt końcowy usługi CDN** wybierz pozycję **Pamięć podręczna**.

Z listy rozwijanej **Zachowanie buforowania ciągu kwerendy** wybierz pozycję **Buforuj każdy unikatowy adres URL**.

Wybierz pozycję **Zapisz**.

![Wybieranie zachowania buforowania ciągów zapytań](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Sprawdzanie, czy unikatowe adresy URL są buforowane osobno

W przeglądarce przejdź do strony głównej w punkcie końcowym usługi CDN i dołącz ciąg zapytania: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Usługa Azure CDN zwraca bieżącą zawartość aplikacji internetowej, w tym ciąg *V2* w nagłówku. 

Odśwież tę stronę, aby się upewnić, że jest ona buforowana w usłudze CDN. 

Otwórz plik *index.html*, zmień ciąg *V2* na *V3*, a następnie wdróż zmianę. 

```bash
git commit -am "version 3"
git push azure main
```

W przeglądarce przejdź do adresu URL punktu końcowego usługi CDN, korzystając z nowego ciągu zapytania, na przykład `q=2`. Usługa Azure CDN pobiera bieżący plik *index.html* i wyświetla ciąg *V3*. Jednak jeśli przejdziesz do punktu końcowego usługi CDN, korzystając z ciągu zapytania `q=1`, zobaczysz ciąg *V2*.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 w tytule w usłudze CDN, ciąg zapytania 2](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 w tytule w usłudze CDN, ciąg zapytania 1](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

W tych danych wyjściowych widać, że każdy ciąg zapytania jest traktowany inaczej:

* Wcześniej użyto ciągu q=1, dlatego zwrócona została zbuforowana zawartość (V2).
* Ciąg q=2 jest nowy, więc pobierana i zwracana jest najnowsza zawartość aplikacji internetowej (V3).

Aby uzyskać więcej informacji, zobacz [Control Azure CDN caching behavior with query strings](../cdn/cdn-query-string.md) (Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Tworzenie punktu końcowego usługi CDN.
> * Odświeżanie elementów zawartości zapisanych w pamięci podręcznej.
> * Używanie ciągów zapytań do sterowania zbuforowanymi wersjami.

Zapoznaj się z następującymi artykułami, aby dowiedzieć się, jak zoptymalizować wydajność usługi CDN:

> [!div class="nextstepaction"]
> [Samouczek: dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN](cdn-map-content-to-custom-domain.md)