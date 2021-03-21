---
title: Microsoft Azure Maps — pokrycie usług pogodowych (wersja zapoznawcza)
description: Dowiedz się więcej o usługach Microsoft Azure Maps (wersja zapoznawcza)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: references_regions
manager: philmea
ms.openlocfilehash: 6c4e9eb765a72b7a0b495f81a954b484ef6aa2b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905489"
---
# <a name="azure-maps-weather-services-preview-coverage"></a>Pokrycie Azure Maps usług pogodowych (wersja zapoznawcza)

> [!IMPORTANT]
> Usługi Azure Maps Pogoda są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ten artykuł zawiera informacje o pokryciu dotyczące Azure Maps [usług pogodowych](/rest/api/maps/weather). Azure Maps usługi danych pogody zwracają szczegóły, takie jak kafelki radarowe, bieżące warunki pogodowe, prognozy pogodowe i Pogoda wzdłuż trasy.

Azure Maps nie ma tego samego poziomu informacji i dokładności dla wszystkich krajów i regionów.

Poniższa tabela zawiera informacje o rodzaju informacji o pogodzie, które można zażądać od poszczególnych krajów/regionów.

| Symbol | Znaczenie |
|--------|---------|
|*       |Obejmuje bieżące warunki, prognozę godzinową, prognozę dzienną, prognozowaną prognozę, Pogoda na trasie i dziennych indeksach. |


## <a name="americas"></a>Ameryka Północna i Południowa

| Kraj/region              |  Kafelki satelitarne | Prognoza minutowa, kafelki radarowe | Poważne alerty pogodowe | Różnych |
|-----------------------------|:----------------:|:-----------------:|:-----------------:|:--------:|  
| Anguilla                                 | ✓ |   | |  ✓| 
| Antarktyda                               | ✓ |   | |✓|
| Antigua i Barbuda                      | ✓ |   | |✓| 
| Argentyna                                | ✓ |   | |✓| 
| Aruba                                    | ✓ |   | |✓| 
| Bahamy                                  | ✓ |   | |✓| 
| Barbados                                 | ✓ |   | |✓| 
| Belize                                   | ✓ |   | |✓| 
| Bermudy                                  | ✓ |   | |✓| 
| Boliwia                                  | ✓ |   | |✓| 
| Bonaire                                  | ✓ |   | |✓| 
| Brazylia                                   | ✓ |   | ✓ |✓| 
| Brytyjskie Wyspy Dziewicze                   | ✓ |   | |✓| 
| Kanada                                   | ✓ | ✓ | ✓ | ✓| 
| Kajmany                           | ✓ |   | |✓| 
| Chile                                    | ✓ |   | |✓| 
| Kolumbia                                 | ✓ |   | |✓| 
| Kostaryka                               | ✓ |   | |✓| 
| Kuba                                     | ✓ |   | |✓| 
| Curaçao                                  | ✓ |   | |✓| 
| Dominika                                 | ✓ |   | |✓| 
| Dominikana                       | ✓ |   | |✓| 
| Ekwador                                  | ✓ |   | |✓| 
| Salwador                              | ✓ |   | |✓| 
| Falklandy                         | ✓ |   | |✓| 
| Gujana Francuska                            | ✓ |   | |✓| 
| Grenlandia                                | ✓ |   | |✓| 
| Grenada                                  | ✓ |   | |✓| 
| Gwadelupa                               | ✓ |   | |✓| 
| Gwatemala                                | ✓ |   | |✓| 
| Gujana                                   | ✓ |   | |✓| 
| Haiti                                    | ✓ |   | |✓| 
| Honduras                                 | ✓ |   | |✓| 
| Jamajka                                  | ✓ |   | |✓| 
| Martynika                               | ✓ |   | |✓| 
| Meksyk                                   | ✓ |   | |✓| 
| Montserrat                               | ✓ |   | |✓| 
| Nikaragua                                | ✓ |   | |✓| 
| Panama                                   | ✓ |   | |✓| 
| Paragwaj                                 | ✓ |   | |✓| 
| Peru                                     | ✓ |   | |✓| 
| Portoryko                              | ✓ |   | ✓ |✓| 
| Saint Barthélemy                         | ✓ |   | |✓| 
| Saint Kitts i Nevis                    | ✓ |   | |✓| 
| Saint Lucia                              | ✓ |   | |✓| 
| Saint-Martin                             | ✓ |   | |✓| 
| Saint Pierre i Miquelon                | ✓ |   | |✓| 
| Saint Vincent i Grenadyny         | ✓ |   | |✓| 
| Sint Eustatius                           | ✓ |   | |✓|  
| Sint Maarten                             | ✓ |   | |✓| 
| Georgia Południowa i Sandwich Południowy | ✓ |   | |✓| 
| Surinam                                 | ✓ |   | |✓| 
| Trinidad i Tobago                      | ✓ |   | |✓| 
| Wyspy Turks i Caicos                 | ✓ |   | |✓| 
| Odległe wyspy Stanów Zjednoczonych                    | ✓ |   | |✓| 
| Wyspy Dziewicze Stanów Zjednoczonych                      | ✓ |   | ✓|✓| 
| Stany Zjednoczone                            | ✓ | ✓ | ✓| ✓| 
| Urugwaj                                  | ✓ |   | |✓| 
| Wenezuela                                | ✓ |   | |✓| 


## <a name="middle-east-and-africa"></a>Bliski Wschód i Afryka

| Kraj/region              |  Kafelki satelitarne | Prognoza minutowa, kafelki radarowe | Poważne alerty pogodowe | Różnych | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:| 
| Algieria                     | ✓               |                              |     |  ✓| 
| Angola                      | ✓               |                              |     |  ✓| 
| Bahrajn                     | ✓               |                              |     |  ✓| 
| Benin                       | ✓               |                              |     |  ✓| 
| Botswana                    | ✓               |                              |     |  ✓| 
| Wyspa Bouveta               | ✓               |                              |     |  ✓| 
| Burkina Faso                | ✓               |                              |     |  ✓| 
| Burundi                     | ✓               |                              |     |  ✓| 
| Kamerun                    | ✓               |                              |     |  ✓| 
| Cabo Verde                  | ✓               |                              |     |  ✓| 
| Republika Środkowoafrykańska    | ✓               |                              |     |  ✓| 
| Czad                        | ✓               |                              |     |  ✓| 
| Komory                     | ✓               |                              |     |  ✓| 
| Kongo (DRK)                 | ✓               |                              |     |  ✓|
| Côte d'Ivoire               | ✓               |                              |     |  ✓| 
| Dżibuti                    | ✓               |                              |     |  ✓| 
| Egipt                       | ✓               |                              |     |  ✓| 
| Gwinea Równikowa           | ✓               |                              |     |  ✓| 
| Erytrea                     | ✓               |                              |     |  ✓| 
| eSwatini                    | ✓               |                              |     |  ✓| 
| Etiopia                    | ✓               |                              |     |  ✓| 
| Francuskie Terytoria Południowe i Antarktyczne | ✓               |                              |     |  ✓| 
| Gabon                       | ✓               |                              |     |  ✓| 
| Gambia                      | ✓               |                              |     |  ✓| 
| Ghana                       | ✓               |                              |     |  ✓| 
| Gwinea                      | ✓               |                              |     |  ✓| 
| Gwinea Bissau               | ✓               |                              |     |  ✓| 
| Iran                        | ✓               |                              |     |  ✓| 
| Irak                        | ✓               |                              |     |  ✓| 
| Izrael                      | ✓               |                              |   ✓   |  ✓| 
| Jordania                      | ✓               |                              |     |  ✓| 
| Kenia                       | ✓               |                              |     |  ✓| 
| Kuwejt                      | ✓               |                              |     |  ✓| 
| Liban                     | ✓               |                              |     |  ✓| 
| Lesotho                     | ✓               |                              |     |  ✓| 
| Liberia                     | ✓               |                              |     |  ✓| 
| Libia                       | ✓               |                              |     |  ✓| 
| Madagaskar                  | ✓               |                              |     |  ✓| 
| Malawi                      | ✓               |                              |     |  ✓| 
| Mali                        | ✓               |                              |     |  ✓| 
| Mauretania                  | ✓               |                              |     |  ✓| 
| Mauritius                   | ✓               |                              |     |  ✓| 
| Wyspa Majotta                     | ✓               |                              |     |  ✓| 
| Maroko                     | ✓               |                              |     |  ✓| 
| Mozambik                  | ✓               |                              |     |  ✓| 
| Namibia                     | ✓               |                              |     |  ✓| 
| Niger                       | ✓               |                              |     |  ✓| 
| Nigeria                     | ✓               |                              |     |  ✓| 
| Oman                        | ✓               |                              |     |  ✓| 
| Autonomia Palestyńska       | ✓               |                              |     |  ✓| 
| Katar                       | ✓               |                              |     |  ✓| 
| Réunion                     | ✓               |                              |     |  ✓| 
| Rwanda                      | ✓               |                              |     |  ✓| 
| Święta Helena, Wyspa Wniebowstąpienia, Tristan da Cunha        | ✓               |            |     |  ✓| 
| Wyspy Świętego Tomasza i Książęca       | ✓               |                              |     |  ✓| 
| Arabia Saudyjska                | ✓               |                              |     |  ✓| 
| Senegal                     | ✓               |                              |     |  ✓| 
| Seszele                  | ✓               |                              |     |  ✓| 
| Sierra Leone                | ✓               |                              |     |  ✓| 
| Somalia                     | ✓               |                              |     |  ✓| 
| Republika Południowej Afryki                | ✓               |                              |     |  ✓| 
| Sudan Południowy                 | ✓               |                              |     |  ✓| 
| Sudan                       | ✓               |                              |     |  ✓| 
| Syria                       | ✓               |                              |     |  ✓| 
| Tanzania                    | ✓               |                              |     |  ✓| 
| Togo                        | ✓               |                              |     |  ✓| 
| Tunezja                     | ✓               |                              |     |  ✓| 
| Uganda                      | ✓               |                              |     |  ✓| 
| Zjednoczone Emiraty Arabskie        | ✓               |                              |     |  ✓| 
| Jemen                       | ✓               |                              |     |  ✓| 
| Zambia                      | ✓               |                              |     |  ✓| 
| Zimbabwe                    | ✓               |                              |     |  ✓| 


## <a name="asia-pacific"></a>Azja i Pacyfik

| Kraj/region              |  Kafelki satelitarne | Prognoza minutowa, kafelki radarowe | Poważne alerty pogodowe | Różnych |
|-----------------------------|:----------------:|:-----------------:|:--------:|  :--------:|
| Afganistan                       | ✓ |   | | ✓| 
| Samoa Amerykańskie                    | ✓ |   |  ✓| ✓| 
| Australia                         | ✓ | ✓ |  ✓ | ✓| 
| Bangladesz                        | ✓ |   | | ✓| 
| Bhutan                            | ✓ |   | | ✓| 
| Brytyjskie Terytorium Oceanu Indyjskiego    | ✓ |   | | ✓| 
| Brunei                            | ✓ |   | | ✓| 
| Kambodża                          | ✓ |   | | ✓| 
| Chiny                             | ✓ | ✓ |  ✓ | ✓| 
| Wyspa Bożego Narodzenia                  | ✓ |   | | ✓| 
| Wyspy Kokosowe (Keelinga)           | ✓ |   | | ✓| 
| Wyspy Cooka                      | ✓ |   | | ✓| 
| Fidżi                              | ✓ |   | | ✓| 
| Polinezja Francuska                  | ✓ |   | | ✓| 
| Guam                              | ✓ |   |  ✓| ✓| 
| Wyspy Heard i McDonalda | ✓ |   | | ✓| 
| SRA Hongkong                     | ✓ |   | | ✓| 
| Indie                             | ✓ |   | | ✓| 
| Indonezja                         | ✓ |   | | ✓| 
| Japonia                             | ✓ | ✓ |  ✓| ✓| 
| Kazachstan                        | ✓ |   | | ✓| 
| Kiribati                          | ✓ |   | | ✓| 
| Korea                             | ✓ | ✓ | ✓ |  ✓| 
| Kirgistan                        | ✓ |   | | ✓| 
| Laos                              | ✓ |   | | ✓| 
| SRA Makau                         | ✓ |   | | ✓| 
| Malezja                          | ✓ |   | | ✓| 
| Malediwy                          | ✓ |   | | ✓| 
| Wyspy Marshalla                  | ✓ |   | ✓ | ✓| 
| Mikronezja                        | ✓ |   | ✓ | ✓| 
| Mongolia                          | ✓ |   | | ✓| 
| Myanmar                           | ✓ |   | | ✓| 
| Nauru                             | ✓ |   | | ✓| 
| Nepal                             | ✓ |   | | ✓| 
| Nowa Kaledonia                     | ✓ |   | | ✓| 
| Nowa Zelandia                       | ✓ |   | ✓ | ✓| 
| Niue                              | ✓ |   | | ✓| 
| Norfolk                    | ✓ |   | | ✓| 
| Korea Północna                       | ✓ |   | | ✓| 
| Mariany Północne          | ✓ |   | ✓ | ✓| 
| Pakistan                          | ✓ |   | | ✓| 
| Palau                             | ✓ |   | ✓ | ✓| 
| Papua Nowa Gwinea                  | ✓ |   | | ✓| 
| Filipiny                       | ✓ |   | ✓ | ✓| 
| Wyspy Pitcairn                  | ✓ |   | | ✓| 
| Samoa                             | ✓ |   | | ✓| 
| Singapur                         | ✓ |   | | ✓| 
| Wyspy Salomona                   | ✓ |   | | ✓| 
| Sri Lanka                         | ✓ |   | | ✓| 
| Tajwan                            | ✓ |   | | ✓| 
| Tadżykistan                        | ✓ |   | | ✓| 
| Tajlandia                          | ✓ |   | | ✓| 
| Timor-Leste                       | ✓ |   | | ✓| 
| Tokelau                           | ✓ |   | | ✓| 
| Tonga                             | ✓ |   | | ✓| 
| Turkmenistan                      | ✓ |   | | ✓| 
| Tuvalu                            | ✓ |   | | ✓| 
| Uzbekistan                        | ✓ |   | | ✓| 
| Vanuatu                           | ✓ |   | | ✓| 
| Wietnam                           | ✓ |   | | ✓| 
| Wallis i Futuna                 | ✓ |   | | ✓| 


## <a name="europe"></a>Europa

| Kraj/region              |  Kafelki satelitarne | Prognoza minutowa, kafelki radarowe | Poważne alerty pogodowe | Różnych | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:|
| Albania                | ✓ |   | | ✓| 
| Andora                | ✓ |   | ✓ | ✓| 
| Armenia                | ✓ |   | | ✓| 
| Austria                | ✓ | ✓ | ✓ | ✓|
| Azerbejdżan             | ✓ |   | | ✓| 
| Białoruś                | ✓ |   | | ✓| 
| Belgia                | ✓ | ✓ |  ✓| ✓| 
| Bośnia i Hercegowina | ✓ | ✓ | ✓ | ✓| 
| Bułgaria               | ✓ |   |  ✓| ✓| 
| Chorwacja                | ✓ | ✓ |  ✓| ✓| 
| Cypr                 | ✓ |   | ✓ | ✓| 
| Czechia                | ✓ | ✓ | ✓ | ✓| 
| Dania                | ✓ | ✓ | ✓ | ✓| 
| Estonia                | ✓ | ✓ |  ✓| ✓| 
| Wyspy Owcze          | ✓ |   | | ✓| 
| Finlandia                | ✓ | ✓ | ✓ | ✓| 
| Francja                 | ✓ | ✓ | ✓ | ✓| 
| Gruzja                | ✓ |   | | ✓| 
| Niemcy                | ✓ | ✓ | ✓ | ✓| 
| Gibraltar              | ✓ | ✓ | | ✓| 
| Grecja                 | ✓ |   |  ✓| ✓| 
| Guernsey               | ✓ |   | | ✓| 
| Węgry                | ✓ | ✓ |  ✓| ✓| 
| Islandia                | ✓ |   | ✓ | ✓| 
| Irlandia                | ✓ | ✓ |  ✓| ✓| 
| Włochy                  | ✓ |   |  ✓| ✓|
| Wyspa Man            | ✓ |   | | ✓| 
| Jan Mayen              | ✓ |   | | ✓| 
| Jersey                 | ✓ |   | | ✓| 
| Kosowo                 | ✓ |   |  ✓| ✓| 
| Łotwa                 | ✓ |   | ✓ | ✓| 
| Liechtenstein          | ✓ | ✓ |  ✓| ✓| 
| Litwa              | ✓ |   | ✓ | ✓| 
| Luksemburg             | ✓ | ✓ |  ✓| ✓| 
| Macedonia Północna        | ✓ |   | ✓ | ✓| 
| Malta                  | ✓ |   | ✓ | ✓| 
| Mołdawia                | ✓ | ✓ | ✓ | ✓| 
| Monako                 | ✓ | ✓ |  ✓| ✓| 
| Czarnogóra             | ✓ | ✓ |  ✓| ✓| 
| Holandia            | ✓ | ✓ |  ✓| ✓| 
| Norwegia                 | ✓ | ✓ |  ✓| ✓| 
| Polska                 | ✓ | ✓ |  ✓| ✓| 
| Portugalia               | ✓ | ✓ |  ✓| ✓| 
| Rumunia                | ✓ | ✓ |  ✓| ✓| 
| Rosja                 | ✓ |   |  ✓| ✓| 
| San Marino             | ✓ |   |  ✓| ✓| 
| Serbia                 | ✓ | ✓ |  ✓| ✓| 
| Słowacja               | ✓ | ✓ |  ✓| ✓| 
| Słowenia               | ✓ | ✓ |  ✓| ✓| 
| Hiszpania                  | ✓ | ✓ |  ✓| ✓| 
| Svalbard               | ✓ |   | | ✓|
| Szwecja                 | ✓ | ✓ |  ✓| ✓| 
| Szwajcaria            | ✓ | ✓ | ✓| ✓| 
| Turcja                 | ✓ |   | | ✓| 
| Ukraina                | ✓ |   | | ✓| 
| Zjednoczone Królestwo         | ✓ | ✓ | ✓| ✓| 
| Watykan           | ✓ |   |✓ | ✓|