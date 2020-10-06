---
title: Scenariusze cenowe dla wywoływania (głosu/wideo) i rozmowy
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej o modelu cen usług komunikacyjnych.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4eec258cf642688c87b363ff467f1f368727a013
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761230"
---
# <a name="pricing-scenarios"></a>Scenariusze cen

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

Ceny usług Azure Communications Services są oparte na modelu z płatność zgodnie z rzeczywistym użyciem bez opłat z góry. Opłaty są naliczane tylko za użycie i korzystanie z usług.

## <a name="voicevideo-calling-and-screen-sharing"></a>Połączenie głosowe/wideo i udostępnianie ekranu

Usługi komunikacyjne platformy Azure umożliwiają dodawanie połączeń głosowych i wideo oraz udostępnianie ekranu do aplikacji. Możesz osadzić środowisko w aplikacjach przy użyciu bibliotek JavaScript, zamierzenia C (Apple), Java (Android) lub .NET Client. Zapoznaj się z naszą [pełną listą dostępnych bibliotek klientów](./sdk-options.md).

### <a name="pricing"></a>Cennik

Połączenia i usługi udostępniania ekranu są płatne za minutę za każdy uczestnika w wysokości $0,004 miesięcznie dla wywołań grupowych. Aby poznać różne przepływy wywołań, które są możliwe, zapoznaj się z [tą stroną](./call-flows.md).

Każdy uczestnik rozmowy będzie liczać opłaty za każdą minutę, która jest połączona z wywołaniem. Ta wartość jest prawdziwa bez względu na to, czy użytkownik ma nagranie wideo, połączenie głosowe czy udostępnianie ekranu.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Przykład cen: połączenie audio/wideo za pomocą bibliotek JS i iOS

Alicja dokonała wywołania grupy z jej współpracownikami, Robert i Charlie. Alicja i Robert korzystali z bibliotek klienckich JS, Charliech bibliotek klienckich systemu iOS.

- Wywołanie trwa łącznie 60 minut.
- Alicja i Robert uczestniczyły w całym wywołaniu. Alicja włączyła Twoje wideo przez pięć minut i udostępniają swój ekran przez 23 minuty. Robert dysponuje wideo dla całego wywołania (60 minut) i udostępnia swój ekran przez 12 minut.
- Charlie pozostawił wywołanie po 43 minutach. Charlie użył audio i wideo na czas trwania (43 minut).

**Obliczenia kosztów**

- 2 uczestnicy x 60 min x $0,004 na uczestnika na minutę = $0,48 [zarówno wideo, jak i audio są naliczone według tej samej stawki]
- 1 uczestnik x 43 min x $0,004 na uczestnika = $0,172 [zarówno wideo, jak i audio są naliczone według tej samej stawki]

**Łączny koszt wywołania grupy**: $0,48 + $0,172 = $0,652

## <a name="chat"></a>Czat

Dzięki usługom komunikacyjnym można ulepszyć aplikację, umożliwiając wysyłanie i odbieranie komunikatów rozmowy między 2 lub większą liczbą użytkowników. Biblioteki klienckie programu Chat są dostępne dla języków JavaScript, .NET, Python i Java. Zapoznaj się z [tą stroną, aby dowiedzieć się więcej o bibliotekach klienta](./sdk-options.md)

### <a name="price"></a>Cena

Opłata jest naliczana $0,0008 dla każdej wysyłanej wiadomości czatu.

### <a name="pricing-example-chat-between-two-users"></a>Przykład cen: rozmowa między dwoma użytkownikami 

Geeta uruchamia wątek rozmowy z Emily w celu udostępnienia aktualizacji i wysłania 5 komunikatów. Czat trwa 10 minut, w którym Geeta i Emily wysyła kolejne 15 komunikatów.

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