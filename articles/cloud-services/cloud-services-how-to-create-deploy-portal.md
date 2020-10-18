---
title: Jak utworzyć i wdrożyć usługę w chmurze | Microsoft Docs
description: Dowiedz się, jak za pomocą metody Quick Create utworzyć usługę w chmurze i użyć przekazywania, aby przekazać i wdrożyć pakiet usługi w chmurze na platformie Azure.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 66938975784f1de2abdc0ac22e62aaca82279f86
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164168"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Jak utworzyć i wdrożyć usługę w chmurze
Azure Portal zapewnia dwa sposoby tworzenia i wdrażania usługi w chmurze: *szybkie tworzenie* i *Tworzenie niestandardowe*.

W tym artykule wyjaśniono, jak za pomocą metody Quick Create utworzyć nową usługę w chmurze, a następnie za pomocą **przekazywania** przekazać i wdrożyć pakiet usługi w chmurze na platformie Azure. W przypadku korzystania z tej metody Azure Portal udostępnia wygodne linki do uzupełniania wszystkich wymagań zgodnie z rzeczywistym użyciem. Jeśli wszystko jest gotowe do wdrożenia usługi w chmurze podczas jej tworzenia, można wykonać obie czynności przy użyciu opcji tworzenie niestandardowe.

> [!NOTE]
> Jeśli planujesz publikację usługi w chmurze w usłudze Azure DevOps, użyj opcji szybkie tworzenie, a następnie skonfiguruj publikowanie usługi Azure DevOps z poziomu przewodnika Szybki Start platformy Azure lub pulpitu nawigacyjnego. Aby uzyskać więcej informacji, zobacz [ciągłe dostarczanie do platformy Azure za pomocą usługi Azure DevOps][TFSTutorialForCloudService]lub zapoznaj się z informacjami na stronie **Szybki Start** .
>
>

## <a name="concepts"></a>Pojęcia
Do wdrożenia aplikacji jako usługi w chmurze na platformie Azure wymagane są trzy składniki:

* **Definicja usługi**  
  Plik definicji usługi w chmurze (. csdef) definiuje model usług, w tym liczbę ról.
* **Konfiguracja usługi**  
  Plik konfiguracji usługi w chmurze (cscfg) zawiera ustawienia konfiguracji usługi w chmurze i poszczególnych ról, w tym liczbę wystąpień ról.
* **Pakiet usługi**  
  Pakiet usługi (. cspkg) zawiera kod i konfiguracje aplikacji oraz plik definicji usługi.

Więcej informacji o tych i sposobach tworzenia pakietu można znaleźć [tutaj](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Przygotowywanie aplikacji
Aby można było wdrożyć usługę w chmurze, należy utworzyć pakiet usługi w chmurze (. cspkg) z kodu aplikacji i pliku konfiguracji usługi w chmurze (cscfg). Zestaw Azure SDK oferuje narzędzia do przygotowywania wymaganych plików wdrożenia. Zestaw SDK można zainstalować ze strony [plików do pobrania platformy Azure](https://azure.microsoft.com/downloads/) w języku, w którym wolisz opracowywać kod aplikacji.

Trzy funkcje usługi w chmurze wymagają specjalnych konfiguracji przed wyeksportowaniem pakietu usługi:

* Jeśli chcesz wdrożyć usługę w chmurze, która korzysta z Transport Layer Security (TLS), wcześniej znanej jako SSL (SSL), aby szyfrować dane, [Skonfiguruj aplikację](cloud-services-configure-ssl-certificate-portal.md#modify) dla protokołu TLS.
* Jeśli chcesz skonfigurować połączenia Pulpit zdalny z wystąpieniami ról, [Skonfiguruj role](cloud-services-role-enable-remote-desktop-new-portal.md) dla Pulpit zdalny.
* Jeśli chcesz skonfigurować pełne monitorowanie dla usługi w chmurze, Włącz Diagnostyka Azure dla usługi w chmurze. *Minimalne monitorowanie* (domyślny poziom monitorowania) używa liczników wydajności zebranych z systemów operacyjnych hosta dla wystąpień roli (maszyn wirtualnych). *Pełne monitorowanie* zbiera dodatkowe metryki na podstawie danych wydajności w wystąpieniach roli w celu zapewnienia bliższej analizy problemów występujących podczas przetwarzania aplikacji. Aby dowiedzieć się, jak włączyć Diagnostyka Azure, zobacz [Włączanie diagnostyki na platformie Azure](cloud-services-dotnet-diagnostics.md).

Aby utworzyć usługę w chmurze z wdrożeniami ról sieci Web lub procesów roboczych, należy [utworzyć pakiet usługi](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Jeśli nie zainstalowano zestawu Azure SDK, kliknij pozycję **Zainstaluj zestaw Azure SDK** , aby otworzyć [stronę pliki do pobrania platformy Azure](https://azure.microsoft.com/downloads/), a następnie Pobierz zestaw SDK dla języka, w którym wolisz opracowywać swój kod. (Możesz to zrobić później).
* Jeśli jakieś wystąpienia roli wymagają certyfikatu, należy utworzyć certyfikaty. Usługi w chmurze wymagają pliku PFX z kluczem prywatnym. Certyfikaty można przesłać na platformę Azure podczas tworzenia i wdrażania usługi w chmurze.

## <a name="create-and-deploy"></a>Tworzenie i wdrażanie
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Kliknij pozycję **Utwórz zasób > obliczenia**, a następnie przewiń w dół do i kliknij pozycję **Usługa w chmurze**.

    ![Zrzut ekranu, który wyróżnia opcję usługi w chmurze w obszarze Tworzenie zasobu > obliczenia.](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. W okienku Nowa **Usługa w chmurze** wprowadź wartość w polu **nazwa DNS**.
4. Utwórz nową **grupę zasobów** lub wybierz istniejącą.
5. Wybierz **lokalizację**.
6. Kliknij pozycję **pakiet**. Spowoduje to otwarcie okienka **Przekaż pakiet** . Wypełnij pola wymagane. Jeśli dowolna z ról zawiera pojedyncze wystąpienie, należy upewnić się, że jest **ono wdrożone nawet wtedy, gdy co najmniej jedna z ról zawiera wybrane pojedyncze wystąpienie** .
7. Upewnij się, że wybrano **Rozpocznij wdrażanie** .
8. Kliknij przycisk **OK** , aby zamknąć okienko **Przekaż pakiet** .
9. Jeśli nie masz żadnych certyfikatów do dodania, kliknij pozycję **Utwórz**.

    ![Publikowanie usługi w chmurze](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Przekaż certyfikat
Jeśli pakiet wdrożeniowy został [skonfigurowany do korzystania z certyfikatów](cloud-services-configure-ssl-certificate-portal.md#modify), możesz go teraz przekazać.

1. Wybierz pozycję **Certyfikaty**, a następnie w okienku **Dodawanie certyfikatów** wybierz plik certyfikatu TLS/SSL, a następnie podaj **hasło** dla certyfikatu.
2. Kliknij pozycję **Dołącz certyfikat**, a następnie kliknij przycisk **OK** w okienku **Dodawanie certyfikatów** .
3. Kliknij przycisk **Utwórz** w okienku **usługi w chmurze** . Gdy wdrożenie osiągnie stan **gotowości** , można przejść do kolejnych kroków.

    ![Zrzut ekranu, który wyróżnia proces przekazywania certyfikatu.](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Sprawdź, czy wdrożenie zostało ukończone pomyślnie
1. Kliknij wystąpienie usługi w chmurze.

    Stan powinien wskazywać, że usługa jest **uruchomiona**.
2. W obszarze **podstawy**kliknij **adres URL witryny** , aby otworzyć usługę w chmurze w przeglądarce internetowej.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: ./cloud-services-choose-me.md

## <a name="next-steps"></a>Następne kroki
* [Ogólna konfiguracja usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Skonfiguruj [niestandardową nazwę domeny](cloud-services-custom-domain-name-portal.md).
* [Zarządzanie usługą w chmurze](cloud-services-how-to-manage-portal.md).
* Skonfiguruj [Certyfikaty TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).