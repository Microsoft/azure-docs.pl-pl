---
title: Rozwiązywanie problemów Azure CDN punkty końcowe — kod stanu 404
description: Dowiedz się, jak rozwiązywać problemy z punktami końcowymi usługi Azure Content Delivery Network, które zwracają kody stanu odpowiedzi HTTP 404.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d6ad0b8b37bd4f04c22ed52d4ac6717202f22889
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192489"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Rozwiązywanie problemów Azure CDN punkty końcowe zwracające kod stanu 404
Ten artykuł umożliwia rozwiązywanie problemów z punktami końcowymi usługi Azure Content Delivery Network (CDN), które zwracają kody stanu odpowiedzi HTTP 404.

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure w [witrynie MSDN Azure i Stack Overflow forów](https://azure.microsoft.com/support/forums/). Alternatywnie możesz także zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.

## <a name="symptom"></a>Objaw
Utworzono profil sieci CDN i punkt końcowy, ale zawartość nie jest dostępna w sieci CDN. Użytkownicy próbujący uzyskać dostęp do zawartości za pośrednictwem adresu URL usługi CDN otrzymują kod stanu HTTP 404. 

## <a name="cause"></a>Przyczyna
Istnieje kilka możliwych przyczyn, w tym:

* Pochodzenie pliku nie jest widoczne dla sieci CDN.
* Punkt końcowy jest niepoprawnie skonfigurowany, powodując, że sieć CDN będzie wyglądać w niewłaściwym miejscu.
* Host odrzuca nagłówek hosta z sieci CDN.
* Punkt końcowy nie miał czasu na propagację w całej sieci CDN.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
> [!IMPORTANT]
> Po utworzeniu punktu końcowego usługi CDN nie będzie on natychmiast dostępny do użycia, ponieważ trwa przekazanie rejestracji przez sieć CDN:
> - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
> - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
> - W przypadku profili usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 90 minut. 
> 
> Jeśli wykonasz kroki opisane w tym dokumencie, a nadal otrzymujesz 404 odpowiedzi, rozważ poczekaj kilka godzin, aby ponownie sprawdzić przed otwarciem biletu pomocy technicznej.
> 
> 

### <a name="check-the-origin-file"></a>Sprawdź plik pierwotny
Najpierw sprawdź, czy plik pamięci podręcznej jest dostępny na serwerze źródłowym i jest publicznie dostępny w Internecie. Najszybszym sposobem jest otwarcie przeglądarki w sesji prywatnej lub incognito i przechodzenie bezpośrednio do pliku. Wpisz lub wklej adres URL w polu adres i sprawdź, czy jego wynikiem jest oczekiwany plik. Załóżmy na przykład, że masz plik na koncie usługi Azure Storage, dostępnym pod adresem https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Jeśli można pomyślnie załadować zawartość tego pliku, przeszedł test.

![To wszystko!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Chociaż jest to najszybszy i najprostszy sposób na zweryfikowanie, że plik jest dostępny publicznie, niektóre konfiguracje sieci w organizacji mogą sprawiać, że plik jest publicznie dostępny, w rzeczywistości tylko widoczny dla użytkowników sieci (nawet jeśli jest hostowany na platformie Azure). Aby upewnić się, że nie jest to przypadek, Przetestuj plik za pomocą zewnętrznej przeglądarki, takiej jak urządzenie przenośne, które nie jest połączone z siecią organizacji lub maszynę wirtualną na platformie Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Sprawdź ustawienia źródła
Po sprawdzeniu, czy plik jest publicznie dostępny w Internecie, sprawdź ustawienia pochodzenia. W [witrynie Azure Portal](https://portal.azure.com)przejdź do swojego profilu CDN, a następnie wybierz punkt końcowy, którego dotyczy problem. Na stronie będącej wynikiem **punktu końcowego** wybierz źródło.  

![Strona punktu końcowego z wyróżnioną pozycją Origin](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Zostanie wyświetlona strona **Źródło** . 

![Strona początkowa](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Typ źródła i nazwa hosta
Sprawdź, czy wartości **typu źródła** i **nazwy hosta źródła** są poprawne. W tym przykładzie https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, część nazwy hosta adresu URL to *cdndocdemo.blob.Core.Windows.NET*, co jest poprawne. Ponieważ magazyn platformy Azure, aplikacja sieci Web i źródła usługi w chmurze używają wartości listy rozwijanej dla pola **Nazwa hosta pochodzenia** , niepoprawna pisownia nie jest problemem. Jeśli jednak używasz źródła niestandardowego, upewnij się, że nazwa hosta jest wpisana poprawnie.

#### <a name="http-and-https-ports"></a>Porty HTTP i HTTPS
Sprawdź porty **http** i **https**. W większości przypadków 80 i 443 są poprawne i nie będzie konieczne wprowadzanie żadnych zmian.  Jeśli jednak serwer pochodzenia nasłuchuje na innym porcie, należy go przedstawić w tym miejscu. Jeśli nie masz pewności, Wyświetl adres URL pliku pierwotnego. Specyfikacje protokołu HTTP i HTTPS używają portów 80 i 443 jako wartości domyślnych. W przykładowym adresie URL, https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, port nie jest określony, więc przyjmuje się wartość domyślną 443 i ustawienia są poprawne.  

Załóżmy jednak, że adres URL testowanego wcześniej pliku jest http: \/ /www.contoso.com:8080/file.txt. Zwróć uwagę na *: 8080* część na końcu segmentu nazwy hosta. Ten numer instruuje przeglądarkę, aby używała portu 8080 do łączenia się z serwerem sieci Web w \. contoso.com www. w związku z tym należy wprowadzić *8080* w polu **Port http** . Należy pamiętać, że te ustawienia portów mają wpływ tylko na port wykorzystywany przez punkt końcowy do pobierania informacji ze źródła.

> [!NOTE]
> **Standard Azure CDN z** punktów końcowych Akamai nie zezwala na pełny zakres portów TCP dla źródeł.  Lista niedozwolonych portów źródłowych znajduje się w artykule [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Azure CDN from Akamai — dozwolone porty źródłowe).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Sprawdź ustawienia punktu końcowego
Na stronie **punkt końcowy** wybierz przycisk **Konfiguruj** .

![Strona punktu końcowego z wyróżnionym przyciskiem Konfiguruj](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Zostanie wyświetlona strona **Konfiguracja** punktu końcowego usługi CDN.

![Konfiguruj stronę](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoły
W przypadku **protokołów** Sprawdź, czy jest wybrany protokół używany przez klientów. Ponieważ ten sam protokół używany przez klienta jest używany do uzyskiwania dostępu do źródła, ważne jest, aby porty pochodzenia zostały prawidłowo skonfigurowane w poprzedniej sekcji. Punkt końcowy usługi CDN nasłuchuje tylko na domyślnych portach HTTP i HTTPS (80 i 443), niezależnie od portów pochodzenia.

Wróćmy do naszego hipotetycznego przykładu przy użyciu protokołu http: \/ /www.contoso.com:8080/file.txt.  Jak pamiętasz, firma Contoso określiła *8080* jako swój port HTTP, ale przypuśćmy również, że określono *44300* jako swój port HTTPS.  Jeśli utworzono punkt końcowy o nazwie *contoso*, nazwa hosta punktu końcowego usługi CDN byłaby *contoso.azureedge.NET*.  Żądanie http: \/ /contoso.azureedge.net/file.txt jest żądaniem http, więc punkt końcowy użyje protokołu HTTP na porcie 8080, aby pobrać go ze źródła.  Bezpieczne żądanie za pośrednictwem protokołu HTTPS, https: \/ /contoso.azureedge.net/file.txt, spowoduje, że punkt końcowy użyje protokołu HTTPS na porcie 44300 podczas pobierania pliku z lokalizacji źródłowej.

#### <a name="origin-host-header"></a>Nagłówek hosta źródła
**Nagłówek hosta źródła** jest wartością nagłówka hosta wysłaną do źródła przy użyciu każdego żądania.  W większości przypadków powinna być taka sama jak **Nazwa hosta** , który został wcześniej zweryfikowany.  Niepoprawna wartość w tym polu nie powoduje ogólnie 404 Stanów, ale prawdopodobnie spowoduje inne Stany 4xx, w zależności od tego, czego oczekuje.

#### <a name="origin-path"></a>Ścieżka do źródła
Na koniec należy zweryfikować **ścieżkę pochodzenia**.  Domyślnie to pole jest puste.  Tego pola należy używać tylko wtedy, gdy chcesz zawęzić zakres zasobów hostowanych przez źródło, które mają być dostępne w sieci CDN.  

W przykładowym punkcie końcowym chcemy, aby wszystkie zasoby na koncie magazynu były dostępne, a **ścieżka pierwotna** została pozostawiona puste.  Oznacza to, że żądanie do protokołu https: \/ /cdndocdemo.azureedge.net/publicblob/lorem.txt skutkuje połączeniem z punktu końcowego do cdndocdemo.Core.Windows.NET, który żąda */publicblob/lorem.txt*.  Podobnie żądanie dotyczące protokołu https: \/ /cdndocdemo.azureedge.net/donotcache/status.png powoduje, że punkt końcowy żąda */donotcache/status.png* od źródła.

Ale co zrobić, jeśli nie chcesz używać sieci CDN dla każdej ścieżki w Twoim źródle?  Załóżmy, że chcesz uwidocznić ścieżkę *publicblob* .  Jeśli wprowadzimy */publicblob* w polu **ścieżka pierwotna** , spowoduje to, że punkt końcowy wstawy */publicblob* przed każdym żądaniem do źródła.  Oznacza to, że żądanie dotyczące protokołu https: \/ /cdndocdemo.azureedge.net/publicblob/lorem.txt teraz przyjmuje część żądania adresu URL, */publicblob/lorem.txt* i Dołącz */publicblob* do początku. Powoduje to żądanie */publicblob/publicblob/lorem.txt* ze źródła.  Jeśli ta ścieżka nie jest rozpoznawana jako rzeczywisty plik, Źródło zwróci stan 404.  Prawidłowy adres URL do pobrania lorem.txt w tym przykładzie rzeczywiście to https: \/ /cdndocdemo.azureedge.net/lorem.txt.  Należy zauważyć, że ścieżka */publicblob* nie jest używana w ogóle, ponieważ część żądania adresu URL jest */lorem.txt* a punkt końcowy dodaje */publicblob*, co spowodowało */publicblob/lorem.txt* żądania przesłanego do źródła.

