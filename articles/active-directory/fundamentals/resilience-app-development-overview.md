---
title: Zwiększanie odporności aplikacji uwierzytelniania i autoryzacji, które tworzysz
titleSuffix: Microsoft identity platform
description: Omówienie naszych wskazówek dotyczących odporności na tworzenie aplikacji przy użyciu Azure Active Directory i platformy tożsamości firmy Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: d06e851390537bf94b59e656f84bf58fe7216410
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96317356"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>Zwiększanie odporności aplikacji uwierzytelniania i autoryzacji, które tworzysz

Tożsamość firmy Microsoft korzysta z nowoczesnego, opartego na tokenach uwierzytelniania i autoryzacji. Oznacza to, że aplikacja uzyskuje tokeny od dostawcy tożsamości w celu uwierzytelnienia użytkownika i autoryzacji aplikacji do wywoływania chronionych interfejsów API.

Token jest ważny przez określony czas, zanim aplikacja będzie musiała uzyskać nową. Sporadycznie wywołanie pobrania tokenu może zakończyć się niepowodzeniem z powodu problemu, takiego jak awaria sieci lub infrastruktury lub awaria usługi uwierzytelniania. W tym dokumencie opisano kroki wykonywane przez dewelopera w celu zwiększenia odporności w aplikacjach w przypadku wystąpienia błędu pozyskiwania tokenu.

Te artykuły zawierają wskazówki dotyczące zwiększenia odporności w aplikacjach przy użyciu platformy tożsamości firmy Microsoft i Azure Active Directory. Istnieją wskazówki dotyczące obu aplikacji klienckich, które działają w imieniu zalogowanego użytkownika, a także aplikacje demona działające w ich własnym imieniu. Zawierają one najlepsze rozwiązania związane z używaniem tokenów, a także do wywoływania zasobów.

- [Tworzenie odporności na aplikacje, które logują użytkowników](resilience-client-app.md)
- [Tworzenie odporności na aplikacje bez użytkowników](resilience-daemon-app.md)
- [Tworzenie odporności w infrastrukturze zarządzania tożsamościami i dostępem](resilience-in-infrastructure.md)
- [Odporność na kompilacje w systemach CIAM](resilience-b2c.md)
