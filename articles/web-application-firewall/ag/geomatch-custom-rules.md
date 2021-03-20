---
title: Reguły niestandardowe geodopasowania zapory aplikacji sieci Web platformy Azure (WAF)
description: Ten artykuł zawiera omówienie niestandardowych reguł geodopasowania zapory aplikacji sieci Web (WAF) w usłudze Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/31/2020
ms.author: victorh
ms.openlocfilehash: bdb115cf6d0f0aa2c55f6143015a323df6faa114
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83744301"
---
# <a name="geomatch-custom-rules-preview"></a>Reguły niestandardowe geodopasowania (wersja zapoznawcza)

Reguły niestandardowe umożliwiają tworzenie dostosowanych reguł spełniających dokładne potrzeby aplikacji i zasad zabezpieczeń. Teraz możesz ograniczyć dostęp do aplikacji sieci Web według kraju/regionu. Podobnie jak w przypadku wszystkich reguł niestandardowych, ta logika może zostać złożona z innymi regułami, aby odpowiadały potrzebom aplikacji.

Aby utworzyć regułę niestandardową filtrowania geograficznego, po prostu wybierz *lokalizację geograficzną* jako typ dopasowania, a następnie wybierz kraj/region lub kraje/regiony, które mają być dozwolone/blokowane z poziomu aplikacji. Aby uzyskać więcej informacji, zobacz [jak utworzyć niestandardowe reguły w programie PowerShell](configure-waf-custom-rules.md) i więcej przykładów reguł niestandardowych (Create-Custom-waf-rules.MD).

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="countryregion-codes"></a>Kody krajów/regionów

Jeśli używasz operatora geodopasowania, selektory mogą być dowolnymi z następujących dwóch kodów kraju/regionu. 

|Kod kraju/regionu | Nazwa kraju/regionu |
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

Po poznaniu reguł niestandardowych [Utwórz własne reguły niestandardowe](create-custom-waf-rules.md).
