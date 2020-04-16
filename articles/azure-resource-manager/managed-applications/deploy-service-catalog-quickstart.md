---
title: Wdrażanie aplikacji katalogu usług za pomocą witryny Azure Portal
description: Pokazuje konsumentom aplikacji zarządzanych, jak wdrożyć aplikację wykazu usług za pośrednictwem witryny Azure portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ce58fc69496f54c078b0a0a55a8a3c7cad82a051
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391707"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Szybki start: wdrażanie aplikacji katalogu usług za pośrednictwem witryny Azure Portal

W [poprzednim przewodniku Szybki start](publish-service-catalog-app.md)opublikowano definicję aplikacji zarządzanej. W tym przewodniku Szybki start utworzysz aplikację katalogu usług z tej definicji.

## <a name="create-service-catalog-app"></a>Tworzenie aplikacji katalogu usług

W witrynie Azure portal należy wykonać następujące czynności:

1. Wybierz pozycję **Utwórz zasób**.

   ![Tworzenie zasobu](./media/deploy-service-catalog-quickstart/create-new.png)

1. Wyszukaj **aplikację zarządzaną wykazu usług** i wybierz ją z dostępnych opcji.

   ![Wyszukiwanie aplikacji katalogu usług](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Zostanie wyświetlony opis usługi aplikacji zarządzanej. Wybierz pozycję **Utwórz**.

   ![Wybieranie pozycji Utwórz](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. Portal zawiera definicje zarządzanych aplikacji, do których masz dostęp. Z dostępnych definicji wybierz tę, którą chcesz wdrożyć. W tym przewodniku Szybki start użyj definicji **zarządzanego konta magazynu** utworzonego w poprzednim przewodniku Szybki start. Wybierz pozycję **Utwórz**.

   ![Wybierz definicję do wdrożenia](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Podaj wartości dla karty **Podstawy.** Wybierz subskrypcję platformy Azure, aby wdrożyć aplikację katalogu usług. Utwórz nową grupę zasobów o nazwie **applicationGroup**. Wybierz lokalizację aplikacji. Po zakończeniu wybierz przycisk **OK**.

   ![Podaj wartości dla podstawowych](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Podaj prefiks nazwy konta magazynu. Wybierz typ konta magazynu do utworzenia. Po zakończeniu wybierz przycisk **OK**.

   ![Podaj wartości pamięci masowej](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Przejrzyj podsumowanie. Po pomyślnym zakończeniu sprawdzania poprawności wybierz przycisk **OK,** aby rozpocząć wdrażanie.

   ![Wyświetl podsumowanie](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Wyświetlanie wyników

Po wdrożeniu aplikacji wykazu usług masz dwie nowe grupy zasobów. Jedna grupa zasobów przechowuje aplikację katalogu usług. Druga grupa zasobów przechowuje zasoby dla aplikacji katalogu usług.

1. Wyświetl grupę zasobów o nazwie **applicationGroup,** aby wyświetlić aplikację katalogu usług.

   ![Wyświetlanie aplikacji](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Wyświetl grupę zasobów o nazwie **applicationGroup{hash-characters},** aby wyświetlić zasoby aplikacji katalogu usług.

   ![Wyświetlanie zasobów](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak utworzyć pliki definicji dla aplikacji zarządzanej, zobacz [Tworzenie i publikowanie definicji aplikacji zarządzanej](publish-service-catalog-app.md).
* W przypadku interfejsu wiersza polecenia platformy Azure zobacz [Wdrażanie aplikacji wykazu usług za pomocą interfejsu wiersza polecenia platformy Azure](./scripts/managed-application-cli-sample-create-application.md).
* W przypadku programu PowerShell zobacz [Wdrażanie aplikacji katalogu usług za pomocą programu PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).
