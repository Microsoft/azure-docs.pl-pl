---
title: Zarządzaj twórcą Microsoft Azure Maps (wersja zapoznawcza)
description: W tym artykule dowiesz się, jak zarządzać twórcą Microsoft Azure Maps (wersja zapoznawcza).
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/16/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d26df4287032bc59cc58dd1d832d9d5a9c40afcd
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559139"
---
# <a name="manage-azure-maps-creator-preview"></a>Zarządzaj twórcą Azure Maps (wersja zapoznawcza) 

> [!IMPORTANT]
> Usługi Azure Maps Creator Services są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Kreator Azure Maps umożliwia tworzenie prywatnych danych mapy pomieszczeń. Korzystając z interfejsu API Azure Maps i modułu Maps (mapy wewnętrzne), można tworzyć interaktywne i dynamiczne aplikacje sieci Web z zamapowanej mapy. Obecnie Kreator jest dostępny tylko w Stany Zjednoczone przy użyciu warstwy cenowej S1.

W tym artykule opisano kroki tworzenia i usuwania zasobu twórcy na koncie Azure Maps.

## <a name="create-creator-preview-resource"></a>Tworzenie zasobu twórcy (wersja zapoznawcza)

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

2. Wybierz konto Azure Maps. Jeśli nie widzisz konta Azure Maps w obszarze **ostatnie zasoby**, przejdź do menu Azure Portal. Wybierz pozycję **Wszystkie zasoby**. Znajdź i wybierz swoje konto Azure Maps.

    ![Strona główna portalu Azure Maps](./media/how-to-manage-creator/select-maps-account.png)

3. Gdy jesteś na stronie konta Azure Maps, przejdź do opcji **Przegląd** w obszarze **Creator**(Tworzenie). Wybierz pozycję  **Utwórz**  , aby utworzyć zasób twórcy Azure Maps.

    ![Tworzenie strony kreatora Azure Maps](./media/how-to-manage-creator/creator-blade-settings.png)

4. Wprowadź nazwę i lokalizację zasobu twórcy. Obecnie Kreator jest obsługiwany tylko w Stany Zjednoczone. Wybierz pozycję **Przejrzyj i utwórz**.

   ![Wprowadź stronę informacji o koncie twórcy](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Przejrzyj ustawienia i wybierz pozycję **Utwórz**.

    ![Strona Potwierdzanie ustawień konta twórcy](./media/how-to-manage-creator/creator-create-dialog.png)

6. Po zakończeniu wdrożenia zostanie wyświetlona strona z komunikatem o powodzeniu lub niepowodzeniu.

   ![Strona stanu wdrożenia zasobu](./media/how-to-manage-creator/creator-resource-created.png)

7. Wybierz pozycję **Przejdź do zasobu**. Na stronie widok zasobów twórcy zostanie wyświetlony stan zasobu twórcy i wybrany region demograficzny.

    ![Strona stanu twórcy](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >Na stronie zasobów twórcy można wrócić do konta Azure Maps, do którego należy, wybierając pozycję Azure Maps Account (konto).

## <a name="delete-creator-preview-resource"></a>Usuwanie zasobu twórcy (wersja zapoznawcza)

Aby usunąć zasób twórcy, przejdź do swojego konta Azure Maps. Wybierz pozycję **Przegląd** w obszarze **twórca**. Kliknij przycisk **Usuń**.

>[!WARNING]
>Usunięcie zasobu twórcy konta Azure Maps spowoduje również usunięcie zestawów danych, tilesets i funkcji statesets utworzonych przy użyciu usług Creator Services.

![Strona kreatora z przyciskiem Usuń](./media/how-to-manage-creator/creator-delete.png)

Wybierz przycisk **Usuń** i wpisz swoją nazwę twórcy, aby potwierdzić usunięcie. Po usunięciu zasobu zostanie wyświetlona strona potwierdzenia, jak na poniższej ilustracji:

![Strona twórcy z potwierdzeniem usunięcia](./media/how-to-manage-creator/creator-confirm-delete.png)

## <a name="authentication"></a>Authentication

Twórca (wersja zapoznawcza) dziedziczy ustawienia Azure Maps Access Control (IAM). Wszystkie wywołania interfejsu API do uzyskiwania dostępu do danych muszą być wysyłane z regułami uwierzytelniania i autoryzacji.

Dane użycia twórcy są zawarte w Azure Maps wykresy użycia i dziennik aktywności.  Aby uzyskać więcej informacji, zobacz [Zarządzanie uwierzytelnianiem w Azure Maps](./how-to-manage-authentication.md).

## <a name="access-to-creator-services"></a>Dostęp do usług Creator

Usługi Creator Services (wersja zapoznawcza) i usługi korzystające z danych hostowanych przez twórcę (na przykład usługi renderowania) są dostępne w geograficznym adresie URL. Geograficzny adres URL jest określany na podstawie lokalizacji wybranej podczas tworzenia. Na przykład jeśli utworzono twórcę w lokalizacji geograficznej Stany Zjednoczone, wszystkie wywołania do usługi konwersji muszą zostać przesłane do `us.atlas.microsoft.com/conversion/convert` .

Ponadto wszystkie dane zaimportowane do twórcy powinny zostać przekazane do tej samej lokalizacji geograficznej co zasób twórcy. Na przykład jeśli twórca jest zainicjowany w podanej Zjednoczonym, wszystkie dane pierwotne powinny zostać przekazane za pośrednictwem `us.atlas.microsoft.com/mapData/upload` .

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do usług Creator Services (wersja zapoznawcza) na potrzeby mapowania pomieszczeń:

> [!div class="nextstepaction"]
> [Przekazywanie danych](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Konwersja danych](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Zestawu](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Zestaw stanu funkcji](creator-indoor-maps.md#feature-statesets)

Dowiedz się, jak korzystać z usług Creator Services (wersja zapoznawcza) w celu renderowania map wewnętrznych w aplikacji:

> [!div class="nextstepaction"]
> [Samouczek kreatora Azure Maps](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamiczne style mapy](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Korzystanie z modułu Maps (mapy wewnętrzne)](how-to-use-indoor-module.md)