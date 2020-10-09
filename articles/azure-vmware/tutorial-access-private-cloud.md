---
title: Samouczek — dostęp do chmury prywatnej
description: Dowiedz się, jak uzyskać dostęp do chmury prywatnej rozwiązania Azure VMware
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: b7f21dbba5256712607241bb1258962fc22418fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578426"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Samouczek: dostęp do chmury prywatnej rozwiązania Azure VMware

Rozwiązanie Azure VMware nie pozwala na zarządzanie chmurą prywatną przy użyciu lokalnego serwera vCenter. Należy wykonać dodatkowe czynności konfiguracyjne i połączenie z lokalnym wystąpieniem programu vCenter za pomocą pola skoku. 

W tym samouczku utworzysz pole skoku w grupie zasobów utworzonej w [poprzednim samouczku](tutorial-configure-networking.md) i zalogujesz się do programu vCenter. Pole skoku jest maszyną wirtualną z systemem Windows w tej samej sieci wirtualnej, która została utworzona.  Zapewnia ona dostęp do programu vCenter i NSX Manager. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Windows do użycia w celu nawiązania połączenia z programem vCenter
> * Zaloguj się do programu vCenter z maszyny wirtualnej

## <a name="create-a-new-windows-virtual-machine"></a>Tworzenie nowej maszyny wirtualnej z systemem Windows

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Nawiązywanie połączenia z lokalnym programem vCenter w chmurze prywatnej

1. W polu skoku Zaloguj się, aby vSphere klienta z programem VMware vCenter SSO przy użyciu nazwy użytkownika administratora chmury i Verity, że interfejs użytkownika zostanie wyświetlony pomyślnie.

1. W Azure Portal Wybierz chmurę prywatną, a następnie w widoku **Przegląd** wybierz pozycję **tożsamość > domyślna**. 

   Adresy URL i poświadczenia użytkownika dla usługi Private Cloud vCenter i NSX-T Menedżera.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Wyświetlaj adresy URL i poświadczenia usługi Cloud webvcenter i NSX Manager." border="true":::

1. Przejdź do maszyny wirtualnej utworzonej w poprzednim kroku, a następnie połącz się z maszyną wirtualną. 

   Jeśli potrzebujesz pomocy przy nawiązywaniu połączenia z maszyną wirtualną, zobacz temat [nawiązywanie połączenia z maszyną wirtualną](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) w celu uzyskania szczegółowych informacji.

1. Na maszynie wirtualnej z systemem Windows otwórz przeglądarkę i przejdź do adresu URL menedżera vCenter i NSX-T na dwóch kartach. 

1. Na karcie vCenter wprowadź `cloudadmin@vmcp.local` poświadczenia użytkownika z poprzedniego kroku.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Wyświetlaj adresy URL i poświadczenia usługi Cloud webvcenter i NSX Manager." border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="Wyświetlaj adresy URL i poświadczenia usługi Cloud webvcenter i NSX Manager." border="true":::

1. Na drugiej karcie przeglądarki Zaloguj się do Menedżera NSX-T.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Wyświetlaj adresy URL i poświadczenia usługi Cloud webvcenter i NSX Manager." border="true":::



## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Windows do użycia w celu nawiązania połączenia z programem vCenter
> * Zaloguj się do programu vCenter z maszyny wirtualnej

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć sieć wirtualną w celu skonfigurowania lokalnego zarządzania klastrami w chmurze prywatnej.

> [!div class="nextstepaction"]
> [Tworzenie Virtual Network](tutorial-configure-networking.md)


