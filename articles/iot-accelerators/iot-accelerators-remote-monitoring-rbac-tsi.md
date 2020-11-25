---
title: Kontrola dostępu do danych przez monitorowanie zdalne — Azure | Microsoft Docs
description: Ten artykuł zawiera informacje na temat sposobu konfigurowania kontroli dostępu dla Eksploratora telemetrii Time Series Insights w akceleratorze rozwiązania do zdalnego monitorowania
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005966"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Konfigurowanie kontroli dostępu dla Time Series Insightsego Eksploratora telemetrii

Ten artykuł zawiera informacje dotyczące sposobu konfigurowania kontroli dostępu dla Eksploratora Time Series Insights w akceleratorze rozwiązania do zdalnego monitorowania. Aby umożliwić użytkownikom akceleratora rozwiązań dostęp do Eksploratora Time Series Insights, należy przyznać każdemu użytkownikowi dostęp do danych.

Zasady dostępu do danych umożliwiają przyznawanie uprawnień do wysyłania zapytań dotyczących danych, manipulowania danymi referencyjnymi w środowisku oraz udostępniania zapisanych zapytań i perspektyw skojarzonych ze środowiskiem.

## <a name="grant-data-access"></a>Przyznawanie dostępu do danych

Wykonaj następujące kroki, aby udzielić dostępu do danych dla podmiotu zabezpieczeń:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź środowisko Time Series Insights. Wpisz **serie czasowe** w polu **wyszukiwania** . Wybierz pozycję **środowisko szeregów czasowych** w wynikach wyszukiwania. 

3. Wybierz środowisko usługi Time Series Insights z listy.

4. Wybierz pozycję **zasady dostępu do danych**, a następnie wybierz pozycję **+ Dodaj**.
    ![Zarządzanie źródłem usługi Time Series Insights — środowisko](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Wybierz pozycję **Wybierz użytkownika**.  Wyszukaj nazwę użytkownika lub adres e-mail, aby zlokalizować użytkownika, który chcesz dodać. Kliknij przycisk **Wybierz** , aby potwierdzić wybór. 

    ![Zarządzanie źródłem usługi Time Series Insights — dodawanie](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Wybierz pozycję **Wybierz rolę**. Wybierz odpowiednią rolę dostępu dla użytkownika:
   - Wybierz opcję **współautor** , jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udostępnianie zapisanych zapytań i perspektyw innym użytkownikom środowiska. 
   - W przeciwnym razie wybierz opcję **czytelnik** , aby zezwolić użytkownikom na wykonywanie zapytań dotyczących danych w środowisku i zapisywanie osobistych (nieudostępnionych) zapytań w środowisku.

     Wybierz **przycisk OK** , aby potwierdzić wybór roli.

     ![Zarządzanie źródłem usługi Time Series Insights — wybieranie użytkownika](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Na stronie **Wybieranie roli użytkownika** wybierz **przycisk OK** .

    ![Zarządzanie źródłem usługi Time Series Insights — wybieranie roli](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. Na stronie **zasady dostępu do danych** są wyświetlane Użytkownicy i role dla każdego użytkownika.

    ![Zarządzanie źródłem usługi Time Series Insights — wyniki](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób udzielania kontroli dostępu dla Eksploratora Time Series Insights w akceleratorze rozwiązania do zdalnego monitorowania.

Aby uzyskać więcej informacji o pojęciach dotyczących akceleratora rozwiązania do monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md) .

Aby uzyskać więcej informacji na temat dostosowywania rozwiązania do monitorowania zdalnego, zobacz [Dostosowywanie i ponowne wdrażanie mikrousługi](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->