---
title: Tworzenie rozwiązań do ochrony zdrowia za pomocą usługi Azure IoT Central | Microsoft Docs
description: Dowiedz się, jak tworzyć rozwiązanie do opieki zdrowotnej przy użyciu szablonów aplikacji IoT Central platformy Azure.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: c46ce28801c74d0dcc37d23f9d384a69f022ecc4
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820351"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Tworzenie rozwiązań dla opieki zdrowotnej za pomocą usługi Azure IoT Central

Dowiedz się, jak tworzyć rozwiązania do ochrony zdrowia za pomocą usługi Azure IoT Central przy użyciu szablonów aplikacji.

## <a name="what-is-continuous-patient-monitoring-template"></a>Co to jest szablon monitorowania ciągłego pacjenta?

W obszarze IoT opieki zdrowotnej ciągłe monitorowanie pacjenta to jeden z najważniejszych elementów umożliwiających zmniejszenie ryzyka związanego z readmisjami, bardziej wydajne zarządzanie chorobami przewlekłymi oraz poprawa wyników pacjenta. Monitorowanie ciągłego pacjenta można podzielić na dwie główne kategorie:

1. **Monitorowanie w pacjentach**: korzystanie z noszenia medycznego i innych urządzeń w szpitalu, zespoły opiekijące mogą monitorować kluczowe objawy i warunki medyczne bez konieczności wysyłania pielęgniarek/pielęgniarzy w celu wyszukania pacjenta przez wiele razy dziennie. Zespoły zajmujące się opieką mogą zrozumieć moment, w którym pacjent wymaga krytycznej uwagi za pomocą powiadomień, i ustalają priorytety czasu efektywnie.
1. **Zdalne monitorowanie pacjentów**: dzięki noszenia lekarskim i raportom dotyczącym pacjenta (specjalistów) do monitorowania pacjentów poza szpitalem można obniżyć ryzyko readmisji. Można zbierać dane z pacjentów przed przewlekłą chorobą i pacjentów do odbudowy, aby upewnić się, że pacjente są zgodne z planami opieki, a także że alerty dotyczące pogorszenia pacjenta mogą być nadane zespołom opieki, zanim staną się krytyczne.

Ten szablon aplikacji może służyć do tworzenia rozwiązań dla obu kategorii ciągłego monitorowania pacjenta. Korzyści:

* Bezproblemowo łącz różne rodzaje noszenia lekarskich z wystąpieniem IoT Central.
* Monitoruj urządzenia i zarządzaj nimi, aby upewnić się, że pozostaną w dobrej kondycji.
* Utwórz niestandardowe reguły dotyczące danych urządzenia, aby wyzwolić odpowiednie alerty.
* Wyeksportuj dane o kondycji pacjenta do interfejsu API platformy Azure dla FHIR, zgodnego magazynu danych.
* Wyeksportuj zagregowane informacje do istniejących lub nowych aplikacji branżowych.

>[!div class="mx-imgBorder"] 
>![CPM — pulpit nawigacyjny](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć tworzenie rozwiązania ciągłego monitorowania pacjenta:

* [Wdróż szablon aplikacji](tutorial-continuous-patient-monitoring.md)
* [Zobacz przykładową architekturę](concept-continuous-patient-monitoring-architecture.md)