---
title: 'Samouczek: skalowanie aplikacji w chmurze Azure wiosennej | Microsoft Docs'
description: Dowiedz się, jak skalować aplikację przy użyciu chmury Azure wiosny w Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.custom: devx-track-java
ms.openlocfilehash: c3c660c5e301081ee82a12275b6e9125f0fc781b
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289821"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Samouczek: skalowanie aplikacji w chmurze Azure wiosennej

W tym samouczku przedstawiono sposób skalowania dowolnej aplikacji mikrousług przy użyciu pulpitu nawigacyjnego chmury Azure wiosny w Azure Portal.

Skaluj swoją aplikację w górę i w dół, modyfikując jej liczbę wirtualnych procesorów CPU (procesorów wirtualnych vCPU) i ilość pamięci. Skaluj swoją aplikację w i na zewnątrz, modyfikując liczbę wystąpień aplikacji.

Po zakończeniu dowiesz się, jak wprowadzać szybkie zmiany ręcznie do każdej aplikacji w usłudze. Skalowanie obowiązuje w kilka sekund i nie wymaga żadnych zmian w kodzie ani ponownego wdrożenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te procedury, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Wdrożone wystąpienie usługi w chmurze Azure wiosną.  Aby rozpocząć pracę, postępuj zgodnie z [przewodnikiem Szybki Start dotyczącym wdrażania aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](spring-cloud-quickstart.md) .
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

Po kilku sekundach wprowadzone zmiany zostaną wyświetlone na stronie **Przegląd** zawierającej więcej szczegółów dostępnych na karcie **wystąpienia aplikacji** . Skalowanie nie wymaga żadnych zmian w kodzie ani ponownego wdrożenia.

## <a name="upgrade-to-the-standard-tier"></a>Zmiana warstwy na wyższą warstwę Standardowa
Jeśli korzystasz z warstwy Podstawowa i ograniczono jeden lub więcej z tych [limitów](spring-cloud-quotas.md), możesz przeprowadzić uaktualnienie do warstwy Standardowa. W tym celu przejdź do menu Warstwa cenowa, wybierając najpierw kolumnę warstwy Standardowa i klikając przycisk **Uaktualnij** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób ręcznego skalowania aplikacji w chmurze Azure wiosennej. Aby dowiedzieć się, jak monitorować aplikację przez skonfigurowanie alertów, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Dowiedz się, jak skonfigurować alerty](spring-cloud-tutorial-alerts-action-groups.md)
