---
title: Szybki Start — Tworzenie wystąpienia usługi Azure API Management
description: Utwórz nowe wystąpienie usługi Azure API Management przy użyciu Azure Portal.
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: apimpm
ms.openlocfilehash: 66d0874536b358b720b43780d6ecce879ac866ee
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90708210"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>Szybki Start: Tworzenie nowego wystąpienia usługi Azure API Management przy użyciu Azure Portal

Usługa Azure API Management (APIM) pomaga organizacjom publikować interfejsy API dla deweloperów zewnętrznych, partnerskich i wewnętrznych, aby w pełni wykorzystać potencjał danych i usług. Usługa API Management zapewnia podstawowe możliwości, które pozwalają na pomyślne programowanie interfejsów API przez zaangażowanych deweloperów, a także udostępnia wgląd w kwestie biznesowe, analizy, zabezpieczenia i ochronę. APIM umożliwia tworzenie nowoczesnych bram interfejsu API i zarządzanie nimi dla istniejących usług zaplecza hostowanych w dowolnym miejscu. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem](api-management-key-concepts.md).

W tym przewodniku Szybki start opisano procedurę tworzenia nowego wystąpienia usługi API Management za pomocą witryny Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

:::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-created.png" alt-text="Wystąpienie API Management":::

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-new-service"></a>Tworzenie nowej usługi

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**. Możesz również wybrać pozycję **Utwórz zasób** na stronie **głównej** platformy Azure. 
   
   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="Wystąpienie API Management":::

   
1. Na stronie **Nowy** wybierz pozycję **integracja**  >  **API Management**.

   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-02.png" alt-text="Wystąpienie API Management":::
   
1. Na stronie **usługa API Management** wprowadź ustawienia.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="Wystąpienie API Management":::
   
   | Ustawienie                 | Opis   |                                                                     
   |-------------------------|-----------------------------------------------|
   | **Nazwa**                | Unikatowa nazwa usługi API Management. Nazwy nie można później zmienić. Nazwa usługi odnosi się zarówno do usługi, jak i odpowiedniego zasobu platformy Azure. <br/> Nazwa usługi jest używana do generowania domyślnej nazwy domeny: * \<name\> . Azure-API.NET.* Jeśli chcesz użyć niestandardowej nazwy domeny, zobacz [Konfigurowanie domeny niestandardowej](configure-custom-domain.md). |
   | **Subskrypcja**          | Subskrypcja, w ramach której zostanie utworzone to nowe wystąpienie usługi.   |
   | **Grupa zasobów**      |  Wybierz nową lub istniejącą grupę zasobów. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. |
   | **Lokalizacja**          | Wybierz region geograficzny w sąsiedztwie od dostępnych lokalizacji usługi API Management. | 
   | **Nazwa organizacji**   | Nazwa organizacji. Ta nazwa jest używana w wielu miejscach, w tym w tytule portalu dla deweloperów i nadawcy wiadomości e-mail z powiadomieniem. |                                                         
   | **Adres e-mail administratora** | Adres e-mail, na który będą wysyłane wszystkie powiadomienia z **API Management** .   |  
   | **Warstwa cenowa**        | Wybierz pozycję warstwa **dewelopera** , aby oszacować usługę. Ta warstwa nie jest używana do użycia w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat skalowania warstw usługi API Management, zobacz [Upgrade and scale](upgrade-and-scale.md) (Uaktualnianie i skalowanie). |

3. Wybierz przycisk **Utwórz**.

    > [!TIP]
    > Utworzenie i aktywowanie usługi API Management w tej warstwie może potrwać od 30 do 40 minut. Wybranie pozycji **Przypnij do pulpitu nawigacyjnego** ułatwia znajdowanie nowo utworzonej usługi.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

Zapoznaj się z właściwościami usługi na stronie **Przegląd** .

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-created.png" alt-text="Wystąpienie API Management":::

Gdy wystąpienie usługi API Management jest w trybie online, możesz go używać. Zacznij od samouczka, aby [zaimportować i opublikować pierwszy interfejs API](import-and-publish.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć, wykonując następujące kroki:

1. W Azure Portal Wyszukaj i wybierz pozycję **grupy zasobów**. Możesz również wybrać **grupy zasobów** na stronie **głównej** . 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="Wystąpienie API Management":::

1. Na stronie **grupy zasobów** wybierz grupę zasobów.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="Wystąpienie API Management":::

1. Na stronie Grupa zasobów wybierz pozycję **Usuń grupę zasobów**. 
   
1. Wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-03.png" alt-text="Wystąpienie API Management":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md)
