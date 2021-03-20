---
title: Użyj Azure Portal, aby wdrożyć aplikację katalogu usług
description: Pokazuje odbiorców aplikacji zarządzanych, jak wdrożyć aplikację katalogu usług za pomocą Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ce58fc69496f54c078b0a0a55a8a3c7cad82a051
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81391707"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Szybki Start: wdrażanie aplikacji katalogu usług za Azure Portal

W [poprzednim przewodniku szybki start](publish-service-catalog-app.md)opublikowano definicję aplikacji zarządzanej. W tym przewodniku szybki start utworzysz aplikację katalogu usług z tej definicji.

## <a name="create-service-catalog-app"></a>Tworzenie aplikacji katalogu usług

W Azure Portal wykonaj następujące czynności:

1. Wybierz pozycję **Utwórz zasób**.

   ![Tworzenie zasobu](./media/deploy-service-catalog-quickstart/create-new.png)

1. Wyszukaj **aplikację zarządzaną katalogu usług** i wybierz ją z dostępnych opcji.

   ![Wyszukaj aplikację katalogu usług](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Zobaczysz Opis usługi zarządzanej aplikacji. Wybierz przycisk **Utwórz**.

   ![Wybieranie pozycji Utwórz](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. W portalu zostaną wyświetlone definicje aplikacji zarządzanych, do których masz dostęp. Z dostępnych definicji Wybierz ten, który chcesz wdrożyć. W tym przewodniku szybki start Użyj definicji **zarządzanego konta magazynu** utworzonej w poprzednim przewodniku Szybki Start. Wybierz przycisk **Utwórz**.

   ![Wybierz definicję do wdrożenia](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Podaj wartości dla karty **podstawy** . Wybierz subskrypcję platformy Azure, w której chcesz wdrożyć aplikację katalogu usług. Utwórz nową grupę zasobów o nazwie **aplikacja**. Wybierz lokalizację dla swojej aplikacji. Po zakończeniu wybierz przycisk **OK**.

   ![Podaj wartości dla warstwy Podstawowa](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Podaj prefiks dla nazwy konta magazynu. Wybierz typ konta magazynu do utworzenia. Po zakończeniu wybierz przycisk **OK**.

   ![Podaj wartości dla magazynu](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Przejrzyj podsumowanie. Po pomyślnym zakończeniu walidacji wybierz pozycję **OK** , aby rozpocząć wdrażanie.

   ![Wyświetl podsumowanie](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Wyświetlanie wyników

Po wdrożeniu aplikacji katalogu usług będziesz mieć dwie nowe grupy zasobów. Jedna grupa zasobów zawiera aplikację katalogu usług. Inna grupa zasobów zawiera zasoby dla aplikacji katalogu usług.

1. Wyświetl grupę zasobów o nazwie **aplikacja** , aby wyświetlić aplikację katalogu usług.

   ![Wyświetlanie aplikacji](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Wyświetl grupę zasobów o nazwie **Application Group {hash-characters}** , aby wyświetlić zasoby dla aplikacji katalogu usług.

   ![Wyświetlanie zasobów](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak utworzyć pliki definicji dla aplikacji zarządzanej, zobacz [Tworzenie i publikowanie definicji aplikacji zarządzanej](publish-service-catalog-app.md).
* Aby zapoznać się z interfejsem wiersza polecenia platformy Azure, zobacz [Deploying Service Catalog app with Azure CLI](./scripts/managed-application-cli-sample-create-application.md).
* Aby uzyskać obsługę programu PowerShell, zobacz [wdrażanie aplikacji wykazu usług przy użyciu programu PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).
