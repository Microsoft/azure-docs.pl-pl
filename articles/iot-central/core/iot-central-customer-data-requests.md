---
title: Funkcje żądania danych klienta na platformie Azure IoT Central | Microsoft Docs
description: W tym artykule opisano identyfikowanie, usuwanie i eksportowanie danych klienta w aplikacji IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: dabcadea96f4ced5bdf73a35ef533e6d290595c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87001880"
---
# <a name="azure-iot-central-customer-data-request-features"></a>Funkcje żądania danych klienta IoT Central platformy Azure

Azure IoT Central to w pełni Internet rzeczy zarządzane rozwiązanie typu "oprogramowanie jako usługa", które ułatwia łączenie i monitorowanie zasobów IoT oraz zarządzanie nimi na dużą skalę, tworzenie szczegółowych informacji na podstawie danych IoT oraz podejmowanie świadomych działań.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identyfikowanie danych klienta

Object-IDs Azure Active Directory są używane do identyfikowania użytkowników i przypisywania ról. W portalu usługi Azure IoT Central są wyświetlane adresy e-mail użytkowników dla przypisań ról, ale tylko identyfikator obiektu Azure Active Directory jest przechowywany, adres e-mail jest dynamicznie wysyłany z Azure Active Directory. Administratorzy usługi Azure IoT Central mogą wyświetlać, eksportować i usuwać użytkowników aplikacji w sekcji Administracja użytkownikami aplikacji IoT Central platformy Azure.

W aplikacji można skonfigurować adresy e-mail w celu otrzymywania alertów. W takim przypadku adresy e-mail są przechowywane w IoT Central i muszą być zarządzane na stronie administratora konta w aplikacji.

W odniesieniu do urządzeń firma Microsoft nie utrzymuje informacji i nie ma dostępu do danych, które umożliwiają korelację urządzenia z użytkownikiem. Wiele urządzeń zarządzanych w usłudze Azure IoT Central nie są urządzeniami osobistymi, na przykład na komputerze lub w ramach ekspresu do sprzedaży. Klienci mogą jednak rozważyć, że niektóre urządzenia mają być identyfikowalne i według ich uznania mogą obsługiwać własne systemy śledzenia zasobów lub spisu, które wiążą urządzenia z osobami. Usługa Azure IoT Central zarządza i przechowuje wszystkie dane skojarzone z urządzeniami, tak jakby były danymi osobistymi.

W przypadku korzystania z usług Microsoft Enterprise Services firma Microsoft generuje pewne informacje, znane jako dzienniki generowane przez system. Te dzienniki składają się na faktyczne działania wykonywane w ramach usługi i danych diagnostycznych związanych z poszczególnymi urządzeniami i nie są związane z aktywnością użytkownika. Dzienniki generowane przez system IoT Central systemu Azure nie są dostępne ani eksportowane przez administratorów aplikacji.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Możliwość usuwania danych użytkownika jest dostępna tylko za pomocą strony Administracja IoT Central. Administratorzy aplikacji mogą wybrać użytkownika, który ma zostać usunięty, a następnie wybrać pozycję **Usuń** w prawym górnym rogu aplikacji, aby usunąć rekord. Administratorzy aplikacji mogą również usunąć pojedyncze konta, które nie są już skojarzone z daną aplikacją.

Po usunięciu użytkownika żadne dalsze alerty nie będą wysyłane do nich pocztą e-mail. Jednak ich adresy e-mail muszą zostać usunięte osobno z każdego skonfigurowanego alertu.

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Możliwość eksportowania danych jest dostępna tylko za pomocą strony Administracja IoT Central. Dane klienta, w tym przypisane role, mogą być wybierane, kopiowane i wklejane przez administratora aplikacji.

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Aby uzyskać więcej informacji na temat administrowania kontem, w tym definicji ról, zobacz [jak administrować aplikacją](howto-administer.md).
