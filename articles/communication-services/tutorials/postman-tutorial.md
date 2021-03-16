---
title: Samouczek — logowanie się i wysyłanie żądań do interfejsu API programu SMS usługi ACS przy użyciu narzędzia publikowania
titleSuffix: An Azure Communication Services tutorial
description: Dowiedz się, jak podpisać i wysyłać żądania usługi ACS za pomocą programu Poster w celu wysłania wiadomości SMS.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c21d8696c0c4edd743aab6cbe7866d27ce46a314
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492612"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>Samouczek: Podpisz i Wysyłaj żądania przy użyciu programu Poster
W tym samouczku skonfigurujemy i korzystamy z programu Poster w celu zgłoszenia żądania do usług Azure Communications Services (ACS) przy użyciu protokołu HTTP. Po zakończeniu tego samouczka pomyślnie wyślesz wiadomość SMS przy użyciu usług ACS i Poster i będzie można użyć programu do eksplorowania innych interfejsów API w usłudze ACS.

W tym samouczku będziemy:
> [!div class="checklist"]
> * Pobieranie wpisu
> * Konfigurowanie programu Poster do podpisywania żądań HTTP
> * Wysyłanie żądania do interfejsu API SMS usługi ACS w celu wysłania wiadomości.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Bezpłatne konto to $200 na kredyty na korzystanie z platformy Azure w celu wypróbowania dowolnej kombinacji usług.
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Dowiedz się, jak utworzyć zasób usług komunikacyjnych](../quickstarts/create-communication-resource.md).
- Numer telefonu usługi ACS, który umożliwia wysyłanie wiadomości SMS, zobacz nasz [numer telefonu](../quickstarts/telephony-sms/get-phone-number.md) , aby go uzyskać.

## <a name="downloading-and-installing-postman"></a>Pobieranie i Instalowanie składnika Poster

Program pocztowy to aplikacja klasyczna, która umożliwia wykonywanie żądań interfejsu API względem dowolnego interfejsu API protokołu HTTP. Jest on często używany do testowania i eksplorowania interfejsów API. Będziemy pobierać najnowszą [wersję pulpitu z witryny sieci Web programu Poster](https://www.postman.com/downloads/). Program Poster ma wersje dla systemów Windows, Mac i Linux, dlatego należy pobrać odpowiednią wersję dla danego systemu operacyjnego. Po pobraniu otwórz aplikację. Zostanie wyświetlony ekran startowy z prośbą o zalogowanie się lub utworzenie konta programu Poster. Tworzenie konta jest opcjonalne i można je pominąć, klikając link "Pomiń i przejdź do aplikacji". Utworzenie konta spowoduje zapisanie ustawień żądania interfejsu API w programie Poster, które umożliwią pobranie żądań na innych komputerach.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="Ekran startowy Twojego autora przedstawia możliwość tworzenia konta lub do pomijania i przechodzenia do aplikacji.":::

Po utworzeniu konta lub pominięciu jego tworzenia należy teraz zobaczyć główne okno programu post.

## <a name="creating-and-configuring-a-postman-collection"></a>Tworzenie i Konfigurowanie kolekcji programu Poster

Program Poster może organizować żądania na wiele sposobów. Na potrzeby tego samouczka. Utworzymy kolekcję ogłoszeń. W tym celu wybierz przycisk Kolekcje po lewej stronie aplikacji:

:::image type="content" source="media/postman/collections-tab.png" alt-text="Główny ekran programu post z wyróżnioną kartą kolekcje.":::

Po wybraniu kliknij pozycję "Utwórz nową kolekcję", aby rozpocząć proces tworzenia kolekcji. Zostanie otwarta nowa karta w środkowym obszarze programu Poster. Nadaj nazwę kolekcji, którą chcesz. W tym miejscu kolekcja nosi nazwę "ACS":

:::image type="content" source="media/postman/acs-collection.png" alt-text="Po otwarciu kolekcji usług ACS i nazwie zaznaczonej kolekcji jest ona otwarta.":::

Po utworzeniu kolekcji i nazwie można ją skonfigurować.

### <a name="adding-collection-variables"></a>Dodawanie zmiennych kolekcji

Aby obsługiwać uwierzytelnianie i ułatwić żądania, należy określić dwie zmienne kolekcji w nowo utworzonej kolekcji ACS. Te zmienne są dostępne dla wszystkich żądań w kolekcji ACS. Aby rozpocząć tworzenie zmiennych, odwiedź kartę Variable's kolekcji.

:::image type="content" source="media/postman/variable-stab.png" alt-text="Ogłoś przy użyciu karty zmienne kolekcji ACS.":::

Na karcie kolekcja Utwórz dwie zmienne:
- klucz — ta zmienna powinna być jednym z kluczy ze strony klucza usługi Azure Communications Services w ramach Azure Portal. Na przykład `oW...A==`.
- punkt końcowy — ta zmienna powinna być punktem końcowym usługi Azure Communications Services ze strony klucza. **Upewnij się, że zostanie usunięty końcowy ukośnik**. Na przykład `https://contoso.communication.azure.com`.

Wprowadź te wartości w kolumnie "wartość początkowa" na ekranie zmienne. Po wprowadzeniu naciśnij przycisk "Utrwalaj wszystko" tuż nad tabelą po prawej stronie. Po poprawnym skonfigurowaniu ekranu pocztowego powinien wyglądać podobnie do tego:

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="Wpis w postaci zmiennych kolekcji ACS został prawidłowo skonfigurowany.":::

Więcej informacji na temat zmiennych można znaleźć [w dokumentacji](https://learning.postman.com/docs/sending-requests/variables)dotyczącej wpisu.

### <a name="creating-a-pre-request-script"></a>Tworzenie skryptu poprzedzającego żądanie

Następnym krokiem jest utworzenie skryptu poprzedzającego żądanie w ramach programu Poster. Skrypt poprzedzający żądanie jest skryptem, który jest uruchamiany przed każdym żądaniem w programie Poster i może modyfikować lub zmieniać parametry żądania w Twoim imieniu. Będziemy używać tej funkcji do podpisywania żądań HTTP, aby umożliwić im autoryzowanie usług ACS. Aby uzyskać więcej informacji na temat wymagań dotyczących podpisywania, możesz [zapoznać się z naszym przewodnikiem dotyczącym uwierzytelniania](https://docs.microsoft.com/rest/api/communication/authentication).

Utworzymy ten skrypt w kolekcji w taki sposób, aby działał on na dowolnym żądaniu w kolekcji. W tym celu na karcie kolekcja kliknij podkartę "skrypt przed żądaniem".

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="Po wybraniu Sub-Tab wybranego skryptu z żądaniem pobrania kolekcji ACS zaznaczono.":::

Na tej karcie podrzędnej można utworzyć skrypt poprzedzający żądanie, wprowadzając go w obszarze tekstu poniżej. Łatwiejsze może być zapisanie tego elementu w ramach pełnego edytora kodu, takiego jak [Visual Studio Code](https://code.visualstudio.com/) przed jego wklejeniem w momencie zakończenia. W tym samouczku przejdziemy do każdej części skryptu. Poczekaj na zakończenie, jeśli chcesz tylko skopiować ją do programu Poster i rozpocząć pracę. Zacznijmy pisać skrypt.

### <a name="writing-the-pre-request-script"></a>Pisanie skryptu przed żądaniem

Pierwszym krokiem jest utworzenie ciągu skoordynowanego czasu uniwersalnego (UTC) i dodanie go do nagłówka HTTP "date". Ten ciąg jest również przechowywany w zmiennej, aby użyć go później podczas podpisywania:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

Następnie zmienimy treść żądania przy użyciu algorytmu SHA 256 i umieścisz ją w `x-ms-content-sha256` nagłówku. Program Poster zawiera kilka [standardowych bibliotek](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries) do tworzenia skrótów i podpisywania globalnie, dlatego nie musimy ich instalować ani wymagać:

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

Teraz użyjemy wcześniej określonej zmiennej punktu końcowego do rozpoznać wartości nagłówka hosta HTTP. Musimy to zrobić, ponieważ nagłówek hosta nie jest ustawiony do momentu przetworzenia tego skryptu:

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

Po utworzeniu tych informacji możemy teraz utworzyć ciąg, który zostanie podpisany dla żądania HTTP, składający się z kilku wcześniej utworzonych wartości:

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

Wreszcie musimy podpisać ten ciąg za pomocą naszego klucza ACS, a następnie dodać go do naszego żądania w `Authorization` nagłówku:

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>Końcowy skrypt poprzedzający żądanie

Końcowy skrypt poprzedzający żądanie powinien wyglądać następująco:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

Wprowadź lub wklej ten ostatni skrypt do obszaru tekstowego na karcie skrypt przed żądaniem:

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="Wprowadzony skrypt żądania pobrania dla kolekcji ACS został podany.":::

Po wprowadzeniu naciśnij klawisze CTRL + S lub naciśnij przycisk Zapisz, aby zapisać skrypt do kolekcji. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Przycisk Zapisz skrypt przed zażądaniem.":::

## <a name="creating-a-request-in-postman"></a>Tworzenie żądania w programie Poster

Teraz, gdy wszystko jest skonfigurowane, możemy utworzyć żądanie ACS w ramach programu Poster. Aby rozpocząć, kliknij ikonę plus (+) obok kolekcji ACS:

:::image type="content" source="media/postman/create-request.png" alt-text="Przycisk plusa.":::

Spowoduje to utworzenie nowej karty dla naszego żądania w programie Poster. Po jego utworzeniu musimy go skonfigurować. Wyślemy żądanie do interfejsu API wysyłania SMS, więc zapoznaj się z [dokumentacją tego interfejsu API w celu uzyskania pomocy](https://docs.microsoft.com/rest/api/communication/sms/send). Skonfigurujmy żądanie post.

Zacznij od ustawienia typ żądania do `POST` i wprowadź `{{endpoint}}/sms?api-version=2021-03-07` w polu adres URL żądania. Ten adres URL używa poprzednio utworzonej `endpoint` zmiennej do automatycznego wysyłania jej do zasobu ACS.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="Żądanie post z typem ustawionym na POST i adres URL jest ustawiony poprawnie.":::

Teraz wybierz kartę treść żądania, a następnie zmień przycisk radiowy poniżej na "RAW". Po prawej stronie znajduje się lista rozwijana "tekst", Zmień ją na JSON:

:::image type="content" source="media/postman/postman-json.png" alt-text="Ustawianie treści żądania na RAW i JSON":::

Spowoduje to skonfigurowanie żądania wysłania i odebrania informacji w formacie JSON.

W obszarze tekstu poniżej należy wprowadzić treść żądania. powinna być w następującym formacie:

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

W przypadku wartości "od" należy [uzyskać numer telefonu](../quickstarts/telephony-sms/get-phone-number.md) w portalu ACS, jak wspomniano wcześniej. Wprowadź ją bez żadnych spacji i poprzedzone przez kod kraju. Na przykład: `+15555551234`. "Wiadomość" może być taka, którą chcesz wysłać, ale `Hello from ACS` jest dobrym przykładem. Wartość "do" powinna być numerem telefonu, do którego masz dostęp, który może odbierać wiadomości SMS. Korzystanie z własnego urządzenia przenośnego jest dobrym pomysłem.

Po wprowadzeniu tej prośby należy zapisać to żądanie w kolekcji ACS, która została wcześniej utworzona. Zapewni to, że program pobierze zmienne i skrypt przed żądaniem, który został wcześniej utworzony. Aby to zrobić, kliknij przycisk "Zapisz" w prawym górnym rogu obszaru żądania.

:::image type="content" source="media/postman/postman-save.png" alt-text="Przycisk Zapisz dla żądania post.":::

Spowoduje to wyświetlenie okna dialogowego z prośbą o podanie informacji o tym, co chcesz wywołać dla żądania, i miejsce, w którym chcesz go zapisać. Możesz ją nazwać w dowolny sposób, ale upewnij się, że wybrano kolekcję usług ACS w dolnej połowie okna dialogowego:

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="Okno dialogowe żądania zapisu zapisywanych danych z wybraną kolekcją ACS.":::

## <a name="sending-a-request"></a>Wysyłanie żądania

Teraz, gdy wszystko jest skonfigurowane, powinno być możliwe wysłanie żądania i otrzymywanie wiadomości SMS na telefonie. W tym celu upewnij się, że wybrane żądanie zostało zaznaczone, a następnie naciśnij przycisk "Wyślij" po prawej stronie:

:::image type="content" source="media/postman/postman-send.png" alt-text="Żądanie post z wyróżnionym przyciskiem Wyślij.":::

Jeśli wszystko poszło dobrze, powinna zostać wyświetlona odpowiedź z usługi ACS, która powinna być kodem stanu 202:

:::image type="content" source="media/postman/postman-202.png" alt-text="Żądanie programu Poster zostało pomyślnie wysłane z kodem stanu 202.":::

Telefon komórkowy, który jest właścicielem numeru podanego w wartości "do", również powinien otrzymać komunikat SMS. Masz teraz działającą konfigurację programu Poster, która może komunikować się z usługami ACS i wysyłać wiadomości SMS.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Eksplorowanie interfejsów API](https://docs.microsoft.com/rest/api/communication/) 
>  usług ACS [Przeczytaj więcej na temat uwierzytelniania](https://docs.microsoft.com/rest/api/communication/authentication) 
>  [Dowiedz się więcej o programie Poster](https://learning.postman.com/)

Możesz również chcieć:

- [Dodawanie czatu do aplikacji](../quickstarts/chat/get-started.md)
- [Utwórz tokeny dostępu użytkowników](../quickstarts/access-tokens.md)
- [Informacje o architekturze klienta i serwera](../concepts/client-and-server-architecture.md)
- [Informacje o uwierzytelnianiu](../concepts/authentication.md)