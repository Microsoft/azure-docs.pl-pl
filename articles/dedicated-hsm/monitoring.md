---
title: Opcje monitorowania — dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Omówienie opcji monitorowania dedykowanego modułu HSM platformy Azure oraz obowiązków monitorowania
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 3fde577a6b0efb7584e1c9efd57c95583ebe4ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "70881422"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Monitorowanie dedykowanego modułu HSM platformy Azure

Dedykowana usługa HSM platformy Azure udostępnia urządzenie fizyczne wyłącznie klientom z pełną odpowiedzialnością za kontrolę administracyjną i zarządzaniem. Udostępnione urządzenie to [firmy Gemalto SafeNet Luna 7 model HSM A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  Firma Microsoft nie będzie mieć dostępu administracyjnego po zainicjowaniu obsługi administracyjnej przez klienta poza fizycznym załącznikiem portu szeregowego jako rolą monitorowania. W związku z tym klienci są zobowiązani do typowych działań operacyjnych, takich jak kompleksowe monitorowanie i Analiza dzienników.
Klienci są w pełni odpowiedzialni za aplikacje korzystające z sprzętowych modułów zabezpieczeń i powinni współpracować z firmy Gemalto w celu uzyskania pomocy technicznej lub konsultacji. Ze względu na stopień własności klienta higieny działania, firma Microsoft nie może oferować żadnych gwarancji wysokiej dostępności dla tej usługi. Klient jest odpowiedzialny za zapewnienie, że aplikacje są prawidłowo skonfigurowane do osiągnięcia wysokiej dostępności. Firma Microsoft będzie monitorować i obsługiwać kondycję urządzeń oraz łączność sieciową.

## <a name="microsoft-monitoring"></a>Monitorowanie firmy Microsoft

Urządzenie firmy Gemalto SafeNet w użyciu ma domyślny port SNMP i szeregowy jako opcje monitorowania urządzenia. Firma Microsoft użyła połączenia portu szeregowego jako fizycznego środka do nawiązywania połączenia z urządzeniem w celu pobrania podstawowych danych telemetrycznych dotyczących kondycji urządzeń. Obejmuje to elementy, takie jak temperatura i stan składnika, takie jak zasilacze i wentylatory.
Aby to osiągnąć, firma Microsoft stosuje rolę "Monitor" nieadministracyjne "na urządzeniu firmy Gemalto. Ta rola umożliwia pobranie danych telemetrycznych, ale nie zapewnia dostępu do urządzenia w warunkach administracyjnych lub w sposób umożliwiający wyświetlanie informacji kryptograficznych. Nasi klienci mogą mieć pewność, że urządzenie jest w pełni używane do zarządzania, administrowania i używania poufnego magazynu kluczy kryptograficznych. W przypadku gdy klient nie spełnia wymagań dotyczących minimalnego dostępu do podstawowego monitorowania kondycji, ma możliwość wyłączenia konta monitorowania. Oczywistą konsekwencją jest to, że firma Microsoft nie będzie mieć żadnych informacji i nie zapewnia żadnych aktywnych powiadomień o problemach z kondycją urządzenia. W takiej sytuacji klient jest odpowiedzialny za kondycję urządzenia.
Sama funkcja monitorowania jest skonfigurowana do sondowania urządzenia co 10 minut, aby uzyskać dane dotyczące kondycji. Ze względu na błąd dotyczący charakteru komunikacji szeregowej, tylko po kilku negatywnych wskaźnikach kondycji w ciągu jednego okresu zostanie zgłoszony alert. Ten alert ostatecznie prowadzi do aktywnej komunikacji klienta z powiadomieniem o tym problemie.
W zależności od natury problemu należy podjąć odpowiednie działania w celu ograniczenia wpływu i zapewnienia niskiego ryzyka. Na przykład awaria zasilacza jest procedurą wymiany gorącą bez wynikowego zdarzenia naruszenia, co może być wykonywane z niskim wpływem i minimalnym ryzykiem operacji. Inne procedury mogą wymagać wyzerowania urządzenia i anulowania jego aprowizacji w celu zminimalizowania ryzyka związanego z zabezpieczeniami klienta. W takiej sytuacji klient będzie mógł udostępnić alternatywne urządzenie, ponownie dołączyć parowanie o wysokiej dostępności w taki sposób, aby wyzwalać synchronizację urządzeń. Normalna operacja zostałaby wznowiona w minimalnym czasie z minimalnymi zakłóceniami i najniższymi zagrożeniami bezpieczeństwa.  

## <a name="customer-monitoring"></a>Monitorowanie klienta

Wartość pozycji dedykowanej usługi HSM to kontrolka pobierana przez klienta do urządzenia, szczególnie biorąc pod uwagę, że jest to urządzenie dostarczone w chmurze. Wynikiem tej kontroli jest obowiązek monitorowania kondycji urządzenia i zarządzania nim. Urządzenie firmy Gemalto SafeNet zawiera wskazówki dotyczące implementacji SNMP i dziennika systemowego. Klienci z dedykowanej usługi HSM są zalecani do korzystania z tej funkcji nawet wtedy, gdy konto monitora firmy Microsoft pozostanie aktywne i należy je uwzględnić w przypadku wyłączenia konta monitora firmy Microsoft.
Każda z dostępnych technik pozwala klientowi zidentyfikować problemy i skontaktować się z pomocą techniczną firmy Microsoft w celu zainicjowania odpowiedniej pracy naprawczej.

## <a name="next-steps"></a>Następne kroki

Zaleca się, aby wszystkie kluczowe pojęcia dotyczące usługi, takie jak wysoka dostępność i zabezpieczenia, były dobrze zrozumiałe przed zainicjowaniem obsługi urządzeń i projektem lub wdrożeniem aplikacji. Dalsze tematy dotyczące poziomu koncepcji:

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)
