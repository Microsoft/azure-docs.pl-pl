---
title: Szybki start — wdrażanie kontenera platformy Docker w wystąpieniu kontenera — portal
description: W tym przewodniku Szybki start użyjemy interfejsu Azure Portal szybko wdrożyć konteneryzowana aplikację internetową, która działa w izolowanym wystąpieniu kontenera platformy Azure
ms.date: 08/24/2020
ms.topic: quickstart
ms.custom:
- mvc
- devx-track-js
- mode-portal
ms.openlocfilehash: c0189bbd04e454205b34a6415ab6109f95b6f51a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536224"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Szybki start: wdrażanie wystąpienia kontenera na platformie Azure przy użyciu Azure Portal

Używaj Azure Container Instances do uruchamiania bez serwera kontenerów platformy Docker na platformie Azure z prostotą i szybkością. Wdrażanie aplikacji w wystąpieniu kontenera na żądanie, gdy nie potrzebujesz pełnej platformy orkiestracji kontenerów, na przykład Azure Kubernetes Service.

W tym przewodniku Szybki start użyjesz usługi Azure Portal do wdrożenia izolowanego kontenera platformy Docker i udostępnisz jego aplikację za pomocą w pełni kwalifikowanej nazwy domeny (FQDN). Po skonfigurowaniu kilku ustawień i wdrożeniu kontenera możesz przejść do uruchomionej aplikacji:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][azure-free-account].

## <a name="create-a-container-instance"></a>Tworzenie wystąpienia kontenera

Wybierz pozycję **Utwórz zasób**  >  **Kontenery**  >  **Container Instances**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Rozpoczynanie tworzenia nowego wystąpienia kontenera w witrynie Azure Portal":::

Na stronie **Podstawowe wprowadź** następujące wartości w polach tekstowych Grupa **zasobów,** **Nazwa kontenera** i **Obraz** kontenera. Pozostaw domyślne wartości w pozostałych polach i kliknij przycisk **OK**.

* Grupa zasobów: **utwórz nową** > `myresourcegroup`
* Nazwa kontenera: `mycontainer`
* Źródło obrazu: **obrazy szybkiego startu**
* Obraz kontenera: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Konfigurowanie podstawowych ustawień nowego wystąpienia kontenera w witrynie Azure Portal":::

W tym przewodniku Szybki start użyjemy ustawień domyślnych do wdrożenia publicznego obrazu firmy `aci-helloworld` Microsoft. Ten przykładowy obraz systemu Linux zawiera małą aplikację internetową napisaną w Node.js, która obsługuje statyczną stronę HTML. Możesz również korzystać z własnych obrazów kontenerów przechowywanych w Azure Container Registry, Docker Hub lub innych rejestrach.

Na stronie **Sieć** określ etykietę **nazwy DNS** dla kontenera. Nazwa musi być unikatowa w obrębie regionu świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. Twój kontener będzie dostępny publicznie pod adresem `<dns-name-label>.<region>.azurecontainer.io`. Jeśli zostanie wyświetlony komunikat o błędzie „Etykieta nazwy DNS nie jest dostępna”, spróbuj użyć innej etykiety nazwy DNS.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Konfigurowanie ustawień sieciowych dla nowego wystąpienia kontenera w Azure Portal":::

Pozostaw wartości domyślne pozostałych ustawień, a następnie wybierz **pozycję Przejrzyj i utwórz.**

Po zakończeniu weryfikacji zostanie wyświetlone podsumowanie ustawień kontenera. Wybierz **pozycję Utwórz,** aby przesłać żądanie wdrożenia kontenera.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Podsumowanie ustawień nowego wystąpienia kontenera w witrynie Azure Portal":::

Po zakończeniu wdrażania zostanie wyświetlone powiadomienie informujące o tym, że wdrożenie jest w toku. Kolejne powiadomienie zostanie wyświetlone, gdy grupa kontenerów zostanie wdrożona.

Otwórz omówienie grupy kontenerów, przechodząc do tematu **Grupy** zasobów  >  **myresourcegroup**  >  **mycontainer.** Zwróć uwagę na wartość **FQDN** (w pełni kwalifikowana nazwa domeny) wystąpienia kontenera, a także na jego **Stan**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Przegląd grupy kontenerów w witrynie Azure Portal":::

Po osiągnięciu przez **Stan** wartości *Uruchomiono* przejdź w przeglądarce do nazwy FQDN kontenera.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

Gratulacje! Konfigurując zaledwie kilku ustawień, udało Ci się wdrożyć publicznie dostępną aplikację w usłudze Azure Container Instances.

## <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

W przypadku rozwiązywania problemów z kontenerem lub uruchomioną w nim aplikacją przydatne jest przeglądanie dzienników wystąpienia kontenera.

Aby wyświetlić dzienniki kontenera, w obszarze Ustawienia **wybierz** pozycję **Kontenery**, a następnie pozycję **Dzienniki.** Powinno pojawić się żądanie HTTP GET generowane podczas wyświetlania aplikacji w przeglądarce.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Dzienniki kontenera w witrynie Azure Portal":::


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z kontenerem wybierz opcję **Przegląd** dla wystąpienia kontenera *mycontainer*, a następnie wybierz polecenie **Usuń**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="Usuwanie wystąpienia kontenera w Azure Portal]":::

Po pojawieniu się okna dialogowego potwierdzenia wybierz pozycję **Tak**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Potwierdzenie usunięcia wystąpienia kontenera w Azure Portal]":::

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono wystąpienie kontenera platformy Azure na podstawie publicznego obrazu firmy Microsoft. Jeśli chcesz skompilować obraz kontenera i wdrożyć go za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/
