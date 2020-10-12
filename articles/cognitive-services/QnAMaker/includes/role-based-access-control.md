---
title: plik dołączany
description: plik dołączany
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83665950"
---
Do współpracy są udostępniane następujące role:

|Rola|Funkcje|Dostęp do interfejsu API|Uprawnienia aplikacji|
|--|--|--|--|
|Właściciel|Wszystkie|Klucz uwierzytelniania|Wszystkie|
|Współautor|Wszystkie z wyjątkiem możliwości dodawania nowych członków do ról|Klucz uwierzytelniania|Wszystkie z wyjątkiem możliwości dodawania nowych członków do ról|
|QnA Maker odczytać<br>Przeczytaj|Eksport/pobieranie<br>Testowanie|Token okaziciela|1. Pobierz interfejs API bazy wiedzy<br>2. Utwórz listę artykułów bazy wiedzy dla interfejsu API użytkownika<br>3. Uzyskaj szczegóły bazy wiedzy<br>4. Pobieranie zmian<br>Generuj odpowiedź |
|Edytor QnA Maker<br>(odczyt/zapis)|Eksport/pobieranie<br>Testowanie<br>Aktualizacja KB<br>Eksportuj KB<br>Importuj KB<br>Zastąp KB<br>Tworzenie bazy wiedzy|Token okaziciela|1. Tworzenie interfejsu API bazy wiedzy<br>2. Zaktualizuj interfejs API bazy wiedzy<br>3. Zastąp interfejs API bazy wiedzy<br>4. Zastąp zmiany<br>5. "Uczenie interfejsu API" [w nowym modelu usług w wersji 5]|
|Użytkownik usługi poznawczej<br>(odczyt/zapis/publikowanie)|Wszystkie|Token okaziciela|Wszystkie|