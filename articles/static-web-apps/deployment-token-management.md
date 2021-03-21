---
title: Zresetuj tokeny wdrażania w usłudze Azure static Web Apps (wersja zapoznawcza)
description: Resetowanie tokenów w witrynie Web Apps statycznej platformy Azure
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746520"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a>Zresetuj tokeny wdrażania w usłudze Azure static Web Apps (wersja zapoznawcza)

Podczas tworzenia nowej witryny Web Apps statycznej platformy Azure usługa Azure generuje token używany do identyfikowania aplikacji podczas wdrażania. Podczas aprowizacji ten token jest przechowywany jako wpis tajny w repozytorium GitHub. W tym artykule wyjaśniono, jak używać tego tokenu i zarządzać nim.

Zwykle nie trzeba martwić się o token wdrożenia, ale poniżej przedstawiono kilka powodów, dla których może być konieczne pobranie lub zresetowanie tokenu.

* **Naruszenie tokenu**: Zresetuj token, jeśli jest on narażony na zewnątrz.
* **Wdrażanie z oddzielnego repozytorium GitHub**: w przypadku ręcznego wdrażania z oddzielnego repozytorium GitHub należy ustawić token wdrożenia w nowym repozytorium.

## <a name="prerequisites"></a>Wymagania wstępne

- Istniejące repozytorium GitHub skonfigurowane przy użyciu statycznego Web Apps platformy Azure.
- Zobacz [kompilowanie pierwszej aplikacji statycznej](getting-started.md) , jeśli jej nie masz.

## <a name="reset-a-deployment-token"></a>Resetowanie tokenu wdrożenia

1. Kliknij link **Zarządzaj tokenem wdrażania** na stronie _Przegląd_ w witrynie sieci web usługi Azure static Web Apps.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="Zarządzanie tokenem wdrożenia":::

1. Kliknij przycisk **Zresetuj token** .

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="Resetowanie tokenu wdrożenia":::

1. Po wyświetleniu nowego tokenu w polu _token wdrożenia_ Skopiuj token, klikając ikonę **Kopiuj do schowka** .


## <a name="update-a-secret-in-the-github-repository"></a>Aktualizowanie wpisu tajnego w repozytorium GitHub

Aby zachować Automatyczne wdrażanie, po zresetowaniu tokenu należy ustawić nową wartość w odpowiednim repozytorium GitHub.

1. Przejdź do repozytorium projektu w usłudze GitHub i kliknij kartę **Ustawienia** .
1. Kliknij element **tajny** menu. Wpis tajny zostanie wygenerowany podczas aprowizacji statycznej aplikacji sieci Web o nazwie _AZURE_STATIC_WEB_APPS_API_TOKEN_... w sekcji wpisy _tajne repozytorium_ .

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="Wyświetlanie listy wpisów tajnych repozytorium":::

    > [!NOTE]
    > Jeśli utworzono lokację usługi Azure static Web Apps dla wielu oddziałów tego repozytorium, zobaczysz wiele _AZURE_STATIC_WEB_APPS_API_TOKEN_... wpisy tajne na tej liście. Wybierz odpowiednią opcję, dopasowując nazwę pliku wymienioną w polu _Edytuj przepływ pracy_ na karcie _Przegląd_ w witrynie statycznej Web Apps.

1. Kliknij przycisk **Aktualizuj** , aby otworzyć Edytor.
1. **Wklej wartość** tokenu wdrożenia do pola _wartość_ .
1. Kliknij przycisk **Aktualizuj klucz tajny**.

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="Aktualizowanie wpisu tajnego repozytorium":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Publikowanie z poziomu generatora witryn statycznych](publish-gatsby.md)
