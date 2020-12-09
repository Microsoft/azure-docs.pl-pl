---
title: Jak wdrożyć przykładowe narzędzie do etykietowania z aparatem rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Poznaj różne sposoby wdrażania przykładowego narzędzia do etykietowania aparatu rozpoznawania formularzy, które ułatwia uczenie nadzorowane.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: abc8cffa3d5b9dffb55beabc90cdaecb3adc647f
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852527"
---
# <a name="deploy-the-sample-labeling-tool"></a>Wdrażanie przykładowego narzędzia do oznaczania etykietami

Przykładowe narzędzie do etykietowania z aparatem rozpoznawania formularzy to aplikacja, która udostępnia prosty interfejs użytkownika, którego można użyć do ręcznego etykietowania formularzy (dokumentów) na potrzeby nadzoru nadzorowanego. W tym artykule przedstawiono linki i instrukcje, które pouczymy się, jak:

* [Uruchom lokalnie przykładowe narzędzie do etykietowania](#run-the-sample-labeling-tool-locally)
* [Wdrażanie przykładowego narzędzia do etykietowania w usłudze Azure Container Instance (ACI)](#deploy-with-azure-container-instances-aci)
* [Korzystanie z narzędzia do etykietowania formularza OCR "open source" i Współtworzenie](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Uruchom lokalnie przykładowe narzędzie do etykietowania

Najszybszą metodą uruchamiania etykietowania danych jest uruchomienie lokalnego narzędzia do etykietowania. Poniższy przewodnik Szybki Start używa interfejsu API REST aparatu rozpoznawania formularzy i przykładowego narzędzia do etykietowania do uczenia modelu niestandardowego z ręcznie oznaczonymi danymi. 

* [Szybki Start: formularze etykiet, uczenie modelu i analizowanie formularza przy użyciu przykładowego narzędzia do etykietowania](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Wdrażanie za pomocą Azure Container Instances (ACI)

Przed rozpoczęciem należy zauważyć, że istnieją dwa sposoby wdrożenia przykładowego narzędzia do tworzenia etykiet do wystąpienia kontenera platformy Azure (ACI). Obie opcje służą do uruchamiania przykładowego narzędzia do etykietowania z ACI: 

* [Korzystanie z Azure Portal](#azure-portal)
* [Przy użyciu interfejsu wiersza polecenia platformy Azure](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

Wykonaj następujące kroki, aby utworzyć nowy zasób przy użyciu Azure Portal: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/signin/index/).
2. Wybierz pozycję **Utwórz zasób**. 
3. Następnie wybierz pozycję **aplikacja sieci Web**. 

   > [!div class="mx-imgBorder"]
   > ![Wybierz aplikację sieci Web](./media/quickstarts/formre-create-web-app.png)
   
4. Najpierw upewnij się, że wybrana jest karta **podstawy** . Teraz musisz podać pewne informacje: 

   > [!div class="mx-imgBorder"]
   > ![Wybierz podstawowe elementy](./media/quickstarts/formre-select-basics.png)
   * Subskrypcja — wybierz istniejącą subskrypcję platformy Azure
   * Grupa zasobów — możesz ponownie użyć istniejącej grupy zasobów lub utworzyć nową dla tego projektu. Zaleca się utworzenie nowej grupy zasobów.
   * Nazwa — Nadaj aplikacji sieci Web nazwę. 
   * Publikowanie — Wybieranie **kontenera platformy Docker**
   * System operacyjny — wybór systemu **Linux**
   * Region — Wybierz region, który jest dla Ciebie zrozumiały.
   * Plan systemu Linux — wybierz warstwę cenową/plan dla usługi App Service. 

   > [!div class="mx-imgBorder"]
   > ![Konfigurowanie aplikacji sieci Web](./media/quickstarts/formre-select-docker-linux.png)

5. Następnie wybierz kartę **Docker** . 

   > [!div class="mx-imgBorder"]
   > ![Wybierz platformę Docker](./media/quickstarts/formre-select-docker.png)

6. Teraz Skonfigurujmy kontener platformy Docker. Wszystkie pola są wymagane, o ile nie wskazano inaczej:

    # <a name="v20"></a>[Wersja 2.0](#tab/v2-0)  
   * Opcje — zaznacz **pojedynczy kontener**
   * Źródło obrazu — wybierz **Rejestr prywatny** 
   * Adres URL serwera — Ustaw tę wartość na `https://mcr.microsoft.com`
   * Nazwa użytkownika (opcjonalnie) — Utwórz nazwę użytkownika. 
   * Hasło (opcjonalnie) — Utwórz bezpieczne hasło.
   * Obraz i tag — Ustaw tę wartość na `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * Ciągłe wdrażanie — Ustaw tę wartość **na włączone** , jeśli chcesz otrzymywać aktualizacje automatyczne, gdy zespół programistyczny wprowadza zmiany w przykładowym narzędziu do etykietowania.
   * Uruchamianie polecenia — Ustaw tę opcję na `./run.sh eula=accept`

    # <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1) 
   * Opcje — zaznacz **pojedynczy kontener**
   * Źródło obrazu — wybierz **Rejestr prywatny** 
   * Adres URL serwera — Ustaw tę wartość na `https://mcr.microsoft.com`
   * Nazwa użytkownika (opcjonalnie) — Utwórz nazwę użytkownika. 
   * Hasło (opcjonalnie) — Utwórz bezpieczne hasło.
   * Obraz i tag — Ustaw tę wartość na `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:2.1.012970002-amd64-preview`
   * Ciągłe wdrażanie — Ustaw tę wartość **na włączone** , jeśli chcesz otrzymywać aktualizacje automatyczne, gdy zespół programistyczny wprowadza zmiany w przykładowym narzędziu do etykietowania.
   * Uruchamianie polecenia — Ustaw tę opcję na `./run.sh eula=accept`
    
    ---

   > [!div class="mx-imgBorder"]
   > ![Skonfiguruj platformę Docker](./media/quickstarts/formre-configure-docker.png)

7. To wszystko. Następnie wybierz kolejno pozycje **Przegląd + Utwórz**, a następnie pozycję **Utwórz** , aby wdrożyć aplikację sieci Web. Po zakończeniu możesz uzyskać dostęp do aplikacji sieci Web przy użyciu adresu URL podanego w **omówieniu** dla zasobu.

> [!NOTE]
> Podczas tworzenia aplikacji sieci Web można również skonfigurować autoryzację/uwierzytelnianie. Nie jest to konieczne, aby rozpocząć pracę. 

> [!IMPORTANT]
> Może być konieczne włączenie protokołu TLS dla aplikacji sieci Web w celu wyświetlenia jej w `https` adresie. Postępuj zgodnie z instrukcjami w temacie [Włączanie punktu końcowego protokołu TLS](https://docs.microsoft.com/azure/container-instances/container-instances-container-group-ssl) w celu skonfigurowania kontenera przyczepki niż włączenie protokołu TLS/SSL dla aplikacji sieci Web.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Alternatywą dla korzystania z Azure Portal można utworzyć zasób przy użyciu interfejsu wiersza polecenia platformy Azure. Przed kontynuowaniem należy zainstalować [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Możesz pominąć ten krok, jeśli już pracujesz z interfejsem wiersza polecenia platformy Azure. 

Istnieje kilka rzeczy, które należy znać dla tego polecenia:

* `DNS_NAME_LABEL=aci-demo-$RANDOM` generuje losową nazwę DNS. 
* W tym przykładzie przyjęto założenie, że istnieje grupa zasobów, której można użyć do utworzenia zasobu. Zamień `<resource_group_name>` na prawidłową grupę zasobów skojarzoną z subskrypcją. 
* Należy określić lokalizację, w której chcesz utworzyć zasób. Zamień `<region name>` na żądany region aplikacji sieci Web. 
* To polecenie automatycznie akceptuje Umowę EULA.

W interfejsie wiersza polecenia platformy Azure Uruchom to polecenie, aby utworzyć zasób aplikacji sieci Web dla przykładowego narzędzia do etykietowania: 


# <a name="v20"></a>[Wersja 2.0](#tab/v2-0)   
```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
``` 
# <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)    
```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
```

---

### <a name="connect-to-azure-ad-for-authorization"></a>Nawiązywanie połączenia z usługą Azure AD w celu autoryzacji

Zalecamy podłączenie aplikacji sieci Web do Azure Active Directory. Dzięki temu tylko użytkownicy z prawidłowymi poświadczeniami mogą się zalogować i korzystać z aplikacji sieci Web. Postępuj zgodnie z instrukcjami w temacie [Konfigurowanie aplikacji App Service](../../app-service/configure-authentication-provider-aad.md) , aby połączyć się z Azure Active Directory.

## <a name="open-source-on-github"></a>Open Source w serwisie GitHub

Narzędzie do etykietowania formularza OCR jest również dostępne jako projekt "open source" w witrynie GitHub. Narzędzie to aplikacja sieci Web skompilowana przy użyciu reaguje i Redux i jest zapisywana w języku TypeScript. Aby dowiedzieć się więcej lub współtworzyć, zobacz [Narzędzia do etykietowania formularzy OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Następne kroki

Skorzystaj z przewodnika Szybki Start [z etykietami](./quickstarts/label-tool.md) , aby dowiedzieć się, jak ręcznie etykietować dane szkoleniowe i przeprowadzać nadzorowane uczenie przy użyciu narzędzia.
