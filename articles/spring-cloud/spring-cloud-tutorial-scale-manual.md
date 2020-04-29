---
title: 'Samouczek: skalowanie aplikacji w chmurze Azure wiosennej | Microsoft Docs'
description: W tym samouczku dowiesz się, jak skalować aplikację przy użyciu chmury Azure wiosennej w Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: f08a3b5d0a03b0e898457bbb783dd5031c4b0f27
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76277480"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Skalowanie aplikacji w chmurze Azure wiosennej

W tym samouczku przedstawiono sposób skalowania dowolnej aplikacji mikrousług przy użyciu pulpitu nawigacyjnego chmury Azure wiosny w Azure Portal.

Skaluj swoją aplikację w górę i w dół, modyfikując jej liczbę wirtualnych procesorów CPU (procesorów wirtualnych vCPU) i ilość pamięci. Skaluj swoją aplikację w i na zewnątrz, modyfikując liczbę wystąpień aplikacji.

Po zakończeniu dowiesz się, jak wprowadzać szybkie zmiany ręcznie do każdej aplikacji w usłudze. Skalowanie obowiązuje w kilka sekund i nie wymaga żadnych zmian w kodzie ani ponownego wdrożenia.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) . 
* Wdrożone wystąpienie usługi w chmurze Azure wiosną.  Skorzystaj z naszego [przewodnika Szybki Start dotyczącego wdrażania aplikacji za pomocą interfejsu wiersza polecenia platformy Azure,](spring-cloud-quickstart-launch-app-cli.md) aby rozpocząć pracę.
* Co najmniej jedna aplikacja została już utworzona w wystąpieniu usługi.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Przejdź do strony skalowanie w Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do strony **omówienia** chmury Azure wiosennej.

1. Wybierz grupę zasobów, która zawiera tę usługę.

1. Wybierz kartę **aplikacje** w obszarze **Ustawienia** w menu po lewej stronie.

1. Wybierz aplikację, którą chcesz skalować. W tym przykładzie wybierz aplikację o nazwie **Account-Service**. Powinna zostać wyświetlona strona **Przegląd** aplikacji.

1. Przejdź do karty **Skala** w obszarze **Ustawienia** w menu po lewej stronie. Należy zapoznać się z opcjami skalowania atrybutów pokazanych w poniższej sekcji.

## <a name="scale-your-application"></a>Skalowanie aplikacji

Jeśli modyfikujesz atrybuty skalowania, pamiętaj o następujących uwagach:

* **Procesory**: Maksymalna liczba procesorów CPU na wystąpienie aplikacji to cztery. Łączna liczba procesorów dla aplikacji to ustawiona w tym miejscu wartość pomnożona przez liczbę wystąpień aplikacji.

* **Pamięć/GB**: Maksymalna ilość pamięci na wystąpienie aplikacji wynosi 8 GB. Całkowita ilość pamięci dla aplikacji to wartość ustawiona w tym miejscu pomnożona przez liczbę wystąpień aplikacji.

* **Liczba wystąpień aplikacji**: w warstwie Standardowa można skalować w poziomie do maksymalnie 20 wystąpień. Ta wartość zmienia liczbę oddzielnych uruchomionych wystąpień aplikacji mikrousług.

Upewnij się, że wybrano pozycję **Zapisz** , aby zastosować ustawienia skalowania.

![Usługa skalowania w Azure Portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Po kilku sekundach wprowadzone zmiany zostaną wyświetlone na stronie **Przegląd** zawierającej więcej szczegółów dostępnych na karcie **wystąpienia aplikacji** . skalowanie nie wymaga żadnych zmian w kodzie ani ponownego wdrożenia.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób ręcznego skalowania aplikacji w chmurze sieci platformy Azure. Aby dowiedzieć się, jak monitorować aplikację, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Dowiedz się, jak monitorować aplikację](spring-cloud-tutorial-distributed-tracing.md)
