---
title: Kody błędów interfejsu API REST — usługa Azure Key Vault
description: Te kody błędów mogą zostać zwrócone przez operację w usłudze sieci web usługi Azure Key Vault.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: bbb30c0ad41babca4158391c9e4e5c5d4d25cbf9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432062"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Kody błędów interfejsu API rest usługi Azure Key Vault
 
Następujące kody błędów mogą zostać zwrócone przez operację w usłudze sieci web usługi Azure Key Vault.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: Nieuwierzylityczne żądanie

401 oznacza, że żądanie jest nieuwierzyfowane dla magazynu kluczy. 

Żądanie jest uwierzytelnione, jeśli:

- Magazyn kluczy zna tożsamość wywołującego; I
- Wywołujący może próbować uzyskać dostęp do zasobów usługi Key Vault. 

Istnieje kilka różnych powodów, dla których wniosek może zwrócić 401.

### <a name="no-authentication-token-attached-to-the-request"></a>Do żądania nie jest dołączony token uwierzytelniania. 

Oto przykładowe żądanie PUT, ustawienie wartości klucza tajnego:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

Nagłówek "Autoryzacja" jest tokenem dostępu, który jest wymagany przy każdym wywołaniu do magazynu kluczy dla operacji na płaszczyznach danych. Jeśli brakuje nagłówka, odpowiedź musi być 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Token nie ma poprawnego zasobu skojarzonego z nim. 

Podczas żądania tokenu dostępu z punktu końcowego usługi Azure OAUTH parametr o nazwie "zasób" jest obowiązkowe. Wartość jest ważna dla dostawcy tokenu, ponieważ obejmuje token dla jego zamierzonego użycia. Zasób dla **wszystkich** tokenów, aby uzyskać dostęp do magazynu kluczy jest *\/https: /vault.keyvault.net* (bez końcowego ukośnika).

### <a name="the-token-is-expired"></a>Token wygasł

Tokeny są zakodowane base64, a wartości można dekodować w witrynach sieci Web, takich jak [http://jwt.calebb.net](http://jwt.calebb.net). Oto powyższy token zdekodowany:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

Widzimy wiele ważnych części w tym tokenie:

- aud (publiczność): zasób tokenu. Należy zauważyć, <https://vault.azure.net>że jest to . Ten token NIE będzie działać dla wszystkich zasobów, które nie są jawnie zgodne z tą wartością, takich jak wykres.
- iat (wydane w): Liczba znaczników od początku epoki, kiedy token został wydany.
- nbf (nie wcześniej): Liczba znaczników od początku epoki, gdy ten token staje się prawidłowy.
- exp (wygaśnięcie): liczba znaczników od początku epoki po wygaśnięciu tego tokenu.
- appid (identyfikator aplikacji): identyfikator GUID dla identyfikatora aplikacji, który tego żądania.
- tid (identyfikator dzierżawy): identyfikator GUID dla identyfikatora dzierżawy głównego zobowiązanego, który złożył to żądanie

Ważne jest, aby wszystkie wartości były poprawnie identyfikowane w tokenie, aby żądanie działało. Jeśli wszystko jest poprawne, żądanie nie spowoduje 401.

### <a name="troubleshooting-401"></a>Rozwiązywanie problemów 401

401s powinny być badane z punktu generowania tokenu, przed żądanie jest składany do magazynu kluczy. Ogólnie kod jest używany do żądania tokenu. Po odebraniu tokenu jest on przekazywany do żądania magazynu kluczy. Jeśli kod jest uruchomiony lokalnie, można użyć Fiddler do `https://login.microsoftonline.com`przechwytywania żądania/odpowiedzi do . Żądanie wygląda następująco:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

Następujące informacje dostarczone przez użytkownika papka być poprawne:

- Identyfikator dzierżawy magazynu kluczy
- Wartość zasobu ustawiona na https%3A%2F%2Fvault.azure.net (zakodowany adres URL)
- Identyfikator klienta
- Klucz tajny klienta

Upewnij się, że reszta żądania jest prawie identyczna.

Jeśli można uzyskać tylko token dostępu odpowiedzi, można go dekodować (jak pokazano powyżej), aby upewnić się, identyfikator dzierżawy, identyfikator klienta (identyfikator aplikacji) i zasobu.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: Niewystarczające uprawnienia

HTTP 403 oznacza, że żądanie zostało uwierzytelnione (zna żądaną tożsamość), ale tożsamość nie ma uprawnień dostępu do żądanego zasobu. Istnieją dwie przyczyny:

- Nie ma żadnych zasad dostępu dla tożsamości.
- Adres IP żądanego zasobu nie jest umieszczany na białej liście w ustawieniach zapory magazynu kluczy.

HTTP 403 często występuje, gdy aplikacja klienta nie używa identyfikatora klienta, który klient uważa, że jest. Zwykle oznacza to, że zasady dostępu nie jest poprawnie skonfigurowany dla rzeczywistej tożsamości wywołującej.

### <a name="troubleshooting-403"></a>Rozwiązywanie problemów 403

Najpierw włącz rejestrowanie. Aby uzyskać instrukcje, jak to zrobić, zobacz [rejestrowanie usługi Azure Key Vault](logging.md)).

Po włączeniu rejestrowania można określić, czy 403 jest ze względu na zasady dostępu lub zasady zapory.

#### <a name="error-due-to-firewall-policy"></a>Wystąpił błąd spowodowany zasadami zapory

"Adres klienta (00.00.00.00) nie jest autoryzowany, a osoba dzwoniąca nie jest usługą zaufaną"

Istnieje ograniczona lista "usług zaufanych platformy Azure". Witryny sieci Web platformy Azure **nie** są zaufaną usługą platformy Azure. Aby uzyskać więcej informacji, zobacz wpis w blogu [Key Vault Firewall access by Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Aby działała, należy dodać adres IP witryny sieci Web platformy Azure do magazynu kluczy.

Jeśli ze względu na zasady dostępu: znajdź identyfikator obiektu dla żądania i upewnij się, że identyfikator obiektu pasuje do obiektu, do którego użytkownik próbuje przypisać zasady dostępu. Często w UAd znajduje się wiele obiektów o tej samej nazwie, więc wybór właściwego jest bardzo ważny. Usuwając i ponownie dodając zasady dostępu, można sprawdzić, czy istnieje wiele obiektów o tej samej nazwie.

Ponadto większość zasad dostępu nie wymaga użycia "Autoryzowanej aplikacji", jak pokazano w portalu. Autoryzowana aplikacja jest używana dla scenariuszy uwierzytelniania "w imieniu", które są rzadkie. 


## <a name="http-429-too-many-requests"></a>HTTP 429: Zbyt wiele żądań

Ograniczanie występuje, gdy liczba żądań przekracza podane maksimum dla przedziału czasowego. Jeśli wystąpi ograniczanie przepustowości, odpowiedzią magazynu kluczy będzie HTTP 429. Określono maksymalne wartości dla typów złożonych wniosków. Na przykład: utworzenie klucza 2048-bitowego modułu HSM wynosi 5 żądań na 10 sekund, ale wszystkie inne transakcje modułu HSM mają limit żądania 1000/10 sekund. Dlatego ważne jest, aby zrozumieć, jakie typy wywołań są dokonywane podczas określania przyczyny ograniczania przepustowości.
Ogólnie rzecz biorąc żądania do magazynu kluczy są ograniczone do 2000 żądań/10 sekund. Exceptions are Key Operations, as documented in [Key Vault service limits](service-limits.md)

### <a name="troubleshooting-429"></a>Rozwiązywanie problemów 429
Ograniczanie jest opracowywał przy użyciu następujących technik:

- Zmniejsz liczbę żądań do magazynu kluczy, określając, czy istnieją wzorce żądanego zasobu i próbując buforować je w aplikacji wywołującej. 

- Gdy wystąpi ograniczanie przepustowości usługi Key Vault, dostosuj kod żądania, aby użyć wykładniczego wycofywania do ponowienia próby. Algorytm jest wyjaśniony tutaj: [Jak dławić aplikację](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Jeśli liczba żądań nie może być zmniejszona przez buforowanie i czasowe wycofywanie nie działa, należy rozważyć podzielenie kluczy do wielu magazynów kluczy. Limit usługi dla pojedynczej subskrypcji wynosi 5-krotnie limit indywidualnego magazynu kluczy. Jeśli używasz więcej niż 5 Magazyny kluczy, należy wziąć pod uwagę przy użyciu wielu subskrypcji. 

Szczegółowe wskazówki, w tym żądanie zwiększenia limitów, można znaleźć tutaj: [Wskazówki dotyczące ograniczania przepustowości magazynu kluczy](overview-throttling.md)


