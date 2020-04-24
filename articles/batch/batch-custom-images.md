---
title: Inicjowanie obsługi administracyjnej puli z obrazu zarządzanego
description: Utwórz pulę usługi Batch z zasobu obrazu zarządzanego, aby udostępnić węzłom obliczeniowym oprogramowanie i dane aplikacji.
ms.topic: article
ms.date: 09/16/2019
ms.openlocfilehash: 10e3932bc6006e1d91fbc7e4cf58a5d98c043520
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117322"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Tworzenie puli maszyn wirtualnych przy użyciu obrazu zarządzanego

Aby utworzyć niestandardowy obraz dla maszyn wirtualnych puli usługi Batch, możesz użyć [galerii obrazów udostępnionych](batch-sig-images.md)lub zasobu *obrazu zarządzanego* .

> [!TIP]
> W większości przypadków należy utworzyć obrazy niestandardowe przy użyciu galerii obrazów udostępnionych. Za pomocą galerii obrazów udostępnionych można udostępniać pule szybciej, skalować większe ilości maszyn wirtualnych i zwiększyć niezawodność podczas aprowizacji maszyn wirtualnych. Aby dowiedzieć się więcej, zobacz [Tworzenie puli niestandardowej za pomocą galerii obrazów udostępnionych](batch-sig-images.md).

## <a name="prerequisites"></a>Wymagania wstępne

- **Zasób obrazu zarządzanego**. Aby utworzyć pulę maszyn wirtualnych przy użyciu obrazu niestandardowego, musisz mieć lub utworzyć zasób obrazu zarządzanego w tej samej subskrypcji i regionie platformy Azure, co konto usługi Batch. Obraz należy utworzyć na podstawie migawek dysku systemu operacyjnego maszyny wirtualnej i opcjonalnie dołączonych dysków danych. Więcej informacji i kroków związanych z przygotowaniem zarządzanego obrazu znajduje się w następującej sekcji.
  - Użyj unikatowego obrazu niestandardowego dla każdej utworzonej puli.
  - Aby utworzyć pulę z obrazem przy użyciu interfejsów API usługi Batch, określ **Identyfikator zasobu** obrazu, który ma postać `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Aby użyć portalu, użyj **nazwy** obrazu.  
  - Zasób obrazu zarządzanego powinien istnieć dla okresu istnienia puli, aby umożliwić skalowanie w górę i można go usunąć po usunięciu puli.

- **Uwierzytelnianie Azure Active Directory (AAD)**. Interfejs API klienta usługi Batch musi korzystać z uwierzytelniania usługi AAD. Azure Batch obsługa usługi AAD została opisana w temacie [uwierzytelnianie rozwiązań w usłudze Batch przy użyciu Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Przygotowywanie obrazu niestandardowego

Na platformie Azure można przygotować zarządzany obraz z:

- Migawki systemu operacyjnego i dysków danych maszyny wirtualnej platformy Azure
- Uogólniona maszyna wirtualna platformy Azure z dyskami zarządzanymi
- Uogólniony, lokalny wirtualny dysk twardy przekazany do chmury

Aby w sposób niezawodny skalować pule usługi Batch przy użyciu obrazu niestandardowego, zalecamy utworzenie obrazu zarządzanego przy użyciu *tylko* pierwszej metody: używanie migawek dysków maszyny wirtualnej. Aby przygotować maszynę wirtualną, wykonać migawkę i utworzyć obraz na podstawie migawki, zobacz następujące kroki.

### <a name="prepare-a-vm"></a>Przygotowywanie maszyny wirtualnej

Jeśli tworzysz nową maszynę wirtualną dla obrazu, Użyj obrazu z witryny Azure Marketplace w pierwszej kolejności jako obrazu podstawowego dla zarządzanego obrazu. Jako obrazu podstawowego można używać tylko obrazów pierwszej strony. Aby uzyskać pełną listę odwołań do obrazów w portalu Azure Marketplace obsługiwanych przez Azure Batch, zobacz część operacji [jednostek SKU agenta węzła listy](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) .

> [!NOTE]
> Nie można użyć obrazu innej firmy, który ma dodatkową licencję i warunki zakupu jako obraz podstawowy. Aby uzyskać informacje na temat tych obrazów z portalu Marketplace, zobacz Wskazówki dotyczące maszyn wirtualnych z systemem [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) lub [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) .

- Upewnij się, że maszyna wirtualna została utworzona przy użyciu dysku zarządzanego. Jest to domyślne ustawienie magazynu podczas tworzenia maszyny wirtualnej.
- Na maszynie wirtualnej nie należy instalować rozszerzeń platformy Azure, takich jak rozszerzenie niestandardowego skryptu. Jeśli obraz zawiera wstępnie zainstalowane rozszerzenie, platforma Azure może napotkać problemy podczas wdrażania puli usługi Batch.
- W przypadku korzystania z dołączonych dysków danych należy zainstalować i sformatować dyski z poziomu maszyny wirtualnej w celu ich użycia.
- Upewnij się, że określony obraz podstawowego systemu operacyjnego używa domyślnego dysku tymczasowego. Agent węzła wsadowego aktualnie oczekuje domyślnego dysku tymczasowego.
- Gdy maszyna wirtualna jest uruchomiona, nawiąż połączenie z nią za pośrednictwem protokołu RDP (dla systemu Windows) lub SSH (system Linux). Zainstaluj wymagane oprogramowanie lub skopiuj wymagane dane.  

### <a name="create-a-vm-snapshot"></a>Tworzenie migawki maszyny wirtualnej

Migawka to pełna kopia tylko do odczytu dysku VHD. Aby utworzyć migawkę systemu operacyjnego lub dysków danych maszyny wirtualnej, można użyć Azure Portal lub narzędzi wiersza polecenia. Aby zapoznać się z krokami i opcjami tworzenia migawki, zobacz Wskazówki dotyczące maszyn wirtualnych z systemem [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) lub [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) .

### <a name="create-an-image-from-one-or-more-snapshots"></a>Tworzenie obrazu z jednej lub kilku migawek

Aby utworzyć obraz zarządzany na podstawie migawki, użyj narzędzi wiersza polecenia platformy Azure, takich jak polecenie [AZ Image Create](/cli/azure/image) . Można utworzyć obraz, określając migawkę dysku systemu operacyjnego i opcjonalnie jedną lub więcej migawek dysków danych.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Tworzenie puli na podstawie obrazu niestandardowego w portalu

Po zapisaniu obrazu niestandardowego i Poznaniu jego identyfikatora lub nazwy zasobu Utwórz pulę usługi Batch na podstawie tego obrazu. Poniższe kroki pokazują, jak utworzyć pulę na podstawie Azure Portal.

> [!NOTE]
> Jeśli tworzysz pulę przy użyciu jednego z interfejsów API usługi Batch, upewnij się, że tożsamość używana do uwierzytelniania w usłudze AAD ma uprawnienia do zasobu obrazu. Zobacz temat [uwierzytelnianie rozwiązań usługi Batch za pomocą Active Directory](batch-aad-auth.md).
>
> Zasób zarządzanego obrazu musi istnieć dla okresu istnienia puli. W przypadku usunięcia zasobu bazowego nie można skalować puli.

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch. To konto musi znajdować się w tej samej subskrypcji i regionie co grupa zasobów zawierająca obraz niestandardowy.
2. W oknie **Ustawienia** po lewej stronie wybierz element menu **Pule** .
3. W oknie **Pule** wybierz polecenie **Dodaj** .
4. W oknie **Dodawanie puli** wybierz pozycję **obraz niestandardowy (Linux/Windows)** z listy rozwijanej **Typ obrazu** . Z listy rozwijanej **niestandardowy obraz maszyny wirtualnej** wybierz nazwę obrazu (krótką formą identyfikatora zasobu).
5. Wybierz prawidłową **wersję wydawcy/oferty/jednostki SKU** dla obrazu niestandardowego.
6. Określ pozostałe wymagane ustawienia, w tym **rozmiar węzła**, **docelowe węzły dedykowane**i **węzły o niskim priorytecie**, a także wszystkie wymagane ustawienia opcjonalne.

    Na przykład dla niestandardowego obrazu systemu Microsoft Windows Server Datacenter 2016 zostanie wyświetlone okno **Dodawanie puli** , jak pokazano poniżej:

    ![Dodawanie puli z niestandardowego obrazu systemu Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Aby sprawdzić, czy istniejąca Pula jest oparta na obrazie niestandardowym, zapoznaj się z właściwością **systemu operacyjnego** w sekcji Podsumowanie zasobów okna **Pula** . Jeśli pula została utworzona na podstawie niestandardowego obrazu, jest ustawiona na **niestandardowy obraz maszyny wirtualnej**.

Wszystkie obrazy niestandardowe skojarzone z pulą są wyświetlane w oknie **Właściwości** puli.

## <a name="considerations-for-large-pools"></a>Zagadnienia dotyczące dużych pul

Jeśli planujesz utworzenie puli z setkami maszyn wirtualnych lub więcej przy użyciu obrazu niestandardowego, należy postępować zgodnie z powyższymi wskazówkami, aby użyć obrazu utworzonego na podstawie migawki maszyny wirtualnej.

Należy również zwrócić uwagę na następujące zagadnienia:

- **Limity rozmiaru** — w przypadku korzystania z obrazu niestandardowego w usłudze Batch jest ograniczany rozmiar puli do 2500 dedykowanych węzłów obliczeniowych 1000 lub węzłów o niskim priorytecie.

  Jeśli używasz tego samego obrazu (lub wielu obrazów opartych na tej samej podstawowej migawce) do tworzenia wielu pul, Łączna liczba węzłów obliczeniowych w pulach nie może przekroczyć powyższych limitów. Nie zalecamy użycia obrazu lub jego podstawowej migawki dla więcej niż jednej puli.

  Limity można zmniejszyć w przypadku skonfigurowania puli przy użyciu [pul NAT dla ruchu przychodzącego](pool-endpoint-configuration.md).

- **Limit czasu zmiany rozmiaru** — Jeśli pula zawiera stałą liczbę węzłów (nie Skalowanie automatyczne), zwiększ właściwość resizeTimeout puli do wartości takiej jak 20-30 minut. Jeśli pula nie osiągnie rozmiaru docelowego w określonym limicie czasu, wykonaj inną [operację zmiany rozmiaru](/rest/api/batchservice/pool/resize).

  W przypadku planowania puli z więcej niż 300 węzłów obliczeniowych może zajść potrzeba zmiany rozmiaru puli wiele razy, aby osiągnąć rozmiar docelowy.
  
Za pomocą [galerii obrazów udostępnionych](batch-sig-images.md)można utworzyć większe pule z dostosowanymi obrazami wraz z bardziej udostępnionymi replikami obrazu. Przy użyciu obrazów udostępnionych czas trwania puli do osiągnięcia stanu stałego jest do 25% szybszy, a opóźnienie maszyny wirtualnej jest krótsze niż 30%.

## <a name="considerations-for-using-packer"></a>Zagadnienia dotyczące korzystania z programu Packer

Tworzenie zasobu obrazu zarządzanego bezpośrednio przy użyciu programu Packer można wykonać tylko z kontami usługi Batch w trybie subskrypcji użytkownika. W przypadku kont trybu usługi Batch należy najpierw utworzyć dysk VHD, a następnie zaimportować dysk VHD do zasobu obrazu zarządzanego. W zależności od trybu alokacji puli (subskrypcja użytkownika lub usługa Batch) kroki tworzenia zasobów obrazu zarządzanego będą się różnić.

Upewnij się, że zasób użyty do utworzenia obrazu zarządzanego istnieje dla okresów istnienia dowolnej puli odwołującej się do obrazu niestandardowego. Niewykonanie tej operacji może spowodować błędy alokacji puli i/lub zmiany rozmiaru.

Po usunięciu obrazu lub zasobu bazowego może zostać wyświetlony komunikat o błędzie podobny do: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Jeśli zostanie wyświetlony ten błąd, upewnij się, że źródłowy zasób nie został usunięty.

Aby uzyskać więcej informacji na temat korzystania z programu Packer w celu utworzenia maszyny wirtualnej, zobacz [Tworzenie obrazu systemu Linux przy użyciu pakietu Packer](../virtual-machines/linux/build-image-with-packer.md) lub [Kompilowanie obrazu z systemem Windows za pomocą programu Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Następne kroki

Szczegółowe omówienie usługi Batch można znaleźć w temacie [programowanie równoległych rozwiązań obliczeniowych na dużą skalę za pomocą usługi Batch](batch-api-basics.md).
