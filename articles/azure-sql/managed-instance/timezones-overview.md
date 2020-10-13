---
title: Strefy czasowe wystąpienia zarządzanego usługi Azure SQL
description: Informacje o strefach czasowych specyficznych dla wystąpienia zarządzanego usługi Azure SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 10/12/2020
ms.openlocfilehash: 7b95ddfdb75dd5e5951a9c95442798692582fe6a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978377"
---
# <a name="time-zones-in-azure-sql-managed-instance"></a>Strefy czasowe w wystąpieniu zarządzanym usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Uniwersalny czas koordynowany (UTC) to zalecana strefa czasowa dla warstwy danych rozwiązań w chmurze. Wystąpienie zarządzane usługi Azure SQL oferuje również pewne strefy czasowe, które spełniają potrzeby istniejących aplikacji, które przechowują wartości daty i godziny oraz wywołują funkcje daty i godziny z niejawnym kontekstem określonej strefy czasowej.

Funkcje języka T-SQL, takie jak [GETDATE ()](/sql/t-sql/functions/getdate-transact-sql) lub kod CLR, obserwują strefę czasową ustawioną na poziomie wystąpienia. Zadania agenta SQL Server są również zgodne z harmonogramami zgodnie ze strefą czasową wystąpienia.

  > [!NOTE]
  > Azure SQL Database nie obsługuje ustawień strefy czasowej; Zawsze postępuj według czasu UTC. Użyj [w strefie czasowej](/sql/t-sql/queries/at-time-zone-transact-sql) w SQL Database, jeśli chcesz interpretować informacje o dacie i godzinie w strefie czasowej innej niż UTC.

## <a name="supported-time-zones"></a>Obsługiwane strefy czasowe

Zestaw obsługiwanych stref czasowych jest Dziedziczony z bazowego systemu operacyjnego wystąpienia zarządzanego. Jest ona regularnie aktualizowana w celu uzyskania nowych definicji stref czasowych i odzwierciedla zmiany w istniejących.

[Zasady zmiany czasu/strefy czas letni](https://aka.ms/time) gwarantuje prawidłowość historyczną od 2010 do przodu.

Lista z nazwami obsługiwanych stref czasowych jest dostępna za pomocą widoku system [sys.time_zone_info](/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) .

## <a name="set-a-time-zone"></a>Ustawianie strefy czasowej

Strefę czasową wystąpienia zarządzanego można ustawić tylko podczas tworzenia wystąpienia. Domyślna strefa czasowa to UTC.

  >[!NOTE]
  > Nie można zmienić strefy czasowej istniejącego wystąpienia zarządzanego.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Ustaw strefę czasową za pomocą Azure Portal

Po wprowadzeniu parametrów dla nowego wystąpienia wybierz strefę czasową z listy obsługiwanych stref czasowych.
  
![Ustawianie strefy czasowej podczas tworzenia wystąpienia](./media/timezones-overview/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Określ właściwość timezoneId w [szablonie Menedżer zasobów](https://aka.ms/sql-mi-create-arm-posh) , aby ustawić strefę czasową podczas tworzenia wystąpienia.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Na końcu tego artykułu znajduje się lista obsługiwanych wartości właściwości timezoneId.

Jeśli nie zostanie określony, strefa czasowa jest ustawiana na czas UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Sprawdź strefę czasową wystąpienia

Funkcja [CURRENT_TIMEZONE](/sql/t-sql/functions/current-timezone-transact-sql) zwraca nazwę wyświetlaną strefy czasowej wystąpienia.

## <a name="cross-feature-considerations"></a>Zagadnienia dotyczące różnych funkcji

### <a name="restore-and-import"></a>Przywróć i Importuj

Można przywrócić plik kopii zapasowej lub zaimportować dane do wystąpienia zarządzanego z wystąpienia lub serwera z różnymi ustawieniami strefy czasowej. Należy zachować ostrożność. Analizuj zachowanie aplikacji oraz wyniki zapytań i raportów, podobnie jak w przypadku transferu danych między dwoma wystąpieniami SQL Server przy użyciu różnych ustawień strefy czasowej.

### <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Gdy wykonujesz przywracanie do punktu w czasie, czas przywracania jest interpretowany jako czas UTC. W ten sposób można uniknąć wszelkich niejasności z powodu czasu letniego i jego potencjalnych zmian.

### <a name="auto-failover-groups"></a>Grupy automatycznego trybu failover

Korzystanie z tej samej strefy czasowej w wystąpieniu podstawowym i pomocniczym w grupie trybu failover nie jest wymuszane, ale zdecydowanie zalecamy.

  >[!WARNING]
  > Zdecydowanie zalecamy użycie tej samej strefy czasowej dla wystąpienia podstawowego i dodatkowego w grupie trybu failover. Z powodu niektórych rzadkich przypadków użycia utrzymywanie tej samej strefy czasowej w wystąpieniach podstawowych i pomocniczych nie jest wymuszane. Ważne jest, aby zrozumieć, że w przypadku ręcznej lub automatycznej pracy awaryjnej wystąpienie pomocnicze zachowa oryginalną strefę czasową.

## <a name="limitations"></a>Ograniczenia

- Nie można zmienić strefy czasowej istniejącego wystąpienia zarządzanego. Aby obejść ten krok, Utwórz nowe wystąpienie zarządzane ze stosowną strefą czasową, a następnie ręcznie wykonaj kopię zapasową i Przywróć lub to, co jest zalecane, wykonaj [przywracanie do punktu w czasie dla wielu wystąpień](https://docs.microsoft.com/azure/azure-sql/managed-instance/point-in-time-restore?tabs=azure-portal#restore-an-existing-database).
- Procesy zewnętrzne uruchomione z zadań agenta SQL Server nie obserwują strefy czasowej wystąpienia.

## <a name="list-of-supported-time-zones"></a>Lista obsługiwanych stref czasowych

| **Identyfikator strefy czasowej** | **Nazwa wyświetlana strefy czasowej** |
| --- | --- |
| Linii zmiany (czas standardowy) | (UTC-12:00) Międzynarodowa linia daty — zachód |
| UTC — 11 | (UTC-11:00) Uniwersalny czas koordynowany-11 |
| Aleuty (czas standardowy) | (UTC-10:00) Wyspy Aleuty |
| Hawajski (czas standardowy) | (UTC-10:00) Hawaje |
| Markizy (czas standardowy) | (UTC-09:30) Wyspy markizy |
| Alaska ((czas standardowy) | (UTC-09:00) Alaska |
| UTC — 09 | (UTC-09:00) Uniwersalny czas koordynowany-09 |
| Pacyficzny czas standardowy (Meksyk) | (UTC-08:00) Kalifornia |
| UTC — 08 | (UTC-08:00) Uniwersalny czas koordynowany-08 |
| Pacyfik (czas standardowy) | (UTC-08:00) Czas pacyficzny (Stany Zjednoczone & Kanada) |
| Stany Zjednoczone (górski czas standardowy) | (UTC-07:00) Arizona |
| Górski czas standardowy (Meksyk) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Górski czas standardowy | (UTC-07:00) Czas górski (Stany Zjednoczone & Kanada) |
| Ameryka Środkowa (czas standardowy) | (UTC-06:00) Ameryka Środkowa |
| Środkowy czas standardowy | (UTC-06:00) Czas środkowy (Stany Zjednoczone & Kanada) |
| Wyspa Wielkanocny (czas standardowy) | (UTC-06:00) Wyspa wielkanocny |
| Środkowy czas standardowy (Meksyk) | (UTC-06:00) Guadalajara, Meksyk, Monterrey |
| Kanada (środkowy czas standardowy) | (UTC-06:00) Saskatchewan |
| Pacyfik (czas standardowy) | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Wschodni czas standardowy (Meksyk) | (UTC-05:00) Chetumal |
| Wschodni czas standardowy | (UTC-05:00) Czas wschodni (Stany Zjednoczone & Kanada) |
| Haiti (czas standardowy) | (UTC-05:00) Haiti |
| Kuba (czas standardowy) | (UTC-05:00) Hawana |
| USA (Wschodni czas standardowy) | (UTC-05:00) Indiana (wschód) |
| Turks i Caicos (czas standardowy) | (UTC-05:00) Turks i Caicos |
| Paragwaj (czas standardowy) | (UTC-04:00) Asuncion |
| Atlantycki czas standardowy | (UTC-04:00) Czas atlantycki (Kanada) |
| Wenezuela (czas standardowy) | (UTC-04:00) Caracas |
| Brazylia Środkowa (czas standardowy) | (UTC-04:00) Cuiaba |
| Zachodnioeuropejski (zachodni czas standardowy) | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Arabia Saudyjska (czas standardowy) | (UTC-04:00) Znajdując |
| Nowa Fundlandia (czas standardowy) | (UTC-03:30) Nowa Fundlandia |
| Tocantins ((czas standardowy) | (UTC-03:00) Araguaina |
| E. Ameryka Południowa (czas standardowy) | (UTC-03:00) Brazylia |
| Wschodni czas standardowy () | (UTC-03:00) Kajenna, Fortaleza |
| Argentyna (czas standardowy) | (UTC-03:00) Miasto Buenos Aires |
| Grenlandia (czas standardowy) | (UTC-03:00) Objęt |
| Montevideo ((czas standardowy) | (UTC-03:00) Montevideo ( |
| Magallanes (czas standardowy) | (UTC-03:00) Punta |
| Saint-Pierre (czas standardowy) | (UTC-03:00) Saint-Pierre i Miquelon |
| Bahia ((czas standardowy) | (UTC-03:00) Salwadoru |
| UTC — 02 | (UTC-02:00) Uniwersalny czas koordynowany-02 |
| Mid-Atlantic czasu standardowego | (UTC-02:00) Mid-Atlantic — stary |
| Azory (czas standardowy) | (UTC-01:00) Azory |
| Wyspy Zielonego Przylądka (czas standardowy) | (UTC-01:00) Wyspy Zielonego Przylądka |
| UTC | (UTC) Uniwersalny czas koordynowany |
| GMT (czas standardowy) | (UTC + 00:00) Dublin, Edynburg, Lizbona, Londyn |
| Greenwich (czas standardowy) | (UTC + 00:00) Monrowia, Rejkiawiku |
| K. Europa (czas standardowy) | (UTC + 01:00) Amsterdam, Berlin, Berno, Rzym, Sztokholm, Wiedeń |
| Europa Środkowa (czas standardowy) | (UTC + 01:00) Belgrad, Bratysława, Budapeszt, Lublana, Praga |
| Romański czas standardowy | (UTC + 01:00) Bruksela, Kopenhaga, Madryt, Paryż |
| Maroko (czas standardowy) | (UTC + 01:00) Casablanca |
| Wyspa Świętego Tomasza (czas standardowy) | (UTC + 01:00) Wyspa Świętego Tomasza |
| Środkowoeuropejski czas standardowy | (UTC + 01:00) Sarajewo, Skopje, Warszawa, Zagrzeb |
| K. Afryka Środkowa (czas standardowy) | (UTC + 01:00) Zachodnie Afryka Środkowa |
| Jordania (czas standardowy) | (UTC + 02:00) Amman |
| GTB (czas standardowy) | (UTC + 02:00) Ateny, Bukareszt |
| Bliski Wschód (czas standardowy) | (UTC + 02:00) Bejrut |
| Egipt (czas standardowy) | (UTC + 02:00) Kair |
| E. Europa (czas standardowy) | (UTC + 02:00) Kiszyniów |
| Syria (czas standardowy) | (UTC + 02:00) Damaszek |
| Zachodni Bank (czas standardowy) | (UTC + 02:00) Gazy, Hebron |
| Republika Południowej Afryki (czas standardowy) | (UTC + 02:00) Harare, Pretoria |
| Finlandia ((czas standardowy) | (UTC + 02:00) Helsinki, Kijów, Ryga, Sofia, Tallin, Wilno |
| Izrael (czas standardowy) | (UTC + 02:00) Jerozolima |
| Kaliningrad (czas standardowy) | (UTC + 02:00) Kaliningrad |
| Sudan (czas standardowy) | (UTC + 02:00) Chartum |
| Libia (czas standardowy) | (UTC + 02:00) Trypolis |
| Namibia (czas standardowy) | (UTC + 02:00) Windhuk |
| Arabski czas standardowy | (UTC + 03:00) Bagdad |
| Turcja (czas standardowy) | (UTC + 03:00) Stambuł |
| Arabski czas standardowy | (UTC + 03:00) Kuwejt, Rijad |
| Białoruś (czas standardowy) | (UTC + 03:00) Mińsk |
| Rosyjski czas standardowy | (UTC + 03:00) Moskwa, Sankt Petersburg |
| E. Afryka (czas standardowy) | (UTC + 03:00) Nairobi |
| Iran (czas standardowy) | (UTC + 03:30) Teheran |
| Arabia (czas standardowy) | (UTC + 04:00) Abu Zabi, Maskat |
| Astrachań (czas standardowy) | (UTC + 04:00) Astrachań, Uljanowsk |
| Azerbejdżan (czas standardowy) | (UTC + 04:00) Baku |
| Strefa 3 czasu Rosji | (UTC + 04:00) Iżewsk, Samara |
| Mauritius (czas standardowy) | (UTC + 04:00) Louis portów |
| Saratów (czas standardowy) | (UTC + 04:00) Saratów |
| Gruziński czas standardowy | (UTC + 04:00) Tibilisi |
| Wołgograd (czas standardowy) | (UTC + 04:00) Wołgograd |
| Kaukaz ((czas standardowy) | (UTC + 04:00) Erywań |
| Afganistan (czas standardowy) | (UTC + 04:30) Kabul |
| Azja Zachodnia (czas standardowy) | (UTC + 05:00) Aszchabad, Taszkient |
| Jekaterynburg (czas standardowy) | (UTC + 05:00) Jekaterynburg |
| Pakistan (czas standardowy) | (UTC + 05:00) Islamabad, Karaczi |
| Indie (czas standardowy) | (UTC + 05:30) Chennai, Kalkuta, Mumbaj, Nowe Delhi |
| Sri Lanka (czas standardowy) | (UTC + 05:30) Sri Dźajawardanapura Kotte |
| Nepal (czas standardowy) | (UTC + 05:45) Katmandu |
| Azja Środkowa (czas standardowy) | (UTC + 06:00) Astana |
| Bangladesz (czas standardowy) | (UTC + 06:00) Dakka |
| Omsk (czas standardowy) | (UTC + 06:00) Omsk |
| Myanmar (czas standardowy) | (UTC + 06:30) Yangon (Rangun) |
| Azja Wschodnia (czas standardowy) | (UTC + 07:00) Bangkok, Hanoi, Dżakarta |
| Ałtaj ((czas standardowy) | (UTC + 07:00) Barnauł, Gorno-Altaysk |
| K. Mongolia (czas standardowy) | (UTC + 07:00) Ajmak kobdoski |
| Azja Północna (czas standardowy) | (UTC + 07:00) Krasnojarsk |
| Azotan. Azja Środkowa (czas standardowy) | (UTC + 07:00) Nowosybirsk |
| Tomsk ((czas standardowy) | (UTC + 07:00) Tomsk ( |
| Chiny (czas standardowy) | (UTC + 08:00) Pekin, Czungking, Hongkong SAR, Urumczi |
| Azja Północna (Wschodni czas standardowy) | (UTC + 08:00) Irkuck |
| Singapur (czas standardowy) | (UTC + 08:00) Kuala Lumpur, Singapur |
| K. Australia (czas standardowy) | (UTC + 08:00) Perth |
| Tajpej (czas standardowy) | (UTC + 08:00) Tajpej |
| Ułan Bator (czas standardowy) | (UTC + 08:00) Ułan Bator |
| Australia Środkowa W. czas standardowy | (UTC + 08:45) Eucla |
| Zabajkale ((czas standardowy) | (UTC + 09:00) Czyta |
| Tokio (czas standardowy) | (UTC + 09:00) Osaka, Sapporo, Tokio |
| Korea Północna (czas standardowy) | (UTC + 09:00) Phenian |
| Korea (czas standardowy) | (UTC + 09:00) Seul |
| Jakuck (czas standardowy) | (UTC + 09:00) Jakuck |
| Znormalizowane. Australia (czas standardowy) | (UTC + 09:30) Adelajda |
| Australia (środkowy czas standardowy) | (UTC + 09:30) Darwin |
| E. Australia (czas standardowy) | (UTC + 10:00) Brisbane |
| Australia (Wschodni czas standardowy) | (UTC + 10:00) Canberra, Melbourne, Sydney |
| Zachodni Pacyfik (czas standardowy) | (UTC + 10:00) Guam, Port Moresby |
| Tasmania ((czas standardowy) | (UTC + 10:00) Hobart |
| Władywostok (czas standardowy) | (UTC + 10:00) Władywostok |
| Howe (czas standardowy) | (UTC + 10:30) Wyspa Howe |
| Bougainville (czas standardowy) | (UTC + 11:00) Wyspa Bougainville |
| Rosja strefa czasowa 10 | (UTC + 11:00) Czokurdach |
| Magadan (czas standardowy) | (UTC + 11:00) Magadan |
| Norfolk (czas standardowy) | (UTC + 11:00) Wyspa Norfolk |
| Sachalin (czas standardowy) | (UTC + 11:00) Sachalin |
| Środkowy Pacyfik (czas standardowy) | (UTC + 11:00) Wyspy Salomona, Nowa Kaledonia |
| Rosja strefa czasowa 11 | (UTC + 12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Nowa Zelandia (czas standardowy) | (UTC + 12:00) Auckland, Wellington |
| UTC + 12 | (UTC + 12:00) Uniwersalny czas koordynowany + 12 |
| Fidżi (czas standardowy) | (UTC + 12:00) Fidżi |
| Kamczatka (czas standardowy) | (UTC + 12:00) Petropavlovsk-Kamchatsky — stary |
| Wyspy Chatham (czas standardowy) | (UTC + 12:45) Wyspy Chatham |
| UTC + 13 | (UTC + 13:00) Uniwersalny czas koordynowany + 13 |
| Tonga (czas standardowy) | (UTC + 13:00) Nuku'alofa |
| Samoa (czas standardowy) | (UTC + 13:00) Zachodniego |
| Wyspy liniowe (czas standardowy) | (UTC + 14:00) Wyspa Kiritimati |

## <a name="see-also"></a>Zobacz też 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [CURRENT_TIMEZONE_ID (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-id-transact-sql)
- [W strefie CZASowej (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
