---
title: Szybki Start — Tworzenie serwera Analysis Services w Azure Portal | Microsoft Docs
description: W tym przewodniku szybki start opisano sposób tworzenia wystąpienia serwera Azure Analysis Services przy użyciu Azure Portal.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 488e7c84e481edd1b136fe90ab948d3cfb51ed2c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89230784"
---
# <a name="quickstart-create-a-server---portal"></a>Szybki start: tworzenie serwera — portal

W tym przewodniku Szybki start opisano, jak utworzyć zasób serwera usług Analysis Services w ramach subskrypcji platformy Azure, korzystając z portalu.

## <a name="prerequisites"></a>Wymagania wstępne 

* **Subskrypcja platformy Azure**: Przejdź do [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) , aby utworzyć konto.
* **Azure Active Directory**: subskrypcja musi być skojarzona z dzierżawą usługi Azure Active Directory. Ponadto musisz zalogować się na platformie Azure przy użyciu konta należącego do tej dzierżawy usługi Azure Active Directory. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal 

[Zaloguj się do portalu](https://portal.azure.com)


## <a name="create-a-server"></a>Tworzenie serwera

1. Kliknij pozycję **+ Utwórz**  >  **Analytics**  >  **Analysis Services**analizy zasobów.

    ![Portal](./media/analysis-services-create-server/aas-create-server-portal.png)

2. W sekcji **Analysis Services** wypełnij wymagane pola, a następnie naciśnij przycisk **Utwórz**.
   
   * **Nazwa serwera**: wpisz unikatową nazwę, która będzie używana do odwoływania się do serwera. Nazwa serwera musi rozpoczynać się od małej litery i zawierać od 3 do 128 małych liter i cyfr. Odstępy i znaki specjalne są niedozwolone.
   * **Subskrypcja**: wybierz subskrypcję, z którą będzie skojarzony ten serwer.
   * **Grupa zasobów**: utwórz nową grupę zasobów lub wybierz istniejącą. Grupy zasobów ułatwiają zarządzanie kolekcją zasobów platformy Azure. Aby dowiedzieć się więcej, zobacz [grupy zasobów](../azure-resource-manager/management/overview.md).
   * **Lokalizacja**: lokalizacja centrum danych Azure, w którym będzie hostowany ten serwer. Wybierz lokalizację najbliższą względem Twojej największej bazy użytkowników.
   * **Warstwa cenowa**: wybierz warstwę cenową. Jeśli testujesz tę funkcję i planujesz zainstalować przykładową modelową bazę danych, wybierz bezpłatną warstwę **D1**. Aby dowiedzieć się więcej, zobacz [cennik usług Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/). 
   * **Administrator**: domyślnie jest to aktualnie zalogowane konto. Możesz wybrać inne konto w usłudze Azure Active Directory.
   * **Ustawienie Magazyn kopii zapasowych**: opcjonalne. Jeśli masz już [konto magazynu](../storage/common/storage-introduction.md), możesz je wskazać jako domyślny magazyn kopii zapasowych modelowej bazy danych. Możesz również określić ustawienia [tworzenia i przywracania kopii zapasowych](analysis-services-backup.md) później.
   * **Czas wygaśnięcia klucza magazynu**: opcjonalne. Określ czas wygaśnięcia klucza magazynu.

Tworzenie serwera zwykle trwa mniej niż minutę. W przypadku wybrania opcji **Dodaj do portalu** przejdź do portalu, aby zobaczyć nowy serwer. Lub przejdź do **wszystkich usług**  >  **Analysis Services** , aby sprawdzić, czy serwer jest gotowy. Serwery obsługują modele tabelaryczne na poziomach zgodności 1200 i wyższych. Poziom zgodności modelu jest określony w programie Visual Studio lub SSMS.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy serwer nie będzie już potrzebny, usuń go. Na stronie **Przegląd** serwera kliknij pozycję **Usuń**. 

 ![Czyszczenie](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki Start przedstawiono sposób tworzenia serwera w ramach subskrypcji platformy Azure. Teraz, po utworzeniu serwera, możesz go zabezpieczyć, konfigurując (opcjonalną) zaporę serwera. Możesz też dodać do serwera podstawowy przykładowy model danych bezpośrednio w portalu. Przykładowy model służy do zdobywania wiedzy na temat konfigurowania ról modelowej bazy danych oraz testowania połączeń klienckich. Aby dowiedzieć się więcej, przejdź do samouczka dotyczącego dodawania przykładowego modelu.

> [!div class="nextstepaction"]
> [Szybki start: konfigurowanie zapory serwera — Portal](analysis-services-qs-firewall.md)   