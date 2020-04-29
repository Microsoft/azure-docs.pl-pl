---
title: Azure Data Box Edge dostęp do urządzenia, Włączanie i tryb łączności | Microsoft Docs
description: Zawiera opis sposobu zarządzania dostępem, możliwością i trybem łączności dla urządzenia Azure Data Box Edge, które ułatwia transfer danych do platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: be4b780161003470622cb367d78138cfeffe341b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79454336"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Zarządzanie dostępem, możliwością i trybem łączności dla Azure Data Box Edge

W tym artykule opisano sposób zarządzania dostępem, możliwością i trybem łączności dla Azure Data Box Edge. Te operacje są wykonywane za pośrednictwem lokalnego interfejsu użytkownika sieci Web lub Azure Portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Zarządzanie dostępem do urządzeń
> * Zarządzanie trybem łączności
> * Zarządzanie mocą


## <a name="manage-device-access"></a>Zarządzanie dostępem do urządzeń

Dostęp do urządzenia Data Box Edge jest kontrolowany przy użyciu hasła urządzenia. Hasło można zmienić za pomocą lokalnego interfejsu użytkownika sieci Web. Możesz również zresetować hasło urządzenia w Azure Portal.

### <a name="change-device-password"></a>Zmienianie hasła urządzenia

Wykonaj następujące kroki w lokalnym interfejsie użytkownika, aby zmienić hasło urządzenia.

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja, > zmienić hasła**.
2. Wprowadź bieżące hasło, a następnie nowe hasło. Podane hasło musi zawierać od 8 do 16 znaków. Hasło musi zawierać 3 z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne. Potwierdź nowe hasło.

    ![Zmień hasło](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Wybierz pozycję **Zmień hasło**.
 
### <a name="reset-device-password"></a>Resetowanie hasła urządzenia

Resetowanie przepływu pracy nie wymaga od użytkownika odwoływania starego hasła i jest przydatne, gdy hasło zostanie utracone. Ten przepływ pracy jest wykonywany w Azure Portal.

1. W Azure Portal przejdź do **omówienia > Resetowanie hasła administratora**.

    ![Resetowanie hasła](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Wprowadź nowe hasło, a następnie potwierdź je. Podane hasło musi zawierać od 8 do 16 znaków. Hasło musi zawierać 3 z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne. Wybierz pozycję **Resetuj**.

    ![Resetowanie hasła](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Zarządzenie dostępem do zasobów

Aby utworzyć zasób Data Box Edge/Data Box Gateway, IoT Hub i zasobów usługi Azure Storage, musisz mieć uprawnienia jako współautor lub wyższy na poziomie grupy zasobów. Wymagane są także odpowiednie dostawcy zasobów do zarejestrowania. W przypadku wszelkich operacji, które obejmują klucz aktywacji i poświadczenia, wymagane są również uprawnienia do interfejsu API Microsoft Graph. Opisano je w poniższych sekcjach.

### <a name="manage-microsoft-graph-api-permissions"></a>Zarządzanie uprawnieniami interfejsu API Microsoft Graph

Podczas generowania klucza aktywacji dla urządzenia Data Box Edge lub wykonywania żadnych operacji wymagających poświadczeń wymagane są uprawnienia do Microsoft Graph interfejsu API. Operacje, które wymagają poświadczeń, mogą być następujące:

-  Tworzenie udziału ze skojarzonym kontem magazynu.
-  Tworzenie użytkownika, który może uzyskiwać dostęp do udziałów na urządzeniu.

Musisz mieć `User` dostęp do Active Directory dzierżawy, ponieważ musisz mieć możliwość `Read all directory objects`. Nie możesz być użytkownikiem-gościem, ponieważ nie ma uprawnień `Read all directory objects`do programu. Jeśli jesteś gościem, operacjami takimi jak generacja klucza aktywacji, utworzenie udziału na urządzeniu Data Box Edge, utworzenie użytkownika, konfiguracja roli obliczeń brzegowych spowoduje niepowodzenie wszystkich operacji resetowania hasła urządzenia.

Aby uzyskać więcej informacji na temat zapewniania dostępu użytkownikom do Microsoft Graph interfejsu API, zobacz [Microsoft Graph informacje o uprawnieniach](https://docs.microsoft.com/graph/permissions-reference).

### <a name="register-resource-providers"></a>Zarejestruj dostawców zasobów

Aby udostępnić zasób na platformie Azure (w modelu Azure Resource Manager), potrzebny jest dostawca zasobów, który obsługuje tworzenie tego zasobu. Na przykład, aby zainicjować obsługę administracyjną maszyny wirtualnej, w subskrypcji powinien być dostępny dostawca zasobów Microsoft. COMPUTE.
 
Dostawcy zasobów są rejestrowani na poziomie subskrypcji. Domyślnie każda nowa subskrypcja platformy Azure ma wstępnie zarejestrowaną listę najczęściej stosowanych dostawców zasobów. Dostawca zasobów dla elementu "Microsoft. DataBoxEdge" nie jest uwzględniony na tej liście.

Nie musisz przyznawać uprawnień dostępu dla poziomu subskrypcji, aby użytkownicy mogli tworzyć zasoby takie jak "Microsoft. DataBoxEdge" w grupach zasobów, do których mają prawa właściciela, o ile dostawcy zasobów dla tych zasobów są już zarejestrowani.

Przed podjęciem próby utworzenia dowolnego zasobu upewnij się, że dostawca zasobów został zarejestrowany w ramach subskrypcji. Jeśli dostawca zasobów nie jest zarejestrowany, należy się upewnić, że użytkownik tworzący nowy zasób ma wystarczającą ilość uprawnień do zarejestrowania wymaganego dostawcy zasobów na poziomie subskrypcji. Jeśli nie zostało to jeszcze zrobione, zobaczysz następujący komunikat o błędzie:

*Nazwa subskrypcji \<subskrypcji> nie ma uprawnień do rejestrowania dostawców zasobów: Microsoft. DataBoxEdge.*


Aby uzyskać listę zarejestrowanych dostawców zasobów w bieżącej subskrypcji, uruchom następujące polecenie:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

W przypadku urządzenia Data Box Edge `Microsoft.DataBoxEdge` należy zarejestrować. Aby zarejestrować `Microsoft.DataBoxEdge`się, administrator subskrypcji powinien uruchomić następujące polecenie:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Aby uzyskać więcej informacji na temat rejestrowania dostawcy zasobów, zobacz [Rozwiązywanie problemów dotyczących rejestracji dostawcy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Zarządzanie trybem łączności

Poza domyślnym trybem w pełni połączonym urządzenie można również uruchomić w trybie częściowo połączonym lub w pełni rozłączonym. Każdy z tych trybów jest opisany poniżej:

- W **pełni połączony** — jest to normalny tryb domyślny, w którym działa urządzenie. W tym trybie jest włączone przekazywanie i pobieranie danych w chmurze. Aby zarządzać urządzeniem, można użyć Azure Portal lub lokalnego interfejsu użytkownika sieci Web.

- **Częściowo Rozłączono** — w tym trybie urządzenie nie może przekazywać ani pobierać danych udostępnionych, jednak można nimi zarządzać za pośrednictwem Azure Portal.

    Ten tryb jest zazwyczaj używany w przypadku taryfowej sieci satelitarnej i celem jest Minimalizacja użycia przepustowości sieci. W przypadku operacji monitorowania urządzeń może nadal występować minimalne zużycie sieci.

- **Rozłączono** — w tym trybie urządzenie jest w pełni odłączone od chmury, a operacje przekazywania i pobierania w chmurze są wyłączone. Urządzenie może być zarządzane tylko za pośrednictwem lokalnego interfejsu użytkownika sieci Web.

    Ten tryb jest zazwyczaj używany, gdy chcesz przełączyć urządzenie do trybu offline.

Aby zmienić tryb urządzenia, wykonaj następujące kroki:

1. W lokalnym interfejsie użytkownika sieci Web urządzenia przejdź do pozycji **konfiguracja > ustawienia chmury**.
2. Z listy rozwijanej wybierz tryb, w którym chcesz korzystać z urządzenia. Możesz wybrać z w **pełni połączone**, **częściowo połączone**i **całkowicie odłączone**. Aby uruchomić urządzenie w trybie częściowo rozłączona, Włącz **zarządzanie Azure Portal**.

    ![Tryb łączności](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Zarządzanie mocą

Możesz zamknąć lub ponownie uruchomić urządzenie fizyczne przy użyciu lokalnego interfejsu użytkownika sieci Web. Zalecamy, aby przed ponownym uruchomieniem przełączyć udziały w tryb offline na serwerze danych, a następnie na urządzeniu. Ta akcja minimalizuje wszelką możliwość uszkodzenia danych.

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja > ustawienia zarządzania**.
2. Wybierz pozycję **Zamknij** lub **Uruchom ponownie** w zależności od tego, co chcesz zrobić.

    ![Ustawienia zasilania](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak** , aby wykonać operację.

> [!NOTE]
> Jeśli urządzenie fizyczne zostanie wyłączone, należy wypchnąć przycisk energia na urządzeniu, aby go włączyć.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać udziałami](data-box-edge-manage-shares.md).
