---
title: Rozwiązywanie problemów ze starszymi przyłączonymi urządzeniami hybrydowymi Azure Active Directory
description: Rozwiązywanie problemów z Azure Active Directory hybrydowymi podłączonymi do urządzeń niższego poziomu.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbf936370b401a76479c7679499103d083e0752a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551751"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Rozwiązywanie problemów z Azure Active Directory hybrydowymi podłączonymi do urządzeń niższego poziomu 

Ten artykuł dotyczy tylko następujących urządzeń: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 z dodatkiem R2 
- Windows Server 2012 
- Windows Server 2012 z dodatkiem R2 

W przypadku systemu Windows 10 lub Windows Server 2016 zobacz [Rozwiązywanie problemów hybrydowych Azure Active Directory dołączone do urządzeń z systemem Windows 10 i Windows server 2016](troubleshoot-hybrid-join-windows-current.md).

W tym artykule przyjęto założenie, że [skonfigurowano urządzenia hybrydowe Azure Active Directory dołączone](hybrid-azuread-join-plan.md) do obsługi następujących scenariuszy:

- Dostęp warunkowy oparty na urządzeniach

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów, które rozwiązują potencjalne problemy.  

**Co należy wiedzieć:** 

- Hybrydowe dołączanie usługi Azure AD dla urządzeń z systemem Windows o niższych działaniach działa nieco inaczej niż w systemie Windows 10. Wielu klientów nie zakłada, że potrzebują AD FS (dla domen federacyjnych) lub bezproblemowe skonfigurowanie logowania jednokrotnego (dla domen zarządzanych).
- Bezproblemowe logowanie jednokrotne nie działa w trybie przeglądania prywatnego w przeglądarkach Firefox i Microsoft Edge. Nie działa również w programie Internet Explorer, jeśli przeglądarka działa w trybie rozszerzonym chronionym lub włączono konfigurację zwiększonych zabezpieczeń.
- W przypadku klientów z domenami federacyjnymi, jeśli punkt połączenia usługi został skonfigurowany w taki sposób, że wskazuje nazwę domeny zarządzanej (na przykład contoso.onmicrosoft.com, a nie contoso.com), to sprzężenie hybrydowe usługi Azure AD dla urządzeń z systemem Windows nie będzie działało.
- To samo urządzenie fizyczne pojawia się wiele razy w usłudze Azure AD, gdy wielu użytkowników domeny loguje się do hybrydowych urządzeń przyłączonych do usługi Azure AD.  Na przykład jeśli *JKowalski* i *jharnett* logowanie do urządzenia, dla każdego z nich zostanie utworzony osobna rejestracja (identyfikator urządzenia) na karcie Informacje o **użytkowniku** . 
- Możesz również uzyskać wiele wpisów dla urządzenia na karcie Informacje o użytkowniku z powodu ponownej instalacji systemu operacyjnego lub ręcznego ponownego rejestrowania.
- Początkowa Rejestracja/przyłączanie urządzeń jest skonfigurowany do wykonania próby zalogowania się lub zablokowania/odblokowania. Zadanie harmonogramu zadań może wystąpić z 5-minutowego opóźnienia. 
- Upewnij się, że zainstalowano [KB4284842](https://support.microsoft.com/help/4284842) , w przypadku systemu Windows 7 z dodatkiem SP1 lub windows Server 2008 R2 z dodatkiem SP1. Ta aktualizacja zapobiega występowaniu w przyszłości niepowodzeń uwierzytelniania z powodu utraty dostępu klienta do kluczy chronionych po zmianie hasła.
- Dołączanie hybrydowej usługi Azure AD może zakończyć się niepowodzeniem, gdy użytkownik zmieni nazwę UPN, przerywając proces uwierzytelniania logowania jednokrotnego. Podczas procesu dołączania może być widoczne, że nadal jest wysyłana stara nazwa UPN do usługi Azure AD, chyba że pliki cookie sesji przeglądarki są czyszczone lub użytkownik jawnie wyloguje się i usunie starą nazwę UPN.

## <a name="step-1-retrieve-the-registration-status"></a>Krok 1. Pobieranie stanu rejestracji 

**Aby sprawdzić stan rejestracji:**  

1. Zaloguj się przy użyciu konta użytkownika, które wykonał sprzężenie hybrydowe usługi Azure AD.
1. Otwórz wiersz polecenia 
1. Wpisz `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

To polecenie wyświetla okno dialogowe, które zawiera szczegółowe informacje o stanie sprzężenia.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/01.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe Workplace Join dla systemu Windows. Tekst zawierający adres e-mail wskazuje, że określone urządzenie jest przyłączone do miejsca pracy." border="false":::

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Krok 2. oszacowanie stanu hybrydowego przyłączenia do usługi Azure AD 

Jeśli urządzenie nie zostało dołączone do hybrydowej usługi Azure AD, możesz spróbować wykonać sprzężenie hybrydowe usługi Azure AD, klikając przycisk "Przyłączanie". Jeśli próba wykonania hybrydowego sprzężenia usługi Azure AD zakończy się niepowodzeniem, zostaną wyświetlone szczegółowe informacje o błędzie.

**Najczęstsze problemy są następujące:**

- Nieprawidłowo skonfigurowane AD FS lub problemy z usługą Azure AD lub sieci

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/02.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe Workplace Join dla systemu Windows. Tekst zgłasza, że wystąpił błąd podczas uwierzytelniania konta." border="false":::
    
   - Autoworkplace.exe nie może przeprowadzić dyskretnego uwierzytelniania przy użyciu usługi Azure AD lub AD FS. Może to być spowodowane brakiem lub nieprawidłowym skonfigurowaniem AD FS (dla domen federacyjnych) lub braku lub nieprawidłowym skonfigurowaniem pojedynczej Sign-Onowej usługi Azure AD (w przypadku domen zarządzanych) lub problemów z siecią. 
   - Może to być, że uwierzytelnianie wieloskładnikowe (MFA) jest włączone/skonfigurowane dla użytkownika, a WIAORMULTIAUTHN nie jest skonfigurowany na serwerze AD FS. 
   - Kolejną możliwością jest to, że strona odnajdowania obszaru macierzystego (HRD) oczekuje na interakcję użytkownika, co uniemożliwia **autoworkplace.exe** z dyskretnym żądaniem tokenu.
   - Może to być AD FS i brak adresów URL usługi Azure AD w strefie intranetowej programu IE na kliencie.
   - Problemy z łącznością sieciową mogą uniemożliwiać **autoworkplace.exe** do osiągnięcia AD FS lub adresów URL usługi Azure AD. 
   - **Autoworkplace.exe** wymaga od klienta bezpośredniej linii wglądu z klienta do lokalnego kontrolera domeny usługi AD w organizacji, co oznacza, że hybrydowe dołączenie do usługi Azure AD powiedzie się tylko wtedy, gdy klient jest połączony z intranetem firmy.
   - Twoja organizacja korzysta z bezproblemowego logowania jednokrotnego usługi Azure AD `https://autologon.microsoftazuread-sso.com` lub `https://aadg.windows.net.nsatc.net` nie jest dostępna w ustawieniach sieci intranet dla tego urządzenia i nie **można włączyć aktualizacji paska stanu za pomocą skryptu** dla strefy intranetowej.
- Użytkownik nie jest zalogowany jako użytkownik domeny

   :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/03.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe Workplace Join dla systemu Windows. Tekst zgłasza, że wystąpił błąd podczas weryfikacji konta." border="false":::

   Istnieje kilka różnych powodów, dla których może to być spowodowane:

   - Zalogowany użytkownik nie jest użytkownikiem domeny (na przykład użytkownik lokalny). Hybrydowe dołączanie usługi Azure AD na urządzeniach niskiego poziomu jest obsługiwane tylko dla użytkowników domeny.
   - Klient nie może nawiązać połączenia z kontrolerem domeny.    
- Osiągnięto limit przydziału

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/04.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe Workplace Join dla systemu Windows. Tekst zgłasza błąd, ponieważ użytkownik osiągnął maksymalną liczbę dołączonych urządzeń." border="false":::

- Usługa nie odpowiada 

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/05.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe Workplace Join dla systemu Windows. Tekst raportuje, że wystąpił błąd, ponieważ serwer nie odpowiedział." border="false":::

Informacje o stanie można także znaleźć w dzienniku zdarzeń w obszarze: **aplikacje i usługi Log\Microsoft-Workplace Join**
  
**Najczęstszymi przyczynami niepowodzenia hybrydowego przyłączenia do usługi Azure AD są:** 

- Komputer nie jest podłączony do sieci wewnętrznej organizacji ani do sieci VPN z połączeniem z lokalnym kontrolerem domeny usługi AD.
- Użytkownik jest zalogowany na komputerze przy użyciu konta komputera lokalnego. 
- Problemy z konfiguracją usługi: 
   - Serwer AD FS nie został skonfigurowany do obsługi **WIAORMULTIAUTHN**. 
   - Las komputera nie ma obiektu punktu połączenia z usługą, który wskazuje zweryfikowaną nazwę domeny w usłudze Azure AD 
   - Lub jeśli domena jest zarządzana, bezproblemowe logowanie jednokrotne nie zostało skonfigurowane ani nie działa.
   - Użytkownik osiągnął limit urządzeń. 

## <a name="next-steps"></a>Następne kroki

Pytania można znaleźć w temacie [często zadawane pytania dotyczące zarządzania urządzeniami](faq.yml)  
