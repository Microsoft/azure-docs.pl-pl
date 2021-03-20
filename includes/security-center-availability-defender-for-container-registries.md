---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 2112cde42ee00b78a82962ee46f53110068977c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98187359"
---
## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|Opłaty za **usługę Azure Defender dla rejestrów kontenerów** są rozliczane zgodnie z pokazaną na [stronie cennika](../articles/security-center/security-center-pricing.md)|
|Obsługiwane rejestry i obrazy:|Obrazy systemu Linux w rejestrach ACR dostępnych z publicznej sieci Internet z dostępem do powłoki|
|Nieobsługiwane rejestry i obrazy:|Obrazy systemu Windows<br>Rejestry prywatne<br>Rejestry z dostępem ograniczonym do zapory, punktu końcowego usługi lub prywatnych punktów końcowych, takich jak link prywatny platformy Azure<br>Obrazy Super minimalistyczny, takie jak obrazy wyłuskane [Docker](https://hub.docker.com/_/scratch/) lub obrazy "Distroless", które zawierają tylko aplikacje i ich zależności środowiska uruchomieniowego bez Menedżera pakietów, powłoki lub systemu operacyjnego|
|Wymagane role i uprawnienia:|**Czytelnik zabezpieczeń** i [role Azure Container Registry i uprawnienia](../articles/container-registry/container-registry-roles.md)|
|Połączeń|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Chmury komercyjne<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov i Chiny gov — obecnie jest obsługiwana tylko funkcja skanowania w trybie push. Dowiedz się więcej w [przypadku skanowania obrazów?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)|
|||