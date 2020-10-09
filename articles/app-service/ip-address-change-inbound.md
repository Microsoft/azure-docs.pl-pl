---
title: Przygotowywanie do zmiany przychodzącego adresu IP
description: Jeśli adres IP ruchu przychodzącego zostanie zmieniony, Dowiedz się, co należy zrobić, aby aplikacja nadal działała po zmianie.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74672415"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Jak przygotować się do zmiany przychodzącego adresu IP

Jeśli otrzymasz powiadomienie o zmianie przychodzącego adresu IP aplikacji Azure App Service, postępuj zgodnie z instrukcjami w tym artykule.

## <a name="determine-if-you-have-to-do-anything"></a>Określanie, czy trzeba wykonać jakieś czynności

* Opcja 1: Jeśli aplikacja App Service nie ma domeny niestandardowej, nie jest wymagana żadna akcja.

* Opcja 2: Jeśli w Azure DNS portalu rejestracji domeny jest skonfigurowany tylko rekord CNAME (rekord DNS wskazujący na identyfikator URI), nie jest wymagana żadna akcja.

* Opcja 3: Jeśli rekord A (rekord DNS wskazujący bezpośrednio na adres IP) został skonfigurowany w portalu rejestracji domeny (dostawca DNS innej firmy lub Azure DNS), Zastąp istniejący adres IP nowym. Nowy adres IP można znaleźć, postępując zgodnie z instrukcjami w następnej sekcji.

* Opcja 4: Jeśli aplikacja znajduje się za modułem równoważenia obciążenia, filtrem IP lub innym mechanizmem IP wymagającym adresu IP Twojej aplikacji, Zastąp istniejący adres IP nowym. Nowy adres IP można znaleźć, postępując zgodnie z instrukcjami w następnej sekcji.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Znajdź nowy adres IP dla ruchu przychodzącego w Azure Portal

Nowy przychodzący adres IP, który jest podawany dla aplikacji, znajduje się w portalu w polu **wirtualnego adresu IP** . Zarówno ten nowy adres IP, jak i stary z nich są połączone z Twoją aplikacją, a później stara zostanie odłączona.

1.  Otwórz witrynę [Azure Portal](https://portal.azure.com).

2.  W menu nawigacji po lewej stronie wybierz pozycję **App Services**.

3.  Wybierz z listy aplikację App Service.

1.  Jeśli aplikacja jest aplikacją funkcji, zobacz [adres IP ruchu przychodzącego aplikacji funkcji](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  W obszarze nagłówka **Ustawienia** kliknij pozycję **Właściwości** na lewym pasku nawigacyjnym i Znajdź sekcję zatytułowaną **wirtualny adres IP**.

5. Skopiuj adres IP i ponownie skonfiguruj swój rekord domeny lub mechanizm IP.

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak przygotować się do zmiany adresu IP, która została zainicjowana przez platformę Azure. Aby uzyskać więcej informacji na temat adresów IP w Azure App Service, zobacz [przychodzące i wychodzące adresy IP w Azure App Service](overview-inbound-outbound-ips.md).
