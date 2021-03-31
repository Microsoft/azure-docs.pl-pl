---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67183835"
---
Jeśli w języku Python wystąpi błąd podczas uruchamiania aplikacji, zostanie zwrócona tylko prosta strona błędu (np. "nie można wyświetlić strony, ponieważ wystąpił wewnętrzny błąd serwera").

Aby przechwycić błędy aplikacji języka Python:

1. W Azure Portal w aplikacji sieci Web wybierz pozycję **Ustawienia**.
2. Na karcie **Ustawienia** wybierz pozycję **Ustawienia aplikacji**.
3. W obszarze **Ustawienia aplikacji** Wprowadź następującą parę klucz/wartość:
    * Klucz: WSGI_LOG
    * Wartość: D:\home\site\wwwroot\logs.txt (Wprowadź wybraną nazwę pliku)

W folderze wwwroot powinny teraz pojawić się błędy w pliku logs.txt.
