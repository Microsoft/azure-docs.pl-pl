---
title: Pokrycie mobilności (tranzytowe) w usłudze Microsoft Azure Maps usługi mobilności (wersja zapoznawcza)
description: Dowiedz się, jaki poziom pokrycia usługi Azure Maps Mobility Services (wersja zapoznawcza) zawiera regiony do funkcji tranzytu publicznego, takie jak Routing i alerty usług.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e902f313edf22d75f6b183575c3dc8d0dd94bc1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96904758"
---
# <a name="azure-maps-mobility-services-preview-coverage"></a>Pokrycie Azure Maps usług mobilności (wersja zapoznawcza)

> [!IMPORTANT]
> Usługi mobilności Azure Maps są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


[Usługi mobilności](/rest/api/maps/mobility) Azure Maps usprawniają czas projektowania aplikacji z funkcjami tranzytu publicznego, takimi jak routing tranzytowy i wyszukiwanie w pobliżu tranzytu publicznego. Użytkownicy mogą pobierać szczegółowe informacje o zatrzymaniu, wierszach i harmonogramach tranzytu. Usługi mobilności umożliwiają również użytkownikom pobieranie zatrzymywania i geometrie wiersza, alertów dotyczących punktów przerwania, wierszy i usług oraz przychodzących tranzytów publicznych i alertów usług w czasie rzeczywistym. Ponadto usługi mobilności udostępniają możliwości routingu z opcjami planowania podróży Multimodal. Multimodale w trakcie podróży, w jednej podróży, umożliwia planowanie przechodzenia między firmami i opcjami tranzytu publicznego. Użytkownicy mogą również uzyskać dostęp do szczegółowych multimodalów krok po kroku.

Azure Maps nie zapewnia tego samego poziomu informacji i dokładności dla wszystkich miast i krajów/regionów. Możliwość wywoływania danych tranzytu publicznego zależy od obszaru Metro. Ponadto dane mapy mogą nie zawierać wszystkich opcji tranzytu publicznego i agencji, które obsługują obszar Metro.

Poniższa tabela zawiera informacje dotyczące pokrycia Azure Maps usług mobilności.

| Symbol | Znaczenie |
|--------|---------|
| *      |Prawie pełne pokrycie kraju/regionu.|

## <a name="americas"></a>Ameryka Północna i Południowa

| Kraj/region |  Miasto (obszar Metro) |
|----------------|---------|
| Antigua i Barbuda | Antigua i Barbuda * |
| Argentyna       | <p>Azul, Bahía Blanca, Buenos Aires, Caleta Olivia, Catamarca, Chivilcoy, Comodoro Rivadavia, Concordia, Córdoba, Corrientes, piaskownicę Pico, Gualeguaychu, La Rioja, Mar del Plata, Mendoza, Miramar, Necochea, Neuquén, Oberá, Olavarría, Paraná, Posadas, Rafaela, Rio Tercero, Rosario, Salta, San Carlos de Bariloche, San Luis, San Miguel de Tucumán, San Pedro, Santa Fe, Tandil, Ushuaia, stan Wiktoria, Viedma, Villa María</p>|
| Barbados       |  Barbados |
| Brazylia         | <p>Angra dos Reis, Anápolis, Apucarana, Aracaju, Araraquara, Araxa, Araçatuba, Atibaia, bage, Barretos, Bauru, Bebedouro, Belem, Belo Horizonte, Blumenau, Boa Vista, Botucatu, Brazylia, Caldas Novas, Campina Grande, Campinas, Campo Belo, Campo Grande, Caraguatatuba, Caratinga, Cascavel, Cataguases, Caxias, Leopoldina e Região, Catalão, Caxias do Sul, Chapecó, Cianorte, Conselheiro Lafaiete, Corumbá, Criciúma, Cruzeiro do Sul, Cuiabá, Curitiba, Curitibanos, Curvelo, Diamantina Divinópolis, Dourados, Estrela, Feira de Santana, Fernando de Noronha, Florianópolis, Fortaleza, Foz do Iguaçu, Franca, Garanhuns, Goiania, Governador Valadares, Guarapuava, Imperatriz, Ipatinga, Irati, Itabira, Itabuna, Itajaí, Itajuba, Ituiutaba, Jaguarao, Jaraguá Sul, Joao Pessoa, Joinville, Juazeiro do Norte, Juiz de Fora, Jundiaí, Lages, Lavras e Regiao, Lucas do Rio Verde, Londrina, Macapa, Macaé, Maceió, Mafra e Rio Negro, Manaus, Manhuacu, Maringá, Marília, Monte Carmelo , Montes Claros, Mossoró, świętowanie, Osorio, Ourinhos, Ouro Preto, Palmas, Paracatu, Paranaguá, Parnaíba, Passo Fundo, Passos, Pato Branco, Patos de Minas, Patrocínio, Pelotas, Picos, Piracicaba, Pirapora, Pocos de Caldas, Ponta Grossa, Alegre, Porto Ferreira, Porto Seguro, Porto Velho, Praia Grande, Recife, Ribeirão Preto, Rio, Rio Branco, Rio Verde, Rondonópolis, Salinas, Święty, Santa Cruz do Sul, Santa Maria, Santa Rita do Sapucaí, Santarem, Sul del Estero, Santos, Wyspy Świętego Gabriel do Oeste, Wyspy Świętego Joao del Rei, Tiradentes e Regiao, Wyspy Świętego Jose, Carlos, so Francisco do Sul, so José dos Campos, so Lourenço, so Luís, Taubaté, Telemaco Borba, Teofilo Otoni, Teresina, Toledo, Três Lagoas, Tucurui, Ubatuba, Uberaba, Uberlândia, Ubá, Uruguaiana, Varginha, Vicosa, Videira & Fraiburgo , Vitória, Vitória da Conquista, Volta Redonda, Votuporanga </p>|
| Kanada | Banff (AB), Brandon (MB), Calgary (AB), Chatham-Kent (ON), Comox Dolina (BC), Cowichan Dolina (BC), Cranbrook (BC), Edmonton (AB), Fort St. Jan, Fredericton (NB), nowszy Sudbury (ON), nowszy Vancouver (BC), Halifax (NS), Kamloops (BC), Kelowna-Vernon (BC), Kingston (ON), Londyn (ON), Moncton (NB), Montrealu (QC), Nanaimo (BC), Ottawa (ON), Wyspa Księcia George (BC), Québec City (QC), Red Deer (AB), Regina (SK), Rimouski (QC), Saskatoon (SK), Sherbrooke (QC), południowo-zachodnie Kolumbia Brytyjska (BC), Squamish (BC), St. Jan (NL), słońca wybrzeża, Thunder (ON), Toronto (ON), stan Wiktoria (BC), zachodnie Kootenay (BC), Whistler (BC), Windsor (ON), Winnipeg (MB), Woodstock</p>|
| Chile  | <p>Antofagasta, Arica, Aysén, Chillán, Concepción, Constitución, Copiapó, Curicó, Iquique, La Serena y Coquimbo, Linares, Los Angeles (Chile), Los Lagos, Punta, Rancagua,, Talca, Temuco, Valdivia, Valparaíso, Viña del mar</p>|
| Kolumbia | <p>Barranquilla, Bogotá, Bucaramanga, cali, Kartageny, Ibagué, Medellín, Pasto, Popayán, Santa Marta, Sincelejo, Valledupar</p>|  
| Kostaryka | José San|
| Dominikana | Domingo Santo |
| Ekwador | Cuenca, Guayaquil, Loja, Manta, Milagro|
| Salwador | Salwador sieci San |
 | Gwatemala | Ciudad de Gwatemala (GT) |
| Meksyk | Acapulco, Aguascalientes, Cancun, Durango, Meksyk City, Guadalajara, Lion, Merida, Monterrey, Puebla, Portoryko Vallarta, Querétaro, San Luis Potosi, Tijuana, Torreon|
| Nikaragua | Managua |
| Panama | Panama|
| Peru | Cusco, Lima |
| Portoryko | Juan San |
| Surinam | Paramaribo |
| Urugwaj | Montevideo (, Paysandu, Punta del Este, Salto |
| Stany Zjednoczone Ameryki | <p>Albany (GA), Albany (NY), Albuquerque (NM), mocowanie (klucza uwierzytelniania), Ann Arbor (MI) Appleton-Oshkosh-Neenah (WI), Asheville (), Ateny (GA), Ateny (w przypadku), Atlanta (GA), Austin (TX), Bakersfield (CA), Baltimore (MD), Bend-Redmond (lub), Berkshire County (MA), Birmingham (AL), Bloomington (IN), Boise (ID), Boston (MA), Boulder (CO), wzzielony (KY), Brevard powiat (FL), Buffalo (NY), Butte (MT), Wyspy Zielonego dorsza (MA), powiatu centrum (PA), Champaign-Urbana (IL), Charleston (SC), Charleston (WV), Charlotte (NC), Charlottesville (VA), Chattanooga (TN), Cheyenne (WY), Chicago (IL), Cincinnati (z), powiaty cytrusów (FL), Cleveland (), Coachella Dolina (CA), Kolorado sprężyny (CO), Kolumbia (TN), Kolumbia (SC), Columbus (niestety), korpus Christi (TX), Dallas/warta (TX), Dayton (), Delaware, Denver (CO), Des Moines (IA), Detroit (MI), Duluth (MN), El Paso (TX), Eugene (lub), Fairbanks (klucza uwierzytelniania), Fargo (ND) , Fayetteville (NC), Flagstaff (AZ), krzesiwowych (MI) Fort Collins (CO), Fort Wayne (IN), Fresno (CA), Gainesville (FL), Grand Rozwidleni (ND), Grand Rapids (MI), Zielona kieszeń (WI), Greensboro (NC), Greenville (SC), Gunnison (CO), Hampton drogi (VA), Hanford (CA), Hartford (CT), Hernando powiat (FL), Hinesville (GA), Honolulu (HI), Houston (TX), Humboldt County (CA), Huntsville (AL), Indianapolis (w), Ithaca (NY), Jacksona (MS), Jacksona (TN), Jacksonville-St. Województwo Jan (FL), Johnsonem miasto (TN), Jonesboro (AR), Joplin (MO), Juneau (klucza uwierzytelniania), Kalamazoo (MI), Kalispell (MT), Kansas City (MO), Kauai (HI), Ketchum (ID), Knoxville (TN), Lafayette (w), Lancaster (PA), Lansing (MI), Laredo (TX), Las Vegas (NV), Lawrence (KS), Lewandowski County (FL), Lexington (KY), Lincoln County (lub), mały Rock (AR), Los Angeles (CA), Louisville (KY), Lubbock (TX), Madison (WI), Manchester (NH) , McAllen (TX), Memphis (TN), Miami (FL), Milwaukee-Waukesha (WI), Minneapolis-St. Paul (MN), Missoula (MT), Modesto (USA), Moline (IL), Monroe County (PA), Montgomery (AL), Morgantown (WV), Nashville (TN), biały nawaho kraj), New Haven (CT), New Orleans (LA), NYC-NJ (NY), Ocala (FL), Okaloosa powiat (FL), Oklahoma City (OK), Omaha (NE), Orlando (FL), Palm pustynia (CA), Panama City (FL), Pensacola (FL), Peoria (IL), Philadelphia (PA), Phoenix (AZ), Pittsburgh (PA), Portland (ME), Portland (lub), Racine (WI), Raleigh (NC), Redding (CA), Reno & Lake Tahoe (NV), Richmond (VA), Roanoke Dolina (VA-Lynchburg), Rochester (NY), Rockford (IL), Rocky Mount (NC), Rocky górski Narodowy Park (CO), nieautoryzowane doliny (lub), Roseburg (lub), Roseville (CA), Sacramento (CA), Salem (lub), sole Lake City (UT), San Antonio (TX), San Diego (CA), San Luis Obispo (CA), Santa Barbara (CA), Santa Fe (NM), Sarasota (FL) , Savannah (GA), Seacoast region (NH), Seattle-Tacoma-Bellevue (WA), obszar wnęki SF (CA), obszar SF-San Jose (CA), Sioux Miasto (IA), Siouxd (SD), sitka (klucza uwierzytelniania), Spokane (WA), Springfield (MA), zgięcie Południowe (w), Springfield (IL), Springfield (masa), St. George (UT), St. Louis (MO), Stockton (CA), Syracuse-Utica (NY), Tallahassee (FL), Tampa-St. Petersburg (FL), Terre Haute (IN), Toledo (), Topeka (KS), przechodzenie przez miasto (MI), Tucson (AZ), Tulsa (OK), Vermont, Victorville (CA), Volusia County (FL), Waco (TX), Waszyngton (DC), Waterbury (CT), Wichita (KS), Wichita: (TX) Wilmington (NC), Yakima (WA), Youngstown (), Prowincja</p>|
| + Wyspy Dziewicze Stanów Zjednoczonych | Wyspy Dziewicze Stanów Zjednoczonych * |
| Wenezuela | Caracas |

## <a name="asia-pacific"></a>Azja i Pacyfik

| Kraj/region |  Miasto (obszar Metro) |
|--------|---------|
| Australia | <p>Adelajda, Alicja sprężyny, Bowen, Brisbane, Bundaberg QLD, granie, Cairns, Canberra, Darwin, Gladstone, Hobart, Innisfail, Launceston, Mackay, Wyspa magnetyczna, kieszeń na Maryborough-Hervey, Melbourne, Nowa Południowa Walia, Perth, RockHampton, Południowe Południowo-Wschodnia Queensland, Sydney, Toowoomba, Townsville, stan Wiktoria, Warwick, Yeppoon</p> |
| Brunei | Bandar seria Begawan |
| Chiny | <p> Changchun, Changsha, Chengdu, Czungking, Dalian, Datong, Dongguan, Hangzhou, Harbin, Jiangyin, Jinan, Nanjing, Nantong, Ningbo, Pingdingshan, Qingdao, Shenyang, Suzhou, Tangshan, Tianjin, Weifang, Wuhan, Wuxi</P>|
| SRA Hongkong | Hongkong SAR *|
| SRA Makau | Makau SAR *|
| Malediwy | Mężczyźni |
| Indie | Ahmedabad, Bengaluru, Delhi, Hyderabad, Mumbaj, Mysuru, Pune|
| Indonezja | Bandung, Banjarmasin, Banyuwangi, Batam, Denpasar, Dżakarta, Kediri, Malang, Palembang, Semarang, Surabaya, Surakarta, Yogyakarta |
| Japonia | Hokkaido, Shizuoka Prefektura, Tokio, Wakkanai, Yamanashi Prefektura |
| Malezja | Ipoh, Johar Bahru, Kuala Lumpur, Kuantan, Penang |
| Nowa Zelandia | Auckland, Christchurch, Dunedin, Queenstown, Timaru, Wellington|
| Filipiny | Manila |
| Singapur | Singapurze |
| Korea Południowa | Busan, Seul |
| Tajwan | Changhua, Tajpej |
| Tajlandia | Bangkok, Chiang poczty |
| Uzbekistan | Samarkand |
| Wietnam | Hanoi, Minh City (miasto) |

## <a name="europe"></a>Europa

| Kraj/region |  Miasto (obszar Metro) |
|----------------|---------|
| Andora        | Andora La Vella |
| Austria        | Wiedeń |
| Białoruś        | Gomel, Grodno, Polotsk & Novopolotsk, Zhlobin, Vileyka, Maladziečna, Mińsk, Rechytsa |
| Belgia        | Belgijsk |
| Boliwia        | La Paz, Santa Cruz de la Sierra |
| Bośnia i Hercegowina | Sarajewo |
| Bułgaria       | <p>Balchik, Blagoevgrad, Burgas, Dobrich, Gabrovo, Haskovo, Kardzhali, Lovech, Nessebar, Pazardzhik, Pernik, Pleven, Plovdiv, Ruse, Shumen, Sliven, Stara Zagora, Vratsa, Yambol, Varna, Weliko, Sofia</P> |
| Chorwacja | Crikvenica, Dubrovnik, Rijeka, Slovanski Brod, Zagrzeb |
| Cypr | Larnaca, Limassol, Nikozja |
| Republika Czeska | Brno, Jablonec, Karlovy Zróżnicuj, Liberec, Ostrava, Praga |
| Dania   | Danii |
| Estonia   | Estońskiej |
| Finlandia   | Hämeenlinna, Helsinki, Joensuu, Jyväskylä, Kajaani, Kouvola-kotka, Kuopio, Lappeenranta, Mikkeli, Oulu, Pori, Rovaniemi, Seinäjoki, Tampere, Turku, Vaasa|
| Francja    | <p>Amberieu-EN-Bugey, Amiens, Angers, Annecy, Annonay, Arras, Aubenas, Bayonne, Besançon, Blois, Bordeaux, Boulogne sur Mer, Brest, Briançon, Cannes, Châlons-en-Szampane, Chartres, Clermont-Ferrand, Colmar, Côte d'Azur, DAX, Dijon, Grenoble, Haguenau, La Rochelle, Le Mans, Lens, Lille, Lorient, Lyon, Mac, Marsylii & Provence, Metz, Millau, Monts-de-Marsan, Montpellier, Mulhouse, Nancy, Nantes, Nice, Nice Côte d'Azur, Nimes, Normandia, Nyons, Paryż, Poitiers, Privas, Quimper, Rennes, Saint Malo, Saint-Étienne, Saint-Nazaire, Saintes, Sarrebourg, Sete, Strasburgu, Tarbes, Toulouse, wycieczki</P> |
| + Gujana Francuska | Kajenna |
| + Nowa Kaledonia | Nouméa  |
| Gruzja | Tibilisi |
| Niemcy | <p>Berlin, Brandenburg, Bremen & Niedersachsen, Kolonia, Eisenach, Frankfurt, Hamburg, Karlsruhe, Mainz, München-Monachium, Rhein-Neckar region, Rhein-Ruhr region, Stuttgart, Titisee-Neustadt, Ulm</P> |
| Grecja | <p>Agrinio, Aigio, Ateny, Arta, Amorgos, Chania, Corfu, Chios Kos, Heraklion, Ioannina, Kavala, Kalamata, Khios, Komotini, Kos, Larissa, Meganisi, Milos, Mykonos, Patra, Rethimno, Rhodes </P> |
| Węgry | Budapeszt, Gyor, Miskolc, County, PESC, Szeged, Székesfehérvár |
| Islandia | Ísland — Islandia * |
| Irlandia | Irlandii |
| Włochy   | <p>Agrigento, Alessandria, Ancona, Bari, Bologna-Bologne, Cagliari-Sardynii, Campobasso, Catania e Messina, Cosenza, Crema, Cremona, Crotone, Cuneo, Firenze-Florencji, Foggia, Genova-Genoa, Iglesias, La Spezia, Lecce, Matera, Milano-Mediolan, Napoli-Naples, Padova, Palermo, Parma, Perugia, Pescara, Pisa, Potenza, Roma Siena, Grosseto-Siracusa, Syracuse. </p> |
| Łotwa | Rīga |
| Liechtenstein | Lichtenstein |
| Litwa | Druskininkai, Kauno, Klaipėda, Panevėžys, Wilno |
| Luksemburg | Luksemburg |
| Mołdawia | Kiszyniów |
| Czarnogóra | Podgorica |
| Holandia | Królestw |
| Norwegia | Norwegia |
| Polska | <p>Wrocław, Białystok, Bydgoszcz, Elbląg, Elk, Gorzow, Kętrzyna, Kraków, Leszno, Lodz, Lublin, Mrągowo, Olsztyn, Poznań, Rzeszów, Sanok, Starachowice, Świonujście, Szczecin, Tricity, Warszawa, Wodzisław Śląski, Wrocław, Zakopane</p> |
| Portugalia | Bragança, Coimbra, Funchal, Leiria, Lisboa, Portimao, Porto|
| Malta | Malta |
| Rumunia | <p>Alba Iulia, Arad, Bistrița, Brăila, Braşov, Bukareszt, Buzau, Cluj Napoca, Constanța, Craiova, Deva, Focșani, Galati, Iaşi, Miercurea Ciuc, Oradea, Piatra Neamt, Pitești, Ploieşti, Reșița, Satu Mare, Sibiu, Suceava, Targu Mures, Timisoara, Tulcea, Zalau</p> | 
| Rosja  | Kaliningrad, Rostov-on-Jan, Wołgograd, Jekaterynburg, Kazan, Kirov, Krasnodar, Moskwa, Nalchik, Nizhny Nowogród, Nowosybirsk, Noyabrsk, Omsk, Oryol, uprawnienie, St Petersburg, Pyatigorsk, Tver, Tomsk ( |
| Serbia  | Beograd, Kragujevac, NIS, Nowy Sad, Valjevo, Subotica | 
| Słowacja | Banská Bystrica, Bratysława, Kosice, Presov, Prievidza, Ruzomberok, Liptovsky Mikulas, Stará Ľubovňa, Trencin |
| Słowenia | Koper, Lublana |
| Hiszpania    | <p>Albacete, Corunna, Alicante, Almería, Asturias, Avila, Badajoz, Bay z Cadiz, Barcelona, Bilbao, Burgos, Caceres, Campo de, Castellon de la Plana, Ceuta, Ciudad Real, Cordoba, Cuenca, El Hierro, Ferrol, Fuerteventura, Gran Canaria, Granada, Huelva, Huesca, Ibiza, Jaén-Úbeda, La Gomera, La Palma, Lanzarote, León, Lleida, Logroño, Lugo, Madryt, Malaga, Mallorca-Majorca, Melilla, Menorca, Merida, Murcia, Ourense, Palencia,, Pamplona, San Salamanca , Santander, Compostela, Segovia, Seville, Soria, Tarragona-Reus, Tenerife, Toledo, Walencja, Valladolid, Vigo, Vitoria-Gasteiz, Zaragoza-Saragossa</p> |
| Szwecja | Goteborg/Gothenburg/Jonkoping, Malmö kommun-Malmö, Norrköping Och Linköping, Sztokholm, Sundsvall |
| Szwajcaria | Basel, Genewa, Yverdon-les-Bains, Zurych | 
| Turcja | Adana-Mersin, Ankara, Antalya, Balıkesir, bilecik, Bolu, Bursa, Çorum, Denizli, duzce, Edirne, Elazig, Eskisehir, Stambuł, Izmir-Aydin, Kahramanmaras, Kayseri, Konya, Malatya, Muğla, Samsun, Şanlıurfa, Trabzon |
| Zjednoczone Królestwo | Wschodnie Anglia, wschód Midlands, Londyn i Południowo-Wschodnia, północno-wschodnia, północne Zachodnie, Irlandia Północna, Szkocja, południowe zachodnie, Walia, zachodnie Midlands, Yorkshire |
| Ukraina | Kharkiv, Zhytomyr, Kijów, Lviv, Chernivtsi |

## <a name="middle-east-and-africa"></a>Bliski Wschód i Afryka

| Kraj/region |  Miasto (obszar Metro) |
|---------|---------|
| Bahrajn | Bahrajnu |
| Burkina Faso | Wagadugu |
| Kongo | Kinszasa |
| Egipt | Cairo    |
| Izrael| Izrael  |
| Kenia | Nairobi  |
| Madagaskar | Antananarywa |
| Maroko | Casablanca, Essaouira, Khouribga, Tétouan|
| Katar| Doha|
| Arabia Saudyjska | Thuwal |
| Senegal | Dakar |
| Republika Południowej Afryki | Kapsztad |
| Tunezja | Kairouan |
| Zjednoczone Emiraty Arabskie  | Abu Zabi, Dubaj |


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak żądać danych tranzytowych przy użyciu usług mobilności (wersja zapoznawcza):

> [!div class="nextstepaction"]
> [Jak żądać danych tranzytowych](how-to-request-transit-data.md)

Informacje na temat żądania danych w czasie rzeczywistym przy użyciu usług mobilności (wersja zapoznawcza):

> [!div class="nextstepaction"]
> [Jak żądać danych w czasie rzeczywistym](how-to-request-real-time-data.md)

Poznaj dokumentację interfejsu API usługi Azure Maps Mobility Services (wersja zapoznawcza)

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usług mobilności](/rest/api/maps/mobility)
