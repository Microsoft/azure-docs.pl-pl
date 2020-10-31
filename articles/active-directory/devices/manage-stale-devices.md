---
title: Jak zarządzać nieaktywnymi urządzeniami w usłudze Azure AD | Microsoft Docs
description: Informacje o usuwaniu starych urządzeń z bazy danych zarejestrowanych urządzeń w Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7651f84e14d6ea7dcb4e12d57e2bf494d5aeff1e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083188"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>Instrukcje: Zarządzanie nieodświeżonymi urządzeniami w usłudze Azure AD

W idealnym przypadku, aby zakończyć cykl życia, zarejestrowane urządzenia powinny zostać wyrejestrowane, gdy nie są już potrzebne. Ale czasami, na przykład z powodu zgubienia, kradzieży lub uszkodzenia urządzenia albo ponownej instalacji systemu operacyjnego, w Twoim środowisku pojawiają się nieaktywne urządzenia. Jako administrator IT prawdopodobnie potrzebujesz metody usuwania takich nieaktywnych urządzeń, aby Twoje zasoby mogły skoncentrować się na zarządzaniu urządzeniami, które faktycznie wymagają zarządzania.

W tym artykule dowiesz się, jak skutecznie zarządzać nieaktywnymi urządzeniami we własnym środowisku.
  

## <a name="what-is-a-stale-device"></a>Co to jest nieaktywne urządzenie?

Nieaktywnym nazywamy urządzenie, które zostało zarejestrowane za pomocą usługi Azure AD, ale nie było używane do uzyskiwania dostępu do aplikacji w chmurze w określonym przedziale czasu. Nieaktywne urządzenia wpływają na możliwość obsługi urządzeń i użytkowników oraz zarządzania nimi w ramach dzierżawy z następujących powodów: 

- Zduplikowane urządzenia mogą utrudnić pracownikom działu pomocy technicznej zidentyfikowanie urządzenia, które jest aktualnie aktywne.
- Zwiększona liczba urządzeń umożliwia tworzenie niepotrzebnych funkcji zapisywania zwrotnego urządzeń w celu zwiększenia czasu synchronizacji programu Azure AD Connect.
- W celu zachowania porządku i spełnienia wymagań dotyczących zgodności warto mieć „czysty” stan urządzeń. 

Istnienie nieaktywnych urządzeń w usłudze Azure AD może być niezgodne z ogólnymi zasadami cyklu życia urządzeń w Twojej organizacji.

## <a name="detect-stale-devices"></a>Wykrywanie nieaktywnych urządzeń

Ponieważ nieaktywne urządzenie jest zdefiniowane jako zarejestrowane urządzenie, które nie było używane do uzyskiwania dostępu do aplikacji w chmurze w określonym przedziale czasu, wykrywanie nieaktywnych urządzeń wymaga właściwości powiązanej ze znacznikiem czasu. W usłudze Azure AD ta właściwość nazywa się **ApproximateLastLogonTimestamp** lub **znacznik czasu aktywności** . Jeśli różnica między bieżącą datą a wartością **znacznika czasu aktywności** przekroczy przedział czasu zdefiniowany dla aktywnych urządzeń, urządzenie jest uznawane za nieaktywne. Ten **znacznik czasu aktywności** znajduje się obecnie w publicznej wersji zapoznawczej.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Jak jest zarządzana wartość znacznika czasu aktywności?  

Obliczanie znacznika czasu aktywności jest wyzwalane przez próbę uwierzytelnienia urządzenia. Usługa Azure AD oblicza znacznik czasu aktywności w następujących sytuacjach:

- Zasady dostępu warunkowego wymagające, że [zarządzane urządzenia](../conditional-access/require-managed-devices.md) lub [zatwierdzone aplikacje klienckie](../conditional-access/app-based-conditional-access.md) zostały wyzwolone.
- Wykazywanie aktywności w sieci przez urządzenia z systemem Windows 10, które są dołączone do usługi Azure AD lub dołączone hybrydowo do usługi Azure AD. 
- Zaewidencjonowanie w usłudze urządzeń zarządzanych przy użyciu usługi Intune.

Jeśli różnica między istniejącą wartością sygnatury czasowej działania a bieżącą wartością jest większa niż 14 dni (wariancja +/5 dni), istniejąca wartość jest zastępowana nową wartością.

## <a name="how-do-i-get-the-activity-timestamp"></a>Jak mogę uzyskać znacznik czasu aktywności?

Istnieją dwie możliwości uzyskania wartości znacznika czasu aktywności:

- Kolumna **Aktywność** na [stronie urządzeń](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) w witrynie Azure Portal

    :::image type="content" source="./media/manage-stale-devices/01.png" alt-text="Zrzut ekranu strony w Azure Portal wyświetlania nazwy, właściciela i innych informacji na urządzeniach. W jednej kolumnie jest wyświetlana sygnatura czasowa działania." border="false":::

- Polecenie cmdlet [Get-AzureADDevice](/powershell/module/azuread/Get-AzureADDevice)

    :::image type="content" source="./media/manage-stale-devices/02.png" alt-text="Zrzut ekranu strony w Azure Portal wyświetlania nazwy, właściciela i innych informacji na urządzeniach. W jednej kolumnie jest wyświetlana sygnatura czasowa działania." border="false":::

## <a name="plan-the-cleanup-of-your-stale-devices"></a>Planowanie oczyszczania nieaktywnych urządzeń

Aby skutecznie oczyścić nieaktywne urządzenia w swoim środowisku, zdefiniuj powiązane zasady. Te zasady pomagają zapewnić uwzględnienie wszystkich zagadnień dotyczących nieaktywnych urządzeń. Poniższe sekcje zawierają przykłady typowych zagadnień uwzględnianych w zasadach. 

### <a name="cleanup-account"></a>Konto oczyszczania

Aby zaktualizować urządzenie w usłudze Azure AD, potrzebne jest konto, które ma przypisaną jedną z następujących ról:

- Administrator globalny
- Administrator urządzenia w chmurze
- Administrator usługi Intune

W zasadach oczyszczania wybierz konta, które mają przypisane wymagane role. 

### <a name="timeframe"></a>Przedział czasu

Zdefiniuj przedział czasu, który jest wskaźnikiem służącym do wykrywania nieaktywnego urządzenia. Podczas definiowania przedziału czasu należy wziąć pod uwagę okno w celu zaktualizowania sygnatury czasowej działania do wartości. Na przykład nie należy uwzględniać sygnatury czasowej starszej niż 21 dni (łącznie z wariancją) jako wskaźnika dla nieodświeżonego urządzenia. Istnieją sytuacje, w których urządzenie może wyglądać na nieaktywne, chociaż tak nie jest. Na przykład gdy właściciel urządzenia jest na urlopie lub na zwolnieniu lekarskim   przekraczającym przedział czasu zdefiniowany dla nieaktywnych urządzeń.

### <a name="disable-devices"></a>Wyłączanie urządzeń

Nie zaleca się natychmiastowego usuwania urządzenia, które wydaje się być nieaktualne, ponieważ takiego usunięcia nie można cofnąć, gdy uznanie urządzenia za nieaktywne okaże się wynikiem fałszywie dodatnim. Najlepszym rozwiązaniem jest wyłączenie urządzenia na okres prolongaty przed jego usunięciem. W zasadach zdefiniuj przedział czasu wyłączenia urządzenia przed jego usunięciem.

### <a name="mdm-controlled-devices"></a>Urządzenia kontrolowane przez rozwiązanie MDM

Jeśli urządzenie jest kontrolowane przez usługę Intune lub dowolne inne rozwiązanie do zarządzania urządzeniami mobilnymi (MDM), wycofaj to urządzenie z takiego rozwiązania przed jego wyłączeniem lub usunięciem.

### <a name="system-managed-devices"></a>Urządzenia zarządzane przez system

Nie należy usuwać urządzeń zarządzanych przez system. Są to zazwyczaj urządzenia, takie jak autopilotaż. Po usunięciu tych urządzeń nie można ponownie zainicjować obsługi administracyjnej. Nowe polecenie cmdlet `Get-AzureADDevice` domyślnie wyklucza urządzenia zarządzane przez system. 

### <a name="hybrid-azure-ad-joined-devices"></a>Urządzenia dołączone hybrydowo do usługi Azure AD

W przypadku urządzeń dołączonych hybrydowo do usługi Azure AD powinny być przestrzegane lokalne zasady zarządzania nieaktywnymi urządzeniami. 

Oczyszczanie w usłudze Azure AD:

- **Urządzenia z systemem Windows 10** — wyłącz lub usuń urządzenia z systemem Windows 10 w lokalnej usłudze AD i pozwól usłudze Azure AD Connect zsynchronizować zmieniony stan urządzenia z usługą Azure AD.
- **Windows 7/8** — najpierw należy wyłączyć lub usunąć urządzenia z systemem Windows 7/8 w lokalnej usłudze AD. Nie możesz używać programu Azure AD Connect do wyłączania ani usuwania urządzeń z systemem Windows 7 lub 8 w usłudze Azure AD. Zamiast tego, po wprowadzeniu zmiany w środowisku lokalnym, należy ją wyłączyć lub usunąć w usłudze Azure AD.

> [!NOTE]
>* Usunięcie urządzeń w lokalnej usłudze AD lub usłudze Azure AD nie powoduje usunięcia rejestracji na kliencie. Uniemożliwi dostęp do zasobów tylko za pomocą urządzenia jako tożsamości (np. dostępu warunkowego). Zapoznaj się z dodatkowymi informacjami na temat [usuwania rejestracji na kliencie](faq.md#hybrid-azure-ad-join-faq).
>* Usunięcie urządzenia z systemem Windows 10 tylko w usłudze Azure AD spowoduje ponowne zsynchronizowanie urządzenia z lokalnego przy użyciu programu Azure AD Connect, ale jako nowy obiekt w stanie "oczekiwanie". Na urządzeniu wymagana jest ponowna rejestracja.
>* Usunięcie urządzenia z zakresu synchronizacji dla urządzeń z systemem Windows 10/Server 2016 spowoduje usunięcie urządzenia usługi Azure AD. Dodanie jej z powrotem do zakresu synchronizacji spowoduje umieszczenie nowego obiektu w stanie "oczekiwanie". Wymagana jest ponowna rejestracja urządzenia.
>* Jeśli nie używasz Azure AD Connect do synchronizowania urządzeń z systemem Windows 10 (np. przy użyciu AD FS do rejestracji), musisz zarządzać cyklem życia podobnym do urządzeń z systemem Windows 7/8.


### <a name="azure-ad-joined-devices"></a>Urządzenia dołączone do usługi Azure AD

Urządzenia dołączone do usługi Azure AD wyłącza się lub usuwa w usłudze Azure AD.

> [!NOTE]
>* Usunięcie urządzenia usługi Azure AD nie powoduje usunięcia rejestracji na kliencie. Uniemożliwi dostęp do zasobów tylko za pomocą urządzenia jako tożsamości (np. dostępu warunkowego). 
>* Dowiedz się więcej na temat [rozłączania w usłudze Azure AD](faq.md#azure-ad-join-faq) 

### <a name="azure-ad-registered-devices"></a>Urządzenia zarejestrowane w usłudze Azure AD

Urządzenia zarejestrowane w usłudze Azure AD wyłącza się lub usuwa w usłudze Azure AD.

> [!NOTE]
>* Usunięcie zarejestrowanego urządzenia usługi Azure AD w usłudze Azure AD nie powoduje usunięcia rejestracji na kliencie. Uniemożliwi dostęp do zasobów tylko za pomocą urządzenia jako tożsamości (np. dostępu warunkowego).
>* Przeczytaj więcej na temat [sposobu usuwania rejestracji na kliencie](faq.md#azure-ad-register-faq)

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Oczyszczanie nieaktywnych urządzeń w witrynie Azure Portal  

Nieaktywne urządzenia można oczyszczać w witrynie Azure Portal, ale bardziej wydajne jest wykonywanie tego za pomocą skryptu programu PowerShell. Użyj najnowszego modułu programu PowerShell V1, aby użyć filtru sygnatur czasowych i odfiltrować urządzenia zarządzane przez system, takie jak autopilotaż. W tym przypadku użycie programu PowerShell w wersji 2 nie jest zalecane.

Typowa procedura obejmuje następujące czynności:

1. Nawiązywanie połączenia z usługą Azure Active Directory przy użyciu polecenia cmdlet [Connect-AzureAD](/powershell/module/azuread/connect-azuread)
1. Pobieranie listy urządzeń
1. Wyłącz urządzenie przy użyciu polecenia cmdlet [Set-AzureADDevice](/powershell/module/azuread/Set-AzureADDevice) (Wyłącz przy użyciu opcji-AccountEnabled). 
1. Przed usunięciem urządzenia poczekaj, aż upłynie wybrana przez Ciebie liczba dni okresu prolongaty.
1. Usuń urządzenie przy użyciu polecenia cmdlet [Remove-AzureADDevice](/powershell/module/azuread/Remove-AzureADDevice) .

### <a name="get-the-list-of-devices"></a>Pobieranie listy urządzeń

Aby uzyskać listę wszystkich urządzeń i zachować zwrócone dane w pliku CSV, użyj następującego polecenia:

```PowerShell
Get-AzureADDevice -All:$true | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-summary.csv
```

Jeśli masz dużą liczbę urządzeń w katalogu, Użyj filtru sygnatur czasowych, aby zawęzić liczbę zwróconych urządzeń. Aby uzyskać wszystkie urządzenia ze znacznikiem czasu starszym niż określona data oraz zachować zwrócone dane w pliku CSV, użyj następującego polecenia: 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-AzureADDevice | Where {$_.ApproximateLastLogonTimeStamp -le $dt} | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Dlaczego znacznik czasu nie jest aktualizowany częściej?

Znacznik czasu jest aktualizowany w celu obsługi scenariuszy cyklu życia urządzenia. Nie jest to inspekcja. Aby wykonywać częstsze aktualizacje na urządzeniu, użyj dzienników inspekcji logowania.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>Dlaczego muszę zadbać o klucze funkcji BitLocker?

Skonfigurowane klucze funkcji BitLocker dla urządzeń z systemem Windows 10 są przechowywane w obiekcie urządzenia w usłudze Azure AD. Jeśli usuwasz nieaktywne urządzenie, usuwasz również klucze funkcji BitLocker, które są przechowywane na tym urządzeniu. Przed usunięciem nieaktywnego urządzenia musisz sprawdzić, czy zasady oczyszczania są zgodne z rzeczywistym cyklem życia urządzenia. 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>Dlaczego należy się martwić o urządzenia z systemem Windows

Po usunięciu urządzenia usługi Azure AD, które zostało skojarzone z obiektem autopilotażu systemu Windows, następujące trzy scenariusze mogą wystąpić, jeśli urządzenie zostanie przecelowo w przyszłości:
- W przypadku wdrożeń opartych na użytkownikach w systemie Windows, które nie korzystają z funkcji wstępnej aprowizacji, zostanie utworzone nowe urządzenie usługi Azure AD, ale nie będzie ono otagowane przy użyciu ZTDID.
- W przypadku wdrożeń w trybie samoobsługowego wdrażania automatycznego systemu Windows nie będą one działać, ponieważ nie można odnaleźć skojarzenia z urządzeniem usługi Azure AD.  (Jest to mechanizm zabezpieczeń, aby upewnić się, że żadne urządzenia "nie będące skrytkami" próbują dołączyć do usługi Azure AD bez poświadczeń). Błąd będzie wskazywać niezgodność ZTDID.
- W przypadku wdrożeń wstępnego inicjowania obsługi administracyjnej w systemie Windows nie będą one działać, ponieważ nie można znaleźć skojarzonego urządzenia usługi Azure AD. (W tle wdrożenia wstępnej aprowizacji używają tego samego procesu samoobsługowego wdrażania, więc wymuszają te same mechanizmy zabezpieczeń).

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Jak mogę sprawdzić, czy wszystkie typy urządzeń zostały dołączone?

Aby dowiedzieć się więcej na temat różnych typów, zobacz [omówienie zarządzania urządzeniami](overview.md).

### <a name="what-happens-when-i-disable-a-device"></a>Co się stanie, gdy urządzenie zostanie wyłączone?

Każde uwierzytelnianie z użyciem urządzenia w usłudze Azure AD jest odrzucane. Typowe przykłady:

- **Hybrydowe urządzenie dołączone do usługi Azure AD** — użytkownicy mogą korzystać z urządzenia, aby zalogować się do domeny lokalnej. Nie mogą jednak uzyskać dostępu do zasobów usługi Azure AD, takich jak Microsoft 365.
- **Urządzenia dołączone do usługi Azure AD** — użytkownicy nie mogą używać urządzenia do logowania. 
- **Urządzenia przenośne** — użytkownik nie może uzyskać dostępu do zasobów usługi Azure AD, takich jak Microsoft 365. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie sposobu zarządzania urządzeniami w witrynie Azure Portal, zobacz [zarządzanie urządzeniami przy użyciu witryny Azure Portal](device-management-azure-portal.md)
