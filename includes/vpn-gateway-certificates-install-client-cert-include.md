---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 393b29245141b2970e7c1a227d6e8b1b131c445c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061642"
---
Jeśli chcesz utworzyć połączenie punkt-lokacja z komputerem klienckim innym niż użyty do wygenerowania certyfikatów klienta, należy zainstalować certyfikat klienta. Podczas instalowania certyfikatu klienta potrzebne jest hasło, które zostało utworzone w trakcie eksportowania certyfikatu klienta.

1. Znajdź plik *pfx* i skopiuj go na komputer kliencki. Na komputerze klienckim kliknij dwukrotnie plik *pfx* , aby go zainstalować. Pozostaw **lokalizację magazynu** jako **bieżącego użytkownika** , a następnie wybierz pozycję **dalej** .
1. Na stronie importowania **Plik** nie wprowadzaj żadnych zmian. Wybierz opcję **Dalej** .
1. Na stronie **Ochrona klucza prywatnego** wprowadź hasło certyfikatu lub sprawdź, czy podmiot zabezpieczeń jest prawidłowy, a następnie wybierz przycisk **dalej** .
1. Na stronie **Magazyn certyfikatów** Pozostaw domyślną lokalizację, a następnie wybierz przycisk **dalej** .
1. Wybierz pozycję **Zakończ** . Na **Ostrzeżenie o zabezpieczeniach** instalacji certyfikatu wybierz pozycję **tak** . Możesz wygodnie wybrać opcję "tak" dla tego ostrzeżenia zabezpieczeń, ponieważ wygenerowałeś certyfikat.
1. Certyfikat został pomyślnie zaimportowany.
