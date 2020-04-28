---
title: Rozwiązywanie problemów z koligalnością sesji
titleSuffix: Azure Application Gateway
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z koligalnością sesji w usłudze Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: aa3617b30fe1ef9b4d4a6c5fe5aac51bff95bb92
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866677"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Rozwiązywanie problemów z koligalnością sesji bramy aplikacji platformy Azure

Dowiedz się, jak zdiagnozować i rozwiązać problemy koligacji sesji za pomocą usługi Azure Application Gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

Funkcja koligacji sesji na podstawie plików cookie jest przydatna, gdy chcesz zachować sesję użytkownika na tym samym serwerze. Używając plików cookie zarządzanych przez bramę, usługa Application Gateway może kierować dalszy ruch z sesji użytkownika na ten sam serwer w celu przetwarzania. Jest to ważne w przypadkach, w których stan sesji jest zapisywany lokalnie na serwerze dla sesji użytkownika.

## <a name="possible-problem-causes"></a>Możliwe przyczyny problemu

Problem w utrzymaniu koligacji sesji opartej na plikach cookie może wystąpić z następujących głównych powodów:

- Ustawienie "Koligacja oparta na plikach cookie" nie jest włączone
- Aplikacja nie może obsłużyć koligacji opartej na plikach cookie
- Aplikacja używa koligacji opartej na plikach cookie, ale żądania nadal podskakują między serwerami zaplecza

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Sprawdź, czy włączone jest ustawienie "Koligacja oparta na plikach cookie"

Czasami problemy koligacji sesji mogą wystąpić, gdy zapomnisz włączyć ustawienie "Koligacja oparta na plikach cookie". Aby ustalić, czy włączono ustawienie "Koligacja oparta na plikach cookie" na karcie Ustawienia HTTP w witrynie Azure portal, postępuj zgodnie z instrukcjami:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. W **lewym** okienku nawigacji kliknij pozycję **Wszystkie zasoby**. Kliknij nazwę bramy aplikacji w bloku Wszystkie zasoby. Jeśli wybrana subskrypcja zawiera już kilka zasobów, można wprowadzić nazwę bramy aplikacji w **filtrze według nazwy...** aby łatwo uzyskać dostęp do bramy aplikacji.

3. Wybierz kartę **Ustawienia HTTP** w obszarze **USTAWIENIA**.

   ![rozwiązywanie problemów z sesjami-koligacja-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Kliknij **przycisk AppGatewayBackendHttpSettings** po prawej stronie, aby sprawdzić, czy wybrano **opcję Włącz dla** koligacji opartej na plikach cookie.

   ![rozwiązywanie problemów z sesjami-koligacja-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Można również sprawdzić wartość "**CookieBasedAffinity**" jest *ustawiona*na Włączone w obszarze "**wewnętrznej bazy danychHttpSettingsCollection**" przy użyciu jednej z następujących metod:

- Uruchamianie [programu Get-AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) w programie PowerShell
- Przejrzyj plik JSON przy użyciu szablonu usługi Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Aplikacja nie może obsłużyć koligacji opartej na plikach cookie

#### <a name="cause"></a>Przyczyna

Brama aplikacji może wykonywać koligacji opartej na sesji tylko przy użyciu pliku cookie.

#### <a name="workaround"></a>Obejście

Jeśli aplikacja nie może obsłużyć koligacji opartej na plikach cookie, należy użyć zewnętrznego lub wewnętrznego modułu równoważenia obciążenia platformy Azure lub innego rozwiązania innej firmy.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Aplikacja używa koligacji opartej na plikach cookie, ale żądania nadal podskakują między serwerami zaplecza

#### <a name="symptom"></a>Objaw

Włączono ustawienie koligacji oparte na plikach cookie podczas uzyskiwania dostępu do bramy aplikacji przy [http://website](http://website/) użyciu krótkiego adresu URL nazwy w programie Internet Explorer, na przykład: żądanie jest nadal odbijane między serwerami zaplecza.

Aby zidentyfikować ten problem, postępuj zgodnie z instrukcjami:

1. Weź śledzenia debugera sieci web na "Klient", który łączy się z aplikacją za bramą aplikacji(Używamy Fiddler w tym przykładzie).
    **Wskazówka** Jeśli nie wiesz, jak korzystać z Fiddler, sprawdź opcję "**Chcę zebrać ruch sieciowy i analizować go za pomocą debugera internetowego**" na dole.

2. Sprawdź i przeanalizuj dzienniki sesji, aby ustalić, czy pliki cookie dostarczone przez klienta mają szczegóły ARRAffinity. Jeśli nie znajdziesz szczegółów ARRAffinity, takich jak "**ARRAffinity =** *ARRAffinityValue*" w zestawie plików cookie, oznacza to, że klient nie odpowiada za pomocą pliku cookie ARRA, który jest dostarczany przez bramę aplikacji.
    Przykład:

    ![rozwiązywanie problemów z sesjami-koligacja-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![rozwiązywanie problemów z sesjami-koligacja-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

Aplikacja nadal próbuje ustawić plik cookie na każde żądanie, dopóki nie otrzyma odpowiedzi.

#### <a name="cause"></a>Przyczyna

Ten problem występuje, ponieważ program Internet Explorer i inne przeglądarki mogą nie przechowywać lub używać pliku cookie z krótkim adresem URL nazwy.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy uzyskać dostęp do usługi Application Gateway przy użyciu nazwy FQDN. Na przykład [http://website.com](https://website.com/) użyj [http://appgw.website.com](http://website.com/) lub .

## <a name="additional-logs-to-troubleshoot"></a>Dodatkowe dzienniki do rozwiązywania problemów

Możesz zbierać dodatkowe dzienniki i analizować je, aby rozwiązać problemy związane z koligalnością sesji opartą na plikach cookie

### <a name="analyze-application-gateway-logs"></a>Analizowanie dzienników bramy aplikacji

Aby zebrać dzienniki bramy aplikacji, postępuj zgodnie z instrukcjami:

Włączanie rejestrowania za pośrednictwem witryny Azure Portal

1. W [witrynie Azure portal](https://portal.azure.com/)znajdź swój zasób, a następnie kliknij pozycję **Dzienniki diagnostyczne**.

   W przypadku bramy aplikacji dostępne są trzy dzienniki: dziennik dostępu, dziennik wydajności, dziennik zapory

2. Aby rozpocząć zbieranie danych, kliknij pozycję **Włącz diagnostykę**.

   ![rozwiązywanie problemów z sesjami-koligacja-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. **Blok Ustawienia diagnostyki** zawiera ustawienia dzienników diagnostycznych. W tym przykładzie usługa Log Analytics przechowuje dzienniki. Kliknij **pozycję Konfiguruj** w obszarze **Usługa Log Analytics,** aby ustawić obszar roboczy. Na potrzeby zapisywania dzienników diagnostycznych można także skorzystać z usługi Event Hubs i konta magazynu.

   ![rozwiązywanie problemów z sesjami-koligacja-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Potwierdź ustawienia, a następnie kliknij przycisk **Zapisz**.

   ![rozwiązywanie problemów z sesjami-koligacja-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Wyświetlanie i analizowanie dzienników dostępu bramy aplikacji

1. W witrynie Azure portal w widoku zasobów bramy aplikacji wybierz pozycję **Dzienniki diagnostyki** w sekcji **MONITOROWANIE** .

   ![rozwiązywanie problemów z sesjami-koligacja-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Po prawej stronie wybierz "**ApplicationGatewayAccessLog**" na liście rozwijanej w obszarze **Kategorie dziennika.**  

   ![rozwiązywanie problemów z sesjami-koligacja-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Na liście Dziennik dostępu bramy aplikacji kliknij dziennik, który chcesz analizować i eksportować, a następnie wyeksportuj plik JSON.

4. Konwertuj eksportowany w kroku 3 plik JSON plik JSON i wyświetlaj go w programie Excel, Power BI lub innym narzędziu do wizualizacji danych.

5. Sprawdź następujące dane:

- **ClientIP**— jest to adres IP klienta z klienta łączącego.
- **ClientPort** — jest to port źródłowy z klienta łączącego dla żądania.
- **RequestQuery** — oznacza to serwer docelowy, że żądanie zostało odebrane.
- **Przekierowane na serwer:** Wystąpienie puli zaplecza, które zostało odebrane.
- **X-AzureApplicationGateway-LOG-ID:** Identyfikator korelacji używany dla żądania. Może służyć do rozwiązywania problemów z ruchem na serwerach zaplecza. Na przykład: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **STATUS SERWERA:** Kod odpowiedzi HTTP odebrany przez bramę aplikacji z zaplecza.

  ![rozwiązywanie problemów z koligacja-sesja-koligacja-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Jeśli widzisz dwa elementy pochodzą z tego samego ClientIP i portu klienta i są one wysyłane do tego samego serwera zaplecza, oznacza to, że brama aplikacji skonfigurowana poprawnie.

Jeśli widzisz dwa elementy pochodzą z tego samego ClientIP i portu klienta i są one wysyłane do różnych serwerów zaplecza, oznacza to, że żądanie jest odbijanie między serwerami wewnętrznej bazy danych, wybierz "**Aplikacja używa koligacji opartej na plikach cookie, ale żądania nadal odbijając między serwerami zaplecza**" na dole, aby rozwiązać ten problem.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Przechwytywanie i analizowanie ruchu HTTP lub HTTPS za pomocą debugera sieci Web

Narzędzia do debugowania sieci Web, takie jak Fiddler, mogą pomóc w debugowaniu aplikacji internetowych przez przechwytywanie ruchu sieciowego między Internetem a komputerami testowymi. Narzędzia te umożliwiają sprawdzanie danych przychodzących i wychodzących w miarę ich odsyłania/wysyłania ich przez przeglądarkę. Fiddler, w tym przykładzie, ma opcję odtwarzania HTTP, która może pomóc w rozwiązywaniu problemów po stronie klienta z aplikacjami sieci web, szczególnie w przypadku uwierzytelniania rodzaju problemu.

Użyj wybranego debugera internetowego. W tym przykładzie użyjemy Fiddler do przechwytywania i analizowania ruchu http lub https, postępuj zgodnie z instrukcjami:

1. Pobierz narzędzie Fiddler <https://www.telerik.com/download/fiddler>w .

    > [!NOTE]
    > Wybierz fiddler4, jeśli komputer przechwytujący ma zainstalowany plik .NET 4. W przeciwnym razie wybierz Fiddler2.

2. Kliknij prawym przyciskiem myszy plik wykonywalny instalatora i uruchom jako administrator, aby zainstalować.

    ![rozwiązywanie problemów z koligacja-sesja-koligacja-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Po otwarciu Fiddler, należy automatycznie rozpocząć przechwytywanie ruchu (zwróć uwagę na Przechwytywanie w lewym dolnym rogu). Naciśnij klawisz F12, aby rozpocząć lub zatrzymać przechwytywanie ruchu.

    ![rozwiązywanie problemów z koligacja-sesja-koligacja-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Najprawdopodobniej będziesz zainteresowany odszyfrowanym ruchem HTTPS i możesz włączyć odszyfrowywanie HTTPS, wybierając **opcję Narzędzia** > **Fiddler Options**i zaznacz pole " **Odszyfruj ruch HTTPS**".

    ![rozwiązywanie problemów z koligacja-sesja-koligacja-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Przed odtworzeniem problemu można usunąć poprzednie niepowiązane sesje, **klikając** x (ikonę) > **Usuń wszystko** w następujący sposób: 

    ![rozwiązywanie problemów z sesjami-koligacja-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Po odtworzeniu problemu zapisz plik do**Save** > sprawdzenia, wybierając pozycję Zapisz **wszystkie sesje..** > .**All Sessions..** 

    ![rozwiązywanie problemów z sesjami-koligacja-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Sprawdź i przeanalizuj dzienniki sesji, aby ustalić, co to jest problem.

    Przykłady:

- **Przykład A:** Znajdziesz dziennik sesji, że żądanie jest wysyłane z klienta i trafia do publicznego adresu IP bramy aplikacji, kliknij ten dziennik, aby wyświetlić szczegóły.  Po prawej stronie dane w dolnym polu jest to, co brama aplikacji zwraca do klienta. Wybierz kartę "RAW" i określ, czy klient otrzymuje "**Set-Cookie: ARRAffinity =** *ARRAffinityValue*." Jeśli nie ma pliku cookie, koligacja sesji nie jest ustawiona lub brama aplikacji nie stosuje pliku cookie z powrotem do klienta.

   > [!NOTE]
   > Ta wartość ARRAffinity jest cookie-id, że brama aplikacji ustawia dla klienta, który ma być wysyłany do określonego serwera zaplecza.

   ![rozwiązywanie problemów z koligacja-sesja-koligacja-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Przykład B:** Następny dziennik sesji, po którym następuje poprzedni, to klient odpowiadający z powrotem do bramy aplikacji, która ustawiła ARRAAFFINITY. Jeśli plik cookie-id ARRAffinity jest zgodny, pakiet powinien zostać wysłany do tego samego serwera zaplecza, który był używany wcześniej. Sprawdź kilka następnych wierszy komunikacji http, aby zobaczyć, czy zmienia się plik cookie ARRAffinity klienta.

   ![rozwiązywanie problemów z sesjami-koligacja-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> W przypadku tej samej sesji komunikacyjnej plik cookie nie powinien się zmieniać. Zaznacz górne pole po prawej stronie, wybierz kartę "Pliki cookie", aby sprawdzić, czy klient używa pliku cookie i wysyłając go z powrotem do bramy aplikacji. Jeśli nie, przeglądarka klienta nie przechowuje i nie używa pliku cookie do konwersacji. Czasami klient może kłamać.

 

## <a name="next-steps"></a>Następne kroki

Jeśli poprzednie kroki nie rozwiążą problemu, otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).
