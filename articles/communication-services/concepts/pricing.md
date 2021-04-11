---
title: Scenariusze cenowe dla wywoływania (głosu/wideo) i rozmowy
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej o modelu cen usług komunikacyjnych.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5f7b1e6d600f5d3652ce6a66a72cbfbf33b336c4
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091875"
---
# <a name="pricing-scenarios"></a>Scenariusze cen

Ceny usług Azure Communication Services są ogólnie oparte na modelu z płatność zgodnie z rzeczywistym użyciem. Ceny w poniższych przykładach są przeznaczone do celów ilustracyjnych i mogą nie odzwierciedlać najnowszych cen platformy Azure.

## <a name="voicevideo-calling-and-screen-sharing"></a>Połączenie głosowe/wideo i udostępnianie ekranu

Usługi komunikacyjne platformy Azure umożliwiają dodawanie połączeń głosowych i wideo oraz udostępnianie ekranu do aplikacji. Środowisko można osadzać w aplikacjach przy użyciu języka JavaScript, celu-C (Apple), Java (Android) lub .NET SDK. Zapoznaj się z naszą [pełną listą dostępnych zestawów SDK](./sdk-options.md).

### <a name="pricing"></a>Ceny

Połączenia i usługi udostępniania ekranu są płatne za minutę za każdy uczestnika w wysokości $0,004 miesięcznie dla wywołań grupowych. Aby poznać różne przepływy wywołań, które są możliwe, zapoznaj się z [tą stroną](./call-flows.md).

Każdy uczestnik rozmowy będzie liczać opłaty za każdą minutę, która jest połączona z wywołaniem. Ta wartość jest prawdziwa bez względu na to, czy użytkownik ma nagranie wideo, połączenie głosowe czy udostępnianie ekranu.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-sdks"></a>Przykład cen: połączenie audio/wideo przy użyciu zestawów SDK i systemu iOS

Alicja dokonała wywołania grupy z jej współpracownikami, Robert i Charlie. Alicja i Robert korzystali z zestawów SDK JS, Charlie zestaw SDK systemu iOS.

- Wywołanie trwa łącznie 60 minut.
- Alicja i Robert uczestniczyły w całym wywołaniu. Alicja włączyła Twoje wideo przez pięć minut i udostępniają swój ekran przez 23 minuty. Robert dysponuje wideo dla całego wywołania (60 minut) i udostępnia swój ekran przez 12 minut.
- Charlie pozostawił wywołanie po 43 minutach. Charlie użył audio i wideo na czas trwania (43 minut).

**Obliczenia kosztów**

- 2 uczestnicy x 60 min x $0,004 na uczestnika na minutę = $0,48 [zarówno wideo, jak i audio są naliczone według tej samej stawki]
- 1 uczestnik x 43 min x $0,004 na uczestnika = $0,172 [zarówno wideo, jak i audio są naliczone według tej samej stawki]

**Łączny koszt wywołania grupy**: $0,48 + $0,172 = $0,652


### <a name="pricing-example-outbound-call-from-app-using-js-sdk-to-a-pstn-number"></a>Przykład cen: wywołanie wychodzące z aplikacji przy użyciu zestawu SDK JS do numeru PSTN

Alicja nawiązuje wywołanie sieci PSTN od aplikacji do Roberta na numer telefonu w Stanach Zjednoczonych, zaczynając od `+1-425` .

- Alicja użyła zestawu SDK JS do skompilowania aplikacji.
- Wywołanie trwa łącznie 5 minut.

**Obliczenia kosztów**

- 1 uczestnik z gałęzi VoIP (Alicja) z aplikacji do serwerów usług komunikacyjnych x 10 minut x $0,004 na jednostkę dla uczestników na minutę = $0,04
- 1 uczestnik podstawki wychodzącej PSTN (Charlie) z serwerów usług komunikacyjnych na numer telefonu w Stanach Zjednoczonych x 10 minut x $0,013 na jednostkę dla uczestników na minutę = $0,13.

Uwaga: mieszane stawki w Stanach Zjednoczonych `+1-425` to $0,013. Aby uzyskać szczegółowe informacje, zobacz następujący link: https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)

**Łączny koszt wywołania grupy**: $0,04 + $0,13 = $0,17


### <a name="pricing-example-group-audio-call-using-js-sdk-and-1-pstn-leg"></a>Przykład cen: połączenie audio grupy przy użyciu zestawu SDK JS i 1 etap sieci PSTN

Alicja i Robert są w wywołaniu VOIP. Robert przeniesieł wywołanie Charlie na numer PSTN Charlie, numer telefonu w Stanach Zjednoczonych, zaczynając od `+1-425` .

- Alicja użyła zestawu SDK JS do skompilowania aplikacji. Szprychy przez 10 minut przed wywołaniem Charlie na numerze PSTN.
- Gdy Robert przeniesieł wywołanie Charlie na swój numer PSTN, trzy z nich szprychy przez kolejne 10 minut.

**Obliczenia kosztów**

- 2 uczestnicy z gałęzią VoIP (Alicja i Robert) z aplikacji do serwerów usług komunikacyjnych x 20 minut x $0,004 na jednostkę dla uczestników na minutę = $0,16
- 1 uczestnik podstawki wychodzącej PSTN (Charlie) z serwerów usług komunikacyjnych na numer telefonu do Stanów Zjednoczonych x 10 minut x $0,013 na jednostkę dla uczestników na minutę = $0,13

Uwaga: mieszane stawki w Stanach Zjednoczonych `+1-425` to $0,013. Aby uzyskać szczegółowe informacje, zobacz następujący link: https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)

**Łączny koszt wywołania metody VoIP i eskalacji**: $0,16 + $0,13 = $. 29


### <a name="pricing-example-a-user-of-the-communication-services-javascript-sdk-joins-a-scheduled-microsoft-teams-meeting"></a>Przykład cen: użytkownik zestawu SDK JavaScript usług komunikacyjnych dołącza zaplanowaną spotkanie Microsoft Teams

Alicja jest lekarzem spotkania z pacjentem, Robert. Alicja będzie dołączać do wizyty z poziomu aplikacji Team Desktop. Robert otrzyma link do sprzężenia przy użyciu witryny sieci Web dostawcy usług opieki zdrowotnej, która nawiązuje połączenie ze spotkaniem przy użyciu zestawu SDK JavaScript usługi komunikacyjnej. Robert będzie używać swojego telefonu komórkowego do wprowadzania spotkania przy użyciu przeglądarki sieci Web (iPhone z programem Safari). Czat będzie dostępny podczas odwiedzin wirtualnych.

- Wywołanie trwa łącznie 30 minut.
- Alicja i Robert uczestniczą w całym wywołaniu. Alicja włącza swoje wideo pięć minut po rozpoczęciu wywołania i udostępni jego ekran przez 13 minut. Robert ma swoje wideo dla całego wywołania.
- Alicja wysyła pięć wiadomości, Robert odpowiedzi z trzema komunikatami.


**Obliczenia kosztów**

- 1 uczestnik (Robert) x 30 minut x $0,004 na uczestnika = $0,12 [opłata za wideo i dźwięk jest naliczana w tej samej stawce]
- 1 uczestnik (Alicja) x 30 minut x $0,000 na uczestnika na minutę = $0,0 *.
- 1 uczestnik (Robert) x 3 komunikaty czatu x $0,0008 = $0,0024.
- 1 uczestnik (Alicja) x 5 wiadomości czatu x $0,000 = $0,0 *.

* Uczestnictwo Alicja jest objęte licencją zespołów. Na fakturze platformy Azure będą wyświetlane minuty i komunikaty rozmowy, które umożliwiają zespołom użytkowników usług komunikacyjnych dla wygody użytkownika, ale te minuty i komunikaty pochodzące od klientów zespołów nie będą kosztowały kosztów.

**Łączny koszt dla wizyty**:
- Przyłączanie użytkownika przy użyciu zestawu SDK JavaScript usług komunikacyjnych: $0,12 + $0,0024 = $0,1224
- Przyłączanie użytkowników do aplikacji klasycznych zespołów: $0 (objętych przez licencję zespołów)


## <a name="chat"></a>Czat

Dzięki usługom komunikacyjnym można ulepszyć aplikację, umożliwiając wysyłanie i odbieranie komunikatów rozmowy między dwoma lub większą liczbą użytkowników. Zestawy SDK rozmowy są dostępne dla języków JavaScript, .NET, Python i Java. Zobacz [Tę stronę, aby dowiedzieć się więcej o](./sdk-options.md) zestawach SDK

### <a name="price"></a>Cena

Opłata jest naliczana $0,0008 dla każdej wysyłanej wiadomości czatu.

### <a name="pricing-example-chat-between-two-users"></a>Przykład cen: rozmowa między dwoma użytkownikami

Geeta uruchamia wątek rozmowy z Emily w celu udostępnienia aktualizacji i wysłania 5 komunikatów. Rozmowa trwa 10 minut. Geeta i Emily wysyłaj kolejne 15 komunikatów.

**Obliczenia kosztów**
- Liczba wysłanych komunikatów (5 + 15 + 15) x $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Przykład cen: rozmowa grupowa z wieloma użytkownikami

Charlie uruchamia wątek rozmowy z jego przyjaciółmi Casey & Jasmine, aby zaplanować urlop. Są one rozmawiane przez czas, gdy Charlie, Casey & Jasmine wysyłać odpowiednio 20, 30 i 18 komunikatów. Zauważają one, że ich znajomy może zainteresować się również z podróżą, a tym samym dodać do wątku rozmowy i udostępnić całą historię wiadomości.

Róża widzi komunikaty i zaczyna czatować. W tym czasie Casey pobiera wywołanie i decyduje o przechwyceniu rozmowy w przyszłości. Charlie, Jasmine & podniesieniu do daty podróży i wysyłania odpowiednio 30, 25, 35 komunikatów.

**Obliczenia kosztów**

- Liczba wysłanych komunikatów (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264


## <a name="telephony-and-sms"></a>Telefonia i wiadomości SMS

## <a name="price"></a>Cena

Opłaty za usługi telefonii są naliczane co minutę, a opłata za wiadomości SMS jest naliczana na podstawie poszczególnych komunikatów. Cennik jest określany przez typ i lokalizację używanego numeru oraz miejsce docelowe wywołań i wiadomości SMS.

### <a name="telephone-number-leasing"></a>Dzierżawa numerów telefonów

Opłaty za dzierżawę numerów telefonów są naliczane z góry, a następnie powtarzają się w oparciu o miesiąc do miesiąca:

|Typ liczby   |Opłata miesięczna   |
|--------------|-----------|
|Lokalna (Stany Zjednoczone)     |$1/miesiąc        |
|Bezpłatny (Stany Zjednoczone) |$2/miesiąc |


### <a name="telephone-calling"></a>Połączenie telefoniczne

Tradycyjne wywoływanie telefoniczne (wywołanie, które jest wykonywane przez publiczną sieć telefoniczną), jest dostępne z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem dla numerów telefonów opartych na Stany Zjednoczone. Cena to opłata za minutę w oparciu o typ używanej liczby i miejsce docelowe wywołania. Szczegóły cennika dla najpopularniejszych miejsc docelowych wywoływania znajdują się w poniższej tabeli. Pełną listę miejsc docelowych można znaleźć w [szczegółowej liście cenowej](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv) .


#### <a name="united-states-calling-prices"></a>Ceny wywoływania Stany Zjednoczone

Następujące ceny obejmują wymagane podatki i opłaty za komunikację do 30 czerwca 2021:

|Typ liczby   |Aby wykonać wywołania   |Aby odebrać wywołania|
|--------------|-----------|------------|
|Lokalne     |Rozpoczynając od wartości $0.013/min       |$0.0085/min        |
|Bezpłatny — bezpłatny |$0.013/min   |$0.0220/min |

#### <a name="other-calling-destinations"></a>Inne obiekty docelowe wywołujące

Następujące ceny obejmują wymagane podatki i opłaty za komunikację do 30 czerwca 2021:

|Wykonaj wywołania do   |Cena za minutę|
|-----------|------------|
|Kanada     |Rozpoczynając od wartości $0.013/min   |
|Zjednoczone Królestwo     |Rozpoczynając od wartości $0,015/min   |
|Niemcy     |Rozpoczynając od wartości $0,015/min   |
|Francja     |Rozpoczynając od wartości $0,016;/min   |


### <a name="sms"></a>SMS

Program SMS oferuje Cennik z opcją płatność zgodnie z rzeczywistym użyciem. Cena jest opłatą za wiadomość w oparciu o miejsce docelowe wiadomości. Komunikaty mogą być wysyłane za pomocą bezpłatnych numerów telefonów do numerów telefonów znajdujących się w Stany Zjednoczone. Należy pamiętać, że nie można używać lokalnych (geograficznych) numerów telefonów do wysyłania wiadomości SMS.

Następujące ceny obejmują wymagane podatki i opłaty za komunikację do 30 czerwca 2021:

|Kraj   |Wysyłanie komunikatów|Odbieranie komunikatów|
|-----------|------------|------------|
|USA (bezpłatny)    |$0.0075/MSG   | $0.0075/MSG |
