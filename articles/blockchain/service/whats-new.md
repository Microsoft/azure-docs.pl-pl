---
title: Co nowego? Informacje o wersji — usługa Azure łańcucha bloków
description: Dowiedz się, co nowego w usłudze Azure łańcucha bloków Service, takich jak Najnowsze informacje o wersji, wersje, znane problemy i nadchodzące zmiany.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 47e0da5a729519f2af3c5b2a2fd3e0f7485624cf
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948463"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Co nowego w usłudze Azure łańcucha bloków Service?

> Otrzymuj powiadomienia o tym, kiedy ponownie odwiedzasz Tę stronę pod kątem aktualizacji, kopiując i wklejając ten adres URL: w przeglądarce kanału informacyjnego RSS `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` [ ![ ikona czytelnika](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522).

Usługa Azure łańcucha bloków Service odbiera usprawnienia w sposób ciągły. Aby zachować aktualność w zakresie najnowszych zmian, ten artykuł zawiera informacje na temat:

- Nowe możliwości
- Uaktualnienia wersji
- Znane problemy

---

## <a name="june-2020"></a>Czerwiec 2020 r.

### <a name="version-upgrades"></a>Uaktualnienia wersji

- Uaktualnianie wersji kworum do 2.6.0. W wersji 2.6.0 można wysyłać podpisane transakcje prywatne. Aby uzyskać więcej informacji na temat wysyłania transakcji prywatnych, zobacz [dokumentację interfejsu API kworum](https://docs.goquorum.com/en/latest/Getting%20Started/api/).
- Uaktualnij wersję Tessera do 0.10.5.

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>Rozmiar kontraktu i rozmiar transakcji został zwiększony do 128 KB

Typ: zmiana konfiguracji

Rozmiar kontraktu (MaxCodeSize) został zwiększony do 128 KB, dzięki czemu można wdrożyć inteligentne kontrakty o większym rozmiarze. Ponadto rozmiar transakcji (txnSizeLimit) został zwiększony do 128 KB. Zmiany konfiguracji dotyczą nowych konsorcjów utworzonych w usłudze Azure łańcucha bloków po Czerwiec 19 2020.

### <a name="trietimeout-value-reduced"></a>TrieTimeout wartość zredukowana

Typ: zmiana konfiguracji

Wartość TrieTimeout została zmniejszona, dzięki czemu stan pamięci jest częściej zapisywana na dysku. Niższa wartość zapewnia szybsze odzyskiwanie węzła w rzadkich przypadkach awarii węzła.

## <a name="may-2020"></a>Maj 2020 r.

### <a name="version-upgrades"></a>Uaktualnienia wersji

- Uaktualnienie wersji Ubuntu do 18,04
- Uaktualnienie wersji kworum do 2.5.0
- Uaktualnienie wersji Tessera 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Usługa Azure łańcucha bloków obsługuje wysyłanie transakcji rawPrivate

Typ: funkcja

Klienci mogą podpisywać transakcje prywatne poza kontem w węźle.

### <a name="two-phase-member-provisioning"></a>Dwufazowe Inicjowanie obsługi członków

Typ: ulepszenie

Dwie fazy pomagają zoptymalizować scenariusze, w których element członkowski jest tworzony w długim istniejącym konsorcjum. Infrastruktura członkowska jest obsługiwana w pierwszej fazie. W drugiej fazie element członkowski jest synchronizowany z łańcucha bloków. Inicjowanie obsługi dwuetapowej pomaga uniknąć niepowodzenia tworzenia elementu członkowskiego z powodu przekroczeń limitu czasu.

## <a name="known-issues"></a>Znane problemy

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>Funkcja ETH. estimateGas zgłasza wyjątek w kworum v 2.6.0

W 2.6.0 kworum v, wywołania funkcji *ETH. estimateGas* bez podawania dodatkowego parametru *wartości* powodują *awarię wyjątku procedury obsługi metody* . Zespół kworum został powiadomiony, a jego poprawka jest oczekiwana na koniec lipca 2020. Następujące obejścia można zastosować do momentu udostępnienia poprawki:

- Należy unikać używania *ETH. estimateGas* , ponieważ może to mieć wpływ na wydajność. Aby uzyskać więcej informacji na temat problemów z wydajnością ETH. estimateGas, zobacz [wywoływanie funkcji ETH. estimateGas zmniejsza wydajność](#calling-ethestimategas-function-reduces-performance). Uwzględnij wartość gazu dla każdej transakcji. Większość bibliotek wywoła ETH. estimateGas, jeśli nie zostanie podana wartość gazu, która powoduje awarię kworum w 2.6.0.
- Jeśli konieczne jest wywołanie *ETH. estimateGas*, zespół kworum sugeruje przekazanie dodatkowej *wartości* parametru jako " *0* " jako obejście.

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>Wyszukiwanie jest zatrzymywane w przypadku mniej niż czterech węzłów modułu sprawdzania poprawności

Sieci produkcyjne powinny mieć co najmniej cztery węzły modułu sprawdzania poprawności. Kworum zaleca co najmniej cztery węzły modułu sprawdzania poprawności, które są zgodne z odpornością na uszkodzenia IBFT (3F + 1). Należy mieć co najmniej dwa węzły warstwy *standardowa* usługi Azure łańcucha bloków, aby uzyskać cztery węzły modułu sprawdzania poprawności. Jest inicjowany węzeł standardowy z dwoma węzłami modułu sprawdzania poprawności.  

Jeśli sieć łańcucha bloków w usłudze Azure łańcucha bloków Service nie ma czterech węzłów modułu sprawdzania poprawności, wyszukiwanie może zostać zatrzymane w sieci. Wyszukiwanie można wykryć przez ustawienie alertu dotyczącego przetworzonych bloków. W dobrej kondycji, przetworzony blok będzie 60 bloków na węzeł na pięć minut.

Jako środek zaradczy zespół usługi Azure łańcucha bloków musi ponownie uruchomić węzeł. Klienci muszą otworzyć żądanie obsługi, aby ponownie uruchomić węzeł. Zespół ds. usługi Azure łańcucha bloków działa w celu automatycznego wykrywania i rozwiązywania problemów z wyszukiwaniem.

Użyj warstwy *standardowa* dla wdrożeń klasy produkcyjnej. Skorzystaj z warstwy *podstawowa* na potrzeby tworzenia, testowania i sprawdzania poprawności koncepcji. Zmiana warstwy cenowej między podstawowa i Standardowa po utworzeniu elementu członkowskiego nie jest obsługiwana.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>Łańcucha bloków Data Manager wymaga węzła warstwy standardowej

Jeśli używasz łańcucha bloków Data Manager, użyj warstwy *standardowa* . Warstwa *podstawowa* ma tylko 4 GB pamięci. W związku z tym nie można skalować do użycia wymaganego przez łańcucha bloków Data Manager i innych działających na nim usług.

Skorzystaj z warstwy *podstawowa* na potrzeby tworzenia, testowania i sprawdzania poprawności koncepcji. Zmiana warstwy cenowej między podstawowa i Standardowa po utworzeniu elementu członkowskiego nie jest obsługiwana.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>Duża ilość wywołań konta odblokowywania powoduje awarię geth

Duża ilość wywołań konta odblokowywania podczas przesyłania transakcji może spowodować awarię geth w węźle transakcji. W związku z tym należy zatrzymać pozyskiwanie transakcji. W przeciwnym razie oczekująca Kolejka transakcji wzrośnie.

Geth ponownie uruchamia się automatycznie w ciągu krótszym niż minutę. Synchronizacja może zająć dużo czasu w zależności od węzła. Zespół ds. usługi Azure łańcucha bloków umożliwia skrócenie czasu synchronizacji.

Aby zidentyfikować awarie geth, można sprawdzić dzienniki dowolnego komunikatu o błędzie w wiadomościach łańcucha bloków w dziennikach aplikacji. Możesz również sprawdzić, czy przetworzone bloki maleją, gdy oczekujące transakcje rosną.

Aby wyeliminować problem, należy wysłać podpisane transakcje zamiast wysyłać niepodpisane transakcje za pomocą polecenia w celu odblokowania konta. W przypadku transakcji, które są już podpisane zewnętrznie, nie ma potrzeby odblokowywania konta.

Jeśli chcesz wysłać niepodpisane transakcje, Odblokuj konto przez nieograniczoną godzinę, wysyłając 0 jako parametr czasu polecenia unlock. Konto można zablokować ponownie po przesłaniu wszystkich transakcji.  

Poniżej przedstawiono parametry geth używane przez usługę Azure łańcucha bloków. Tych parametrów nie można dostosować.

- Okres bloku Stambuł: 5 s
- Limit gazu podłogowego: 700000000
- Limit gazów CEIL —: 800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>Duże ilości transakcji prywatnych zmniejszają wydajność

Jeśli używasz warstwy Podstawowa i transakcji prywatnych usługi Azure łańcucha bloków, Tessera może ulec awarii.

Awarię Tessera można wykryć, przeglądając Dzienniki aplikacji łańcucha bloków i wyszukując komunikat `Tessera crashed. Restarting Tessera...` .

Usługa Azure łańcucha bloków powoduje ponowne uruchomienie usługi Tessera w przypadku wystąpienia awarii. Ponowne uruchomienie trwa około minuty.

W przypadku wysyłania dużej liczby transakcji prywatnych należy użyć warstwy *standardowa* . Skorzystaj z warstwy *podstawowa* na potrzeby tworzenia, testowania i sprawdzania poprawności koncepcji. Zmiana warstwy cenowej między podstawowa i Standardowa po utworzeniu elementu członkowskiego nie jest obsługiwana.

### <a name="calling-ethestimategas-function-reduces-performance"></a>Wywołanie funkcji ETH. estimateGas zmniejsza wydajność

Wielokrotne wywoływanie funkcji *ETH. estimateGas* zmniejsza liczbę transakcji na sekundę. Nie należy używać funkcji *ETH. estimateGas* dla każdego przesłania transakcji. Funkcja *ETH. estimateGas* ma duże ilości pamięci.

Jeśli to możliwe, należy użyć wartości zachowawczej gazu do przesyłania transakcji i zminimalizować użycie *ETH. estimateGas*.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>Nieograniczone pętle w przypadku kontraktów inteligentnych zmniejszają wydajność

Unikaj niepowiązanych pętli w inteligentnych kontraktach, ponieważ mogą one obniżyć wydajność. Więcej informacji można znaleźć w następujących zasobach:

- [Unikaj pętli niezwiązanych](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [Najlepsze rozwiązania w zakresie zabezpieczeń dla kontraktu inteligentnego](https://github.com/ConsenSys/smart-contract-best-practices)
- [Wskazówki dotyczące kontraktu inteligentnego udostępniane przez kworum](http://docs.goquorum.com/en/latest/Security/Framework/Decentralized%20Application/Smart%20Contracts%20Security/)
- [Wytyczne dotyczące limitów gazu i pętli zapewniające trwałość](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)