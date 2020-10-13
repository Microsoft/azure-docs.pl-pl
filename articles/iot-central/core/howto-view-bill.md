---
title: Zarządzaj rachunkiem i Konwertuj z bezpłatnego planu cenowego w aplikacji Azure IoT Central | Microsoft Docs
description: Skontaktuj się z administratorem, aby dowiedzieć się, jak zarządzać rozliczeniami i przechodzić z planu cen bezpłatnych do standardowego planu cenowego w aplikacji IoT Central platformy Azure
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 515f5f665e947ff9594cce6dbbaea9b5e0c50ebf
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999702"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Zarządzanie rozliczeniami w aplikacji IoT Central

W tym artykule opisano, jak administrator może zarządzać rozliczeniami usługi Azure IoT Central. Możesz przenieść swoją aplikację z bezpłatnego planu cenowego do standardowego planu cenowego, a także uaktualnić lub obniżyć plan cenowy.

Aby uzyskać dostęp do sekcji **Administracja** , musisz mieć rolę *administratora* lub mieć *niestandardową rolę użytkownika* , która umożliwia wyświetlanie rozliczeń. Jeśli utworzysz aplikację IoT Central platformy Azure, zostanie ona automatycznie przypisana do roli **administratora** .

## <a name="move-from-free-to-standard-pricing-plan"></a>Przejdź od bezpłatnej do standardowego planu cen

- Aplikacje korzystające z bezpłatnego planu cenowego są bezpłatne przez siedem dni przed ich wygaśnięciem. Aby uniknąć utraty danych, można je przenieść do standardowego planu cenowego w dowolnym momencie przed wygaśnięciem.
- W przypadku aplikacji korzystających ze standardowego planu cenowego są naliczone opłaty za każde urządzenie, a pierwsze dwa urządzenia są bezpłatne dla każdej aplikacji.

Więcej informacji o cenach znajdziesz na [stronie cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

W sekcji Cennik możesz przenieść aplikację z bezpłatnej do standardowego planu cenowego.

Aby ukończyć ten proces samoobsługowy, wykonaj następujące kroki:

1. Przejdź do strony **cennika** w sekcji **Administracja** .

    ![Stan wersji próbnej](media/howto-view-bill/freetrialbilling.png)

1. Wybierz pozycję **Konwertuj na płatny plan**.

    ![Konwertuj wersję próbną](media/howto-view-bill/convert.png)

1. Wybierz odpowiednie Azure Active Directory, a następnie subskrypcję platformy Azure, która ma być używana dla aplikacji korzystającej z płatnego planu.

1. Po wybraniu opcji **Konwertuj**Twoja aplikacja korzysta z planu płatnego, a opłaty są naliczane.

> [!Note]
> Domyślnie jest konwertowany na plan cenowy w *warstwie Standardowa 2* .

## <a name="how-to-change-your-application-pricing-plan"></a>Jak zmienić plan cenowy aplikacji

W przypadku aplikacji korzystających ze standardowego planu cenowego są naliczone opłaty za każde urządzenie, a pierwsze dwa urządzenia są bezpłatne dla każdej aplikacji.

W sekcji Cennik można w dowolnym momencie uaktualnić lub obniżyć plan cen usługi Azure IoT.

1. Przejdź do strony **cennika** w sekcji **Administracja** .

    ![Uaktualnij plan prcing](media/howto-view-bill/pricing.png)

1. Wybierz **Plan** , a następnie wybierz pozycję **Zapisz** w celu uaktualnienia lub obniżenia poziomu.

## <a name="view-your-bill"></a>Wyświetlanie rachunku

1. Wybierz odpowiednie Azure Active Directory, a następnie subskrypcję platformy Azure, która ma być używana dla aplikacji korzystającej z płatnego planu.

1. Po wybraniu opcji **Konwertuj**Twoja aplikacja korzysta z planu płatnego, a opłaty są naliczane.

> [!Note]
> Domyślnie jest konwertowany na plan cenowy w *warstwie Standardowa 2* .

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać rozliczeniami w aplikacji IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z tematem [Dostosowywanie interfejsu użytkownika aplikacji](howto-customize-ui.md) na platformie Azure IoT Central.