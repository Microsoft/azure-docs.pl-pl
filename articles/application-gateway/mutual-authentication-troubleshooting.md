---
title: Rozwiązywanie problemów z uwierzytelnianiem obustronnym na platformie Azure Application Gateway
description: Dowiedz się, jak rozwiązywać problemy z uwierzytelnianiem obustronnym na Application Gateway
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 623addd253b3eb28bdf70db02ddfbe4b320cbae7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231507"
---
# <a name="troubleshooting-mutual-authentication-errors-in-application-gateway-preview"></a>Rozwiązywanie problemów z uwierzytelnianiem obustronnym w Application Gateway (wersja zapoznawcza)

Dowiedz się, jak rozwiązywać problemy z uwierzytelnianiem obustronnym przy użyciu Application Gateway. 

## <a name="overview"></a>Omówienie 

Po skonfigurowaniu uwierzytelniania wzajemnego na Application Gateway może wystąpić wiele błędów, które pojawiają się podczas próby użycia wzajemnego uwierzytelniania. Niektóre typowe przyczyny błędów to:

* Przekazano certyfikat lub łańcuch certyfikatów bez certyfikatu głównego urzędu certyfikacji.
* Przekazywanie łańcucha certyfikatów z wieloma certyfikatami głównego urzędu certyfikacji
* Przekazano łańcuch certyfikatów, który zawierał tylko certyfikat liścia bez certyfikatu urzędu certyfikacji. 
* Błędy walidacji z powodu niezgodności nazwy wyróżniającej wystawcy  

Przejdziemy do różnych scenariuszy, w których można korzystać i jak rozwiązać te scenariusze. Następnie podajemy adresy kodów błędów i wyjaśnimy przyczyny dotyczące pewnych kodów błędów, które mogą być widoczne przy użyciu wzajemnego uwierzytelniania. 

## <a name="scenario-troubleshooting---configuration-problems"></a>Scenariusz rozwiązywania problemów — problemy z konfiguracją
Istnieje kilka scenariuszy, które mogą wystąpić podczas próby skonfigurowania uwierzytelniania wzajemnego. Przeprowadzimy Cię przez proces rozwiązywania problemów z najbardziej typowymi pułapekami. 

### <a name="self-signed-certificate"></a>Certyfikat z podpisem własnym

#### <a name="problem"></a>Problem 

Przekazany certyfikat klienta jest certyfikatem z podpisem własnym, a wynikiem jest kod błędu ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate.

#### <a name="solution"></a>Rozwiązanie 

Sprawdź, czy certyfikat z podpisem własnym, którego używasz, ma rozszerzenie *BasicConstraintsOid* = "2.5.29.19", które wskazuje, że podmiot może pełnić rolę urzędu certyfikacji. Pozwoli to zagwarantować, że używany certyfikat jest certyfikatem urzędu certyfikacji. Aby uzyskać więcej informacji na temat generowania certyfikatów klientów z podpisem własnym, zobacz [certyfikaty zaufanych klientów](./mutual-authentication-certificate-management.md).

## <a name="scenario-troubleshooting---connectivity-problems"></a>Scenariusz rozwiązywania problemów — problemy z łącznością

Może być możliwe skonfigurowanie uwierzytelniania wzajemnego bez jakichkolwiek problemów, ale podczas wysyłania żądań do Application Gateway nie występują problemy. W poniższej sekcji przedstawiono kilka typowych problemów i rozwiązań. Właściwość *sslClientVerify* można znaleźć w dziennikach dostępu Application Gateway. 

### <a name="sslclientverify-is-none"></a>SslClientVerify nie jest

#### <a name="problem"></a>Problem 

Właściwość *sslClientVerify* jest wyświetlana jako "Brak" w dziennikach dostępu. 

#### <a name="solution"></a>Rozwiązanie 

Jest to widoczne, gdy klient nie wyśle certyfikatu klienta podczas wysyłania żądania do Application Gateway. Taka sytuacja może wystąpić, jeśli klient wysyłający żądanie do Application Gateway nie jest prawidłowo skonfigurowany do korzystania z certyfikatów klienta. Jednym ze sposobów sprawdzenia, czy konfiguracja uwierzytelniania klienta na Application Gateway działa zgodnie z oczekiwaniami, odbywa się za pomocą następującego polecenia OpenSSL:

```
openssl s_client -connect <hostname:port> -cert <path-to-certificate> -key <client-private-key-file> 
```

`-cert`Flaga jest certyfikatem liścia, `-key` flaga jest plikiem klucza prywatnego klienta. 

Aby uzyskać więcej informacji na temat korzystania z `s_client` polecenia OpenSSL, zapoznaj się z ich [stroną ręczną](https://www.openssl.org/docs/man1.0.2/man1/openssl-s_client.html).

### <a name="sslclientverify-is-failed"></a>SslClientVerify nie powiodła się

#### <a name="problem"></a>Problem

Właściwość *sslClientVerify* jest wyświetlana jako "Niepowodzenie" w dziennikach dostępu. 

#### <a name="solution"></a>Rozwiązanie

Istnieje wiele potencjalnych przyczyn błędów w dziennikach dostępu. Poniżej znajduje się lista typowych przyczyn niepowodzenia:
* **Nie można pobrać certyfikatu wystawcy:** Nie można znaleźć certyfikatu wystawcy certyfikatu klienta. Zazwyczaj oznacza to, że łańcuch certyfikatów zaufanego klienta urzędu certyfikacji nie został ukończony na Application Gateway. Sprawdź, czy łańcuch certyfikatów zaufanego klienta urzędu certyfikacji przekazany do Application Gateway został ukończony.  
* **Nie można pobrać certyfikatu lokalnego wystawcy:** Podobnie jak w przypadku nie można pobrać certyfikatu wystawcy, nie można odnaleźć certyfikatu wystawcy certyfikatu klienta. Zazwyczaj oznacza to, że łańcuch certyfikatów zaufanego klienta urzędu certyfikacji nie został ukończony na Application Gateway. Sprawdź, czy łańcuch certyfikatów zaufanego klienta urzędu certyfikacji przekazany do Application Gateway został ukończony.
* **Nie można zweryfikować pierwszego certyfikatu:** Nie można zweryfikować certyfikatu klienta. Ten błąd występuje w przypadku, gdy klient wyświetla tylko certyfikat liścia, którego wystawca nie jest zaufany. Sprawdź, czy łańcuch certyfikatów zaufanego klienta urzędu certyfikacji przekazany do Application Gateway został ukończony.
* **Nie można zweryfikować wystawcy certyfikatu klienta:** Ten błąd występuje, gdy *VerifyClientCertIssuerDN* konfiguracji ma wartość true. Zwykle zdarza się to, gdy nazwa wyróżniająca wystawcy certyfikatu klienta nie jest zgodna z *ClientCertificateIssuerDN* wyodrębnionym z łańcucha certyfikatów zaufanego klienta urzędu certyfikacji przekazanego przez klienta. Aby uzyskać więcej informacji na temat sposobu wyodrębniania *ClientCertificateIssuerDN* przez Application Gateway, zapoznaj się z [Application Gateway Wyodrębnij nazwę wyróżniającą wystawcy](./mutual-authentication-overview.md#verify-client-certificate-dn). Zgodnie z najlepszymi rozwiązaniami upewnij się, że przekazujesz jeden łańcuch certyfikatów dla każdego pliku do Application Gateway. 

Aby uzyskać więcej informacji na temat sposobu wyodrębnienia całego łańcucha certyfikatów zaufanego klienta urzędu certyfikacji w celu przekazania do Application Gateway, zobacz [Jak wyodrębnić łańcuch certyfikatów zaufanego urzędu certyfikacji klienta](./mutual-authentication-certificate-management.md).

## <a name="error-code-troubleshooting"></a>Rozwiązywanie problemów z kodem błędu
Jeśli widzisz dowolny z poniższych kodów błędów, mamy kilka zalecanych rozwiązań, które pomogą Ci w rozwiązaniu problemu. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustspecifydata"></a>Kod błędu: ApplicationGatewayTrustedClientCertificateMustSpecifyData

#### <a name="cause"></a>Przyczyna

Brak danych certyfikatu. Przekazany certyfikat może być pustym plikiem bez żadnych danych certyfikatu. 

#### <a name="solution"></a>Rozwiązanie

Sprawdź, czy przekazany plik certyfikatu nie zawiera żadnych brakujących danych. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustnothaveprivatekey"></a>Kod błędu: ApplicationGatewayTrustedClientCertificateMustNotHavePrivateKey

#### <a name="cause"></a>Przyczyna

W łańcuchu certyfikatów istnieje klucz prywatny. Nie powinien istnieć klucz prywatny w łańcuchu certyfikatów. 

#### <a name="solution"></a>Rozwiązanie

Sprawdź dokładnie łańcuch certyfikatów, który został przekazany, i Usuń klucz prywatny, który był częścią łańcucha. Przekaż łańcuch bez klucza prywatnego. 

### <a name="error-code-applicationgatewaytrustedclientcertificateinvaliddata"></a>Kod błędu: ApplicationGatewayTrustedClientCertificateInvalidData

#### <a name="cause"></a>Przyczyna

Istnieją dwa potencjalne przyczyny związane z tym kodem błędu.
1. Analizowanie nie powiodło się, ponieważ łańcuch nie został przedstawiony w odpowiednim formacie. Application Gateway oczekuje, że łańcuch certyfikatów będzie w formacie PEM, a także oczekuje, że poszczególne dane certyfikatu mają być rozdzielane. 
2. Analizator nie znalazł niczego do analizy. Przekazany plik może potencjalnie mieć tylko ograniczniki, ale nie dane certyfikatu. 

#### <a name="solution"></a>Rozwiązanie

W zależności od przyczyny tego błędu istnieją dwa potencjalne rozwiązania. 
* Sprawdź, czy przekazany łańcuch certyfikatów ma prawidłowy format (PEM) i czy dane certyfikatu zostały prawidłowo rozdzielone. 
* Sprawdź, czy przekazany plik certyfikatu zawiera dane certyfikatu oprócz ograniczników. 

### <a name="error-code-applicationgatewaytrustedclientcertificatedoesnotcontainanycacertificate"></a>Kod błędu: ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate

#### <a name="cause"></a>Przyczyna

Certyfikat został przekazany tylko z certyfikatem liścia bez certyfikatu urzędu certyfikacji. Przekazywanie łańcucha certyfikatów z certyfikatami urzędu certyfikacji i certyfikatem liścia jest akceptowalne, ponieważ certyfikat liścia byłby po prostu ignorowany, ale certyfikat musi mieć urząd certyfikacji. 

#### <a name="solution"></a>Rozwiązanie 

Sprawdź dokładnie, czy łańcuch certyfikatów, który został przekazany, zawiera więcej niż tylko certyfikat liścia. Rozszerzenie *BasicConstraintsOid* = "2.5.29.19" powinno być obecne i wskazywać, że podmiot może działać jako urząd certyfikacji.

### <a name="error-code-applicationgatewayonlyonerootcaallowedintrustedclientcertificate"></a>Kod błędu: ApplicationGatewayOnlyOneRootCAAllowedInTrustedClientCertificate

#### <a name="cause"></a>Przyczyna

Łańcuch certyfikatów zawiera wiele certyfikatów głównego urzędu certyfikacji *lub* zawiera zerowe certyfikaty głównego urzędu certyfikacji. 

#### <a name="solution"></a>Rozwiązanie

Przekazane certyfikaty muszą zawierać dokładnie jeden certyfikat głównego urzędu certyfikacji (oraz wiele pośrednich certyfikatów urzędu certyfikacji zgodnie z potrzebami).


