---
title: Filtrowanie geograficzne w domenie dla usługi platformy Azure front-drzwi
description: W tym artykule poznasz zasady filtrowania geograficznego usługi Azure Front Door Service
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: fcd7a0fe60639bbb17661a906d15136996b325e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87005450"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door-service"></a>Co to jest filtrowanie geograficzne w domenie dla usługi frontonu platformy Azure?

Domyślnie usługa Azure Front Door Service odpowiada na żądania użytkowników niezależnie od lokalizacji użytkownika wysyłającego żądanie. Jednak w niektórych przypadkach możesz chcieć ograniczyć dostęp do aplikacji sieci Web według kraju/regionu. Usługa Zapora aplikacji sieci Web (WAF) w przód drzwi umożliwia zdefiniowanie zasad przy użyciu niestandardowych reguł dostępu dla określonej ścieżki w punkcie końcowym, aby zezwolić na dostęp z określonych krajów/regionów lub go zablokować. 

Zasady WAF zazwyczaj obejmują zestaw reguł niestandardowych. Reguła składa się z warunków dopasowań, akcji i priorytetu. W warunku dopasowania należy zdefiniować zmienną dopasowania, operator i wartość dopasowania.  Dla reguły filtrowania geograficznego zmienna dopasowania jest REMOTE_ADDR, operator jest geodopasowania, wartość to dwuliterowy kod kraju/regionu. Możesz połączyć warunek GeoMatch i warunek dopasowania ciągu REQUEST_URI, aby utworzyć regułę filtrowania geograficznego opartą na ścieżkach.

Można skonfigurować zasady filtrowania geograficznego dla drzwi z przodu przy użyciu [Azure PowerShell](waf-front-door-tutorial-geo-filtering.md) lub przy użyciu naszego [szablonu szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering).

## <a name="countryregion-code-reference"></a>Odwołanie do kodu kraju/regionu

|Kod kraju/regionu | Trzeciego? Nazwa regionu |
| ----- | ----- |
| AD | Andora |
| AE | Zjednoczone Emiraty Arabskie|
| AF | Afganistan|
| AG | Antigua i Barbuda|
| AL | Albania|
| AM | Armenia|
| AO | Angola|
| AR | Argentyna|
| AS | Samoa Amerykańskie|
| AT | Austria|
| AU | Australia|
| AZ | Azerbejdżan|
| BA | Bośnia i Hercegowina|
| BB | Barbados|
| BD | Bangladesz|
| BE | Belgia|
| BF | Burkina Faso|
| BG | Bułgaria|
| BH | Bahrajn|
| BI | Burundi|
| BJ | Benin|
| BL | Saint Barthélemy|
| BN | Brunei Darussalam|
| BO | Boliwia|
| BR | Brazylia|
| BS | Bahamy|
| BT | Bhutan|
| BW | Botswana|
| BY | Białoruś|
| BZ | Belize|
| CA | Kanada|
| CD | Demokratyczna Republika Konga|
| CF | Republika Środkowoafrykańska|
| CH | Szwajcaria|
| CI | Cote d'Ivoire|
| CL | Chile|
| CM | Kamerun|
| CN | Chiny|
| CO | Kolumbia|
| CR | Kostaryka|
| CU | Kuba|
| CV | Cabo Verde|
| CY | Cypr|
| CZ | Republika Czeska|
| DE | Niemcy|
| DK | Dania|
| DO | Dominikana|
| DZ | Algieria|
| EC | Ekwador|
| EE | Estonia|
| EG | Egipt|
| ES | Hiszpania|
| ET | Etiopia|
| FI | Finlandia|
| FJ | Fidżi|
| FM | Federalne Stany Mikronezji|
| PW | Francja|
| GB | Zjednoczone Królestwo|
| GE | Gruzja|
| GF | Gujana Francuska|
| GH | Ghana|
| GN | Gwinea|
| GP | Gwadelupa|
| GR | Grecja|
| GT | Gwatemala|
| GY | Gujana|
| HK | SRA Hongkong|
| HN | Honduras|
| HR | Chorwacja|
| HT | Haiti|
| HU | Węgry|
| ID (Identyfikator) | Indonezja|
| IE | Irlandia|
| IL | Izrael|
| IN | Indie|
| IQ | Irak|
| IR | Islamska Republika Iranu|
| IS | Islandia|
| IT | Włochy|
| JM | Jamajka|
| JO | Jordania|
| JP | Japonia|
| KE | Kenia|
| KG | Kirgistan|
| KH | Kambodża|
| KI | Kiribati|
| KN | Saint Kitts i Nevis|
| KP | Koreańska Republika Ludowo-Demokratyczna|
| KR | Korea Południowa|
| KW | Kuwejt|
| KY | Kajmany|
| KZ | Kazachstan|
| LA | Laotańska Republika Ludowo-Demokratyczna|
| LB | Liban|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Litwa|
| LU | Luksemburg|
| LV | Łotwa|
| LY | Libia |
| MA | Maroko|
| MD | Republika Mołdawii|
| MG | Madagaskar|
| MK | Macedonia Północna|
| ML | Mali|
| MM | Myanmar|
| MN | Mongolia|
| MO | SRA Makau|
| MQ | Martynika|
| MR | Mauretania|
| MT | Malta|
| MV | Malediwy|
| MW | Malawi|
| MX | Meksyk|
| MY | Malezja|
| MZ | Mozambik|
| NA | Namibia|
| NE | Niger|
| NG | Nigeria|
| NI | Nikaragua|
| NL | Holandia|
| NO | Norwegia|
| NP | Nepal|
| NR | Nauru|
| NZ | Nowa Zelandia|
| OM | Oman|
| PA | Panama|
| PE | Peru|
| PH | Filipiny|
| PK | Pakistan|
| PL | Polska|
| PR | Portoryko|
| PT | Portugalia|
| PW | Palau|
| PY | Paragwaj|
| QA | Katar|
| RE | Reunion|
| RO | Rumunia|
| RS | Serbia|
| RU | Federacja Rosyjska|
| RW | Rwanda|
| SA | Arabia Saudyjska|
| Odchylenie standardowe | Sudan|
| SE | Szwecja|
| SG | Singapur|
| SI | Słowenia|
| SK | Słowacja|
| SN | Senegal|
| SO | Somalia|
| SR | Surinam|
| SS | Sudan Południowy|
| SV | Salwador|
| SY | Arabska Republika Syryjska|
| SZ | Suazi|
| TC | Wyspy Turks i Caicos|
| TG | Togo|
| TH | Tajlandia|
| TN | Tunezja|
| TR | Turcja|
| TT | Trinidad i Tobago|
| TW | Tajwan|
| TZ | Zjednoczona Republika Tanzanii|
| UA | Ukraina|
| UG | Uganda|
| USA | Stany Zjednoczone|
| UY | Urugwaj|
| UZ | Uzbekistan|
| VC | Saint Vincent i Grenadyny|
| VE | Wenezuela|
| VG | Brytyjskie Wyspy Dziewicze|
| VI | Wyspy Dziewicze Stanów Zjednoczonych|
| VN | Wietnam|
| ZA | Republika Południowej Afryki|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Następne kroki

- Uzyskaj informacje o [zabezpieczeniach warstwy aplikacji w usłudze Front Door](../../frontdoor/front-door-application-security.md).
- Dowiedz się, jak [utworzyć usługę Front Door](../../frontdoor/quickstart-create-front-door.md).
