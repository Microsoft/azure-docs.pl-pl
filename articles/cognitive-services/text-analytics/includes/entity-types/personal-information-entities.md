---
title: Nazwane jednostki do informacji osobistych
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77086908"
---
## <a name="personal-information-entity-types"></a>Typy jednostek informacji osobistych:

### <a name="person"></a>Person (Osoba)
Rozpoznawaj nazwiska osób w tekście.

Języki:
* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                               | Dostępne począwszy od wersji modelu |
|--------------|-----------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Rozpoznawane nazwy, na `Bill Gates`przykład`Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organizacja  

Rozpoznawaj organizacje, firmy, agencje, firmy, trefle i inne grupy osób.

Języki: 

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                                                                       | Dostępne począwszy od wersji modelu|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| Nie dotyczy          | organizacje, na przykład `Microsoft` `NASA`,`National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Numer telefonu

Numery telefonów (tylko numery telefonów USA). 

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                    | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Numery telefonów US, na przykład`(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>Poczta e-mail

Adres e-mail. 

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                      | Dostępne począwszy od wersji modelu |
|--------------|--------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Adres e-mail, na przykład`support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>Adres URL

Internetowe adresy URL.

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                          | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Adresy URL do witryn sieci Web, na przykład`https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>Adres IP

Adres protokołu internetowego

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                              | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------|----------------------------------------|
| Nie dotyczy          | Adres sieciowy na przykład`10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Liczba 

Ilości liczbowe

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                   | Dostępne począwszy od wersji modelu |
|--------------|-------------------------------|----------------------------------------|
| Wiek          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>DateTime

Jednostki daty i godziny

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                   | Dostępne począwszy od wersji modelu |
|--------------|-------------------------------|----------------------------------------|
| Date         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>Współrzędne GPS w Unii Europejskiej

 Współrzędne GPS dla lokalizacji w Unii Europejskiej. 

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                               | Dostępne począwszy od wersji modelu |
|--------------|-------------------------------------------|----------------------------------------|
| Nie dotyczy          | Współrzędne GPS w Unii Europejskiej | `2019-10-01`                           |

### <a name="azure-information"></a>Informacje o platformie Azure

Identyfikowalne informacje dotyczące platformy Azure, w tym informacje o uwierzytelnianiu i parametry połączenia. 

* Dostępne począwszy od wersji `2019-10-01`modelu.

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu                          | Opis                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Klucz uwierzytelniania usługi Azure DocumentDB             | Klucz autoryzacji dla serwera usługi Azure DocumentDB.                           |
| Parametry połączenia z usługą Azure IAAS Database | Parametry połączenia dla bazy danych infrastruktury platformy Azure jako usługi (IaaS). |
| Parametry połączenia usługi Azure SQL           | Parametry połączenia dla bazy danych SQL Azure.                                |
| Parametry połączenia usługi Azure IoT           | Parametry połączenia dla Internetu rzeczy (IoT) platformy Azure.                        |
| Hasło ustawienia publikowania platformy Azure        | Hasło dla ustawień publikowania platformy Azure.                                        |
| Azure Redis Cache parametry połączenia   | Parametry połączenia dla pamięci podręcznej platformy Azure dla Redis.                             |
| SAS platformy Azure                             | Parametry połączenia dla oprogramowania Azure jako usługi (SAS).                     |
| Azure Service Bus parametry połączenia   | Parametry połączenia dla usługi Azure Service Bus.                                |
| Klucz konta usługi Azure Storage             | Klucz konta dla konta usługi Azure Storage.                                   |
| Klucz konta usługi Azure Storage (ogólny)   | Ogólny klucz konta dla konta usługi Azure Storage.                           |
| SQL Server parametry połączenia          | Parametry połączenia dla programu SQL Server.                                         |

### <a name="identification"></a>Identyfikacja

* Dostępne począwszy od wersji `2019-10-01`modelu.

Języki:

* Publiczna wersja zapoznawcza:`English`

#### <a name="financial-account-identification"></a>Identyfikacja konta finansowego

| Nazwa podtypu               | Opis                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA numery routingu        | Numery routingu tranzytowego stowarzyszenia bankowego (ABA).                  |
| Kod SWIFT                 | Kody SWIFT dla informacji o instrukcji płatności.                           |
| Karta kredytowa                | Numery kart kredytowych.                                                       |
| Kod IBAN                  | Kody IBAN dla informacji o instrukcji płatności.                            |

#### <a name="government-and-country-specific-identification"></a>Identyfikacja dla instytucji rządowych i krajów

Poniższe jednostki są pogrupowane i wyszczególnione według kraju:

Argentyna
* Numer tożsamości narodowej (DNI)

Australia
* Numer pliku podatku 
* Identyfikator licencji sterownika
* Identyfikator paszportu
* Numer konta medycznego
* numery kont Bank (na przykład sprawdzanie, oszczędności i konta debetowe)

Belgia
* Numer Narodowy

Brazylia
* Numer podmiotu prawnego (numer CNPJ)
* Numer numer CPF
* Krajowa karta IDENTYFIKACYJNa (RG)

Kanada
* Identyfikator paszportu
* Identyfikator licencji sterownika
* Numer ubezpieczenia kondycji
* Osobisty numer IDENTYFIKACYJNy kondycji (PHIN)
* Numer ubezpieczenia społecznego
* numery kont Bank (na przykład sprawdzanie, oszczędności i konta debetowe)

Chile
* Numer karty tożsamości 

Chiny
* Numer karty tożsamości
* Numer karty identyfikatora rezydenta (ChRL)

Chorwacja
* Numer karty identyfikatora
* Numer osobisty (OIB)

Czechy
* Numer krajowej karty IDENTYFIKACYJNej

Dania
* Osobisty numer IDENTYFIKACYJNy

Unia Europejska (UE)
* Numer IDENTYFIKACYJNy Narodowego
* Identyfikator paszportu
* Identyfikator licencji sterownika
* Numer ubezpieczenia społecznego (SSN) lub równoważny identyfikator
* Numer identyfikacji podatkowej UE (NIP)
* Numer karty debetowej UE

Finlandia
* Numer IDENTYFIKACYJNy Narodowego
* Identyfikator paszportu

Francja
* Krajowa karta IDENTYFIKACYJNa (CNI)
* Numer ubezpieczenia społecznego (INSEE)
* Identyfikator paszportu
* Identyfikator licencji sterownika

Niemcy
* Numer karty identyfikatora
* Identyfikator paszportu
* Identyfikator licencji sterownika

Grecja 
* Numer krajowej karty IDENTYFIKACYJNej

Hongkong
* Numer karty IDENTYFIKACYJNej (HKID)

Indie
* Stały numer konta (PAN)
* Unikatowy identyfikator (AADHAAR)

Indonezja
* Numer karty IDENTYFIKACYJNej (KTP)

Irlandia
* Osobisty numer usługi publicznej (PPS)

Izrael
* Identyfikator Narodowy
* numery kont Bank (na przykład sprawdzanie, oszczędności i konta debetowe)

Włochy
* Identyfikator licencji sterownika

Japonia
* Numer rejestracji rezydenta
* Numer karty pobytu
* Identyfikator licencji sterownika
* Numer ubezpieczenia społecznego (SIN)
* Identyfikator paszportu
* numery kont Bank (na przykład sprawdzanie, oszczędności i konta debetowe)

Malezja
* Numer karty identyfikatora

Holandia
* Numer usługi (BSN)

Nowa Zelandia
* Numer Ministerstwa kondycji

Norwegia
* Numer karty identyfikatora

Filipiny
* Ujednolicony Numer IDENTYFIKACYJNy wielu przeznaczeniów

Polska
* Numer karty identyfikatora
* Identyfikator Narodowy (PESEL)
* Identyfikator paszportu

Portugalia 
* Numer karty obywatela

Arabia Saudyjska
* Identyfikator Narodowy

Singapur
* Numer krajowej karty rejestracji (NRIC)

Republika Południowej Afryki
* Numer IDENTYFIKACYJNy
* Numer rejestracji rezydenta

Korea Południowa
* Numer rejestracji rezydenta

Hiszpania 
* Numer ubezpieczenia społecznego (SSN)

Szwecja
* Identyfikator Narodowy
* Identyfikator paszportu

Tajwan 
* Identyfikator Narodowy
* Numer certyfikatu rezydentnego (ARC/TARC)
* Identyfikator paszportu

Tajlandia
* Kod identyfikacji populacji

Wielka Brytania
* Identyfikator paszportu
* Identyfikator licencji sterownika
* Krajowy numer ubezpieczenia (NINO)
* Numer Narodowy Usługa kondycji (NHS)

Stany Zjednoczone
* Numer ubezpieczenia społecznego (SSN)
* Identyfikator licencji sterownika
* Identyfikator paszportu
* Numer rzutowania wyborczyego
* Numer NIP (ITIN)
* Numer w agencji wymuszania narkotyków (DEA)
* numery kont Bank (na przykład sprawdzanie, oszczędności i konta debetowe)
