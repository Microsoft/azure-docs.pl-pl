---
title: Zezwól twórcy laboratorium na wybranie lokalizacji w Azure Lab Services
description: W tym artykule opisano, jak administrator konta laboratorium może umożliwić twórcom laboratorium Wybieranie lokalizacji dla ich laboratoriów.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3b6c2d83414b1abc763755fbf15402b122c2186b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85444203"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Zezwól twórcy laboratorium na wybranie lokalizacji dla laboratorium w Azure Lab Services
W Azure Lab Services właściciel konta laboratorium może zezwolić twórcom laboratorium (wykładowcom) na wybranie lokalizacji dla tworzonych przez siebie laboratorium. Ta lokalizacja może różnić się od lokalizacji konta laboratorium. Lokalizacja jest grupą regionów świadczenia usługi Azure. Na przykład lokalizacja Stany Zjednoczone jest grupą regionów, takich jak Wschodnie stany USA, zachodnie stany USA itd. 

Jako właściciel konta laboratorium można wybrać opcję **Zezwalaj na twórcę laboratorium do wybierania lokalizacji laboratorium** podczas tworzenia konta laboratorium i po utworzeniu konta laboratorium (lub istniejącego konta laboratorium). 

## <a name="at-the-time-of-lab-account-creation"></a>W momencie tworzenia konta laboratorium
Podczas tworzenia konta laboratorium ta opcja jest wyświetlana na pierwszym ekranie (na karcie **podstawowe** ). 

![Włącz opcję podczas tworzenia laboratorium](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Ta opcja jest wyłączona, jeśli wybierzesz równorzędną sieć wirtualną dla konta laboratorium na karcie **Zaawansowane** .  

![Gdy równorzędna Sieć wirtualna jest włączona](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Po utworzeniu konta laboratorium
Po utworzeniu konta laboratorium można włączyć lub wyłączyć tę opcję, wykonując następujące czynności: 

1. Na stronie **konto laboratorium** wybierz pozycję **Ustawienia laboratorium** w menu po lewej stronie.
2. Wybierz opcję **Zezwalaj na twórcę laboratorium do wybrania lokalizacji laboratorium** , jeśli chcesz zezwolić na wybór lokalizacji laboratorium przez twórcę laboratorium. W przypadku wyłączenia tej funkcji laboratoria są tworzone automatycznie w tej samej lokalizacji, w której znajduje się konto laboratorium. 
    
    To pole jest wyłączone w przypadku wybrania sieci wirtualnej dla pola **równorzędnej sieci wirtualnej** . Jest to spowodowane tym, że laboratorium na koncie laboratorium musi znajdować się w tym samym regionie co konto laboratorium, aby uzyskać dostęp do zasobów w równorzędnej sieci wirtualnej. 
1. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Ustawienia laboratorium](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Żadna Sieć wirtualna i wybór lokalizacji nie są dozwolone
W tym scenariuszu nie włączono opcji **Zezwalaj na wybór lokalizacji laboratorium przez twórcę programu Lab** . 

![Brak lokalizacji laboratorium](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Następnie twórcy laboratorium (wykładowcy) nie widzą opcji wyboru lokalizacji dla laboratorium. Zostanie wyświetlona cena za godzinę dla każdej dostępnej opcji rozmiaru. Po utworzeniu laboratorium zostanie ono utworzone w regionie świadczenia usługi Azure, który znajduje się w tej samej lokalizacji co region platformy Azure, w którym znajduje się konto laboratorium. Na przykład jeśli konto laboratorium znajduje się w regionie **zachodnie stany USA**, laboratorium może zostać utworzone w **Południowo-środkowe stany USA** , ale nie zostanie utworzone w **Kanadzie wschodniej**. Nie gwarantujemy żadnych informacji na temat regionu, który wybieramy z tej lokalizacji. Jeśli rozmiar jest obecnie ograniczony, twórca laboratorium zobaczy pole wyboru, gdzie będzie widzieć rozmiary, które zwykle obsługuje, ale są obecnie niedostępne. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>W obszarze Sieć wirtualna i wybór lokalizacji nie są dozwolone
W tym scenariuszu opcja **Zezwól na wybór laboratorium do wybrania lokalizacji laboratorium** jest wyłączona, ponieważ wybrano równorzędną sieć wirtualną dla konta laboratorium. Następnie twórcy laboratorium zobaczą ten sam ekran, jak z poprzednią opcją. Ponieważ wszystkie maszyny wirtualne muszą znajdować się w tym samym regionie świadczenia usługi Azure co sieć wirtualna, laboratorium zostanie utworzone w tym samym regionie świadczenia usługi Azure, w którym znajduje się sieć wirtualna. Jeśli określony region jest ograniczony do rozmiaru, rozmiar będzie wyświetlany jako niedostępny. 

## <a name="location-selection-is-enabled"></a>Wybrana lokalizacja jest włączona
Po wybraniu opcji **Zezwól twórcy laboratorium na wybranie lokalizacji laboratorium**, twórcy laboratorium (wykładowcy) widzą opcję wybrania lokalizacji podczas tworzenia laboratorium. 

![Wybierz lokalizację laboratorium](./media/allow-lab-creator-pick-lab-location/location-selection.png)

Twórcy laboratorium widzą zakres cen dla wszystkich lokalizacji, które mają rozmiar, i można wybrać lokalizację. Laboratorium zostanie utworzone w dowolnym regionie świadczenia usługi Azure, który jest mapowany na tę lokalizację.

Jeśli lokalizacja jest ograniczona, domyślnie nie jest wyświetlana na liście. Rozwiń listę rozwijaną i wybierz pozycję **Pokaż niedostępne lokalizacje dla tego rozmiaru**. 

![Pokaż niedostępne lokalizacje](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Koszt
Wcześniej cena była oparta na wybranym przez Ciebie rozmiarze maszyny wirtualnej w laboratorium. Cena jest teraz oparta na kombinacji systemu operacyjnego, rozmiaru i lokalizacji. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Łączenie sieci laboratorium z równorzędną siecią wirtualną](how-to-connect-peer-virtual-network.md)
- [Dołączanie galerii obrazów udostępnionych do laboratorium](how-to-attach-detach-shared-image-gallery.md)
- [Dodawanie użytkownika jako właściciela laboratorium](how-to-add-user-lab-owner.md)
- [Wyświetlanie ustawień zapory dla laboratorium](how-to-configure-firewall-settings.md)
- [Skonfiguruj inne ustawienia laboratorium](how-to-configure-lab-accounts.md)