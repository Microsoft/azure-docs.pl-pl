---
title: Rozwiązywanie problemów z zaleceniami dotyczącymi rezerwacji na platformie Azure
description: Ten artykuł ułatwia zrozumienie i rozwiązywanie problemów z zaleceniami dotyczącymi rezerwacji platformy Azure, które są wyświetlane w witrynie Azure Portal.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: a3137d779908bf2791ca396068a8c9edf5d56739
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492252"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>Rozwiązywanie problemów z zaleceniami dotyczącymi rezerwacji na platformie Azure

Ten artykuł ułatwia zrozumienie i rozwiązywanie problemów z zaleceniami dotyczącymi rezerwacji platformy Azure, które są wyświetlane w witrynie Azure Portal. Może się zdarzyć, że nie są wyświetlane żadne zalecenia lub wyświetlane zalecenia nie spełniają oczekiwań. Na przykład możesz już mieć wystąpienie zarezerwowane dla określonej konfiguracji maszyny wirtualnej. Być może spodziewasz się zobaczyć zalecenie dotyczące podobnej konfiguracji maszyny wirtualnej.

## <a name="symptoms"></a>Objawy

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i przejdź do obszaru **Rezerwacje** .
2. Wybierz pozycję **+ Dodaj** , a następnie wybierz produkt.
3. Na karcie **Zalecane** mogą nie być wyświetlane żadne zalecenia lub wyświetlane zalecenia nie spełniają oczekiwań.

## <a name="cause"></a>Przyczyna

Lista zalecanych produktów jest generowana na podstawie ilości danych użycia dla Twojego zakresu (Współdzielona lub Pojedyncza) w porównaniu do kosztów rezerwacji produktu. Jeśli aparat zaleceń wykrywa oszczędności, zaleca produkt do zakupu. Jednak jeśli aparat nie identyfikuje żadnych oszczędności, nie zaleca żadnego produktu.

Gdy uruchamiasz zasób z daną konfiguracją, nie ma gwarancji, że zaoszczędzisz pieniądze, kupując rezerwację dla tej konfiguracji. Na przykład dlatego, że Twoje użycie jest sporadyczne. W takim przypadku łączny koszt rezerwacji może nie przynieść oszczędności w okresie istnienia rezerwacji.

Ważne jest również, aby zrozumieć, jaki wpływ na zalecenia ma wybór zakresu. Gdy zakres jest ustawiony na **Współdzielona** , zalecenia na liście zawierają wystąpienia zarezerwowane, w przypadku których platforma Azure znajduje oszczędności dla całej rejestracji skojarzonej z subskrypcją rozliczeniową. Jeśli zakres jest ustawiony na **Pojedyncza** , zalecenia na liście dotyczą tylko zasobów uruchamianych w ramach subskrypcji. Istnieje możliwość, że niektóre rozmiary maszyn wirtualnych będą zalecane dla jednego zakresu, ale nie dla innego. Na przykład zagregowane użycie w całej Twojej rejestracji może być na poziomie **Standard_B1ls** , co jest wystarczająco wysoką wartością, aby uzasadnić zakup rezerwacji w zakresie rejestracji. Jednak pojedyncza subskrypcja w ramach rejestracji może nie mieć wystarczającego użycia, które uzasadniałoby koszt zakupu rezerwacji w zakresie. Zmiana zakresu między **Współdzielona** i **Pojedyncza** może powodować generowanie różnych zaleceń.

Platforma Azure może zalecić zakup rezerwacji w pewnych terminach, a w innych nie, na podstawie zidentyfikowanych oszczędności kosztów. Na przykład rabaty w przypadków terminów trzyletnich są większe niż w przypadku terminów rocznych. Istnieje większe prawdopodobieństwo, że platforma Azure znajdzie oszczędności w terminie trzyletnim niż w terminie rocznym.

Jeśli chcesz zrozumieć, dlaczego platforma Azure zaleca określony rozmiar i ilość zasobów, wybierz pozycję **&lt;Ilość&gt; Zobacz szczegóły** , aby uzyskać szczegółową wizualizację przedstawiającą potencjalne oszczędności w czasie.

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="Przykład pokazujący link Zobacz szczegóły zalecenia rezerwacji" lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>Rozwiązanie

Możesz kupić dowolną ilość rezerwacji dla wybranego terminu. Zakup rezerwacji, której ilość i termin różnią się od zalecenia, prawdopodobnie spowoduje nieoptymalne oszczędności i użycie.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat zaleceń dotyczących rezerwacji, zobacz [Zalecenia dotyczące zakupu rezerwacji](determine-reservation-purchase.md).
