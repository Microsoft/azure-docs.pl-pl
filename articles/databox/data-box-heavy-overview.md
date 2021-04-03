---
title: Microsoft Azure Data Box Heavy — omówienie | Microsoft Docs — dane
description: Ten artykuł dotyczy hybrydowego rozwiązania Azure Data Box, które umożliwia przenoszenie ogromnych ilości danych na platformę Azure
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 08/28/2019
ms.author: alkohli
ms.openlocfilehash: 2f1f01a8cfa25c222848e7cc5c86dc3532eba348
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80437778"
---
# <a name="what-is-azure-data-box-heavy"></a>Czym jest Azure Data Box Heavy?

Azure Data Box Heavy pozwala na szybkie, niedrogie i niezawodne wysyłanie setek terabajtów danych na platformę Azure. Dane są przesyłane do platformy Azure przez wysyłkę urządzenia Data Box Heavy z pojemnością magazynu 1 – PB, która jest wypełniana danymi i wysyłana z powrotem do firmy Microsoft. Urządzenie ma odporność na ochronę i zabezpieczanie danych podczas przesyłania.

Gdy urządzenie zostanie odebrane w centrum danych, skonfiguruj je przy użyciu lokalnego interfejsu użytkownika sieci Web. Skopiuj dane z serwerów na urządzenie, a następnie wyślij urządzenie z powrotem do centrum danych platformy Azure. W centrum danych platformy Azure dane są przekazywane do kont usługi Azure Storage. Można śledzić cały kompleksowy proces w Azure Portal.


> [!IMPORTANT]
> - Aby zażądać urządzenia, zarejestruj się w [Azure Portal](https://portal.azure.com).


## <a name="use-cases"></a>Przypadki zastosowań

Data Box Heavy najlepiej nadaje się w przypadku rozmiarów danych w setkach terabajtów, w których łączność sieciowa jest niewystarczająca do przekazywania danych na platformę Azure. Przenoszenie danych może być jednorazowe lub okresowe. Może być to również początkowy transfer danych zbiorczych, po którym następują transfery okresowe. Poniżej przedstawiono różne scenariusze, w których można wykorzystać rozwiązanie Data Box Heavy do transferu danych.

 - **Migracja jednorazowa** — przenoszenie dużej ilości danych lokalnych na platformę Azure.
     - Przenieś bibliotekę multimediów z taśm offline na platformę Azure, aby utworzyć bibliotekę multimediów online.
     - Przeprowadź migrację farmy maszyn wirtualnych, programu SQL Server i aplikacji na platformę Azure.
     - Przenieś dane historyczne na platformę Azure, aby uzyskać szczegółową analizę i raport przy użyciu usługi HDInsight.

 - **Początkowy transfer zbiorczy** — po zakończeniu początkowego transferu zbiorczego przeprowadzonego za pomocą rozwiązania Data Box Heavy (inicjatora) następują transfery przyrostowe za pośrednictwem sieci.
     - Na przykład Data Box Heavy i partner rozwiązań do tworzenia kopii zapasowych są używane do przenoszenia początkowej dużej historycznej kopii zapasowej na platformę Azure. Po zakończeniu tego procesu dane przyrostowe są przekazywane za pośrednictwem sieci do usługi Azure Storage.

 - **Okresowe operacje przekazywania** — gdy duże ilości danych są generowane okresowo i trzeba je przenosić na platformę Azure. Może to dotyczyć na przykład scenariusza eksploracji zasobów energii, w którym jest generowana zawartość wideo przeznaczona dla platform wiertniczych i elektrowni wiatrowych.

## <a name="benefits"></a>Korzyści

Data Box Heavy zaprojektowano w celu przenoszenia ogromnych ilości danych na platformę Azure z niewielkim wpływem na sieć. Oferuje ono następujące korzyści:

- **Szybkość** Data Box Heavy zużywa interfejsy sieciowe o wysokiej wydajności 40 GB/s.

- **Zabezpieczenia** — Data Box Heavy ma wbudowaną ochronę zabezpieczeń dla urządzenia, danych i usługi.
    - Urządzenie ma odporną na wstrząsy obudowę zabezpieczoną antywłamaniowymi śrubami i nalepkami umożliwiającymi wykrycie manipulacji.
    - Dane na urządzeniu są przez cały czas zabezpieczane za pomocą 256-bitowego szyfrowania AES.
    - Urządzenie można odblokować tylko przy użyciu hasła podanego w witrynie Azure Portal.
    - Usługa jest chroniona przy użyciu funkcji zabezpieczeń platformy Azure.
    - Po przekazaniu danych na platformę Azure dyski na urządzeniu są czyszczone jako czyste, zgodnie z przepisami Standard Institute of Standards and Technology (NIST) 800-88r1.


## <a name="features-and-specifications"></a>Funkcje i specyfikacje

W tej wersji urządzenie Data Box Heavy ma następujące funkcje.

| Specyfikacje                                          | Opis              |
|---------------------------------------------------------|--------------------------|
| Waga                                                  | ~ 500 funtów <br>Urządzenie przy blokowaniu kół na potrzeby transportu|
| Wymiary                                              | Szerokość: 26 cm wysokości: 28 cala Długość: 48 cala |
| Miejsce w stojaku                                              | Nie może być zainstalowany w stojaku|
| Wymagane przewody                                         | 4 rozmieszczone 120 V/10 napędy zasilania (NEMA 5-15) <br> Urządzenie obsługuje do 240 V i ma pojemniki zasilające C-13 <br> Korzystanie z kabli sieciowych zgodnych z programem [MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Zasilanie                                                    | 4 wbudowane jednostki zasilacza (PSUs) współużytkowane przez węzły urządzeń <br> 1 200 w typowym narysowaniu napięcia|
| Pojemność magazynu                                        | ~ 1 – PB RAW, 70 dysków z 14 TB <br> 770 TB pojemności do wykorzystania|
| Liczba węzłów                                          | 2 niezależne węzły na urządzenie (500 TB każde) |
| Interfejsy sieciowe na węzeł                             | 4 interfejsy sieciowe na węzeł <br><br> ZARZĄDZANIE, DATA3 <ul><li> 2 X 1-GbE — interfejsy </li><li> Zarządzanie jest przeznaczone do zarządzania i konfiguracji początkowej, a nie do konfigurowania przez użytkownika </li><li> DATA3 jest domyślnie konfigurowany przez użytkownika i Dynamic Host Configuration Protocol (DHCP)</li></ul>DANE1, DANE2 — interfejsy danych <ul><li>2 X 40-GbE interfejsy </li><li> Użytkownik konfigurowalny jako serwer DHCP (domyślnie) lub statyczny</li></ul>|


## <a name="components"></a>Składniki

Rozwiązanie Data Box Heavy składa się z następujących składników:

* **Urządzenie Data Box Heavy** — urządzenie fizyczne z odporną na wstrząsy obudową, które przechowuje dane w bezpieczny sposób. To urządzenie ma przydatną pojemność magazynu wynoszącą 770 TB.
    
* **Usługa Data Box** — rozszerzenie witryny Azure Portal, które pozwala na zarządzanie urządzeniem Data Box Heavy za pomocą internetowego interfejsu umożliwiającego uzyskiwanie dostępu z różnych lokalizacji geograficznych. Administruj urządzeniem Data Box Heavy za pomocą usługi urządzenie Data Box. Zadania usługi obejmują tworzenie zamówień i zarządzanie nimi, wyświetlanie alertów i zarządzanie nimi oraz zarządzanie udziałami.  

* **Lokalny internetowy interfejs użytkownika** — internetowy interfejs użytkownika używany do konfigurowania urządzenia (dzięki czemu może ono nawiązać połączenie z siecią lokalną) oraz do rejestrowania urządzenia w usłudze Data Box. Lokalny internetowy interfejs użytkownika umożliwia również wyłączanie i ponowne uruchamianie urządzenia, wyświetlanie dzienników kopiowania oraz kontaktowanie się z pomocą techniczną firmy Microsoft w celu zgłoszenia żądania obsługi.


## <a name="the-workflow"></a>Przepływ pracy

Typowy przepływ obejmuje następujące kroki:

1. **Zamawianie** — utwórz zamówienie w witrynie Azure Portal oraz podaj informacje wysyłkowe i docelowe konto magazynu platformy Azure dla danych. Jeśli urządzenie jest dostępne, platforma Azure przygotowuje i wysyła urządzenie z identyfikatorem śledzenia przesyłki.

2. **Odbieranie** — gdy urządzenie zostanie dostarczone, podłącz je do sieci i zasilania przy użyciu wskazanych przewodów. Włącz urządzenie i nawiąż z nim połączenie. Skonfiguruj sieć urządzenia i zainstaluj udziały na komputerze-hoście, z którego chcesz skopiować dane.

3. **Kopiowanie danych** — skopiuj dane do udziałów urządzenia Data Box Heavy.

4. **Zwracanie** — przygotuj, wyłącz i odeślij urządzenie z powrotem do centrum danych platformy Azure.

5. **Przekazywanie** — dane są automatycznie kopiowane z urządzenia na platformę Azure. Dyski urządzenia są bezpiecznie wymazywane zgodnie z wytycznymi Narodowego Instytutu Standaryzacji i Technologii (NIST, National Institute of Standards and Technology).

W trakcie tego procesu otrzymujesz powiadomienie pocztą e-mail o wszystkich zmianach stanu.

## <a name="region-availability"></a>Dostępność w danym regionie

Data Box Heavy może transferować dane na podstawie regionu, w którym wdrożono usługę, kraju/regionu, do którego urządzenie jest dostarczane, oraz docelowego konta usługi Azure Storage, na którym są przesyłane dane.

- **Dostępność usługi** — w tej wersji rozwiązanie Data Box Heavy jest dostępne w następujących regionach:
    - Wszystkie regiony chmury publicznej w Stanach Zjednoczonych — Zachodnio-środkowe stany USA, Zachodnie stany USA 2, Zachodnie stany USA, Południowo-środkowe stany USA, Środkowe stany USA, Północno-środkowe stany USA, Wschodnie stany USA i Wschodnie stany USA 2.
    - Unia Europejska — Europa Zachodnia i Europa Północna.
    - Zjednoczone Królestwo — Południowe Zjednoczone Królestwo i Zachodnie Zjednoczone Królestwo.
    - Francja — Francja Środkowa i Francja Południowa.

- **Docelowe konta magazynu** — konta magazynu do przechowywania danych są dostępne we wszystkich regionach świadczenia usługi Azure, w których ta usługa jest dostępna.

Aby uzyskać najbardziej aktualne informacje dotyczące dostępności regionów Data Box Heavy, przejdź do pozycji [produkty platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Rejestrowanie

Wykonaj następujące kroki, aby zarejestrować się w usłudze Data Box Heavy:

1. [Zaloguj się do Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **+ Utwórz zasób** , aby utworzyć nowy zasób. Wyszukaj **Azure Data Box**. Wybierz usługę **Azure Data Box**.
3. Kliknij pozycję **Utwórz**.
4. Wybierz subskrypcję, której chcesz użyć dla Data Box Heavy. Wybierz region, w którym chcesz wdrożyć zasób rozwiązania Data Box Heavy. W opcji **Data Box Heavy** kliknij pozycję **Zarejestruj się**.
5. Odpowiedz na pytania dotyczące kraju/regionu pobytu danych, ramy czasowe, docelowej usługi platformy Azure na potrzeby transferu danych, przepustowości sieci i częstotliwości transferu danych. Przejrzyj zasady ochrony prywatności i warunki i zaznacz pole wyboru obok pozycji Firma Microsoft może używać Twojego adresu e-mail do kontaktu.

Po nawiązaniu rejestracji możesz zamówić Data Box Heavy.

    
