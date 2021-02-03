---
title: Generowanie raportów trendów i statystyk
description: Zyskaj wgląd w aktywność sieci, statystyki i trendy, korzystając z usługi Defender for IoT — trendy i statystyk.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 20b47204ea8f64bc430f436a9bcf1b2311409aa6
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509490"
---
# <a name="sensor-trends-and-statistics-reports"></a>Trendy czujnika i raporty statystyczne

## <a name="about-sensor-trends-and-statistics-reports"></a>Informacje o trendach czujników i raportach statystycznych

Można tworzyć wykresy widget i wykresy kołowe, aby uzyskać wgląd w trendy sieci i statystyki. Widżety można grupować w obszarze pulpity nawigacyjne zdefiniowane przez użytkownika.

> [!NOTE]
> Analityki i administratorzy zabezpieczeń mogą tworzyć trendy i raporty statystyczne.

Pulpit nawigacyjny składa się z widżetów, które opisują graficznie następujące rodzaje informacji:

- Ruch według portu
- Najpopularniejszy ruch według portu
- Przepustowość kanału
- Całkowita przepustowość
- Aktywne połączenie TCP
- Najpopularniejsza przepustowość według sieci VLAN
- Devices (Urządzenia):
  - Nowe urządzenia
  - Zajęte urządzenia
  - Urządzenia według dostawcy
  - Urządzenia według systemu operacyjnego
  - Liczba urządzeń na sieć VLAN
  - Odłączone urządzenia
- Łączność spadnie o godz.
- Alerty dla incydentów według typu
- Dostęp do tabeli bazy danych
- Elementy widget sekcji protokołu
- DELTAV
  - Rozkład operacji DeltaV Roc
  - DeltaV zdarzenia Roc według nazwy
  - Zdarzenia DeltaV według czasu
- AMS
  - Ruch AMS według portu serwera
  - Ruch z AMS przez polecenie
- Sieć Ethernet i adres IP:
  - Ruch sieciowy sieci Ethernet i IP według usługi PRZELEWu
  - Sieć Ethernet i ruch adresów IP według klasy przelew
  - Ruch sieciowy i adres IP według polecenia
- OPC
  - OPC najważniejsze operacje zarządzania
  - OPC operacji we/wy
- S7 firmy Siemens:
  - Ruch S7 przez funkcję sterowania
  - Ruch S7 przez funkcję podfunkcji
- OKREŚLONE
  - Liczba urządzeń na sieć VLAN
  - Najpopularniejsza przepustowość według sieci VLAN
- 60870-5-104
  - Ruch IEC-60870 przez ASDU
- BACNET
  - Usługi BACnet Services
- DNP3
  - Ruch DNP3 przez funkcję
- SRTP:
  - Ruch SRTP według kodu usługi
  - SRTP błędów według dnia
- SuiteLink:
  - SuiteLink najważniejsze Tagi zapytania
  - Zachowanie tagów numerycznych SuiteLink
- Ruch IEC-60870 przez ASDU
- Ruch DNP3 przez funkcję
- Ruch MMS według usługi
- Ruch Modbus przez funkcję
- Ruch OPC-UA według usługi

> [!NOTE]
>  Czas w widżetach jest ustawiany na podstawie czasu czujnika.

## <a name="create-reports"></a>Tworzenie raportów

Aby wyświetlić pulpity nawigacyjne i widżety:

Wybierz pozycję **trendy & dane statystyczne** w menu po stronie.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Zrzut ekranu przedstawiający badanie.":::

Domyślnie wyniki są wyświetlane w celu wykrycia w ciągu ostatnich 7 dni. Możesz użyć narzędzi filtru Zmień ten zakres. Na przykład bezpłatne wyszukiwanie tekstu.

## <a name="see-also"></a>Zobacz też

[Raportowanie](how-to-create-risk-assessment-reports.md) 
 oceny ryzyka Zapytania wyszukiwania danych [czujników](how-to-create-data-mining-queries.md) 
 [Raportowanie wektorów ataków](how-to-create-attack-vector-reports.md)