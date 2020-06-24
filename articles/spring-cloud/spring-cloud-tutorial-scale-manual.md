---
title: 'Samouczek: skalowanie aplikacji w chmurze Azure wiosennej | Microsoft Docs'
description: W tym samouczku dowiesz się, jak skalować aplikację przy użyciu chmury Azure wiosennej w Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: 93cefd0c71e2d51187e68c6f5f02777d158e95a4
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84792068"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Skalowanie aplikacji w chmurze Azure wiosennej

W tym samouczku przedstawiono sposób skalowania dowolnej aplikacji mikrousług przy użyciu pulpitu nawigacyjnego chmury Azure wiosny w Azure Portal.

Skaluj swoją aplikację w górę i w dół, modyfikując jej liczbę wirtualnych procesorów CPU (procesorów wirtualnych vCPU) i ilość pamięci. Skaluj swoją aplikację w i na zewnątrz, modyfikując liczbę wystąpień aplikacji.

Po zakończeniu dowiesz się, jak wprowadzać szybkie zmiany ręcznie do każdej aplikacji w usłudze. Skalowanie obowiązuje w kilka sekund i nie wymaga żadnych zmian w kodzie ani ponownego wdrożenia.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
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

## <a name="upgrade-to-the-standard-tier"></a>Zmiana warstwy na wyższą warstwę Standardowa
Jeśli korzystasz z warstwy Podstawowa i ograniczono jeden lub więcej z tych [limitów](spring-cloud-quotas.md), możesz przeprowadzić uaktualnienie do warstwy Standardowa. W tym celu przejdź do menu Warstwa cenowa, wybierając najpierw kolumnę warstwy Standardowa i klikając przycisk **Uaktualnij** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób ręcznego skalowania aplikacji w chmurze sieci platformy Azure. Aby dowiedzieć się, jak monitorować aplikację, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Dowiedz się, jak monitorować aplikację](spring-cloud-tutorial-distributed-tracing.md)
