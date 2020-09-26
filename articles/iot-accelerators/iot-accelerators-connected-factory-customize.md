---
title: Dostosowywanie rozwiązania połączonej fabryki — Azure | Microsoft Docs
description: Opis sposobu dostosowywania zachowania akceleratora połączonej fabryki.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: a092859517ee6ac41e62e55fe95f72b8bfdf87d5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91290726"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Dostosuj sposób wyświetlania danych z serwerów OPC UA przez rozwiązanie połączonej fabryki

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

Rozwiązanie połączonej fabryki agreguje i wyświetla dane z serwerów OPC UA podłączonych do rozwiązania. Polecenia można przeglądać i wysyłać do serwerów OPC UA w rozwiązaniu. Aby uzyskać więcej informacji na temat OPC UA, zobacz [często zadawane pytania dotyczące połączonej fabryki](iot-accelerators-faq-cf.md).

Przykłady zagregowanych danych w rozwiązaniu obejmują ogólną wydajność sprzętu (OEE) i kluczowe wskaźniki wydajności (KPI), które można wyświetlić na pulpicie nawigacyjnym na poziomach fabryki, linii i stacji. Poniższy zrzut ekranu przedstawia wartości OEE i KPI stacji **montażowej** w **wierszu produkcji 1** **w fabryce:**

![Przykład wartości OEE i kluczowego wskaźnika wydajności w rozwiązaniu][img-oee-kpi]

Rozwiązanie pozwala wyświetlić szczegółowe informacje z określonych elementów danych z serwerów OPC UA, nazywanych *stacjami*. Poniższy zrzut ekranu przedstawia liczbę wyprodukowanych elementów z określonej stacji:

![Wykresy liczby wyprodukowanych elementów][img-manufactured-items]

Kliknięcie jednego z wykresów umożliwia dalsze Eksplorowanie danych przy użyciu Time Series Insights (TSI):

![Eksplorowanie danych przy użyciu Time Series Insights][img-tsi]

W tym artykule opisano:

- Jak dane są udostępniane różnym widokom w rozwiązaniu.
- Jak można dostosować sposób, w jaki rozwiązanie wyświetla dane.

## <a name="data-sources"></a>Źródła danych

Rozwiązanie połączonej fabryki wyświetla dane z serwerów OPC UA podłączonych do rozwiązania. Instalacja domyślna obejmuje kilka serwerów OPC UA z uruchomioną symulacją fabryki. Możesz dodać własne serwery OPC UA, które [nawiązują połączenie za pomocą bramy] [lnk-Connect-CF] do rozwiązania.

Można przeglądać elementy danych, które połączony serwer OPC UA może wysłać do rozwiązania na pulpicie nawigacyjnym:

1. Wybierz pozycję **przeglądarka** , aby przejść do widoku **Wybierz serwer OPC UA** :

    ![Przejdź do widoku wybierz serwer OPC UA][img-select-server]

1. Wybierz serwer, a następnie kliknij przycisk **Połącz**. Po **Proceed** wyświetleniu ostrzeżenia dotyczącego zabezpieczeń kliknij pozycję Wybierz.

    > [!NOTE]
    > Ostrzeżenie to pojawia się tylko raz dla każdego serwera i ustanawia relację zaufania między pulpitem nawigacyjnym rozwiązania a serwerem.

1. Teraz można przeglądać elementy danych, które serwer może wysłać do rozwiązania. Elementy, które są wysyłane do rozwiązania, mają znacznik wyboru:

    ![Opublikowane elementy][img-published]

1. Jeśli jesteś *administratorem* w rozwiązaniu, możesz opublikować element danych, aby udostępnić go w rozwiązaniu połączonej fabryki. Jako administrator możesz również zmienić wartość elementów danych i wywołać metody z serwera OPC UA.

## <a name="map-the-data"></a>Mapowanie danych

Rozwiązanie połączonej fabryki mapuje i agreguje elementy danych publikowanych z serwera OPC UA do różnych widoków w rozwiązaniu. Rozwiązanie połączonej fabryki jest wdrażane na koncie platformy Azure podczas aprowizacji rozwiązania. Plik JSON w rozwiązaniu połączonej fabryki programu Visual Studio przechowuje te informacje mapowania. Ten plik konfiguracji JSON można wyświetlić i zmodyfikować w rozwiązaniu połączonej fabryki Visual Studio. Po wprowadzeniu zmiany możesz ponownie wdrożyć rozwiązanie.

Możesz użyć pliku konfiguracji, aby:

- Edytuj istniejące symulowane fabryki, linie produkcyjne i stacje.
- Mapuj dane z prawdziwych serwerów OPC UA, które nawiązują połączenie z rozwiązaniem.

Aby uzyskać więcej informacji na temat mapowania i agregowania danych w celu spełnienia określonych wymagań, zobacz [jak skonfigurować Akcelerator rozwiązania połączonej fabryki ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Wdróż zmiany

Po zakończeniu wprowadzania zmian w **ContosoTopologyDescription.js** pliku należy ponownie wdrożyć rozwiązanie połączonej fabryki na koncie platformy Azure.

Repozytorium **Azure-IoT-Connected-Factory** obejmuje skrypt **build.ps1** PowerShell, którego można użyć do odbudowania i wdrożenia rozwiązania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o akceleratorze rozwiązania połączonej fabryki, odczytując następujące artykuły:

* [Uprawnienia w witrynie azureiotsolutions.com][lnk-permissions]
* [Połączona fabryka — często zadawane pytania](iot-accelerators-faq-cf.md)
* [Często zadawane pytania][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md