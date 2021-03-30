---
title: Przygotowywanie do zmiany wychodzącego adresu IP
description: Jeśli wychodzący adres IP zostanie zmieniony, Dowiedz się, co należy zrobić, aby aplikacja nadal działała po zmianie.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74671663"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Jak przygotować się do zmiany wychodzącego adresu IP

Jeśli otrzymasz powiadomienie o zmianie wychodzących adresów IP aplikacji Azure App Service, postępuj zgodnie z instrukcjami w tym artykule.

## <a name="determine-if-you-have-to-do-anything"></a>Określanie, czy trzeba wykonać jakieś czynności

* Opcja 1: Jeśli aplikacja App Service nie używa filtrowania adresów IP, jawnej listy dołączania ani specjalnej obsługi ruchu wychodzącego, takiego jak Routing lub zapora, nie jest wymagana żadna akcja.

* Opcja 2: Jeśli aplikacja ma specjalną obsługę dla wychodzących adresów IP (Zobacz przykłady poniżej), Dodaj nowe wychodzące adresy IP wszędzie tam, gdzie pojawią się istniejące. Nie zamieniaj istniejących adresów IP. Nowe wychodzące adresy IP można znaleźć, postępując zgodnie z instrukcjami w następnej sekcji.

  Na przykład wychodzący adres IP może być jawnie zawarty w zaporze poza aplikacją lub zewnętrzna usługa płatnicza może mieć listę dozwolonych adresów IP wychodzących dla aplikacji. Jeśli Twój adres wychodzący jest skonfigurowany na liście w dowolnym miejscu poza aplikacją, należy zmienić.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Znajdź wychodzące adresy IP w Azure Portal

Nowe wychodzące adresy IP zostaną wyświetlone w portalu, zanim zaczną obowiązywać. Gdy platforma Azure zacznie korzystać z nowych, stare nie będą już używane. Używany jest tylko jeden zestaw w czasie, więc wpisy na listach dołączania muszą mieć zarówno stare, jak i nowe adresy IP, aby zapobiec awariom, gdy nastąpi przełączenie. 

1.  Otwórz witrynę [Azure Portal](https://portal.azure.com).

2.  W menu nawigacji po lewej stronie wybierz pozycję **App Services**.

3.  Wybierz z listy aplikację App Service.

1.  Jeśli aplikacja jest aplikacją funkcji, zobacz [wychodzące adresy IP aplikacji funkcji](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  W obszarze nagłówka **Ustawienia** kliknij pozycję **Właściwości** na lewym pasku nawigacyjnym i Znajdź sekcję zatytułowaną **wychodzące adresy IP**.

5. Skopiuj adresy IP i Dodaj je do specjalnej obsługi ruchu wychodzącego, takiego jak filtr lub lista dozwolonych. Nie usuwaj istniejących adresów IP z listy.

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak przygotować się do zmiany adresu IP, która została zainicjowana przez platformę Azure. Aby uzyskać więcej informacji na temat adresów IP w Azure App Service, zobacz [przychodzące i wychodzące adresy IP w Azure App Service](overview-inbound-outbound-ips.md).
