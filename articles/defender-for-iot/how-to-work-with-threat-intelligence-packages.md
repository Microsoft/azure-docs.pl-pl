---
title: Aktualizowanie danych analizy zagrożeń
description: Pakiet danych analizy zagrożeń jest dostarczany z każdą nową wersją usługi Defender dla IoT lub w razie potrzeby między wydaniami.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: bb38d0a2486bda336d6881ec6f4c5d680906c973
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750468"
---
# <a name="threat-intelligence-research-and-packages"></a>Badania i pakiety analizy zagrożeń #
## <a name="overview"></a>Omówienie ##

Zespoły ds. zabezpieczeń w firmie Microsoft przeprowadzają zastrzeżone analizy zagrożeń ICS i badania luk w zabezpieczeniach. Są to m.in. MSTIC (Microsoft Threat Intelligence Center), JEDNOSTKI wykrywania i reagowania firmy Microsoft, DCU (digital crimes unit) i sekcja 52 (eksperci w zakresie domeny IoT/OT/ICS, którzy śledzą specyficzne dla ICS zero dni, złośliwe oprogramowanie odwrotnej inżynierii, kampanie i przeciwników)

Zespoły zapewniają wykrywanie zabezpieczeń, analizę i reagowanie na działania firmy Microsoft:

- Infrastruktura i usługi w chmurze.
- Tradycyjne produkty i urządzenia.
- Wewnętrzne zasoby firmowe.

Zespoły ds. zabezpieczeń uzyskują korzyści z:

- Ochrona przed znanymi i odpowiednimi zagrożeniami.
- Szczegółowe informacje, które ułatwiają określanie priorytetów i ich określanie.
- Zrozumienie pełnego kontekstu zagrożeń przed ich wpływem.
- Bardziej istotne, dokładne i z akcjami dane.

Ta analiza udostępnia informacje kontekstowe w celu wzbogacenia analizy platformy firmy Microsoft i obsługuje usługi zarządzane przez firmę w zakresie reagowania na zdarzenia i badania naruszeń. Pakiety analizy zagrożeń zawierają podpisy (w tym sygnatury złośliwego oprogramowania), cve i inną zawartość zabezpieczeń.

## <a name="when-are-packages-delivered"></a>Kiedy są dostarczane pakiety ##

Pakiety analizy zagrożeń są udostępniane mniej więcej raz w miesiącu lub w razie potrzeby częściej. Ogłoszenia o nowych pakietach są dostępne na stronie: https://techcommunity.microsoft.com/t5/azure-defender-for-iot/bd-p/AzureDefenderIoT . 

## <a name="update-threat-intelligence-packages-to-your-sensors"></a>Aktualizowanie pakietów analizy zagrożeń do czujników ##

Dostępne są trzy opcje aktualizowania pakietów analizy zagrożeń do czujników:

- Automatyczne wypychanie pakietów do czujników dostarczanych przez usługę Defender dla IoT.
- W razie potrzeby ręcznie wypychaj pakiet analizy zagrożeń do czujników.
- Pobierz pakiet, a następnie przekaż go do czujnika lub wielu czujników.

Użytkownicy z uprawnieniami czytelnika zabezpieczeń usługi Defender for IoT mogą automatycznie i ręcznie wypychać pakiety do czujników.

### <a name="automatically-push-threat-intelligence-updates-to-sensors"></a>Automatyczne wypychanie aktualizacji analizy zagrożeń do czujników ###

Pakiety analizy zagrożeń można automatycznie aktualizować *do* czujników połączonych z chmurą, gdy są one publikowane przez usługę Defender dla IoT. Zapewnij automatyczną aktualizację pakietu, dołączając czujnik połączony z chmurą z włączoną opcją **Automatyczne aktualizacje** analizy zagrożeń. Aby uzyskać więcej informacji, zobacz [Dołączanie czujnika](getting-started.md#onboard-a-sensor).

### <a name="manually-push-threat-intelligence-updates-to-sensors"></a>Ręczne wypychanie aktualizacji analizy zagrożeń do czujników ###

Czujniki *połączone z chmurą* można automatycznie aktualizować za pomocą pakietów analizy zagrożeń. Jeśli jednak chcesz przyjąć bardziej zachowawczy sposób, możesz wypychać pakiety z portalu Azure Defender dla IoT do czujników tylko wtedy, gdy uważasz, że jest to wymagane.
Dzięki temu można kontrolować, kiedy pakiet jest instalowany, bez konieczności pobierania, a następnie przekazywania go do czujników.

**Aby ręcznie wypchnąć pakiety:**

1. Przejdź do strony Azure Defender dla IoT **Lokacje i czujniki.**
1. Wybierz wielokropek (...) dla czujnika, a następnie wybierz **pozycję Wypychanie aktualizacji analizy zagrożeń.** Postęp **aktualizacji jest wyświetlany w** polu Stan aktualizacji analizy zagrożeń.

#### <a name="change-the-threat-intelligence-update-mode"></a>Zmienianie trybu aktualizacji analizy zagrożeń ####

Po początkowym dojecheniu można zmienić tryb aktualizacji analizy zagrożeń z czujników.

**Aby zmienić tryb aktualizacji:**

1. Wybierz wielokropek (...) dla czujnika, a następnie wybierz pozycję **Edytuj**.
1. Włącza lub wyłącza przełącznik **Automatyczne aktualizacje analizy** zagrożeń.

### <a name="download-packages-and-upload-to-sensors"></a>Pobieranie pakietów i przekazywanie do czujników ###

Pakiety można pobrać z portalu usługi Defender dla IoT i ręcznie przekazać do poszczególnych czujników. Jeśli lokalna konsola zarządzania zarządza czujnikami, możesz pobrać pakiety analizy zagrożeń do konsoli zarządzania i wypchnąć je do wielu czujników jednocześnie.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Pobieranie aktualizacji za pośrednictwem Azure Defender dla IoT portal.":::

Ta opcja jest dostępna zarówno dla *czujników połączonych z chmurą,* *jak i lokalnie zarządzanych.*

**Aby przekazać dane do pojedynczego czujnika:**

1. Przejdź do strony Azure Defender dla IoT **aktualizacje.**

2. Pobierz i zapisz pakiet **analizy** zagrożeń.

3. Zaloguj się do konsoli czujnika.

4. W menu bocznym wybierz pozycję **Ustawienia systemowe**.

5. Wybierz **pozycję Threat Intelligence Data (Dane analizy zagrożeń),** a następnie wybierz pozycję Update **(Aktualizuj).**

6. Przekaż nowy pakiet.

**Aby przesłać jednocześnie do wielu czujników:**

1. Przejdź do strony Azure Defender dla IoT **aktualizacje.**

2. Pobierz i zapisz pakiet **analizy** zagrożeń.

3. Zaloguj się do konsoli zarządzania.

4. W menu bocznym wybierz pozycję **Ustawienia systemowe**.

5. W sekcji **Konfiguracja aparatu czujnika** wybierz czujniki, które powinny odbierać zaktualizowane pakiety.  

6. W sekcji **Wybierz dane analizy zagrożeń** wybierz znak plus ( **+** ).

7. Przekaż pakiet.

## <a name="review-package-update-status-on-the-sensor"></a>Przeglądanie stanu aktualizacji pakietu na czujniku ##

Informacje o stanie aktualizacji pakietu i wersji są wyświetlane w sekcji **Ustawienia systemowe** czujnika , **Analizy** zagrożeń.  

## <a name="review-package-information-for-cloud-connected-sensors"></a>Przeglądanie informacji o pakiecie dla czujników połączonych z chmurą ##

Przejrzyj następujące informacje o pakietach analizy zagrożeń dla czujników połączonych z chmurą:

- Zainstalowana wersja pakietu
- Tryb aktualizacji analizy zagrożeń
- Stan aktualizacji analizy zagrożeń

Aby przejrzeć informacje dotyczące analizy zagrożeń:

1. Przejdź do strony Azure Defender dla IoT **Lokacje i czujniki.**
1. Zapoznaj się z **wersją analizy zagrożeń zainstalowaną** na każdym czujniku. Nazewnictwo wersji jest oparte na dniu, w który pakiet został sbudowaną przez usługę Defender dla IoT.
1. Przejrzyj tryb **analizy zagrożeń.** *Automatyczne* oznacza, że nowo dostępne pakiety zostaną automatycznie zainstalowane na czujnikach, gdy zostaną wydane przez usługę Defender dla IoT. *Ręczne* wskazuje, że można wypychać nowo dostępne pakiety bezpośrednio do czujników zgodnie z potrzebami.
1. Przejrzyj stan **aktualizacji analizy zagrożeń.** Mogą być wyświetlane następujące stany:

- Niepowodzenie
- W toku
- Dostępna aktualizacja
- Ok

Jeśli aktualizacje analizy zagrożeń połączone z chmurą nie powiodą się, przejrzyj informacje o połączeniu w kolumnach **Stan** czujnika i Ostatnio połączone **czas UTC** na **stronie Witryny i czujniki.** 

## <a name="see-also"></a>Zobacz też

[Dołączanie czujnika](getting-started.md#onboard-a-sensor)

[Zarządzanie czujnikami z konsoli zarządzania](how-to-manage-sensors-from-the-on-premises-management-console.md)