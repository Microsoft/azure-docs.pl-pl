---
title: Ustawienia zapory dla usługi Azure Lab Services
description: Dowiedz się, jak określić publiczny adres IP i zakres numerów portów maszyn wirtualnych w laboratorium, aby można było dodać informacje do reguł zapory.
author: emaher
ms.author: enewman
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: 067a2c9672b87974557f650cef07b0394e7d5a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85445852"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Ustawienia zapory dla usługi Azure Lab Services

Każda organizacja lub szkoła skonfiguruje własną sieć w taki sposób, który najlepiej pasuje do swoich potrzeb.  Czasami obejmuje ustawienie reguł zapory, które blokują połączenia Remote Desktop Protocol (RDP) lub Secure Shell (SSH) z maszynami spoza sieci.  Ponieważ Azure Lab Services działa w chmurze publicznej, niektóre dodatkowe konfiguracje mogą być konieczne, aby umożliwić uczniom dostęp do maszyny wirtualnej podczas nawiązywania połączenia z sieci kampusowej.

Każde laboratorium używa jednego publicznego adresu IP i wielu portów.  Na wszystkich maszynach wirtualnych, na maszynach wirtualnych z szablonami i dla uczniów, będzie używany ten publiczny adres IP.  Publiczny adres IP nie zmieni się w okresie istnienia laboratorium.  Jednak każda maszyna wirtualna będzie mieć inny numer portu.  Numery portów mieszczą się w zakresie od 49152 do 65535.  Kombinacja publicznego adresu IP i numeru portu służy do łączenia instruktorów i studentów z poprawną maszyną wirtualną.  W tym artykule opisano sposób znajdowania określonego publicznego adresu IP używanego przez laboratorium.  Te informacje mogą służyć do aktualizowania reguł zapory dla ruchu przychodzącego i wychodzącego, aby studenci mogli uzyskiwać dostęp do swoich maszyn wirtualnych.

>[!IMPORTANT]
>Każde laboratorium będzie miało inny publiczny adres IP.

## <a name="find-public-ip-for-a-lab"></a>Znajdź publiczny adres IP dla laboratorium

Publiczne adresy IP dla każdego laboratorium są wyświetlane na stronie **Wszystkie laboratoria** na koncie laboratorium usługi Lab Services.  Aby uzyskać instrukcje dotyczące sposobu znajdowania strony **Wszystkie laboratoria** , zobacz [przeglądanie laboratoriów na koncie laboratorium](manage-labs.md#view-labs-in-a-lab-account).  

> [!div class="mx-imgBorder"]
> ![Strona Wszystkie laboratoria](./media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Publiczny adres IP nie zostanie wyświetlony, jeśli komputer szablonu dla laboratorium nie zostanie jeszcze opublikowany.

## <a name="conclusion"></a>Podsumowanie

Teraz wiemy publiczny adres IP dla laboratorium.  Reguły ruchu przychodzącego i wychodzącego można utworzyć dla zapory organizacji dla publicznego adresu IP i zakresu portów 49152-65535.  Po zaktualizowaniu reguł uczniowie mogą uzyskać dostęp do swoich maszyn wirtualnych bez dostępu blokowania zapory sieciowej.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Zezwalanie twórcy laboratorium na wybieranie lokalizacji laboratorium](allow-lab-creator-pick-lab-location.md)
- [Łączenie sieci laboratorium z równorzędną siecią wirtualną](how-to-connect-peer-virtual-network.md)
- [Dołączanie galerii obrazów udostępnionych do laboratorium](how-to-attach-detach-shared-image-gallery.md)
- [Dodawanie użytkownika jako właściciela laboratorium](how-to-add-user-lab-owner.md)
- [Wyświetlanie ustawień zapory dla laboratorium](how-to-configure-firewall-settings.md)
- [Skonfiguruj inne ustawienia laboratorium](how-to-configure-lab-accounts.md)
