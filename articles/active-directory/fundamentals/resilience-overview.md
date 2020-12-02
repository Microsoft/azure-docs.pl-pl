---
title: Tworzenie odpornej na zarządzanie tożsamościami i dostępem za pomocą Azure Active Directory
description: Przewodnik dla architektów, administratorów IT i deweloperów na potrzeby tworzenia odporności na zakłócenia w systemach tożsamości.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39021fecb79f6b8e7536daad592378ed998760a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454343"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>Kompilowanie odporności do zarządzania tożsamościami i dostępem przy użyciu Azure Active Directory

Zarządzanie tożsamościami i dostępem (IAM) to struktura procesów, zasad i technologii, które ułatwiają zarządzanie tożsamościami i dostęp do nich. Obejmuje to wiele składników obsługujących uwierzytelnianie i autoryzację użytkowników i innych kont w systemie.

Funkcja odporności IAM to zdolność do trwałego zakłócenia składników systemowych i odzyskiwania z minimalnym wpływem na firmę, użytkowników, klientów i operacje. Zmniejszenie zależności, złożoności i pojedynczych punktów awarii, zapewniając kompleksową obsługę błędów, zwiększy odporność.

Zakłócenia mogą pochodzić z dowolnego składnika systemów IAM. Aby skompilować odporny system IAM, założono zakłócenia i zaplanuje się to. 

Podczas planowania odporności rozwiązania IAM należy wziąć pod uwagę następujące elementy: 

* Aplikacje korzystające z systemu IAM.

* Infrastruktura publiczna korzysta z połączeń uwierzytelniania, w tym firm telekomunikacyjnych, usługodawców internetowych i dostawców kluczy publicznych.

* Dostawcy tożsamości w chmurze i lokalnych.

* Inne usługi, które opierają się na usłudze IAM, oraz interfejsy API, które je łączą.

* Wszystkie inne składniki lokalne w systemie.

Niezależnie od źródła, rozpoznawanie i planowanie zdarzeń awaryjnych jest ważne. Jednak dodanie dodatkowych systemów tożsamości oraz ich wynikowe zależności i złożoność może zmniejszyć odporność, a nie zwiększyć.

Aby zwiększyć odporność w systemie, zapoznaj się z następującymi artykułami:

* [Tworzenie odporności w infrastrukturze IAM](resilience-in-infrastructure.md)

* [Tworzenie odporności IAM w aplikacjach](resilience-app-development-overview.md)

* [Tworzenie odporności w systemach zarządzania tożsamościami i dostępem klientów (CIAM)](resilience-b2c.md)
