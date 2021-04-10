---
title: Przepis wystąpienia kontenera platformy Azure
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrażać kontenery Cognitive Services w usłudze Azure Container instance
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 4641723ed1ad334ab9f960728f4986b03fd0ac99
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065892"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Wdrażanie i uruchamianie kontenera w wystąpieniu kontenera platformy Azure

Poniższe kroki umożliwiają łatwe skalowanie aplikacji Cognitive Services platformy Azure w chmurze za pomocą usługi Azure [Container Instances](../../container-instances/index.yml). Kontenerach ułatwia skoncentrowanie się na tworzeniu aplikacji, a nie zarządzaniu infrastrukturą. Aby uzyskać więcej informacji na temat korzystania z kontenerów, zobacz [funkcje i korzyści](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Wymagania wstępne

Przepis działa z jakimkolwiek kontenerem Cognitive Services. Przed użyciem przepisu należy utworzyć zasób usługi poznawczej. Każda usługa poznawcze, która obsługuje kontenery, zawiera artykuł "jak zainstalować" na potrzeby instalowania i konfigurowania usługi dla kontenera. Niektóre usługi wymagają pliku lub zestawu plików jako dane wejściowe dla kontenera, dlatego ważne jest, aby zrozumieć i używało kontenera pomyślnie przed użyciem tego rozwiązania.

* Zasób platformy Azure dla usługi poznawczej platformy Azure, której używasz.
* **Adres URL punktu końcowego** usługi poznawczej — zapoznaj się z określoną usługą "jak zainstalować" dla kontenera, aby sprawdzić, gdzie znajduje się adres URL punktu końcowego w Azure Portal i jak wygląda prawidłowy przykład adresu URL. Dokładny format może ulec zmianie od usługi do usługi.
* **Klucz** usługi poznawczej — klucze znajdują się na stronie **klucze** zasobu platformy Azure. Potrzebny jest tylko jeden z tych dwóch kluczy. Klucz jest ciągiem 32 znaków alfanumerycznych.

* Pojedynczy kontener Cognitive Services na hoście lokalnym (Twoim komputerze). Upewnij się, że możesz:
  * Pobierz obraz za pomocą `docker pull` polecenia.
  * Pomyślnie uruchomiono kontener lokalny ze wszystkimi wymaganymi ustawieniami konfiguracji za pomocą `docker run` polecenia.
  * Wywołaj punkt końcowy kontenera, pobierając odpowiedź HTTP 2xx i odpowiedź JSON z powrotem.

Wszystkie zmienne w nawiasach kątowych `<>` muszą zostać zastąpione własnymi wartościami. To zastąpienie obejmuje nawiasy ostre.

> [!IMPORTANT]
> Kontener LUIS wymaga `.gz` pliku modelu, który jest pobierany w czasie wykonywania. Kontener musi być w stanie uzyskać dostęp do tego pliku modelu za pośrednictwem instalacji woluminu z wystąpienia kontenera. Aby przekazać plik modelu, wykonaj następujące kroki:
> 1. [Utwórz udział plików platformy Azure](../../storage/files/storage-how-to-create-file-share.md). Zanotuj nazwę konta usługi Azure Storage, klucz i nazwę udziału plików, ponieważ będą potrzebne później.
> 2. [Eksportuj model Luis (spakowaną aplikację) z portalu Luis](../LUIS/luis-container-howto.md#export-packaged-app-from-luis). 
> 3. W Azure Portal przejdź do strony **Przegląd** zasobu konta magazynu, a następnie wybierz pozycję **udziały plików**. 
> 4. Wybierz ostatnio utworzoną nazwę udziału plików, a następnie wybierz pozycję **Przekaż**. Następnie Przekaż spakowaną aplikację. 

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>Korzystanie z wystąpienia kontenera

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. Wybierz **Przegląd** i skopiuj adres IP. Będzie to liczbowy adres IP, taki jak `55.55.55.55` .
1. Otwórz nową kartę przeglądarki i użyj adresu IP, na przykład, `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Zostanie wyświetlona strona główna kontenera z informacją o tym, że kontener jest uruchomiony.

    ![Strona główna kontenera](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. Wybierz pozycję **opis interfejsu API usługi** , aby wyświetlić stronę struktury Swagger dla kontenera.

1. Wybierz dowolny z **wpisów** interfejsów API i wybierz opcję **Wypróbuj**.  Parametry są wyświetlane wraz z danymi wejściowymi. Wypełnij parametry.

1. Wybierz polecenie **Wykonaj** , aby wysłać żądanie do wystąpienia kontenera.

    Tworzenie i używanie kontenerów Cognitive Services w usłudze Azure Container instance zostało pomyślnie zakończone.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> Jeśli używasz analiza tekstu dla kontenera kondycji, użyj następującego adresu URL, aby przesłać zapytania: `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
