---
title: Jak wdrożyć narzędzie do etykietowania przykładowego aparatu rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrożyć narzędzie do etykietowania przykładowego aparatu rozpoznawania formularzy, aby ułatwić nadzorowane uczenie się.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 7ddb4b2cd465b5e9542d777d33b9bd8cb952becd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531341"
---
# <a name="deploy-the-sample-labeling-tool"></a>Wdrażanie przykładowego narzędzia do oznaczania etykietami

Narzędzie do etykietowania przykładowego aparatu rozpoznawania formularzy to aplikacja, która zapewnia prosty interfejs użytkownika (UI), którego można używać do ręcznego etykietowania formularzy (dokumentów) w celu nadzorowanego uczenia się. W tym artykule udostępnimy linki i instrukcje, które nauczą Cię, jak:

* [Lokalne uruchamianie przykładowego narzędzia do etykietowania](#run-the-sample-labeling-tool-locally)
* [Wdrażanie przykładowego narzędzia do etykietowania w wystąpieniu kontenera platformy Azure (ACI)](#deploy-with-azure-container-instances-aci)
* [Używanie i współtworzenie narzędzia do etykietowania formularzy OCR typu open source](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Lokalne uruchamianie przykładowego narzędzia do etykietowania

Najszybszym sposobem rozpoczęcia etykietowania danych jest lokalne uruchomienie przykładowego narzędzia do etykietowania. Poniższy przewodnik Szybki start używa interfejsu API REST aparatu rozpoznawania formularzy i przykładowego narzędzia do etykietowania, aby wyszkolić model niestandardowy z danymi oznaczonymi ręcznie. 

* [Szybki start: etykietuj formularze, trenuj model i analizuj formularz za pomocą przykładowego narzędzia do etykietowania](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Wdrażanie przy za pomocą wystąpień kontenerów platformy Azure (ACI)

Zanim zaczniemy, należy pamiętać, że istnieją dwa sposoby wdrażania przykładowego narzędzia do etykietowania w wystąpieniu kontenera platformy Azure (ACI). Obie opcje są używane do uruchamiania przykładowego narzędzia do etykietowania za pomocą ACI: 

* [Korzystanie z witryny Azure Portal](#azure-portal)
* [Korzystanie z interfejsu wiersza polecenia platformy Azure](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

Wykonaj następujące kroki, aby utworzyć nowy zasób przy użyciu witryny Azure portal: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/signin/index/).
2. Wybierz pozycję **Utwórz zasób**. 
3. Następnie wybierz pozycję **Aplikacja sieci Web**. 

   > [!div class="mx-imgBorder"]
   > ![Wybierz aplikację internetową](./media/quickstarts/formre-create-web-app.png)
   
4. Najpierw upewnij się, że jest zaznaczona karta **Podstawy.** Teraz będziesz musiał podać kilka informacji: 

   > [!div class="mx-imgBorder"]
   > ![Wybierz podstawowe](./media/quickstarts/formre-select-basics.png)
   * Subskrypcja — wybierz istniejącą subskrypcję platformy Azure
   * Grupa zasobów — można ponownie użyć istniejącej grupy zasobów lub utworzyć nową dla tego projektu. Zaleca się utworzenie nowej grupy zasobów.
   * Nazwa — nadaj aplikacji internetowej nazwę. 
   * Publikuj — wybierz **kontener platformy Docker**
   * System operacyjny - Wybierz **Linuksa**
   * Region — wybierz region, który ma dla Ciebie sens.
   * Plan systemu Linux — wybierz warstwę cenową/plan usługi aplikacji. 

   > [!div class="mx-imgBorder"]
   > ![Konfigurowanie aplikacji sieci Web](./media/quickstarts/formre-select-docker-linux.png)

5. Następnie wybierz kartę **Docker.** 

   > [!div class="mx-imgBorder"]
   > ![Wybierz pozycję Docker](./media/quickstarts/formre-select-docker.png)

6. Teraz skonfigurujmy kontener platformy Docker. Wszystkie pola są wymagane, chyba że zaznaczono inaczej:

   * Opcje — **wybieranie pojedynczego kontenera**
   * Źródło zdj. **Private Registry** 
   * Adres URL serwera — ustaw na taki adres`https://mcr.microsoft.com`
   * Nazwa użytkownika (opcjonalnie) — tworzenie nazwy użytkownika. 
   * Hasło (opcjonalnie) — utwórz bezpieczne hasło, które zapamiętasz.
   * Obraz i znacznik — ustaw na to`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * Ciągłe wdrażanie — ustaw to **na Włączone,** jeśli chcesz otrzymywać automatyczne aktualizacje, gdy zespół programistów wprowadza zmiany w przykładowym narzędziu etykietowania.
   * Polecenie Uruchamianie — ustaw na to`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![Konfigurowanie platformy Docker](./media/quickstarts/formre-configure-docker.png)

7. Gotowe. Następnie wybierz **pozycję Przejrzyj + Utwórz**, a następnie **utwórz,** aby wdrożyć aplikację sieci Web. Po zakończeniu można uzyskać dostęp do aplikacji sieci web pod adresem URL podanym w **przeglądzie** zasobu.

> [!NOTE]
> Podczas tworzenia aplikacji sieci web można również skonfigurować autoryzację/uwierzytelnianie. Nie jest to konieczne, aby rozpocząć. 

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Jako alternatywę dla korzystania z witryny Azure portal, można utworzyć zasób przy użyciu interfejsu wiersza polecenia platformy Azure. Przed kontynuowaniem należy zainstalować narzędzie [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Możesz pominąć ten krok, jeśli już pracujesz z interfejsu wiersza polecenia platformy Azure. 

Jest kilka rzeczy, które musisz wiedzieć o tym poleceniu:

* `DNS_NAME_LABEL=aci-demo-$RANDOM`generuje losową nazwę DNS. 
* W tym przykładzie przyjęto założenie, że masz grupę zasobów, której można użyć do utworzenia zasobu. Zamień `<resource_group_name>` ją na prawidłową grupę zasobów skojarzoną z subskrypcją. 
* Musisz określić, gdzie chcesz utworzyć zasób. Zamień `<region name>` żądany region aplikacji internetowej. 
* To polecenie automatycznie akceptuje umowy EuLA.

W interfejsie wiersza polecenia platformy Azure uruchom to polecenie, aby utworzyć zasób aplikacji sieci web dla przykładowego narzędzia do etykietowania: 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resorunce_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>Łączenie się z usługą Azure AD w celu autoryzacji

Zaleca się połączenie aplikacji sieci web z usługą Azure Active Directory. Dzięki temu tylko użytkownicy z prawidłowymi poświadczeniami mogą logować się i korzystać z aplikacji sieci web. Postępuj zgodnie z instrukcjami w [konfigurowanie aplikacji usługi App Service,](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) aby połączyć się z usługą Azure Active Directory.

## <a name="open-source-on-github"></a>Open source w usłudze GitHub

Narzędzie do etykietowania formularzy OCR jest również dostępne jako projekt typu open source w usłudze GitHub. Narzędzie jest aplikacją internetową stworzoną przy użyciu React + Redux i jest napisane w TypeScript. Aby dowiedzieć się więcej lub przyczynić się, zobacz [Narzędzie do etykietowania formularzy OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Następne kroki

Skorzystaj z przewodnika Szybki start [pociągu z etykietami,](./quickstarts/label-tool.md) aby dowiedzieć się, jak używać tego narzędzia do ręcznego etykietowania danych szkoleniowych i wykonywania nadzorowanego uczenia się.
