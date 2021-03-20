---
title: Kody błędów interfejsu API REST — Azure Key Vault
description: Te kody błędów mogą być zwracane przez operację na Azure Key Vault usłudze sieci Web.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 30b7e34f2a791cfd8dec1a6d8e81d706fa07939f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91631226"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Kody błędów interfejsu API REST Azure Key Vault
 
Następujące kody błędów mogą zostać zwrócone przez operację w usłudze sieci Web Azure Key Vault.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: żądanie nieuwierzytelnione

401 oznacza, że żądanie nie jest uwierzytelniane dla Key Vault. 

Żądanie jest uwierzytelniane, jeśli:

- Magazyn kluczy wie tożsamość obiektu wywołującego; lub
- Obiekt wywołujący może próbować uzyskać dostęp do zasobów Key Vault. 

Istnieje kilka różnych powodów, dla których żądanie może zwrócić 401.

### <a name="no-authentication-token-attached-to-the-request"></a>Brak tokenu uwierzytelniania dołączonego do żądania. 

Oto przykładowe żądanie PUT, ustawiając wartość wpisu tajnego:

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

Nagłówek "Autoryzacja" jest tokenem dostępu, który jest wymagany w przypadku każdego wywołania Key Vault dla operacji w ramach płaszczyzny danych. Jeśli brakuje nagłówka, odpowiedź musi być 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Z tokenem nie jest skojarzony prawidłowy zasób. 

Podczas żądania tokenu dostępu z punktu końcowego usługi Azure OAUTH parametr o nazwie "Resource" jest obowiązkowy. Wartość jest ważna dla dostawcy tokenów, ponieważ zakresy tokenów dla zamierzonego użycia. Zasób dla **wszystkich** tokenów mających dostęp do Key Vault to *https: \/ /Vault.keyvault.NET* (bez końcowego ukośnika).

### <a name="the-token-is-expired"></a>Token wygasł

Tokeny są kodowane algorytmem Base64, a wartości można zdekodować w witrynach sieci Web, takich jak [http://jwt.calebb.net](http://jwt.calebb.net) . Poniżej przedstawiono zdekodowane tokeny:

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

W tym tokenie możemy zobaczyć wiele ważnych części:

- AUD (odbiorcy): zasób tokenu. Zwróć uwagę, że jest to `https://vault.azure.net` . Ten token nie będzie działał w przypadku wszystkich zasobów, które nie pasują jawnie do tej wartości, takiej jak Graph.
- IAT (wystawiony na): liczba taktów od momentu rozpoczęcia epoki podczas wystawiania tokenu.
- NBF (nie wcześniej): liczba taktów od momentu rozpoczęcia epoki, gdy ten token będzie prawidłowy.
- EXP (Wygaśnięcie): liczba taktów od momentu rozpoczęcia epoki po wygaśnięciu tego tokenu.
- AppID (Identyfikator aplikacji): identyfikator GUID dla identyfikatora aplikacji wysyłającego to żądanie.
- TID (identyfikator dzierżawy): identyfikator GUID dla identyfikatora dzierżawcy podmiotu zabezpieczeń tworzącego to żądanie

Należy pamiętać, że wszystkie wartości są prawidłowo identyfikowane w tokenie, aby żądanie działało. Jeśli wszystkie elementy są poprawne, żądanie nie będzie wynikiem 401.

### <a name="troubleshooting-401"></a>Rozwiązywanie problemów 401

401s należy zbadać od momentu wygenerowania tokenu przed wysłaniem żądania do magazynu kluczy. Zazwyczaj kod jest używany do żądania tokenu. Po odebraniu tokenu zostanie on przekazany do żądania Key Vault. Jeśli kod jest uruchomiony lokalnie, można użyć programu Fiddler do przechwycenia żądania/odpowiedzi do `https://login.microsoftonline.com` . Żądanie wygląda następująco:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

Następujące informacje podane przez użytkownika muszą być poprawne:

- Identyfikator dzierżawy magazynu kluczy
- Wartość zasobu ustawiona na https %3 A %2 F %2 F. Azure. NET (zakodowany adres URL)
- Identyfikator klienta
- Klucz tajny klienta

Upewnij się, że pozostała część żądania jest niemal identyczna.

Jeśli można uzyskać tylko token dostępu do odpowiedzi, można zdekodować go (jak pokazano powyżej), aby upewnić się, że identyfikator dzierżawy, identyfikator klienta (Identyfikator aplikacji) i zasób.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: niewystarczające uprawnienia

HTTP 403 oznacza, że żądanie zostało uwierzytelnione (zna tożsamość żądającą), ale tożsamość nie ma uprawnień dostępu do żądanego zasobu. Istnieją dwie przyczyny:

- Brak zasad dostępu dla tożsamości.
- Adres IP zażądanego zasobu nie jest zatwierdzony w ustawieniach zapory magazynu kluczy.

Protokół HTTP 403 często występuje, gdy aplikacja klienta nie korzysta z identyfikatora klienta, który uważa klient. Zazwyczaj oznacza to, że zasady dostępu nie są poprawnie skonfigurowane dla rzeczywistej tożsamości wywołującej.

### <a name="troubleshooting-403"></a>Rozwiązywanie problemów 403

Najpierw Włącz rejestrowanie. Aby uzyskać instrukcje, jak to zrobić, zobacz [rejestrowanie Azure Key Vault](logging.md).

Po włączeniu rejestrowania można określić, czy 403 jest spowodowane zasadami dostępu czy zasadami zapory.

#### <a name="error-due-to-firewall-policy"></a>Błąd spowodowany zasadami zapory

"Adres klienta (00.00.00.00) nie jest autoryzowany, a element wywołujący nie jest zaufaną usługą"

Istnieje ograniczona lista "zaufanych usług platformy Azure". Witryny sieci Web systemu Azure **nie** są zaufaną usługą platformy Azure. Aby uzyskać więcej informacji, zobacz wpis w blogu [Key Vault dostęp przez zaporę za pomocą usługi Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Należy dodać adres IP witryny sieci Web systemu Azure do Key Vault, aby można było jej używać.

Jeśli z powodu zasad dostępu: Znajdź identyfikator obiektu dla żądania i upewnij się, że identyfikator obiektu jest zgodny z obiektem, do którego użytkownik próbuje przypisać zasady dostępu. W usłudze AAD często występuje wiele obiektów o tej samej nazwie, więc wybranie odpowiedniej opcji jest bardzo ważne. Przez usunięcie i ponowne dodanie zasad dostępu można sprawdzić, czy istnieją wiele obiektów o tej samej nazwie.

Ponadto większość zasad dostępu nie wymaga używania "autoryzowanej aplikacji", jak pokazano w portalu. Autoryzowane aplikacje są używane w scenariuszach uwierzytelniania "w imieniu", które są rzadkie. 


## <a name="http-429-too-many-requests"></a>HTTP 429: zbyt wiele żądań

Ograniczanie przepływności występuje, gdy liczba żądań przekracza określoną wartość maksymalną dla przedziału czasu. W przypadku ograniczenia przepustowości Key Vault odpowiedzi będzie HTTP 429. Określono maksymalne wartości dla typów żądań. Na przykład: Tworzenie klucza modułu HSM 2048-bitowego jest 5 żądań na 10 sekund, ale wszystkie inne transakcje modułu HSM mają limit 1000 żądania/10 sekund. W związku z tym ważne jest, aby zrozumieć, jakie typy wywołań są wykonywane podczas określania przyczyny ograniczenia przepustowości.
Ogólnie rzecz biorąc, żądania do Key Vault są ograniczone do 2000 żądań/10 sekund. Wyjątki to kluczowe operacje, zgodnie z opisem w [Key Vault limity usługi](service-limits.md)

### <a name="troubleshooting-429"></a>Rozwiązywanie problemów 429
Ograniczanie przepływności odbywa się przy użyciu następujących technik:

- Zmniejsz liczbę żądań skierowanych do Key Vault, określając, czy istnieją wzorce do żądanego zasobu, i spróbuj wykonać ich buforowanie w aplikacji wywołującej. 

- Gdy nastąpi ograniczenie Key Vault, Dostosuj kod żądania, aby użyć wykładniczej wycofywania do ponawiania próby. Ten algorytm został wyjaśniony tutaj: [Jak ograniczyć swoją aplikację](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Jeśli liczba żądań nie może zostać zmniejszona przez buforowanie i wycofywania czasu nie działa, należy rozważyć rozdzielenie kluczy do wielu magazynów kluczy. Limit usługi dla pojedynczej subskrypcji to pięciokrotną limit Key Vault poszczególnych. W przypadku korzystania z więcej niż 5 magazynów kluczy należy wziąć pod uwagę używanie wielu subskrypcji. 

Szczegółowe wskazówki, w tym żądanie zwiększenia limitów, można znaleźć tutaj: [Key Vault wskazówki dotyczące ograniczania przepustowości](overview-throttling.md)
