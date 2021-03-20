---
title: Cennik ofert dla maszyn wirtualnych — Azure Marketplace
description: W tym artykule opisano trzy metody określania cen ofert maszyn wirtualnych.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: fe8024c60e204a4ec72002c878f7753ae35aa00d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89393823"
---
# <a name="pricing-for-virtual-machine-offers"></a>Cennik ofert maszyn wirtualnych

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z usługą i będą nadal działać w centrum partnerskim. Przejście wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [dokumentacji interfejsu API Portal Cloud partner](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie kontynuował pracę po przejściu do Centrum partnerskiego. Interfejsy API CPP powinny być używane tylko dla istniejących produktów, które zostały już zintegrowane przed przejściem do Centrum partnerskiego; nowe produkty powinny używać interfejsów API przekazywania Centrum partnerskiego.

Istnieją trzy sposoby określania cen dla ofert maszyn wirtualnych: niestandardowe ceny podstawowe, ceny za podstawowe i Cennik arkusza kalkulacyjnego.

## <a name="customized-core-pricing"></a>Dostosowane ceny podstawowe

Cennik jest specyficzny dla każdej kombinacji regionów i podstawowych. Każdy region na liście Sprzedaj musi być określony w sekcji **virtualMachinePricing** / **regionPrices** definicji.  Użyj poprawnych kodów walut dla każdego [regionu](#regions) w żądaniu.  Poniższy przykład ilustruje następujące wymagania:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```

## <a name="per-core-pricing"></a>Cennik na rdzeń

W takim przypadku wydawcy określają jedną cenę w USD dla swojej jednostki SKU, a wszystkie inne ceny są generowane automatycznie. Cena za rdzeń jest określona w **pojedynczym** parametrze żądania.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```

## <a name="spreadsheet-pricing"></a>Cennik arkusza kalkulacyjnego

Wydawca może również przekazać swój arkusz cen do tymczasowej lokalizacji przechowywania, a następnie dołączyć identyfikator URI w żądaniu, jak inne artefakty plików. Arkusz kalkulacyjny zostanie następnie przekazany, przetłumaczony w celu oszacowania określonego harmonogramu cen, a wreszcie aktualizuje ofertę przy użyciu informacji o cenach. Kolejne żądania GET dla oferty zwracają identyfikator URI arkusza kalkulacyjnego oraz ocenione ceny dla regionu.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

## <a name="new-core-sizes-added-on-722019"></a>Nowe rozmiary podstawowe dodane do 7/2/2019

Wydawca maszyn wirtualnych został powiadomiony 2 lipca 2019 o dodawaniu nowych cen dla nowych rozmiarów maszyn wirtualnych platformy Azure (na podstawie liczby rdzeni).  Nowe ceny są dla wielkości rdzeni 10, 44, 48, 60, 120, 208 i 416.  W przypadku istniejącej maszyny wirtualnej nowe ceny dla tych rozmiarów rdzeni zostały automatycznie obliczone na podstawie bieżących cen. Wydawcy mają do 1 sierpnia 2019 w celu przejrzenia dodatkowych cen i wprowadzenia wymaganych zmian.  Po tej dacie, jeśli nie została jeszcze ponownie opublikowana przez wydawcę, automatycznie obliczone ceny dla tych nowych rozmiarów podstawowych zaczną obowiązywać.

## <a name="regions"></a>Regiony

W poniższej tabeli przedstawiono różne regiony, które można określić dla dostosowywanych podstawowych cen i ich kodów walutowych.

| **Region** | **Nazwa**             | **Kod waluty** |
|------------|----------------------|-------------------|
| DZ         | Algieria              | DZD               |
| AR         | Argentyna            | ARS               |
| AU         | Australia            | AUD               |
| AT         | Austria              | EUR               |
| BH         | Bahrajn              | BHD               |
| BY         | Białoruś              | RUB               |
| BE         | Belgia              | EUR               |
| BR         | Brazylia               | USD               |
| BG         | Bułgaria             | BGN               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Kolumbia             | WPROWADZANE               |
| CR         | Kostaryka           | CRC               |
| HR         | Chorwacja              | HRK               |
| CY         | Cypr               | EUR               |
| CZ         | Czechia       | CZK               |
| DK         | Dania              | DKK               |
| DO         | Dominikana   | USD               |
| EC         | Ekwador              | USD               |
| EG         | Egipt                | EGP               |
| SV         | Salwador          | USD               |
| EE         | Estonia              | EUR               |
| FI         | Finlandia              | EUR               |
| PW         | Francja               | EUR               |
| DE         | Niemcy              | EUR               |
| GR         | Grecja               | EUR               |
| GT         | Gwatemala            | GTQ               |
| HK         | SRA Hongkong        | HKD               |
| HU         | Węgry              | HUF               |
| IS         | Islandia              | Alokacja               |
| IN         | Indie                | INR               |
| ID (Identyfikator)         | Indonezja            | IDR               |
| IE         | Irlandia              | EUR               |
| IL         | Izrael               | ILS               |
| IT         | Włochy                | EUR               |
| JP         | Japonia                | JPY               |
| JO         | Jordania               | JOD               |
| KZ         | Kazachstan           | KZT               |
| KE         | Kenia                | KES               |
| KR         | Korea                | KRW               |
| KW         | Kuwejt               | KWD               |
| LV         | Łotwa               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litwa            | EUR               |
| LU         | Luksemburg           | EUR               |
| MY         | Malezja             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Meksyk               | MXN               |
| ME         | Czarnogóra           | EUR               |
| MA         | Maroko              | Mad —               |
| NL         | Holandia          | EUR               |
| NZ         | Nowa Zelandia          | NZD               |
| NG         | Nigeria              | NGN               |
| MK         | Macedonia Północna      | MKD               |
| NO         | Norwegia               | NOK               |
| OM         | Oman                 | OMR               |
| PK         | Pakistan             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paragwaj             | PYG               |
| PE         | Peru                 | ZA               |
| PH         | Filipiny          | PHP               |
| PL         | Polska               | PLN               |
| PT         | Portugalia             | EUR               |
| PR         | Portoryko          | USD               |
| QA         | Katar                | QAR               |
| RO         | Rumunia              | Piotr               |
| RU         | Rosja               | RUB               |
| SA         | Arabia Saudyjska         | SAR               |
| RS         | Serbia               | RSD               |
| SG         | Singapur            | Stochastyczny spadek gradientu               |
| SK         | Słowacja             | EUR               |
| SI         | Słowenia             | EUR               |
| ZA         | Republika Południowej Afryki         | ZAR               |
| ES         | Hiszpania                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Szwecja               | SEK               |
| CH         | Szwajcaria          | CHF               |
| TW         | Tajwan               | TWD               |
| TH         | Tajlandia             | THB               |
| TT         | Trinidad i Tobago  | DOCELOWY               |
| TN         | Tunezja              | TND               |
| TR         | Turcja               | TRY               |
| UA         | Ukraina              | UAH               |
| AE         | Zjednoczone Emiraty Arabskie | EUR               |
| GB         | Zjednoczone Królestwo       | GBP               |
| USA         | Stany Zjednoczone        | USD               |
| UY         | Urugwaj              | UYU               |
| VE         | Wenezuela            | USD               |
|  |  |  |
