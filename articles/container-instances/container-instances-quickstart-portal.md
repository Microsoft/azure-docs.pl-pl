---
title: Szybki Start — wdrażanie kontenera platformy Docker do wystąpienia kontenera — Portal
description: W tym przewodniku szybki start użyjesz Azure Portal, aby szybko wdrożyć aplikację sieci Web, która działa w izolowanym wystąpieniu kontenera platformy Azure
ms.topic: quickstart
ms.date: 08/24/2020
ms.custom: seodec18, mvc, devx-track-js
ms.openlocfilehash: c8477bd91c3a02a2cd02d341c38c16da251902ae
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91324539"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Szybki Start: Wdrażanie wystąpienia kontenera na platformie Azure przy użyciu Azure Portal

Używanie Azure Container Instances do uruchamiania kontenerów platformy Docker bez serwera na platformie Azure z prostotą i szybkością. Wdróż aplikację w wystąpieniu kontenera na żądanie, gdy nie potrzebujesz pełnej platformy aranżacji kontenerów, takiej jak usługa Azure Kubernetes.

W tym przewodniku szybki start użyjesz Azure Portal do wdrożenia izolowanego kontenera platformy Docker i udostępnienia jego aplikacji za pomocą w pełni kwalifikowanej nazwy domeny (FQDN). Po skonfigurowaniu kilku ustawień i wdrożeniu kontenera możesz przejść do uruchomionej aplikacji:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][azure-free-account].

## <a name="create-a-container-instance"></a>Tworzenie wystąpienia kontenera

Wybierz Container Instances **tworzenia**  >  **kontenerów**zasobów  >  **Container Instances**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

Na stronie **podstawowe** wprowadź następujące wartości w polach tekstowych **Grupa zasobów**, **nazwa kontenera**i **obraz kontenera** . Pozostaw domyślne wartości w pozostałych polach i kliknij przycisk **OK**.

* Grupa zasobów: **Utwórz nową** > `myresourcegroup`
* Nazwa kontenera: `mycontainer`
* Źródło obrazu: **obrazy szybkiego startu**
* Obraz kontenera: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

W tym przewodniku szybki start użyjesz domyślnych ustawień do wdrożenia publicznego `aci-helloworld` obrazu firmy Microsoft. W tym przykładowym obrazie systemu Linux jest to mała aplikacja internetowa zapisywana w Node.js, która obsługuje statyczną stronę HTML. Możesz również umieścić własne obrazy kontenerów przechowywane w Azure Container Registry, Docker Hub lub innych rejestrach.

Na stronie **Sieć** Określ **etykietę nazwy DNS** dla kontenera. Nazwa musi być unikatowa w regionie świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. Twój kontener będzie dostępny publicznie pod adresem `<dns-name-label>.<region>.azurecontainer.io`. Jeśli zostanie wyświetlony komunikat o błędzie „Etykieta nazwy DNS nie jest dostępna”, spróbuj użyć innej etykiety nazwy DNS.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

Pozostaw wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**.

Po zakończeniu weryfikacji zostanie wyświetlone podsumowanie ustawień kontenera. Wybierz pozycję **Utwórz** , aby przesłać żądanie wdrożenia kontenera.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

Po rozpoczęciu wdrażania zostanie wyświetlone powiadomienie informujące o tym, że wdrożenie jest w toku. Kolejne powiadomienie zostanie wyświetlone, gdy grupa kontenerów zostanie wdrożona.

Otwórz przegląd dla grupy kontenerów, przechodząc do sekcji **grupy zasobów**moje  >  **zasoby**  >  **mycontainer**. Zwróć uwagę na wartość **FQDN** (w pełni kwalifikowana nazwa domeny) wystąpienia kontenera, a także na jego **Stan**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

Po osiągnięciu przez **Stan** wartości *Uruchomiono* przejdź w przeglądarce do nazwy FQDN kontenera.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

Gratulacje! Konfigurując zaledwie kilku ustawień, udało Ci się wdrożyć publicznie dostępną aplikację w usłudze Azure Container Instances.

## <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

W przypadku rozwiązywania problemów z kontenerem lub uruchomioną w nim aplikacją przydatne jest przeglądanie dzienników wystąpienia kontenera.

Aby wyświetlić dzienniki kontenera, w obszarze **Ustawienia**wybierz opcję **kontenery**, a następnie pozycję **dzienniki**. Powinno pojawić się żądanie HTTP GET generowane podczas wyświetlania aplikacji w przeglądarce.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z kontenerem wybierz opcję **Przegląd** dla wystąpienia kontenera *mycontainer*, a następnie wybierz polecenie **Usuń**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

Po pojawieniu się okna dialogowego potwierdzenia wybierz pozycję **Tak**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono wystąpienie kontenera platformy Azure na podstawie publicznego obrazu firmy Microsoft. Jeśli chcesz skompilować obraz kontenera i wdrożyć go za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/